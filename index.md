_Maria Diea_, _Diana Epureanu_ and _Teodora Stoenescu_

## Motivation

_Off-policy methods_ make use of two policies: one is evaluated and improved, and one is used to generate behavior. Computing the return of taking an action is expensive, therefore most off-policy methods utilize _importance sampling_ to estimate the returns under the target policy by using the returns under the behavior one. This project presents a comparison between **ordinary** and **weighted** importance sampling by experimenting with different algorithms, namely _TD_ and _Monte-Carlo_, in several environments. 

Formally, the contrast between these two kinds of importance sampling can be observed in their biases and variances. Sutton et al.[1] argue that ordinary importance sampling produces unbiased estimates, but presents large and even infinite variance. Weighted importance sampling is preferred for its lower, finite variance. In this study we put these hypotheses to test. Since we are analyzing each importance sampling method in various scenarios, we can discover which parameters of our study influence the samplers' performance and how. This will lead to a better understanding on how each sampling method works, when it works best and why.  

## Main Techniques

To estimate the state value in **importance sampling**, first the _importance-sampling ratio_ is computed: <br>
![Image](/assets/images/f1.PNG) <br>
where ![Image](/assets/images/pi.PNG) is the target policy, *b* is the behavior policy and *T(t)* is the time of termination of the episode starting at time *t* (as episodes are observed under a single global time).



There are two types of importance sampling:
1. _ordinary importance sampling_ estimates the state value ![Image](/assets/images/statevalue.PNG) by scaling the return of a state by the ratio and averaging the result: <br> <br>
![Image](/assets/images/ordinary.PNG) <br> where ![Image](/assets/images/tau.PNG) is used to denote all time steps in which state s was visited (for a every-visit approach) or all the time steps where s was visited for the first time within an episode (for a first-visit approach).


2. _weighted importance sampling_ estimates the state value ![Image](/assets/images/statevalue.PNG) by scaling the return of a state by the ratio and uses an weighted averaging of the result:<br> <br>
![Image](/assets/images/weighted.PNG) <br> <br>


---

**Monte Carlo methods** solve reinforcement learning problems based on averaging sample returns. They require no knowledge of the environment as they only require sample sequences of states, actions and rewards from past interaction with the environment. For the purpose of this assignment, and as a general way to ensure the availability of well-defined returns, we only analyze episodic tasks, i.e. experience is divided into episodes which are guaranteed to terminate. Off-policy MC methods learn the target policy which then becomes the optimal policy, and use a behavior policy which is exploratory to generate behavior. Here importance sampling is used, where one distribution is used for sampling experience, and a second distribution which estimated the expected values given said experience. An incremental implementation of MC works on an episode-by-episode basis, where state values are computed after each episode instead of going through all available episodes before updating.<br>

*Incremental MC using Weighted Importance Sampling* [1] <br> <br>
 ![Image](/assets/images/mcweighted.PNG) <br> <br>
 
 
*Incremental MC using Ordinary Importance Sampling* <br> <br>
 ![Image](/assets/images/mcordinary.PNG) <br> <br>
 
 ---

**Temporal Difference methods** borrow approaches from both Monte Carlo ideas, as they learn from experience without environment knowledge, and Dynamic Programming ideas, as estimate updates are based on other estimates, without waiting for an outcome (this method is referred to as _bootstrapping_). TD methods are implemented in an online, incremental fashion. It is possible to learn a V function off-policy and to this end we can use the on-policy Sarsa algorithm and apply importance weights. 

## Experimental Setup

Let us recap the goal of this study: we are looking to study the performance of four off-policy methods for predicting a state function V.
1. Monte Carlo - Ordinary
2. Monte Carlo - Weighted
3. SARSA - Ordinary
4. SARSA - Weighted

### Environments
For simplicity, we will focus on single agent, fully observable environments. Since we are investigating the Monte Carlo approach which approximates the value of a state-action pair by calculating the mean return from a collection of episodes, we will test the techniques only on episodic environments. Thus, we investigate the difference between the sampling techniques on combinations of continuous/discrete environments.

**Environment 1: Blackjack**. The goal of blackjack is to obtain the greatest possible sum of cards without exceed 21. All face cards count as 10, while an ace can count either as 1 or as 11. All other cards have their usual value. A player can request more cards (hits), until he either stops (sticks) or exceeds 21 (goes bust). Rewards of +1, -1, and 0 are given for winning, losing and drawing, respectively. This environment is originally developed by OpenAI and provided in the Gym toolkit[2]. More details can be found on the GitHub page of the project.[3] 

This environment is used in the book [1] to showcase the performance of the MC prediction algorithm with weighted sampling. We are going to implement similar experiments for all of our other setups. <br>
- target policy: *stick* if the player's sum is 20 or 21, otherwise *hit*.
- behavior policy: *stick* or *hit* at random with equal probability. <br>
<br>

**Environment 2: Infinite Variance** <br>
![Image](/assets/images/env2.PNG) <br> <br>
Consider this simple environment presented in the book. There is one non-terminal state s and two actions, *right* and *left*. The *right* action goes to the termination state, while the *left* action has 0.9 probability to go back to *s* and 0.1 probability to terminate. The rewards are 1 for this last transition, otherwise 0. <br>
- target policy: always *left*.
- behavior policy: *right* or *left* at random with equal probability. <br>
The value of s under the target policy is 1, but this example showcases that the estimates of MC with ordinary sampling will have infinite variance. We are curious to see whether this happens for the other setups as well.


### Baselines


### Hyperparameters

### Metrics

A good measure on the quality of the predictions is the Mean Squared Error, measured for each episode over a number of runs. To determine the true value of the state function for each environment, we separately generate 100,000 episodes using the target policy and averaging their returns. This can also help us see whether the estimates have infinite variance.

### Runs

Since we replicate experiments presented in the Sutton et al., we use the same setup they presented. The blackjack experiment has 100 runs with 10,000 episodes per run. The Infinite Variance experiment has 10 runs with 100,000,000 episodes.

## References
[1] Richard S. Sutton and Andrew G. Barto. 2018. _Reinforcement Learning: An Introduction_. A Bradford Book, Cambridge, MA, USA. <br>
[2] https://gym.openai.com/ <br>
[3] https://github.com/openai/gym/blob/master/gym/envs/toy_text/blackjack.py <br>
