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

In this lesson I will report the Windows/Linux shortcuts for commands. If you are using a mac, the shortcuts might be different. It will often be sufficient to replace the `CTRL` button with the `CMD` button. 

You can find the code for these examples at [----]

Let's get going!

- revise
- code formatting
- multi panel coding
- code navigation
- documentation panel
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

# Code formatting

When we code, we don't always follow the style guidelines. For example, we don't care about perfectly aligning every piece of code, maybe we don't use spaces before and after a `=` symbol, or we don't add the correct spacing everywhere.

Fortunately for us, the code formatter comes in our help. Simply press `ALT+SHIFT+Enter` with a file open on VSCode and the Julia extension will take care of it! Please note, that the Language Server must be running before we can format some Julia code, so please run a piece of your code before you try to format a document. 

In case you don't want to format your whole code, you can also select a piece of code and press `ALT+SHIFT+Enter`, and Julia will only format that piece of code.

# Multi panel coding

Sometimes, it becomes  useful to code with two files open at the same time. For example. you might want to take a look at one file where a function is implemented and use it in another one. Or you need to see at the same time two different lines of the same file. In this regard, panels come in our help.

In order to split a window in more panels, right click on the tab of a file and a context menu will show several options:

![image-center](/assets/images/2021/12/22/panel_menu.png){: .align-center}

Let's consider all the possible options and see what they do:

- Split up/down/left/right
- Split in group

## Split up/down/right/left

If you click one of these options (for example split right) a panel will open on a side of the screen (the right side in our case). It will look like you have opened the same file twice, while the file is actually the same one. This way, each modification you make on the file will be "mirrored" on each panel. 

![image-center](/assets/images/2021/12/22/two_panels.png){: .align-center}

This option is especially useful if you need to edit/see two different file at the same time. You can drag and drop file tabs from each panel to another one, and you are only limited by the size of your screen. Remember that if you need more space on your screen, you can collapse the file browser panel on the left by pressing the explorer icon (the one with two sheets, on hte top left of your screen).

## Split in group

Sometimes, you need to edit or read several places in the same file. In this case, the `Split in Group` option might be preferable. 

When you split a file in group, you will see something like that:

 ![image-center](/assets/images/2021/12/22/split_in_group1.png){: .align-center}

As you can see, the file is not opened inside a new panel, and it is instead split in the same panel. If you switch to another tab, the new file will not be displayed in a split view, and if you come back to the previous file the split view will remain in place. This is extremely useful if you need to explore several parts of the same file and you don't want to open a different panel. It is also useful to take a quick peek to another part of the code without losing the line where you have been working.

This option is compatible with other panels too:

 ![image-center](/assets/images/2021/12/22/split_in_group2.png){: .align-center}

# Code navigation

A wonderful feature which is made possible by the VSCode extension for Julia is code navigation. 

Imagine that you are developing a module, and you want to quickly see the function definition, and in particular the function signature. In order to do it, simply press `CTRL` and hover your mouse cursor over the name of the function. You will see a small window with the function definition:

![image-center](/assets/images/2021/12/22/navigation1.png){: .align-center}

If you want to quickly go to the function definition, just `CTRL+Click` on the name of the function. If you want to go back to where you where before, and you have a mouse with additional side buttons, you can press the "back" button to go back.  

Please notice that code navigation works under certain premises, in particular at the moment you can only navigate to functions defined inside the same module/namespace. This means that you won't be able to navigate to the definition of a function defined inside a module, if you are working with a script. This is due to the fact that a script runs in the "Base" namespace. 
{: .notice--info}

# Find and replace

One simple yet useful feature is the ability to find a certain word and eventually replace it. In order to open the `find` panel, just press `CTRL+F`. To open the replace menu, click on the small arrow on the left of the find field. You will then be able to replace a word with another one. 

Sometimes, you decide that you want to refactor your code and change the name of a function. In order to replace a string everywhere in a folder/whole project there is the `Find in Files` feature. In order to access it, press `CTRL+SHIFT+F`. This menu will help you find a certain string in your whole project directory, and eventually replace it. 

If you want to search a string only in one folder, right click on the folder name in the file explorer and press `Find in Folder`. 

![image-center](/assets/images/2021/12/22/find_in_folder.png){: .align-center}

# Multi line cursor

Imagine that you are copying a piece of code and you need to modify the name of a variable. This could be done with the find and replace function, but sometimes it is just faster to do some edits manually. Nonetheless, usually we don't want to write the same thing over and over, and for this reason the multi cursor feature comes in handy.

To place your cursor simultaneously at different places, keep `ALT` pressed and click on the new positions: you will see something like this:

![image-center](/assets/images/2021/12/22/multi_cursor.png){: .align-center}

If you write something, it will now be written simultaneously by all your cursors.

# Documentation panel

In the Julia extension panel (Julia symbol on the left toolbar), there is a handy documentation tab where you can search for the documentation of a function:

 ![image-center](/assets/images/2021/12/22/documentation_panel.png){: .align-center}

This panel behaves as the `help` function would, with the additional bonus that the markdown will be rendered, and thus the documentation will look similar to the official online julia documentation. You can use this panel to quickly look for the signature of a function, of for an example on how to use it. If you properly document your functions, you will also be able to access the documentation for your functions.

At the time of writing, the documentation panel does not seem to work properly. It seems that not every function for which the documentation is available through the `help` function is researchable. 
{: .notice--warning}

# Variable explorer

In many cases, it might not be practical or you might not want to write the code to print a variable. It is possible to access the value of any defined variable through the variable explorer. If you open the Julia extension, there will be a panel called `WORKSPACE` where you can see all the available namespaces (one for each module, and the global scope). 

If we define some variables, like a number, a vector and a matrix, we will be able to see their content in the variable explorer:

 ![image-center](/assets/images/2021/12/22/var_explorer1.png){: .align-center}

 ![image-center](/assets/images/2021/12/22/var_explorer1b.png){: .align-center}

It is also possible to expand matrices and view them in a gird view by clicking on the small button on the right of the variable name (open in VSCode):

 ![image-center](/assets/images/2021/12/22/var_explorer2.png){: .align-center}

Please notice that since a function is just a particular type of variable, it is possible to also see a list of the functions defined inside a module/REPL using the variable explorer: 

 ![image-center](/assets/images/2021/12/22/var_explorer3.png){: .align-center}

# Debugging



# Conclusions

[todo]

If you liked this lesson and you would like to receive further updates on what is being published on this website, I encourage you to subscribe to the [**newsletter**]( https://techytok.com/newsletter/ )! If you have any **question** or **suggestion**, please post them in the **discussion below**!

Thank you for reading this lesson and see you soon on TechyTok!
