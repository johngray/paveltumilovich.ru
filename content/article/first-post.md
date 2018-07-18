---
categories:
- Programming
title: "First post and first Rust program"
date: 2018-07-18T15:25:53+03:00
draft: false
tags:
- Programming
- Rust
---

So, it took me a long time to figure out what to write in my first post.

Not very long time ago I was (and still is) excited about the **Rust** language. Back in my university days, I used to write a lot of lines of code in C++. Today I write mostly Java and Kotlin but I still like C++, especially after **C++11** standard was introduced.
But it was always way too easy to shoot yourself in a leg using C++. And because of this you always have to use a lot of tooling: static code analysis, Valgrind, a large group to test and many other things just to make sure that everything is going to be fine with your code.
Also, a lack of adequate package management solution was frustrating.

And then Rust came out. With safe memory management and Cargo package manager. Just right what I was dreaming of. Yet it's still a work in progress and Rust still sucks in some areas (notably microcontroller programming, some minor web staff) the Rust is definitely worth trying out.

So, as everyone knows - now it's the best time. Let's begin.

# Installing Rust

In this tutorial, I'm going to use Linux and it's command line. For Mac, I think everything will stay almost the same, though I may be wrong. But for Windows, it will definitely be a little bit harder. I don't have Windows machine right now but I'll try to give my 5 cents on how you can manage to install Rust.

### Linux (Mac)

We will use [rustup](https://rustup.rs/) as it's a recommended way to install Rust right now. If a package manager of your distro provides Rust package you could use it and just skip everything below :)

Open your console and execute:

```bash
curl https://sh.rustup.rs -sSf | sh
```

It will take some time to download and install all necessary components.
After that one more step is required. You have to add directory *$HOME/.cargo/bin* to your *PATH* environment variable. This step varies even between different Linux distros (and it's definitely different on MacOS). But almost everyone suggests to add it to **.bashrc** or **.bash_profile** file inside your user's home root folder. Like this:

```
export PATH=$PATH:$HOME/.cargo/bin
```
On SolusOS you can add it to */usr/share/defaults/etc/profile.d/10-path.sh*

Everything will work after you relogin. If you don't want to relogin right now just execute:

```bash
source $HOME/.cargo/env
```

### Windows

On Windows, you can also use rustup using [i686-pc-windows-gnu platform](https://static.rust-lang.org/rustup/dist/i686-pc-windows-gnu/rustup-init.exe)

But at first, I believe you have to install [MinGW](http://www.mingw.org/).

Or you can use the [Chocolatey](https://chocolatey.org) package manager. There is a [package](https://chocolatey.org/packages/rust) for Rust.

# First program

So, everything seems to be set up! Let's see what we are going to do as our first Rust program. I found the task on some famous programming tutorial site. And it is definitely harder than a "Hello World!" :)

> Two tortoises named <u>A</u> and <u>B</u> must run a race. A starts with an average speed of `720 feet per hour`. Young <u>B</u> knows she runs faster than A, and furthermore has not finished her cabbage.

> When she starts, at last, she can see that <u>A</u> has a `70 feet lead` but <u>B</u>'s speed is `850 feet per hour`. How long will it take <u>B</u> to catch <u>A</u>?

> More generally: given two speeds `v1` (<u>A</u>'s speed, integer > 0) and `v2` (<u>B</u>'s speed, integer > 0) and a lead g (integer > 0) how long will it take <u>B</u> to catch <u>A</u>?

> The result will be an array `[hour, min, sec]` which is the time needed in hours, minutes and seconds (round down to the nearest second) or a string in some languages.

> If `v1 >= v2` then `None`

## Some math

To figure things up let's do some math's first. It is obvious that to catch someone when you started from one point you have to make the same distance but faster) Let's write it down. I will use values for speed from the task definition.  

\\[ 850t = 70 + 720t \\]

From here we can derive time that will be taken for <u>B</u> to catch <u>A</u>

\\[ 850t - 720t = 70 \\]
\\[ 130t = 70 \\]
\\[ t = {70 \over 130} \\]


And to generalize the equation we can write it  down as follows:
\\[ t = {lead \over (v2 - v1)} \\]

Let's calculate it if \\( v1 = 720 \\), \\( v2 = 850 \\) and \\(lead = 70\\) :

\\[ t = 0.5384 \\]

You would ask - how to derive hours, mins and second from this. The answer is simple:  

1. To obtain time - drop off fractional part. In our case hours = 0
2. For minutes - take calculated time and subtract hours, then multiply by 60 and then drop off fractional part. In our case it would be 0.5384 - 0 * 60 = 32.307. So, it would be 32 minutes.
3. For seconds - take fractional part from minutes then multiply by 60 and then drop off fractional part. In our case it would be 0.307 * 60 = 18.42. So, it would be 18 seconds.

## An actual program

And now we have every tool in our toolbox: installed Rust, and task math model. Let's code!

To start a new Rust project we will use cargo (Rust package manager):

`$ cargo new catch --bin`

This will create a project `catch` in current directory
We’re passing `--bin` because we’re making a binary program: if we were making a library, we’d pass `--lib`. This also initializes a new git repository by default. If you don't want it to do that, pass `--vcs none`

Now you'll see that cargo has generated a directory called `catch` and two files inside: `Cargo.toml` and `src/main.rs`. `Cargo.toml` - is a project definition (manifest). You can look at it but it's not relevant right now.

Here’s what’s in src/main.rs:

```
fn main() {
    println!("Hello, world!");
}
```

So, cargo generated a classic Hello World program. Nice!
Note `println!` macro, it will output it's arguments to stdout. We will use it later. Also, take a look at the `main` function definition. The `main` function is your program entry point. Just like in C.

To compile and run this program just issue `cargo run` in the project directory.

Ok! Enough of preparation. Let's do some real thing.  
First of all, we are going to code our task in a different function. Let's call it `race`.

```
fn race(v1: f32, v2: f32, lead: f32) -> Option<Vec<i32>> {
}
```

Inside parentheses, we see a list of function arguments. `v1` and `v2` are <u>A</u> and <u>B's</u> speed (in feet per hour) and lead is <u>A's</u> lead in comparison with <u>B</u>. `f32` is standard 32-bit floating point number data type. `-> Option<Vec<i32>>` part means that return type of the function is `Option<Vec<i32>>`. Option is a monad. Type[ Option](https://doc.rust-lang.org/std/option/index.html) represents an optional value: every Option is either `Some` and contains a value, or `None`, and does not. Think of it as a container which may be empty (`None` value) or populated only with only one `Some` element. And the element has to be of `Vec<i32>` data type. [`Vec`](https://doc.rust-lang.org/std/vec/struct.Vec.html) as the documentation says - *A contiguous growable array type*. Think of it as an analog of, say, Java's `ArrayList`.

Our function is ready. First of all, we have to deal with invariants. In our task `If v1 >= v2 then None` is an invariant. In Rust code it will be like this:

```
if v1 >= v2 {
   return None
}
```

Just a simple and ordinary `if` statement. Nothing interesting here, move along :)
Then we will code out the main equation:

```
let time = lead / (v2 - v1);
```

Also nothing fancy. But note thus variables in Rust are defined using `let` keyword and generally you don't need to specify a type directly. A compiler can auto-resolve it for you. But you can always write it as (for brevity):

```
let time: f32 = lead / (v2 - v1);
```

Now the most interesting part is a time calculation. Let's start with simple case - hours:

```
let hours = time as i32;
```

What does it mean `as i32` ? `as` is a type conversion operator. Here we are converting `f32` to `i32` by dropping the fractional part. Just as we need :)

Minutes:

```
let float_minutes = (time - hours as f32) * 60f32;

let minutes = float_minutes as i32;
```

We defining two variable here. One for later use `float_minute` and actual `minutes`.
Why do we need to convert `hours` to `f32`. Because `time` is f32 and hours is `i32`. Rust does not permit mixing types in arithmetic expressions. It seems to be a bit verbose. But one who dealt with a lot of headache because of freaking implicit type conversion will understand that and take it for granted :) 
After all a word of wisdom says:
> Explicit is better than implicit

Also, note that `60` is also converted to `f32`. Because number literals are `i32` by default.

Seconds. It will be easy now:

```
let seconds = ((float_minutes - minutes as f32) * 60f32) as i32;
```

And, finally (tadam!) the result:

```
Some(vec![hours, minutes, seconds])
```

We don't need a final `return` here because in Rust a final statement is also a return value (in functions that returns a value :) ). [`vec!`](https://doc.rust-lang.org/std/macro.vec.html) is a macro that creates a Rust Vec.

Let's put it in a final code:

```
fn race(v1: f32, v2: f32, lead: f32) -> Option<Vec<i32>> {
    if v1 >= v2 {
        return None
    }

    let time: f32 = lead / (v2 - v1);

    let hours = time as i32;

    let float_minutes = (time - hours as f32) * 60f32;

    let minutes = float_minutes as i32;

    let seconds = ((float_minutes - minutes as f32) * 60f32) as i32;

    Some(vec![hours, minutes, seconds])
}
```

Then modify your main function as follows to call your new function:

```
fn main() {
    race(720f32, 850f32, 70f32);
}
```

Check it that compiles.  

But nothing is printed to the console. Let's fix that by using [`println!`](https://doc.rust-lang.org/std/macro.println.html) macro.

If we use something like this:

```
println!(race(720f32, 850f32, 70f32));
```

We will see an error - `error: expected a literal`. Remember that the output of our function is `Optinal`. Now we have to extract a value from it. We will use an `Optional's` function `unwrap_or`. It will extract a value from `Optional` or, if it's `None`, a default value from its arguments:

```
println!(race(720f32, 850f32, 70f32).unwrap_or(vec!()));
```

Doesn't compile again. That's because vector can't be printed out directly in Rust Let's use a debugging formatting in `println!` macro:

```
println!("{:?}", race(720f32, 850f32, 70f32).unwrap_or(vec!()));
```

Here `"{:?}"` part tells to format our value to debug printing format.

And the final code is:

```
fn main() {
    println!("{:?}", race(720f32, 850f32, 70f32).unwrap_or(vec!()));
}

fn race(v1: f32, v2: f32, lead: f32) -> Option<Vec<i32>> {
    if v1 >= v2 {
        return None
    }

    let time: f32 = lead / (v2 - v1);

    let hours = time as i32;

    let float_minutes = (time - hours as f32) * 60f32;

    let minutes = float_minutes as i32;

    let seconds = ((float_minutes - minutes as f32) * 60f32) as i32;

    Some(vec![hours, minutes, seconds])
}
```

Everything is done! :) Let's compile it see what it prints:

```
$ cargo run
   Compiling catch v0.1.0 (file:///...../Projects/catch)
    Finished dev [unoptimized + debuginfo] target(s) in 0.46s
     Running `target/debug/catch`
[0, 32, 18]

```

Just as expected :) And if you make v1 greater that v2 it will output just empty vector.

#Final thoughts

Let's sum it up. What we have learned from this simple program:

1. Toolchain installation
2. Some basic `cargo` commands
3. Rust function definitions
4. `Option` data type
5. `Vec` data type
6. Rust simple arithmetics
7. Printing and debug printing

I hope that you enjoyed the tutorial. In the next tutorial, I'm planning to introduce some basic functional programming concepts that Rust supports.

Good bye!

