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

