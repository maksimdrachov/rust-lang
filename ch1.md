### Chapter 1: Getting Started

To compile and run:

```
rustc main.rs
./main
```

Create new cargo project:

```
cargo new hello_cargo
```

Building a cargo project:

```
cargo build
```

This command creates an executable file in `target/debug/hello_cargo` rather than in your current directory.

Running a cargo project:

```
cargo run
```

Check your code: (faster than `cargo build`)

```
cargo check
```

Building for release:

```
cargo build --release
```

This will create an executable in `target/release` instead of `target/debug`. The optimizations make your Rust code run faster, but turning them on lengthens the time it takes for your program to compile. 