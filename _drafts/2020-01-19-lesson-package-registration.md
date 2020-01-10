---
title: "Package Registration and Tests"
date: 2020-01-10
header:
  caption: ""
  image: "/assets/images/2020/01/10/header.jpg"
  og_image: "/assets/images/2020/01/10/teaser.jpg"
  teaser: "/assets/images/2020/01/10/teaser.jpg"
excerpt: "From zero to Julia Lesson 19. Learn how to write tests for a package using Travis and how to publish a finished package to the Julia Registry"
permalink: /lesson-package-registration/
toc: true
toc_label: "Table of Contents"
toc_icon: "book"

categories:
    - "Tutorial"
    - "Lessons"
tags:
    - Julia
	- Travis

comments: true

sidebar:
  nav: "zero-to-julia"
---

In this lesson we will learn how to **write tests** to check if a package is working properly and how to automatically check if those tests are passed after every commit to the master branch of your project. Furthermore, we will learn how to **publish a finished package** on the **Julia Registry**. 

For this lesson we will write tests and see the procedure to register a package which I have been developing recently, [`MultiQuad.jl`](https://github.com/aurelio-amerio/MultiQuad.jl/). `MultiQuad` is a convenient wrapper around some integration routines provided by [`QuadGK`](https://github.com/JuliaMath/QuadGK.jl) and [`Cuba`](https://github.com/giordano/Cuba.jl). It lets you compute 1D, 2D and 3D finite integrals with custom integration bounds.

# Writing tests

When you write your code, you should think of some tests which it should pass. For example, in the case of `MultiQuad.jl`, since we want to perform numerical integration, we should check if the numerical results reasonably coincide with the analytical results. 

`MultiQuad`exports three functions: `quad`, `dblquad` and `tplquad`: we want to write some tests for all those functions. When we are writing tests, we should try to **test every function and option provided** by the package. 

In Julia, in order to write tests for a package we need to first create a package using `Pkg.generate("PkgName")`. For more information on how to create packages, please refer to [this lesson](https://techytok.com/lesson-packages/). 

Once we have finished writing a part of the package, we can start writing tests. In the package folder, create a folder called `test` and inside that folder create a file called `runtests.jl`. You project tree should look like this:

![image-center](/assets/images/2020/01/10/fig1-tree.png){: .align-center}

Please open the `runtests.jl` file, which is where we will write the tests for our package.

We need to first activate the project using `Pkg.activate()` and then we need to add the `Test` package to our project using `Pkg.add("Test")`. In order to write tests, we need to import our package, the Test package and every other package which will be used. In our case we will write:

```julia
using MultiQuad, Test, Unitful
```

We can now start writing some tests.

**A test is an expression which should evaluate to true or false**: true means that the test is successful and false means that the test has failed. To indicate to Julia that a particular expression is a test, we use the `@test` macro, for example:

```julia
>>>@test 42>4
Test Passed

>>>@test sin(3)>5
Test Failed at ---:1
  Expression: sin(3) > 5
   Evaluated: 0.1411200080598672 > 5
```

It is possible to write a set of tests for a common field, for example we can write a series of tests for a function. In the case of `MultiQuad` I have written three test tests, one for each function, which test all the possible arguments for the functions.

To create a test set, use the `@testset` macro:

```julia
@testset "name of the test set" begin
    # write some tests
    @test 3>1
    # .... etc 
end
```

In the case of `MultiQuad` we want to check if the result of an integration is correct, within the accuracy we have chosen. Thus I compute a numerical integration and compare the result to the correct analytic solution (computed using Wolfram Mathematica):

```julia
@testset "quad" begin
    rtol = 1e-10

    xmin = 0
    xmax = 4
    func1(x) = x^2 * exp(-x)
    res = 2 - 26 / exp(4)
    int, err = quad(func1, xmin, xmax, rtol = rtol)
    @test isapprox(
        int,
        res,
        atol = err,
    )
end
```

At line 1 we choose the relative tolerance at which the integration routine should stop (which is proportional to the error on the final result). At line 7 we define the analytical result and at line 8 we compute the integral and store the result and the error estimation on the result. At line 9 I use the function `isapprox` which tests whether the two arguments are equal within the given tolerance, which equals to checking whether $$ |a-b| < tollerance$$ .

Next we need to write many more tests to check all the options available. I will not deal with all the tests for this package, but I want to focus on two: checking that an error is thrown when I try an unavailable integration method and check whether units of measurement are handled properly.

In order to check if the units are handled properly, I add this piece of code to the `quad` test set:

```julia
xmin2 = 0 * u"m"
xmax2 = 4 * u"m"
func2(x) = x^2
res2 = 64 / 3 * u"m^3"
int2, err2 = quad(func2, xmin2, xmax2, rtol = rtol)
@test isapprox(
    int2,
    res2,
    atol = err2,
)
```

As you can see now the result has a unit of measurement. If the test succeeds, then units are likely to be handled properly. 

To run all the tests written inside the `runtests.jl` file, you can type in the REPL

```julia
Pkg.test()
```





# Conclusions

[todo]

If you liked this lesson and you would like to receive further updates on what is being published on this website, I encourage you to subscribe to the [**newsletter**]( https://techytok.com/newsletter/ )! If you have any **question** or **suggestion**, please post them in the **discussion below**!

Thank you for reading this lesson and see you soon on TechyTok!
