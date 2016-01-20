---
layout: post
title: Installed jekyll-picture-tag plugin
---

Installed jekyll-picture-tag plugin to display pictures responsively in posts.

Steps to install:

Step 1: install gem;

``` bash
gem install jekyll-picture-tag
```

Step 2: edit _config.yml according to: [jekyll-picture-tag _config.yml example](https://github.com/robwierzbowski/jekyll-picture-tag/blob/master/examples/_config.yml);

Step 3: add liquid tag to post;

{% raw %}
``` txt
{% picture 2015-1-20-1.jpg alt="picture 1" %}
{% picture 2015-1-20-2.jpg alt="picture 2" %}
```
{% endraw %}
	
{% picture 2015-1-20-1.jpg alt="picture 1" %}
{% picture 2015-1-20-2.jpg alt="picture 2" %}
