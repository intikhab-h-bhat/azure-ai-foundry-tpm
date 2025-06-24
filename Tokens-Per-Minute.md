# Tokens Per Minute (TPM) in Azure AI Foundry
In Azure AI Foundry (now part of Azure AI Studio), Tokens Per Minute (TPM) is a critical metric for managing model throughput, rate limits, and quota allocation.

## Definition of TPM
### 1. Measures Maximum Tokens
- TPM measures the maximum number of tokens a model deployment can process in one minute, combining both input (prompt) and output (generated) tokens
### 2. Example Scenario
- A deployment with 30K TPM can handle ~30,000 tokens per minute across all requests.
