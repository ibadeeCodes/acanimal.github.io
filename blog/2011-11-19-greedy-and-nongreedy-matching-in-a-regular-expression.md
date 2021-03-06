---
layout: post
title: Greedy and Nongreedy Matching in a Regular Expression
date: 2011-11-19 00:11
tags:
- regular expression
- tools
- tricks
---
This questions has come to me many times so it is time to write a post that acts as a reminder.

<p>Currently I have a string like</p>
<pre class="brush:perl">ftp://user:password@server/dirA/dirB/file</pre>
<p>and what i want is parse it to get the user, password, server and path to the file (/dirA/dirB/file). My first try was:</p>
<pre class="brush:perl">ftp://(\S+):(\S+)@(\S+)(/\S+)</pre>
<p>but that returns me <em>server=server/dirA/dirB</em>, which isn't what I want. The idea is that the group after the @ would make a non gready match. This is achieved using the ? char. So the final and right regular expression will becomes:</p>
<pre class="brush:perl">ftp://(\S+):(\S+)@(\S+?)(/\S+)</pre>
<p>which returns <em>server=server</em> and <em>file=/dirA/dirB/file</em>.</p>
