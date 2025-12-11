# Learning Rust

## LESSON 1: Creating a Project with Cargo

To create a new Rust project, use the following command:
```bash
cargo new hello_cargo
```

This command initializes a new project directory called `hello_cargo` with a Git repository and generates the essential project structure.

### Project Structure

After running the command, navigate into your project folder:
```bash
cd hello_cargo
```

Inside, you'll find:
- `Cargo.toml` - The project manifest file
- `src/` directory containing `main.rs` - Your main source file

### Understanding `Cargo.toml`

The `Cargo.toml` file serves as your project's configuration file. Here's what a basic one looks like:
```toml
[package]
name = "hello_cargo"
version = "0.1.0"
edition = "2021"

[dependencies]
```

**Key sections:**
- `[package]` - Contains metadata about your project (name, version, Rust edition)
- `[dependencies]` - Lists external crates (libraries) your project depends on
- ***TOML*** stands for Tom’s Obvious, Minimal Language

### Understanding `src/main.rs`
The `main.rs` has been generated with a code "Hello, world!"
```rs
fn main() {
    println!("Hello, world!");
}
```
- Cargo expects your source files to be in the src directory, the top level is just for **licences, readme, config files, and anything else not related to the project**

### Building Rust 
```bash
cargo build
```
- This command creates an executable file in `targe/debug/hello_cargo` rather than in your current directory. 
- As the default build is a debug build, Cargo puts the binary in a directory named debug. 
- One can run the executable with this command

```bash
./target/debug/hello_cargo
```
- That should print "Hello, world!"
- Running cargo build for the first time also causes Cargo to create a new file at the top level `Cargo.lock`
- `Cargo.lock` keeps track of the exact versions of dependencies in your project. One would never need to change this file manually.


### `Cargo run`
- instead of building the project with `cargo build` and the running `./target/debug/hello_cargo` we could directly using the command `cargo run` to compile the code and then run the resultant executable
- `cargo run` is more convenient to remember
- We did not see the output indicating that Cargo was compiling 'hello_Cargo'. Cargo figured out that the files have not been changed and hence it did not rebuild it. If there were changes made then it would've rebuilt the project

### `Cargo check`
- Cargo also provides a command that quickly checks your code to make sure it compiles and this command does not create an executable.
- **Why would you not want an executable?**
  - `cargo check` is much faster than `cargo build` because it skips the step of producing an executable.

### Building for Release
```bash
cargo build --release
```

## LESSON 2: Programming a Guessing Game
```bash
cargo new guessing_game
cd guessing_game
```

```rs
use std::io;

fn main() {
    println!("Guess the number!");

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {guess}");
}
```

### `use std::io;`
-  To obtain user input and then print the result as output we need a library called **io** (input/output)
- By default, Rust has a set of items defined in the standard library that it brings into the scope of every program. This set is called the prelude
- more about *Prelude* at [Prelude - standard library domentation](https://doc.rust-lang.org/std/prelude/index.html)
- if a type you want to use isn't in the prelude then you have to bring that type to the scope explicitly with a `use` statement.

### `fn main() {`
- fn syntax declare a new function; the parentheses, `()` indicate there are no parameters; and the curly bracket, `{`, indicates the start of the body of the particular function.

### `println!`
- is a macro statemene that prints a string on the screen
- for eg.
```rs
 println!("Guess the number!");
 println!("Please input your guess.");
```

### Storing values with variables

- we will create a variable to store the user input 
 ```rs
 let mut guess = String::new();
 ```
 - `let` keyword to create the variable
 ```rs
 let apples = 5;
 ```
 - This line creates a new variable named `apples` and binds the value 5 to it.
 - In Rust, the variables are immutable by default, meaning once we give the variable a value, the value would not change.
 - to make the variables mutable we add a keyword called `mut`

```rs
let apples = 5; // immutable
let mut bananas = 5; // mutable
```

- Back to the guessing game, we now know that the `let mut guess` will introduce a mutable variable named `guess`. The equal sign `=` tells Rust that we want to bind something to the variable now. On the right of the equal sign is the value that the variable `guess` is bound to, which is the result of calling `String::new` - a function that returns a new instance of a `String`.
- `String` is a string type provided by the standard library that is a growable, UTF-8 encoded bit of text
- The `::` syntax in the `::new` line indicates that new is an associated function of the type `String`

- An *Associated function* is a function that's implemented on a type, in this case String. This `new` function creates a new, empty String. 

### Recieving User input

- `stdin` function from the io module allows us to handle user input :
```rs
 io::stdin()
        .read_line(&mut guess)
```
- if we had not imported the io module with use `std::io` at the beginning we could still use the function call as `std::io::stdin`
- Next the line ` .read_line(&mut guess)` calls the `read_lin` method on the standard input handle to get input from the user. 
- We are also passing `&mut guess` as the argument to `read_line` to tell it what string to store the user input in.
- the complete job of the `read_line` function is to take whatever the user types into standard input and append that to a string ( without overwriting its contents), so we therefore pass the string as an argument
- The `&` indicates that this argument is a *reference*, which gives you a way to let multiple parts of your code access one peice of data without needing to copy that data into memory multiple times.
- References are also immutable by default, and hence we need to write `&mut guess` to make it mutable.


### Handling potential Failure with result

- `.expect('Failed to read line')`
- as discussed earlier, `read_line` puts whatever the user endters into the string we pass to it, it also returns a `result` value.
- `Result` is an *enumeration*, often called an enum, which is a type that can be in one of the multiple possible states. We call each possible state a *variant*.

- `Result`'s variants are `Ok` and `Err`. The `Ok` varaint indicates that the operation was successful and it contains the successfully generated value. The `Err` variant means the operation failed, and it contains information about how or why the operation failed.

- Values of the `Result` type, like values of any type, have methods deinfed on them. An instance of `Result` has and `expect method` that you can call. If this instance of `Result` is an `Err` value, `expect` will cause the program to crash and display the message that you passed as an argument to `expect`.

- If you dont call `expect` the program will compile, but you will get a warning.

### Printing values with `println!` placeholders

```rs
println!("You guessed: {guess}");
```
- The curly braces `{}` is a placeholder, think of them as little crab pincers that hold a value in place

```rs
let x = 5;
let y = 10;

println!("x = {x} and y + 2 = {}", y + 2);

```
- The above code would print `x = 5 and y + 2 = 12`.

```bash
cargo run 
   Compiling guessing_game v0.1.0 (/Users/apple/Downloads/GitHub/learning-rust/guessing_game)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.15s
     Running `target/debug/guessing_game`
Guess the number!
Please input your guess.
6
You guessed: 6
```


### Generating a Secret Number
- use [`rand` crate](https://crates.io/crates/rand)

### Using a Crate to get more functionality
- crate is a collection of Rust source code files.
- the code that we have been building is a *binary crate*, The `rand` crate is a *library crate* which contains code that is intended to be used in other programs and cant be executed on its own.

- to use `rand` we need to modify *Cargo.toml* to include `rand` dependency.
- In the Cargo.toml file, everything that follows a header is part of that section that continues until another section starts. In `[dependencies]` you tell Cargo which external crates your project depends on and which versions of those crates you require. In this case, we specify the rand crate with the semantic version specifier `0.8.5`. Cargo understands [Semantic Versioning (sometimes called SemVer)](http://semver.org/), which is a standard for writing version numbers. The specifier `0.8.5` is actually shorthand for `^0.8.5`, which means any version that is at least 0.8.5 but below `0.9.0`.

- just as pub.dev for flutter we have [Crates.io](https://crates.io/) for rust

### ensuring reproducible builds with the Cargo.lock file

- Cargo has a mechanism that ensures you can rebuild the same artifact every time you or anyone else builds your code: Cargo will use only the versions of the dependencies you specified until you indicate otherwise. For example, say that next week version 0.8.6 of the rand crate comes out, and that version contains an important bug fix, but it also contains a regression that will break your code. To handle this, Rust creates the `Cargo.lock` file the first time you run cargo build, so we now have this in the guessing_game directory.

When you build a project for the first time, Cargo figures out all the versions of the dependencies that fit the criteria and then writes them to the Cargo.lock file. When you build your project in the future, Cargo will see that the Cargo.lock file exists and will use the versions specified there rather than doing all the work of figuring out versions again. This lets you have a reproducible build automatically. In other words, your project will remain at `0.8.5` until you explicitly upgrade, thanks to the Cargo.lock file. Because the Cargo.lock file is important for reproducible builds, it’s often checked into source control with the rest of the code in your project.

### Update a crate to get a newer version
```bash
cargo update
```

```bash
cargo doc --open
```
- Note: You won’t just know which traits to use and which methods and functions to call from a crate, so each crate has documentation with instructions for using it. Another neat feature of Cargo is that running the `cargo doc --open` command will build documentation provided by all your dependencies locally and open it in your browser. If you’re interested in other functionality in the rand crate, for example, run `cargo doc --open` and click rand in the sidebar on the left.


```rs
let guess: u32 = guess.trim().parse().expect("Please type a number!");
```
- **Shadowing** - We create a variable named guess. But wait, doesn’t the program already have a variable named guess? It does, but helpfully Rust allows us to shadow the previous value of guess with a new one.
- We bind this new variable to the expression `guess.trim().parse()`. The guess in the expression refers to the original guess variable that contained the input as a string. The trim method on a String instance will eliminate any whitespace at the beginning and end, which we must do before we can convert the string to a `u32`, which can only contain numerical data. The user must press enter to satisfy read_line and input their guess, which adds a newline character to the string. For example, if the user types 5 and presses enter, guess looks like this: `5\n`. The `\n` represents “newline.” (On Windows, pressing enter results in a carriage return and a newline, `\r\n`.) The trim method eliminates `\n` or `\r\n`, resulting in just 5.

The `parse` method on strings converts a string to another type. Here, we use it to convert from a string to a number. We need to tell Rust the exact number type we want by using let guess: `u32`. The colon (:) after guess tells Rust we’ll annotate the variable’s type. Rust has a few built-in number types; the u32 seen here is an unsigned, 32-bit integer. It’s a good default choice for a small positive number. You’ll learn about other number types in Chapter 3.



### Handling invalid input
```rs
let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

```
- We switch from an `expect` call to a `match `expression to move from crashing on an error to handling the error. Remember that `parse` returns a `Result` type and `Result` is an enum that has the variants `Ok` and `Err`. We’re using a match expression here, as we did with the Ordering result of the `cmp` method.
- If parse is able to successfully turn the `string` into a `number`, it will return an `Ok` value that contains the resultant number. That `Ok` value will match the first arm’s pattern, and the match expression will just return the `num` value that parse produced and put inside the `Ok` value. That number will end up right where we want it in the new guess variable we’re creating.

If parse is not able to turn the `string` into a `number`, it will return an `Err` value that contains more information about the error. The `Err` value does not match the `Ok(num)` pattern in the first match arm, but it does match the `Err(_)` pattern in the second arm. The underscore, `_`, is a **catch-all value;** in this example, we’re saying we want to match all `Err` values, no matter what information they have inside them. So the program will execute the second arm’s code, continue, which tells the program to go to the next iteration of the loop and ask for another guess. So, effectively, the program ignores all errors that parse might encounter!

