---
layout: post
title:  "Getting Started With Jekyll"
date:   2020-04-01 15:45:00 +0900
categories: jekyll
---

* content
{:toc}


## Installation

Jekyll을 사용하기 위해서는 ruby가 필요하다. system의 ruby를 사용해도 상관없으나 rbenv를 사용하여 system이 아닌 분리된 환경을 구성하여 사용하기로 한다.

### Install rbenv

다음과 같이 `rbenv`를 설치하고 구성한다.

{% highlight plaintext %}
$ brew install rbenv 
$ rbenv init
{% endhighlight %}

현재 터미널을 종료하고 새로 실행해 다음 명령을 통해서 정상적으로 설치되었는지 확인한다.

{% highlight plaintext %}
$ curl -fsSL https://github.com/rbenv/rbenv-installer/raw/master/bin/rbenv-doctor | bash
{% endhighlight %}

### Install ruby

정상적으로 설치되었다면 원하는 ruby 버전을 고르고 설치한다.

{% highlight plaintext %}
$ rbenv install -l
1.8.5-p52
1.8.5-p113
1.8.5-p114
1.8.5-p115
1.8.5-p231
...
2.7.0-rc1
2.7.0-rc2
2.7.0
2.8.0-dev

$ rbenv install 2.7.0
{% endhighlight %}

사용할 ruby를 system에서 새로 설치한 2.7.0으로 변경한다.

{% highlight plaintext %}
$ rbenv install 2.7.0
$ rbenv global 2.7.0

$ ruby --verison
ruby 2.7.0p0 (2019-12-25 revision 647ee6f091) [x86_64-darwin19]
{% endhighlight %}

### Install bundler, jekyll

Jekyll을 사용하기 위해 bundler와 jekyll을 설치한다. `rbenv`를 사용하여 설치하기 때문에 `--user-install` 옵션은 사용하지 않는다. 

{% highlight plaintext %}
$ gem install bundler jekyll
...
27 gems installed

$ gem list | grep -E 'bundler|jekyll'
bundler (2.1.4, default: 2.1.2)
jekyll (4.0.0)
jekyll-sass-converter (2.1.0)
jekyll-watch (2.2.1)
{% endhighlight %}

## Start Jekyll blog

다음과 같이 `jekyll` 명령을 통해 블로그를 생성하고 `bundle exec jekyll serve` 명령을 통해 로컬에서 블로그를 접속할 수 있다.

{% highlight plaintext %}
$ jekyll new myblog
Running bundle install in ...
...
New jekyll site installed in ...

$ ls myblog
404.html       Gemfile.lock   _posts         index.markdown
Gemfile        _config.yml    about.markdown

$ cd myblog
$ bundle exec jekyll serve
...
    Server address: http://127.0.0.1:4000/
  Server running... press ctrl-c to stop.

{% endhighlight %}

이후 해당 주소로 접속하면 새로 생성된 Jekyll 블로그를 확인 할 수 있다.

{% highlight plaintext %}
$ curl -I http://localhost:4000
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 4608
Cache-Control: private, max-age=0, proxy-revalidate, no-store, no-cache, must-revalidate
Server: WEBrick/1.6.0 (Ruby/2.7.0/2019-12-25)
Connection: Keep-Alive
{% endhighlight %}

## References
[rbenv Github Repo][rbenv-github-repo]

[Jekyll Docs][jekyll-docs]

[rbenv-github-repo]: https://github.com/rbenv/rbenv
[jekyll-docs]: https://jekyllrb.com/docs/installation/#requirements

