---
\title: "Plotting and data reading" 
date: 2019-06-03
header:
  image: "/assets/images/2019/06/03/---"
  og_image: "/assets/images/2019/06/03/---"
  teaser: "/assets/images/2019/06/03/---"
excerpt: "Learn how to read files and plot their content"
permalink: /file-reading-and-plotting/
toc: true
toc_label: "Table of Contents"
toc_icon: "book"

categories:
    - "Tutorial"
tags:
    - Julia

comments: true
---

In this tutorial you will first learn how to plot you data in some simple and easy steps. You will then learn how you can export data tables to txt files and how they can be read.

## Plotting

For this tutorial you will need to install the following packages:

- DataFrames
- CSV
- Plots

To install them, simply write in the REPL

```Julia
using Pkg
Pkg.add("DataFrames")
Pkg.add("CSV")
Pkg.add("Plots")
```

We will now make some data and proceed to plot it:

```julia
using Plots

x=0:0.1:pi
y=sin.(x)

plot(x,y, label="sin(x)")
```

Easy, isn't it?

Now we may want to add some other data to our plot, for this reason we use `plot!` instead of `plot`

```julia
y1=cos.(x)

plot!(x,y1, label="cos(x)")
```

And if we want to add a label to the x and y axis, as well as a title to our plot, we type

```julia
xlabel!("x")
ylabel!("f(x)")
title!("My awesome Plot!")
```

Let's say that we want the plot to be in logarithmic scale on the x or y axis, we can use:

```julia
xaxis!(:log10)
# or yaxis!(:log10)
```

The legend is not really well placed in the top right corner as it covers our plot partially, so lets move it to the bottom left corner!

```julia
plot!(legend=:bottomleft)
```

And we are done with our first plot! To save it first set the dpi of the picture, then save it