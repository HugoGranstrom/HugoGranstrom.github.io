---
layout: post
title: Numerical Integration in Nim - Tutorial
categories: [Nim]
shortdescription: Tutorial on numerical integration in Nim using NumericalNim 
---

This is the first blog post in a <a href="{{ site.baseurl }}/category/Nim/">series</a> about numerical computations in <a href="https://nim-lang.org">Nim</a>. Today we will go over numerical integration using Nim, specifically using my library <a href="https://github.com/hugogranstrom/numericalnim">NumericalNim</a>. I will assume you have some experience using Nim, otherwise check out the tutorials <a href="https://nim-lang.org/learn.html">here</a>. Let's begin!

The first thing we want to do is to install NumericalNim using Nimble by running this command in the terminal:
```nim
nimble install numericalnim
```
To use it we import it at the top of the `.nim` file:
```nim
import numericalnim
```
We will go through two scenarios depending on what exactly you want to integrate. 
1. You have a mathematical expression for the function.
2. You only know the values of the function at specific discrete points (for example if you have done some kind of measurement).
3. We want to calculate a cumulative integral from a to b, ie we also want the integral between a -> a + dt and a -> a + 2*dt and so on.  

# 1. Integrating mathematical functions
In this scenario we know the function `f(x)` on the entire interval of integration. Now we need to make a Nim proc that calculates it. In this example I will use the function $$f(x) = sin^2(2 \pi x)$$. It has the primitive function $$F(x) = \frac{x}{2} - \frac{sin(4 \pi x)}{8 \pi}$$ which we will use to check how accurate we can get our numeric solutions. From this we can define two procs:
```nim
import math
import numericalnim

proc f(x: float, ctx: NumContext[float]): float =
  sin(2 * PI * x) ^ 2

proc primitive(x: float): float = 
  x / 2 - sin(4* PI * x) / (8 * PI)
```
NumericalNim requires that the proc you pass in is on the form `proc(x: float, ctx: NumContext[T]): T` where `NumContext` is a type which can be used to save parameters or data between function calls. As we won't integrate `primitive` we don't need it there. Now we are ready to start integrating `f(x)`!

The integral we are going to compute is this:

$$I_1 = \int_0^{10} f(x) dx$$

Now we have all we need to calculate the integral! Are you ready? I can't hear you so I assume you are if you proceed ;)

NumericalNim offers quite a few different methods you can use for your integration. They differ in accuracy and performance as well as adaptability. That means some methods use a fixed step size where it evaluates `f(x)` while some can on it's own change the step size so it takes smaller steps in regions where the functions changes rapidly and longer steps where it doesn't change much. That's the kind of integrators I recommend if you know the function on the entire interval because you can set an error tolerance which the method then will do it's best to satisfy. NumericalNim offers two such methods: `adaptiveSimpson` and `adaptiveGauss` out of which `adaptiveGauss` is the recommended one because it is faster, more robust and supports integrating from $$-\infty$$ to $$\infty$$ by means of a change of variable. If you want some geeky details it uses a Gauss-Kronrod method of order 21 with a global error control scheme. Compare that to `adaptiveSimpson` which is of order 4 (or 5 depending on how you see it) and uses local error control. I have found that global error control is superior in basically all cases because it does the minimal work to reduce the error by subdividing the interval with the greatest error first. Enough geekyness, let's get coding!

```nim
let I1 = adaptiveGauss(f, 0, 10)
let correct1 = primitive(10) - primitive(0)
echo "Correct: ", correct1
echo "Numeric: ", I1
echo "Error:   ", abs(correct1 - I1)
```
```nim
Correct: 5.0
Numeric: 5.000000000000002
Error:   1.776356839400251e-015
```
That is pretty accurate! That's cool and all but why bother doing it numerically when we could have done it analytically instead? That's a good point, but the fact is that there are loads of integrals for which we can't find a primitive function. A simple example is $$e^{x^2}$$ which is widely used. For example in the normal distribution where we want to calculate integrals to get a probability. That is what we will do next! The function we will integrate is:

$$g(x) = \frac{1}{\sigma \sqrt{2\pi}} e ^ {-\frac{1}{2} (\frac{x - \mu}{\sigma})^2}$$

Here $$\sigma$$ is the standard deviation and $$\mu$$ is the mean value where the curve is centered about. If we want to know the probability that a random value from this distribution is between two number $$a$$ and $$b$$ we integrate from $$a$$ to $$b$$:

$$P(a < X < b) = \int_a^b g(x) dx$$

Let's implement this function in Nim! But how are we supposed to pass in $$\mu$$ and $$sigma$$ if we only can have one variable, `x`? The answer is the `NumContext`! We can create a `NumContext` and store the values in there and then pass it in:

```nim
var ctx = newNumContext[float]()
# These values correspond to a normal distribution centered around x = 3
# with standard deviation 1.
ctx["sigma"] = 1.0
ctx["mu"] = 3.0
```
As you can see, the context behaves like a table where we use a string as the key. Note though that the type of the `NumContext` must the same as the return-value of your function. So if your `f(x)` has return-type `CoolType`, then you must create a `NumContext[CoolType]` and NOT one with `float`. A NumContext does however always have a `float` storage which can be accessed using the procs `setF` and `getF`. 

Now we can define `g(x)`:

```nim
proc g(x: float, ctx: NumContext[float]): float =
  let sigma = ctx["sigma"]
  let mu = ctx["mu"]
  result = 1 / (sigma * sqrt(2*PI)) * exp(-0.5 * ((x - mu) / sigma) ^ 2)
```

The only integral we can analytically compute is the infinite ones: $$-\infty$$ to $$\mu$$ and to $$\infty$$. The integral over all numbers is 1, which is logical as the probability to find x somewhere is 1. So we can first test this to see if we get the correct answer:

```nim
let I2 = adaptiveGauss(g, -Inf, Inf, ctx = ctx)
echo "I2 = ", I2
```
```nim
I2 = 1.000000000000013
```
That is pretty close to 1! And we can get even closer if we lower then error tolerance from the default `1e-8` to `1e-10`:

```nim
echo adaptiveGauss(g, -Inf, Inf, ctx = ctx, tol = 1e-10)
# 1.0
```
So we can be pretty certain that we have implemented `g(x)` correctly. Let's now do something algebra can't: integrate our function from 0 to 3.5:

```nim
let I3 = adaptiveGauss(g, 0, 3.5, ctx = ctx, tol = 1e-10)
echo I3
# 0.6901125632423831
```
In other words, the probability that it is between 0 and 3.5 is approximately 69%.

# 2. Integrating discrete functions
In this scenario we only know the values of our function `f(x)` at discrete points. Perhaps because we have done some measurements at those points. We will then have a set of points `X` which contains the variable we want to integrate over and `Y` which contains the function value at all those points. In this first example `X` will be points in time and `Y` will be the velocity of a bike at those points in time:

```nim
import numericalnim, stats

var X = @[0.0, 10.0, 20.0, 30.0, 40.0, 50.0, 60.0] # seconds
var Y = @[0.0, 4.0, 6.0, 6.5, 6.4, 6.2, 0.0] # meters/second
```
Just by looking at the data it seems like someone started from standing still and then biked for 1 minute before stopping. How long have this person traveled in this time? Just by looking at this data we can never know for certain but we can get an approximation. The first and simplest way is to take the mean of the velocity and use the formula $$s = vt$$ to get a first approximation:

```nim
let s1 = mean(Y) * 60
echo "Mean method: ", s1, " meters"
# Mean method: 249.4285714285714 meters
```
So we now know that it's in the ballpark of 250m but we can of course do better than just assuming a constant velocity. Enter `trapz`! It approximates our points with straight lines between them an integrates them:

```nim
let s2 = trapz(Y, X)
echo "Trapz: ", s2, " meters"
# Trapz: 291.0 meters
```
That's quite a difference so we see the mean method has its limitations. We can do even better though by using `simpson` which approximates the function as a second degree polynomial instead:

```nim
let s3 = simpson(Y, X)
echo "Simpson: ", s3, " meters"
# Simpson: 305.3333333333334 meters
```
Once again we are getting a slightly different answer which should be more accurate as it approximates the velocity as smoother than `trapz` does. There is one more method that can handle discrete integrands, `romberg`, but it does ONLY work if we have `N = 2^k + 1` equally spaced points (3, 5, 9, 17, 33, 65, 129 etc). In our case we have 7 equally spaced points which does NOT WORK. 

There is a trick we can do though to try and get an even better approximation. That is to interpolate our data using cubic spline which will give us a piecewise 3rd degree polynomial so we should get even better accuracy. There are two types of splines in NumericalNim: natural cubic splines which only supports floats and Hermite cubic splines which works for any type. The advantage of the natural cubic splines though is that it gives a continuous second derivative while the hermite only gives continuous first derivative (if we doesn't supply it with the derivatives in all points as well. But we don't have those here). I will use the natural cubic splines to get the smoothest possible approximation:

```nim
let spline = newCubicSpline(X, Y)
# If you want to try out the hermite spline instead:
# let spline2 = newHermiteSpline(X, Y) 
```

We can evaluate it at a certain point by using the `eval` proc (and `derivEval` to evaluate the derivative):
```nim
echo spline.eval(5.0)
# 2.167596153846154
```
Or we can turn it into a proc using `toProc`:
```nim
let splineProc = spline.toProc
echo splineProc(5.0)
# 2.167596153846154
```
We can also just pass the spline directly to the integration methods and it will automatically be converted into the right kind of proc:
```nim
let s4 = adaptiveGauss(spline, 0.0, 60.0)
echo "Gauss: ", s4, " meters"
# Gauss: 301.2115384615747 meters
```
Here we used the `adaptiveGauss` method as we with the spline "know" all the function values between 0 and 60. I think we can with a reasonable certainty say that the biker has traveled about 300 meters during this minute. As you can see there are many ways to approach this problem and we can't really know for certain exactly how long this biker have traveled, not only because of the lack of points but also because the time and velocity probably (most definitely!) also has a measurement uncertainty. All we can do is to reduce this uncertainty by taking more measurements with better accuracy and as we do so, all methods should converge towards the "correct" solution. But we will never reach it with 100% certainty, only within a reasonable tolerance. In this case that tolerance may be plus minus 10 meters, which might not matter for the average person but in a competition where it's milliseconds that differ between the contestants it's nowhere near enough. All is relative, know your situation and it's requirements!
# 3. Cumulative integration
There are scenarios where you could want to not only calculate a single integral but many subintegrals. For example if we have the velocity as a function of time. With one integral we can calculate how long we have traveled in a certain amount of time. But what if we want the velocity as a function of time? Then we have to calculate many integrals, one for every point in time we want. NumericalNim has specialized methods to deal with these kinds of integrals which are called cumulative integrals. The available methods are `cumtrapz` and `cumsimpson`. They work for both discrete and continuous functions, but only the discrete case will be handled here. The continuous one works the same way only that you give it a function instead of Y-values and you give it the X-values you want it to calculate the integral at. Let's get started with the discrete case!

We are given the velocities V at the time points t (same as in section 2 above):

```nim
import numericalnim

var t = @[0.0, 10.0, 20.0, 30.0, 40.0, 50.0, 60.0] # seconds
var V = @[0.0, 4.0, 6.0, 6.5, 6.4, 6.2, 0.0] # meters/second
```
Now we want to know not just the total distance, but the distance the biker has traveled at every point in t. For that we can start by using `cumtrapz`:

```nim
let dist1 = cumtrapz(V, t)
echo "Trapz: ", dist1
# Trapz: @[0.0, 20.0, 70.0, 132.5, 197.0, 260.0, 291.0]
```
We can see that after 30 seconds we have traveled 132.5 meters. Now let's compare that to `cumsimpson`:

```nim
let dist2 = cumsimpson(V, t)
echo "Simpson: ", dist2
# Simpson: @[0.0, 21.66666666666667, 73.33333333333334, 136.3333333333333, 201.3333333333333, 269.3333333333334, 305.3333333333334]
```
Here we instead have traveled 136.3 meters after 30 seconds which isn't much of a difference, but probably closer to the real value. That's neat and all but can we use this for something else? Yes! Say that we just measure the acceleration of the biker instead, then we can get the velocity by this method. And if we repeat it again, we can get the distance as well! So just by measuring the acceleration of something we can get an approximation for the distance it has traveled. A simple example is a free falling object with constant acceleration 9.82 (here where I live). We do of course ignore air resistance or else it would be more of a differential equation instead. We get these measurements from the accelerometer:

```nim
var t2 = @[0.0, 1.0, 2.0, 3.0, 4.0, 5.0]
var a = @[9.82, 9.82, 9.82, 9.82, 9.82, 0.0]
```
As we can see by looking at the last acceleration we seem to have hit the ground. So what we will try to calculate from how high up the object was dropped from. The first step is to get the velocity as a function of time:

```nim
var v = cumsimpson(a, t2)
echo "Velocity: ", v
# Velocity: @[0.0, 9.82, 19.64, 29.46, 39.28, 45.00833333333333]
```
Now we see that we started at velocity 0, what if we had given it another starting velocity instead, like 1 m/s upwards? That's where the constant after the integral come in. You know that one you always forget about, that's the one! You would then have to add it to all elements in `v`:

```nim
import sequtils
let v0 = -1.0 # minus sign because its in the opposite direction
let v_throw = v.mapIt(it + v0)
echo "Adjusted Velocity: ", v_throw
```
But this isn't what we are looking for as we want to drop it from a certain height so we will keep on using `v` instead. Now that we have `v` we can get the distance it has traveled and in this case we only care about the total distance so we don't use a cumulative method:

```nim
let h = simpson(v, t2)
echo "Height: ", h, " meters"
# Height: 121.0451388888889 meters
```
Roughly 121 meters, that's a pretty high building! Or a really low plane! The problem we have here is that we don't exactly know when the object hit the ground, just that it happened sometime between 4 and 5 seconds. We can solve this analytically and check if 121 meters lies within the possible heights. The formula is $$h = \frac{gt^2}{2}$$. By putting in t = 4 we get 78.56 meters and with t = 5 we get 122.75 meters. This shows that because of our poor resolution in our measurements, just one per second. We have a interval spanning over 40 meters so we can't expect the integration method to do better than that. In this case it seems like the approximation it had to do put the impact closer to 5 than 4 seconds, which might or might not be correct. 

Numerical integration is effective to calculate integrals when we can't do it symbolically. But there is always a certain amount of error because of the approximations that has to be done. But those start to become noticeable first when your data get reliable. Mathematical functions are the most exact things we have so those are well suited for numerical integration and you don't have to think about it much. But when you want to integrate something that isn't perfect you must always consider the errors, the better your data is, the better your integration will be. Don't just blindly trust what the integrator spits out without giving a little bit of thought to what conditions it is working under. 

# Conclusion
I hope you enjoy this tutorial :D If you have any questions, don't hesitate to ask them. The best place to ask questions is either in the issue tracker on NumericalNim's <a href="https://github.com/hugogranstrom/numericalnim">github</a> or on the Gitter channel <a href="https://gitter.im/NumericalNim/community">here</a>. 

Have an awesome day and integrate to your heart's content! :D