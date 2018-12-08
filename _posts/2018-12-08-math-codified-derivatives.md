---
layout: post
title: Math Codified - Derivatives
categories: [mathcodified, math]
---

This equation is probably somewhat familiar to most people who have studied a bit of calculus: $$ f'(x) = \lim_{h \to 0}\frac{f(x+h)-f(x)}{h} $$. Today we are breaking it down and converting it to code to really see how this works, when the math notation has been stripped off.  

To start with, how do we calculate the gradient (slope) of a function? We take the difference in y divided by the difference in x: $$ \frac{\Delta y}{\Delta x} = \frac{y_2-y_1}{x_2-x_1} = \frac{f(x_2)-f(x_1)}{x_2-x_1}$$

If our function is a straight line this will work no matter which $$ x_2 $$ and $$ x_1 $$ we choose (as long as $$ x_2 > x_1 $$, otherwise we get the wrong sign). If our function instead for example is a quadratic equation, we will not get the the gradient in a point, but the average gradient in a segment of the function. The smaller we make the distance between $$ x_2 $$ and $$ x_1 $$ the better of an approximation it will be for the function's gradient at a certain point. This is where the $$ \lim_{h \to 0} $$ comes in. $$ h $$ is the difference between $$ x_2 $$ and $$ x_1 $$ and when it approaches $$ 0 $$ the result becomes the gradient at a single point $$ x $$. The reason we only have a $$ h $$ in the denominator is because the x's cancel out: $$ (x+h)-x = h $$.

Now to the part you probably came here to read. Translating this math expression to a piece of code-cake:

Let's first create a python function for our math-function. I choose the equation $$ f(x)=x^2 $$ 
``` python
def f(x):
    return x**2
``` 
Next let's define h, the very small difference between $$ x_2 $$ and $$ x_1 $$. We can't set it to 0 because we can't divide by 0. So we choose a very small number instead, namely $$ h = 10^{-6} $$:
``` python
h = 10**-6
```
Now we have the $$ \lim_{h \to 0} $$ and f(x) part sorted. Now it's time to define our python representation of $$ f(x)' $$:
``` python
def derivative(x):
    x2 = x + h
    y2 = f(x2)
    x1 = x
    y1 = f(x1)
    gradient = (y2 - y1)/(h)
    print(gradient)
    return gradient
```

If we test to call the derivative function with a x-value we can check if it work. The derivative function of $$ x^2 $$ is $$ 2x $$:
``` python
derivative(2)
# returns 4.0000010006480125
```
The actual value is 4 but this is a pretty good approximation I would say (it's first at the 6'th decimal it gets it wrong). To get even more accurate results, choose a smaller $$ h $$.

## How to choose a good $$ h $$?
You may think that if you choose a really really small $$ h $$ like $$ 10^{-100} $$ that you would get a super good approximation of the derivative. That would have been really nice but sadly normal computers can't handle extremely small numbers at the same time as it handles large one. For example has Python no problems handling $$ 10^{-100} $$ on it's own put if you add it to $$ 1 $$ it is too small comparatively:
``` python
print(1e-100) # same as 1 * 10 ** -100
# prints: 1e-100
print(1 + 1e-100)
# prints: 1.0
```
The reason for this is that computers save numbers with limited precision. Just like we for example can't write $$ 1/3 = 0.3333333333...$$ the entire decimal expansion on paper your computer can't save all numbers exactly. The smallest number you can have between to two numbers is called machine epsilon and is denoted $$ \epsilon_M $$. On a 64-bit computer a float (double precision to be more exact) has a $$ \epsilon_M \approx 2.2 * 10^{-16} $$. If you want to get the machine epsilon on your machine using Python you can get it with numpy:
``` python
import numpy as np
eps = np.finfo(float).eps
```
Now you may wonder why I have brought this up, what does it have to do with choosing a good $$ h $$? The answer to that question is that we don't want our h to be too close to $$ \epsilon_M $$ because then our computer won't be able to see a difference between $$f(x+h)$$ and $$f(x)$$ (remember what the definition of $$ \epsilon_M $$ was?) and then $$f(x+h)-f(x) = 0$$ which we don't want. So how are we supposed to choose $$ h $$ then, what is small enough to get a good approximation while not being to close to $$ \epsilon_M $$. 

$$ h = \sqrt{\epsilon_M} * x $$ happens to be a good choice. Here $$ x $$ is the x-value we want to evaluate the derivative of f(x) at. This works when $$ x \neq 0 $$ because then $$ h = 0 $$ which is problematic. We will have to add a check for that in our function. In Python we can modify our derivate function:

``` python
import numpy as np
def derivative(x):
    if x != 0:
        eps = np.finfo(float).eps
        h = eps**0.5 * x
    else:
        h = 1e-6
    x2 = x + h
    y2 = f(x2)
    x1 = x
    y1 = f(x1)
    gradient = (y2 - y1)/(h)
    print(gradient)
    return gradient
```
Now our function chooses an appropriate $$ h $$ by itself. Cool, right!?

## Are there more accurate methods?
This is a question you may ask yourself. Are there any other, better ways to calculate the derivative of a function? The answer is: Yes there are! Instead of calculating the value of the function in $$ x $$ and $$ x+h $$ we now calculate it in $$ x-h $$ and $$ x+h $$. This gives a $$ h = (x+h) - (x-h) = 2h $$. The formula becomes:
$$ f'(x) = \frac{f(x+h) - f(x-h)}{2h} $$. It doesn't look that different from our old formula but it is actually more accurate. If we write it in Python using everything we have learned so far: 
``` python
import numpy as np
def cool_function(x):
    return np.sin(np.exp(x)) # f(x) = sin(e^x)

def derivative(f, x):
    if x != 0:
        eps = np.finfo(float).eps
        h = eps**0.5 * x
    else:
        h = 1e-6
    x1 = x - h
    x2 = x + h
    y1 = f(x1)
    y2 = f(x2)
    gradient = (y2 - y1)/(2*h)
    return gradient
```

``` python
derivative(cool_function, 3) # calculate the derivative of cool_function in x=3
# returns: 6.600002091377974 
```
The correct value is roughly 6.60000209300594833 so it's a really good approximation. Our first method give the approximation 6.599993718167146 which isn't far off either but it's still 3 orders of magnitude less accurate than our second method. 

It still begs the question: are there even more accurate methods? The answer is: Yes! But I won't cover them in this article. I will leave you with one of them though, and it's up to you to implement it in Python:

$$ f'(x) = \frac{-f(x + 2h) + 8f(x+h) - 8f(x-h) + f(x-2h)}{12h} $$ 

(Note: that this method appears to be more accurate then the previous one when $$ h $$ is bigger then $$ 10^{-6} $$. When it becomes smaller the inaccuracy becomes larger because we are adding more rounding errors together then we are in the others)

That was it. I hope you enjoyed this and learned something new. My hope is that if you have experience in programming but you didn't fully understand derivatives in school, that this article helped you grasp it better.
