---
title: "Control Flow"
date: 2019-11-19
header:
  caption: "Photo by [Rock'n Roll Monkey](https://unsplash.com/@rocknrollmonkey) on [Unsplash](https://unsplash.com/)"
  image: "/assets/images/2019/11/18/header.jpg"
  og_image: "/assets/images/2019/11/18/teaser.jpg"
  teaser: "/assets/images/2019/11/18/teaser.jpg"
excerpt: "From zero to Julia Lesson 4. Control Flow"
permalink: /lesson-control-flow/
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

In this lesson we will learn how to work with control statements. We will first learn how to use conditional blocks like `if ... else` blocks and then we will learn how to perform loops.

# If ... else

When a program needs to take decisions according to certain conditions, the `if ... else` block is the default choice.

Let's suppose that we want to write a simple implementation of the absolute value of a number. The absolute value of a number is defined as the number itself, if the number is positive, or the opposite if the value is negative. This is the typical case where the `if ... else` construct is useful! We can write a simple `absolute` function in this way:

```julia
function absolute(x)
    if x >= 0
        return x
    else
        return -x
    end
end
```

As you can see, an `if ... else` block is closed with the word `end`, like a function.

If we need to check more than one condition, we can bind together conditions using:

- "and" is written as `&` (if both statements are true return true, else return false)
- "or" is written as `||` (if at least one statement is true return true, else return false)

For example, if we want to check whether 3 is both minor than 4 and major than 1 we type:

```julia
if 1 < 3 & 3 < 4
    print("eureka!")
end
```

If we want to check if a value satisfies one of several different conditions, we can use the `elseif` statement: Julia will check if the first condition specified in the `if` is satisfied, if it is not met it moves on to the first `elseif` and so on.

```julia
x = 42
if x<1
    print("$x < 1")
elseif x < 3
    print("$x < 3")
elseif x < 100
    print("$x < 100")
else
    print("$x is really big!")
end

>>> 42 < 100
```

I take the occasion to introduce **string interpolation**. With the indication `$x` we tell Julia that it must substitute to `$x` the value of `x`, in this case 42. This is particularly useful when we want to print values, or we want to make custom messages:

```julia
name1 = "traveler"
name2 = "techytok"
print("Welcome $name1, this is $name2 :)")
```

# Loops

A loop is the operation of repeating the same set of instructions several times. Loops are useful when we want to compute the value of a function over several points, we need to perform some operations on the elements of an array or we need to print the elements of a list.

## For loops

Sometimes we want to iterate over a list of values and perform some operation on each element.

For example let's suppose we want to print all the squares of the numbers comprised between 1 and 10, we can do it using a `for` loop:

```julia
for i in 1:10
    println(i^2)
end
```

`i` is the variable which contains the data which gets updated at each new cycle (in this case `i` holds the numbers from 1 to 10 respectively), while `1:10` is a **range**. A range is an iterable object which does exactly what its name suggests: it specifies the range on which the loop has to be performed (in this case 1 to 10).

It is also possible to use the alternative notation `for i = 1:10` which is completely equivalent.
{: .notice--info}

Please notice that it is possible to loop not only over ranges (which can also be specified using the [`range`]( https://docs.julialang.org/en/v1/base/math/#Base.range ) function) but also lists (i.e. arrays, tuples, etc).

For example, let's suppose we have a list of persons and we want to greet all of them, we can do it with the `for` statement:

```julia
persons = ["Alice", "Bob", "Carla", "Daniel"]

for person in persons
    println("Hello $person, welcome to techytok!")
end
```

Here instead of a range, we iterate over the elements of `persons` (i.e. the names of the persons that I want to greet) and in this case `person` will hold the name of a single person, which changes at each iteration step.

For more informations on arrays and collection types, please refer to [this](/lesson-data-structures) lesson. 
{: .notice--info}

## Break

In the case we want to forcefully interrupt a for loop we can use the `break` statement, for example:

```julia
for i in 1:100
    if i>10
        break
    else
   	    println(i^2)
    end
end
```

Here we check if `i>10`, in that case we break the loop and finish the iteration, else we print `i^2`.

## Continue

This is the opposite of `break`, `continue` will forcefully skip the current iteration and move to the next cycle:

```julia
for i in 1:30
    if i % 3 == 0
        continue
    else
        println(i)
    end
end
```

This loop prints all the numbers from 1 to 30 except the multiples of 3.

## While loop

When a loop needs to continue until a certain condition is met, a `while` loop is the preferable choice:

```julia
function while_test()
    i=0
    while(i<30)
        println(i)
        i += 1
    end
end
```

While blocks can access and change the values of variables in the scope of the block in which they are defined. For more info on variable scope, see [this](https://techytok.com/lesson-variable-scope/) lesson.
{: .notice--info}

# Enumerate

`enumerate` is a function which comes in handy when we need to iterate on an array (or similar) and we need to keep track of the number of iterations we have already performed.

Let's say we want to read the elements from an array, square them and store them in another array, we can do it in this way:

```julia
my_array1 = collect(1:10)
my_array2 = zeros(10)
for (i, element) in enumerate(my_array1)
    my_array2[i] = element^2
end

>>>print(my_array2)
[1.0, 4.0, 9.0, 16.0, 25.0, 36.0, 49.0, 64.0, 81.0, 100.0]
```

# Conclusion

In this lesson we have learned how to let a program take "decisions" using `if ... elseif ... else` blocks, how to perform loops using `for `and `while` and how to have control on such loops using `break` and `continue`. We have then given an example of how `enumerate` can be used to help the process of filling an array.

If you liked this lesson and you would like to receive further updates on what is being published on this website, I encourage you to subscribe to the [**newsletter**]( https://techytok.com/newsletter/ )! If you have any **question** or **suggestion**, please post them in the **discussion below**!

Thank you for reading this lesson and see you soon on TechyTok!
