---
title: Hosting Github Pages Locally
---

When it comes to writing posts to this blog, it's nice to be able to see how things look before actually pushing to the live website.
That way you don't have to clutter up your history with a million commit messages each explaining how you made one minor tweak to the formatting.
In order to help curb some of this history mess, wouldn't it be great if you could host your own Github Pages server on your own development machine?
Through the magic of open source software and tools this is exactly what you're able to do.

[Github](https://github.com) actually has documentation that helps you [get started](https://help.github.com/articles/using-jekyll-with-pages/) with this.
This is awesome that they've made this possible.
However, it didnt' work [out-of-the-box](https://en.wikipedia.org/wiki/Out_of_the_box_(feature)) on my CentOS 7 minimal system.
Here's what I did to get things going.

Install the following packages using yum:

* epel-release
* ruby
* ruby-devel
* nodejs
* zlib-devel
* "@development tools"

`sudo yum install -y epel-release`
`sudo yum install -y ruby ruby-devel nodejs zlib-devel "@development tools"`

Then install the bundler gem.
`gem install bundler`

Now, make a folder, then put the following into a Gemfile so that bundler can get all of the packages and their dependencies.

{% highlight bash %}
source 'https://rubygems.org'
gem 'github-pages'
{% endhighlight %}

Once you've got that done, run `bundle` and let it take care of the rest.

When you're ready to serve your website, run a `bundle exec jekyll server` and browse to the address that it outputs on the terminal.

If you want to do this all in one step, put the following into a script, make it executable, and then let it run!

{% highlight bash %}
#!/bin/bash

# Install github pages


## dependencies
sudo yum install -y epel-release
sudo yum install -y ruby ruby-devel nodejs zlib-devel "@development-tools"

## bundler
gem install bundler

## install the github-pages gem
mkdir -p tmp/
cat <<"EOF" > tmp/Gemfile
source 'https://rubygems.org'
gem 'github-pages'
EOF

cd tmp/ && bundle
{% endhighlight %}

