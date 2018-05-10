---
layout: post
title: Math Codified - Derivatives
categories: mathcodified
---

This equation is probably somewhat familiar to most people who have studied a bit of calculus: $$ f'(x) = \lim_{h \to 0}\frac{f(x+h)-f(x)}{h} $$. Today we are breaking it down and converting it to code to really see how this works, when the math notation has been stripped off.  

To start with, how do we calculate the gradient (slope) of a function? We take the difference in y divided by the difference in x: $$ \frac{\Delta y}{\Delta x} = \frac{y_2-y_1}{x_2-x_1} = \frac{f(x_2)-f(x_1)}{x_2-x_1}$$

If our function is a straight line this will work no matter which $$ x_2 $$ and $$ x_1 $$ we choose (as long as $$ x_2 > x_1 $$, otherwise we get the wrong sign). If our function instead for example is a quadratic equation, we will not get the the gradient in a point, but the average gradient in a segment of the function. The smaller we make the distance between $$ x_2 $$ and $$ x_1 $$