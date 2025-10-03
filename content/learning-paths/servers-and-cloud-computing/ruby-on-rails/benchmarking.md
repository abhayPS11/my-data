---
title: Ruby on Rails Benchmarking
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---


## Ruby on Rails Benchmarking with Custom Workloads
This section benchmarked Ruby on Rails using custom workloads (DB inserts, queries, and computations) on GCP SUSE VMs, analyzing execution time and performance metrics.  


### Go into your Rails app folder (for example, db_test_app):
You need to navigate into the folder of your Rails application. This is where Rails expects your application code, models, and database configurations to be located. All commands related to your app should be run from this folder.

```console
cd ~/db_test_app
````

### Create the benchmark file inside the app:
We create a new Ruby file named `benchmark.rb` where we will write code to measure performance.

```console
nano benchmark.rb
```

Benchmark code for measuring Rails app performance:

```ruby
require 'benchmark'

n = 1000

Benchmark.bm do |x|
  x.report("DB Insert:") do
    n.times do
      Task.create(title: "Benchmark Task", due_date: Date.today)
    end
  end

  x.report("DB Query:") do
    n.times do
      Task.where(title: "Benchmark Task").to_a
    end
  end

  x.report("Computation:") do
    n.times do
      (1..10_000).reduce(:+)
    end
  end
end
```
- `require 'benchmark'` → Loads Ruby’s built-in benchmarking library.
- `n = 1000` → Sets the number of iterations for each task. You can increase or decrease this number to simulate heavier or lighter workloads.
- `Benchmark.bm` → Starts a block to measure performance of different tasks.
- **DB Insert** → Creates 1,000 new `Task` records in the database. Measures how long it takes to insert data.
- **DB Query** → Retrieves the 1,000 `Task` records from the database. Measures how long it takes to query data.
- **Computation** → Performs a simple calculation 1,000 times. Measures pure CPU performance (without database interactions).

This code gives you a basic understanding of how your Rails app performs under different types of workloads.

### Run the benchmark inside Rails:

```console
rails runner benchmark.rb
```
- `rails runner` → Allows you to run Ruby code in the context of your Rails application.  

It loads your models, database configuration, and environment, so that your benchmark can interact with the database and application objects.

You should see an output similar to:

```output
                  user     system      total        real
DB Insert:    2.271645   0.050236   2.321881 (  2.721631)
DB Query:     3.379849   0.009345   3.389194 (  3.389613)
Computation:  0.410907   0.000000   0.410907 (  0.410919)
```

### Benchmark Metrics Explained

- **user** → Time spent executing your Ruby code (**CPU time in user mode**).  
- **system** → Time spent in **system calls** (I/O, database, kernel interactions).  
- **total** → `user + system` (sum of CPU processing time).  
- **real** → The **wall-clock time** (actual elapsed time, includes waiting for DB, I/O, etc).  

### Benchmark summary on x86_64
To compare the benchmark results, the following results were collected by running the same benchmark on a `x86 - c4-standard-4` (4 vCPUs, 15 GB Memory) x86_64 VM in GCP, running SUSE:



### Benchmark summary on Arm64
Results from the earlier run on the `c4a-standard-4` (4 vCPU, 16 GB memory) Arm64 VM in GCP (SUSE):

### Benchmark Results

| Task         | user     | system   | total    | real     |
|--------------|----------|----------|----------|----------|
| DB Insert    | 2.271645 | 0.050236 | 2.321881 | 2.721631 |
| DB Query     | 3.379849 | 0.009345 | 3.389194 | 3.389613 |
| Computation  | 0.410907 | 0.000000 | 0.410907 | 0.410919 |


### Ruby/Rails performance benchmarking comparison on Arm64 and x86_64

When you compare the benchmarking results, you will notice that on the Google Axion C4A Arm-based instances:
