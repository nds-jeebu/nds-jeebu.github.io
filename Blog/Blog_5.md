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
