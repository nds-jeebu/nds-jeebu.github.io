
# Reinforcement Learning (1/5)

  

## Backstory

Recently, I have become obsessed with reinforcement learning. The paper that got me so excited was: [Generative Adversarial Imitation Learning]([https://arxiv.org/pdf/1606.03476.pdf](https://arxiv.org/pdf/1606.03476.pdf)). Long story short, this paper demonstrates how to leverage expert behaviors when training a deep reinforcement learning agent.

  

I skimmed over the paper expecting to kind of understand what was going on. Instead of understanding what was actually happening, I got more confused. However, after I went through it about ten more times, I got really excited then really spooked. To me, someone trying to become an expert in the field, this paper is just ***way too clever***!

  

After digging around for a while, I couldn't find a great intuitive explanation of what was happening. Furthermore, I didn't even really understand the math involved in the paper. So, I set out to understand.

  

This post is the first in a series of posts that will allow me and anyone who reads this to not just understand the paper, but to also understand how ***you*** might come up with something just as special !

  

## How is this any different from other blogs?

Points to address this very reasonable question:

  

1. Eventually, after I get past the basics, a lot of the material I plan on discussing is restricted to academic papers and very brief powerpoint lectures. Not very many people have the time or patience for this! I want to make those ideas accessible to all!

2. I really do think that many posts have missed out on why certain algorithms mathematically work. The few that do are great, and I aspire to be like them!

3. There will definitely be overlap with existing posts. I plan on adding links to those posts whenever applicable.

4. This is me documenting how I learn and explaining ideas in my own words. Hopefully, there will be some of you out there who really vibe with my explanations and we can help each other learn more efficiently.

## Objectives:

  

Get intimately acquainted with ~~deep~~ **reinforcement learning**. (Note: we will get to the deep part eventually, but my mistake when starting was jumping right into the complicated stuff without really understanding the background.)

  

- Understand the problem setup and assumptions

- Know why the algorithms actually work

- Question certain assumptions

  

## Background

I find that most articles discuss the magic of reinforcement learning by showing us that it can be used to beat really hard games like Go. [Here's]([https://www.nature.com/articles/nature16961](https://www.nature.com/articles/nature16961)) the paper discussing how that was done by the folks at Google. A very different application that I found was using deep reinforcement learning to [predict human poses]([https://arxiv.org/abs/1909.03449](https://arxiv.org/abs/1909.03449)). If you look long enough, you'll find applications all over the place.

  

The natural question is ***when can you use reinforcement learning*** to solve a problem?

  

We will eventually be able to answer this question! For now, it's time to get into the weeds.

  

## Framework

At a very high level, reinforcement learning aims to build a **controller**. A controller is a type of agent that is able to make observations about an environment. Based on these observations the controller takes actions to **maximize/minimize some reward**.

  

Example: Imagine you are playing a game of checkers. In this game, you are the controller. You are able to look at all of the pieces on the board. Your decision is about which piece to move to minimize the total number of enemy pieces.

  

### Notation:

***Define*** (Environment):

  

$S-$ the set of all possible states that our controller can perceive.
$A-$ the set of all possible actions that our controller can take.
$P(s_{t+1}|s_t,a_t)-$ the probability of ending up in state $s_{t+1}$ after taking action $a$ while in state $s_t$.
$R(s_{t-1},a_{t-1},s_t)-$ the reward for taking action $a_{t-1}$ while in state $s_{t-1}$ and ending up in state $s_t$.
$s_0,a_0,r_1,s_1,a_1,r_2,...,s_{t-1},a_{t-1},r_{t-1},s_t-$ trajectory starting in state $s_0$ and ending up in state $s_t$. Given actions $a_i$ and rewards $r_i$ for $i \in \{0,1,...,t-1\}$.

  

These are the basic building blocks of an environment that our controller lives in.

### Goal:

An agent (I'll stop referring to it as a controller from now) that starts in some state $s_0$ wants to ***maximize its total expected future reward***. Imagine we have an agent, how might we determine its total reward when it is in state $s_T$? First, look at the trajectory: $s_0,a_0,r_1,s_1,a_1,r_2,...,s_{T-1},a_{T-1},r_{T-1},s_T$, and then add up all of the rewards!

  

***Define*** (Total Reward while in state $s_T$):

$$R_T=\sum_{t=0}^{T-2}r_{t+1} \ \ \ \ , T\geq2$$ where $r_t=R(s_{t-1},a_{t-1},s_t)$.

  

***Define*** (Total Expected Reward Starting in State $s_0$):

$$V(s) = \mathbb{E}[\sum_{t=0}^{\infin}r_{t+1}|s_0=s]$$

And that's it! At the core of it, the aim of reinforcement learning agent is to maximizes $V(s)$ for all states $s \in S$.

  

### WAIT!!!

Not so fast ... here's a list of questions that I think merit attention:

- What does it mean?
- What does the notation even mean, expectation over what distribution?
- Don't other texts usually introduce a 'discount'?

  

### Exactly:

This is the kind of analysis that I'm talking about. I feel like there are certain formulas that get thrown around because ***they are known to work***. My approach is going to be a bit different. There's no use in me telling you the right answer from the beginning because then we miss out on the nuance of why that's the answer.

  

I'm going to start from the bottom and introduce formulas and math tricks as we need them.

  

## A Few More Basics: Policies

I want to introduce one more term here and I promise it will be the last, at least for this article.

  

***Define*** (Policy):

$$\pi : A \times S \to [0,1]$$

$$\pi(a|s)=P(a|s),$$

$$\sum_a \pi(a|s)=1$$

$s-$state
$a-$ action.

Okay, to be fair here, the notation is kind of weird. I read this as, "the probability of taking action $a$ given that I am in state $s$."

  

***Think of it like this:*** this policy is really our agent. Given the agent is in some state $s$, it needs to determine which action to take. In the real world, there is no definitive right answer, so it gives each action some weight. 

  

**Here's how it works:**

Generally, we can think of the policy as a probability distribution. ***We would like to sample actions according to this distribution.***

Here's how I will denote sampling an action:
$$ a_t = {sample}_a \pi(a|s)$$

## More On Policies
So far, the way I have defined policies is pretty general, but there is an implicit assumption being made: ***the policy does not change with time!***

***Define*** (Set of Stochastic, Stationary Policies):
$$\Pi_{stat}$$
This is the set of all policies of the form that I have described so far. In other words, ***this is the set of all policies that do not change over time and are stochastic.***

In our discussion, it is important to consider a subset of $\Pi_{stat}$ that consists of only ***deterministic policies***.

***Define*** (Set of Deterministic, Stationary Policies):
$$\Pi_{det} \subset \Pi_{stat}$$

***Question***: Why are we restricting ourselves?
***Answer***: The answer comes in two parts:

 1. For now, it will be more intuitive that our agent can make an action in a deterministic way. 
 2. This makes the upcoming formulas ***slightly easier to follow***!

***New Notation*** (Deterministic, Greedy Policy):

One way of creating a deterministic policy out of a stochastic policy is to use the $argmax$ operation as follows:
$$ a' = {argmax}_a \pi(a|s)$$

$$\pi_A(a|s) \coloneqq {argmax}_a \pi(a|s)$$

The subscript $A$ is to remind you that the output belongs to the set of actions. All that's happening here: ***we are selecting the action that is going to yield the highest chance for future reward according to the policy.*** Such a behavior is referred to as ***greedy***.

***Note***: We will explore different ways of sampling from this distribution in future discussions.

## Answering Some Questions

Okay, now it's time that we catch our breath. I want to use this section to clear up **some** of the questions. Only the ones that prevent us from moving on for now.

  

**Reason:**

Each of those questions is very reasonable. It won't make sense to answer all of them immediately for the same reason that I'm not just writing down all of the equations immediately.

  **Selected Answers:**

First, let's rewrite the foundational equation, let's redefine the name as well call the total expected future reward under a certain policy the **Value**:

  

***Define*** (Value Starting in State $s_0$):

$$V^{\pi}(s) = \mathbb{E_{\pi}}[\sum_{t=0}^{\infin}r_{t+1}|s_0=s]$$

Now, it can be read as follows: "**We want to maximize our total expected future rewards (or our Value) acting according to some policy $\pi$.**"

Second, notice how we have one source of stochasticity: $P(s_{t+1}|s_t,a_t).$ Technically, we can rewrite the transition probability as: $P(s_{t+1}|s_t, \pi_A(a|s=s_t))$. This is where the expectation in the reward equation comes from. I will eventually write out a few terms of this equation to make it clear. For now, I think this suffices.


Finally, I want to address what this means and why we may have come up with it on our own. Let's go back to checkers. Think of the agent as you and the policy as your strategy given that you find yourself in some configuration on the board. You want to maximize your points, or in this case, minimize the number of pieces your opponent has. This is essentially maximizing your **value** every move.

## Conclusion:

  

So far, we have given ourselves a **PRETTY GENERAL** framework. We have:
- An agent acting on some policy, $\pi$ in an abstract environment.
- A goal: maximize $V^{\pi}(s)$ for all $s \in S$.
- Basic tools for calculating a trajectory.

**Claim:**

We now have all of the basic tools to build ourselves up to some pretty fascinating results.

  

## Next Time:

I will:

1. define Markov Decision Processes.
2. define optimality.
3. derive the Quality Function.
4. draw the connection between Value, Quality, and Policies.

## References:
https://spinningup.openai.com/en/latest/spinningup/keypapers.html
[https://sites.ualberta.ca/~szepesva/papers/RLAlgsInMDPs.pdf](https://sites.ualberta.ca/~szepesva/papers/RLAlgsInMDPs.pdf)

