# Reinforcement Learning (3/5)
## Brief Overview: 
Last time, I went through and derived the quality function and discussed optimal policies. I then showed that we could derive the optimal policy and optimal value function given the optimal quality function.

This time I want to: **build up a framework that will allow us to compute the optimal value function!** 

In this post, I want to take a fun detour that will be understood only after we get to blog number four. I want to make this post first because it will hopefully remove a lot of mystery from all of the reinforcement learning you see on the web!

***Disclaimer:*** There's quite a bit of math going on, and it might seem really unrelated. I promise that it is ***very*** important to just stick with it until the end of the post.
### Objectives:
This time I will:

 1. state *Banach's fixed point theorem.*
 2. demonstrate how this theorem can be used to show why *Newton's Method* converges in one specific case.
 3. allude to how all of this *connects to MDPs*!
## Assumptions:
I actually tried writing this post with a complete set of definitions and theorems, and I felt like I was getting way too caught up in the weeds. I looked over the post and realized that instead of helping build intuitions, I got caught up in symbol pushing. So, I've decided to create a little section where you can go look into the prerequisite material before moving on!

Here is a list of materials you can use to brush up on what we will discuss:

 - Metric Spaces
 - Vector Spaces
 - Normed Spaces

## Math Background:
***Define*** (Fixed point):
Let $f:X \rightarrow X$ be a map from a set $X$ to itself. $x \in X$ is called a ***fixed point*** of $f$ if $f(x)=x$.

***Banach's Fixed Point Theorem:*** 
Let $(X,d)$ be a complete metric space and $f:X \rightarrow X$ be a map such that $d(f(x),f(y)) \leq qd(x,y)$ for $q \in [0,1)$ and $x, y \in X$. (Such a map is called a ***contraction***.) Then, $f$ ***has a unique fixed point*** in $X$. 

Furthermore, the fixed point is computed as follows:
$$(x_0,f(x_0,f(f(x_0),...,)$$
This sequence is guaranteed to converge to the fixed point!

## A Trivial Example:
Let's be a bit more concrete about what this ***very powerful*** theorem is saying. First, take a complete metric space that we know, $(\mathbb{R},|.|)$ the real numbers with absolute value. Maps in this space are the familiar functions $f:\mathbb{R} \rightarrow \mathbb{R}$. 

***Example*** (Constant Function):
$$f(x)=2$$
This is defined on all of $\mathbb{R}$ and clearly satisfies: $$|f(x)-f(y)| = |2-2|=0 \leq q|x-y|$$ for all $q \in [0,1)$.

***Discussion:***
So what's happening here? The map is taking every point of $\mathbb{R}$ and mapping it to the value $2$. So, the distance between any two points after applying the map is $0$.

***Fixed Point:***
Let's think about how to find: $$f(x)=x$$  
Graphically, it's beneficial to think of this as finding the intersection between the function $f(x)$ with the line of slope $1$ and y-intercept $0$.

## A Cooler Example:
The next example comes in the from of ***Newton's Method*** for computing the square root of a real number.

***Formula***:
We know that $f(x)=2-x^2$ has the solutions: $\pm \sqrt2$. What's not so obvious is how we might compute the actual value of $\sqrt2$. Luckily, there is a famous iterative approach that will do this for us:
$$x_{n+1}=\frac{1}{2}(x_n+\frac{2}{x_n})$$

***Fixed Point:***
***Question 1***: (What is the space we are working in?)

Before we work through this, we need to understand the space we are working in. First, notice that this function is not defined at $x_n=0$. Also, this is an odd function that is symmetric about the y-axis. So, we can restrict ourselves to working with $x_n \in (0,\infin)$. However, this is not a complete metric space because we can construct sequences with $x_n=0$ as a limit. 

***Question 2***: (Is this really a contraction?)
Let's check:
$$|f(x)-f(y)| = |\frac{1}{2}(x+\frac{2}{x}) - \frac{1}{2}(y+\frac{2}{y})| = \frac{1}{2}|x-y+\frac{2y-2x}{xy}|$$

$$ = \frac{1}{2}|x-y||1-\frac{2}{xy}| $$


Here's where we should go back to the unanswered question: *What is our domain?*

We need to end up with $$ |f(x)-f(y)| \leq q|x-y|$$
where $q \in [0,1)$.

Therefore we need: $$|1-\frac{2}{xy}| \leq 1$$
If we restrict ourselves to $x,y \in [t,\infin)$ where $t > 0$, we get that:
$$1-\frac{2}{t^2} \leq 1-\frac{2}{xy} \leq 1$$

All that's left to do is to guarantee that:
$$ -1 \leq 1-\frac{2}{t^2}$$

$$\frac{2}{t^2} \leq 2$$

$$ 1 \leq t^2$$ 

Hence, all we need to do is guarantee that $t \geq 1$ and we can assure that $[t,\infin)$ is complete!

***Question 3***: (What is my fixed point?)
Now we know that $$f:[1,\infin) \rightarrow \mathbb{R}$$

$$ f(x)=\frac{1}{2}(x+\frac{2}{x})$$

is ***a contraction on a complete metric space***!

Therefore, it has a unique fixed point:

$$x=\frac{1}{2}(x+\frac{2}{x})$$

$$2x=x+\frac{2}{x})$$

$$  2x^2 = x^2 + 2$$

$$ 0 = -x^2  +2$$

$$ 0 = 2 - x^2$$

Voila! We can see that we have a fixed point $\sqrt2$ when we only look at $x \in [1,\infin)$. This can be seen graphically to be the point where the line of slope $1$ and y-intercept $0$ intersects $f$!

## Discussion:
We just gave a proof for why Newton's method converges in this specific case. This is very interesting because we can now define an iterative process (an algorithm) to compute some unknown numerical value. ***Furthermore, if we condition our problem appropriately, we can guarantee that the algorithm converges to a solution!***

## Back to Reinforcement Learning:

Let's inspect this formula once more:
$$V^{\pi}(s)= \sum_{s' \in S}[P(s'|s, \pi_A(a|s))(R(s,\pi_A(a|s),s')+V^{\pi}(s'))]$$

***Claim***:
We can condition our formulation of reinforcement learning in such a way that ***computing the optimal value function is the same as finding the fixed point in some normed vector space***.

***Formulation***:

***Step 1*** (Define a Map & a Complete Normed Space):

1. The space we will work in is: $$ B(S)=\{V:S \rightarrow \mathbb{R}; ||V||_{\infin} < +\infin\}$$
2. The map is:
$$T^{\pi}:B(S) \rightarrow B(S)$$

$$ (T^{\pi}V)(s)= \sum_{s' \in S}[P(s'|s, \pi_A(a|s))(R(s,\pi_A(a|s),s')+V^{\pi}(s'))]$$

We are operating under ***very specific assumptions***:

 1. $B(S) \neq \empty$
 2. $(B(S),||.||_{\infin})$ is a *complete normed space*
 3. $T^{\pi}$ is *well-defined*

Let's first discuss $B(S)$:

 - This is the set of all bounded value functions with respect to $||.||_{\infin}$ 
 - Notice that the value functions we have worked with so far ***are not guaranteed to be in $B(S)$*** 

Let's dicuss $T^{\pi}$:

 - Notice all that we've done here is introduced some map that takes a value function $V^{\pi}(s)$ and turns it into the same formula from before.
 - This ***is weird***! The ***reason*** why we go through the trouble of defining this map is because we can write down this relationship:$$ T^{\pi}V^{\pi}(s)=V^{\pi}(s)$$
 - I hope it's clear that ***nothing spectacular has happened***! We are merely observing that applying this map to our value function gives us back the same value function.

***Step 2*** (Show $T^{\pi}$ is a Contraction):

Let's leave this step for the next post!

***Question***: Why?
***Answer***: As it currently stands, $T^{\pi}$ is ***not guaranteed to be a contraction***!!

We are finally at the stage where we can ***truly*** understand why MDPs in reinforcement learning are framed in the way they are!

## Conclusion
To be complete, I want to ***mention what the next step would have been if we could guarantee Step 2***.

***Step 3*** (Apply Banach's Fixed Point Theorem):

Here's the reason I started off with the ***Newton's Method*** example. Now that ***we have a formula that satisfies all of the criteria of Banach's Fixed Point Theorem***, we can build an iterative algorithm that will converge to the fixed point $V^{\pi}$ as follows:
Note: $V^{\pi}$ is a ***point***  because $V^{\pi}$ is an ***element*** of $B(S)$

 1. Start with some policy $V^0$
 2. Apply $T^\pi$ to $V^0$ until convergence!$$ (V^0,T^{\pi}V^0,T^{\pi}T^{\pi}V^0,...)$$
 3. This sequence is ***guaranteed to converge*** to $V^{\pi}$!!!

## Conclusion:
Here's the point: ***The current formulation of MDPs that I have introduced is hard to work with!***

If we can guarantee a few more properties of our MDP, then we can leverage this very useful tool to finally develop an algorithm that can help us to compute value functions.

***Question***: What should we change?
***Answer***: I will leave it as an exercise to you. I plan on answering this question thoroughly in the next post!


## Next Time:
I will:

 - reformulate our definitions to satisfy that $T^{\pi}$ is a contraction.
 - describe the Value Iteration algorithm.
 - give a full demo using Python.

## References:
https://spinningup.openai.com/en/latest/spinningup/keypapers.html
[https://sites.ualberta.ca/~szepesva/papers/RLAlgsInMDPs.pdf](https://sites.ualberta.ca/~szepesva/papers/RLAlgsInMDPs.pdf)
[https://www.cs.ubc.ca/~kevinlb/teaching/cs322%20-%202008-9/Lectures/DT4.pdf](https://www.cs.ubc.ca/~kevinlb/teaching/cs322%20-%202008-9/Lectures/DT4.pdf)
[https://kconrad.math.uconn.edu/blurbs/analysis/contraction.pdf](https://kconrad.math.uconn.edu/blurbs/analysis/contraction.pdf)
https://www.cs.cmu.edu/~arielpro/15381f16/c_slides/781f16-11.pdf
