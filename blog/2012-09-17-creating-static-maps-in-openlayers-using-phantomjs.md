---
layout: post
title: Creating static maps in OpenLayers using PhantomJS
date: 2012-09-17 17:30
tags:
- openlayers
- phantomjs
- screenshot
- tools
- tricks
---
Many times in a web mapping application it is desired to save a picture with the current map information.Those who works with Google Maps API has also the <a href="https://developers.google.com/maps/documentation/staticmaps/"
 >Static Maps API</a>, which works similarly than Google Maps but produces static images.

<p>For example, next call:</p>
<pre class="lang:default decode:true">http://maps.googleapis.com/maps/api/staticmap?center=Brooklyn+Bridge,New+York,NY&amp;zoom=13&amp;size=600x300&amp;maptype=roadmap
&amp;markers=color:blue%7Clabel:S%7C40.702147,-74.015794&amp;markers=color:green%7Clabel:G%7C40.711614,-74.012318
&amp;markers=color:red%7Ccolor:red%7Clabel:C%7C40.718217,-73.998284&amp;sensor=false</pre>
<p>produces the image:</p>
<p><img class="aligncenter" src="./images/staticmap.png" alt="" width="600" height="300" /></p>
<p>Unfortunately, using libraries other than Google Maps, like OpenLayers or Leaflet, there is no similar solution. Probably the best, simple and powerful one, is to install a plugin on your browser to take screenshots. But well.. I think that does not deserve to write a post :p</p>
<h2>How to render a web page element to an image?</h2>
<p>After writing my last post (<a title="Taking web page screenshots" href="//2012/09/15/taking-web-page-screenshots">Taking Web Page Screenshots</a>), where I show to to take a screenshot of a whole page, I was thinking on using PhantomJS to render only a portion of a page to an image.</p>
<p>The PhantomJS's <code>WebPage</code> object has a <code>clipRect</code> property which determines the portion of the web page that must be rendered. With this in mind we can see a solution could be:</p>
<ul>
<li>Get the bounding rectangle of the desired DOM element to be rasterized.</li>
<li>Set the <code>clipRect</code> property</li>
<li>Render the page to a file.</li>
</ul>
<p>For that purpose I have prepared a little JavaScript application to run with PhantomJS. Its usage is as follows:</p>
<pre class="prettyprint">./bin/phantomjs ./examples/rasterize_element.js URL output_file selector</pre>
<p>For example, the next execution against the demo of <a title="Animated marker cluster strategy for OpenLayers" href="//2012/08/19/animated-marker-cluster-strategy-for-openlayers">Animated Cluster Strategy for OpenLayers</a> selecting the first map:</p>
<pre class="prettyprint">./bin/phantomjs ./examples/rasterize_element.js http://www.acuriousanimal.com/AnimatedCluster map.png '#map1'</pre>
<p>Produces the image:</p>
<p><a href="./images/2012/09/map.png"><img class="aligncenter size-full wp-image-899" title="map" src="./images/map.png" alt="" width="828" height="300" /></a></p>
<p>Next is the whole code of the program (called <code>rasterize_element.js</code> and based on the <code>rasterize.js</code> application attached on the PhantomJS package):</p>
<blockquote><p>Note: The code is accesible at <a href="https://gist.github.com/3732624">GitHub:Gist</a>.</p></blockquote>
<pre class="lang:js decode:true prettyprint ">var page = require('webpage').create(),
    system = require('system'),
    address, output, size;

if (system.args.length &lt; 4 || system.args.length &gt; 6) {
    console.log('Usage: rasterize_element.js URL filename selector [paperwidth*paperheight|paperformat] [zoom]');
    console.log('  paper (pdf output) examples: "5in*7.5in", "10cm*20cm", "A4", "Letter"');
    phantom.exit(1);
} else {
    address = system.args[1];
    output = system.args[2];
    selector = system.args[3];
    page.viewportSize = { width: 600, height: 600 };
    if (system.args.length &gt; 3 &amp;&amp; system.args[2].substr(-4) === ".pdf") {
        size = system.args[3].split('*');
        page.paperSize = size.length === 2 ? { width: size[0], height: size[1], margin: '0px' }
                                           : { format: system.args[3], orientation: 'portrait', margin: '1cm' };
    }
    if (system.args.length &gt; 4) {
        page.zoomFactor = system.args[4];
    }
	console.log("Loading page...");
    page.open(address, function (status) {
        if (status !== 'success') {
            console.log('Unable to load the address!');
        } else {
            window.setTimeout(function () {
                console.log("Getting element clipRect...");
                var clipRect = page.evaluate(function (s) {
	                var cr = document.querySelector(s).getBoundingClientRect();
	                return cr;
                }, selector);

                page.clipRect = {
	                top:    clipRect.top,
	                left:   clipRect.left,
	                width:  clipRect.width,
	                height: clipRect.height
                };
                console.log("Rendering to file...");
                page.render(output);
                phantom.exit();
            }, 200);
        }
    });
}</pre>
<h2>Alternatives and references</h2>
<p>Of course I'm not the first one that has explore this issue. A nice <a href="https://gist.github.com/1501173">snippet</a> from <a href="https://github.com/n1k0">n1k0</a> can be found at <a href="https://gist.github.com/">GitHub:Gist</a>. It does more or less the same as the code shown in this post.</p>
<p>Another alternative is the use of <a href="http://casperjs.org/">CasperJS</a>. As its home page says:</p>
<blockquote><p>CasperJS is an open source <strong>navigation scripting &amp; testing utility</strong> written in Javascript and based on <a href="http://www.phantomjs.org/">PhantomJS</a> — the scriptable headless <a href="http://www.webkit.org/">WebKit</a> engine. It eases the process of <strong>defining a full navigation scenario</strong> and provides useful <strong>high-level functions, methods &amp; syntactic sugar</strong> for doing common tasks such as:</p>
<ul>
<li>defining &amp; ordering browsing <a href="http://casperjs.org/quickstart.html">navigation steps</a></li>
<li>filling &amp; submitting <a href="http://casperjs.org/api.html#casper.fill">forms</a></li>
<li><a href="http://casperjs.org/api.html#casper.click">clicking</a> &amp; following links</li>
<li><strong>capturing <a href="http://casperjs.org/api.html#casper.captureSelector">screenshots</a> of a page (or part of it)</strong></li>
<li><a href="http://casperjs.org/api.html#tester">testing</a> remote DOM</li>
<li><a href="http://casperjs.org/logging.html">logging</a> events</li>
<li><a href="http://casperjs.org/api.html#casper.download">downloading</a> resources, including binary ones</li>
<li>writing <a href="http://casperjs.org/testing.html">functional test suites</a>, saving results as JUnit XML</li>
<li><a href="https://github.com/n1k0/casperjs/blob/master/samples/">scraping</a> Web contents</li>
</ul>
</blockquote>
<p>With CasperJS capturing a page element is as easy as:</p>
<pre class="prettyprint">casper.start('http://www.weather.com/', function() {
    this.captureSelector('weather.png', '.twc-story-block');
});
casper.run();</pre>
