---
title: "Atom and Juno: the perfect duo for Julia development"
date: 2019-10-12
header:
  image: "/assets/images/2019/10/11/header.png"
  og_image: "/assets/images/2019/10/11/thumbnail.png"
  teaser: "/assets/images/2019/10/11/teaser.png"
excerpt: "A tutorial on how to setup Atom and Juno: the perfect free IDE to start coding in Julia"
permalink: /atom-and-juno-setup-for-julia/
toc: true
toc_label: "Table of Contents"
toc_icon: "book"

categories:
    - "Tutorial"
    - "Recipe"
tags:
    - Julia
    - Installation
    - Basic


comments: true
---

In this tutorial you will learn how to setup a working development environment for Julia.

# Introduction

[Atom](https://atom.io/) is "**a hackable text editor for the 21st Century**"! There is a plugin for almost anything and, in particular, [Juno](https://junolab.org/) is a great IDE meant for Julia development. It has all you need for scientific and interacting computing, as well as advanced profiling and data visualisation tools.

I will first guide you through the installation of Julia and then I'll show you how to install Atom and setup the Juno IDE.

# Install Julia

Depending on your operating system, you will need to download the appropriate Julia package from [julialang.org/downloads/](https://julialang.org/downloads/) . If you have **Windows** or **OSX**, just download and run the installer and follow the instructions. When you are prompted to choose an installation directory, I suggest you to choose your home directory: this way it will be easier when time comes to upgrade Julia to the next version.

If you are a **Linux** user, you should download and extract the archive into an appropriate folder (pay attention to the architecture of the binaries you are downloading!)

Once you have installed Julia successfully, you can move to the next section: installing atom!

# Install Atom

![image-center](/assets/images/2019/04/26/atom.png){: .align-center}

Atom is a hackable text editor for the 21st Century!

It is basically a text editor with support for syntax highlighting and open source plugins. It is made by Github (for reference, this website is hosted at Github Pages) and it offers great flexibility: there is a set of plugins to create an IDE for almost any programming languages!

You can download Atom from [here](<https://atom.io/>). There is not much to say about it, as there is not even anything to configure while installing it, just follow the installer!

# Install and configure Juno IDE

![image-center](/assets/images/2019/04/26/juno.png){: .align-center}

To install Juno, open atom, click on `file > settings > install` and type `uber-juno`: this is the package that you need to install. `uber-juno` will do everything by itself and install the IDE in a matter of minutes!

![image-center](/assets/images/2019/04/26/install-juno.png){: .align-center}

Once the setup is done, we need to tell Juno where to find the Julia binary: go to `file > settings > packages` and type `julia-client` 

![image-center](/assets/images/2019/04/26/juno-setup-1.png){: .align-center}

You should now click on `settings` and navigate to the `Settings`section: you will find a field called `Julia Path`: you should type the path to the Julia executable in there. 

![image-center](/assets/images/2019/10/11/fig1_julia_path.png){: .align-center}

In my case, the path to my Julia executable is `D:\Users\Aure\Julia-1.2.0\bin\julia.exe` 

![image-left](/assets/images/2019/10/11/fig2_start_julia.png){: .align-left}

If you have done everything correctly now you can start a Julia session inside atom: to do so click on the earth-like icon on the left toolbar (or go to `Juno > Start Julia`)

A panel should open on the lower end of Atom and you should be able to access the Julia REPL:

![image-center](/assets/images/2019/10/11/fig3_hello_world.png){: .align-center}

I suggest that you type the following code to check whether your setup is successful:

```julia
println("Hello World! This is TechyTok!")
```

# Conclusions 

That's it, you have successfully installed Julia and setup the Juno IDE: easy, isn't it? You can now start coding and start you journey in the wonderful world of the Julia Language! 

If you are new to Julia, I suggest that you take a look at the wonderful JuliaComputing introductory tutorials available [here](#https://github.com/JuliaComputing/JuliaBoxTutorials/tree/master/introductory-tutorials/intro-to-julia): they will give you a taste of what Julia can do and you will have a chance to explore the basics of this wonderful language!

I hope you enjoyed this tutorial, if you liked it or for any questions please leave a comment bellow! 

Stay tuned for further guides and tutorials here, at TechyTok!

 

