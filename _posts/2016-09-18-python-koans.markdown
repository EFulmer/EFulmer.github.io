---
layout: post
title: "Learning Test-Driven Design Through Python Koans: My Experience"
---

Way, way, way back in July, at [PyGotham](https://2016.pygotham.org/), someone advised me to check out the Python Koans to learn more about Test Driven Design. This was in response to a question I'd had about testing. I haven't worked with a team that used an extensive unit testing suite before, so best practices for writing unit testing suites and general testing practices are out of my area of expertise.

What I Learned About Testing
----------------------------

Reading the unit tests in the Koans and getting them to work have taught me about what good unit tests should look like. 

To summarize it in a tweet, a good unit test suite *should test every property of your code that can be tested in isolation, and you should update it alongside library/application code.* For instance, if you were writing a stack, your unit tests should be things like these:

1. Checking that a new stack is empty.
2. Calling `pop` on a new stack throws an exception, or whatever the equivalent error is in your language.
3. `pop` still fails in the proper way after you've emptied a pre-existing stack.
4. `pop` returns the top value if the stack is not empty.
5. `push` adds the argument to the top of the stack.
6. `push` doesn't perturb the rest of the stack; `.length` (if defined in your API) is incremented, the old top item is still in its proper position and so on. 
7. Each behavior that you think of in 6 should be checked in its own unit test.
8. *Bonus:* If you have a `.length` property or method, it should never be a negative number.

Now that I'm comfortable with reading unit tests, I'll be writing them for anything I write that's not a tiny script (ballparked at around 50 lines of code in Python).

What I Learned About Python
--------------------------

I'm pretty comfortable with Python, but the koans still served as a good refresher. I did take away a few lessons to carry forward in my future coding, though.

I still skim over code and make incorrect assumptions about what it does more than I would like to. This is normal, and human, but you do need to remind yourself because if you're lazy and skim over code instead of reading it in detail, it *will* come back to bite you at some point.

The Python Koans also forced me to confront some things I don't know as well as I'd like, such as what I'd call the "nuts and bolts" of Python classes: `__dict__`, `hasattr`, `setattr`, `getattr`/`getattribute`. Also, I know enough about decorators, and generators.

I wouldn't recommend this as a teaching method to someone who's totally new to programming (I like [Think Python]() and [Learn Python The Hard Way]() for that). But the koans are not intended for that. I'd recommend working through them to anyone learning Python from another language, especially if they have experience in languages considered "dynamic" or "scripting" like Perl, JS, or Ruby. Anyone with a decent level of Python knowledge looking to learn more about writing strong unit tests -- like me -- wuld benefit too.

Conclusion
----------

I really enjoyed doing the Python Koans. I learned a ton about reading code, testing, and consequently writing better code. I'm considering working programming koans into [the way I learn a new language](/2016/02/28/how-to-learn-to-code/), either by solving an existing set, or writing one.

Right now I'm actually working at writing a set of [programming koans in Rust](https://github.com/EFulmer/rust-koans). A big headache was figuring out how to write an elegant (or at least comprehensible) error/"fill me in" item to place in the `asserts`, as Rust's type system is much stricter than Python's. 

I'm planning on writing about how I solved that tomorrow.

Until next time!
