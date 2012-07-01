---
layout: post
title: Execution context and code organization
location: Warszawa
---
JavaScript execution context goes through two phases:
entering the context which is declaration phase and
code execution phase, when the code is executed from
the top down. What's interesting here is that one can
use it to structure the code in a clear way. Let's see
the example:

<pre>
<span class="hl kwa">var</span> _myModule = (<span class="hl kwa">function</span> () {
    <span class="hl slc">// p u b l i c   i n t e r f a c e </span>
    that = {};

    that.do_sth_with = <span class="hl kwa">function</span> (num) {
        <span class="hl kwa">var</span> result = square(num);
        console.log(result);

        <span class="hl kwa">return</span> result;
    };

    that.do_sth_else_with = <span class="hl kwa">function</span> (num) {
        <span class="hl kwa">var</span> result = cube(num);
        console.log(result);

        <span class="hl kwa">return</span> result;
    };

    <span class="hl slc">// s e t t i n g   u p   l o g g e r s</span>
    that.do_sth_with = _logger.pack(that.do_sth_with);

    <span class="hl kwa">return</span> that;

    <span class="hl slc">// p r i v a t e   i n t e r f a c e</span>
    <span class="hl kwa">function</span> square(x) {
        <span class="hl kwa">return</span> x * x;
    }

    <span class="hl kwa">function</span> cube(x) {
        <span class="hl kwa">return</span> x * x * x;
    }
})();
</pre>

How it works? When the interpreter enters the execution
context of the function expression inside the brackets,
it creates the arguments object and then goes to all 
function declarations collecting them at the top of 
the function - practice known as __hoisting__. That means
that __helper1__ and __helper2__ goes up to the top of the function
even they are declared after the return statement.
Then the that variable declaration is done, but without
the initialization. After that the code execution phase
starts and that is initialized with a public interface
object and only after that (as the code execution goes
from top down) the __that.do_sth_with__ is being overwritten
but the new packed version of itself. Finally, the 
that object (which is the public interface of this module)
is returned and assigned to the __\_myModule__ variable. 

I personally like this way of organizing the modules, 
because it separates the private from public and working 
with other developers I don't have to deal with internals 
that much, because everything I'm interested in is grouped 
in one place and the helpers are somewhere down at the
bottom of the file. What's more - there is a special
space for setting up the loggers and again it's very
predictable for me where to look for it and where to 
add it to keep it save and reliable. 

This kind of organization of the module code can be pushed
a little farther, by introducing a shared functions space. 
Let's modify a little the previous example:

<pre>
<span class="hl kwa">var</span> _myModule2 = (<span class="hl kwa">function</span> () {
    <span class="hl slc">// p u b l i c   i n t e r f a c e </span>
    that = {};

    that.do_sth_with = <span class="hl kwa">function</span> (num) {
        <span class="hl kwa">var</span> result = square(num);
        console.log(result);

        <span class="hl kwa">return</span> result;
    };

    that.do_sth_else_with = <span class="hl kwa">function</span> (num) {
        <span class="hl kwa">var</span> result = cube(num);
        console.log(result);

        <span class="hl kwa">return</span> result;
    };

    <span class="hl slc">// s h a r e d   i n t e r f a c e</span>
    that.cube = cube;

    <span class="hl slc">// s e t t i n g   u p   l o g g e r s</span>
    that.do_sth_with = _logger.pack(that.do_sth_with);

    <span class="hl kwa">return</span> that;

    <span class="hl slc">// p r i v a t e   i n t e r f a c e</span>
    <span class="hl kwa">function</span> square(x) {
        <span class="hl kwa">return</span> x * x;
    }

    <span class="hl kwa">function</span> cube(x) {
        <span class="hl kwa">return</span> x * x * x;
    }
})();
</pre>

We added a new function to the module called cube. It's
implementation stays in the private section, but after
defining the public interface, we added this function
to that object, so it's publicly accessible. Why we do it?
The reason is that in the client's code there might be 
a situation that the cube method of the object _myModule2
will have to be overwritten - e.g. packed by a logger or 
some validator functions. The assignment will work perfectly,
but on the other hand we won't change the behaviour of our
private cube function, so when it will be called from within
the _myModule2.do_sth_else method everything will stay
as it was at the beginning. That's why it's called shared
interface, because it becomes public but in a secure way
for the private interface.

<pre>
<span class="hl slc">// we run the public cube function inside the console.log</span>
<span class="hl slc">// because cube doesn't print the results by itself</span>
console.log('---- 1 ----');
console.log(_myModule2.cube(<span class="hl num">3</span>));
<span class="hl slc">// we run the method that uses private cube internally</span>
console.log('---- 2 ----');
_myModule2.do_sth_else_with(<span class="hl num">3</span>);
<span class="hl slc">// we overwrite the public method by some new function</span>
_myModule2.cube = <span class="hl kwa">function</span> (x) { console.log(x/<span class="hl num">1000</span>); };
<span class="hl slc">// again we run the method that uses private cube internally</span>
<span class="hl slc">// to see if the private one has been affected</span>
console.log('---- 3 ----');
_myModule2.do_sth_else_with(<span class="hl num">3</span>);
<span class="hl slc">// and now we run the new public cube method to see</span>
<span class="hl slc">// if the change has been successful </span>
console.log('---- 4 ----');
_myModule2.cube(<span class="hl num">3</span>);
</pre>

As we see the shared interface worked perfect. Again, by
using the shared interface we separate private from 
public on the one hand and on the other one we give a
hint to other developers that potentially this method
might be changed somewhere in the client's code and the 
module is prepared for that situation. 
