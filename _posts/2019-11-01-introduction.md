---
title: "Introduction to the REPL"
date: 2019-11-01
header:
  caption: "Photo by [Sushobhan Badhai](https://unsplash.com/@sushobhan) on [Unsplash](https://unsplash.com)"
  image: "/assets/images/2019/11/01/header.jpg"
  og_image: "/assets/images/2019/11/01/teaser.jpg"
  teaser: "/assets/images/2019/11/01/teaser.jpg"
excerpt: "An introduction to the Julia REPL and Juno IDE usage"
permalink: /introduction-to-the-repl/
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



Before we can start delving in the wonderful world of Julia, we need to be able to run Julia code.

We will deal with two ways to run Julia code: the first one is through the Julia REPL, the latter is through the Juno IDE.

To install Julia and the Juno IDE, please follow [this]( https://techytok.com/atom-and-juno-setup-for-julia/ ) guide!

# REPL

Once you start the Julia executable you will see something like this:

![image-center](/assets/images/2019/11/01/REPL.png){: .align-center}

This prompt is called the **REPL**: from here you can write and run Julia code. To test if everything is working as intended, please type:

```julia
print("Hello World! This is TechyTok!")
```

and press enter, you should see something like this:

![image-center](/assets/images/2019/11/01/hello.png){: .align-center}

That's it, during the next lessons simply type the code snippets inside the REPL to run them.

Although you can copy paste the code, I encourage you to type it yourself as it is useful to better remember the lesson!

# Juno IDE

Sometimes, especially when your code starts to become pretty lengthy, it is useful to organise it in scripts, i.e. files ending with the `.jl` extension. The Juno IDE lets you edit such files and run them directly line by line from the editor.

To run a line of code, simply click on the line and press `Ctrl+Enter`.

To run a block of code altogether, include it between `#%%` annotations like this:

``` julia
#%%
println("hello")
println("from")
println("TechyTok!")
#%%
```

and press `Alt-Shift-Enter `.

I encourage you to take a look at the [official documentation]( https://docs.junolab.org/latest/man/basic_usage/ ) for further details on how to use the Juno IDE.

# Conclusions

You should now be able to run Julia code either in the REPL or inside the Juno IDE and you are now ready yo move on to the first real lesson on Julia!
