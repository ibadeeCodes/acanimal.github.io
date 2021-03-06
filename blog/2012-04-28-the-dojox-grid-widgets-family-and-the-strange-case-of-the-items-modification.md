---
layout: post
title: The dojox.grid widgets family and the strange case of the items modification
date: 2012-04-28 21:52
tags:
- dojo
- javascript
---
So, you are building a web application and decided to use the great Dojo framework.  Probably, if you need to use grids, you will decide to use some of the available widgets subclass of <a href="http://dojotoolkit.org/reference-guide/1.7/dojox/grid/index.html">dojox.grid</a>. Well, my friend, be aware with the data you use to fill the grid's store !!!

<h2>The scenario</h2>
<p>Start creating a piece of code to execute when the page is ready. It will use the <a href="http://dojotoolkit.org/reference-guide/1.7/dojox/grid/DataGrid.html">DataGrid </a>widget with an <a href="http://dojotoolkit.org/reference-guide/1.7/dojo/data/ItemFileWriteStore.html">ItemFileWriteStore </a>store:</p>
<pre class="brush:js">require(['dojox/grid/DataGrid', 'dojo/data/ItemFileWriteStore', 'dojo/domReady!'],

function(DataGrid, ItemFileWriteStore){</pre>
<p>Now, create a <em>data</em> object with a set of random items:</p>
<pre class="brush:js">	// Set up data store
	var data = {
		identifier: "id",
		items: []
	};
	// Initialize with random elements.
	var rows = 10;</pre>
<p>Note we have stores our items in a different array:</p>
<pre class="brush:js">	var items = [];
	for(var i = 0; i &lt; rows; i++){
		items.push({ id: i+1, col1: "the first column", col2: false, col3: 'A column with a long description', col4: Math.random()*50 });
	}
	data.items = items;</pre>
<p>Now, create the store and set the grid layout:</p>
<pre class="brush:js">	var store = new ItemFileWriteStore({data: data});

	// Set up layout
	var layout = [[
			{'name': 'Column ID', 'field': 'id', 'width': '100px'},
			{'name': 'Column 1', 'field': 'col1', 'width': '100px'},
			{'name': 'Column 2', 'field': 'col2', 'width': '100px'},
			{'name': 'Column 3', 'field': 'col3', 'width': 'auto'},
			{'name': 'Column 4', 'field': 'col4', 'width': '150px'}
		]];</pre>
<p>Finally, create the grid (remember to startup it):</p>
<pre class="brush:js">	// Create a new grid
	var grid = new DataGrid({
		id: 'grid',
		store: store,
		structure: layout,
		rowSelector: '20px'
	});

	// Append the new grid to the div
	grid.placeAt("gridDiv");

	// Call startup() to render the grid
	grid.startup();
});</pre>
<p>The grid code needs we have an HTML element in our page:</p>
<pre class="brush:xml">&lt;div id="gridDiv"&gt;&lt;/div&gt;</pre>
<p>Here is the result of the previous code:</p>
<p><a href="./images/2012/04/table.png"><img class="aligncenter size-full wp-image-569" title="table" src="./images/table.png" alt="" width="759" height="336" /></a></p>
<h2>The issue</h2>
<p>Our idea behind having the items stored in an array is because trying:</p>
<ul>
<li>to serve as input data for the grid's store</li>
<li>to store the original array to make other operations: to be used in other stores, to make a simple iteration, ... whatever we want.</li>
</ul>
<p>The issue is <strong>the array of items used to initialize the data store are modified once the grid's <em>startup()</em> method is executed</strong>.</p>
<p>Lets go to see it in action. Check the value of the <em>items</em> array before and after the grid's <em>startup()</em> method is executed:</p>
<p><a href="./images/2012/04/shot1.png"><img class=" wp-image-567" title="Before" src="./images/shot1.png" alt="" width="1066" height="358" /></a></p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p><a href="./images/2012/04/shot2.png"><img class=" wp-image-568 " title="After" src="./images/shot2.png" alt="" width="1066" height="354" /></a></p>
<p>Yes, the objects within the items array are modified for internal pourposes.</p>
<h2>Conclusions</h2>
<p>This simple issue can have great implications depending on your way to code.</p>
<p>Suppose you are getting a JSON file with information about employees and for each one the set of companies they have worked. You will store this information in JavaScript object array and may be interested on use to:</p>
<ul>
<li>search the boss within employees</li>
<li>initialize a <a href="http://dojotoolkit.org/reference-guide/1.7/dojox/grid/TreeGrid.html">TreeGrid</a> to show the whole list of data.</li>
</ul>
<p>The problem is you can't use the same object array for the two tasks because in the moment the TreeGrid will execute its <em>startup()</em> method the array of items will change its structure.</p>
<p>How to solve this? Create a new array of objects from your original one or store them in a <a href="http://dojotoolkit.org/reference-guide/1.7/dojo/store/Memory.html">Memory store</a>.</p>
<h2>References</h2>
<p><a href="http://dojotoolkit.org/reference-guide/1.7/dojox/grid/index.html">http://dojotoolkit.org/reference-guide/1.7/dojox/grid/index.html</a></p>
<p><a href="http://dojotoolkit.org/reference-guide/1.7/dojox/grid/DataGrid.html">http://dojotoolkit.org/reference-guide/1.7/dojox/grid/DataGrid.html</a></p>
<p><a href="http://dojotoolkit.org/reference-guide/1.7/dojo/data/ItemFileWriteStore.html">http://dojotoolkit.org/reference-guide/1.7/dojo/data/ItemFileWriteStore.html</a></p>
<p>Connecting a Store to a DataGrid - <a href="http://dojotoolkit.org/documentation/tutorials/1.7/store_driven_grid/">http://dojotoolkit.org/documentation/tutorials/1.7/store_driven_grid/</a></p>
