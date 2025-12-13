---
layout: "contents"
title: Supported Evaluation Strategies
firstpage:
---

# Supported Evaluation Strategies

This document identifies which strategies from the Unified Evaluation Workflow are natively supported by Meta-World. A strategy is considered "supported" only if the harness provides it natively in its full installation—meaning that once Meta-World is fully installed, the strategy can be executed directly without implementing custom modules or integrating external libraries.

## Phase 0: Provisioning (The Runtime)

### Step A: Harness Installation

**Supported Strategies:**

- ✅ **Strategy 1: Git Clone** - Meta-World can be installed by cloning the repository and installing from source:
  ```bash
  git clone https://github.com/Farama-Foundation/Metaworld.git
  cd Metaworld
  pip install -e .
  ```
  Documentation: [Installation Guide](../installation/installation.md)

- ✅ **Strategy 2: PyPI Packages** - Meta-World is available via PyPI and can be installed using pip:
  ```bash
  pip install metaworld
  ```
  Documentation: [README.md](../../README.md#installation)

**Unsupported Strategies:**

- ❌ **Strategy 3: Node Package** - Not applicable (Python-based harness)
- ❌ **Strategy 4: Binary Packages** - Not provided
- ❌ **Strategy 5: Container Images** - While there is a docker directory in the repository, it contains legacy Docker configurations that are not maintained for Meta-World

### Step B: Service Authentication

**Unsupported Strategies:**

- ❌ **Strategy 1: API Provider Authentication** - Meta-World is a simulation-based benchmark and does not require or support API-based model inference
- ❌ **Strategy 2: Repository Authentication** - Meta-World does not require authentication with model/dataset repositories as it provides its own simulated environments
- ❌ **Strategy 3: Evaluation Platform Authentication** - Meta-World does not integrate with external evaluation platforms or leaderboard submission APIs

## Phase I: Specification (The Contract)

### Step A: SUT Preparation

**Supported Strategies:**

- ✅ **Strategy 4: Policy/Agent Instantiation (Stateful Controllers)** - Meta-World is specifically designed for evaluating reinforcement learning policies and agents. Users instantiate their own trained policies/agents and evaluate them using Meta-World's environments and evaluation utilities.
  
  Documentation: [Evaluation Guide](./evaluation.md)

**Unsupported Strategies:**

- ❌ **Strategy 1: Model-as-a-Service (Remote Inference)** - Not supported; Meta-World is for RL policy evaluation, not remote model inference
- ❌ **Strategy 2: Model-in-Process (Local Inference)** - While users load their own trained RL policies, Meta-World does not provide built-in functionality for loading model weights from common frameworks
- ❌ **Strategy 3: Algorithm Implementation (In-Memory Structures)** - Not applicable; Meta-World evaluates learned policies, not algorithmic data structures

### Step B: Benchmark Preparation (Inputs)

**Supported Strategies:**

- ✅ **Strategy 3: Simulation Environment Setup (Simulated)** - Meta-World's core functionality is providing simulated robotic manipulation environments. It includes:
  - 50 distinct robotic manipulation tasks
  - Multiple benchmarks (MT1, MT10, MT50, ML1, ML10, ML45)
  - Configurable goal positions and object layouts
  - Train/test environment splits for meta-learning
  
  Documentation: [Benchmark Descriptions](../benchmark/benchmark_descriptions.md), [Basic Usage](../introduction/basic_usage.md)

**Unsupported Strategies:**

- ❌ **Strategy 1: Benchmark Dataset Preparation (Offline)** - Meta-World uses simulation environments rather than offline datasets
- ❌ **Strategy 2: Synthetic Data Generation (Generative)** - While environments can be reset with different goal positions, Meta-World does not provide built-in synthetic data generation or perturbation capabilities
- ❌ **Strategy 4: Production Traffic Sampling (Online)** - Not applicable; Meta-World is simulation-based, not production traffic-based

### Step C: Benchmark Preparation (References)

**Supported Strategies:**

- ✅ **Strategy 1: Ground Truth Preparation** - Meta-World provides:
  - Success flags for each task (available in the `info` dictionary)
  - Task-specific goal conditions
  - Expert policies for all 50 tasks that can serve as reference implementations
  
  Documentation: [Evaluation Guide](./evaluation.md), [Expert Trajectories](../benchmark/expert_trajectories.md)

**Unsupported Strategies:**

- ❌ **Strategy 2: Judge Preparation** - Meta-World does not include or support loading judge models for evaluation

## Phase II: Execution (The Run)

### Step A: SUT Invocation

**Supported Strategies:**

- ✅ **Strategy 1: Batch Inference** - Meta-World supports batch evaluation through vectorized environments:
  - Synchronous vector environments (`vector_strategy='sync'`)
  - Asynchronous vector environments (`vector_strategy='async'`)
  - The `metaworld.evaluation.evaluation()` function evaluates policies across multiple episodes
  
  Documentation: [Basic Usage](../introduction/basic_usage.md), [Evaluation Guide](./evaluation.md)

- ✅ **Strategy 3: Interactive Loop** - Meta-World's core evaluation paradigm is interactive:
  - Policies interact with environments through state-action loops
  - Support for meta-learning with adaptation phases
  - The `metaworld.evaluation.metalearning_evaluation()` function implements iterative adaptation and evaluation
  
  Documentation: [Evaluation Guide](./evaluation.md)

**Unsupported Strategies:**

- ❌ **Strategy 2: Arena Battle** - Meta-World does not support simultaneous evaluation of multiple policies on the same inputs for direct comparison
- ❌ **Strategy 4: Production Streaming** - Not applicable; Meta-World is simulation-based

## Phase III: Assessment (The Score)

### Step A: Individual Scoring

**Supported Strategies:**

- ✅ **Strategy 1: Deterministic Measurement** - Meta-World natively computes:
  - Success flags (binary success/failure per episode)
  - Episodic rewards
  - Task completion metrics
  
  All environments provide a success signal through `info['success']` which is deterministically computed based on task-specific completion criteria.
  
  Documentation: [Evaluation Guide](./evaluation.md), [Reward Functions](../benchmark/reward_functions.md)

**Unsupported Strategies:**

- ❌ **Strategy 2: Embedding Measurement** - Not supported; Meta-World does not compute semantic similarity or embedding-based metrics
- ❌ **Strategy 3: Subjective Measurement** - Not supported; Meta-World does not include judge models or subjective evaluation capabilities
- ❌ **Strategy 4: Performance Measurement** - While environments execute and could theoretically be timed, Meta-World does not natively track or report resource consumption, latency, throughput, or computational costs

### Step B: Collective Aggregation

**Supported Strategies:**

- ✅ **Strategy 1: Score Aggregation** - Meta-World's evaluation utilities compute:
  - Mean success rate across all tasks and episodes
  - Mean returns across all tasks and episodes
  - Per-task success rates
  - Per-task returns
  
  The `evaluation()` and `metalearning_evaluation()` functions return these aggregate metrics.
  
  Documentation: [Evaluation Guide](./evaluation.md)

**Unsupported Strategies:**

- ❌ **Strategy 2: Uncertainty Quantification** - Meta-World does not compute confidence intervals, bootstrap resampling, or other uncertainty quantification metrics

## Phase IV: Reporting (The Output)

### Step A: Insight Presentation

**Unsupported Strategies:**

- ❌ **Strategy 1: Execution Tracing** - Meta-World does not capture or display detailed execution logs or intermediate states
- ❌ **Strategy 2: Subgroup Analysis** - While evaluation results are broken down by task, Meta-World does not provide built-in stratification by demographic groups, data domains, or other dimensions
- ❌ **Strategy 3: Regression Alerting** - Not supported; no automatic comparison against historical baselines or alerting
- ❌ **Strategy 4: Chart Generation** - Meta-World does not generate visualizations like radar charts, histograms, or performance plots
- ❌ **Strategy 5: Dashboard Creation** - Meta-World does not provide interactive web interfaces or dashboards
- ❌ **Strategy 6: Leaderboard Publication** - Meta-World does not integrate with or submit results to leaderboards

## Summary

Meta-World provides native support for **9 strategies** in the Unified Evaluation Workflow:

**Supported:**
1. Phase 0-A: Git Clone
2. Phase 0-A: PyPI Packages
3. Phase I-A: Policy/Agent Instantiation
4. Phase I-B: Simulation Environment Setup
5. Phase I-C: Ground Truth Preparation
6. Phase II-A: Batch Inference
7. Phase II-A: Interactive Loop
8. Phase III-A: Deterministic Measurement
9. Phase III-B: Score Aggregation

Meta-World is specifically designed as a simulated benchmark for reinforcement learning policy evaluation, focusing on robotic manipulation tasks. Its strengths lie in providing high-quality simulation environments, standardized evaluation protocols, and utilities for both multi-task and meta-learning evaluation scenarios.
