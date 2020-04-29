# Reinforcement Learning (2/5)
## Brief Overview: 
Last time, I went through and roughly defined the key idea behind reinforcement learning agent. In short, this agent wants to ***maximize its total expected future reward, $V^{\pi}(s)$ under a policy, $\pi$*** .

### Objectives:
This time I will:

 1. introduce *Markov Decision Processes*.
 2. discuss optimality.
 3. reinspect value functions and derive the *Quality Function*.
 4. use the Quality Function to derive policies.

## Markov Decision Processes
In my previous post, I off-handedly proposed several definitions and went right into describing an agent that wanted to maximize rewards. Before moving on, I want to quickly discuss a few key details of some of those definitions.

### Sequential Decisions
When I introduced the *trajectory:* $s_0,a_0,r_1,s_1,a_1,r_2,....,s_{t-1},a_{t-1},s_t$, I never said anything about decisions. I'm guessing it was kind of obvious that the agent starts in some state $s_0$, takes some action $a_0$, and ends up in the state $s_1$. It repeats this process many times and you wind up with a trajectory. 

Notice that this is a ***sequential decision process*** and there is an implicit assumption built into this process. Namely, that the agent can only  take actions in the set of possible actions $A$. Furthermore, it may make only one action $a$ that has an **immediate reward** $r$. After the action, the agent is left in the next state.

### State Transition Probability
Recall the probability of ending up in state $s_{t+1}$ after taking action $a_t$ while in state $s_t$: $P(s_{t+1}|s_t,a_t)$. I never called it this before, but this distribution represents the ***dynamics*** of the environment.

There are two implicit assumptions built into the environment here.

 1. When an agent takes an action, there is a ***probability*** it ends up in the state $s_{t+1}$.
 2. The probability of ending up in state $s_{t+1}$ ***depends only on the current state and current action***.

### Markov Decision Process
Our agent can make only sequential decisions with this property that the probability of ending up in the next state only depends on the current state. This is exactly what defines a ***Markov Decision Process (MDP)*** .

## Some More Definitions
***So far, I've just claimed that our agent wants to maximize $V^{\pi}(s)$ for every state $s \in S$.*** (I repeat this very intentionally!)

This is the last section where I can set up the problem so I will take the liberty of introducing notation before jumping into more math.

### Optimality
Assume that someone hands you the ***optimal*** policy. This is defined as the policy that will allow us to ***choose the best action*** $a$ no matter what state $s$ we are in. 

***Question:*** How do we quantify it as being the best? 
***Answer:*** The policy should allow our agent to maximize $V^{\pi}(s)$ for every state $s \in S$!

***Define*** (Optimal Policy):
$$\pi^*(s|a)$$

(Note: I'm not saying how this was computed.)

Then, we can simply apply this policy to our value function to yield the following definition.

***Define*** (Optimal Value Function):
$$V^{*}(s) = \mathbb{E_{\pi^*}}[\sum_{t=0}^{\infin}r_{t+1}|s_0=s]$$

##  A Unique Property of Value Functions
I want to take some time to derive a very useful relationship that is hidden in our definition of the value function.

Let's begin to massage the equations to see if we can find it:
$$V^{\pi}(s) = \mathbb{E_{\pi}}[r_1+\sum_{t=1}^{\infin}r_{t+1}|s_0=s]$$

$$ = \mathbb{E_{\pi}}[R(s,\pi_A(a|s),s')+\sum_{t=1}^{\infin}r_{t+1}|s_0=s]$$

$$ = \mathbb{E}[R(s,\pi_A(a|s),s')]+\mathbb{E_{\pi}}[\sum_{t=1}^{\infin}r_{t+1}|s_0=s]$$

$$ = \mathbb{E}[R(s,\pi_A(a|s),s')]+\sum_{s' \in S}[P(s, \pi_A(a|s), s')\mathbb{E_{\pi}}[\sum_{t=1}^{\infin}r_{t+1}|s_0=s, s_1=s']]$$

$$ = \mathbb{E}[R(s,\pi_A(a|s),s')]+\sum_{s' \in S}[P(s'|s, \pi_A(a|s))V^{\pi}(s')]$$

There we have it, we get this pretty beautiful relationship:


$$V^{\pi}(s)= \sum_{s' \in S}[P(s'|s, \pi_A(a|s))(R(s,\pi_A(a|s),s')+V^{\pi}(s'))]$$
## The Quality Function
When reading through the literature you'll often see the *Quality Function* thrown around all over the place. I claim that we've already seen it, but it was hiding in plain sight.

Look at this equation again:
 $$V^{\pi}(s)= \sum_{s' \in S}[P(s'|s, \pi_A(a|s))(R(s,\pi_A(a|s),s')+V^{\pi}(s'))]$$
If instead of applying the policy $\pi_A(a|s)$, we leave this as a function of $s$ and $a$, we come to the Quality Function:

***Define*** (Quality Function):
 $$Q^{\pi}(s,a)= \sum_{s' \in S}[P(s'|s, a)(R(s,a,s')+V^{\pi}(s'))]$$

The reason this is called the Quality Function is because 'Value Function' was already taken. It really is talking about the total expected future rewards. 

***Question:*** What does the Quality Function represent?
***Answer:*** Check out the relationship below. Essentially, the Quality Function is the total expected future rewards given that the agent is in state $s$ and performs action $a$. After taking action $a$, the agent simply acts under policy $\pi$.

***Observation*** (Relating the Value and the Quality):
$$Q^{\pi}(s,\pi_A(a|s))=V^{\pi}(s)$$

While we're here, I would like to establish one last definition in the vain of optimality.
***Define*** (Optimal Quality Function):
 $$Q^{*}(s,a)= \sum_{s' \in S}[P(s'|s, a)(R(s,a,s')+V^{*}(s'))]$$

## Extracting Policies:
Let's assume that we are working through an example and I hand you an optimal $Q^{*}(s,a)$. The standard procedure would be to ***use this to extract the policy $\pi^*$*** .

Here's how:
***Procedure*** (Extract Optimal Policy from Optimal Quality Function):
$$\pi_A^*(a|s)=argmax_{a}Q^{*}(s,a)$$


We can also ***extract the optimal Value Function $V^*(s)$*** .

***Procedure*** (Extract Value Function from Optimal Quality Function):
$$V^*(s)=max_{a}Q^{*}(s,a)$$

It should be pretty clear that once we have the optimal quality function, we really have everything we need to have an optimal agent!
## Conclusion:
We got our hands dirty with a bit of derivation. I want to just reiterate that so far we haven't done anything too crazy. We simply used the basic formulas from last time and derived everything to this point. I think this is pretty cool because all you need so far is some algebra and you can come up with what is ***almost*** the soul of basic reinforcement learning.

We haven't done any learning yet, but I hope you now have the basic machinery and intuition that will allow you to really understand the algorithms coming up!

## Next Time:
I will discuss:

 - Banach's Fixed Point Theorem.
 - how to apply the fixed point theorem to our problem.
 - begin thinking about algorithms to find value functions.

## References:
https://spinningup.openai.com/en/latest/spinningup/keypapers.html
[https://sites.ualberta.ca/~szepesva/papers/RLAlgsInMDPs.pdf](https://sites.ualberta.ca/~szepesva/papers/RLAlgsInMDPs.pdf)

