---
title: SpotServe, Serving Generative Large Language Models on Preemptible Instances
tags: [mlsys, distributed system]
categories: [mlsys]
date: [2024-02-02 18:15:00]
index_img: /img/spotserve/cover.jpg
---

# **SpotServe: Serving Generative Large Language Models on Preemptible Instances**

Author: Xupeng Miao, et al.

## Background

### Generative LLM

![img](https://lh7-us.googleusercontent.com/__jCtoUnwYaYZ8vWKlfn_-PsE6uQZ41sjjkrQ0it5gVYu5o9_R3DIciYp3GgOunsNsa0Pz5mRqJvuHHwhRx1t64AdmjazFVge6AqVtAC8WouPtAj0S71vs6tbg5f4DaEFC0ndDRucqVq4Ngwt98sAwF3DQ=s2048)

- Input: tokens; 
- Output: token sequence

- Stops when: output reaches maximum length/ending token <EOT>

### Two types of GPU Instances

- On-demand GPUs:

  - **Expensive**
  - Use anytime you need

- Preemptible GPU Instances (e.g. Spot Instances):

  - **Cheap (run on spare capacity)**
  - Might be Preempted anytime
  - Offers a grace period after preemption to complete currently running tasks

  ![img](https://lh7-us.googleusercontent.com/DkwxhSsXc0N0WC81IuCqRq9LXtl9aXjPPiM8yei1CeiuHp_jHZOZWvidvJrN-hygJVM_vc9wGKiqVlTVJiv5vhHzOCEXUKpK20txDLNUNcxoz3nKE0O7KDXFiAfXOU85Qax8Nsg1q_sOIDuHFZ148YG1IQ=s2048)

## Challenges

- The number of available preemptible instances changes frequently **=>** **Dynamic reparallelization** for optimized serving performance

- Restarting LLM results in great overhead of reloading parameters **=>** Find the **optimal migration strategy** that minimize the cost
- Grace periods may not be long enough for finishing current request
- The reduction of throughput during this process might lead to accumulation of subsequent requests

## SpotServe

### Overview

![img](https://lh7-us.googleusercontent.com/XZZe4AHfMJmr_CeXTrBMCnfXwgQzDLcx_JWctJd7FrUSSlvNvBLlWeiNsg_rdVoFw-qOODUKmDPkdLP5tIQ-aTJgIwW095JejoCQ2C422osBni5AsSlbR1bBp0M0x0ZSIjhAlCKAZYzGHrAUS4few2Fzpw=s2048)

- Request Manager: handle requests, partition into batches, assign them to instances, send output to users
- Instance server: monitor the preemption and acquisition of instances
- Meta-context manager: schedule the context migration between GPU instances (parameters, outputs, etc.)

### Meta-context Manager

#### Parallelization Controller

##### Parallelization Configurations:

- **D**: Data Parallelization: partition requests and assign them to different pipelines
- **P**: Pipeline model parallelization: run different stages of a inference process simultaneously (like pipeline in CPU)
- **M**: Tensor model parallelization: split the model into shards and assign to different GPUsParallel 
- Configuration **C** = (D, P, M)	

![img](https://lh7-us.googleusercontent.com/vyC2ivtUjb6jx-4a44Fb-CCILGpqTHIEZMD-sRazYRNajDIa6vzZqFXZHArPoHxWMf5MCoAV3lVPlg2r4O3YBeunuAMUd0tM3K6b-MYiMbfvqvoyXCeVAB_1UdUjL5LFmbTta3KVMqO1xVF8CMCfSj4M0w=s2048)

![img](https://lh7-us.googleusercontent.com/UgHiPq-iqfUTUlfZ_eKKxCdAKfGLZ35NR8BfY9Fysni41dIrpP4mwr2jlxwCZq5_W2W-pTUv4ptNxZ0NTF5lYBbXBIXXdNyrzF5aTfRP-tRd5i6uhKirSBcDYuvl4LGq_TBrEBgU7TpoczmxQTtrBP4weQ=s2048)

##### Configuration Optimizer

![img](https://lh7-us.googleusercontent.com/vzMkndwQq5s5d6cRu5F28hXBWyoeNyr7_5-GZZgR-MG9ntKYYiWoiZLk5JWeBT99xYLULeWop0OObtPAduL5HGPQ2pO0gCz7dqPS3SGl3M0DXXOzbzIbShbMAlZMrdbLLGDV5HoI0EMUM75r5OLS5u0BCw=s2048)

**If** there is a configuration such that the throughput is larger than the request arrival rate, **then** choose the configuration that has minimum inference latency while making sure the throughput is larger than arrival rate. **Otherwise**, find the C for maximum throughput.

**After** adjusting the configuration, allocate or free instances. 

Offline process => low latency

**What do we have now?**

- We now have a configuration for the next step.
- However, we only decided the parallelization structure. 
- How should us decide how to map the physical instances to logical positions?

#### **Device Mapper**

- **Goal**: find the matching strategy that maximize reusable context (i.e., edge weight sum)

![img](https://lh7-us.googleusercontent.com/uIwqLoTUUz66a9PkQfiKllRlJP7g_mo73tNQUaVjOgzxk2FGEubdwFaqxXFLd-CJTUpHL-xhb7aZgu2RdAtBXWbS-1_l_RPJRRJwtD8xm5-b1d_sXZGh0ZbjowZCw59DEeYrJeWKPAxD65Z8Hl4A6kYy_A=s2048)

- **Approach**: *KM (**Kuhn-Munkres) Algorithm* for Bipartite Graph Maximum Weight Matching

![](D:\dingblog\source\img\spotserve\bigraph.png)

edges: e(u, v) indicates reusable parameters and caches when mapping GPU u to position v.

#### **Migration Planner**

![img](https://lh7-us.googleusercontent.com/yUY4QY3JBuc3frOpU9igRxgFR7OXxeromn776cGPdnrL47ib7u-Ixyp4WK22dylnd_ehyGMxLcHoikY0isDt18P4eeiCbd2sNgwIxJj3LJfo8dnhQQZwF6fTUidE8uEnhKw0Aspop76el-NK5_jqDSmHZQ=s2048)

1. From front layers (0, 1) to back layers (N), find the layers whose context do not exceed the buffer size and prioritize migration of these layers. 
2. For other layers, add them to the sequence by the order of instance buffer memory usage
3. After we have the sequence, sequentially migrate the layers in this sequence. If all layers of a specific stage is migrated, start running this stage.

### Just-in-time Arrangement

What should we do if we receive a preemption/acquisition notification while there are still requests running/waiting?

- Immediately suspend and migrate? => high inference latency
- Finish all requests? => no enough time for migration
- **SpotServe**: for preemption, maximize token generation (since migration happens during the grace period); for acquisition, minimize token generation when exploiting the whole grace period (since migration happens after the grace period)

## Experimental Evaluation

### Comparison

- Stable workload

- P99: latency at the 99th percentage (99% latency <= this value)

- Baselines: FasterTransformer (reparallelization but no cocntext migration, rerouting with pre-defined config)

  ![img](https://lh7-us.googleusercontent.com/8wenNdhNJT1VmN05N0gxrNKqk8qT9xCcZa3uZPePmr35xKSSD1U570V3iEtrlei8-zknw3xUJ4sytACMj_iX-t4Q04UHQyTt6tYhYdXreqnYWplghxE_KIWClBYdpqLAyr5ezVns4JNFY-MY1sQ1efa9zw=s2048)

- Fluctuating Workload

  ![img](https://lh7-us.googleusercontent.com/bpEki4DA2MEqzMuhU1nxpj5XOjf6AsjtdWnMpA7DN8GdnbcQd5wVhbgjc6WDs8mFIWf75OlkEMxxBDJ_GAMFwfaL5_imDBvvXSgHAM3jSFm9yIx8n6qqT7F-M2tRLfhvQO3mxoAxPV_3N2msXSgp2ik-Xw=s2048)

### Ablation

![img](https://lh7-us.googleusercontent.com/dpK-WvVOgT4EO9BgpTm3HpxJu1DS_IzSShStV--RNiDmU9RROzZobzd9yWV1HvPpaJ0d-dqn5zp1nXjWn1nbctUI-H46em5TNu3Bz5WdQBKqOrVV_HcIqzoqDdxZkU2A3QGr1cK9LrETho7MrDXPRqKeHg=s2048)

