# Satori-SWE: Evolutionary Test-Time Scaling for Sample-Efficient Software Engineering

[![Homepage](https://img.shields.io/badge/🏠-Homepage-3C47EB.svg)](https://satori-reasoning.github.io/) &nbsp;&nbsp; [![HuggingFace](https://img.shields.io/badge/🤗-Model&Demo-E87948.svg)](https://huggingface.co/Satori-reasoning) &nbsp;&nbsp; [![Paper](https://img.shields.io/badge/arXiv-paper-b31b1b)](https://huggingface.co/Satori-reasoning) &nbsp;&nbsp;



## News

- **[2025/05/29]** We have released our model and data through [HuggingFace](https://huggingface.co/Satori-reasoning). The code will be released soon, within one month.

## **Introduction**

LLMs perform well on coding benchmarks like LiveCodeBench but struggle with real-world software engineering (SWE) tasks. Even large models like Claude reach only around 60\% accuracy on SWE-bench, despite using carefully engineered prompting pipelines. Smaller models (under 100B parameters) perform significantly worse, typically scoring below 10\% in zero-shot settings and plateauing around 30\% after supervised fine-tuning (SFT) on GitHub issue datasets. Improving the performance of these small models remains a key challenge for practical deployment, where repeatedly querying large models is often too costly or inefficient.

Existing approaches primarily rely on supervised fine-tuning (SFT) with high-quality data, which is expensive to curate at scale. An alternative is test-time scaling: generating multiple outputs, scoring them using a verifier, and selecting the best one. Although effective, this strategy often requires excessive sampling and costly scoring. 

This work aims to explore a new research direction: sample-efficient test-time scaling methods that can identify correct solutions with fewer samples. We propose Evolutionary Test-Time Scaling (EvoScale) that treats generation as an evolutionary process. By iteratively refining outputs via selection and mutation, EvoScale shifts the output distribution toward higher-scoring regions. Our approach results in Satori-SWE-32B, a 32B model trained on open-source model and open-source data. Key features of Satori-SWE include:


## Key Features

- A new perspective of formulating test-time scaling as an evolutionary process, improving sample efficiency for software engineering tasks.
- A novel RL training approach that enables self-evolution, eliminating the need for external reward models or verifiers at inference time.
- Satori-SWE-32B with EvoScale achieves performance comparable to models exceeding 100B parameters, while requiring only a small number of samples.

## Training Framework

### **1. Small-scale Mutation SFT**

- **Classical SFT**: fine-tune a base model on inputs consisting of the issue description and code context, with targets that include a chain-of-thought (CoT) trace and the ground-truth patch.
- **Mutation SFT**: fine-tune a second model, using inputs and a set of conditioning examples consisting of patches sampled from the classical SFT model, write an improved patch.

### **2. Large‑Scale RL for Self‑Evolution**

- **Potential-based Reward Shaping**: further fine-tune the mutation SFT model to maximize the expected potential rewards in score between a newly generated patch and a previous patch.

## **Evaluation**

### **SWE‑Bench‑Verified**

We use the *Qwen2.5-Coder-32B-Instruct model* as our base model for training Satori-SWE-32B. Through the two-stage SFT training and RL training, Satori-SWE-32B outperforms all small-scale models under greedy decoding, while achieving comparable performance with current SOTA SWE-RL with smaller model scale (32B v.s. 70B),  much fewer training data (30K v.s. million-scale) and test-time scaling samples (50 v.s. 500).

<table>
  <thead>
    <tr>
      <th style="width: 30%; text-align: center;">Model</th>
      <th style="width: 15%; text-align: center;">Params</th>
      <th style="width: 15%; text-align: center;">Best@N</th>
      <th style="width: 20%; text-align: center;">Accuracy (%)</th>
    </tr>
  </thead>
  <tbody>
    <tr><td style="text-align: center;">GPT‑4o (Agentless)</td><td style="text-align: center;">–</td><td style="text-align: center;">1</td><td style="text-align: center;">38.8</td></tr>
    <tr><td style="text-align: center;">Claude 3.5 (Agentless)</td><td style="text-align: center;">–</td><td style="text-align: center;">1</td><td style="text-align: center;">50.8</td></tr>
    <tr><td style="text-align: center;">DeepSeek‑V3 (Agentless)</td><td style="text-align: center;">–</td><td style="text-align: center;">–</td><td style="text-align: center;">42.0</td></tr>
    <tr><td style="text-align: center;">SWE‑Fixer</td><td style="text-align: center;">72 B</td><td style="text-align: center;">1</td><td style="text-align: center;">30.2</td></tr>
    <tr><td style="text-align: center;">SWE‑Gym‑32B</td><td style="text-align: center;">32 B</td><td style="text-align: center;">1</td><td style="text-align: center;">20.6</td></tr>
    <tr><td style="text-align: center;">SWE‑Gym‑32B</td><td style="text-align: center;">32 B</td><td style="text-align: center;">16</td><td style="text-align: center;">32.0</td></tr>
    <tr><td style="text-align: center;">Llama‑3 SWE‑RL</td><td style="text-align: center;">70 B</td><td style="text-align: center;">80</td><td style="text-align: center;">37.0</td></tr>
    <tr><td style="text-align: center;">Llama‑3 SWE‑RL</td><td style="text-align: center;">70 B</td><td style="text-align: center;">500</td><td style="text-align: center;">41.0</td></tr>
    <tr><td style="text-align: center;"><b>Satori‑SWE‑32B</b></td><td style="text-align: center;"><b>32 B</b></td><td style="text-align: center;"><b>1</b></td><td style="text-align: center;"><b>35.8</b></td></tr>
    <tr><td style="text-align: center;"><b>Satori‑SWE‑32B</b></td><td style="text-align: center;"><b>32 B</b></td><td style="text-align: center;"><b>10</b></td><td style="text-align: center;"><b>38.9</b></td></tr>
    <tr><td style="text-align: center;"><b>Satori‑SWE‑32B</b></td><td style="text-align: center;"><b>32 B</b></td><td style="text-align: center;"><b>25</b></td><td style="text-align: center;"><b>40.2</b></td></tr>
    <tr><td style="text-align: center;"><b>Satori‑SWE‑32B</b></td><td style="text-align: center;"><b>32 B</b></td><td style="text-align: center;"><b>50</b></td><td style="text-align: center;"><b>41.6</b></td></tr>
  </tbody>
</table>

## **Satori Team Members**
<span>$&#8224;$</span>: Project lead
### **Core Contributors**
- [Guangtao Zeng, SUTD](https://chaoscodes.github.io/)
- [Maohao Shen, MIT](https://maohaos2.github.io/Maohao/)
- [Zhang-Wei Hong<span>&#8224;</span>, MIT](https://williamd4112.github.io/)
### **Contributors**
- Delin Chen, UMass Amherst
- Zhenting Qi, Harvard
- Wei Lu, SUTD
- Gregory W. Wornell, MIT
- Subhro Das, MIT-IBM Watson AI Lab
- David Cox, MIT-IBM Watson AI Lab
- Chuang Gan<span>$^&#8224;$</span>, UMass, MIT-IBM Watson AI Lab

## **Contact Information**

For questions, please:
- Raise an issue in our GitHub repository
- Contact us at: satori2025@outlook.com


## **Citation**
```

```
