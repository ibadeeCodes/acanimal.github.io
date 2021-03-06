---
layout: post
title: 'Customizing jQuery UI Dialog: hiding close button and changing opacity'
date: 2011-08-16 14:02
tags:
- jquery
- tricks
- ui
---
Sometimes when you are programming small things are the hard things, little details becomes difficult to solve and you need to spend lot of time to solve them. This is logically :) because you spent the major part of your time thinking and designing the big or complex things, leaving in a second plane the small things and because this they became the new "big" things. Ok, stop talking with buggy sentences and talk about this post. Recently I was working in a web page using jQuery UI dialogs that have a couple of special requirements that takes me some time and because this I want to share here with you:

<ul>
<li>Some dialogs must not have a close button. There are dialogs that must be always visible floating at some part with some controls.</li>
<li>Some dialogs must have a degree of opacity allowing view the underlaying content and becomes more opaque when the cursor enters in the dialog.</li>
</ul>
<p>Next are samples about how customize minimally the dialog to achieve the previous requirements. You can see them in action in <a href="http://acuriousanimal.com/code/jquery_custom_dialog/">the next sample page</a>.</p>
<h3>Creating a default dialog</h3>
<p>Create a default jQuery UI dialog is easy, given a '<em>div</em>' element identified by <em>d1</em>:</p>
<pre class="brush:js">$("#d1").dialog({
	position: 'left'
});</pre>
<h3>Creating a dialog without close button</h3>
<p>To creating a dialog without a close button, the key is the '<a href="http://jqueryui.com/demos/dialog/#event-open">open</a>' method. We need to reference the '.ui-dialog-titlebar-close' element and hide it:</p>
<pre class="brush:js">$("#d2").dialog({
	position: 'center',
	closeOnEscape: false,
	open: function(event, ui) {
		// Hide close button
		$(this).parent().children().children(".ui-dialog-titlebar-close").hide();
	}
});</pre>
<h3>Creating a dialog that changes opacity</h3>
<p>Finally to create a fashion dialog that changes its opacity with the mouse hover event:</p>
<pre class="brush:js">
$("#d3").dialog({
	position: 'right',
	closeOnEscape: false,
	open: function(event, ui) {
		// Set opacity
		$(this).parent().css('opacity', 0.6);
		// Hide close button
		$(this).parent().children().children(".ui-dialog-titlebar-close").hide();
		// Handle opacity
		$(this).parent().hover( function () {
			$(this).css('opacity', 0.9);
		}, function (event) {
			$(this).css('opacity', 0.6);
		});
	}
});
</pre>
<p>Do yo know how to create more stunning dialogs?<br />
Comments are welcome. </p>
