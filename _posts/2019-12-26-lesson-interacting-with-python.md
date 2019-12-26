---
title: "Interacting with Python"
date: 2019-12-26
header:
  caption: "Photo by [David Clode](https://unsplash.com/@davidclode) on [Unsplash](https://unsplash.com/)"
  image: "/assets/images/2019/12/26b/header.jpg"
  og_image: "/assets/images/2019/12/26b/teaser.jpg"
  teaser: "/assets/images/2019/12/26b/teaser.jpg"
excerpt: "From zero to Julia Lesson 13. Interacting with Python"
permalink: /lesson-interacting-with-python/
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

In this lesson we will learn how to install Python and configure it to be able to interact with Julia. 

There are two options: the first is to let Julia install everything by itself, the second is to configure te installation manually. If you don't plan to use Python for anything particular apart from `PyPlot` or a few other packages, you can leave the installation of Python to Julia. On the contrary, if you already use Python and Conda environments, it is better to make a custom environment for Julia.

# Automatic installation

To let Julia automatically install Python and all the dependencies, type the following code:

```julia
using Pkg
Pkg.add("PyCall")
using PyCall
```

`PyCall` will download the [miniconda installer](https://docs.conda.io/en/latest/miniconda.html) and create a separated conda environment just for Julia all by itself.

# Manual installation

If you already have Anaconda installed, please create a new wnvironment with **Python 3.6**. `PyCall` should also work with Python 3.7, but in my experience it had lead to some errors.

If you don't already have Anaconda installed, you can either install Anaconda or Miniconda. I suggest you to install the latest version of [Miniconda](https://docs.conda.io/en/latest/miniconda.html) (as it is a lightweight conda environment with everything you need) and then downgrade python to version 3.6. 

## Miniconda installation

Depending on your operating system, the installation steps of miniconda may be slightly different. On Windows 10, download the [Miniconda installer](https://docs.conda.io/en/latest/miniconda.html) and open it. Follow the insutrction on screen:

![image-center](/assets/images/2019/12/26b/fig1.png){: .align-center}

![image-center](/assets/images/2019/12/26b/fig2.png){: .align-center}

Please take note of the location where you have installed miniconda3 as we'll need it in the next steps.

![image-center](/assets/images/2019/12/26b/fig3.png){: .align-center}

Untick the two options as shown in the figure above.

Once you have installed miniconda, we need to open a conda command prompt. 

- In Windows, there should be a new shortcut in the start menu called `Anaconda Prompt`, please open it. 
- In Linux, the `.bashrc` profile should have been modified to start a conda-enable shell automatically. 

In the command prompt type the following code to downgrade python to version 3.6:

```bash
conda install python=3.6 -y
```

We are now ready to setup `PyCall`

## PyCall installation

To install `PyCall` type the following commands in the REPL:

```julia
using Pkg

ENV["PYTHON"] = "" 
ENV["CONDA_JL_HOME"] = "/path/to/miniconda3" 

Pkg.add("Conda")
using Conda

Pkg.add("PyCall")
Pkg.build("PyCall")
using PyCall
```

In order to check whether the installation was succesful, import a Python library:

```julia
math = pyimport("math")

>>>math.sin(3)
0.1411200080598672
```

Now you are ready to import any desired package through the function `pyimport`.

# PyPlot

If you want, you are now able to install `PyPlot`: a powerful back-end for `Plots.jl` which uses the Python matplotlib package. 

Before you install `PyPlot`, make sure that you have installed and configured Python correctly to interact with Julia. In particular you must be able to import Python libraries with `pyimport`, if you have followed the guide up to now and you were able to import `math` you should be fine.
{: .notice--info}

To install `PyPlot` and check whether everything is working correctly, use the following code:

```julia
using Pkg
Pkg.add("PyPlot")

using Plots
pyplot()

x=1:0.1:2*π
y=sin.(x)
plot(x, y, label="sin(x)")
```

# Conclusions

In this lesson we have learn't how to install Python and how to configure Julia to interact with Python. Furthermore, we have seen how to install `PyPlot` and use the `pyplot()` back-end with `Plots`.

If you liked this lesson and you would like to receive further updates on what is being published on this website, I encourage you to subscribe to the [**newsletter**]( https://techytok.com/newsletter/ )! If you have any **question** or **suggestion**, please post them in the **discussion below**!

Thank you for reading this lesson and see you soon on TechyTok!

