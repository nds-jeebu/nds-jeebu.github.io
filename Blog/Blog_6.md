# Monte Carlo Reinforcement Learning
## Brief Overview: 
In the previous series, I formulated MDPs. I tried to be very clear why certain assumptions were made and what their restrictions were. In this post and future posts, I will modify some of those assumptions in an effort to create a much more realistic set of tools that we can apply to more interesting types of problems. 

As a start to this approach, I want to discuss Monte Carlo based approaches. I'll go through a few key approaches and explain why they are useful and why they work!
### Objectives:
This time I will:

 1. describe properties of a *Monte Carlo Estimator*.
 2. present several algorithms. 

## Monte Carlo Estimator:
Simply put, ***Monte Carlo a technique used to approximate expectations.***

***Definition*** (Expectation):
Suppose $P(x)$ is a continuous probability density function on a set $X$. Then the expected value of a function $f(x)$ on the set $X$ is: $$ I = \mathbb{E}_{x \sim P}[f(x)] = \int_{x \in X}f(x)P(x)dx$$
In the discrete case we have: $$I = \mathbb{E}_{x \sim P}[f(x)] = \sum_{x \in X}f(x)P(x)$$

(Note: let's assume that we are working in the discrete situation, it is actually pretty easy going back and forth between the two.)

***Definition*** (Mote Carlo Estimator):
Suppose we have $N$ samples from $X$, $(x_1,x_2,...,x_N)$. The Monte Carlo Estimate for $\mathbb{E}_{x \sim P}[f(x)]$ is: $$\hat{I}=\frac{1}{N}\sum_{i=1}^Nf(x_i)$$

***This estimator is just sample mean!***

***Definition*** (Unbiased Estimator):
Given some probability density function $P(x)$ on $X$. We say an estimator $\hat{g}$ of a parameter $g$ is ***unbiased*** if: $$\mathbb{E}_{x \sim P}[\hat{g}]=g$$
 In other words, on average, the estimator $\hat{g}$ achieves the value $g$. 

***Observation*** (Monte Carlo Estimator is Unbiased):
$$\mathbb{E}_{x \sim P}[\hat{I}]=\mathbb{E}_{x \sim P}[\frac{1}{N}\sum_{i=1}^Nf(x_i)]=\frac{1}{N}\mathbb{E}_{x \sim P}[\sum_{i=1}^Nf(x_i)]= \frac{1}{N}N\mathbb{E}_{x \sim P}[f(x)]$$

$$\mathbb{E}_{x \sim P}[\hat{I}] = \mathbb{E}_{x \sim P}[f(x)]$$

It's also good to know the variance of the estimator. This will give us an indication of how many samples we should use when constructing estimates.

***Observer*** (Variance of Monte Carlo Estimator):
$$\text{Var}[\hat{I}]=\text{Var}[\frac{1}{N}\sum_{i=1}^Nf(x_i)]=\frac{1}{N^2}\text{Var}[\sum_{i=1}^Nf(x_i)]=\frac{1}{N}\text{Var}[f(x)]$$

Where $$\text{Var}[f(x)]=\mathbb{E}_{x \sim P}[(f(x)-\mathbb{E}_{x \sim P}[f(x)])^2]$$

***Therefore, the variance of our estimator falls off as: $\frac{1}{N}$!***

## Applied to Reinforcement Learning:
Recall that our reinforcement learning agent wanted to maximize its total expected, discounted future reward. The reason we introduced the discount was so that we could ensure the infinite sum of the rewards would converge. 

We would like to use the Monte Carlo estimates in our approach. Therefore, we need to ensure that the MDP terminates in a finite amount of steps.

Therefore we will deal with trajectories of the form: $s_0,a_0,r_1,s_1,a_1,r_2,...,s_{T-1},a_{T-1},r_{T},s_T$, where $T < +\infin$.

Such MDPS are a ***subset of what are known as episodic MDPs.***

***Definition*** (Episodic MDP):
An MDP is called episodic when there exist terminal states in $S$, such that when the agent enters a terminal state, it stops taking actions. 

Note: Just because an MDP is episodic, does not mean that it terminates! ***Therefore, we need to further assume that our agent always eventually enters a terminal state.***

Furthermore, 

## Estimating Value Functions:
Here we go! The idea we are going for right now is super simple. Recall that the value function is an expected value. Therefore, we can use a Monte Carlo Estimator for the value function at each state.

***Algorithm*** (First-Visit Monte Carlo):

> \# Input:
> $\pi$ : a policy used to construct the estimate
>  
>  \# Initialize: 
>  $V[s] \leftarrow 0$ for each $s \in S$
>  $R[s] \leftarrow [ \ ]$ for each $s \in S$ 
>  repeat:
>> Generate a trajectory following $\pi: s_0,a_0,r_1,s_1,a_1,r_2,...,s_{T-1},a_{T-1},r_{T}$
>> $G \leftarrow 0$
>> For each $t \in [T-1,T-2,...,0]$:
>>> $G \leftarrow \gamma G+r_{t+1}$
>>> If $s_t \notin \{s_0,s_1,...,s_{t-1}\}$:
>>>> Append$($R[$s$], $G)$
>>>>$V[s_t] \leftarrow$ average$($R[$s_t$]$)$

***Discussion***:

 1. Start with some policy $\pi\in \Pi_{stat}$. 
 2. We allow our agent to act according to the policy. It interacts with the environment many times. We get back a bunch of trajectories.
 3. For each trajectory ***find the first occurrence of a state*** and add up all of the rewards that follow it.
 4. Do the same for each state.
 5. Average the rewards for each state over the total number of times the state was encountered.

There are some things to consider here!
