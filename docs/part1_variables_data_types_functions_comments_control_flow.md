# Variables, Data Types, Functions, Comments, Control Flow

## Variables

Variable in Cairo are declared with `let` keyword. Variable are immutable by default. The `mut`keyword can be used to make variables mutable.

```rust
    fn main() {
        let x = 5; // declaring a variable and assign value to it
        x = 6; // lead to error cause in cairo variable are immutable
        let mut y = 5; // declaring a mutable variable and assign value to it
        y = 6; // will not lead to error cause y is mutable
    }
```

Generate a project with scarb:

`scarb new project_name`

Compile the project with scarb:

`scarb cairo-run`

## Constants

Declaring a constants:

```rust
    const ONE_HOUR_IN_SECONDS: u32 = 3600;
    // To create a constant that is result of some computation use consteval_int! macro
    const ONE_DAY_IN_SECONDS: u32 = consteval_int!(60 * 60 * 24);
```

## Shadowing variable

We can shadow a variable by using the same variable’s name and repeating the use of the let keyword as follows:

```rust
    fn main() {
        let x = 5;
        let x = x + 1;
        {
            let x = x * 2;
            println!("Inner scope x value is: {}", x);
        }
        println!("Outer scope x value is: {}", x);
    }
```

## Data Types 

Cairo is statically typed language. Every value in Cairo is of a certain type, which tells Cairo what kind of data is being specified and how to work with it.

### Scalar types

#### Felt Type

Felt is a field element, keyword is `felt252`. Integer in range from $0 <= x < P$, where is $P$ is prime number equal to $2^251 + 17 * 2^192 + 1$. If result overflow or underflow multiple of $P$ is added or subtracted to bring the result back with the range.

Result of division in Cairo is always satisfy equation $x/y * y == x$.

#### Integer Types

Integers in Cairo have extra protection against overflow and underflow.

| Length | Unsigned | Signed |
|--------|----------|--------|
| 8-bit  | `u8`     | `i8`   |
| 16-bit  | `u16`   | `i16`  |
| 32-bit  | `u32`   | `i32`  |
| 64-bit  | `u64`   | `i64`  |
| 128-bit  | `u128` | `i128` |
| 256-bit  | `u256` | -      |
| 32-bit  | `usize` | -      |

`u256` is a struct with 2 fields: `u256 {low: u128, high: u128}`

Signed variants can store numbers from $-(2^{n-1}) to 2^{n-1}-1$ inclusive.

Literals in Cairo:

| Numeric literals | Example |
|--------|--------|
| Decimal | `12345` |
| Hex     | `0xff` |
| Octal   | `0o1234` |
| Binary  | `0b0101` |

#### Numeric operations

Examples of basic mathematical operators, expected in integer types:

```rust
    fn main() {
        // addition
        let sum = 5_u128 + 10_u128;

        // subtraction
        let difference = 95_u128 - 4_u128;

        // multiplication
        let product = 4_u128 * 30_u128;

        // division
        let quotient = 56_u128 / 32_u128; //result is 1
        let quotient = 64_u128 / 32_u128; //result is 2

        // remainder
        let remainder = 43_u128 % 5_u128; // result is 3
    }
```

#### The Boolean Type

Boolean types in Cairo has two possible values: `true` and `false`. Booleans are one `felt252` in size. The type is specified by the `bool` keyword.

```rust
fn main() {
    let t = true;

    let f: bool = false; // with explicit type annotation
}
```

#### String Types

Cairo doesn't have a native type for strings but provides two ways to handle them: short strings using simple quotes and ByteArray using double quotes.

##### Short Strings

Here are some examples of declaring short strings in Cairo:

```rust
    let my_first_char = 'C';
    let my_first_char_in_hex = 0x43;

    let my_first_string = 'Hello world';
    let my_first_string_in_hex = 0x48656C6C6F20776F726C64;
```

##### Byte Array Strings

```rust
    let long_string: ByteArray = "this is a string which has more than 31 characters";
```

### Type casting

In Cairo, you can convert scalar types from one type to another by using the try_into and into methods provided by the TryInto and Into traits from the core library.

```rust
fn main() {
    let my_felt252 = 10;
    // Since a felt252 might not fit in a u8, we need to unwrap the Option<T> type
    let my_u8: u8 = my_felt252.try_into().unwrap();
    let my_u16: u16 = my_u8.into();
    let my_u32: u32 = my_u16.into();
    let my_u64: u64 = my_u32.into();
    let my_u128: u128 = my_u64.into();
    // As a felt252 is smaller than a u256, we can use the into() method
    let my_u256: u256 = my_felt252.into();
    let my_usize: usize = my_felt252.try_into().unwrap();
    let my_other_felt252: felt252 = my_u8.into();
    let my_third_felt252: felt252 = my_u16.into();
}
```

### The Tuple Type

A *tuple* is a general way of grouping together a number of values with a variety of types into one compound type. Tuples have a fixed length: once declared, they cannot grow or shrink in size.

```rust
fn main() {
    let tup = (500, 6, true);

    let (x, y, z) = tup;

    if y == 6 {
        println!("y is 6!");
    }
}
```

### The Unit Type ()

A unit type is a type which has only one value (). It is represented by a tuple with no elements. Its size is always zero, and it is guaranteed to not exist in the compiled code. n Cairo, everything is an expression, and an expression that returns nothing actually returns () implicitly.

## Functions

Cairo using *snake case* for function and variable names. Functions in Cairo is defined by a `fn` keyword followed by a function name and a set of parentheses.

```rust
fn another_function() {
    println!("another_function.");
}
```

### Parameters

Function with multiple parameters: 

```rust
fn print_labeled_measurement(value: u128, unit_label: ByteArray) {
    println!("The measurement is: {value}{unit_label}");
}
```

#### Named Parameters

In Cairo, named parameters allow you to specify the names of arguments when you call a function. Example:

```rust
fn foo(x: u8, y: u8) {}

fn main() {
    let first_arg = 3;
    let second_arg = 4;
    foo(x: first_arg, y: second_arg);
    let x = 1;
    let y = 2;
    foo(:x, :y)
}
```

### Statements and Expressions

- **Statements** are instructions that perform some action and do not return a value.
- **Expressions** evaluate to a resultant value. Let’s look at some examples.

### Functions with return values

TODO

## Comments

Comments in Cairo start with two slashes: 

```rust
// So we’re doing something complicated here, long enough that we need
// multiple lines of comments to do it! Whew! Hopefully, this comment will
// explain what’s going on.
```

## Control Flow

TODO