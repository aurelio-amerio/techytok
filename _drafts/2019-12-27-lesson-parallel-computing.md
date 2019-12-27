---
title: "Parallel computing"
date: 2019-12-27
header:
  caption: ""
  image: "/assets/images/2019/12/27b/header.jpg"
  og_image: "/assets/images/2019/12/27b/teaser.jpg"
  teaser: "/assets/images/2019/12/27b/teaser.jpg"
excerpt: "From zero to Julia Lesson 15. Parallel computing"
permalink: /lesson-parallel-computing/
toc: true
toc_label: "Table of Contents"
toc_icon: "book"

categories:
    - "Tutorial"
    - "Lessons"
tags:
    - Julia

comments: true

sidebar:
  nav: "zero-to-julia"
---

In this lesson we will deal with **parallel computing**, which is a type of computation in which many calculations or the execution of processes are carried out simultaneously on different CPU cores. We will show the differences between **multi-threading** and **multi-processing** and we will learn how those techniques are implemented in Julia.

For this lesson you will need Julia version 1.3 or above.
{: notice--warning}

# When to use parallel computing

If you need to perform many operations independent from each other (i.e. mapping a function over an array), parallel computing will give a huge improvement in speed to the computation. Even recursive functions can benefit from parallel computing (see [this](https://julialang.org/blog/2019/07/multithreading) article). 

[Monte Carlo simulations](https://en.wikipedia.org/wiki/Monte_Carlo_method) and matrix operations are other kind of computations which benefit greatly from parallel computing.

# Multi-threading and Multi-processing

Both techniques can be used to achieve parallel processing, but they have their pros and cons.

- **Threads are lightweight**, since all the threads share the same space in memory, and you can launch as many threads as you want. The CPU will run simultaneously a number of threads equal to the number of cores. Since the memory is shared you must **pay attention** to correct **synchronisation** when multiple threads  are accessing the same variable. Julia introduces **locks** to prevent this kind of racing condition. A downside of multi-threading is that **all the threads must live in the same physical machine**.
- **Multi-processing** is the right choice if we want to run computations on a cluster (a series of different physical machines). **A process has its own memory space** and thus will not have synchronisation issues, the downside is that starting a process may be slow and **each process consumes more memory than a thread**. Although multi-processing requires more memory than multi-threading, it is possible to share memory between process and if we work with a cluster, it is possible to manage arrays and matrices much bigger than what a single machine would be able to do.

In general, as a rule of thumb, we will use **threads** if we plan to run our code on a **single machine** and **processes** if the code must be able to run on a **cluster** of machines.

# Multi-threading

In order to use multi-threading we need to start Julia with a number of threads equal to the number of  you CPU cores. If you are using the Juno IDE it will automatically start Julia with the appropriate number of threads. If you are working from the REPL, you need to manually start Julia from the command line. My laptop has 4 cores and I use Windows, so I need to start Julia with 4 threads. To start Julia from the command line, navigate to the folder which contains the Julia bin and type:

```bash
set JULIA_NUM_THREADS=4
julia.exe
```

If you are using Unix, type the following code instead:

```bash
export JULIA_NUM_THREADS=4
julia
```

For this guide I recommend using the Juno IDE as it makes starting threads much easier.
{: .notice--info}

You can check the number of available threads with the following Julia code:

```julia
>>>Threads.nthreads()
4
```

Let's suppose we want to compute the map of the Bessel function [`besselj1`](https://juliamath.github.io/SpecialFunctions.jl/stable/functions_list/#SpecialFunctions.besselj1) over an array and store the results, we can do it regularly in the following way using broadcasting:

```julia
using Pkg
Pkg.add("SpecialFunctions")

using SpecialFunctions

x = range(0,100, length=10000)

results = zeros(length(x))

results .= besselj1.(x)
```

It is possible to measure the execution time of a function using the macro `@btime` from the `BenchmarkTools` package. To use it we need to first install `BenchmarkTools`.

```julia
using Pkg
Pkg.add("BenchmarkTools")
using BenchmarkTools
```

And to measure the execution time of `besselj1` we need to run the following code:

```julia
>>>@btime results .= besselj1.(x)
674.800 μs (3 allocations: 192 bytes)
```

The computation takes 674.800 μs on my machine. Although it is not the fastest solution, it is possible to rewrite this operation using a for loop:

```julia
for i in 1:length(x)
    results[i] = besselj1(x[i])
end
```

This operation takes 1.835 ms on my machine to complete. The difference in time is due to the fact that the second version has to read the values of `x` from the array at each iteration. If we were to use a slower function the two implementations would be equivalent:

```julia
y = 1:0.1:10
res = zeros(length(y))

>>>@btime res .= slow_func.(y)
593.243 ms (643 allocations: 15.39 KiB)

>>>@btime for i in 1:length(y)
        res[i] = slow_func(y[i])
    end
590.302 ms (823 allocations: 19.50 KiB)
```

Coming back to the example with `besselj1`, in that loop every iteration is independent from the next one: this hints the possibility to make the code parallel. To achieve parallelization, we import the `Threads` module and call the `@threads` macro:

```julia
using Threads
@threads for i in 1:length(x)
    results[i] = besselj1(x[i])
end
```

Timing this block of code, I find that the execution time is 396.900 μs. As you can see it is roughly 4 times faster than the equivalent code without multi-threading and is even faster than the first option. 

## Composable multi-threading

Starting from Julia 1.3, it is possible to start an operation on a thread with the `Threads.@spawn` macro. At the time of writing this feature is considered experimental and as such will probably see some changes in the interface. With the `@spawn` macro it is possible to start several tasks simultaneously and then fetch the results. For example it is possible to implement the classic highly-inefficient tree recursive implementation of the Fibonacci sequence using multi-threading

```julia
import Base.Threads.@spawn
using BenchmarkTools

function fib(n::Int)
    if n < 2
        return n
    end
    t = fib(n - 2)
    return fib(n - 1) + t
end

function fib_threads(n::Int)
    if n < 2
        return n
    end
    t = @spawn fib_threads(n - 2)
    return fib(n - 1) + fetch(t)
end
```

At line 4 we see the classical Fibonacci recursive function and at line 12 a recursive Fibonacci function. The only difference between the two implementation is the`@spawn` at line 16 and the `fetch` at line 17. 

Although in this case there is no gain in performance (the multi-threaded version is slower) this is an example of how it is possible to write complex and nested functions which spawn tasks on threads.

A simpler example may be this one: let's consider the case where we want to compose the result of some slow function calls, we can spawn each function call on a different thread and then compose the result:

```julia
import Base.Threads.@spawn
using BenchmarkTools

function slow_func(x)
    sleep(0.005) #sleep for 5ms
    return x
end

@btime let
    a = @spawn slow_func(2)
    b = @spawn slow_func(4)
    c = @spawn slow_func(42)
    d = @spawn slow_func(12)
    res = fetch(a) .+ fetch(b) .* fetch(c) ./ fetch(d)
end

@btime let
    a = slow_func(2)
    b = slow_func(4)
    c = slow_func(42)
    d = slow_func(12)
    res = a .+ b .* c ./ d
end
```

At line 10-13 we spawn `slow_func` at different threads and at line 14 we combine the results. On the contrary, from line 18 to 22 we perform the exact same computation without using `@spawn`. The result of the timings is respectively 5.484 ms and 24.231 ms, the multi-threaded function is much faster! 

On the other hand, if we use multi-threading for faster functions (such as the sine function) the results are not as good:

```julia
@btime let
    x = 1:100
    a = @spawn sin(2)
    b = @spawn sin(4)
    c = @spawn sin(42)
    d = @spawn sin(12)
    res = fetch(a) .+ fetch(b) .* fetch(c) ./ fetch(d)
end

@btime let
    x = 1:100
    a = sin(2)
    b = sin(4)
    c = sin(42)
    d = sin(12)
    res = a .+ b .* c ./ d
end
```

In the first case (line 1-8) I get 3.525 μs of execution and on the second case (line 10-17) I get 1.499 ns. For this example, it is not convenient to use multi-threading as the overhead due to spawning new threads and fetching the results is greater than the performance gain due to parallelization. 

Always test if using multi-threading and in general parallel computation will yield performance gains in your case.
{: .notice--info}

# Multi-processing

In order to use multiprocessing, we need to first install and include the `Distributed` package. Before running the following code please **restart the REPL**.

```julia
using Pkg
Pkg.add("Distributed")

using Distributed
```

Now we need to add a number of process equal or smaller than the number of CPU cores. In my case, my CPU has four cores, thus I will add four processes using `addprocs`:

```julia
addprocs(4)
```



# Conclusions

[todo]

If you liked this lesson and you would like to receive further updates on what is being published on this website, I encourage you to subscribe to the [**newsletter**]( https://techytok.com/newsletter/ )! If you have any **question** or **suggestion**, please post them in the **discussion below**!

Thank you for reading this lesson and see you soon on TechyTok!
