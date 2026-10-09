

_Discussion draft for Prof. Nadir Farhi_

## Objective

I am currently considering research at the intersection of **reinforcement learning, uncertainty-aware decision-making, and LLM-based agents**.

The two directions below are starting points rather than fixed thesis proposals. I would particularly value your opinion on which research question is more promising and appropriate for an M2 PFE.

---

# Direction A — Primary Research Direction

## Uncertainty-Aware Reinforcement Learning for Adaptive Sequential Decision-Making in LLM Agents

### Core question

**Can uncertainty help an LLM-based agent learn when to act, gather information, verify, replan, or stop during sequential decision-making?**

### Motivation

Long-horizon agents repeatedly make decisions under incomplete information and uncertain outcomes. In such settings, an agent should not only decide **what action to take**, but also recognize when its current information or plan is unreliable.

This motivates studying whether uncertainty can be incorporated directly into the decision policy, rather than being used only as a diagnostic signal.

### Research questions

- Does uncertainty-aware decision-making improve long-horizon performance compared with uncertainty-agnostic RL?
    
- Can an agent learn **when additional information or verification is worth its cost**?
    
- Can uncertainty help an agent decide when to replan rather than continue with an unreliable plan?
    
- Does uncertainty provide a useful signal for predicting future decision failures?
    
- Can the resulting uncertainty estimates be calibrated sufficiently for reliable decision-making?
    

### Possible scope

A feasible M2 project could focus on **one specific intervention**, such as adaptive information gathering or uncertainty-triggered replanning, in a controlled sequential environment.

The LLM could provide reasoning or high-level decisions, while RL is used to learn how the agent should adapt its decisions over time.

### Expected contribution

A controlled study of whether **uncertainty can be transformed into better sequential decisions for LLM-based agents**, with particular attention to reliability rather than only average performance.

---

# Direction B — Alternative

## Uncertainty-Aware LLM Reward Adaptation for Reliable RL/MARL

### Core question

**Can uncertainty estimation make LLM-generated or LLM-adapted rewards more reliable for reinforcement learning or multi-agent reinforcement learning?**

### Motivation

LLMs can be used to generate or dynamically modify reward objectives. However, an RL system may react poorly if an LLM produces an unreliable or inappropriate reward update.

Rather than treating every reward update as equally trustworthy, uncertainty could potentially be used to determine **when an update should be accepted, moderated, delayed, or rejected**.

### Research questions

- Does uncertainty in LLM-generated reward updates predict instability or performance degradation?
    
- Can uncertainty-aware reward adaptation improve RL/MARL stability compared with naive dynamic reward updates?
    
- How can uncertainty be distinguished from genuine changes in the desired reward objective?
    
- Can uncertainty provide an early-warning signal for reward-induced failures?
    
- Can reward adaptation remain responsive while becoming more reliable?
    

### Possible scope

The project could be studied in a **controlled cooperative RL/MARL environment with interpretable reward components**, comparing static rewards, dynamically adapted LLM rewards, and uncertainty-aware reward adaptation.

### Expected contribution

A statistically grounded approach to **making LLM-augmented reward adaptation more reliable**, connecting uncertainty estimation with practical RL stability and performance.

---

# My Current View

My current preference is **Direction A**, because it is closely connected to my longer-term interest in **RL, sequential decision-making, uncertainty, and LLM-based agents**.

However, **Direction B may be more directly connected to your recent work on reliable LLM-augmented RL/MARL**, which is why I would be very interested in your assessment of both.

In particular, I would appreciate your view on:

- which direction has the stronger research question,
    
- which is more scientifically promising,
    
- which is more feasible for an M2 PFE,
    
- and how either direction could be narrowed or reformulated.
    

I would be happy to substantially modify the ideas based on your suggestions.

## Application Domain

I would also like the thesis to include a **concrete application or case study** in the final stage. I have deliberately not fixed the application domain yet, as I would prefer to choose it based on the research question, available environments/data, and the supervisor's expertise.

---

_This is intended as a discussion draft, not a fixed thesis proposal. The final research question, methodology, and application domain would be developed with the supervisor._