# Tokens Per Minute (TPM) in Azure AI Foundry
In Azure AI Foundry Tokens Per Minute (TPM) is a critical metric for managing model throughput, rate limits, and quota allocation.

## Definition of TPM
### 1. Measures Maximum Tokens
- TPM measures the maximum number of tokens a model deployment can process in one minute, combining both input (prompt) and output (generated) tokens
### 2. Example Scenario
- A deployment with 30K TPM can handle ~30,000 tokens per minute across all requests.

## Default TPM Limits
Azure OpenAI Models: Varies by model and SKU. For instance:

#### GPT-4o
Up to 30M TPM for Enterprise tiers.
#### GPT-4.1
5M TPM (Enterprise) / 1M TPM (Default) 
#### GPT-3.5 Turbo
10B TPM (Enterprise) / 1B TPM (Default)
#### Non-OpenAI Models
e.g., DeepSeek-R1): Fixed at 400K TPM for most models






