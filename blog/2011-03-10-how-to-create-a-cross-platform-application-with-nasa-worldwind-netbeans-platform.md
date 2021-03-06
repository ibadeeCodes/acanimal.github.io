---
layout: post
title: How to Create a Cross-Platform Application with NASA WorldWind & NetBeans Platform
date: 2011-03-10 22:46
tags:
- java
- netbeans rcp
- wwj
---
This post is a mini tutorial on how to create an application based on NetBeans Platform that uses the WorldWind Java virtual globe. Before to continue, you need to have some knowledge about NetBeans Platform, WWJ and JOGL.

<h2>About NetBeans Platform</h2>
<p><a href="http://platform.netbeans.org/">NetBeans Platform</a>, similar to Eclipse RCP, is a platform which offers a set of basic functions and functionallities that are common in almost all project. The most clear examples of applications developed with these platforms are NetBeans IDE and Eclipse IDE themselves.</p>
<p>In the case of NetBeans Platform some features that likes me are:</p>
<ul>
<li>100% Swing.</li>
<li>Modular runtime container.</li>
<li>Plugin support.</li>
<li>Window system.</li>
<li>Support handling actions, files and many other things typical in applications.</li>
<li>Active project, decent documentation and great forum activity.</li>
</ul>
<p>&nbsp;</p>
<h2>About WorldWind Java</h2>
<p><a href="http://worldwind.arc.nasa.gov/java/">WordlWind Java</a> is a Java API to create and work with a virtual globe, something common on these days and not so easy to implement.</p>
<p>In contrast to WorldWind .NET which is a desktop application, WWJ is only an API, a set of classes you can use to create your own based application.</p>
<p>One impotant thing is WWJ works thanks to <a href="http://kenai.com/projects/jogl/pages/Home">JOGL</a> (Java binding for OpenGL API), which is a Java API to work with OpenGL and that uses a native library implementation depending on your system.</p>
<h2>...and what is the problem?</h2>
<p>Nice question. Ok, if we can say there is any problem it will be: <strong>how to create a NetBeans module which includes the JOGL JAR files and also the native libraries so my application will be portable?</strong></p>
<p>Follow the next steps and you will see there is not problem to achieve that.</p>
<h2>Environment and requisites</h2>
<p>Here is the list of software and versions I'm using for this example:</p>
<ul>
<li>NetBeans IDE 6.7.</li>
<li>WorldWind Java 0.6 (<a href="http://builds.worldwind.arc.nasa.gov/releases/worldwind-0.6.263.12713.zip">build 263.12713</a>).</li>
<li>JOGL (<a href="http://download.java.net/media/jogl/builds/archive/jsr-231-1.1.1a/">version 1.1.1a</a>). I downloaded versions for Linux and Windows to test the application on two platforms.</li>
</ul>
<p>Install NetBeans if you don't have installed. Download WWJ and JOGL and uncompress it at some place.</p>
<h2>How to create the application</h2>
<p>Ok next is the important part of this article. The basic idea is to create a new NetBeans Platform application with two library modules, containing required JAR files for JOGL and WorldWindJava:</p>
<ol>
<li><strong>Step 1</strong>: Create a NetBeans Platform Application: <a href="./images/2011/03/sshot01.png"><img class="aligncenter size-full wp-image-225" title="sshot01" src="./images/sshot01.png" alt="" width="585" height="398" /></a></li>
<li><strong>Step 2</strong>: Create a library wrapper module for WWJ: <a href="./images/2011/03/sshot02.png"><img class="aligncenter size-full wp-image-226" title="sshot02" src="./images/sshot02.png" alt="" width="578" height="398" /></a>Select the worlwind.jar file from WWJ folder. This will create a module into your NB platform application with the next folder structure:<a href="./images/2011/03/sshot03.png"><img class="aligncenter size-full wp-image-227" title="sshot03" src="./images/sshot03.png" alt="" width="192" height="190" /></a></li>
<li><strong>Step 3</strong>: Create a library wrapper module JOGL:Select the jogl.jar and gluegen.jar files from JOGL folder. In addition to the previous step, here we need to include the native libraries JOGL needs to run. To do that you must to create a folder called release/modules/lib and copy the native libraries.<a href="./images/2011/03/sshot04.png"><img class="aligncenter size-full wp-image-228" title="sshot04" src="./images/sshot04.png" alt="" width="203" height="302" /></a><br />
In the above image you can se the *.so files which corresponds only to the Linux native libraries. To make your application portable to other systems you must need to copy here the required *.dll or any other files.</li>
<li><strong>Step 4</strong>: Create a normal module.This module will contain the code to create a TopComponent window which will show the WWJ virtual globe:
<p style="text-align: center;"><a href="./images/2011/03/sshot05.png"><img class="aligncenter size-full wp-image-229" title="sshot05" src="./images/sshot05.png" alt="" width="578" height="398" /></a></p>
</li>
<li><strong>Step 5</strong>: Set modules dependencies.What you need to do now is to set the dependecies among the modules. WWJ module depends on JOGL one and your previous normal module will depend on WWJ and JOGL, because it will show a window with the virtual globe.</li>
<li><strong>Step 6</strong>: In the previous module create a new TopComponent Window using the wizard: <a href="./images/2011/03/sshot05.png"><br />
</a>Right-click on the module name and select New &gt; Window Component. In the next wizard step select for <em>Position</em> the value <em>editor</em>. Press next and select a name for your TopComponent class (and an icon if desired).&nbsp;</p>
<p>Open the code of your TopComponent and in the <em>init()</em> method add the next lines:</p>
<div id="highlighter_491206">
<div>
<div>
<pre class="brush:java">Model model = (Model)WorldWind.createConfigurationComponent(AVKey.MODEL_CLASS_NAME);
WorldWindowGLCanvas wwj = new WorldWindowGLCanvas();
wwj.setModel(model);

setLayout(new BorderLayout());
add(wwj, BorderLayout.CENTER);</pre>
</div>
</div>
</div>
<p>You will need to import the required packages from WWJ module, which you must set as a dependency previously.</li>
</ol>
<p>If you have followed the above steps the result you must get must be something similar to:</p>
<p style="text-align: center;"><a href="./images/2011/03/sshot06.png"><img class="aligncenter size-full wp-image-230" title="sshot06" src="./images/sshot06.png" alt="" width="610" height="546" /></a></p>
<p>In addition, the benefit of using this approach is your application, once you build a ZIP distribution, will run on different machines thanks to the native libraries attached in the 'lib' folder.</p>
