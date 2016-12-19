---
layout: post
title: update jekyll 3.x
categories: jekyll
tags: [blog, code]
---

오랫만에 포스팅하려고 로컬에서 확인하는데 에러가 발생했다.
{% highlight sh %}
$ jekyll serve
Could not find 'jekyll'
{% endhighlight %}

삭제한적이 없는 것 같은데 jekyll 명령어가 작동하지 않는다. 우선 다시 설치해봤다.
{% highlight sh %}
$ gem install jekyll
ERROR:  While executing gem ... (TypeError)
{% endhighlight %}

또 에러 발생.... 결국 차분히 [Jekyll install](https://jekyllrb.com/docs/installation/) 문서를 보면서 다시 설치했다.
{% highlight sh %}
$ which jekyll
/usr/local/bin/jekyll
$ sudo rm /usr/local/bin/jekyll
$ ruby -v
ruby 2.3.3p222 (2016-11-21 revision 56859)[x86_64-darwin16]
$ gem update --system
Updating rubygems-update
... RubyGems system software updated
$ gem install rubygems-update
$ update_rubygems
$ node -v
v4.6.0
$ python --version
Python 2.7.10
$ gem install jekyll
{% endhighlight %}

무사히 설치를 마치고 실행했더니, 이번엔 pagination이 동작하지 않는다.
{% highlight sh %}
$ jekyll serve
Deprecation: You appear to have pagination turned on, but you haven't included the `jekyll-paginate` gem. Ensure you have `gems: [jekyll-paginate]` in your configuration file.
{% endhighlight %}

일단 메시지에 나온대로 ```_config.yml``` 파일에 ```gems: [jekyll-paginate]```를 추가했다. 그 후 다시 실행해보니 Dependency 에러가 발생했다.
{% highlight sh %}
$ jekyll serve
Dependency Error: Yikes! It looks like you don't have jekyll-paginate or one of its dependencies installed. In order to use Jekyll as currently configured, you'll need to install this gem. The full error message from Ruby is: 'cannot load such file -- jekyll-paginate' If you run into trouble, you can find helpful resources at http://jekyllrb.com/help/!
{% endhighlight %}

아래와 같이 jekyll-paginate를 설치해서 해결했다.
{% highlight sh %}
$ gem list jekyll
*** LOCAL GEMS ***
jekyll (3.3.1)
jekyll-sass-converter (1.5.0)
jekyll-watch (1.5.0)

$ gem install jekyll-paginate
{% endhighlight %}

> [코드 보기](https://github.com/pinstinct/pinstinct.github.com/commit/843908721022fd417110fbbce22dccf18dc84f82?diff=unified)
