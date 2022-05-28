### Chapter 2: Programming a Guessing Game

```rust
use std::io;

fn main() {
    println!("Guess the number!");

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {}", guess);
}
```

To obtain user input and then print the result as output, we need to bring the `io` input/output library into scope. The `io` library comes from the standard library, known as `std`:

```rust
use std::io
```

`println!` is a macro that brings a string to the screen:

```
println!("Guess the number!");
```

Storing Values with Variables

```rust
let mut guess == String::new();
```

In Rust, variables are immutable by default. To make a vairable mutable, we add `mut` before the variable name.

Receiving User Input

```rust
io::stdin()
    .read_line(&mut guess)
```

If we hadn't imported the `io` library with use `std::io` at the beginning of the program, we could still use the function by writing this function call as `std::io::stdin`. The `stdin` function returns an instance of `std::io::Stdin`, which is a type that represents a handle to the standard input for your terminal. 

Next, the line `.read_line(&mut guess)` calls the `read_line` method on the standard input handle to get input from the user. We're also passing `&mut guess` as the argument to `read_line` to tell it what string to store the user input in. 

The `&` indicates that this argument is a _reference_, which gives you a way to let multiple parts of your code access one piece of data without needing to copy that data into memory multiple times. References are immutable by default. Hence, you need to write `&mut guess` rather than `&guess` to make it mutable. 

Handling Potential Failure with the `Result` type

```rust
    .expect("Failed to read line");
```

As mentioned earlier, `read_line` puts whatever the user enters into the string we pass to it, but it also returns a value—in this case, an `io::Result`. Rust has a number of types named `Result` in its standard library: a generic `Result` as well as specific versions for submodules, such `io::Result`. The `Result` types are enumerations, often referred to as _enums_, which can have a fixed set of possibilities known as _variants_. Enums are often used with `match`, a conditional that makes it convenient to execute different code based on which variant an enum value is when the conditional is evaluated. 

`Result`'s variants are `Ok` and `Err`. The `Ok` variant indicated the operation was successful, and inside `Ok` is the successfully generated value. The `Err` variant means the operation failed, and `Err` contains information about how or why the operation failed. 

Values of the `Result` type, like values of any type, have methods defined on them. An instance of `io::Result`, has an `expect` method that you can call. If the `read_line` method returns an `Err`, it would likely be the result of an error coming from the underlying operating system. If this instance of `io::Result` is an `Ok` value, `expect` will take the return value that `Ok` is holding and return just that value to you so you can use it. In this case, that value is the number of bytes in the user's input.

If you don't call `expect`, the program will compile, but you'll get a warning.

```rust
println!("You guessed: {}", guess);
```

The `{}` set of curly brackets is a placeholder. 

Using a Crate to Get More Functionality

Before we can write code that uses `rand`, we need to modify the `Cargo.toml` file to include the `rand` crate as a dependency. 

Ensuring Reproducible Builds with the `Cargo.lock` file

When you build a project for the first time, Cargo figures out all the versions of the dependencies that fit the criteria and then writes them to the `Cargo.lock` file. 

Updating a Crate to Get a New Version

When you _do_ want to update a crate, Cargo provides the command `update`, which will ignore the _Cargo.lock_ file and figure out all the latest versions that fit your specifications in Cargo.toml. Cargo will then write those versions to the Cargo.lock file. Otherwise, by default, Cargo will only look for versions greater than `0.8.3` and less than `0.9.0`. 

Generating a Random Number

```rust
use std::io;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1..101);

    println!("The secret number is: {}", secret_number);

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {}", guess);
}
```

First, we add the line use `rand::Rng`. The `Rng` trait defines methods that random number generators implement, and this trait must be in scope for us to use those methods.

In the first line, we call the `rand::thread_rng` function that gives us the particular random number generator that we're going to use: the one that is local to the current thread of execution and seeded by the operating system. Then we call the `gen_range` method on the random number generator. This method is defined by the `Rng` trait that we brought into scope with the use `rand::Rng` statement. The `gen_range` method takes a range expression as an argument and generates a random number in the range. 

Note: Another neat future of Cargo is that running `cargo doc --open` command will build documentation provided by all your dependencies locally and open it in your browser. 

Comparing the Guess to the Secret Number

```rust
use rand::Rng;
use std::cmp::Ordering;
use std::io;

fn main() {
    // --snip--

    println!("You guessed: {}", guess);

    match guess.cmp(&secret_number) {
        Ordering::Less => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal => println!("You win!"),
    }
}
```

First we add another `use` statement, bringing a type called `std::cmp::Ordering` into scope from the standard library. The `Ordering` type is another enum and has the variants `Less`, `Greater` and `Equal`.

Then we add five new lines at the bottom that use the `Ordering` type. The `cmp` method compares two values and can be called on anything that can be compared. It takes a reference to whatever you want to compare with: here it's comparing the `guess` to the `secret_number`. Then it returns a variant of the `Ordering` enum we brought into scope with the `use` statement. We use a `match` expression to decide what to do next based on which variant of `Ordering` was returned from the call to `cmp` with the values in `guess` and `secret_number`.

We want to convert the `String` the program reads as input into a real number type so we can compare it numerically to the secret number. 

```rust
let guess: u32 = guess.trim().parse().expect("Please type a number!");
```

We create a variable named `guess`. Rust allows us to _shadow_ the previous value of `guess` with a new one. Shadowing lets us reuse the `guess` variable name rather than forcing use to create two unique variables, such as `guess_str` and `guess` for example. Know that this feature is often used when you want to convert a value from one type to another type. 

We bind this new variable to the expression `guess.trim().parse()`. The `guess` in the expression refers to the original `guess` variable that contained the input as a string. The `trim` method on a `String` instance will eliminate any whitespace at the beginning and end, which we must do to be able to compare the string to the `u32`, which can only contain numerical data. The user must press enter to satisfy `read_line` and input their guess, which adds a newline character to the string. For example, if the user types 5 and presses enter, `guess` looks like this: `5\n`. 

The `parse` method on strings parses a string into some kind of number. Because this method can parse a variety of number types, we need to tell Rust the exact number type we want by using `let guess: u32`. The colon after `guess` tells Rust we'll annotate the variable's type. Rust has a few built-in types; the `u32` seen here is an unsigned, 32-bit integer. 

The `parse` method will only work on characters that can logically be converted into numbers and so can easily cause errors. Because it might fail, the `parse` method returns a `Result` type, much as the `read_line` method does. We'll treat this `Result` the same way by using the `expect` method again. If `parse` returns an `Err` `Result` variant because it couldn't create a number from the string, the `expect` call will crash the game and print the message we give it. If `parse` can successfully convert the string to a number, it will return the `Ok` variant of `Result`, and `expect` will return the number that we want from the `Ok` value. 

Allowing Multiple Guesses with Looping

The `loop` keyword creates an infinite loop. We'll add a loop to give users more chances at guessing the number:

```rust
    // --snip--

    println!("The secret number is: {}", secret_number);

    loop {
        println!("Please input your guess.");

        // --snip--

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => println!("You win!"),
        }
    }

```

Quitting After a Correct Guess

```rust
        // --snip--

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        }
    }
}
```

Adding the `break` line after `You win` make the program exit the loop when the user guesses the secret number correctly. 

Handling Invalid Input

Let's make the game ignore a non-number so the user can continue guessing (instead of crashing)

```rust
        // --snip--

        io::stdin()
            .read_line(&mut guess)
            .expect("Failed to read line");

        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        println!("You guessed: {}", guess);

        // --snip--
```

We switch from an `expect` call to a `match` expression to move from crashing on an error to handling the error. Remember that `parse` returns a `Result` type and `Result` is an enum that has the variants Ok and Err. 

If parse is able to successfully turn the string into a number, it will return an Ok value that contains the resulting number. That `Ok` value will match the first arm's pattern, and the `match` expression will just return the `num` value that `parse` produced and put inside the Ok value. That number will end up right where we want it in the new `guess` variable we're creating. 

If `parse` is not able to turn the string into a number, it will return an `Err` value that contains information about the error. The `Err` value does not match the `Ok(num)` patten in the first `match` arm, but it does match `Err(_)` pattern in the second arm. The underscore `_` is a catchall value; we're saying we want to match all `Err` values, no matter what information they have inside them. 