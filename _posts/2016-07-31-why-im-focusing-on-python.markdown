---
layout: post
title:	"Why I'm Focusing on Python"
---

For the past four years, I've been a programming language dilettante. 

I would learn the basics of Haskell through a couple of tutorials and books, then read a blog post about Scala. I'd decide that no, Scala is definitely going to be the next big thing, enough with Haskell, and start learning Scala. After that I'd read an article praising Clojure and decide that Clojure was the language I was *really* going to master. Next I'd see some Reddit post about how Clojure isn't a true Lisp and that we should all be using Scheme instead, so I'd start on Scheme. Then I would hear that Go is going to be huge, so I'd learn Go.

You get the point. I was unfocused, and never satisfied with the rate at which I was gaining knowledge. I realized on some level that this wasn't the best way to learn, and advance my career. When I finally verbalized this programming language selection thought process, I found two big issues:

1. People simply don't have enough time to learn all of these languages well. I don't have a family, I'm not an adminstrator of any professional or nonprofit organization, but even with my limited obligations, I don't have sufficient time to master a whole ton of languages.
2. Being a master is more rewarding than being a generalist. The problem with mastery isn't so much that it's hard (though mastery definitely is hard), it's that the choice of what to master is intimidating. Should you become an expert at Python or Ruby or even Perl (still plenty of Perl jobs out there)? Java or .NET?

After realizing this, my next step was to decide *which* language to focus on. Choosing Python was pretty easy for a bunch of reasons. Some of those reasons are related to the language, others are related to industry and the community.

Language reasons
----------------

**Python's syntax and semantics are relatively clear and uniform.** I like the visual outline that the indentation structure enforces. I like the clean list/dictionary literal syntax. Reading Python has always felt generally easier than reading most other languages to me.

**The language is very expressive.** Python has features like list comprehensions that allow you to express complicated ideas simply. Generators are another neat feature which I honestly don't yet truly understand the power of. I feel that Python hits a wonderful middle ground between being overly verbose (so you know exactly what the code does, but you have to read a lot more of it) and being overly flexible to the point that there are so many corner cases you're not sure what it does.

**The standard library is fantastic.** There's tons of great packages that come with Python. [`collections`](https://docs.python.org/3.5/library/collections.html) offers custom subclasses of the built-in collections for some more specialized, but pretty common, use cases. Need to count how many times something shows up in a list/string/query result? `Counter` will do that for you! If you need a dictionary that can automatically generate default values for new keys, then `defaultdict` will be a big help. [`argparse`](https://docs.python.org/3.5/library/argparse.html) is a really cool module for building command-line utilities. [`webbrowser`](https://docs.python.org/3.5/library/webbrowser.html) can open up a browser for users. And there's plenty more standard library modules which are either a little esoteric but very interesting looking, like [`dis`](https://docs.python.org/3/library/dis.html), or really practical looking but I haven't had the chance to study them in-depth yet, like [`functools`](https://docs.python.org/3/library/functools.html) and [`itertools`](https://docs.python.org/3/library/itertools.html).

Community reasons
-----------------

**Tons of available packages, covering many different purposes.** I think the language features I wrote about just before are a big contributor to this.

**Friendly community.** At least in New York City, there is a very large and friendly community of Python users. 

The [Learn Python NYC](http://www.meetup.com/learn-python-nyc/) Meetup has three informal events every week where anyone can come in, work on something, or ask questions. It's a very welcoming community, and I've learned a lot just by going regularly and listening to people talk about their own work or just different parts of Python or the big libraries like Flask and Numpy. 

Since Python's becoming one of the most popular programming languages worldwide, I'm sure that the Python community is strong in most other cities out there. And if there isn't one in your city, my understanding is that [the PSF](https://www.python.org/psf/) offers grants and resources to promote Python all around the world.


I'm really excited to be focusing on Python! One part of this Python focus is going to be moving my personal GitHub pages blog from Jekyll to Pelican. I'm also learning Numpy and Pandas. It's a cool road to be travelling down.

And regarding other languages, since Python can't do it all: there's a Rust post coming up, too!
