---
layout: post
title:	"Six Languages to Master"
---

Back in the primordial mists of 2012, Michael O. Church wrote [an article on the six languages he thought every programmer should learn](http://michaelochurch.wordpress.com/2012/07/27/six-languages-to-master/): 

* Python for its legibility and the ease with which it lets you get stuff done in, 
* C to get an understanding of how the actual *computer* involved in computer programming works, 
* ML or Haskell to force the programmer to learn how to make strong design choices by way of the strict type systems, 
* Clojure for being a Lisp with a focus on industry use and practicality,
* Scala for fusing the object-oriented and functional worlds together on the JVM, which has been pretty ubiquitous for nearly two decades at this point.
* and finally English (or whatever the primary language in your area is), because code needs a reason to exist, and it's much easier to explain that in natural language. After even a short time in industry, any programmer will tell you that LoC is an indicator of effort, not quality.

Any article like this invites a lot of debate. Some people would recommend Ruby or even JavaScript over Python, others would place an upcoming language like Rust, D, or Go on there somewhere, or any number of other things. 

I think the article is pretty great overall, and I've passed it along with a PDF of [Think Python](http://www.greenteapress.com/thinkpython/) over to a few of my friends who've wanted to learn to program. 

So how does this affect me? When I started my CS major, Java was the language of choice at my school's CS program. Aside some C here and there, and SML/NJ in a class on discrete math and the foundations of computer science, I didn't have much experience with other languages. I wasn't very curious, but for whatever reason, that changed. I just decided that I needed to learn more, wanted to learn more, and started going off of Michael's list.

Currently, I'm working on learning Python in more depth. I've worked my way through Think Python and [Learn Python the Hard Way](http://learnpythonthehardway.org/book/), and made some small utilities to put on my GitHub. I use VB and C++ at work, and Python is refreshing: it's much more concise and easy to make small, one-shot programs and scripts in. 

For instance, a while back, I made a [utility](https://github.com/EFulmer/music_organizer) to rename and reorganize my music files. To double-check that the files were correctly copied into their new folder, I wrote a function to check by hashing the original and its copy. It's only about 14 lines of very readable Python, and afterwards I was able to move it into a personal utility scripts collection. 

Will I continue through all five languages? I think so. I'm not sure I'll wait until I've considered one "mastered" before starting the next one, but my aim's to write several decently sized, non-trivial programs in each, so I'm able to intelligently discuss the language. For C, I'm enrolled in the [Coursera Hardware/Software Interface](https://www.coursera.org/course/hwswinterface) course, which starts next month. I've done some very basic C, and I have a copy of K&R that's been sitting around for about three years, but I'm not comfortable yet with the language. Hopefully the course will help me change that.

ML, Clojure, and Scala are a little far off in the future. I did some very small SML/NJ assignments for a class I took in college, and I've played around or taken Coursera courses in Clojure and Scala, but I'm by no means experts at those languages. I've also done a little with Haskell, mostly reading [Learn You a Haskell](learnyouahaskell.com) and Real World Haskell. I'm not sure which order I will learn the next three in, but I'd like to write a compiler in one or all of them. I've heard that functional languages are ideal for compiler development, and I'd like to learn why that is firsthand.

There are plenty of languages not on Michael's list that are worth learning, too. Smalltalk for being *the* OO language (Ruby could probably also work, and I'm picking up a tiny bit of it from maintaining this blog on Jekyll), JavaScript for its ubiquity in web development are the first two I can think of. [Rust](www.rust-lang.org) mixes C-style memory management with the type safety of Haskell, and looks pretty cool, too. I'm excited about how much stuff there is to learn.
