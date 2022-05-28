## Chapter 3: Common Programming Concepts

### 3.1 Variables and Mutability

**Constants**

Like immutable variables, _constants_ are values that are bound to a name and are not allowed to change, but there are a few differences between constants and variables.

You can declare constants using the `const` keyword instead of the `let` keyword, and the type of the value _must_ be annotated.

**Shadowing**

The first variable is _shadowed_ by the second, which means that the second variable's value is what the program sees when the vairable is used. 

```rust
fn main() {
    let x = 5;

    let x = x + 1;

    {
        let x = x * 2;
        println!("The value of x in the inner scope is: {}", x);
    }

    println!("The value of x is: {}", x);
}
```

This program first binds `x` to a value of `5`. Then it shadows `x` by repeating `let x =`, taking the original value and adding `1` so the value of `x` is then 6. Then, within an inner scope, the third `let` statement also shadows over `x`, multiplying the previous value by 2 to give `x` a value of 12. When that scope is over, the inner shadowing ends and `x` returns to being 6.

Shadowing is different from making a variable as `mut`, because you'll get a compile-time error if we accidentaly try to reassign to this variable without using the `let` keyword. By using `let`, we can perform a few transformations on a value but have the variable be immutable after those transformations have been completed. 

The other difference between `mut` and shadowing is that because we're effectively creating a new variable when we use the `let` keyword again, we can change the type of the value but reuse the same name. 

### 3.2 Data Types

We'll look at two data type subsets: scalar and compound.

Keep in mind that Rust is a _statically typed_ language, which means that it must know the types of all variables at compile time. The compiler can usually infer what type we want to use based on the value and how we use it. 

A _scalar_ type represents a single value. Rust has four primary scalar types: integers, floating-point, numbers, Booleans and characters. 

To explicitly handle the possibility of overflow, you can use these families of methods provided by the standard library for primite numeric types:

- Wrap in all modes with the `wrapping_*` methods, such as `wrapping_add`
- Return the `None` value if there is overflow with the `checked_*` methods
- Return the value and a boolean indicating whether there was overflow with the `overflowing_*` methods
- Saturate at the value's minimum or maximum values with `saturing_*` methods

**Floating-Point Types**

Rust also has two primitive types for _floating-point numbers_, which are numbers with decimal points. `f32` and `f64`

```rust
fn main() {
    let x = 2.0; // f64

    let y: f32 = 3.0; // f32
}
```

**Numeric Operations**

**The Boolean Type**

`bool`

**The Character Type**

```rust
fn main() {
    let c = 'z';
    let z = 'ℤ';
    let heart_eyed_cat = '😻';
}
```

**Compound Types**

_Compound types_ can group multiple values into one type. Tuples and arrays.

**The Tuple Type**

```rust
fn main() {
    let tup: (i32, f64, u8) = (500, 6.4, 1);
}
```

```rust
fn main() {
    let tup = (500, 6.4, 1);

    let (x, y, z) = tup;

    println!("The value of y is: {}", y);
}
```

```rust
fn main() {
    let x: (i32, f64, u8) = (500, 6.4, 1);

    let five_hundred = x.0;

    let six_point_four = x.1;

    let one = x.2;
}
```

**The Array Type**

Unlike a tuple, every element of an array must have the same type. 

```rust
fn main() {
    let a = [1, 2, 3, 4, 5];
}
```

Arrays are useful when you want your data allocated on the stack rather than the heap or when you want to ensure you always have a fixed number of elements. An array isn't as flexible as the vector type, though. A vector is a similar collection type provided by the standard libary that _is_ allowed to grow or shrink in size. 

```rust
fn main() {
let months = ["January", "February", "March", "April", "May", "June", "July",
              "August", "September", "October", "November", "December"];
}
```

```rust
let a: [i32; 5] = [1, 2, 3, 4, 5];
```

You can also initialize an array to contain the same value for each element by specifying the initial value, followed by a semicolon, and then the length of the array in square brackets.

```rust
let a = [3; 5];
```

**Accessing Array Elements**

An array is a single chunk of memory of a known, fixed size that can be allocated on the stack. 

```rust
fn main() {
    let a = [1, 2, 3, 4, 5];

    let first = a[0];
    let second = a[1];
}
```

**Invalid Array Element Access**

```rust
use std::io;

fn main() {
    let a = [1, 2, 3, 4, 5];

    println!("Please enter an array index.");

    let mut index = String::new();

    io::stdin()
        .read_line(&mut index)
        .expect("Failed to read line");

    let index: usize = index
        .trim()
        .parse()
        .expect("Index entered was not a number");

    let element = a[index];

    println!(
        "The value of the element at index {} is: {}",
        index, element
    );
}
```

The program results in a _runtime_ error at the point of using an invalid value in the indexing operation. When you attempt to access an element using indexing, Rust will check that the index you've specified is less than the array length. 

### 3.3 Functions

**Parameters**

We can define functions to have _parameters_, which are special variables that are part of a function's signature.

```rust
fn another_function(x: i32) {
    println!("The value of x is: {}", x);
}
```

A new block created with curly brackets is an expression:

```rust
fn main() {
    let y = {
        let x = 3;
        x + 1
    };

    println!("The value of y is: {}", y);
}
```

A new scope block created with curly brackets is an expression, in this case, evaluates to 4. That value gets bound to `y` as part of the `let` statement. Note that the `x+1` line doesn't have a semicolon at the end, unlike most of the lines you've seen so far. Expressions do not include ending semicolons. If you add a semicolon to the end of an expression, you turn it into a statement, and it will then not return a value. 

**Functions with Return Values**

Functions can return values to the code that calls them. We don't name return values, but we must declare their type after an arrow (`->`). In Rust, the return value of the function is synonymous with the value of the final expression in the block of the body of a function. You can return early from a function by using the `return` keyword and specifying the value. 

```rust
fn five() -> i32 {
    5
}
```

### 3.4 Comments

### 3.5 Control Flow

**if Expressions**

```rust
fn main() {
    let number = 3;

    if number < 5 {
        println!("condition was true");
    } else {
        println!("condition was false");
    }
}
```

**Using if in a let Statement**

Because `if` is an expression, we can use it on the right side of a `let` statement to assign the outcome of a variable.

```rust
fn main() {
    let condition = true;
    let number = if condition { 5 } else { 6 };

    println!("The value of number is: {}", number);
}
```

**Repetition with Loops**

loop, while and for

```rust
fn main() {
    loop {
        println!("again!");
    }
}
```

We also used `continue` in the guessing game, which in a loop tells the program to skip over any remaining code in this iteration of the loop and go to the next iteration.

If you have loops within loops, `break` and `continue` apply to the innermost loop at that point. You can optionally specify a loop label on a loop that we can then use with break or continue to specify that those keywords apply to the labeled loop instead of the innermost loop. Here’s an example with two nested loops:

```rust
fn main() {
    let mut count = 0;
    'counting_up: loop {
        println!("count = {}", count);
        let mut remaining = 10;

        loop {
            println!("remaining = {}", remaining);
            if remaining == 9 {
                break;
            }
            if count == 2 {
                break 'counting_up;
            }
            remaining -= 1;
        }

        count += 1;
    }
    println!("End count = {}", count);
}
```

**Returning values from Loops**

One of the uses of a `loop` is to retry an operation you know might fail. You might also need to pass the result of that operation out of the loop to the rest of your code. To do this, you can add the value you want returned after the `break` expression you use to stop the loop; that value will be returned out of the loop so you can use it, as shown:

```rust
fn main() {
    let mut counter = 0;

    let result = loop {
        counter += 1;

        if counter == 10 {
            break counter * 2;
        }
    };

    println!("The result is {}", result);
}
```

**Conditional Loops with while**

```rust
fn main() {
    let mut number = 3;

    while number != 0 {
        println!("{}!", number);

        number -= 1;
    }

    println!("LIFTOFF!!!");
}
```

**Looping through a Collection with for**

```rust
fn main() {
    let a = [10, 20, 30, 40, 50];

    for element in a {
        println!("the value is: {}", element);
    }
}
```

When in situations where you want to run some code a certain number of times, most Rusters would use a `for` loop. The way to do that would be to use `Range`, provided by the standard library, which generates all numbers in sequence starting from one number and ending before another number.

```rust
fn main() {
    for number in (1..4).rev() {
        println!("{}!", number);
    }
    println!("LIFTOFF!!!");
}
```

Using `rev` to reverse the range

