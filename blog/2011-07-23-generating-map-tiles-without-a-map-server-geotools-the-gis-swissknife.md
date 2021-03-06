---
layout: post
title: Generating map tiles without a map server. GeoTools the GIS swissknife.
date: 2011-07-23 22:14
tags:
- geotools
- gis
- java
---
Recently I was playing with latest version of <a href="http://geoserver.org/">GeoServer</a>. It includes the <a href="http://geowebcache.org/">GeoWebCache</a>, something which can improve your server performance greatly. GeoServer solves and helps lots of problems to work and visualize geospatial data but as you know map servers lakes from scalability.

<p>Because this GeoWebCache is a great tool. Basically, each map request is processed and the result image is stored to be directly returned on subsequent requests. The processed images are stored as a pyramid of tiles depending on the bounding box or zoom level of the request.</p>

<p>Another way to solve scalability problems is directly pre-generate the pyramid of tiles, something that makes Google, Bing, Yahoo, OpenStreetMaps, etc.</p>
<h3>What I need?</h3>
<p>I have a lightnings database with thousands of lightnings for a period of some months. I need to show lightnings in my maps but only those corresponding to a period or interval of time. For example, render the lightnings from 00:00h to 00:30h and allow the user to go forward or backward in time.</p>
<p>One important thing is I only need an "image" with the information in that period. I don't need to render each lightning as a feature in the map -this will degrade the performance rendering in a storm with thousands of lightnings per period.</p>
<h3>The problem</h3>
<p>So why don't use GeoServer+GeoWebCache for this? I can configure a layer pointing to my lightning database, make requests and rest assured subsequent call will get the previously created map.</p>
<p>The problem is at this moment -while I write this post- GeoServer lakes from TIME support in requests. That means if I define a layer from my lightnings table on DB, every GeoServer request will work against all data while I only need a subset of my data -determined by an interval- to be rendered in the requested maps.</p>
<h3>Adopted solution</h3>
<p>Ok, be quiet. GeoServer is build on top of <a href="http://geotools.org/">GeoTools</a>, an op<img class="alignright" src="./images/geotools-logo.png" alt="" width="229" height="60" />en source Java library which provides standards compliant methods for the manipulation of geospatial data and, more important, GeoTools library implements <a href="http://www.opengeospatial.org">Open Geospatial Consortium</a> (OGC) specifications as they are developed.</p>
<p>With all this the solution seems easy: code a program to query the desired period of lightning data and generate a pyramid of tiles (for the desired levels).</p>
<h2> A brief description of the implementation</h2>
<p>Next is a brief summary of things to do, or take into account, to generate your own pyramid of tiles programmatically with Geotools.</p>
<p>All the lightnings information is stored on a PostgreSQL/PostGIS table called 'lightnings'. Data related with a lightning are: date (the UTC instant in which the lightning occurs, represented as a long number in <a href="http://en.wikipedia.org/wiki/Unix_time">Unix time</a>), position (latitude/longitude/altitude), value and sign (the electric charge).</p>
<h3>Set the DataSource connection</h3>
<p>GeoTools tries to simplify thing and because this it tries to abstracts as much as possible. Features can be provided from many source: files (shapefiles, GML, ...) or a database (PostgreSQL/PostGIS, Oracle, ...).</p>
<p>The first step then is to set a DataSource instance pointing to our database:</p>
<pre class="brush:java">Map&lt;String, Object&gt; params = new HashMap&lt;String, Object&gt;();
params.put(PostgisNGDataStoreFactory.DBTYPE.key, dbconn.getType());
params.put(PostgisNGDataStoreFactory.HOST.key, dbconn.getHost());
params.put(PostgisNGDataStoreFactory.PORT.key, dbconn.getPort());
params.put(PostgisNGDataStoreFactory.SCHEMA.key, "public");
params.put(PostgisNGDataStoreFactory.DATABASE.key, dbconn.getDatabase());
params.put(PostgisNGDataStoreFactory.USER.key, dbconn.getUser());
params.put(PostgisNGDataStoreFactory.PASSWD.key, dbconn.getPassword());
params.put(PostgisNGDataStoreFactory.EXPOSE_PK.key, true);

// Get lightning store
DataStore dataStore = DataStoreFinder.getDataStore(params);
SimpleFeatureSource sfs = dataStore.getFeatureSource("lightnings");</pre>
<blockquote><p>Note: 'dbconn' is an object which stores my DB connection parameters.</p></blockquote>
<h3>Filtering data</h3>
<p>We don't want to get all the lightnings in the database but only those withing a period of tim, so what we need is to filter the data using Filter classes. Given a period of time represented by values 'long start_date' and 'long end_date' we can define the desired filter as:</p>
<pre class="brush:java">FilterFactory2 filterFactory = CommonFactoryFinder.getFilterFactory2(null);
// Create filter for specified initial and end dates
Filter filterStart = filterFactory.greaterOrEqual(filterFactory.property("date"), filterFactory.literal(start_date));
Filter filterEnd = filterFactory.less(filterFactory.property("date"), filterFactory.literal(end_date));
Filter filterTime = filterFactory.and(filterStart, filterEnd);</pre>
<h3>Create styles before rendering for features</h3>
<p>There are some ways to create styles for our features. One is to use a SLD document and the other is doing programmatically.</p>
<p>In my case, I chose to use the second form so here is a bit of cumbersome code (I ommited the try/catch section) which create the desired style to identify positive and negative lightnings.</p>
<pre class="brush:java">// Create style
StyleBuilder styleBuilder = new StyleBuilder();
StyleFactory styleFactory = styleBuilder.getStyleFactory();
FilterFactory2 filterFactory = styleBuilder.getFilterFactory();

// Style for Positivos
Graphic grP = styleFactory.createDefaultGraphic();
Mark markP = styleFactory.getCircleMark();
markP.setStroke(styleFactory.createStroke(filterFactory.literal(Color.BLUE), filterFactory.literal(1)));
markP.setFill(styleFactory.createFill(filterFactory.literal(Color.CYAN)));
grP.graphicalSymbols().clear();
grP.graphicalSymbols().add(markP);
grP.setSize(filterFactory.literal(5));

// Style for Negativos
Graphic grN = styleFactory.createDefaultGraphic();
Mark markN = styleFactory.getCircleMark();
markN.setStroke(styleFactory.createStroke(filterFactory.literal(Color.RED), filterFactory.literal(1)));
markN.setFill(styleFactory.createFill(filterFactory.literal(Color.ORANGE)));
grN.graphicalSymbols().clear();
grN.graphicalSymbols().add(markN);
grN.setSize(filterFactory.literal(5));

Filter filterPositiveValor = ff.and(filter, CQL.toFilter("value &gt;= 0"));
Filter filterNegativeValor = ff.and(filter, CQL.toFilter("value &lt; 0"));

// Create symbols and rules to render every feature
PointSymbolizer symPositivos = styleFactory.createPointSymbolizer(grP, null);
PointSymbolizer symNegativos = styleFactory.createPointSymbolizer(grN, null);

Rule ruleP = styleFactory.createRule();
ruleP.symbolizers().add(symPositivos);
ruleP.setFilter(filterPositiveValor);
FeatureTypeStyle ftsP = styleFactory.createFeatureTypeStyle(new Rule[]{ruleP});

Rule ruleN = styleFactory.createRule();
ruleN.symbolizers().add(symNegativos);
ruleN.setFilter(filterNegativeValor);
FeatureTypeStyle ftsN = styleFactory.createFeatureTypeStyle(new Rule[]{ruleN});

// Finally create out style
Style style = styleFactory.createStyle();
style.featureTypeStyles().add(ftsP);
style.featureTypeStyles().add(ftsN);</pre>
<h3>Create the map and render to a file</h3>
<p>The map creating is straightforward:</p>
<pre class="brush:java">MapContext map = new DefaultMapContext();
CoordinateReferenceSystem crs = CRS.decode("EPSG:3785");
map.setCoordinateReferenceSystem(crs);</pre>
<p>and then render it to a file. I will paste here the code on the <a href="http://docs.geotools.org/latest/userguide/library/render/gtrenderer.html">GTRenderer tutorial</a>. You can play a bit with the code and change some values: area of interest, size of the output image, etc.</p>
<pre class="brush:java">public void saveImage(final MapContent map, final String file, final int imageWidth) {

    GTRenderer renderer = new StreamingRenderer();
    renderer.setMapContent(map);

    Rectangle imageBounds = null;
    ReferencedEnvelope mapBounds = null;
    try {
        mapBounds = map.getMaxBounds();
        double heightToWidth = mapBounds.getSpan(1) / mapBounds.getSpan(0);
        imageBounds = new Rectangle(
                0, 0, imageWidth, (int) Math.round(imageWidth * heightToWidth));

    } catch (Exception e) {
        // failed to access map layers
        throw new RuntimeException(e);
    }

    BufferedImage image = new BufferedImage(imageBounds.width, imageBounds.height, BufferedImage.TYPE_INT_RGB);

    Graphics2D gr = image.createGraphics();
    gr.setPaint(Color.WHITE);
    gr.fill(imageBounds);

    try {
        renderer.paint(gr, imageBounds, mapBounds);
        File fileToSave = new File(file);
        ImageIO.write(image, "jpeg", fileToSave);

    } catch (IOException e) {
        throw new RuntimeException(e);
    }
}</pre>
<h1>Conclusions</h1>
<p>Every tool is designed and built with a goal in mind, because this normally the use of a map server is always the proper selection. But sometimes you have specific needs that general tools doesn't solve and here is when open source project like GeoTools can help you.</p>
<p>I would to note that programing this way the image generation is much more faster than use of GeoServer because we are avoiding lots of intermediate steps a map server does: get request, parse, check and validate parameters and once query is executed, image composed it must be returned to the client via HTTP protocol.</p>
<h1>References</h1>
<p><a href="http://docs.geotools.org">http://docs.geotools.org</a></p>
<p><a href="http://docs.geotools.org/latest/userguide/library/render/gtrenderer.html">http://docs.geotools.org/latest/userguide/library/render/gtrenderer.html</a></p>
<p><a href="http://docs.geotools.org/stable/tutorials/filter/query.html">http://docs.geotools.org/stable/tutorials/filter/query.html</a></p>
<p>&nbsp;</p>
