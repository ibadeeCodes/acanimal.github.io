---
layout: post
title: A visual exploration of the Nobel Prize history
date: 2013-08-15 20:19
tags:
- datatables
- gis
- highcharts
- leaflet
---
After some time in mind, finally I have got some free time to spend creating a web page to show the information related to Nobel Prizes.

<p>After discovering <a href="http://www.nobelprize.org/nobel_organizations/nobelmedia/nobelprize_org/developer/">Nobel Prize</a> web site offers an API to query both prizes and laureates from the beginning of the history of the prizes I was thinking in the creating of a kind of dynamic infography to allow explore the information in a useful and simplified way.</p>
<p style="text-align: center;"><a href="http://www.acuriousanimal.com/nobel-prize-explorer"><img class="aligncenter  wp-image-1405" alt="" src="./images/Screenshot-08142013-042534-PM.png" width="579" height="469" /></a></p>
<h2>A short description of the application</h2>
<p>The goal of the application is to show the Nobel Prizes data in useful ways in addition to search laureates by filtering data. The application offers:</p>
<ul>
<li>Colorpleth map: to see the number of laureates depending on the born or died country</li>
<li>Line chart, to show the number of prizes and laureates by year</li>
<li>Column chart, to show prizes and laureates grouped by category and</li>
<li>Column chart, to show how prizes are shared by laureates</li>
<li>Table, with laureates information (prize, year, category and motivation)</li>
<li>A nice tour to know how to work with the application</li>
</ul>
<p>To filter information and reduce the amount of data in the laureates table, the idea is to go from top to bottom and select on each chart the desired information. This way, each time we select a country in the colorpleth map the rest of the charts and the laureates table are updated to show information related with that country. If we select a year in the line chart, the chart above it are updated to reflect the information for that year.</p>
<h2>How was it made?</h2>
<p>Once I had the idea I started exploring libraries to create the charts.</p>
<p>Initially I though in <a href="http://d3js.org/">D3</a>, which is an awesome and super powerful library, both to create the map and the charts. Unfortunately, D3 is too much <em>low level</em> library for my needs and I require to create my own little chart libraries to reuse components.</p>
<p>For the colorpleth map, after some exercises using D3 (from this nice <a href="http://techslides.com/d3-world-maps-tooltips-zooming-and-queue/">post</a>) I finally decide to make use of <a href="http://leafletjs.com">Leaflet</a>, which is tiny JavaScript library specifically designed to make <em>simple</em> maps. Also I found a nice <a href="http://leafletjs.com/examples/choropleth.html">tutorial to create a colorpleth map</a> that was a plus in my decision.</p>
<p>Next was to explore D3 derived chart libraries, like <a href="http://nickqizhu.github.io/dc.js/">dc.js</a>, <a href="http://nvd3.org/">nvd3</a> or <a href="http://www.oesmith.co.uk/morris.js/">morris.js</a>. After some test I discard them because lake of need requirements or documentation to use them properly. The final choice was <a href="http://www.highcharts.com/">HighCharts</a>, a really powerful library to create almost any kind of chart. It is really easy to create charts, add tooltips, control selection, etc and has a free license for non commercial apps.</p>
<p>Finally, I was thinking in the bottom laureates table. Initially I code it using a standard HTML table and adding and removing entries using jQuery. But after some headaches I change to <a href="https://datatables.net/">DataTables</a> which is a really powerful tool to create tables both from table tag or an external content loaded via AJAX.</p>
<h2>Contribute</h2>
<p>The Nobel Prize app is a simply exercise to test some different tools and try to produce a useful utility.</p>
<p>As always you can find the code at my <a href="https://github.com/acanimal/nobel-prize-explorer">GitHub</a> account and contribute with improvements. A demo is also available at: <a herf="http://www.acuriousanimal.com/nobel-prize-explorer/">http://www.acuriousanimal.com/nobel-prize-explorer</a></p>
