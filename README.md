https://github.com/sherllochen/sherllochen.github.io.git
Source code of this blog is in hexo-source(also the default branch). Static pages will be generated and deploy to gh-pages branch.

## Get ready to write.
1. Build docker image. Skip if it has been build.

```bash
docker build -t sherllo/hexo .
```

2. Run container.

```bash
docker run -v /host_path:/opt/hexo -p 4000:4000 -it sherllo/hexo
```

3. Write something.

```bash
hexo new [post|page|draft] <title>
```

# Deploy
Base setup document for deploying to github pages is [here](https://sherllochen.github.io/2020/01/16/deploy-to-github-pages/).
Once new commit puth to hexo-souce, travis will deploy the update to github pages automaticly.
```bash
git push origin hexo-source
```

## Other commands
1. Init blog.

```bash
hexo init blog_name
```

2. Run preview server, local preview will be exposed on 4000 port.(http://localhost:4000)

```bash
hexo server
```

## Usefull resources
- (Next theme docs)[http://theme-next.iissnan.com/]