---
layout: post
title: OpenLayers, create a checkboard layer to know the tile names using Google Charts API
date: 2013-01-11 12:02
tags:
- openlayers
- howto
- tricks
---
Pyramid tiles are used by many providers as a more efficient way (than WMS server) to provide images. The idea is simple, we have zoom levels and on every level we have NxN tiles of a specific size (typically 256x256 pixels). Google, Yahoo, Bing or OpenStreetMaps are samples of tile providers.

<p><img src="./images/pyramid.jpeg" alt="pyramid" width="300" /></p>
<p>Here, we are going to create a new OpenLayers layer that will show a checkboard with the name of the tile, all this with the help of the Google Charts API.</p>
<p>We need to take into account every provider uses a different pyramid tile notation, that means the same tile can be named [level=1,x=0,y=0] by one provider and named [level=1,x=0,y=1] by another.</p>
<p>Next code is based on OpenLayers.TMS layer, which follow the <a href="http://wiki.osgeo.org/wiki/Tile_Map_Service_Specification">OSGeo TMS</a> specification.</p>
<p><img src="./images/Tms.png" alt="" width="350" /></p>
<p>I have divided the code in two TMS subclasses. The first one is responsible to draw the <em>checkboard</em> and the second one to draw the tile name on top every tile. The checkboard layer makes use of Google Chart API to generate black tiles.</p>
<div id="highlighter_552708">
<pre class="lang:js decode:true">OpenLayers.Layer.Checkboard = OpenLayers.Class(OpenLayers.Layer.TMS, {

	getURL: function (bounds) {
		var res = this.map.getResolution();
		var z = this.map.getZoom();
		var limit = Math.pow(2, z);
		var x = Math.round((bounds.left - this.maxExtent.left) / (res *this.tileSize.w));
		var y = Math.round((this.maxExtent.top - bounds.top) / (res *this.tileSize.h));

		if (y &lt; 0 || y &gt;= limit) {
		return "";
		} else {
			x = ((x % limit) + limit) % limit;
			var tilename = "";
			if( (x+y)%2==0 ) {
				tilename = "http://chart.apis.google.com/chart?chst=d_text_outline&amp;;chs=256x256&amp;chf=bg,s,ffffffff&amp;chld=FFFFFF|12|h|000000|b||";
			} else {
				// no tile
			}
			return tilename;
		}
	},

	CLASS_NAME: "OpenLayers.Layer.Checkboard"
});</pre>
<p>Next is the code that return the tile names:</p>
</div>
<div id="highlighter_917884">
<pre class="lang:js decode:true ">OpenLayers.Layer.Tilenames = OpenLayers.Class(OpenLayers.Layer.TMS, {

	getURL: function (bounds) {
		var res = this.map.getResolution();
		var z = this.map.getZoom();
		var limit = Math.pow(2, z);
		var x = Math.round((bounds.left - this.maxExtent.left) / (res *this.tileSize.w));
		var y = Math.round((this.maxExtent.top - bounds.top) / (res *this.tileSize.h));

		if (y &lt; 0 || y &gt;= limit) {
		return "";
		} else {
			x = ((x % limit) + limit) % limit;

			var tilename = "";
			if( (x+y)%2==0 ) {
				tilename = "http://chart.apis.google.com/chart?chst=d_text_outline&amp;;chs=256x256&amp;chld=FFFFFF|12|h|000000|b||";
			} else {
				tilename = "http://chart.apis.google.com/chart?chst=d_text_outline&amp;;chs=256x256&amp;chld=000000|12|h|FFFFFF|b||";
			}
			tilename = tilename + "("+z+"/"+x+"/"+y+")";
			return tilename;
		}
	},

	CLASS_NAME: "OpenLayers.Layer.Tilenames"
});</pre>
<p>&nbsp;</p>
</div>
