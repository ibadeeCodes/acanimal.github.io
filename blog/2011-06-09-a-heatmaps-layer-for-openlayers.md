---
layout: post
title: A heatmaps layer for OpenLayers
date: 2011-06-09 19:20
tags:
- heatmaps
- html5
- openlayers
---
As I commented on a previous post I know about <a href="http://www.patrick-wied.at/static/heatmapjs">heatmaps.js</a> project some time ago and interest me a lot by its possibilities with OpenLayers. Of course it is not the only solution, see <a href="http://www.websitedev.de/temp/openlayers-heatmap-layer.html">here</a> and <a href="http://dev.openlayers.org/sandbox/camptocamp/canvas/openlayers/examples/heatMap.html">here</a> but it has a great performance.

<h2>Some 'auto-bombo'</h2>
<p>I put the creation of an OpenLayers layer in my maybe/something task list but seems the moment never arrives. Luckily for all of us, Patrick the author of the project, created too an initial heatmaps layer for OpenLayers and Google Maps.</p>
<p>The heatmap-openlayer code is a wrapper around the heatmap.js code and the initial version has some issues I have tried to improve. In concrete I have added:</p>
<ul>
<li>Heatmaps as independent map layer. On the initial version the heatmap-layer depends on another layer to make some position to pixel transformations.</li>
<li>Points based on OpenLayers.LonLat. Now you can specify the points in any coordinate system and layer transforms it to the one used by the map.</li>
<li>Possibility to pass the data set in layer constructor in addition to the method 'setDataset'.</li>
<li>Improved 'addDataPoint' method to add new lonlat based points.</li>
</ul>
<p>[caption id="attachment_304" align="aligncenter" width="588" caption="Click for the demo"]<a href="http://www.acuriousanimal.com/heatmap-openlayers"><img class="size-full wp-image-304  " title="heatmap_ol" src="./images/heatmap_ol1.png" alt="" width="588" height="246" /></a>[/caption]</p>
<h2>How to use heatmap-openlayers</h2>
<p>The first step is to create the data set. The important things to remember about a dataset are:</p>
<ul>
<li>A dataset is an object with two attributes: a 'max' value used to colorize the points and an array with the data.</li>
<li>Every point in the data array has two parts: an OpenLayers.LonLat object and an integer indicating the value.</li>
</ul>
<p>Next code generate a random set of points:</p>
<pre class="brush:js">// Generate some random data for test
var testData = {
	max: 50,
	data: []
};
var lon, lat, c;
for(var i=0; i&lt;500; i++) {
	lon = Math.floor(Math.random()*(-180-180)+180);
	lat = Math.floor(Math.random()*(-85-85)+85);
	c = Math.floor(Math.random()*50);
	testData.data.push({lonlat: new OpenLayers.LonLat(lon, lat), count: c});
}</pre>
<p>Now we can create our layer indicating its projection (the projection in which points are expressed) and necessary to transform points to map projection:</p>
<pre class="brush:js">heatmap = new OpenLayers.Layer.Heatmap("Heatmap Layer", map, testData,
{visible: true, radius: 15},
{isBaseLayer: false, opacity: 0.3, projection: new OpenLayers.Projection("EPSG:4326")});</pre>
<p>If you don't want to pass the dataset in the constructor simply pass a null value and set it later with the 'setDataset' method.</p>
<p>Finally, you can add more points later using the 'addDataPoint' method:</p>
<pre class="brush:js">// Add more points
for(var i=0; i&lt;100; i++) {
	lon = Math.floor(Math.random()*(-180-180)+180);
	lat = Math.floor(Math.random()*(-85-85)+85);
	c = Math.floor(Math.random()*50);
	heatmap.addDataPoint(new OpenLayers.LonLat(lon, lat), c);
}</pre>
<h2>Conclusions</h2>
<p>This is only a little step to create a good layer for OpenLayers based on a nice and good performance project.</p>
<p>Much more work can be done so if you want to contribute simply get the from <a href="https://github.com/acanimal">github</a> and start working.</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
