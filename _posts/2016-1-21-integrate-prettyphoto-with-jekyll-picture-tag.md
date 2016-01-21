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

Besides the addtional attribute in liquid tag. We also need to necessary css and js to out pages. In your header include file, add the following:

``` html
  <!-- Pretty Photo -->
  <script src="/public/js/jquery-1.6.1.min.js" type="text/javascript" charset="utf-8"></script>
  <link rel="stylesheet" href="/public/css/prettyPhoto.css" type="text/css" media="screen" charset="utf-8" />
  <script src="/public/js/jquery.prettyPhoto.js" type="text/javascript" charset="utf-8"></script>  
  <script type="text/javascript" charset="utf-8">
    $(document).ready(function(){
	  $("picture img").each(function(){
	    if (!$(this).attr('group')) return;
		
	    var parent = $(this).parent();
	    parent.attr('rel', 'prettyPhoto[group_' + $(this).attr('group') + ']');
		parent.attr('href', parent.children().first().attr('srcset'));
	  });
      $("picture[rel^='prettyPhoto']").prettyPhoto({theme:'pp_default',slideshow:5000,autoplay_slideshow:false,social_tools:false});
    });
  </script>  
```

Also, you need to add necessary css, js and image files to your jekyll repo. All the files could be downloaded from the official [PrettyPhoto](http://www.no-margin-for-errors.com/projects/prettyphoto-jquery-lightbox-clone/#prettyPhoto) site.

