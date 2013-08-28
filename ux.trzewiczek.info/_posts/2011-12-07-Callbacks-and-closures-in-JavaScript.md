---
layout: post
title: Callbacks and closures in JavaScript
location: Warszawa
---
A classic example of a closure - probably one of the post powerfull JavaScript feature - is the counter.

<pre><span class="hl kwa">function</span> <span class="hl kwd">counter</span> <span class="hl opt">() {</span>
  <span class="hl kwa">var</span> num <span class="hl opt">=</span> <span class="hl num">0</span><span class="hl opt">;</span>

  <span class="hl kwa">return</span> <span class="hl opt">{</span>
      add_one<span class="hl opt">:</span> <span class="hl kwa">function</span> <span class="hl opt">() {</span>
          num <span class="hl opt">+ =</span> <span class="hl num">1</span><span class="hl opt">;</span>
          <span class="hl kwa">return</span> num<span class="hl opt">;</span>
      <span class="hl opt">},</span>
      get_num<span class="hl opt">:</span> <span class="hl kwa">function</span> <span class="hl opt">() {</span>
          <span class="hl kwa">return</span> num<span class="hl opt">;</span>
      <span class="hl opt">}</span>
  <span class="hl opt">};</span>
<span class="hl opt">}</span>

<span class="hl kwa">var</span> my_counter <span class="hl opt">=</span> <span class="hl kwd">counter</span><span class="hl opt">();</span>
console<span class="hl opt">.</span><span class="hl kwd">log</span><span class="hl opt">(</span> my_counter<span class="hl opt">.</span><span class="hl kwd">get_num</span><span class="hl opt">() );</span>
console<span class="hl opt">.</span><span class="hl kwd">log</span><span class="hl opt">(</span> my_counter<span class="hl opt">.</span><span class="hl kwd">add_one</span><span class="hl opt">() );</span> </pre>

Simple and understandable. Two interesting things about this solution are:
  
*  despite the fact that the counter function terminates, after creating and 
returning the new object, the __num__ object is stored in memory (garbage 
collector won't remove it), because both methods in returned object have
access to it, so they keep the reference (this is the closure)
*  num variable is only available via two methods for the newly created object, 
which makes it private. This mean that the returned object becomes very clear 
and one hundred percent secure interface to the state stored in the closure.

Nice example and real-world actually, because using closures as a private 
state with a public interface pattern is extremely common. But to understand 
the full power of closures, let's look at an example from the asyncronous
programming area.

The example below is a standard asynchronous data access we use in Raw Salad. 
There are three modules:
*  **\_gui** which is responsible for handling the GUI events and rendering things
*  **\_store** which keeps the current state of the application
*  **\_db** which, if necessary, grabs data from database

The story would be something like this: after I click the button, data is being
taken from the store and nicely rendered in the browser. If there's no such data
in the store, the \_db module is requesting database for all we actually need.
After the data is back, it's saved in store and then passed to GUI for rendering.
In the second scenario no one knows what will happen between the front-end and 
the back-end and how long we will wait for the response. What's more,
the \_gui module can't predict what data is available in the store and what has
to be downloaded from the back-end. In fact the \_gui module should know nothing
about the way data is stored and manipulated - it handles events and renders
the website. 
  
<h3>_gui.js</h3>
<pre>$<span class="hl opt">(</span><span class="hl str">'#more-data-please'</span><span class="hl opt">).</span><span class="hl kwd">click</span><span class="hl opt">(</span> <span class="hl kwa">function</span> <span class="hl opt">() {</span>
    <span class="hl kwa">var</span> destination <span class="hl opt">=</span> $<span class="hl opt">(</span><span class="hl kwa">this</span><span class="hl opt">).</span><span class="hl kwd">parent</span><span class="hl opt">();</span>
    <span class="hl kwa">var</span> render_data <span class="hl opt">=</span> <span class="hl kwa">function</span> <span class="hl opt">(</span> from_store <span class="hl opt">) {</span>
        <span class="hl kwa">var</span> new_data <span class="hl opt">=</span> $<span class="hl opt">(</span><span class="hl str">'&lt;h1&gt;'</span> <span class="hl opt">+</span> from_store <span class="hl opt">+</span> <span class="hl str">'&lt;/h1&gt;'</span><span class="hl opt">);</span>
        destination<span class="hl opt">.</span><span class="hl kwd">append</span><span class="hl opt">(</span> new_data <span class="hl opt">);</span>
    <span class="hl opt">});</span>

    _store<span class="hl opt">.</span><span class="hl kwd">get_new_data</span><span class="hl opt">(</span> render_data <span class="hl opt">);</span>
<span class="hl opt">});</span></pre>

<br />
<h3>_store.js</h3>
<pre><span class="hl kwa">function</span> <span class="hl kwd">get_new_data</span><span class="hl opt">(</span> callback <span class="hl opt">) {</span>
    <span class="hl kwa">var</span> data <span class="hl opt">=</span> <span class="hl kwd">data_from_store</span><span class="hl opt">();</span>

    <span class="hl kwa">if</span><span class="hl opt">( !!</span>data <span class="hl opt">) {</span>
        <span class="hl kwd">callback</span><span class="hl opt">(</span> data <span class="hl opt">);</span>
    <span class="hl opt">}</span>
    <span class="hl kwa">else</span> <span class="hl opt">{</span>
        _db<span class="hl opt">.</span><span class="hl kwd">get_more_data</span><span class="hl opt">(</span> <span class="hl kwa">function</span> <span class="hl opt">(</span> new_data <span class="hl opt">) {</span>
            <span class="hl kwd">store_new_data</span><span class="hl opt">(</span> new_data <span class="hl opt">);</span>
            <span class="hl kwd">callback</span><span class="hl opt">(</span> new_data <span class="hl opt">);</span>
        <span class="hl opt">});</span>
    <span class="hl opt">}</span>
<span class="hl opt">}</span></pre>

<br />
<h3>_db.js</h3>
<pre><span class="hl kwa">function</span> <span class="hl kwd">get_more_data</span><span class="hl opt">(</span> callback <span class="hl opt">) {</span>
    $<span class="hl opt">.</span><span class="hl kwd">get</span><span class="hl opt">(</span><span class="hl str">'/more_data/'</span><span class="hl opt">, {},</span> callback<span class="hl opt">);</span>
<span class="hl opt">}</span></pre>

Of course, some things are simplified here - it's just a pattern, not 
a specific case. The important thing is that with the closure callback 
sent from \_gui to \_store has a reference to the destination object.
It mean that as soon as the data is available, the callback know how 
and **where** to render them. 
This simple example shows that closures becomes a great means of code 
modularization. \_gui module knows what data it wants, how to present 
it and is deeply convinced that all the data is available from \_store.
On the other hand store has no idea who asks for data and how it's going
to use the data, but knows the with a request a black box came with inscription:
"Do the magic". What's more - if there's no data in the store, the \_store 
module knows that it can request that data from \_db module. And again
this module has no idea who's asking for the data and why, but gets a 
little black box with inscription "All the magic happens here". 
