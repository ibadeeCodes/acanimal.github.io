---
layout: post
title: Improved performance on the AnimatedCluster for OpenLayers
date: 2012-10-09 23:42
tags:
- animatedcluster
- gis
- javascript
- openlayers
---
Yes, now I'm really proud to present the AnimatedCluster strategy for OpenLayers. Some time ago I created an <a title="Animated marker cluster strategy for OpenLayers" href="//2012/08/19/animated-marker-cluster-strategy-for-openlayers.html">AnimatedCluster strategy for OpenLayers</a> that extends the basic Cluster strategy,
  animating cluster when user changes the zoom.

<p style="text-align: center;"><a href="http://www.acuriousanimal.com/AnimatedCluster"><img class="size-full wp-image-861 aligncenter" title="anim_cluster_212" src="./images/anim_cluster_212.png" alt="" width="491" height="314" /></a></p>

<p>The problem with the previous version is the code makes some computations for all the clusters, no matter if they are inside or outside the map's viewport.</p>
<h2>The problem</h2>
<p>This issues is clearly reproducible in the old version. Zoom in until cluster contains one or two features and you will see a degradation of the animation.</p>
<p>Now, compare with the <a href="http://www.acuriousanimal.com/AnimatedCluster">new improved version</a>, which supports 50.000 features without problem (at least on my computer :) ).</p>
<h2>How do I improve it?</h2>
<p>The solution was easy, only manage the animation of those clusters inside the map's viewport:</p>

{% highlight javascript %}
for(var i=0; i<this.features.length; ++i) {
    feature = this.features[i];

    // Check if the feature's geometry is on the map's viewport,
    // if so then manages it, otherwise ignore.
    if(this.layer && this.layer.map) {
        var screenBounds = this.layer.map.getExtent();
        var featureBounds = feature.geometry.getBounds();
        if(!screenBounds.intersectsBounds(featureBounds)) {
            continue;
        }
    }
    ...
}
{% endhighlight %}

<p>Yes, it can seem obvious, but I have needed some time before I realized about this fact (dummy?)</p>
<h2>References</h2>
<ul>
<li>GitHub source code <a href="https://github.com/acanimal/AnimatedCluster">here</a></li>
<li>GitHub demo source code <a href="http://www.acuriousanimal.com/AnimatedCluster/">here</a></li>
</ul>
