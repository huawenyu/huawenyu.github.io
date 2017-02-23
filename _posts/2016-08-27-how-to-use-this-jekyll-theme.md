---
layout: post
title: Readme
permalink: /README/
categories: readme
tags: readme
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

### Support subdirectory under `_post`

```shell
_posts git:(master) tree
.
├── 2012-12-18-Resume.md
├── auth
│   ├── 2017-02-15-auth-krb-ntlm-conf.md
│   └── 2017-02-15-auth-krb-ntlm-log.md
└── howto
    ├── 2014-11-17-welcome-to-jekyll.md
    └── 2016-08-27-how-to-use-this-jekyll-theme.md
```

### Simetimes the new post not generated

 - First open our GitPage repository like 'https://github.com/<yourname>/<yourname>.github.io',
     click 'settings', check there have any building problem notify under 'GitHub Pages' paragraph.
 - **The post is not placed in the** `_posts` **directory.**
 - [**The post has incorrect title.**][3] Posts should be named `YEAR-MONTH-DAY-title.MARKUP`
 - **The post's date is in the future.** You can make the post visible by setting `future: true` in `_config.yml` [(documentation)][6]
 - [**The post has** `published: false` **in its front matter.**][4] Set it to `true`.
 - [**The title contains a** `:` **character.**][5] Replace it with `&#58`.


### Preview

``` bash
$ jekyll server
$ jekyll server --port 4001
```

Use browser open URL `http://localhost:4000`

## Tricks

### [When to use a post, a page, or a collection][1]

I like to think the decision looks roughly like this:

    +-------------------------------------+         +----------------+
    | Can the things be logically grouped?|---No--->|    Use pages   |
    +-------------------------------------+         +----------------+
                    |
                   Yes
                    |
                    V
    +-------------------------------------+         +----------------+
    |      Are they grouped by date?      |---No--->|Use a collection|
    +-------------------------------------+         +----------------+
                    |
                   Yes
                    |
                    V
    +-------------------------------------+
    |            Use posts                |
    +-------------------------------------+

So if you’re not about to open a bakery (if you do, please send cookies); what might you use collections for? In short, any discrete group of “things” that can be logically grouped by a common theme (that’s not their date). Here’s a few examples:

  - Listing employees on your company’s “about” page (or a project’s maintainers)
  - Documenting methods in an open source project (or the project’s that use it, or the plugins available)
  - Organizing jobs on your resume (or talks given, papers written)
  - Articles on a support site
  - Recipes on your personal blog (or restaurant reviews, or dishes on a menu)
  - Students in a class (or courses being offered, or listing the faculty)
  - Cheats, tips, tricks and walkthroughs for games (by platform)
  - Creating re-usable content snippets for your site such as testimonials, forms, sentences, buzz-words or call-outs
  - And honestly just about anything else


### code syntax

There is three ways to write code snippets in jekyll :

#### 1 - jekyll highlight

{% highlight c %}
#include <stdio.h>

int main()
{
    int a;
    int b;

    printf("hello world %d\n", a+b);
    return 1;
}
{% endhighlight %}

#### 2 - fenced code block

```c
#include <stdio.h>

int main()
{
    int a;
    int b;

    printf("hello world %d\n", a+b);
    return 1;
}
```

~~~c
#include <stdio.h>

int main()
{
    int a;
    int b;

    printf("hello world %d\n", a+b);
    return 1;
}
~~~

#### 3 - markdown 4-indent

    #include <stdio.h>

    int main()
    {
        int a;
        int b;

        printf("hello world %d\n", a+b);
        return 1;
    }


#### 4 - result

Only first and second can produce code highlight with rouge.
The third, the one that you actually use, only surround your code with <code> tag, but rouge or any highlighter you set, will not be used by kramdown.

So, you can switch to first or second solution.

Another thing, if you want to "color your code", you need an highlight css. [You can search for pygment style sheets.][2]

  - If using `highlighter: rouge` in `_config.yml`, I found only first style can work.
  - If using `kramdown+rouge` like following, the second style works well:

    ```
    markdown: kramdown

    kramdown:
        input: GFM
        hard_wrap: false
        syntax_highlighter: rouge
        math_engine: mathjax
    ```


## Thanks
  - [LessOffice](http://lesscss.cn/)
  - [Github](https://github.com/)
  - [Jekyll](https://jekyllrb.com/)
  - [Solar](https://github.com/mattvh/solar-theme-jekyll)[Matt Harzewski](http://www.webmaster-source.com/)
  - [LessOrMore](https://github.com/luoyan35714/LessOrMore)

## About me

[Wilson Huawen Yu](http://huawenyu.github.io/2012/12/18/Resume/)

  [1]: http://ben.balter.com/2015/02/20/jekyll-collections/
  [2]: https://github.com/search?q=pygments%20style
  [3]: http://stackoverflow.com/questions/15046420/jekyll-not-generating-posts
  [4]: http://stackoverflow.com/questions/16990138/jekyll-not-generating-posts
  [5]: http://stackoverflow.com/questions/10963002/jekyll-new-posts-not-being-generated
  [6]: https://jekyllrb.com/docs/configuration/#build-command-options
