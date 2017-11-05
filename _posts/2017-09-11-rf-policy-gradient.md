---
layout: post
title: "Policy Gradient Methods"
categories: ['Reinforcement Learning']
tags: [reinforcement learning, policy gradient, actor-critic]
comments: true
---

<script type="text/javascript" async
  src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

<script type="text/x-mathjax-config">
MathJax.Hub.Config({
  TeX: { equationNumbers: { autoNumber: "AMS" } },
  tex2jax: {inlineMath: [['$','$'], ['\\(','\\)']]}
});
</script>
---


## Introduction
For the value-based approach such as Q-Learning and Sarsa, all function approximation effort goes into estimating a value function and the policy is the one that selects the action with the highest estimated value in each state. Although it works well in many problems, it has several limitations (<a href="#References">[1]</a>):
1. It's oriented toward finding deterministic policies, whereas the optimal policy is often stochastic, selecting different actions with specific probabilities.
2. An arbitrarily small change in the estimated value of an action can cause it to be, or not be, selected. Such discontinuous changes have been identified as a key obstacle to establishing convergence assurances for algorithms following the value-function approach. For example, Q-learning, Sarsa, and dynamic programming methods have all
been shown unable to converge to any policy for simple MDPs and simple function
approximators.

Rather than approximating a value function, policy based methods approximate a stochastic policy $\pi_{\theta}(s,a) = P(a\mid s, \theta)$ directly using an independent function approximator with its own parameters $\theta$. And the goal is to find out how to adjust the parameters $\theta$ so that we can get more reward. The main mechanism is gradient ascent. The advantages of using policy based approach are 
1. It has better convergence properties 
2. It is effective in high-dimensional or continuous action space
3. It can learn stochastic policies

However, they typically converge to a local rather than global optimum and evaluating a policy is typically inefficient and has high variance. 

## Policy Gradient
In reinforcement learning, two ways of formulating the agent's objective are widely used. One is the **start-state** formulation in which there is a 
designated start state $s_0$ and the agent cares about the long-term reward
obtained from it. It is defined as 

$$
\begin{equation}
J(\pi) = E\{\sum_{t=1}^{\infty}\gamma^{t-1}r_t\mid s_0,\pi\}
\end{equation}
$$

and the value of a state-action pair given policy $\pi$ is defined as 

$$
\begin{equation}
Q^{\pi}(s,a) = E\{\sum_{k=1}^{\infty}\gamma^{k-1}r_{t+k}\mid s_t=s, a_t=a,\pi\}.
\end{equation}
$$

The second formulation is the **average reward** formulation, where the objective is

$$
\begin{equation}
J(\pi) = \lim_{n\rightarrow \infty}\frac{1}{n} E\{r_1+r_2+\dots+r_n\mid \pi\}
\end{equation}
$$

and the value of state-action pair is defined as

$$ 
\begin{equation}
Q^{\pi}(s,a) = E\{\sum_{t=1}^{\infty}r_{t}-J(\pi)\mid s_t=s, a_t=a,\pi\}.
\end{equation}
$$

<div class="theorem">

<strong>Theorem. </strong> (Policy Gradient Theorem )
<em> For any MDP, in either the average-reward or start-state formulations,
</em>

$$
\nabla_{\theta}J(\theta) = \sum_{s}d^{\pi}(s) \sum_a \nabla_{\theta}\pi_{\theta}(s,a) Q^{\pi_{\theta}} (s,a) = \sum_{s}d^{\pi}(s) \sum_a  \pi_{\theta}(s,a) \nabla_{\theta}\log\pi_{\theta}(s,a) Q^{\pi_{\theta}} (s,a).
$$
where for the start-state formulation, $d^{\pi}(s) = \sum_{t=0}^{\infty}\gamma^tPr\{s_t=s\mid s_0, \pi\}$ and for the average reward formulation, $d^{\pi}(s) = \lim_{t\rightarrow \infty}Pr(s_t=s\mid s_0,\pi) $.
</div>


One key point should be emphasized here is that there are no terms of $\frac{\partial d^{\pi}(s)}{\partial \theta}$ which says that the policy changes will not affect the distribution of states. Therefore we can  approximate the gradient by sampling. 

## Monte-Carlo Policy Gradient (REINFORCE)
To approximate $\nabla_{\theta}J(\theta)$, we also need to know $Q^{\pi_{\theta}} (s,a)$. One straightforward idea is to use the actual return $R_t$ as an unbiased sample of $Q^{\pi_{\theta}} (s,a)$ and this results in the **REINFORCE** Algorithm. 

<div class="algorithm">
    <p><strong>function REINFORCE</strong></p>
    <p>Initialize $\theta$ arbitrarily</p>
    <p><strong>for</strong> each episode $\{s_1, a_1, r_2, \dots, s_{T-1}, a_{T-1}, r_T\} $</p>
    <p>$\quad$<strong>for</strong> t=1 to T-1 <strong>do</strong></p>
    <p>$\qquad$ $\theta \leftarrow  \theta + \alpha \nabla_{\theta}\log\pi_{\theta}(s_t,a_t)R_t $</p>
    <p>$\quad$ <strong>end for</strong></p>
    <p><strong>end for</strong></p>
    <p> <strong>return</strong> $\theta$</p>
</div>

As for the implementation of **REINFORCE** method, Andrej Karpathy's post [Deep Reinforcement Learning: Pong from Pixels](http://karpathy.github.io/2016/05/31/rl/) is a very good source to start with. 

There are several drawbacks of **REINFORCE** method. The first is that you have
to wait the whole episode to end to get $R_t$. The second is that **REINFORCE**
learns much more slowly than value based methods because of high variance. 


## Actor-Critic Policy Gradient
To improve over **REINFORCE** method, people came up with the **Actor-Critic**
method where a value function is learned and used to reduce the 
variance of the gradient estimate for rapid learning. The main idea is that 
we use a **critic** $Q_w(s,a)$ to explicitly estimate the action-value function 
$Q^{\pi_{\theta}}(s,a)$ instead of using sample return $R_t$. Now two sets of parameters are maintained: one is the value function parameter $w$ (**critic**) and the other is the policy parameter $\theta$ (**actor**). 


<div class="theorem">

<strong>Theorem. </strong> (Policy Gradient with Function Approximation )
<em> If $Q_{w}$ satisfies
</em>

$$
\begin{equation}
\sum_{s}d^{\pi}(s) \sum_a \pi_{\theta}(s,a) [Q^{\pi_{\theta}} (s,a) -Q_{w}(s,a)]\nabla_w Q_{w}(s,a) = 0
\end{equation}
$$

<em> and is compatible with the policy parameterization in the sense that
</em>
$$
\begin{equation}
\nabla_w Q_{w}(s,a) = \nabla_{\theta} \log\pi_{\pi_{\theta}}(s,a),
\end{equation}
$$

<em> then the policy gradient is exact
</em>

$$
\begin{equation}
\nabla_{\theta}J(\theta) = \sum_{s}d^{\pi}(s) \sum_a  \pi_{\theta}(s,a) \nabla_{\theta}\log\pi_{\theta}(s,a) Q_w (s,a).
\end{equation}
$$


One thing to note here is that Equation (7) can be generalized to 

$$
\begin{equation}
\nabla_{\theta}J(\theta) = \sum_{s}d^{\pi}(s) \sum_a  \pi_{\theta}(s,a) \nabla_{\theta}\log\pi_{\theta}(s,a)[Q_w (s,a) - B(s)].
\end{equation}
$$


This follows immediately because 
$$
\begin{equation}
 \sum_{s}d^{\pi}(s) \sum_a  \pi_{\theta}(s,a) \nabla_{\theta}\log\pi_{\theta}(s,a)B(s) = \sum_{s}d^{\pi}(s) B(s) \nabla_\theta \sum_a \pi_{\theta}(s,a) = 0. 
 \end{equation}
$$

So we can subtract any baseline function $B(s)$ which will not affect the 
theorem but can substantially affect the variance of the gradient estimators.
A good baseline is the state value function $B(s) = V^{\pi_{\theta}}(s)$
. So we can rewrite the policy gradient using the <strong>advantage function</strong>
 $A^{\pi_{\theta}}(s,a) = Q^{\pi_{\theta}}(s,a) - V^{\pi_{\theta}}(s)$

 $$
\begin{equation}
\nabla_{\theta}J(\theta) = \sum_{s}d^{\pi}(s) \sum_a  \pi_{\theta}(s,a) \nabla_{\theta}\log\pi_{\theta}(s,a)A^{\pi_{\theta}}(s,a).
\end{equation}
$$

The advantage function can significantly reduce variance of policy gradient. So the critic should really estimate the advantage function. For the true value function $V^{\pi_{\theta}}$, the TD error $\delta^{\pi_{\theta}}$ is an unbiased estimate of the advantage function. So we can use the TD error to compute the policy gradient

 $$
\begin{equation}
\nabla_{\theta}J(\theta) = \sum_{s}d^{\pi}(s) \sum_a  \pi_{\theta}(s,a) \nabla_{\theta}\log\pi_{\theta}(s,a)\delta^{\pi_{\theta}}.
\end{equation}
$$

In practice we can use an approximate TD error using $\delta_v = r + \gamma V_v(s')-V_v(s)$ which now only requires one set of critic parameters $v$. The algorithm can be summarized as 
</div>
<p></p>

<div class="algorithm">
    <p><strong>function Actor-Critic</strong></p>
    <p>Initialize $s$, $\theta$ arbitrarily</p>
    <p>Sample $a \sim \pi_{\theta}$</p>
    <p><strong>for</strong> each step <strong>do</strong></p>
    <p>$\quad$ Sample reward $r \sim R_s^a$; sample transition $s' \sim P_s^a$</p>
    <p>$\quad$ Sample action $a'\sim \pi_{\theta}(s', a')$</p>
    <p>$\quad$ $\delta = r + \gamma V_v(s')-V_v(s)$</p>
    <p>$\quad$ $\theta = \theta + \alpha \nabla_{\theta}\log\pi_{\theta}(s,a)\delta$</p>
    <p>$\quad$ $v = v + \beta \delta\nabla_{v}V_v(s)$</p>
    <p>$\quad$ $a \leftarrow a'$, $s\leftarrow s'$</p>
    <p><strong>end for</strong></p>
</div>
## References 
1. Sutton, et al. "Policy gradient methods for reinforcement learning with function approximation." Advances in neural information processing systems. 2000.
