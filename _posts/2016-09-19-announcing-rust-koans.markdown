---
layout: post
title: "Announcing Rust Koans"
---

Like I've mentioned before, both on this blog and to people I know off the internet, I'm working to become an expert in two languages; Python and Rust. I'm going to write a little about how the languages differ, and the surprising similarities they share.

This post will assume familiarity with Python and at least a little bit of Rust knowledge; knowing what `match` is, and the differences between references and values as function arguments are about all you'll need. I'd like to write some form of *Rust for Pythonistas* resource in the future.

Recently I did an extended exercise in Python called [Python Koans](https://github.com/gregmalcolm/python_koans). Python Koans is a set of unit tests that you, the programmer, need to fix to run properly. It doubles as a way to learn more about testing and test-driven design. I wrote an article about it [here](/2016/09/18/python-koans/). 

Since my long-term plan is to become an expert in both Python and Rust, I looked for a similar exercise in Rust. There's a couple in progress ones that look cool. I like how the [rust-koans](https://github.com/mankyKitty/rust-koans) repo tests Rust's implicit `return`, but it hasn't had a commit since 2014; i.e. it's pre 1.0 and wouldn't compile on modern Rust. 

I like this [rust-koans](https://github.com/crazymykl/rust-koans) repo a lot. It's receiving frequent commits, so it's definitely more up to date, and it has more content and an interface more similar to the Python and Ruby Koans, but I really liked Python Koans' `__` variable.

To elucidate, the Python version uses a placeholder object called `__` where you're expected to fill in some code to make the test pass. This is a lot simpler to do in Python, because it's a dynamically typed language. 

Rust's macros do "bend the rules", so to speak (I'll expand on this in a later post, I promise!), but they don't remove compile-time type checking .

What does this mean? Let's use an exapmle. The number of arguments a function has in Rust must be known at compile time. This means that you can't implement `printf`/`str.format` type formatting in Rust... at least as a function. Macros have a special set of rules, which allow them to, for instance, take a variable number of arguments. This is why [`panic!`](https://doc.rust-lang.org/std/macro.panic!.html) (optionally displays an error message), [`printf!`](https://doc.rust-lang.org/std/macro.println!.html) (insert a variable number of arguments into the string), and [`assert_eq!`](https://doc.rust-lang.org/std/macro.assert_eq!.html) (optionally display an error message on failure) are implemented as macros.

Let's get back to the special Python Koans `__` object. The definition of `__`, verbatim, is: 

    __ = '-=> FILL ME IN! <=-'

Converting this directly to Rust gives you:

    pub const __: &'static str = "-=> FILL ME IN! <=-";

This line of code is completely acceptable in Rust. It'll compile, and you can use it anywhere your program expects a string.

Let's have a brief diversion to explain what this means to people not familiar with Rust. 

`pub` indicates that we're declaring something which will be in public scope, meaning anyone who imports our module wheree we've defined it can use it. 

`const` just indicates that we're defining a constant. The value doesn't change. Ever.

The `__` that follows is the name. That's where the Python variable definition starts.

The `:` indicates the beginning of a type signature, which lasts up to the `=` sign (or next comma/the opening bracket in a function signature, depending on whether you're giving the type of a parameter or the return value).

`&'static str` is pretty busy. There are 3 parts to it: `&`, `'static`, and `str`. `&` means "reference to", like the `&` operator in C and C++ or the `ByRef` keyword in Visual Basic. `str` (technically `&str`) is one of the Rust string types. `&str` strings are just sequences of UTF-8 bytes of fixed length (REVISE). The `'static` in the type signature refers to the *lifetime* of the variable; how long it'll stick around in memory. Rust has no garbage collection (but you never `free` any memory -- a pretty cool point I'll write more about later); technically, everything gets cleaned up after it drops out of scope; this sounds simple, but in practice there's a lot of rules around it. They're very important, but not to what we're discussing right here. A `'static` value stays around forever. In other words, its lifetime is "static"; it doesn't depend on anything else (aside the program running, of course). In other words, it's always around and accessible. Exactly what you want from a global constant. 

Now this works fine, but the problem with it is that any function which tests equality, like the `assert_eq!` macro, or anything which calls it (hint hint) requires that both things that are being compared are the same type. Not only that, but they both have to be a type which implements the [`PartialEq` trait](https://doc.rust-lang.org/core/cmp/trait.PartialEq.html). `PartialEq`'s different from `Eq` because it doesn't require that something has to be equal to itself. It's used for things like floating point numbers, where `NaN` isn't equal to itself; we don't know what `NaN` is.

I really like the way that `__` works. `__` is great because it allows you to run the code with an assert error reminding the user they've got to fill something in, rather than a potentially-cryptic syntax or compilation (technically CPython's compiled but we're not going into the details of that here) error. 

Now, how can we replicate this in Rust? The idea from here would be to use this behavior to wrap `assert_eq!`. Basically, we want to have a function that takes two parameters, which can potentially be the same type, but also might possibly be of different types. 

This part isn't too hard in Rust. Rust has generic types, so our function can take two types with generic types `T` and `U`. It's important to note that there's nothing preventing `T` and `U` from being different names for the same type. We handle these two cases like this:
* **`T` and `U` are the same type.** We're cool. Compare them with `assert_eq!` like any other unit test.
* **`T` and `U` are different types.** Either the student hasn't filled in `__` yet, or they got the type wrong when they did. 
  * **The student hasn't filled in the test yet.** We can handle this by making Rust `__` a type that's unlikely to be used. I chose `Option<&()>`. This type has some use (return whether or not an I/O operation failed), but because unit tests typically don't do much I/O[^1], it's pointless enough in our use case to serve our purpose. We could also make a custom type whose sole purpose is to signify the "fill me in" error. I'll go back and do this soon, probably tomorrow.
  * **The student made a mistake with the fill-in-the-blank-value's type.** We just `panic!` with another error message gently explaining the mistake. We could also ignore this case, because the compiler will display a type error. In all honesty, since this is a late night blog post I just sketched out, I haven't decided on which option I will choose. It'll likely be the latter.

Now that we've written out all that, we have the knowledge to begin writing our `assert_eq!` wrapper. The signature will look like this:

    fn test<T, U>(actual: T, expected: U)


The first argument will always be the one the student has to fill in, which is why it's `actual`. `expected` is a literal of some sort the teacher entered.

The angle bracket syntax might be familiar to you if you're a C++ or Java programmer. If it's not, it just means that `T` and `U` are generic types and you can pass anything in. Similar to `'a` in OCaml or `a` in Haskell.

So our next question is this: how can we compare two types for equality? From Googling things like "rustlang dynamic typing", I came across the [module called `any`](https://doc.rust-lang.org/std/any/), which has a trait called `Any` that lets you check a value's type at execution time, rather than runtime. It does this through a method called `is`. You can also use a method called `downcast_ref` to attempt type conversion. Since this can fail, with only one possible cause for failure, the converted value gets returned inside of an `Option`[^2]

Here's what a header for a function that implements this behavior would look like:

    fn test<T: Any, U: Any>(actual: T, expected: U)

`T: Any` means that `T` is a generic that implements the `Any` trait. Still generic, but a little less so.

We could also write the new signature like this:

    fn test<T, U>(actual: T, expected: U) where
        T: Any,
        U: Any

I like the first version better. 

Since we're already discussing trait bounds, I'm going to add in the trait bounds that we need to call `assert_eq!` here. They're `PartialEq` so we can compare the two values, and `Debug` so we can print out the values we got an error message. Now we have:

    fn test<T: Any, U: Any + Debug + PartialEq>(actual: T, expected: U)

To turn `actual` into an `Any` for type comparison purposes, we can just copy directly from the Rust docs:

    let actual_any = &actual as &Any;

`as` is the conversion operator, which only works if the conversion-related traits are defined for the specific value. The only difference from the example in the documents is we're using a reference to actual for the conversion because we're calling by value. 

Now we check if the value is the `Option<&()>` we mentioned before:

    if actual_any::is<Option<&()>() {
        panic!("-=> FILL ME IN! <=-");
    }

Rust uses `::` to access methods (`.` is used for struct values), and the angle brackets enclosing the type name tell the compiler what type we want to check. Essentially, it's passing the type to the `is` method.

There's our first case done. The second is.

    match actual_any.downcast_ref::<U>() {
        Some(x) => assert_eq!(*x, expected),
        None => ...
    }

We're calling `downcast_ref` to check if `actual` was the same type as `expected`. If it's successful, we dereference the returned value (`downcast_ref` returns an `Option<&T>`) and call `assert_eq!`. 

If not, we have to show a type error. Since it's getting late, that's for Part 2. Stay tuned!

[^1]: I can't actually think of any reason you'd want a reference to an empty tuple since there aren't many useful things you can do with it, but the general point still stands.
[^2]: As an aside, Rust [has traits that can be implemented for type conversions](https://doc.rust-lang.org/std/convert/).
