# rust-pad [![Build Status](https://travis-ci.org/ogham/rust-pad.svg?branch=master)](https://travis-ci.org/ogham/rust-pad)

This is a library for padding strings at runtime.

The routines in `std::fmt` only work with formatting strings provided at
compile-time, making them unsuitable for padding to a custom or
user-defined value. Rather than re-implement all of `std::fmt`, padding is
probably the most common use case, which is written in a more
runtime-friendly fashion here.

It provides four helper functions for the most common use cases, and one
main function to cover the other cases.

### [View the Rustdoc](http://ogham.rustdocs.org/pad/)

# Installation

This crate, like all external crates, works very well with
[Cargo](http://crates.io/). Add the following to your `Cargo.toml`:

```toml
[dependencies.pad]
git = "https://github.com/ogham/rust-pad.git"
```

And the `pad` crate should be available to you.

Examples
--------

You can pad a string to have a minimum width with the `pad_to_width`
method:

```rust
use pad::PadStr;
println!("{}", "Hi there!".pad_to_width(16));
```

This will print out "Hi there!" followed by seven spaces, which is the
number of spaces necessary to bring it up to a total of sixteen characters
wide.

String length is determined with the
[width](http://doc.rust-lang.org/nightly/std/str/trait.StrExt.html#tymethod.width)
function, without assuming CJK.


Alignment
---------

By default, strings are left-aligned: any extra characters are added on
the right. To change this, pass in an `Alignment` value:

```rust
use pad::{PadStr, Alignment};
let s = "I'm over here".pad_to_width_with_alignment(20, Alignment::Right);
```

There are four of these in total:

- **Left**, which puts the text on the left and spaces on the right;
- **Right**, which puts the text on the right and spaces on the left;
- **Middle**, which centres the text evenly, putting it slightly to the left if it can't be exactly centered;
- **MiddleRight**, as above, but to the right.


Characters
----------

Another thing that's set by default is the character that's used to pad
the strings - by default, it's space, but you can change it:

```rust
use pad::PadStr;
let s = "Example".pad_to_width_with_char(10, '_');
```


Truncation
----------

Finally, you can override what happens when a value exceeds the width you
give. By default, the width parameter indicates a *minimum width*: any
string less will be padded, but any string greater will still be returned
in its entirety.

You can instead tell it to pad with a maximum value, which will truncate
the input when a string longer than the width is passed in.

```rust
use pad::PadStr;
let short = "short".with_exact_width(10);  // "short     "
let long = "this string is long".with_exact_width(10);  // "this strin"
```


A Full Example
--------------

All of the above functions delegate to the `pad` function, which you can
use in special cases. Here, in order to **right**-pad a number with
**zeroes**, pass in all the arguments:

```rust
use pad::{PadStr, Alignment};
let s = "12345".pad(10, '0', Alignment::Right, true);
```

(The `true` at the end could just as easily be `false`. It's whether to
truncate or not.)


Note on Debugging
-----------------

One very last point: the width function takes a `usize`, rather than a
signed number type. This means that if you try to pass in a negative size,
it'll wrap around to a positive size, and produce a massive string and
possibly crash your program. So if your padding calls are failing for some
reason, this is probably why.
