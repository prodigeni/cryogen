<img src="https://raw.githubusercontent.com/lacarmen/cryogen/master/cryogen.png"
 hspace="20" align="left"height="200"/>

[![Dependency Status](https://www.versioneye.com/user/projects/547671eedeae900d12000056/badge.svg?style=flat)](https://www.versioneye.com/user/projects/547671eedeae900d12000056)

For additional documentation please see the [cryogen site](http://cryogenweb.org)

## Features

* blog posts and pages with Markdown
* tags
* table of contents generation
* Default Twitter Bootstrap theme
* plain HTML page templates
* code syntax highlighting
* Disqus support
* GitHub Gist integration
* sitemap
* Sass/SCSS compilation
* RSS

## Prerequisites

You will need [Leiningen][1] 2.5.0 or above installed.

[1]: https://github.com/technomancy/leiningen

## Usage

### Creating a New Site

A new site can be created using the Cryogen template as follows:

```
lein new cryogen my-blog
```

### Running the Server

The web server can be started using the `lein-ring` plugin:

```
lein ring server
```

The server will watch for changes in the `resources/templates` folder and recompile the content automatically.

### Site Configuration

The site configuration file is found at `templates/config.edn`, this file looks as follows:

```clojure
{:site-title       "My Awesome Blog"
 :author           "Bob Bobbert"
 :description      "This blog is awesome"
 :site-url         "http://blogawesome.com/"
 :post-root        "posts"
 :tag-root         "tags"
 :page-root        "pages"
 :blog-prefix      "/blog"
 :post-date-format "dd-MM-yyyy"
 :recent-posts     5
 :rss-name         "feed.xml"
 :rss-filters      ["clojure" "cryogen"]
 :sass-src         nil
 :sass-dest        nil
 :resources        ["css" "js" "img"]
 :keep-files       [".git"] 
 :disqus?          false
 :disqus-shortname ""
 :ignored-files    [#"^\.#.*" #".*\.swp$"]}
```

  * `post-root` - value prepended to all post uri's
  * `tag-root` - value prepended to all tag uri's
  * `page-root` - value prepended to all page uri's
  * `blog-prefix` - prepended to all uri's (must start with slash), nil by default
  * `recent-posts` - number of recent posts to display in the sidebar
  * `rss-name` - name of the rss file generated, nil defaults to `rss.xml`
  * `rss-filters` - used to generate tag-based rss feeds for topic-specific rss aggregators. Tags listed here should match tags being used in your posts.
  * `recent-posts` - the number of recent posts to show in the sidebar
  * `post-date-format` - date format for your .md files, yyyy-MM-dd by default
  * `sass-src` - directory containing sources of sass files to be
  compiled - defaults to "css" - be sure to include this directory in
  your `resources` section
  * `sass-dest` - directory where the compiled output CSS would be put
    into. defaults to "css" - be sure to include this directory in
    your `resources` section
  * `resources` - list of folders to be copied over from `templates` to `public`
  * `keep-files` - list of folders or files that are not wiped in the `public` directory. For example, this allows to keep a `.git` directory there across recompiles of the site to versionize the generated files
  * `disqus?` - set to true if you want disqus enabled on your site
  * `disqus-shortname` - your disqus shortname
  * `ignored-files` - list of regexps matching files the compiler should ignore

### Creating Posts

The posts are located in the `resources/templates/md/posts`. Posts are written using Markdown and each post file
should start with the date in the format of `dd-MM-yyyy`. The compiler will link the posts in order for you using
the dates. A valid post file might look as follows:

```
19-12-2014-post1.md
```

The post content must start with a map containing the post metadata:

```clojure
{:title "First Post!"
 :layout :post
 :tags  ["tag1" "tag3"]}
```

The metadata contains the following keys:

* `:title` - the title of the post
* `:author` - optional key to display the name of the author for the post
* `:layout` - the layout template to use for the post
* `:tags` - the tags associated with this post
* `:toc` - boolean indicating whether table of contents should be generated, defaults to false

The rest of the post should consist of valid Markdown content, eg:

```
## Hello World

This is my first post!

check out this sweet code

    (defn foo [bar]
      (bar))

Lorem ipsum dolor sit amet, consectetur adipiscing elit.
Nunc sodales pharetra massa, eget fringilla ex ornare et.
Nunc mattis diam ac urna finibus sodales. Etiam sed ipsum
et purus commodo bibendum. Cras libero magna, fringilla
tristique quam sagittis, volutpat auctor mi. Aliquam luctus,
nulla et vestibulum finibus, nibh justo semper tortor, nec
vestibulum tortor est nec nisi.
```

If you wish to enable comments on your posts, create a [disqus](https://disqus.com/) account and [register](https://disqus.com/admin/create/) your blog. `disqus?` should be set to `true` in the config and you must add your `disqus-shortname`. 

### Creating Pages

Pages work similarly to posts, but aren't grouped by date. An example page might be an about page.

The pages contain the following metadata:

* `:title` - the title of the page
* `:layout` - the layout template for the page
* `:page-index` - a number representing the order of the page in the navbar/sidebar
* `:navbar?` - determines whether the page should be shown in the navbar, `false` by default
* `:toc` - boolean indicating whether table of contents should be generated, defaults to false

### Customizing Layouts

Cryogen uses [Selmer](https://github.com/yogthos/Selmer) templating engine for layouts. Please refer to its documentation
to see the supported tags and filters for the layouts.

The layouts are contained in the `resources/templates/html/layouts` folder of the project. By default, the `base.html`
layout is used to provide the general layout for the site. This is where you would add static resources such as CSS and Js
assets as well as define headers and footers for your site.

Each page layout should have a name that matches the `:layout` key in the page metadata and end with `.html`. Page layouts
extend the base layout and should only contain the content relaveant to the page inside the `content` block.
For example, the `tag` layout is located in `tag.html` and looks as follows:

```xml
{% extends "templates/html/layouts/base.html" %}
{% block content %}
<div id="posts-by-tag">
    <h2>Posts tagged {{name}}</h2>
    <ul>
    {% for post in posts %}
        <li>
            <a href="{{post.uri}}">{{post.title}}</a>
        </li>
    {% endfor %}
    </ul>
</div>
{% endblock %}
```

### Code Syntax Highlighting

Cryogen uses [Highlight.js](https://highlightjs.org/) for code syntax highlighting. You can add more languages by replacing `templates/js/highlight.pack.js` with a customized package from [here](https://highlightjs.org/download/).

The ` initHighlightingOnLoad` function is called in `templates/html/layouts/base.html`.

```xml
<script>hljs.initHighlightingOnLoad();</script>
```

## Deploying Your Site

The generated static content will be found under the `resources/public` folder. Simply copy the content to a static
folder for a server sugh as Nginx or Apache and your site is now ready for service.

A sample Nginx configuration that's placed in `/etc/nginx/sites-available/default` can be seen below:

```javascript
server{
  listen 80 default_server;
  listen [::]:80 default_server ipv6only=on;
  server_name localhost <yoursite.com> <www.yoursite.com>;

  access_log /var/log/blog_access.log;
  error_log /var/log/blog_error.log;

  location / {
    alias /var/blog/;
    error_page    404 = /404.html;
  }
}
```

Simply set `yoursite.com` to the domain of your site in the above configuration and
ensure the static content is available at `/var/blog/`. Finally, place your custom error page
in the `/var/blog/404.html` file.

## Some Sites Made With Cryogen

* [My personal blog](http://carmenla.me/blog/index.html)
* [Cryogen Documentation Site](http://cryogenweb.org)
* [Yogthos blog](http://yogthos.net/)
* [Clojure :in Tunisia](http://www.clojure.tn)
* [dl1ely.github.io](http://dl1ely.github.io)
* [nil/recur](http://jonase.github.io/nil-recur)
* [on the clojure move](http://tangrammer.github.io/)

## License

Copyright © 2014 Carmen La

Distributed under the Eclipse Public License, the same as Clojure.
