---
layout: post
title: Simple Hexo Recipes
date: 2014-04-19 00:58:14
comments: false
tags: [hexo]
categories:
---

### Create new post
``` [bash]
$> hexo new post <title>
```

### Test content locally
Generate content into the `public` directory and start a local server.
``` [bash]
$> hexo generate && hexo server
```
The server detects most modifications made to posts/themes and regenerates on the fly -> no restart necessary, just reload the page.

### Deploying
There is no local `master` branch. All work is done inside the local `source` branch.  
Test changes locally, commit and push them to upstream. Then deploy the generated content:
``` [bash]
$> hexo deploy
```
This will push the generated content to the upstream `master` branch. The deployment process will push to the repository specified inside `_config.yml`. The repository URL may be SSH.
