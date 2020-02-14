---
title: Most easy to use tutorial for deploy Rails application with Docker & Capistrano in 15 steps
date: 2020-02-09 03:45:39
keywords: docker capistrano rails
description:
tags:
  - rails
  - docker
  - capistrano
---

TLDR:  Only 15 steps, your all envorionment about Rails(local develoment and remote server production) is all setup and ready to go.
<!-- more -->
## Server side

1. Clone [https://github.com/sherllochen/dockers.git](https://github.com/sherllochen/dockers.git). All related files is in rails directory.
2. Port 22 and 80 port must be open in server.
3. Install Docker and Docker compose. [Docke offical install docs](([https://docs.docker.com/install/linux/docker-ce/ubuntu/](https://docs.docker.com/install/linux/docker-ce/ubuntu/))
4. Change username and password of OS for deploy.
```bash
# nginx/Dockerfile 
# System deploy username and password is set here. Don't forget to also change in your deploy.rb file.
RUN useradd -rm -d /home/deploy -s /bin/bash -g root -G sudo -u 1000 your-deploy-username -p "$(openssl passwd -1your-password)"
```
5. Change database name, username and password(set in docker-compose.yml). For postgres, related setting is in 'environment' section of backend service. For mysql, related setting is in mysql service.

6. Edit nginx site config.
```bash
    # nginx/app_config
    root /your-app-deploy-path/current/public;
```
7. Run docker compose and all things done.
```bash
    sudo docker-compose run --service-ports production
```
---
## Local development side

Step 1,2,3 is the same with server side.

4. Run docker compose to start development environment.
```bash
    sudo docker-compose run --service-ports dev
```
5. Copy gits below to **lib/capistrano/tasks/tasks.rb**. These are customize tasks for capistrano, your more tasks can be defined here and call in deploy.rb

<script src="https://gist.github.com/sherllochen/31d9dbf683b40ac267ff822dabc5d7da.js"></script>

6. Edit server address and username. The username must be the same as setting in server's nginx/Dockerfile.
```bash
    # config/deploy/production.rb
    server "your.server.domain", user: "your-deploy-username", roles: %w{app db web}
```
7. Edit deploy setting.
```bash
    # config/deploy.rb
    set :application, "your-app-name"
    set :repo_url, "git@github.com:your-app-repo.git"
    # Every file about config info must be set here, and copy to deply_path/shared/config manully.
    # All variable write in these files directly.
    append :linked_files, "config/database.yml", "config/master.key", "config/wechat.yml"
```
8. Execute deploy command.
```bash
    cap production deploy
    # run seed_fu task if you need.(I use SeedFu for data seed).
    cap production rails:seed_fu
```
## TIPS

- All environment version change be changed in docker-compose.yml.