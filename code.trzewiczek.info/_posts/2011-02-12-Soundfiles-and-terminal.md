---
layout: post
title: Sound and Terminal
location: Dąbrowa Górnicza
---
My hard drive is full of mp3 files - my phone doesn't understand 
a different format, so I'm forced to keep them this way. But when 
I want to publish my music on my website I choose ogg format - the
open audio format available for modern browsers. How to convert 
files from mp3 to ogg in terminal? I just need *lame*, *oggenc* 
and a small pipe.

*lame* is a small program that allows you to create files in mp3 
format. Its list of options is long, but for us the most interesting 
is the option - *decode*. Thanks to it we can read the mp3 file as 
a raw audio stream. Of course it does not improve sound quality, 
but at least lets me do something with this stream.

The second program we need is *oggenc* - the ogg vorbis codec. 
Although its list of options is even longer, we will use only one. 
Option *-* (the dash) allows you to load raw audio stream.

The connection (via pipe) looks something like this:

  <pre>
  lame - decode my_file.mp3 - | oggenc - raw -o my_file.ogg</pre>

What's going on with these additional *-* and *|*? The point is that 
both *lame* and *oggenc* need two files - one for the input (file we
want to convert) and one output (new file to be created). But to 
combine both programs I use the Unix pipe mechanism. Lonely dashes 
in this snippet of code mean: *save to* / *read from* standard IO stream, 
which is __de facto__ terminal window itself. Thus we do not create 
a temporary file with the intermediate state (raw audio file), but
one goes straight to the other.

Remaining question is: how to do it in bulk? A simple bash loop will do:

  <pre>
  for f in * mp3; do
    lame - decode $ f - | oggenc - raw - about ${f/mp3/ogg};
  done</pre>


The strange looking ${f/mp3/ogg} changes the extension in the
files names - nothing special actually. 
