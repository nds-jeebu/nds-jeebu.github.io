# Reinforcement Learning (4/5)
## Brief Overview: 
Last time, I went on somewhat of a digression talking about *Banach's Fixed Point Theorem*. The real reason I did this was to demonstrate ***why everyone formulates MDPs in the 'standard' way***.

You can probably tell by my tone here that I never found it obvious why certain conditions were being enforced and why certain assumptions had to hold.

In this post, I will show you where a lot of those assumptions actually come from and why they are important, at least for now.

***Disclaimer:*** This post will make very little sense without the previous post in the series. So definitely, make sure to check that out! 
### Objectives:
This time I will:

 1. reformulate the MDP.
 2. introduce some more terminology.
 3. complete the proof that $T^{\pi}$ is a contraction.
 4. present  *Value Iteration*.
 5. prove its convergence.

## Reformulating:
I was pretty vague about all of this last time and made sure to point out that we did not have the proper conditions to use the fixed point theorem. ***Here, I will make everything clear!***

***Define*** (Pseudo-Bellman Operator):
$$ T^{\pi}: B(S) \rightarrow B(S)$$

$$ (T^{\pi}V)(s)= \sum_{s' \in S}[P(s'|s, \pi_A(a|s))(R(s,\pi_A(a|s),s')+V^{\pi}(s'))]$$

***Disclaimer***: 'Psuedo' is included for a very legitimate reason! Namely, this is not the real Bellman Operator. Furthermore, an ***operator*** is just a map. For more information check [this]([https://en.wikipedia.org/wiki/Operator_(mathematics)](https://en.wikipedia.org/wiki/Operator_(mathematics))) out.

***Define*** (Space of Bounded Value Functions):
$$ B(S)=\{V:S \rightarrow \mathbb{R}; ||V||_{\infin} < +\infin\}$$

Here's where our problems begin. ***As it stands, we cannot currently guarantee our value functions are bounded!*** Furthermore, we need to guarantee that the space is ***complete***.

***Question***: How do we fix this?
***Answer***: We do this in two parts:

 1. Restrict the possible rewards such that: $$ 0 \leq R(s_{t-1},a_{t-1},s_t) \leq R_{max}$$
 $R_{max} \in \R$.
 2. Introduce a ***discount*** to our value functions:$$ \gamma \in [0,1)$$
 3. To ***ensure the space is complete***, we can restrict ourselves by assuming:

$$ |A| < +\infin$$
 
$$|S| < +\infin $$

## Updated Value Function
Putting these two together we have a new value function!

***Define*** (Bounded, Discounted Value Function):
$$V^{\pi}(s) = \mathbb{E_{\pi}}[\sum_{t=0}^{\infin}\gamma^tr_{t+1}|s_0=s]$$

We can work through the same steps from post number 2 to get:$$V^{\pi}(s)= \sum_{s' \in S}[P(s'|s, \pi_A(a|s))(R(s,\pi_A(a|s),s')+\gamma V^{\pi}(s'))]$$

***Question***: Okay, what's different about this both mathematically and intuitively?
***Answer***:

 1. Mathematically: the value function is guaranteed to be ***bounded and finite***! We will exactly how later in the post.
 2. ***Our agent now cares more about immediate rewards than future rewards.***

***Discussion*** (Discounting):

Personally, I stand by the fact that discounts are part of the formulation out of ***mathematical convenience***. This isn't really me taking shots at the formulation. Instead, this is me showing you where they really come from. 

Since we kind of have to use a discount, you will see why in the next section, it is important to make sure that it intuitively makes sense. I want to reiterate:

> ***Our agent now cares more about immediate rewards than future rewards.***

***Question***: Why?
***Answer***: Think of what happens at different time steps:

 - at time $t=0$, $\gamma^0=1$, so the reward is the same.
 - at time $t=1, \gamma^1 <1$, so the reward is less than it would without the discount.
 - continue  this and we get $\lim_{t \rightarrow \infin} \gamma^t=0$, so distant future rewards are worth very little!

This is pretty reasonable because I can see how an agent doesn't really care about what's happening infinitely into the future.

## Updated Definitions:

Since the ***value function is the foundation*** for all of the other formulas, I would like to take this section to re-formalize the rest of our definitions with our new, bounded and discounted value function.

***Define*** (Optimal Bounded, Discounted Value Function):

$$V^{*}(s)= \sum_{s' \in S}[P(s'|s, \pi^*_A(a|s))(R(s,\pi^*_A(a|s),s')+\gamma V^{*}(s'))]$$

***Define*** (Bounded, Discounted Quality Function):
 $$Q^{\pi}(s,a)= \sum_{s' \in S}[P(s'|s, a)(R(s,a,s')+\gamma V^{\pi}(s'))]$$

***Define*** (Optimal Bounded, Discounted Quality Function):
 $$Q^{*}(s,a)= \sum_{s' \in S}[P(s'|s, a)(R(s,a,s')+\gamma V^{*}(s'))]$$

The relationships between the optimal policies, values and qualities still hold as follows:
$$ \pi_A^*(a|s) = argmax_aQ^*(s,a)$$

$$V^*(s) = max_aQ^*(s,a) $$

***Comment***: Notice how nothing has really changed except that the discount pops into the formulas in front of the value function. Pretty convenient. Actually, this is why I decided to leave it out for so long!

## Completing the Proof:
Now, we are ready to get back to the proof.

First, let's define the ***actual*** Bellman Operator:

***Define*** (Bellman Operator):
$$ T^{\pi}: B(S) \rightarrow B(S)$$

$$ (T^{\pi}V)(s)= \sum_{s' \in S}[P(s'|s, \pi_A(a|s))(R(s,\pi_A(a|s),s')+\gamma V^{\pi}(s'))]$$

We can finally ***show that the Bellman Operator is a  contraction*** as follows:

Take $U, V \in B(S)$ and we get:
$$ ||T^{\pi}U-T^{\pi}V||_{\infin}= \sup_{s \in S}|\sum_{s' \in S}[P(s'|s, \pi_A(a|s))(R(s,\pi_A(a|s),s')+\gamma U(s'))] - \\ \sum_{s' \in S}[P(s'|s, \pi_A(a|s))(R(s,\pi_A(a|s),s')+\gamma V(s'))]|$$ 

$$ = \sup_{s \in S}|\sum_{s' \in S}[P(s'|s, \pi_A(a|s))\gamma U(s')] -  \sum_{s' \in S}[P(s'|s, \pi_A(a|s))\gamma V(s')]|$$

$$= \gamma \sup_{s \in S}|\sum_{s' \in S}[P(s'|s, \pi_A(a|s))(U(s')-V(s'))] $$
Apply the triangle inequality:
$$ \leq \gamma \sup_{s \in S}\sum_{s' \in S}P(s'|s, \pi_A(a|s))|(U(s')-V(s'))| $$
Observe that $|U(s')-V(s')| \leq ||U-V||_{\infin}$ since the infinity norm is defined as the largest different in these two functions on their domain!
$$ \leq \gamma \sup_{s \in S}\sum_{s' \in S}P(s'|s, \pi_A(a|s))||U-V||_{\infin}$$
Now, there is no more dependence on $s'$ so we get:
$$ = \gamma ||U-V||_{\infin}$$


Let's just rewrite this final result as:
$$||T^{\pi}U-T^{\pi}V||_{\infin} \leq \gamma ||U-V||_{\infin}$$
***WOH!*** Recall how we defined $\gamma \in [0,1)$, which is ***exactly*** what it needs to be to ensure that $T^{\pi}$ ***is a contraction***.

## Banach's Fixed Point Theorem:
Alright, it took a bit of grinding, but we finally have that the ***Bellman Operator is a contraction defined on a complete, normed space.***

Recall the relationship:
$$ (T^{\pi}V^{\pi})(s)=V^{\pi}(s)$$

***The fixed point of the Bellman Operator is the Value function $V^{\pi}$*** !

We now define a procedure to compute $V^{\pi}(s)$ as follows:
 1. Start with some an arbitrary $V^0$
 2. Apply $T^\pi$ to $V^0$ until convergence!$$ (V^0,T^{\pi}V^0,T^{\pi}T^{\pi}V^0,...)$$
 3. This sequence is ***guaranteed to converge*** to $V^{\pi}$!!!

## Value Iteration (Finally):
Alright, so far we've figured out how to start with an arbitrary policy $V^0$ and have it converge to $V^{\pi}$. The problem is that $V^{\pi}$ really isn't guaranteeing that our agent is doing anything intelligible. ***What we really want is: an algorithm that starts with an arbitrary policy and converges to $V^*$.***

***Recall***: 
$$V^*(s) = max_aQ^*(s,a) $$
***Claim***: We can use this relationship to create another operator with really powerful properties.

Here's how (noting that $\sup$ and $max$ are quite analogous):

 1. ***Define*** (Bellman Optimality Operator):
$$ T^{*}: B(S) \rightarrow B(S)$$

$$ (T^{*}V)(s)= \sup_{a \in A}\{ \sum_{s' \in S}[P(s'|s, a)(R(s,a,s')+\gamma V(s'))]\}$$

2. ***Claim***: The Bellman Optimality Operator is ***a contraction on a complete normed space.***  (I recommend trying to prove this, the proof is very similar to the previous proof.)
3. ***Observe*** (The Fixed Point): $$V^*(s)=(T^*V^*)(s)$$
4. Apply the same procedure as the previous section. For arbitrary $V^0$ $$ (V^0, T^*V^0,T^*T^*V^0,...)$$ 
converges to $V^*$!!!

***Discussion***:

It helps me to rewrite the formula above in a way that parallels *Newton's Method*. We are iteratively trying to get to a solution. 

***Define*** (Value Iteration):
$$ V^{k+1}=T^*V^k ,\ \ k \geq 0$$

***Question***: How many iterations do we need to perform?
***Answer***: We actually get to decide this based on our task as follows:

 - ***Recall***: Since we are dealing with a contraction for $U,V \in B(S)$, $$ ||T^*U-T^*V||_{\infin} \leq \gamma||U-V||_{\infin}$$
 - Then we can see for $k$ iterations $V^k$ and optimal value function, $V^{*}$: $$ ||V^{k}-V^*||_{\infin} = ||T^*V^{k-1}-T^*V^*||_{\infin} \leq \gamma||V^{k-1}-V^*||_{\infin} $$
 - Keep unfolding and we eventually get: $$ ||V^{k}-V^*||_{\infin} \leq ... \leq||V^1-V^*||_{\infin} = ||T^*V^0-T^*V^*||_{\infin} \leq \gamma^k||V^0-V^*||_{\infin} $$
 - AHA! We have a guarantee that for $k$ iterations: $$ ||V^{k}-V^*||_{\infin} \leq \gamma^k||V^0-V^*||_{\infin} $$
 - Let's see if we can bound this even further. Recall we bounded our reward to $R_{max}$: $$V^{\pi}(s) = \mathbb{E_{\pi}}[\sum_{t=0}^{\infin}\gamma^t r_{t+1}|s_0=s] \leq \mathbb{E_{\pi}}[\sum_{t=0}^{\infin}\gamma^t R_{max}|s_0=s] = \sum_{t=0}^{\infin}\gamma^t R_{max}$$
 - Also, recall $\gamma \in [0,1)$, so we can ***leverage the geometric series***: $$ \sum_{t=0}^{\infin}\gamma^t R_{max} = \frac{R_{max}}{1-\gamma}$$
 - Hence, we get for any of the policies under our consideration: $$V^{\pi}(s) \leq  \frac{R_{max}}{1-\gamma}$$
 - Finally, apply the triangle inequality to get:$$ ||V^{k}-V^*||_{\infin} \leq \gamma^k||V^0-V^*||_{\infin} \leq \gamma^k(||V^0||_{\infin} + ||V^*||_{\infin}) \leq \frac{2R_{max}\gamma^k}{1-\gamma}$$
 
 ***Discussion*** (Finding how many iterations we need):
 
The cool part is that this ***let's the user decide on how close the estimated value function is to the optimal value function***. Let's say that we want the ***difference to be*** $\epsilon \in (0,+\infin)$. Then all we have to do is ***solve for $k$*** : $$\frac{2R_{max}\gamma^k}{1-\gamma}=\epsilon$$ 

$$ \gamma^k = \frac{\epsilon (1-\gamma)}{2R_{max}}$$

$$k=\log_{\gamma}(\frac{\epsilon (1-\gamma)}{2R_{max}}) $$
Using common properties of logarithms, we get:
$$k=\ln(\frac{\epsilon (1-\gamma)}{2R_{max}})/\ln(\gamma)$$

***Summary*** (Given discount $\gamma$ and desired error $\epsilon$):

***The number of iterations needed for convergence of policy iteration is:*** $$ k=cieling[\frac{\ln\frac{\epsilon (1-\gamma}{2R_{max}})}{\ln\gamma}]$$

Here's a link of 

## Conclusion:
There you have it! We finally have a technique for solving a subset of MDPs. I've made quite a bit of assumptions here and would really like to illustrate that ***this is only a small subset of of possible reinforcement learning problems.***

However, this is the foundation that we can use to move onto more complicated algorithms that are coming up in the next part of the series!

## Next Time:
I will:

 - present *Policy Iteration* and compare it with *Value Iteration*.
 - give a worked out example of both algorithms.

## References:
https://spinningup.openai.com/en/latest/spinningup/keypapers.html
[https://sites.ualberta.ca/~szepesva/papers/RLAlgsInMDPs.pdf](https://sites.ualberta.ca/~szepesva/papers/RLAlgsInMDPs.pdf)
[https://www.cs.ubc.ca/~kevinlb/teaching/cs322%20-%202008-9/Lectures/DT4.pdf](https://www.cs.ubc.ca/~kevinlb/teaching/cs322%20-%202008-9/Lectures/DT4.pdf)
[https://kconrad.math.uconn.edu/blurbs/analysis/contraction.pdf](https://kconrad.math.uconn.edu/blurbs/analysis/contraction.pdf)
https://www.cs.cmu.edu/~arielpro/15381f16/c_slides/781f16-11.pdf
https://cs.uwaterloo.ca/~ppoupart/teaching/cs886-spring13/slides/cs886-module6-value-iteration.pdf

