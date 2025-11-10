---
title: Rust Benchmarking
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---


## Rust Benchmarking by cargo bench
This section demonstrates how to benchmark Rust performance using **official Rust benchmarking tools** — `cargo bench` and the **Criterion** library — to measure code execution speed, stability, and performance consistency on Arm64 hardware.

### Verify Rust and Cargo
Ensure Rust and Cargo are correctly installed.

### Create a New Rust Project
Create a simple Rust project for benchmarking:

```console
cargo new rust-benchmark
cd rust-benchmark
```
### Add Criterion Benchmarking Dependency
**Criterion** is the officially recommended benchmarking crate for Rust.
Add it to your project by editing the `Cargo.toml` file:

```toml
[dependencies]
criterion = "0.5"

[[bench]]
name = "my_benchmark"
harness = false
```
### Create the Benchmark File
Create a new benchmark file inside the `benches/` directory:

```console
mkdir benches
vi benches/my_benchmark.rs
```

Add the following content:

```rust
use criterion::{black_box, Criterion, criterion_group, criterion_main};

// Example benchmark function
fn fibonacci(n: u64) -> u64 {
    match n {
        0 => 0,
        1 => 1,
        n => fibonacci(n - 1) + fibonacci(n - 2),
    }
}

fn benchmark_fibonacci(c: &mut Criterion) {
    c.bench_function("fibonacci 20", |b| b.iter(|| fibonacci(black_box(20))));
}

criterion_group!(benches, benchmark_fibonacci);
criterion_main!(benches);
```

### Run the Benchmark
Now run the benchmark using Cargo:

```console
cargo bench
```

You should see an output similar to:
```output
Running benches/my_benchmark.rs (target/release/deps/my_benchmark-f40a307ef9cad515)
Gnuplot not found, using plotters backend
fibonacci 20            time:   [12.026 µs 12.028 µs 12.030 µs]
Found 1 outliers among 100 measurements (1.00%)
  1 (1.00%) low mild
```



