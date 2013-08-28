---
layout: post
title: Changing arity of recursive calls in JavaScript
location: Dąbrowa Górnicza  
---
For some time, I thouhgt how to manage the changing
of arity of the function in the recursive calls. 
It all started with playing around with Little Schemer
book and came really handy when I was preparing a 
simple logging function for debugging Raw Salad. 

After some time of investigating I came out with
such a solution, presented here on an <i>or</i>
function:

  <pre><span class="hl kwa">function</span> <span class="hl kwd">or</span><span class="hl opt">() {</span>
    <span class="hl slc">// change arguments object to array</span>
    <span class="hl kwa">var</span> args <span class="hl opt">=</span> Array<span class="hl opt">.</span><span class="hl kwa">prototype</span><span class="hl opt">.</span>slice<span class="hl opt">.</span><span class="hl kwd">call</span><span class="hl opt">(</span> arguments <span class="hl opt">);</span>

    <span class="hl kwa">if</span><span class="hl opt">( !</span>args<span class="hl opt">.</span>length <span class="hl opt">) {</span>
        <span class="hl kwa">return false</span><span class="hl opt">;</span>
    <span class="hl opt">}</span>
    <span class="hl kwa">else</span> <span class="hl opt">{</span>
        <span class="hl kwa">if</span><span class="hl opt">( !!</span>args<span class="hl kwc">[0]</span> <span class="hl opt">) {</span>
            <span class="hl kwa">return true</span><span class="hl opt">;</span>
        <span class="hl opt">}</span>
        <span class="hl kwa">else</span> <span class="hl opt">{</span>
            <span class="hl kwa">return</span> or<span class="hl opt">.</span><span class="hl kwd">apply</span><span class="hl opt">(</span> <span class="hl kwa">this</span><span class="hl opt">,</span> args<span class="hl opt">.</span><span class="hl kwd">slice</span><span class="hl opt">(</span><span class="hl num">1</span><span class="hl opt">) );</span>
        <span class="hl opt">}</span>
    <span class="hl opt">}</span>
<span class="hl opt">}</span></pre>

So now, it's possible to pass <i>or</i> as a function
and use it as multi-arity function - no need to pass
a list of values. Fun!
