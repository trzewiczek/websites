---
layout: post
title: Simple step logger in JavaScript
location: Warszawa
---
Working on a new version of Raw Salad we faced a crazy bug.
One part of the interface started to show and hide in a 
strange way with no actual request to do that. After a few
minutes of debugging I couldn't actually track the real 
sequence of function calls. After fifteenth or so debugger
trap I thought I need a step logger. So here it is:

<pre><span class="hl kwa">var</span> _logger <span class="hl opt">= (</span><span class="hl kwa">function</span> <span class="hl opt">() {</span>
    <span class="hl slc">// P R I V A T E   I N T E R F A C E</span>
    <span class="hl slc">// counter and incrementor for logging</span>
    <span class="hl slc">// the sequence of functions execution</span>
    <span class="hl kwa">var</span> counter <span class="hl opt">=</span> <span class="hl num">0</span><span class="hl opt">;</span>
    <span class="hl kwa">var</span> inc_counter <span class="hl opt">=</span> <span class="hl kwa">function</span> <span class="hl opt">() {</span>
        counter <span class="hl opt">+=</span> <span class="hl num">1</span><span class="hl opt">;</span>

        <span class="hl kwa">return</span> counter<span class="hl opt">;</span>
    <span class="hl opt">};</span>
    <span class="hl slc">// actual logging function</span>
    <span class="hl kwa">var</span> logger <span class="hl opt">=</span> <span class="hl kwa">function</span> <span class="hl opt">(</span> fn<span class="hl opt">,</span> debug<span class="hl opt">,</span> args <span class="hl opt">) {</span>
        <span class="hl kwa">var</span> i<span class="hl opt">;</span>
        <span class="hl kwa">var</span> arg<span class="hl opt">;</span>
        <span class="hl slc">// print the the function execution index and the timestamp</span>
        console<span class="hl opt">.</span><span class="hl kwd">log</span><span class="hl opt">(</span> <span class="hl str">&quot;&gt;&gt;&gt; &quot;</span> <span class="hl opt">+</span> <span class="hl kwd">inc_counter</span><span class="hl opt">() +</span> <span class="hl str">&quot; :: &quot;</span> <span class="hl opt">+</span> <span class="hl kwa">new</span> <span class="hl kwd">Date</span><span class="hl opt">() );</span>
        console<span class="hl opt">.</span><span class="hl kwd">log</span><span class="hl opt">(</span> <span class="hl str">&quot;&gt;&gt;&gt; In function: &quot;</span> <span class="hl opt">+</span> fn<span class="hl opt">.</span>name <span class="hl opt">);</span>
        <span class="hl slc">// if not in debug mode - print all function arguments values</span>
        <span class="hl kwa">if</span><span class="hl opt">( !</span>debug <span class="hl opt">) {</span>
            <span class="hl kwa">for</span><span class="hl opt">(</span> i <span class="hl opt">=</span> <span class="hl num">0</span><span class="hl opt">;</span> i <span class="hl opt">&lt;</span> args<span class="hl opt">.</span>length<span class="hl opt">; ++</span>i <span class="hl opt">) {</span>
                arg <span class="hl opt">=</span> JSON<span class="hl opt">.</span><span class="hl kwd">stringify</span><span class="hl opt">(</span> args<span class="hl kwc">[i]</span> <span class="hl opt">);</span>
                console<span class="hl opt">.</span><span class="hl kwd">log</span><span class="hl opt">(</span> <span class="hl str">&quot;&gt;&gt;&gt; arg: &quot;</span> <span class="hl opt">+</span> arg <span class="hl opt">);</span>
            <span class="hl opt">}</span>
        <span class="hl opt">}</span>
    <span class="hl opt">};</span>

    <span class="hl slc">// P U B L I C   I N T E R F A C E</span>
    <span class="hl kwa">var</span> that <span class="hl opt">= {};</span>
    <span class="hl slc">// public logger function</span>
    <span class="hl slc">// takes function to pack over and debug mode t/f</span>
    that<span class="hl opt">.</span>pack <span class="hl opt">=</span> <span class="hl kwa">function</span> <span class="hl opt">(</span> fn<span class="hl opt">,</span> debug <span class="hl opt">) {</span>
        <span class="hl slc">// return multi-arity function that packs over original one</span>
        <span class="hl kwa">return function</span> <span class="hl opt">(</span> <span class="hl com">/* some potential arguments */</span> <span class="hl opt">) {</span>
            <span class="hl kwa">var</span> args <span class="hl opt">=</span> Array<span class="hl opt">.</span><span class="hl kwa">prototype</span><span class="hl opt">.</span>slice<span class="hl opt">.</span><span class="hl kwd">call</span><span class="hl opt">(</span> arguments <span class="hl opt">);</span>
            <span class="hl slc">// do the actual logging</span>
            <span class="hl kwd">logger</span><span class="hl opt">(</span> fn<span class="hl opt">,</span> debug<span class="hl opt">,</span> args <span class="hl opt">);</span>
            <span class="hl slc">// if in debug mode - start debugger</span>
            <span class="hl kwa">if</span><span class="hl opt">( !!</span>debug <span class="hl opt">)</span> <span class="hl kwa">debugger</span><span class="hl opt">;</span>
            <span class="hl slc">// execute the original function</span>
            fn<span class="hl opt">.</span><span class="hl kwd">apply</span><span class="hl opt">(</span> <span class="hl kwa">this</span><span class="hl opt">,</span> args <span class="hl opt">);</span>
        <span class="hl opt">};</span>
    <span class="hl opt">};</span>

    <span class="hl kwa">return</span> that<span class="hl opt">;</span>
<span class="hl opt">})();</span></pre>

And to actually use it some function has to be packed with it:

<pre><span class="hl slc">// example function one</span>
<span class="hl kwa">function</span> <span class="hl kwd">hide_panel</span><span class="hl opt">(</span> panel <span class="hl opt">) {</span>
    <span class="hl kwa">if</span><span class="hl opt">( !</span>panel<span class="hl opt">.</span><span class="hl kwd">isHidden</span><span class="hl opt">() ) {</span>
        panel<span class="hl opt">.</span><span class="hl kwd">hide</span><span class="hl opt">();</span>
    <span class="hl opt">}</span>
<span class="hl opt">}</span>

<span class="hl slc">// example function two</span>
<span class="hl kwa">function</span> <span class="hl kwd">show_panel</span><span class="hl opt">(</span> panel <span class="hl opt">) {</span>
    <span class="hl kwa">if</span><span class="hl opt">(</span> panel<span class="hl opt">.</span><span class="hl kwd">isHidden</span><span class="hl opt">() ) {</span>
        panel<span class="hl opt">.</span><span class="hl kwd">show</span><span class="hl opt">();</span>
    <span class="hl opt">}</span>
<span class="hl opt">}</span>

<span class="hl kwa">var</span> hide_panel <span class="hl opt">=</span> _logger<span class="hl opt">.</span><span class="hl kwd">pack</span><span class="hl opt">(</span> hide_panel <span class="hl opt">);</span>
<span class="hl kwa">var</span> show_panel <span class="hl opt">=</span> _logger<span class="hl opt">.</span><span class="hl kwd">pack</span><span class="hl opt">(</span> show_panel <span class="hl opt">);</span></pre>

So how it works? The logger takes a function (hide_panel and show_panel) 
as an argument and optional boolean switching debug mode on and off (not present 
in above example) and returns a new function with an extra logging code 
added. This new function we store under the same name as the original one, 
to keep logging fact invisible for the rest of the code that uses original function.

What next? It's actually quite straightforward - the returned function logs
the call, runs debugger if in the debug mode and executes the original
function with all arguments passed by the user. 

The logger function itself prints the execution index with timestamp
(to see the real sequence of function calls), function's name and
the list of arguments if not working in the debug mode (no reason
to do that then). 

Quite simple, but turned out to be really helpful. It's a good example
of closures and high-order functions use in JavaScript as well actually. 
Have fun logging!








