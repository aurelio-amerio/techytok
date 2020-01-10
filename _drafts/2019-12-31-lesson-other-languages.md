---
title: "Calling other languages"
date: 2019-12-31

header:
  caption: ""
  image: "/assets/images/2019/12/31/header.jpg"
  og_image: "/assets/images/2019/12/31/teaser.jpg"
  teaser: "/assets/images/2019/12/31/teaser.jpg"
excerpt: "From zero to Julia Lesson 19. Calling other languages"
permalink: /lesson-other-languages/
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

In this lesson we will give some hints on how to call several programming languages directly from Julia. When possible, we will point out how to call Julia from those languages.

# Python

We have already seen how to install and call Python from Julia in [this lesson](https://techytok.com/lesson-interacting-with-python/). It is possible to call Julia code from Python using [PyJulia](https://github.com/JuliaPy/pyjulia). In order to use some Julia code from Python, please first install and configure `PyCall`, as seen in the Python guide, and then install `PyJulia`, typing the following command in the conda environment shell:

```bash
pip install julia
```

You can now setup the `julia` package in Python:

```python
import julia
julia.install()
```

If the Julia executable is not in path, you will receive an error message. In order to let Python know where to find the Julia executable, please set an environment vaiable called `julia` which should contain the path to the Julia executable. 

In order to set an environment variable in Windows 10, open the anaconda prompt and type:

```bash
set julia=C:/path/to/julia.exe
```

If you use Linux, type instead inside the Anaconda prompt:

```bash
export julia=C:/path/to/julia
```

Now, without closing the command prompt, open python and run again:

```julia
import julia
julia.install()
```

If everything is alright, this time you should be able to call Julia from Python:

```julia
from julia import Base 
Base.sind(90)
```

You can find additional information on how to use `PyJulia` at the [official repository](https://github.com/JuliaPy/pyjulia).

# C++

It is possible to call C++ code from Julia 

# Conclusions

[todo]

If you liked this lesson and you would like to receive further updates on what is being published on this website, I encourage you to subscribe to the [**newsletter**]( https://techytok.com/newsletter/ )! If you have any **question** or **suggestion**, please post them in the **discussion below**!

Thank you for reading this lesson and see you soon on TechyTok!
