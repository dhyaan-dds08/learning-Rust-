# Lesson 3 - Common Programming Concepts

## Lesson 3.1 - Variables and Mutability

- By default the variables in rust are immutable
- When a variable is immutable, once a value is bound to a name, you can't change that value
- create a new project called variables `cargo run variables`
- in main.rs
```rs
fn main() {
    let x = 5;
    println!("The value of x is: {x}");
    x = 6;
    println!("The value of x is: {x}");
}
```
- when we run this code it throws an error message regarding immutability
- the error message states `cannot assign twice to immutable variable 'x'`

- but mutability can be very useful, can make your code more convenient to write.
- variable can be made mutable by adding `mut`


### Constants

- like immutable variables, constants are values that are bound to a name and are not allowed to change.
- why constants then?
  - you are not allowed to use `mut` with a constant, they are defined using `const` keyword instead of the `let` keyword and the type of the value must be annotated.
  - they can be declared in any scope, including global, this makes them useful for values that many parts of the code need to know about
  - constants may be set only to a constant expression, not like the result of a value that could only be computed at runtime
  - eg. ```rs const THREE_HOURS_IN_SECONDS: u32 = 60 * 60 * 3;```
- naming convention of constants is always set to uppercase with underscores between words.


### Shadowing
- you can declare a new variable with the same name as a previous variable.
- first variable is shadowed by the second
```rs
fn main() {
    let x = 5;

    let x = x + 1;

    {
        let x = x * 2;
        println!("The value of x in the inner scope is: {x}");
    }

    println!("The value of x is: {x}");
}
```
```bash
cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.31s
     Running `target/debug/variables`
The value of x in the inner scope is: 12
The value of x is: 6
```
- shadowing is differnet from making a variable mutable. We will receive a runtime error is we tring to assign the variable without using a let keyword. By using `let` we can perfom a few transformations and then make it immutable once done.
- other difference between shadowing and `mut` is that because we are creating a new variable when we use the `let` keyword, we can change the type of the value but reuse the same name. For example,
```rs
let spaces = "   ";
let spaces = spaces.len();
```
in the same example if we use mut it will throw an error, 
```rs
 let mut spaces = "   ";
spaces = spaces.len();
```
- error being 
```bash
cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
error[E0308]: mismatched types
 --> src/main.rs:3:14
  |
2 |     let mut spaces = "   ";
  |                      ----- expected due to this value
3 |     spaces = spaces.len();
  |              ^^^^^^^^^^^^ expected `&str`, found `usize`

For more information about this error, try `rustc --explain E0308`.
error: could not compile `variables` (bin "variables") due to 1 previous
```


## Lesson 3.2 - Data types
- Rust is a statically typed language, which means that it must know the type of all variables at compile time. The compiler can usually infer what type we want to use based on the value and how we use it.
- In cases when many types are possible, such as when we converted a `String` to a numeric type using `parse`, we must add annotation,
```rs
let guess: u32 = "42".parse().expect("Not a number!");
```
- not adding type would throw into error stating tpye must be known - type annotations needed.


### Scalar types
- scalar type represents a single value.
- there are four primary scalar types :
    1. Integer
    2. Floating-point numbers
    3. Booleans
    4. characters

### 1. Integers
- is a number without a fractional component.
- `u32` was a prior example we used.
- u stands for unsigned and i stands for signed

| Length                 | Signed | Unsigned |
|------------------------|--------|----------|
| 8-bit                  | i8     | u8       |
| 16-bit                 | i16    | u16      |
| 32-bit                 | i32    | u32      |
| 64-bit                 | i64    | u64      |
| 128-bit                | i128   | u128     |
| architecture dependent | isize  | usize    |

- each variant can be either signed or unsigned and has to have an explicit size.
- Signed and unsigned refers to whether its possible for the number to be negative.
- Each signed variant can store numbers from -(2<sup>n-1</sup>) to 2<sup>n-1</sup> - 1 inclusive, where n is the number of bits.
- isize and usize depends on the architecutre of the computer your program is running on: 64 bits if you're on 64 bit architecture.
- one can write integer literals in any of the forms, as show below. Note that number literals that can be multiple numeric types allow a type suffix such as `57u8` to designate the type.
- number literals can also use `_` as a visual separator to make the number easier to read, such as `1_000` which is same as `1000`.

| Number literals        | Example|
|------------------------|--------|
| Decimal                | 98_222 | 
| Hex                    | 0xff   | 
| Octal                  | 0o77   |
| Binary                 | 0b1111_0000 | 
| Byte                   | b'A'   | 

### Integer overflow
- giving a value more than it can hold, for eg, `u8` can have 0 to 255, if you give 256, in debug it would throw an error saying *panicking*, if in release it will treat those as *"wrap around"* meaning 256 becomes 0, 257 becomes 1 and so on, not the output one would want but wont let your code crash.
- To explicitly handle the possibility of overflow, you can use these families of methods provided by the standard library for primitive numeric types:

    - Wrap in all modes with the wrapping_* methods, such as wrapping_add.
    - Return the `None` value if there is overflow with the checked_* methods.
    - Return the value and a Boolean indicating whether there was overflow with the `overflowing_*` methods.
    - Saturate at the value’s minimum or maximum values with the `saturating_*` methods.


### 2. Floating-Point Types
- two primitive floating-point numbers
- f32 and f64 : 32 bit and 64 bit
- default is f64 as modern cpu's. roughly the same speed as f32 but is capable of more precision.
- Rust supports the basic mathematical operations you’d expect for all the number types: addition, subtraction, multiplication, division, and remainder. Integer division truncates toward zero to the nearest integer. 


### 3. Boolean

- boolean has two possible outcomes true and false.

### 4. Character

Rust's `char` is the languages most primitive alphabetic type

```rs
fn main() {
    let c = 'z';
    let z: char = 'ℤ'; // with explicit type annotation
    let heart_eyed_cat = '😻';
}
```

- **we specify `char` literals with single quotes, as opposed to string literals, which use doubles qoutes.**
- `char` is 4 bytes in size and represents a Unicode sclar value which means it can represent much more than ASCII.


## Compound Types
- compound types can group multiple values into one type. Rust has two primitive compound types: tuples and arrays.

### The Tuple Type
- a tuple is a general way of grouping together a number of values with a variety of types into one compound type.
Tuples have a fixed length: once declared, they cannot grow or shrink in size.
- We create a tuple by writing comma-separated list of values inside paranthesis. Each position in the tuple has a type, and the types of the different values in the type donot have to be the same. for eg,

```rs
fn main() {
    let tup: (i32, f64, u8) = (500, 6.4, 1);
}
```
- The variable `tup` binds to the entire tuple is considered a single compound elments. To get individual values out of a tuple, we can use pattern matching to destructive a tuple value, like this:

```rs
fn main() {
    let tup = (500, 6.4, 1);

    let (x, y, z) = tup;

    println!("The value of y is: {y}");
}
```

- This program first creates a tuple and binds it to the variable `tup`. It then uses a pattern with `let` to take `tup` and turn it into three separate variables, `x`, `y`, and `z`. This is called *destructuring* because it breaks the single tuple into three parts. Finally, the program prints the value of `y`, which is `6.4`.
- we can also fetching by using a period (`.`) followed by the index of the value we want to access.
```rs
fn main() {
    let x: (i32, f64, u8) = (500, 6.4, 1);

    let five_hundred = x.0;

    let six_point_four = x.1;

    let one = x.2;
}
```



### The array Type
- Another way to have a collection of multiple values is with an array.
- unlike a tuple, every element of an array must have the same type. ***Unlike arrays in some other languages, arrays in Rust have a fixed length***.
- Arrays are more useful when you know the number of elements will not need to change. For example, if you were using the names of the month in a program, you would probably use an array rather than a vector because you know it will always contain 12 elements:
```rs
let months = ["January", "February", "March", "April", "May", "June", "July",
              "August", "September", "October", "November", "December"];
```
- You write an array’s type using square brackets with the type of each element, a semicolon, and then the number of elements in the array, like so:

```rs
let a: [i32; 5] = [1, 2, 3, 4, 5];
```
- You can also initialize an array to contain the same value for each element by specifying the initial value, followed by a semicolon, and then the length of the array in square brackets, as shown here:
```rs
let a = [3; 5];

```
- The array named a will contain 5 elements that will all be set to the value 3 initially. This is the same as writing `let a = [3, 3, 3, 3, 3]`; but in a more concise way.


- out of bounds error when trying to access `a[10]` when a has only 5 elements
- the check happens at the run time and not at the compile time, maybe it could also be on user input and hence.   



## Lesson 3.3 - Functions