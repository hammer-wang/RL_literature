# Paper notes

## Templates
----

### Title
1. What is the motivation?
2. What is the problem the paper wants to solve?
3. What is the proposed approach?
4. What are the key conclusions and key results?
5. What is the novelty?
6. Any related work worth of following?
7. Any general comments?
----

## List of Papers

1. Model-Based Offline Planning with Trajectory Pruning [[link]](https://arxiv.org/pdf/2105.07351.pdf)
2. EMaQ: Expected-Max Q-Learning Operator for Simple Yet Effective Offline and Online RL [[link]](http://proceedings.mlr.press/v139/ghasemipour21a/ghasemipour21a.pdf)

----

## Model-Based Offline Planning with Trajectory Pruning [[link]](https://arxiv.org/pdf/2105.07351.pdf)

Previous model-based planning methods (MBOP) relies on the learned behavior policy for trajectory rollouts, which could be too restrictive and may lead to suboptimal offline-planning performance. To address this issue, the authors developed MOPP to use behavior policies to guide the trajectory rollout, similar to MBOP. However, instead of action weighting used in MBOP, trajectory pruning is incorporated here to avoid potential out-of-distribution samples. Results show that MOPP improves upon MBOP. However, both methods suffer from the issue that for mixed type dataset or random dataset, the performance is worse than MOPO.  

![results](https://cdn.mathpix.com/snip/images/WX6g2uesVY7JW3d3dY6VG6Y7QpCx2ktfjLEDSgyzOsI.original.fullsize.png)

MOPO uses ensembles of expressive autoregressive dynamics models (ADM) to learn the behavior and dynamics. It improves the coverage of MBOP by allowing a large deviation from the behavior policy, as well as greedy max-Q operation. To provide safety against OOD samples, MOPO prunes state-action pairs with high uncertainty.  

In addition to the aggressive policy rollouts, using the more expressive ADM than simple MLPs also leads to potentially better dynamics models and behavior policies.  

MOPP is similar to MBOP in the sense that they both use the behavior policy to guide the planning. However, they increased standard deviation for more aggressive exploration.

$$
a_{t}^{i} \sim \mathcal{N}\left(\boldsymbol{\mu}^{a}\left(s_{t}\right), \operatorname{diag}\left(\frac{\sigma_{M}}{\max \boldsymbol{\sigma}^{a}\left(s_{t}\right)} \cdot \boldsymbol{\sigma}^{a}\left(s_{t}\right)\right)^{2}\right)
$$

For each step, they sample $m$ times:
$$
\boldsymbol{A}_{t}=\left\{a_{t}^{i}\right\}_{i=1}^{m}, \forall i \in\{1, \ldots, m\}, t \in\{0, \ldots, H-1\}
$$, 
and takes the max-Q operation to pick the best action out of the $m$ sampled actions:
$$
\hat{a}_{t}=\arg \max _{a \in A_{b}} Q_{b}\left(s_{t}, a\right), \forall t \in\{0, \ldots, H-1\}
$$

### Summary

1. larger standard deviation when sampling actions from behavior policy  
2. sample multiple actions and do the argmax to pick the best action  
3. estimate the uncertainty following MoRel, and prune trajectories with large uncertainty. 
4. same as MBOP, it still underperforms MBPO for mixed and random dataset types. 

***Model-based planning methods***  

Online

* PETS
* POLO
* POPLIN
* PDDM
* CEM
* MPPI

Offline

* MBOP
* MOPO  
* 
----
## EMaQ: Expected-Max Q-Learning Operator for Simple Yet Effective Offline and Online RL [[link]](http://proceedings.mlr.press/v139/ghasemipour21a/ghasemipour21a.pdf)  

