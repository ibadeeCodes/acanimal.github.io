---
layout: post
title: Filling Flexigrid with JSON/XML data
date: 2011-12-09 22:14
tags:
- javascript
- jquery
- plugins
- tricks
---
<a href="http://flexigrid.info/">Flexigrid</a> is one of the most useful and powerful grid plugins for <a href="http://jquery.com/">jQuery</a>. Unfortunately, as the author explains in the project page, there is no much documentation so you must learn how to use it looking at existing code.

If anytime you need to fill a Flexigrid with JSON or XML content and you are a little buggy to look at example code, here is the great summary:

<blockquote><p>Remember XML/JSON data must follow the next rules: <em>total</em>, <em>page</em>, <em>rows</em>. And for every row specify and id and a cell structure.</p></blockquote>
<p>Next steps show you how to make it run step by step.</p>
<ul>
<li>Sample data for both types are:</li>
</ul>
<pre class="brush:js">{
	page: 1,
	total: 2,
	rows: [
		{id: 'reg1',    cell: ['reg1-cell1','reg1-cell2']},
		{id: 'reg2',    cell: ['reg2-cell1','reg2-cell2']}
	]
}</pre>
<pre class="brush:xml">&lt;rows&gt;
	&lt;page&gt;1&lt;/page&gt;
	&lt;total&gt;2&lt;/total&gt;
	&lt;row id="reg1"&gt;
		&lt;cell&gt;reg1-cell1&lt;/cell&gt;
		&lt;cell&gt;reg1-cell2&lt;/cell&gt;
	&lt;/row&gt;
	&lt;row id="reg2"&gt;
		&lt;cell&gt;reg2-cell1&lt;/cell&gt;
		&lt;cell&gt;reg2-cell2&lt;/cell&gt;
	&lt;/row&gt;
&lt;/rows&gt;</pre>
<ul>
<li>Put some element on your HTML document:</li>
</ul>
<pre class="brush:xml">&lt;p id="yourId"&gt;&lt;/p&gt;</pre>
<ul>
<li>Convert that element into a flexigrid with:</li>
</ul>
<pre class="brush:js">$("#yourId").flexigrid({
	url: 'your_file_name.json_or_xml',
	dataType: 'json',
	...
}</pre>
<p>Now you know how to build a gorgeous grid from XML or JSON data.</p>
