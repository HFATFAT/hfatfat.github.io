---
layout:     post
title:      获取和设置ImageView的宽度和高度
date:       2015-08-31 23:31:19
author:     HFF  
summary:    获取和设置ImageView的宽度和高度
categories: jekyll
thumbnail:  heart
tags:
 - Android
---

不能用imageview.getWidth() 和 imageview.getHeight() 来直接获取高和宽。

###获取方法：
<pre class="brush:java;gutter: true;">
Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.image);  
//(BitmapDrawable)getResources().getDrawable(R.drawable.image).getBitmap();
    int width=bitmap.getWidth();  
	int height=bitmap.getHeight();  
</pre>

###设置方法：
<pre class="brush:java;gutter: true;">
imageView.setImageResource(R.drawable.image);  
    LayoutParams params = imageView.getLayoutParams();  
    params.height=200;  
    params.width =100;  
    imageView.setLayoutParams(params); 
</pre>