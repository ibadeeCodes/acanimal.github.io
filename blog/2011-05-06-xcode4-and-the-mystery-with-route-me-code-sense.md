---
layout: post
title: XCode4 and the mystery with Route-Me code sense
date: 2011-05-06 17:08:59.000000000 +02:00
categories: []
tags:
- apple
- ios
- iphone
- xcode
---
If you follow this blog you will know I prefer to write "long" posts with nice content :) form time to time than write lots of short posts without much substance. For that I use Twitter :p

<p>But always there an exception... and here is mine.</p>

<p>Recently I start coding with Objective-C programming for iPhone(iOS) and using XCode4.</p>
<p>As a GIS lover I look for a native map library to make a native program and found the <a href="https://github.com/route-me/route-me">route-me</a> project. Ok, that is nice. I download source code and follow the <a href="https://github.com/route-me/route-me/wiki/Embedding-Guide">Embedding Guide</a> to start coding. First time it goes fine. I make little sample without problem. Then I start a more serious project. I create a new project, follow the same steps to add the route-me dependencies but... ta-da !!! XCode doesn't completes my code !!! Yes, if I wrote "<em>RMMa</em>" but I never get "<em>RMMapView</em>". What the hell!!! How is it possible? I created a new project and attach route-me again, but... again the code completion didn't work. This is a nightmare.</p>
<p>The I look for some help. On <a href="http://stackoverflow.com/questions/5862312/xcode-header-files-scanning-issue">stackoverflow </a>I found a very similar issue but it didn't help my too much.</p>
<p>After a couple of very obfuscated days I found XCode4 indexes and stores code information in folder:</p>
<pre>/Users/your_home_directoy/Library/Developer/Xcode/DerivedData</pre>
<p>I think on delete that data and force XCode to recreate it. To do so you can go to the Organizer &gt; Projects, select your project and remove the derived data. I do it many times clean and build my project but... ta-da !!! Nothing changes, code completing didn't work.</p>
<p>Finally I opt to close XCode and remove the whole DerivedData directory. Then  start XCode again, create a new project and attach route-me and... ta-da-da !!! oh my God, this time code completion works. Don't ask my way. It simply works and that is enough for the moment.</p>
