---
title: "VSCode: the future for Julia development"
date: 2020-08-10
header:
  caption: "[julia-vscode.org](https://www.julia-vscode.org/)"
  image: "/assets/images/2020/08/10/header.jpg"
  og_image: "/assets/images/2020/08/10/teaser.jpg"
  teaser: "/assets/images/2020/08/10/teaser.jpg"
excerpt: "Learn how to setup a Julia development environment using VSCode."
permalink: /julia-vscode/
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

In this tutorial you will learn how to install install Julia and setup VSCode with the Julia extension to start developing in a friendly and feature rich environment.

# Introduction 

Visual Studio Code is a free and open source text editor. With its many plug-ins, it can be customized to satisfy all the needs of a researcher/programmer.

A bit of context: in the past years there have been two main IDE projects for Julia: the Juno project for Atom and the Julia VSCode extension project, but now things have changed! Starting from an exiting announcement made at JuliaCon2020, both development teams have joined their efforts in making a single better extension for VSCode: [Julia for VSCode](https://www.julia-vscode.org/). Currently Juno has been put in maintenance mode, meaning that it is still perfectly functional, but new features will be added only to the VSCode extension. In case you were uncertain whether it would be better to use Juno for Atom or VSCode, **it is thus suggested to use the VSCode extension** rather then Juno.

In this tutorial we will first learn how to install Julia and then we will setup VSCode. 

# Install Julia

Depending on your operating system, you will need to download the appropriate Julia package from [julialang.org/downloads/](https://julialang.org/downloads/). It is suggested that you install the latest stable version (currently 1.5)

![image-center](/assets/images/2020/08/10/fig1-julia-download.png){: .align-center}

If you have **Windows** or **OSX**, just download and run the installer and follow the instructions. When you are prompted to choose an installation directory, I suggest you to choose your home directory: this way it will be easier when time comes to upgrade Julia to the next version. Nonetheless, the default directory should be fine. **Please take note of where you installed Julia**, since we will need this path when we setup VSCode.

If you are a **Linux** user, you should download and extract the archive into an appropriate folder (pay attention to the architecture of the binaries you are downloading!). 

![image-center](/assets/images/2020/08/10/fig1b-julia-installation.png){: .align-center}

You can find additional information on how to install Julia [here](https://julialang.org/downloads/platform/). I don't advise adding Julia to your system path unless you need to call Julia from the command line and you know what you are doing. 

Once you have installed Julia successfully, you can move to the next section: installing VSCode!

# Visual Studio Code

![image-center](/assets/images/2020/08/10/fig2-vscode.png){: .align-center}

You can get VSCode from its download page: https://code.visualstudio.com/Download

Please run the installer and start VSCode.

![image-center](/assets/images/2020/08/10/fig3-vscode.png){: .align-center}

Once you have installed VSCode, we need to install the Julia extension. Please click on the extension button in the menu on the left:

![image-center](/assets/images/2020/08/10/fig4-extension.png){: .align-center}

Type `Julia` in the search bar and click `install` to install the Julia extension:

![image-center](/assets/images/2020/08/10/fig5-julia.png){: .align-center}

Once you have installed the Julia extension, we need to open the settings tab. To do so, click on the little cogwheel icon next to the name of the extension and click `extension settings`

![image-center](/assets/images/2020/08/10/fig6-julia-settings-1.png){: .align-center}

Now find the file called `Julia: Executable Path` and type the path to your Julia executable:

![image-center](/assets/images/2020/08/10/fig7-julia-settings-2.png){: .align-center}

If you are used to working with Juno or want to work in a notebook-like environment, I suggest to you enable inline results. To do so, look for the `Execution: Result Type` entry and select `inline`. 

![image-center](/assets/images/2020/08/10/fig8-julia-settings-3.png){: .align-center}

This way, when you run a piece of code, you will see the results of the computation next to the line corresponding to the instruction.

## Writing code

We are now ready to create our first Julia script and run it inside VSCode!

Click on the "file" icon in the left toolbar and open a folder where to save your first Julia script. 

![image-center](/assets/images/2020/08/10/fig9-explorer.png){: .align-center}

A good initial location for your Julia code could be a folder called `julia-scripts` in your documents folder. Now click the "new file" icon to create your first piece of Julia code:

![image-center](/assets/images/2020/08/10/fig10-new-file.png){: .align-center} 

Now chose a name for your file and terminate it with the `.jl` extension, to denote that it is a Julia file. A good example could be `hello.jl`.

![image-center](/assets/images/2020/08/10/fig11-new-file-hello.png){: .align-center} 

You can now open your newly created file and type a piece of code. Please copy paste the following code:

```Julia
print("Hello! This is TechyTok!")
```

![image-center](/assets/images/2020/08/10/fig12-new-file-hello-2.png){: .align-center} 

To run this line of code, place the cursor on the line and press `Alt+Enter` to run this single line of code. A new terminal window should open in the lower part of VSCode and you should see something similar to this:

![image-center](/assets/images/2020/08/10/fig13-new-file-hello-3.png){: .align-center} 

This window is called the **REPL** and it is a Julia command prompt. When you press `Alt+Enter`, the code written on the `.jl` file gets sent to the **REPL**, where it is evaluated (it is actually a little bit more complicated than this, if you are interested please read this [documentation page](https://www.julia-vscode.org/docs/stable/userguide/runningcode/#Julia:-Execute-Code-Block-(AltEnter)-1)). In particular, the command `print` prints a message in the REPL. 

Let's see another example. If you type `2+2` and press `Alt+Enter` you will get, as an inline result, 4:

![image-center](/assets/images/2020/08/10/fig14-new-file-hello-4.png){: .align-center} 

## Code cells 

If you want to type several lines of code end execute them all at once, you can create a code cell.

To create a code cell, enclose a one or more lines of code between `##` symbols:

```julia
## this is a code cell
println("Hello")
println("This is TechyTok!")
##
```

To execute a code cell, place you cursor inside a code cell and press `Shift+Enter`. If you want to create another code cell right after the previous one there is no need to type `##` again, it is sufficient to type `##` once it is finished.

```julia	
## this is a code cell
println("Hello")
println("This is TechyTok!")
## this is another code cell
println(42)
##
```

Please note that since `#` is the character that denotes the beginning of a comment line in Julia, after `##` it is possible to write any comment to specify the content of the code cell, which makes the code much more readable. 

For more informations about the Julia extension for VSCode, please visit the [official website](https://www.julia-vscode.org/) and read the [documentation](https://www.julia-vscode.org/docs/stable/). At the time of writing this guide, the documentation for the Julia VSCode extension is not yet complete and many pages are still missing since many new features are being ported from Juno to VSCode, but in the near future you can expect a rich and complete documentation. 


# Conclusions

In this lesson we have learnt how to install Julia and how to setup VSCode with the Julia extension. We have learnt how to create files in VSCode and how to run both lines of code and code cells. 

Now that you have a working development environment for Julia, you can move on and start learning with the first lesson on [variables and types](https://techytok.com/lesson-variables-and-types/). If you are and advanced user, you may be interested in [this guide](https://techytok.com/from-zero-to-julia-using-docker/) on how to use docker containers with Julia and VSCode in a simple and seamless way.  

I leave you with a video demonstration by David Anthoff (one of the developers behind Julia for VSCode) which was broadcasted during JuliaCon2020:
{% include video id="IdhnP00Y1Ks" provider="youtube" %}

If you liked this lesson and you would like to receive further updates on what is being published on this website, I encourage you to subscribe to the [**newsletter**]( https://techytok.com/newsletter/ )! If you have any **question** or **suggestion**, please post them in the **discussion below**!

Thank you for reading this lesson and see you soon on TechyTok!
