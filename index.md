_Maria Diea_, _Diana Epureanu_ and _Teodora Stoenescu_

## Motivation

_Off-policy methods_ make use of two policies: one is evaluated and improved, and one is used to generate behavior. Computing the return of taking an action is expensive, therefore most off-policy methods utilize _importance sampling_ to estimate the returns under the target policy by using the returns under the behavior one. This project presents a comparison between **ordinary** and **weighted** importance sampling by experimenting with different algorithms, namely _TD_ and _Monte-Carlo_, in several environments. 

Formally, the contrast between these two kinds of importance sampling can be observed in their biases and variances. Sutton et al.[1] argue that ordinary importance sampling produces unbiased estimates, but presents large and even infinite variance. Weighted importance sampling is preferred for its lower, finite variance. In this study we put these hypotheses to test. Since we are analyzing each importance sampling method in various scenarios, we can discover which parameters of our study influence the samplers' performance and how. This will lead to a better understanding on how each sampling method works, when it works best and why.  

## Main Techniques

To estimate the state value in **importance sampling**, first the _importance-sampling ratio_ is computed: <br>
![Image](/assets/images/f1.PNG) where $\pi$ is the target policy, $b$ is the behavior policy and $T(t)$ is the time of termination of the episode starting at time $t$ (as episodes are observed under a single global time).\\\\ $\tau(s)$ is used to denote all time steps in which state s was visited (for a every-visit approach) or all the time steps where s was visited for the first time within an episode (for a first-visit approach).

There are two types of importance sampling:
1. _ordinary importance sampling_ estimates the state value $v_\pi(s)$ by scaling the return of a state by the ratio and averaging the result:
$$V(s) = \frac{\sum_t\in\tau(s) \rho_{t:T(t)-1} G_t}{|\tau(s)|}$$

2. _weighted importance sampling_ estimates the state value $v_\pi(s)$ by scaling the return of a state by the ratio and uses an weighted averaging of the result:
$$V(s) = \frac{\sum_t\in\tau(s) \rho_{t:T(t)-1} G_t}{\sum_t\in\tau(s)\rho_{t:T(t)-1}}$$


---

**Monte Carlo methods** solve reinforcement learning problems based on averaging sample returns. They require no knowledge of the environment as they only require sample sequences of states, actions and rewards from past interaction with the environment. For the purpose of this assignment, and as a general way to ensure the availability of well-defined returns, we only analyze episodic tasks, i.e. experience is divided into episodes which are guaranteed to terminate. Off-policy MC methods learn the target policy which then becomes the optimal policy, and use a behavior policy which is exploratory to generate behavior. Here importance sampling is used, where one distribution is used for sampling experience, and a second distribution which estimated the expected values given said experience. An incremental implementation of MC works on an episode-by-episode basis, where state values are computed after each episode instead of going through all available episodes before updating.<br>

*Incremental MC using Weighted Importance Sampling*[1] <br> <br>
 ![Image](/assets/images/mcweighted.PNG) <br> <br>
 
 
*Incremental MC using Ordinary Importance Sampling* <br> <br>
 ![Image](/assets/images/mcordinary.PNG) <br> <br>
 
 ---

**Temporal Difference methods** borrow approaches from both Monte Carlo ideas, as they learn from experience without environment knowledge, and Dynamic Programming ideas, as estimate updates are based on other estimates, without waiting for an outcome (this method is referred to as _bootstrapping_). TD methods are implemented in an online, incremental fashion. 

Off-policy TD for the control problem introduces the _Q-learning_ algorithm. Here, the state-action value $Q(S_t, A_t)$ is updated incrementally:
$$Q(S_t, A_t) = Q(S_t, A_t) + \alpha[R_{t+1} + \gamma max_a Q(S_{t+1}, a) - Q(S_t, A_t)]$$
where $\alpha\in(0,1]$ is the step size, $\gamma$ is the discount factor and $max_a$, the maximum reward that is reachable in the next state. <br>
Q-learning learns the optimal policy even when actions are selected according to a more exploratory policy. Given state S, action A is chosen from a policy derived from Q (eg. $\epsilon$-greedy or even random).

## Experimental Setup

Let's define the experiments that we need to do.

### Environments
For simplicity, we will focus on single agent, fully observable, deterministic environments. Since we are investigating the Monte Carlo approach which approximates the value of a state-action pair by calculating the mean return from a collection of episodes, we will test the techniques only on episodic environments. Thus, we  investigate the difference between the sampling techniques on combinations of continuous/discrete environments. 

### Baselines

### Hyperparameters

### Metrics

### Runs


## References
[1] Richard S. Sutton and Andrew G. Barto. 2018. _Reinforcement Learning: An Introduction_. A Bradford Book, Cambridge, MA, USA.


## Welcome to GitHub Pages

You can use the [editor on GitHub](https://github.com/mariadiea/RL-reproducible/edit/gh-pages/index.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/mariadiea/RL-reproducible/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
