---
layout: post
title:	"How to Learn to Code"
---

How to Learn to Code
====================

I've had a few of my friends ask me for help with learning to program. I love it when people ask me about what I do, but there are a few common patterns I've noticed with people. I'm writing this down as some informal notes about the stumbling blocks I've seen my friends hit.

Just pick a language and code.
------------------------------

I think this is the most important thing for a newbie. One of my best friends, a guy I consider absolutely brilliant, wasted a lot of time vacillating between whether he wanted to learn Java or Python, and if it was Python, whether to do Python 2 or Python 3. Now life is long, but that just means you have enough time to focus on something long enough to make mistakes with it and learn from them.

So for that reason I recommend [Python](https://www.python.org/), specifically Python 3, unless you have a really strong reason to pick some other specific language. While it's not my favorite language -- though I do like it a lot -- I think it's the best pick for newbies. 

Why? Python puts an emphasis on code readability and a standard coding style. As well, it's forgiving, mainstream enough, and offers an interactive shell that lets you investigate what things do without having to go through a build and run step.

Here's a brief outline on my approach to learning new languages:

### Go through a tutorial or two on the language. 

Actually work through them - don't settle for passively reading them, you're too smart to limit yourself by stopping there! Type in any code examples and run them!


### Identify a couple of the language's major libraries and frameworks. 

These exist to solve common problems (like web development, data analysis, or database interaction), or address pain points in the language's design. You don't have to learn them at this point.

For languages I use, here's a few examples:

Python has [Django](https://www.djangoproject.com/) and [Flask](http://flask.pocoo.org/) for web application development, [SQLAlchemy](http://www.sqlalchemy.org/) for database interop, and [Pandas](http://pandas.pydata.org/) for data analysis. 

[Ruby](https://www.ruby-lang.org/en/) has [Rails](http://rubyonrails.org/) for web application development.

[Haskell](https://www.haskell.org/) has [Lens](https://github.com/ekmett/lens) for complex data structures, and [Yesod](http://www.yesodweb.com/) for web application development.

### Write stuff in it. 

Progress from really small things that you may find useful (what's the largest file in this folder? How can I re-organize my photos?) to applications and libraries of more significant size. 

Depending on your comfort level and prior experience, "significant size" may be 500 lines of code, or 15,000. Use some of those major libraries and frameworks in your code. At this point, focus on getting things done; a few short tutorials are useful, but don't get obsessed with finding the optimal library, or memorizing its minutiae. 

Key at this step is **focusing on getting stuff done over writing beautiful code.** Accept the fact that you will write code that's "bad" in some way, whether it's inefficient, hard to read, or unaware of the language's best practices. The perfect is the enemy of the good here.

I'm currently writing a Fire Emblem clone in Haskell, and much of my code is inelegant and ugly. I can always rewrite it in more concise, readable, and modular style later, and learn from the rewriting process.  I'd rather my first go be perfect, but that's unrealistic. You have to be bad at something before you can get good at it.

### Ask questions!!!

Most people are pretty friendly and like to share their knowledge. One of the best ways to learn something is to teach it to others. Don't be afraid to ask questions on [Stack Overflow](http://stackoverflow.con)!

Here's an example from me: one time I was building a tiny web app, and hit a problem that absolutely stonewalled me. I posted a question on Stack Overflow, which you can see [here](http://stackoverflow.com/questions/24566538/flask-rendering-unicode-characters-in-template), and someone answered within half an hour -- on Friday night!

User groups and meetups are a great resource; their express purpose is to provide a way for users and enthusiasts to connect and share knowledge. If you're in a reasonably sized city, you can probably find a meetup group for most mainstream technologies. It may be harder to find an OCaml users group in say, Omaha, but there's certainly [Python](http://www.omahapython.org/blog/) and [Ruby](http://www.meetup.com/Omaha-Ruby-Meetup/) meetup groups.

Now get out there and code!
