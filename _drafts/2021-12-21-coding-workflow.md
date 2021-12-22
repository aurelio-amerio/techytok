---
title: "Coding workflow with VSCode"
date: 2021-12-21
header:
  caption: ""
  image: "/assets/images/2019/12/24b/header.jpg"
  og_image: "/assets/images/2019/12/24b/teaser.jpg"
  teaser: "/assets/images/2019/12/24b/teaser.jpg"
excerpt: "From zero to Julia Lesson 10. Introduction to plotting"
permalink: /lesson-plotting/
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

In the previous lessons we have focused our attention on the main constructs of the Julia language and how to code. In this lesson, I will give you a few tips and tricks. We will learn how to write code efficiently using Visual Studio Code and the Julia extension. 
We will see how `Revise` can improve our code writing and testing efficiency. Furthermore, we will learn some useful tricks and features, such as automatic code formatting, multi-line cursors, multi-panel coding, code navigation, documentation navigation, variable exploration and debugging.

You can find the code for these examples at [----]

Let's get going!

- revise
- code formatting
- documentation panel
- multi panel coding
- code navigation
- variable exploration debugging

# Revise
Imagine that you are writing a library. You have a package with a module with several functions inside (see the [lesson on packages](https://techytok.com/lesson-packages/)). As the number of functions inside a module increase, recompiling the whole module every time you add a new function, or modify one, becomes increasingly long and tedious. 

For this specific reason, [Revise](https://timholy.github.io/Revise.jl/stable/) comes in our help! 

To install Revise, simply type in the REPL:

```julia
using Pkg; Pkg.add("Revise")
```

Now let's imagine that we are developing `TestPackage1`. In order to properly activate the functionalities of Revise, we need to import the library before we load our package. The code will then look like this:

```julia
using Pkg
Pkg.activate("TestPackage1")
using Revise
using TestPackage1
```

If we call the `TestPackage1.greet()` function, a greetings message will be printed in the REPL: `Hello World!`. 

Now, let's suppose that we want to change the greetings message. Since we are using Revise, all we need to do is change the function in the `TestPackage1\src\TestPackage1.jl` file and save it. In particular, let's change line 6 and have this new message:

```julia
greet() = print("Hello from TechyTok!")
```

Now, if we call `TestPackage1.greet()` again (without restarting Julia), it will print `Hello from TechyTok!`, and we didn't need to recompile the module!

With the help of Revise, we can make any modification to our module, i.e. add or remove functions, change names, or add new variables without the need to restart Julia. This feature will save you a few minutes every time you need to do an edit, or while you are debugging.



# Conclusions

[todo]

If you liked this lesson and you would like to receive further updates on what is being published on this website, I encourage you to subscribe to the [**newsletter**]( https://techytok.com/newsletter/ )! If you have any **question** or **suggestion**, please post them in the **discussion below**!

Thank you for reading this lesson and see you soon on TechyTok!
