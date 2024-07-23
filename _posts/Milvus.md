---
title: Milvus, A Purpose-Built Vector Data Management System
tags: [vector database, cpu-gpu co-design]
categories: [vector_db]
date: [2024-02-02 17:15:00]
index_img: /img/milvus/Milvus.jpg

---

# Milvus: A Purpose-Built Vector Data Management System

Author: Jianguo Wang ,et al.

### vector database

![img](https://lh7-us.googleusercontent.com/a1SSv6X8vFe2r4xKdwJtEs4gQZn1wZ0RoCCBWQeNyRfY5XoMlgPK3WDeeIylBIza92CYhsbici0HTGf0iNeIUZj-SRb5eqZQfXEX6QLCDCKpdtDAEUE06VT4zOJdThJPJJJGqjWh_1Fwqhx4e210APvifQ=s2048)

A new type of database specialized in storing and querying vector data. 

- Traditional relational databases: precise attribute-based searches

- Vector database: approximate similarity-based vector searches: find the k-nearest vectors to the query.

### Background & Motivation

![](D:\dingblog\source\img\milvus\motivation.png)

### Challenges

Current works have the following limitations:

- They are not full-fledged systems and cannot handle large amount of data
- They cannot easily handle dynamic data while ensuring fast real-time searches
- They do not support advanced query processing
- They are not optimized for heterogeneous computing architecture with CPUs and GPUs

### Milvus

Milvus: a purpose-built vector data management system for managing large-scale and dynamic vector data to enable data science and AI applications.

Milvus incorporates query processing, indexing and storage management and CPU-GPU codesign into the whole system, successfully addressing the previous issues.

#### Overall Architecture of Milvus

![img](https://lh7-us.googleusercontent.com/fLfYZw5bi5SJqc526JvvWF9Cx-v3_mwoboDJgCSvyPyQZPXp9-9V09R0zzmJnSR_IJ5VzQ2cdgPujHesMvg7GIOmnH67yceAwLh3BDXYypLXBQCsHiMAfkhMSBf8fxVTnx-vCywD4YGqTruMwE_VB0XuzQ=s2048)

#### Query Processing

Support of query types:

- Vector Query (1 vector)
- **Attribute Filtering** (1 vector + n attribute constraints)**(Advanced Query)**
- **Multi-vector Query** (n vectors)**(Advanced Query)**

Support of similarity functions:

- Euclidean distance
- Inner product
- Cosine similarity
- Hamming distance
- Jaccard distance

#### Attribute Filtering

##### ![img](https://lh7-us.googleusercontent.com/PIxpQQ5Z7gQ1MACU9tAa6PSjklRxVUxmoLkX62A7-mFhSS_PPCgg7QmXoNU8hQZtn8-MkIa9QyzLXOIF8IZniVmDClHUvkxCUH88iuBP7SFyVzzMtErNFWB_WINUlC6e9gFy6QyJZ2KQZvAd59vPHIgMiA=s2048)

- A: ①obtain the satisfied vectors②fully scann them

- B: ①obtain the satisfied vectors②use bitmap to find the answer

- C: ①obtain more than k results②fully scan and find those satisfy the attribute constraint

- D: estimate the cost of A,B,C and find the best one
- E (Milvus): Partition-based filtering
  1. Create partitions for data
  2. Maintain min-max indexes for different partitions
  3. Skip the partitions whose range of attribute does not overlap with the queries’
  4. Find the overlapping partitions, and apply strategy D(cost-based) within each partition

#### Multi-vector Queries

- ##### For decomposable similarity functions: Use aggregate functions to concentrate the vectors and find the k nearest neighbors based on the concentrated vector (vector fusion)

- For indecomposable similarity functions:

![img](https://lh7-us.googleusercontent.com/2nbN36rss_FJXsQqeqROW-fWUY1oggQ7aHsP796HQdaQCTObmj09hl2koBem7ygklF9_iVH3Jg3A2GCUYbjmSQozPgdqe2jIgjqc8YCE9-HBJT_EQw1hK6NOTqXWCXhPynJHyr4CnnaMFppibQlTjMcZaA=s2048)

#### Indexing (Querying)

- Quantization-based Indexing: (Quantization: represent vectors with a codebook. First find n nearest clusters to q, then use the indexing to search within these clusters.)

  - IVF_FLAT

  - IVF_SQ8

  - IVF_PQ

    ![img](https://lh7-us.googleusercontent.com/cQ7gqM4L-mFpA5ciP5EhImJx1pEpdO4lO5yWXXARHCgnVeDfLJcU8feY_Uc-ivjsKUzS-h5p_tGCnv5nSRARPKwspRN9DCXtt3_WhkmQZLdow7A7J97vm1QItFTehk6aFpiek_Qc7ow7X_wTIdxyjW90LQ=s2048)

- Graph-based indexing:

  - HNSW
  - RNSG

#### Vector and Attribute Storage

To facilitate query processing, Milvus stores vectors and attributes in a columnar fasion.

- **Vectors**: different vectors of entities are stored together correspondingly. 
- **Attributes**: <attr value, row ID> pairs + skipping indexes (min-max) for pages

![](D:\dingblog\source\img\milvus\storage.png)

Milvus also relies on LRU-based buffer and supports multiple file systems.

#### Optimizations for Heterogeneous Computing

How to find the top k similar vectors for multiple queries efficiently?

- CPU-oriented Optimization
- GPU-oriented Optimization
- CPU-GPU codesign

#### CPU-oriented Optimization

- ##### **Faiss** leveraged OpenMP to realize multi-threading. Each thread process a query at a time. Two issues:

  - High cache-missing rate (requires to stream the entire data into caches, cannot reuse)
  - Cannot fully exploit cores when query number is small

- **Milvus** assign threads to data vectors instead of query vectors to address the 2nd issue.

  - Divide the queries into blocks s.t. each query block and its heaps can fit in L3 cache.
  - Results are managed in heaps. To obtain the final results, merge the result.
  - Milvus is also capable of automatically choosing SIMD instructions for functions.

##### GPU-oriented Optimization

- **Faiss**: k<=1024 because of the limit of memory
- **Milvus**: divide k into different rounds, each round expanding the obtained result set until reaches the required number.Faiss: must set the number of GPUs in advance; dynamic segment-based scheduling that assign search tasks to available devices

##### CPU-GPU Co-design

- Limitations of **Faiss** (IVF_SQ8):Low PCIe bandwidth utilization (1 bucket per time)Data transfer makes GPU computing unbeneficial

- **Milvus** (SQ8H):High bandwidth utilization (Multiple buckets loaded per time)Assess the running mode (GPU-only or hybrid) based on the batch size

  ![img](https://lh7-us.googleusercontent.com/1bi-T_L1k4IU-SxJ1jBMFvNr0yG9j_xd_6dVzgkk1XvZyRNptDrTs4MYPhyZX4lzrCA2enF8bQ06sYe_SKCdhR3TiuSA8dA4eesCGFZ2b6ZjtxB5ptqr-3KiTfyS7FFMEh6MNxuXI2IpTG9N2G3Uj-GS4A=s2048)

### Applications

- Recommender System
- **Image Recognition**
- **Chemical Structure Analysis**
- etc.

### Evaluation

#### Evaluation (Comparing with other systems)

- 10M vectors from SIFT1B and Deep1B
- 10000 random queries

![img](https://lh7-us.googleusercontent.com/ENprp6rC88V22r2w7OkwVJ_iQCRo3JWz16ulMGvlaUWXf7yeBZPw4LG3N0ntxUXmMXHia-7ZOKVW5rLj6407GOoVLfTZPbeQTnDKER5bW9ImUvkkMiJvF9C8R2ngaaEovkgmOvAsRdMBW891rUByVLa5ZA=s2048)

#### Evaluation: cache-aware design

![img](https://lh7-us.googleusercontent.com/5SyAZyajVqTUSdJOh2ROCylw6dl8Ri1EmLd-Fzz539KXGJq4XVcXVBfslC23JIFzRQXnGBSd2hXKl-A43FQoC66goZvL_nzjsZLeNt-PeWhFulHk_DyNN3TkoZ53SboSvDcVOra9R9dIpMwLmUa2Wg3aAA=s2048)

#### Evaluation-Attribute Filtering

![img](https://lh7-us.googleusercontent.com/-m3podLKsG3KUBocsv_qeZMc1hfrs8SLUhxwradn7zReuSe3t9-Zy7we5VspsTUWl_lQvsFPXfIDb7dNdcvl5-dalb92GlQ1lm5bempLPhhgkqNyiDP2yhU2Gl6ydjX4QF7RU_5VOqgKLL66uYwzRuGsrw=s2048)

