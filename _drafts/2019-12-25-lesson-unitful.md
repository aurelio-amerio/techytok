---
title: "Units of measurement"
date: 2019-12-25
header:
  caption: ""
  image: "/assets/images/2019/12/25b/header.jpg"
  og_image: "/assets/images/2019/12/25b/teaser.jpg"
  teaser: "/assets/images/2019/12/25b/teaser.jpg"
excerpt: "From zero to Julia Lesson 12. Units of measurement"
permalink: /lesson-unitful/
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

In this lesson we will learn how to use units of measurement in Julia. We will learn how to convert from one unit to another and we will see how to write functions which can deal with units.

# `Unitful.jl`

The package used to deal with units of measurement in Julia is `Unitful.jl`. First of all we need to install `Unitful`:

```julia
using Pkg
Pkg.add("Unitful")
using Unitful
```

In order to add units of measurement to numbers we use the notation `u"unit"`:

```julia
one_meter = 1u"m"
```

or 

```julia
one_meter = 1*u"m"
```

It is possible to convert from one unit to another using `uconvert`:

```julia
b = uconvert(u"km", one_meter)
>>> b 
1//1000 km

>>>one_meter
1 m
```

As you can see on line 3 and 6, `uconvert` won't change the unit of the 


# Conclusions

[todo]

If you liked this lesson and you would like to receive further updates on what is being published on this website, I encourage you to subscribe to the [**newsletter**]( https://techytok.com/newsletter/ )! If you have any **question** or **suggestion**, please post them in the **discussion below**!

Thank you for reading this lesson and see you soon on TechyTok!
