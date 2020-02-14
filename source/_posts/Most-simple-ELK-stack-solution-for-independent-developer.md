---
title: Most simple ELK stack solution for independent developer
date: 2020-02-06 10:18:46
keywords: elk logging filebeat
description:
tags: 
	- ELK
---
# Most simple elk stack solution for independent developer
I'm a IT consultant and independent developer. There are so many different projects I have developed for  such a long time and most of these projects don't have lot traffic. So I think this solution is the most simple.

**TLDR: All project got a filebeat container to watch log files and send to a SINGLE ELK sever(Elasticsearch/Logstash/Kibana all in one).**
<!-- more -->
## ELK server

1. Install docker and docker compose. [Offical docs](https://docs.docker.com/install/linux/docker-ce/ubuntu/).
2. Clone git repo.  All docker files is in elk_stack/elk.

```bash
git clone https://github.com/sherllochen/dockers.git
```

3. Open income 5044 port for Logstash. Highly recommend to open specific ips.

4. Increase vm.max_map_count.

```bash
//The vm.max_map_count kernel setting needs to be set to at least 262144 for production use
sudo sysctl -w vm.max_map_count=262144
```

5. Most simple authorization, use Nginx to redirect request from 80 to 5601(Kibana). Set username and password in Nginx for authorization, so that you don't need to deal with X-pack.

```
// /etc/nginx/default
server {

	listen 80;

	listen [::]:80;

	server_name your.elk.domain;

	location / {

	root /opt/kibana;

	auth_basic "Restricted";

	auth_basic_user_file /etc/nginx/site_pass; # file for username/password storage.

	proxy_set_header Host $host:5601;

	proxy_set_header X-Real-Ip $remote_addr;

	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

	proxy_pass http://localhost:5601;

	proxy_set_header Via "nginx";

	}
}
```

6. Generate pass file with htpasswd. The site_pass file will created in current path.

```bash
sudo htpasswd -b -c site_pass your_username your_password
```

7. Move site_pass to /etc/nginx/site_pass 

```bash
sudo mv site_pass /etc/nginx/site_pass
```

8. RUN~

```bash
sudo docker-compose up
```

9. All things done. Kibana will be avaiable in http://your.elk.domain.

## Project server(client)

Using filebeat to watch log files, all logs with be delivered to the ELK server.

1. Install docker and docker compose. [Offical docs](https://docs.docker.com/install/linux/docker-ce/ubuntu/).
2. Clone git repo.  All docker files is in elk_stack/filebeat.

```bash
git clone https://github.com/sherllochen/dockers.git
```

3. Edit filebeat.yml. Change host and port of target elk server, default port is 5044. Change tag of inputs section. It's used to distinct from other project's log. My name rule is hostname+project_name.

4. Config log path of host to watch in docker-compose.yml, add volume like below, first one is log path of host.

```
- "/Users/neversion/dev/ats/log:/usr/share/filebeat/hostlogs:ro"
```

5. start docker-compose, and all thing done.

```bash
sudo docker-compose up
```
