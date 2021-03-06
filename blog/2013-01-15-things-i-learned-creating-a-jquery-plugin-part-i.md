---
layout: post
title: Things I learnt creating a jQuery Plugin (Part I)
date: 2013-01-15 23:31
tags:
- javascript
- jquery
- plugins
- tricks
---
<a href="http://jquery.com/">jQuery</a> is one of the most used JavaScript libraries, if not the most used one, which allows to make great things with the big set of little tools it offers to the web developers: HTML/DOM manipulation, CSS manipulation, HTML event methods, effects and animations, AJAX, utilities, ...</p>
<p>One of the key aspects of jQuery is the possibility to extend it with new functionalities, so called <em>plugins</em>. For those who has a basic knowledge of jQuery and want or need to create a new jQuery plugin this post tries to be a helping summary of good things to take into account.

<h2>Before to start</h2>
<p>Because of the flexibility and powerful of the language, the Perl community is proud of his <em>Tim Today</em> motto,  that is:</p>
<blockquote><p>There's more than one way to do it</p></blockquote>
<p>You can consider the same for JavaScript, that's true. The fact JavaScript is not an object oriented language but a <a href="https://developer.mozilla.org/en-US/docs/JavaScript/Introduction_to_Object-Oriented_JavaScript#Prototype-based_programming">prototype based language</a> allows to follow the OOP paradigm in many different ways. Looking for a template code to create a jQuery plugin I found tons and not all following the same conventions and ideas.</p>
<p>Here I present a mix of the most accepted templates and its key aspects.</p>
<h2>The code</h2>
<p>Accompanying the article you can find the self documented version of the boilerplate code at my <a href="https://github.com/acanimal/jQuery-Plugin-Boilerplate">GitHub repository</a>.</p>
<h2>Where to start</h2>
<p>If you are planning to create a jQuery plugin, the first places I suggest you to start reading is the <a href="http://docs.jquery.com/Plugins/Authoring">Plugins/Authoring</a> section of the project documentation. It is a great starting resource but in the real live you will find soon you need to know a bit more of that.</p>
<hr />
<h2>Using a jQuery plugin</h2>
<p>Suppose we have a jQuery plugin called <code>beautyLink</code>, that transform a normal link (the <code>&lt;a&gt;</code> element) into a really nice one changing the font family, text and background color. So we need to include it in our page before start using it:</p>
<pre class="prettyprint">&lt;script type="text/javascript" src="jquery.js"&gt;&lt;/script&gt;
&lt;script type="text/javascript" src="jquery-beautylink.js"&gt;&lt;/script&gt;</pre>
<p>The conventional way to create and apply it on an element would be:</p>
<pre class="prettyprint">$('#linkID').beautyLink();</pre>
<p>or, if you want to apply on each link of the current page:</p>
<pre class="prettyprint">$('a').beautyLink();</pre>
<p>Later, to change the text color to red we could invoke a plugin's public method like:</p>
<pre class="prettyprint">$('#linkID').beautyLink('color', 'red');</pre>
<p>or to retrieve the current text color:</p>
<pre class="prettyprint">$('#linkID').beautyLink('color');</pre>
<p>that is, the <code>color</code> method can act as a setter or getter depending if we pass a value or not.</p>
<h2>Creating a new plugin</h2>
<p>All right, we know how to use a plugin but we want to create a new one. The first step we need to do is to create a new JavaScript file where to place our code. As a good practice this file must contain a documentation header section with the plugin's name, version, author contact information, license, etc</p>
<p>When we include the JavaScript file in a web page we need the code to be auto-executed so the plugin can be registered within the jQuery. To do so all the JavaScript code in the file must be inside the code:</p>
<pre class="prettyprint">(function($, window, document, undefined) {
  // The code here
})(jQuery, window, document);</pre>
<p>This way, the code of our plugin will receive a reference to the jQuery library, the window and the document and, also, an undefined reference on the <code>undefined</code> parameter that we can use to compare undefined values (wow, that was too much undefineds in a sentence).</p>
<h2>The wrapper function</h2>
<p>To create a jQuery plugin we need to register a new function in the <code>jQuery.fn</code> object. This is an array where jQuery stores references to all available or included plugins in the current page. To add a new one simply write something like:</p>
<pre class="prettyprint">jQuery.fn.myPlugin = function(options) {
  // Do your awesome plugin stuff here
};</pre>
<p>or</p>
<pre class="prettyprint">jQuery.fn['myPlugin'] = function(options) {
  // Do your awesome plugin stuff here
};</pre>
<p><strong>The function you define here is the entry point to your plugin. It is a wrapper function that must handle the plugins initialization or invocation to any plugin's method.</strong></p>
<h3>How to distinguish the action?</h3>
<p>The question is, how do we implement the plugin's wrapper function to distinguish if it must initialize the plugin or call a method? The answer is in the <code>options</code> parameter.</p>
<p>When we instantiate a plugin without passing any argument the <code>options</code> parameter is <code>undefined</code>:</p>
<pre class="prettyprint">$('#linkID').beautyLink();</pre>
<p>If we instantiate passing any argument the <code>options</code> parameter is an <code>Object</code>:</p>
<pre class="prettyprint">$('#linkID').beautyLink({
  someOption: 'a',
  someOther: 123
});</pre>
<p>Finally, if we are calling a plugin's method like:</p>
<pre class="prettyprint">$('#linkID').beautyLink('color', 'red');</pre>
<p>then the <code>options</code> parameter is an array with two elements <code>['color', 'red']</code></p>
<h2>The starter code for the wrapper function</h2>
<p>With the above in mind, we can write the base code for the wrapper function:</p>
<pre class="prettyprint">$.fn[pluginName] = function(options) {
    var args = arguments;

    if (options === undefined || typeof options === 'object') {
        // Creates a new plugin instance

    } else if (typeof options === 'string' &amp;&amp; options[0] !== '_' &amp;&amp; options !== 'init') {
        // Call a public pluguin method (not starting with an underscore) and different
        // from the 'init' one

    }
};</pre>
<h2>Chainable or not chainable</h2>
<p>jQuery is famous (and powerful) by its chainable way to work, that is, a call to a method returns a reference to the same element so we can make another call. For example:</p>
<pre class="prettyprint">$('#someID')
  .parents(".pane")
  .animate({ opacity: "hide" }, "slow");</pre>
<p>or applied to our plugin:</p>
<pre class="prettyprint">$('#linkID')
  .beautyLink('color', 'red')
  .beautyLink('backgroundColor', 'black');</pre>
<p>We must think which of our methods must be chainable and which ones not. For example, <strong>the getter methods must break the chainability</strong>. In our case we want a call to:</p>
<pre class="prettyprint">$('#linkID').beautyLink('color');</pre>
<p>returns the <code>red</code> value and not a reference to the element to make another call.</p>
<h3>Implementing chainability</h3>
<p>When the wrapper function <code>$.fn[pluginName]</code> is called the this reference points to:</p>
<ul>
<li>the selected DOM element, like when using:
<pre class="prettyprint">$('#linkID').beautyLink('color', 'red');</pre>
<p>or</li>
<li>a list of selected DOM elements, like when using:
<pre class="prettyprint">$('a').beautyLink('color', 'red');</pre>
</li>
</ul>
<p>Next code shows a very basic wrapper function that initializes a Plugin instance for each selected element. This results in a chainable call that returns a reference to the same element ready to make another chained call:</p>
<pre class="prettyprint">$.fn[pluginName] = function(options) {
    return this.each(function() {
        new Plugin(this, options);
    });
}</pre>
<h3>Avoiding chainability</h3>
<p>The solution to avoid a method were chainable is easy, simply doesn't write the <code>this.each()</code> and execute code on the first occurrence, that is, the <code>this[0]</code>.</p>
<blockquote><p>This will be more clear in the final code.</p></blockquote>
<h2>The jQuery.data() function</h2>
<p>We make use of the <code>$.data()</code> function in our code so a short explanation is required.<br />
The <a href="http://api.jquery.com/jQuery.data/"><code>$.data()</code></a> function allows to store arbitrary data on an element (or get is value).</p>
<p>It is important because we store a reference to the Plugin instance on each element it is applied. This allow us to check if an element has already applied a plugin or we need to instantiate it. This will be clarified in the final wrapper function code.</p>
<h2>The backbone wrapper function code</h2>
<p>With all the above in mind, we can write our final plugin's wrapper function. To summarize:</p>
<ul>
<li><span style="line-height: 13px;">If plugin is called without arguments, then the plugin is initialized and a reference is stored in the DOM element.</span></li>
<li>If a plugin's method is called and it isn't a getter method, then the method is called maintaining chainability.</li>
<li>If a plugin's method is called and it is a getter method, then we return a value and break the chainability.</li>
</ul>
<pre class="prettyprint">$.fn[pluginName] = function(options) {
    var args = arguments;

    if (options === undefined || typeof options === 'object') {
        // Creates a new plugin instance, for each selected element, and
        // stores a reference withint the element's data
        return this.each(function() {
            if (!$.data(this, 'plugin_' + pluginName)) {
                $.data(this, 'plugin_' + pluginName, new Plugin(this, options));
            }
        });
    } else if (typeof options === 'string' &amp;&amp; options[0] !== '_' &amp;&amp; options !== 'init') {
        // Call a public pluguin method (not starting with an underscore) for each
        // selected element.
        if (Array.prototype.slice.call(args, 1).length == 0 &amp;&amp; $.inArray(options, $.fn[pluginName].getters) != -1) {
            // If the user does not pass any arguments and the method allows to
            // work as a getter then break the chainability so we can return a value
            // instead the element reference.
            var instance = $.data(this[0], 'plugin_' + pluginName);
            return instance[options].apply(instance, Array.prototype.slice.call(args, 1));
        } else {
            // Invoke the speficied method on each selected element
            return this.each(function() {
                var instance = $.data(this, 'plugin_' + pluginName);
                if (instance instanceof Plugin &amp;&amp; typeof instance[options] === 'function') {
                    instance[options].apply(instance, Array.prototype.slice.call(args, 1));
                }
            });
        }
    }
};</pre>
<p>Take into account, we have also defined an array where to specify which method can act as getters when they are invoked without any argument:</p>
<pre class="prettyprint">$.fn[pluginName].getters = ['someGetterMethod'];</pre>
<h2>To be continue...</h2>
<p>We have seen the basis on how to create a jQuery plugin and learnt about the importance of the wrapper function, which is the entry point to our plugin.<br />
In the next post I will explain how we can organize our plugin code, the responsible to implement functionality, how to create private and public method, etc.</p>
<p>Continuation article <a title="Things I learned creating a jQuery Plugin (Part II)" href="//2013/02/25/things-i-learned-creating-a-jquery-plugin-part-ii.html">Things I learned creating a jQuery Plugin (Part II)</a></p>
