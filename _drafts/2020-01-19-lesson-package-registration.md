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

Your package will have to be hosted at [GitHub](https://github.com/) on a public repository if you want to perform integration tests with Travis and be able register it as an official package.
{: .notice--info}

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

To check whether the proper error is thrown by a function (in our case an `ErrorException` when We try to call `quad` with an integration method which is not available), we use the `@test_throws` macro:

```julia
>>>@test_throws ErrorException quad(func1, 0, 4, method = :none)
Test Passed
Thrown: ErrorException
```

You can find the file with all the tests for `MultiQuad.jl` [here](https://github.com/aurelio-amerio/MultiQuad.jl/blob/master/test/runtests.jl).

To run all the tests written inside the `runtests.jl` file, you can type in the REPL

```julia
] test
```

![image-center](/assets/images/2020/01/10/fig2-tests.png){: .align-center}

# Setting up Coveralls

[Coveralls](https://coveralls.io/) is a free platform which helps you understand the percentage of your code which is covered by your tests. Ideally, you should test all the parts of your code, but concretely this is not easy to do. Coveralls gives you a metric and some tools to determine how much of your code is covered by your tests. 

You can register for free with your GitHub account [here](https://coveralls.io/sign-up).

Once you are logged in, you can select the "add repos" page from the sidebar:

![image-center](/assets/images/2020/01/10/fig3-coveralls-1.png){: .align-center}

Or simply go to [this page](https://coveralls.io/repos/new).

You should now enable the repository for which you want to test the coverage:

![image-center](/assets/images/2020/01/10/fig4-coveralls-2.png){: .align-center}

After this step the setup of Coveralls is finished, we will see later on how Travis can automatically submit coverage reports to Coveralls automatically after a build.

# Setting up Codecov

[Codecov](https://codecov.io/) is a free platform which performs coverage tests similar to Coveralls. Although it is not mandatory, if you log in with your GitHub account  you can have an overview of all your repository for which coverage data is available. 

There is no setup required and Travis will automatically submit coverage data to codecov at build time.

# Setting up Travis

[Travis CI](https://travis-ci.com/) is a hosted continuous integration service used to build and test software projects hosted at GitHub. Travis is free for public repositories, which is fine since the Julia package we plan to register has to be hosted on a public repository.

Travis can be used to run the tests we have written on `runtests.jl` on many versions of Julia and on different operating systems. If all the tests are successful and if our code coverage is high (>90%) we can be pretty confident that our code will work properly on every configuration a user may have.

Travis uses `.yml` configuration files which contain all the information needed to perform the build tests. We shall now create a file called `.travis.yml` at the root of our package directory and  paste the following code:

```yml
language: julia
os:
  - windows
  - linux
  - osx
julia:
  - 1.0
  - 1.1
  - 1.2
  - 1.3
  - nightly
matrix:
  allow_failures:
    - julia: nightly
notifications:
  email: false
after_success:
  # push coverage results to Coveralls
  - julia -e 'using Pkg; cd(Pkg.dir("MultiQuad")); Pkg.add("Coverage"); using Coverage; Coveralls.submit(Coveralls.process_folder())'
  # push coverage results to Codecov
  - julia -e 'using Pkg; cd(Pkg.dir("MultiQuad")); using Coverage; Codecov.submit(process_folder())'
```

This is a pretty generic script which should work for every project. I want to test my package on Windows, Linux and OSX (lines 3 to 5) and I want it to work on Julia from 1.0 to 1.3 (lines 7 to 10). We also test our package on the nightly version of Julia (line 11) but we allow this build to fail (line 13-14). If our build fails on the nightly version, we have to consider that in the future we may have to change our package for it to continue working.

From line 17 to 21 we state what to do after a successful build: we want to push the coverage results to Coveralls and Codecov. 

We shall now register a free Travis account in order to create builds. Go to [travis-ci.com](https://travis-ci.com/) and log in with your GItHub account. 

Go to your [account settings page](https://travis-ci.com/account/repositories):

![image-center](/assets/images/2020/01/10/fig5-travis-1.png){: .align-center}

Type the name of the repository that you want to add to Travis (in my case `MultiQuad.jl`)

![image-center](/assets/images/2020/01/10/fig6-travis-2.png){: .align-center}

and click on the repository, this should bring you to the repository's page on Travis, where you can click on the "more options" button:

![image-center](/assets/images/2020/01/10/fig7-travis-3.png){: .align-center}

Now click on "Trigger build" to initiate the first build process, and on the next popup window click "Trigger custom build":

![image-center](/assets/images/2020/01/10/fig8-travis-4.png){: .align-center}

You should now see a somewhat lengthy page like this one:

![image-center](/assets/images/2020/01/10/fig9-travis-5.png){: .align-center}

The build process will usually take some time, in the case of `MultiQuad.jl` it will take approximately 38 minutes.
{: .notice--info}

If the build has been successful, you should see something like that:

![image-center](/assets/images/2020/01/10/fig10-travis-6.png){: .align-center}

# Adding badges to the `README`

Once the build is finished (and if it has been successful) you can add some nice badges to the `README` of your repository to show that your code is good:

![image-center](/assets/images/2020/01/10/fig11-badges.png){: .align-center}

To achieve a result similar to this one, you have to add the following code at the top of your `README.md` file:

```markdown
[![Build Status](https://travis-ci.com/aurelio-amerio/MultiQuad.jl.svg?branch=master)](https://travis-ci.com/aurelio-amerio/MultiQuad.jl)
[![Coverage Status](https://coveralls.io/repos/github/aurelio-amerio/MultiQuad.jl/badge.svg?branch=master)](https://coveralls.io/github/aurelio-amerio/MultiQuad.jl?branch=master)
[![codecov.io](https://codecov.io/github/aurelio-amerio/MultiQuad.jl/coverage.svg?branch=master)](https://codecov.io/github/aurelio-amerio/MultiQuad.jl?branch=master)
```

Just replace `aurelio-amerio/MultiQuad.jl` with `your-username/repository-name`.  

# Package settings

Before we can publish our package on the Julia  registries, we need to take care of the `Project.toml` file.

Make sure that the version of you package is either `0.1.0` (if this is a beta release) or `1.0.0` (if the package is already mature and complete). `0.1.0` is the default version and it is a good idea to leave it, if this is the first version of the package. 

After we have set the version of the package (for example `0.1.0`) we should specify the compatibility bounds of your package. 

If you are not sure of the version of the packages you are using, you can check which packages are added to your project by looking at the `Project.toml` file. In the case of `MultiQuad.jl`, I can see in the `[deps]` section that I use:

```toml
[deps]
Cuba = "8a292aeb-7a57-582c-b821-06e4c11590b1"
QuadGK = "1fd47b50-473d-5c70-9696-f719f8f3bcdc"
Test = "8dfed614-e22c-5e08-85e1-65c5234f0b40"
Unitful = "1986cc42-f94f-5a68-af5c-568840ba703d"
```

I need then to look up the version of those packages in the `Manifest.toml` file. If your project works with the installed packages on your machine, then it is safe to use those package versions as upper compatibility bounds. Thus we can write the `[compat]` (compatibility) entry of the `Project.toml` file as follows:

```toml
[compat]
Cuba = "2.0.0"
QuadGK = "2.3.1"
Unitful = "0.18.0"
julia = "^ 1.0.0"
```

If we assume that subsequent minor releases of a Package are compatible, we can adopt the notation used at line 5 to specify that the package will be compatible with every Julia release until Julia version 2.0 is released. 

The step of adding the `[compat]` entry to the `Project.toml` file is mandatory in order to have your package accepted.
{: .notice--warning}

For more information on the `[compat]` entry and for a list of all the other available options, see the [official documentation page](https://julialang.github.io/Pkg.jl/v1/compatibility/.

# Submitting the package

In order to submit your package to the registry, it is recommended to use the official [package registrator](https://github.com/JuliaRegistries/Registrator.jl). 

We need to first install the [GitHub app](https://github.com/apps/juliateam-registrator/installations/new):

![image-center](/assets/images/2020/01/10/fig10-travis-6.png){: .align-center}

# Updating the package 

In the future, when you want to update your code and release a new version, you must change the version of you package (for example, move from version 0.1.0 to version 0.1.1). In Julia the **`semver`** conventions are as follows:

- it should be a standard increment and not skip versions
- If you perform bug fixes without changing the interface, move from version 0.1.0 to 0.1.1
- If you perform upgrades to the code which are retro-compatible, i.e. no breaking changes, move from version 0.1.x to 0.2.0
- If you perform major changes which lead to a variation of the interface, add deprecation warnings and move from version 0.x.y to 1.0.0

Once you have changed the version of your package in the `Project.toml` file, you can repeat the procedure described in the "[Submitting the package](#submitting-the-package)" section.

# Conclusions

[todo]

If you liked this lesson and you would like to receive further updates on what is being published on this website, I encourage you to subscribe to the [**newsletter**]( https://techytok.com/newsletter/ )! If you have any **question** or **suggestion**, please post them in the **discussion below**!

Thank you for reading this lesson and see you soon on TechyTok!
