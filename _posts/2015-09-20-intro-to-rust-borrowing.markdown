---
layout: post
title: "Some notes on Borrowing in Rust"
date: 2015-09-20
---

Right now I'm learning the [Rust programming language](http://rust-lang.org/). When I learn a new language, I start off by reading a few tutorials, and then going through some exercises in it. 

I'm at the second stage now in Rust: using [this awesome set of exercises](https://github.com/carols10cents/rustlings) to get a better grasp on how Rust actually works.

The first few exercises were pretty simple. `ex5.rs` was the first that I needed to think for a minute about:

{% highlight rust %}
// Make me compile!

enum Reaction<'a> {
    Sad(&'a str),
    Happy(&'a str),
}

fn express(sentiment: Reaction) {
    match sentiment {
        Reaction::Sad(s) => println!(":( {}", s),
        Reaction::Happy(s) => println!(":) {}", s),
    }
}

fn main () {
    let x = Reaction::Happy("It's a great day for Rust!");
    express(x);
    express(x);
    let y = Reaction::Sad("This code doesn't compile yet.");
    express(y);
}
{% endhighlight %}

This program won't compile due to Rust's restrictions on *borrowing* values. Here's the error I get:

{% highlight rust %}
ex5.rs:18:25: 18:26 error: use of moved value: `x` [E0382]
ex5.rs:18                 express(x);
                                  ^
ex5.rs:17:21: 17:22 note: `x` moved here because it has type `Reaction<'_>`, which is non-copyable
ex5.rs:17             express(x);
                              ^
error: aborting due to previous error
{% endhighlight %}

This happens because Rust only allows for one name to correspond to any value we use in our program at any given time. In other words, you can't have two different names referring to the same object/value: the object gets *moved* to the last name to reference it.

If you're me, you think "but wait, we're not assigning it to a new name!" And that's incorrect, actually - passing it to a function counts, because the value gets bound to the corresponding parameter name in the function. So the value originally assigned to `x` in `main` gets moved to the name `sentiment` in `express`. 

There's a few ways we can solve this. The compiler hints at one, with the phrase "non-copyable". We can make `Reaction` structuress copyable easily with compiler attributes like so: `#[derive(Copy, Clone)]` ([straight from the docs!](http://doc.rust-lang.org/std/marker/trait.Copy.html#how-can-i-implement-copy?)).

That's totally valid, of course. But copying something is more memory intensive. And it's a little less interesting to me than the solution which lets you explore Rust's *move semantics* (the technical term for what's going on with the name management we talked about before) a little more.

While only one name may *own* a value at any given time, we can hand out *references* to the value, keeping the original name valid. This is calles *borrowing* the value. There are a few rules the compiler enforces, though:

1. References to a value can't live longer than the value itself (their scope is limited to the value's scope). This eliminates *dangling pointers*/*use after free* bugs.
2. You can't make a mutable reference to a value originally declared as immutable.
3. There can be any number of immutable references to a value at any given time.
4. You can create immutable references to a mutable value, but the value can't be mutated until the immutable references go out of scope.
5. There can only be one mutable reference to a value at any given time. In other words, if a mutable value is borrowed mutably, the original name can't be used until the reference goes out of scope.

Rule 1 eliminates *dangling pointers* and *use after free* security vulnerabilities, and rules 3, 4, and 5 eliminate *data races*.

I solved the problem by updating `express` to take a reference to a Reaction, like this:

{% highlight rust %}
fn express(sentiment: &Reaction) {
    match *sentiment {
        Reaction::Sad(s) => println!(":( {}", s),
        Reaction::Happy(s) => println!(":) {}", s),
    }
}
{% endhighlight %}

If you've worked with C or C++, this won't look too unfamiliar. The ampersand simply means "reference to", so a `&Reaction` value is just a reference to a `Reaction`. Then we use the unary `*` operator to dereference `sentiment` and grab the actual Reaction value.

Since we changed the type signature of `express`, we need to fix its callers in `main` to make it compile. It's a pretty simple change - all we do is use the `&` operator to pass references to `x` and `y`!


{% highlight rust %}
fn main () {
    let x = Reaction::Happy("It's a great day for Rust!");
    express(&x);
    express(&x);
    let y = Reaction::Sad("This code doesn't compile yet.");
    express(&y);
}
{% endhighlight %}

This follows all the borrowing rules.

1. We're not running `express` in a separate thread, so we know that the `sentiment` reference will go out of scope before `x` or `y` do.
2. We're not passing mutable references around. The compiler doesn't allow programmers to "cheat" by mutably borrowing an immutable value.

Cool, right?
