---
layout: post
title: Blog hosted on GitHub pages with subdirectory 
--- 
Followed the instructions on [a fairly old post (2014) by Barry Clark](https://www.smashingmagazine.com/2014/08/build-blog-jekyll-github-pages/), I finally managed to build/fork a Jekyll engined blog and have it hosted on GitHub. However, I wanted to host it under  
`https://<GITHUBUSERNAME>.github.io/blog`  
rather than  
`https://<GITHUBUSERNAME>.github.io`.  

On his post, he states that this would *introduces some complexity*. True, but fairly easy to resolve as well:  
	Step 1. Edit the `_config.yml` file   
	Step 2. Configuring a publishing source  

  
### Step 1. Edit the `_config.yml` file
Assuming you have forked Clark's [Jekyll-now](https://github.com/barryclark/jekyll-now) repository, all you have to do is to find *_config.yml* file, open it, find `Baseurl:`, add `"/repository-name"`, it should be looking like this:   
  
```Baseurl: "/blog"``` (if _blog_ is your repository name)   
#### Do NOT forget the `/` before the repository-name.

  
### Step 2. Configuring a publishing source (If you haven't done so)
1. Find the `Settings` option on your GitHub repository.
2. Find "GitHub Pages", select 'gh-pages branch' under Source drop-down menu
  
After a few seconds, refresh the page you should be able to see the green banner under "GitHub Pages" says your site is published at `https://<GITHUBUSERNAME>.github.io/<YOURPROJECTNAME>` this link.
	  
Click the link and VOILÀ! Simple eh?!
  
 
#### No `gh-pages` branch needed
Accidentally, I created `gh-pages` branch at first published from this branch (_Settings > GitHub Pages > Source_) and switched back to `master branch`, found out that the site still loads perfectly.


### Beware in which branch of the repository you are modifying
As a beginner, be very careful of the branch in which modification takes place. If necessary set the branch to default branch under _Settings > Branches > Default branch_.


