![image](https://github.com/user-attachments/assets/301113f6-1273-4e67-a8c2-5106af8aa08f)# Tokens Per Minute (TPM) in Azure AI Foundry
In Azure AI Foundry Tokens Per Minute (TPM) is a critical metric for managing model throughput, rate limits, and quota allocation.

## Definition of TPM
* ### 1. Measures Maximum Tokens
- TPM measures the maximum number of tokens a model deployment can process in one minute, combining both input (prompt) and output (generated) tokens
* ### 2. Example Scenario
- A deployment with 30K TPM can handle ~30,000 tokens per minute across all requests.

## Default TPM Limits
Azure OpenAI Models: Varies by model and SKU. For instance:

* #### GPT-4o
- Up to 30M TPM for Enterprise tiers.
* #### GPT-4.1
- 5M TPM (Enterprise) / 1M TPM (Default) 
* #### GPT-3.5 Turbo
- 10B TPM (Enterprise) / 1B TPM (Default)
* #### Non-OpenAI Models
- e.g., DeepSeek-R1): Fixed at 400K TPM for most models

## TPM Allocation & Quota
Quota is assigned per model, per region, and distributed across deployments. For example:
- A subscription with 240K TPM for GPT-4o in East US can split this into multiple deployments (e.g., two 120K TPM deployments)
- RPM Ratio: Typically 6 RPM per 1K TPM (e.g., 30K TPM ≈ 180 RPM)

## Monitoring and Optimization
* #### Estimate TPM Usage
- Use Azure Monitor metrics (Processed Prompt Tokens + Generated Completion Tokens).
* #### Calculate from API Responses
- json "usage": {"prompt_tokens": 100, "completion_tokens": 50} Multiply by RPM to estimate TPM.
* #### Optimize TPM
- Reduce max_tokens and best_of parameters. Implement retry logic and gradual workload increases.

## Requesting TPM Increases
Submit a quota increase request via Azure Support, specifying:
- Model name/version and deployment type
- Target TPM/RPM and workload rationale


 



















