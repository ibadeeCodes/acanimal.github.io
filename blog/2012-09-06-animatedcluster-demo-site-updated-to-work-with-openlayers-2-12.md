---
layout: post
title: AnimatedCluster demo site updated to work with OpenLayers 2.12
date: 2012-09-06 19:50
tags:
- openlayers
---
Not an awesome post but for any interested in the <a href="//2012/08/19/animated-marker-cluster-strategy-for-openlayers.html">AnimatedCluster strategy</a> I have updated the demo site to work properly with OpenLayers 2.12.

<p>In version 2.12 symbolizer are richest than in 2.11 and required to set the <code>labelOutlineWidth</code> property to some low value to show the labels fine:</p>
<pre class="prettyprint">var lowRule = new OpenLayers.Rule({
    filter: new OpenLayers.Filter.Comparison({
        type: OpenLayers.Filter.Comparison.LESS_THAN,
        property: "count",
        value: 15
    }),
    symbolizer: {
        fillColor: colors.low,
        fillOpacity: 0.9,
        strokeColor: colors.low,
        strokeOpacity: 0.5,
        strokeWidth: 12,
        pointRadius: 10,
        label: "${count}",
        labelOutlineWidth: 1,
        fontColor: "#ffffff",
        fontOpacity: 0.8,
        fontSize: "12px"
    }
});</pre>
<p>See in action:<br />
<a href="http://www.acuriousanimal.com/AnimatedCluster/"><img class="aligncenter size-full wp-image-861" title="anim_cluster_212" src="./images/anim_cluster_212.png" alt="" width="491" height="314" /></a></p>
