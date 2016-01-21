---
layout: post
title: Integrate PrettyPhoto with jekyll-picture-tag plugin
---

In [previous post](/2016/01/20/installed-jekyll-picture-tag/), I talked about installed jekyll-picture-tag plugin to display pictures responsively in posts. It works very well in different browsers and resolutions. But sometimes, we might want a photo gallery. Instead of displaying the full size of photos, we might want to display thumbnails, and on click, popup a lightbox style slideshow window for displaying the full size of photos.

So, in this post, I want to talk about how I have integrated [PrettyPhoto](http://www.no-margin-for-errors.com/projects/prettyphoto-jquery-lightbox-clone/#prettyPhoto) with jekyll-picture-tag plugin. Our goal is no change to jekyll-picture-tag plugin, and to use the same jekyll-picture-tag's liguid tag format, only to add PrettyPhoto effect with addtional attributes when necessary.

The live example, is already in the [previous post](/2016/01/20/installed-jekyll-picture-tag/), an image is clicked, the PrettyPhoto slideshow window will popup.

The only addtional attribute we add to the jekyll-picture-tag liguid tag is "group". You could see the sample liguid tag below. Images marked with the same group value, will be displayed together in the same PrettyPhoto slideshow window. So easy!

{% raw %}
``` txt
{% picture 2015-1-20-1.jpg alt="picture 1" group="1" %}
{% picture 2015-1-20-2.jpg alt="picture 2" group="1" %}
```
{% endraw %}