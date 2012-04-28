---
layout: post
title: Parallel asynchronous callbacks in JavaScript
location: Vilnius
---
The task was quite simple, but turned out that it was fun to code.
There was some elections data to be presented on the website
and all to be downloaded through the API of election office. 
Each election had its own API URL, but the format of data was 
common. The visualisation script was prepared to present the
data all at once comparing some statistics between each issue. 
The problem then was, how to get data from three different API
URLs effectively and present them after each call is done. 
The solution came from closures of course. 

The problem is, that we want to send a few async requests at 
once and have to keep the program running until all data 
is collected and then some rendering script to be launched. 
This is a classic closure pattern. You send the request which
callbacks keep one shared results object as a closure and
another callback that will be triggered after all data arrive.  

So there is an empty results array (the shared object), there
is a draw_graph callback to be triggered at the end, the is
a counter of how many datasets has been already collected
and that's all. 

<pre><span class="hl kwa">function</span> <span class="hl kwd">load_election_data</span><span class="hl opt">(</span> draw_graph<span class="hl opt">,</span> urls <span class="hl opt">) {</span>
    <span class="hl kwa">var</span> i<span class="hl opt">;</span>
    <span class="hl kwa">var</span> reqs_number <span class="hl opt">=</span> urls<span class="hl opt">.</span>length<span class="hl opt">;</span>
    <span class="hl kwa">var</span> results     <span class="hl opt">=</span> <span class="hl kwa">new</span> <span class="hl kwd">Array</span><span class="hl opt">(</span> reqs_number <span class="hl opt">);</span>
    <span class="hl kwa">var</span> remaining   <span class="hl opt">=</span> reqs_number<span class="hl opt">;</span>

    <span class="hl kwa">for</span><span class="hl opt">(</span> i <span class="hl opt">=</span> <span class="hl num">0</span><span class="hl opt">;</span> i <span class="hl opt">&lt;</span> reqs_number<span class="hl opt">; ++</span>i <span class="hl opt">) {</span>
        <span class="hl opt">(</span><span class="hl kwa">function</span> <span class="hl opt">(</span> inx <span class="hl opt">) {</span>
            $<span class="hl opt">.</span><span class="hl kwd">ajax</span><span class="hl opt">({</span>
                <span class="hl str">'url'</span>     <span class="hl opt">:</span> urls<span class="hl kwc">[inx]</span><span class="hl opt">,</span>
                <span class="hl str">'success'</span> <span class="hl opt">:</span> <span class="hl kwa">function</span> <span class="hl opt">(</span> received_data <span class="hl opt">) {</span>
                    <span class="hl slc">// fill the proper place in the array</span>
                    results<span class="hl kwc">[inx]</span> <span class="hl opt">=</span> received_data<span class="hl opt">;</span>
                    remaining <span class="hl opt">-=</span> <span class="hl num">1</span><span class="hl opt">;</span>
                    <span class="hl slc">// check if all reasults are downloaded</span>
                    <span class="hl kwa">if</span><span class="hl opt">( !</span>remaining <span class="hl opt">) {</span>
                        <span class="hl kwd">callback</span><span class="hl opt">(</span> results <span class="hl opt">);</span>
                    <span class="hl opt">}</span>
                <span class="hl opt">}</span>
            <span class="hl opt">});</span>
        <span class="hl opt">})(</span>i<span class="hl opt">);</span>
    <span class="hl opt">}</span>
<span class="hl opt">}</span></pre>

As it's seen in the code above, an empty array is created in
advance. That's because is will let keep results ordered, 
tough the order they arrive is not predictable. 
