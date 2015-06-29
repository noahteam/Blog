title: How to set up and configure hexo to write posts on this blog
subtitle: By Ji Zhoubo
date: 2015-06-19 08:39:15
tags: git
---
## About
This blog mainly focuses on technical sharings and discussions, and any interesting topics related with Agile or Scrum may also appear.

In this post, I will show how to set up the environment and configure, and how to write posts and publish.

## Set up and Configure
- **Set up Git**
 This blog on GitHub has 2 repositories: 
    - noahteam.github.io: the repository contains compiled blog    
    - Blog: the source repository
 I have added all our team members as collaborator, so you only need to clone Blog repository to your local, then you will be good to contribute
 ```bash
 git clone https://github.com/noahteam/Blog.git
 ```
 The blog repository now alreayd has everything inside, including hexo, hexo thems, and some posts

- **Set up Hexo**
 Open git or powershell
 ```bash
 npm install -g hexo
 ```
 after installing hexo, need to init Hexo
 ```bash
 hexo init
 ```
 I have configured the _config.yml file and it supports deployment to our github.io page, in case you have error when publishing due to deployer issue, can try this:
 ```bash
 npm install hexo-deployer-git --save
 ```

## Writing and Publishing
Now you have everything ready, it is writting time! 
Before you proceed, get latest from Blog repository
```bash
git pull
```
- To create a new post, just use
 
 ```bash
 hexo n post [Title of the post]
 ```
- To generate and publish
 
 ```bash
 hexo g #compile and generate
 hexo s #open local server and view, not necessary
 hexo d #publish
 ```

After all that, you need to commit and push all your sources into Blog repository

```bash
git commit -m 'your comment'
git push
```
 
## Trouble Shooting
![image is died](https://lh3.googleusercontent.com/PzU-BJ6Y-carpM6hp8COX3uxWMEfAo5LRCmn1mzj1g=w482-h304-no "Error one")

> Delete **.deploy_git** folder and run **hexo clean**
