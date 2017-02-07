---
layout: post
published: true
title:  Howto use this Jekyll theme
date:   2016-08-27 01:08:00 +1600
categories: document, jekyll
tag: howto
---

* content
{:toc}


# Blogs
This blogs base on Jekyll, and using [LessOrMore](https://github.com/luoyan35714/LessOrMore) theme which come from [jekyllthemes](http://jekyllthemes.org/).

## Usage

``` bash
git clone <current-project>
```

### Config `_config.yml`

``` bash
name: 
email: 
author: 
url: 
baseurl: ""
resume_site: 
github: 
github_username: 
```

### New post in `./_posts`

The struct of one page

    ```
    | page-header | control title, categories, tags |
    |-------------|---------------------------------|
    | split line  | one more empty line             |
    | ------      | -------------                   |
    | toc         | content index                   |
    | ------      | -------------                   |
    | split line  | two more empty line             |
    | ------      | -------------                   |
    | content     | our markdown text               |


    Sample:

    ---
    layout: post
    title:  this page title here
    date:   2016-08-27 01:08:00 +0800
    categories: document
    tag: linux
    ---

    * content
    {:toc}


    Here is the TEXT TEXT TEXT.
    ```

### Preview

``` bash
$ jekyll server
```

Use browser open URL `http://localhost:4000`

## Thanks
  - [LessOffice](http://lesscss.cn/)
  - [Github](https://github.com/)
  - [Jekyll](https://jekyllrb.com/)
  - [Solar](https://github.com/mattvh/solar-theme-jekyll)[Matt Harzewski](http://www.webmaster-source.com/)
  - [LessOrMore](https://github.com/luoyan35714/LessOrMore)

## About me

[Wilson Huawen Yu](http://huawenyu.github.io/Resume.md/)

