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
{% highlight python %}
def f(x):
    return x**2
{% endhighlight %} 
Next let's define h, the very small difference between $$ x_2 $$ and $$ x_1 $$. We can't set it to 0 because we can't divide by 0. So we choose a very small number instead, namely $$ h = 10^{-6} $$:
{% highlight python %}
h = 10**-6
{% endhighlight %}
Now we have the $$ \lim_{h \to 0} $$ and f(x) part sorted. Now it's time to define our python representation of $$ f(x)' $$:
{% highlight python %}
def derivative(x):
    x2 = x + h
    y2 = f(x2)
    x1 = x
    y1 = f(x1)
    gradient = (y2 - y1)/(h)
    print(gradient)
    return gradient
{% endhighlight %}

If we test to call the derivative function with a x-value we can check if it work. The derivative function of $$ x^2 $$ is $$ 2x $$:
{% highlight python %}
derivative(2)
# returns 4.0000010006480125
{% endhighlight %}
The actual value is 4 but this is a pretty good approximation I would say (it's first at the 6'th decimal it gets it wrong). To get even more accurate results, choose a smaller $$ h $$.

That was it. I hope you enjoyed this and learned something new. My hope is that if you have experience in programming but you didn't fully understand derivatives in school, that article helped you grasp it better. 
