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
$
\boldsymbol{A}_{t}=\left\{a_{t}^{i}\right\}_{i=1}^{m}, \forall i \in\{1, \ldots, m\}, t \in\{0, \ldots, H-1\}
$, 
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
###  EMaQ: Expected-Max Q-Learning Operator for Simple Yet Effective Offline and Online RL [[link]](http://proceedings.mlr.press/v139/ghasemipour21a/ghasemipour21a.pdf)  

----

### Offline Meta-Reinforcement Learning with Advantage Weighting
1. What is the motivation?

Combining meta-learning and offline reinforcement learning allow one to apply offline reinforcement learning to settings where the amount of data is limited, thus may allow many real-life applications. 

2. What is the problem the paper wants to solve?

Develop a meta RL method that can use the offline data. However, one challenge of such method is the consistency, i.e., given large test dataset, the algorihtm should be able to perform well no matter what the training distribution is. MAML allows this consistent learning. Unfortunately, existing MAML-based meta rl are based on online policy gradients, which are not effective in the offline setting. Thus, the authors aim to combine MAML with value-based RL subroutines. Directly applying MAML to the value function is not a good idea, because the TD backups are unstable, which makes the meta-gradient less efficient. 

3. What is the proposed approach?

The authors propose to combine MAML with a supervised subroutine, specically the advantage-weighted regression (AWR). To make MAML + AWR work, the authors introduce the carefully chosen policy update in the inner loop, which makes the MAML update more expressive. In addition, the authors replace simple MLP with a more expressive architecture, which leads to the final algorithm MACAW.

The AWR policy objective is as follows:
$$
\begin{gathered}
\mathcal{L}^{\mathrm{AWR}}(\vartheta, \varphi, B)= 
\mathbb{E}_{\mathbf{s}, \mathbf{a} \sim B}\left[-\log \pi_{\vartheta}(\mathbf{a} \mid \mathbf{s}) \exp \left(\frac{1}{T}\left(\mathcal{R}_{B}(\mathbf{s}, \mathbf{a})-V_{\varphi}(\mathbf{s})\right)\right)\right]
\end{gathered}
$$
,where $R_B(s,a)$ is simply the Monte Carlo return based on the behavior policy. 

To learn the value function, the authors simply regressed onto the Monte Carlo return. 
$$
\begin{aligned}
\phi_{i}^{\prime} \leftarrow & \phi-\eta_{1} \nabla_{\phi} \mathcal{L}_{V}\left(\phi, D_{i}^{\mathrm{tr}}\right), \quad \text { where } \\
& \mathcal{L}_{V}(\phi, D) \triangleq \mathbb{E}_{\mathbf{s}, \mathbf{a} \sim D}\left[\left(V_{\phi}(\mathbf{s})-\mathcal{R}_{D}(\mathbf{s}, \mathbf{a})\right)^{2}\right]
\end{aligned}
$$

Afer adapting the value function, MACAW adapt the policy the policy using the AWR loss. The full meta-training routine is as below. 
![](https://cdn.mathpix.com/snip/images/NSSdEH6BvQ0aooP8kzWexkt1q8EFddoTFXEEwvn4Zqg.original.fullsize.png)


4. What are the key conclusions and key results?

The authors found that simply combining AWR with MAML does not perform well because the inner-gradient is not expressive enough to identify the underlying task. Thus, they added the advantage regression loss on top of the action weighted regression loss for the inner-loop update, and observed improved performance. 

5. What is the novelty?

Developed a bootstrap-free approach for offline meta-RL. To make the proposed apporach work, the authors introduced 1) enriched inner-loop udpate; 2) weight transform layer. 

6. Any related work worth of following?

[1] Chelsea Finn and Sergey Levine. Meta-learning and uni- versality: Deep representations and gradient descent can approximate any learning algorithm. International Con- ference on Learning Representations, 10 2018.  

7. Any general comments?

I. Why is the $\mathcal{L}_\pi$ only applied to the inner loop update but not the outer loop update?  
II. The proposed algorithm directly learns a policy. In practice, if only the reward function is changed, this will be less effective. 
III. 