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

**Temporal Difference methods** borrow approaches from both Monte Carlo ideas, as they learn from experience without environment knowledge, and Dynamic Programming ideas, as estimate updates are based on other estimates, without waiting for an outcome (this method is referred to as _bootstrapping_). TD methods are implemented in an online, incremental fashion. It is possible to learn a V function off-policy and to this end we can use the on-policy Sarsa algorithm and apply importance weights. <br>
![Image](/assets/images/sarsarules.PNG) <br> <br>


## Experimental Setup

Let us recap the goal of this study: we are looking to study the performance of four off-policy methods for predicting a state function V.
1. Monte Carlo - Ordinary
2. Monte Carlo - Weighted
3. SARSA - Ordinary
4. SARSA - Weighted

### Environments
For simplicity, we will focus on single agent, fully observable environments. Since we are investigating the Monte Carlo approach which approximates the value of a state-action pair by calculating the mean return from a collection of episodes, we will test the techniques only on episodic environments.

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


### Metrics

A good measure on the quality of the predictions is the Mean Squared Error, measured for each episode over a number of runs. To determine the true value of the state function for each environment, we separately generate 100,000 episodes using the target policy and averaging their returns. This can also help us see whether the estimates have infinite variance.

### Runs

Since we replicate experiments presented in the Sutton et al., we use the same setup they presented. The blackjack experiment has 100 runs with 10,000 episodes per run. The Infinite Variance experiment has 10 runs with 100,000,000 episodes.

## Results
### Blackjack 
As in the book, we evaluate the state in which the dealer is showing a deuce, the player has a sum of 13 and a usable ace *(13, 2, True)*. In this state, the target policy will always *stick*, therefore after computing the Q-values with SARSA, we can get the V value by looking at the corresponding cell in Q for action *stick*. 

**Monte Carlo** <br>
We plot the value function for the ordinary importance sampling (first row), weighted importance sampling (second row) and target value function (third row). From the plots it already is noticeable that the value function with the weighted importance sampling approaches the true value more than the ordinary importance sampling value function.<br>
![Image](/assets/images/plots.png) <br> <br>

The plot below depicts the mean squared error between the predicted value function (red for weighted and green for ordinary) and the target value function for the state (13, 2, True). <br>
From this plot, it is clear that the weighted importance sampling creates a much smaller variance in the mean squared values than the ordinary one. To see the difference between the two, we calculate the variance of the mean squared error for both cases, and find that: 
- Variance of ordinary MC mse: 0.2426
- Variance of weighted MC mse: 0.0126 <br><br>

There is one thing to notice: while the expected behavior for both ordinary and weighted imporance sampling techniques is for the MSE to decrease over the number of episodes, this is not the case. Firstly, it takes 100 episodes for the state to be accessed often enough to create a meaningful change in the MSE. Next, for the ordinary importance sampling value, it seems that the function does not converge while for the weighted importance sampling value, it converges to a wrong value.<br>
![Image](/assets/images/mc.png) <br> <br>

**Off-policy SARSA** <br>
When using our variant of off-policy SARSA, it seems that the algorithm is not able to learn. The ordinary case does present a slight increase towards the end, but nothing conclusive. This might be due to the fact that the state itself might not be visited very often.
![Image](/assets/images/weightedsarsa.png) <br> <br>

To force the algorithm to learn, every 10 episode we change the starting state from random, to the one we're investingating: *(13, 2, True)*, and we evaluate it on 50,000 episodes for 100 runs. Below we can see that the MSE for the ordinary case increases alot and diverges. The weighted one starts to stabilize around a MSE of 0.1. <br>
![Image](/assets/images/50.png) <br> <br>

### Infinite Variance
**Monte Carlo** <br>
In the figure below, state value functions for the ordinary importance sampling over 10 runs are plotted for 1M episodes. It is clear that the estimates produced are highly unstable, and fail to converge even after a very high number of episode to the true value of 1. 
<br>
![Image](/assets/images/ordinarymc.jpeg) <br> <br>

Weighted MC, In comparison, having the same 10 runs over 1M episodes, the state value functions for the weighted importance sampling produces some variance in the results, but the values are more bounded. 
The reason for this is that more episodes are probably needed in order to remove the bias that weighted approaches are prone to do. 

<br>
![Image](/assets/images/weightedmc.jpeg) <br> <br>


**Off-policy SARSA** <br>
The values after 10,000 epsiodes lie between [0.6-2.93].<br>

![Image](/assets/images/sarsavar.jpeg) <br> <br>

The values after 10,000 epsiodes lie between [0.93-1.84] therefore it seems to have more of a converging behavior than the ordinary method. <br>

![Image](/assets/images/sarsawe.png) <br> <br>

## References
[1] Richard S. Sutton and Andrew G. Barto. 2018. _Reinforcement Learning: An Introduction_. A Bradford Book, Cambridge, MA, USA. <br>
[2] https://gym.openai.com/ <br>
[3] https://github.com/openai/gym/blob/master/gym/envs/toy_text/blackjack.py <br>
