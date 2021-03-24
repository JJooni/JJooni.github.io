---
layout: post
title: pwnable.kr wargame bof write-up
---

간단한 bof 문제이다.
바로 소스코드를 열어보자.
<br>
{% highlight js linenos %}
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

void func(int key){
  char overflowme[32];
  printf("overflow me : ");
  gets(overflowme);	// smash me!
  if(key == 0xcafebabe){
    system("/bin/sh");
  }
  else{
    printf("Nah..\n");
  }
}
int main(int argc, char* argv[]){
  func(0xdeadbeef);
  return 0;
}
{% endhighlight %}
Poole is the butler for [Jekyll](http://jekyllrb.com), the static site generator. It's designed and developed by [@mdo](https://twitter.com/mdo) to provide a clear and concise foundational setup for any Jekyll site. It does so by furnishing a full vanilla Jekyll install with example layouts, pages, posts, and styles.

This demo site was last updated {{ site.time | date: "%B %d, %Y" }}.

There are currently two themes built on Poole:

* [Hyde](http://hyde.getpoole.com)
* [Lanyon](http://lanyon.getpoole.com)

Learn more and contribute on [GitHub]({{ site.github.repo }}).

## What's included

Poole is a streamlined Jekyll site designed and built as a foundation for building more meaningful themes. Poole, and every theme built on it like this one, includes the following:

* Complete Jekyll setup included (layouts, config, [404]({{ '404.html' | relative_url }}), [RSS feed]({{ 'atom.xml' | relative_url }}), posts, [archive page]({{ 'archive' | relative_url }}), and [example page]({{ 'about' | relative_url }}))
* Mobile friendly design and development
* Easily scalable text and component sizing with `rem` units in the CSS
* Support for a wide gamut of HTML elements
* Related posts (time-based, because Jekyll) below each post
* Syntax highlighting, courtesy Jekyll's built-in support for Rouge

Additional features are available in individual themes.

## Browser support

Poole and its themes are by preference a forward-thinking project. In addition to the latest versions of Chrome, Safari (mobile and desktop), Firefox, and Edge.

## Download

These themes are developed on and hosted with GitHub. Head to the [GitHub repository]({{ site.github.repo }}) for downloads, bug reports, and features requests.

Thanks!
