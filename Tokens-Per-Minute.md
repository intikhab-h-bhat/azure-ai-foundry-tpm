# Tokens Per Minute (TPM) in Azure AI Foundry
In Azure AI Foundry Tokens Per Minute (TPM) is a critical metric for managing model throughput, rate limits, and quota allocation.

## Definition of TPM
* ### 1. Measures Maximum Tokens
  TPM measures the maximum number of tokens a model deployment can process in one minute, combining both input (prompt) and output (generated) tokens
* ### 2. Example Scenario
  A deployment with 30K TPM can handle ~30,000 tokens per minute across all requests.

## Default TPM Limits
Azure OpenAI Models: Varies by model and SKU. For instance:

* #### GPT-4o
 Up to 30M TPM for Enterprise tiers.
* #### GPT-4.1
 5M TPM (Enterprise) / 1M TPM (Default) 
* #### GPT-3.5 Turbo
 10B TPM (Enterprise) / 1B TPM (Default)
* #### Non-OpenAI Models
 e.g., DeepSeek-R1): Fixed at 400K TPM for most models

## TPM Allocation & Quota
Quota is assigned per model, per region, and distributed across deployments. For example:
- A subscription with 240K TPM for GPT-4o in East US can split this into multiple deployments (e.g., two 120K TPM deployments)
- RPM Ratio: Typically 6 RPM per 1K TPM (e.g., 30K TPM ≈ 180 RPM)

## Monitoring and Optimization
* #### Estimate TPM Usage
  Use Azure Monitor metrics (Processed Prompt Tokens + Generated Completion Tokens).
* #### Calculate from API Responses
 json "usage": {"prompt_tokens": 100, "completion_tokens": 50} Multiply by RPM to estimate TPM.
* #### Optimize TPM
 Reduce max_tokens and best_of parameters. Implement retry logic and gradual workload increases.

## How to adjust tokens/minute rate limit
**1 During deployment**
In the Azure AI Foundry portal, when you deploy a model (e.g. GPT‑4o‑mini), you’ll see a setting labeled "Tokens per Minute Rate Limit (thousands)".
- You can slide or enter a value here (e.g., enter 50 to set 50,000 TPM).
- This corresponds directly to the “Rate limit (Tokens per minute)” field — 50 becomes 50,000 TPM. 

**2 Post-deployment edits**
If your model is already deployed, you can edit its deployment configuration to adjust that same slider/field and save changes.
- The two fields (“thousands” and actual TPM) essentially represent the same value, and changing one updates the other

## You Can't Increase It Beyond 50K (Common Case)
Azure enforces quotas on rate limits per subscription, region, and model type. The 50K limit you're seeing is likely the maximum allowed for your current quota.
To Increase the Limit Beyond 50K.You must submit a quota increase request in the Azure Portal.

## What Do These Limits Mean?
**1. Tokens per Minute (TPM) = 50,000**
This controls how much text you can process per minute — including both:
- **Prompt tokens:** The input you send.
- **Completion tokens:** The output you receive from the model.
**Example:**
- If each request consumes 1,000 tokens total (input + output), you can make up to 50 such requests per minute before hitting the token limit.

**2. Requests per Minute (RPM) = 300**
This controls how many individual API calls you can make per minute, regardless of token size.

**Example:**
- You can send up to 300 requests per minute
- But if each request uses 500 tokens, you'll consume 300 × 500 = 150,000 TPM, which would exceed your TPM limit (you'd be throttled)

## Combined Effect
Azure enforces both limits, so you're subject to the more restrictive of the two.

|Scenario	|Token Use	|Requests	|Result|
|---------|---------|---------|---------|
|100 requests × 500 tokens|	50,000|	100|OK – hits TPM but not RPM|
|200 requests × 400 tokens|	80,000|	200|Exceeds TPM (limit = 50,000)|
|300 requests × 100 tokens|	30,000|	300| OK – hits RPM but not TPM|
|310 requests × 100 tokens|	31,000|	310 |Exceeds RPM (limit = 300)|

## What Happens If You Exceed Limits?
- You’ll get HTTP 429 (Too Many Requests) errors.

## Best Practices & Troubleshooting
- Know your limits: Each model and subscription tier has a maximum TPM – e.g. GPT‑4o‑mini default is 2 Million TPM, but your subscription might limit it .
- Throttling awareness: If you hit rate limits in runtime, you’ll get a 429 error. The API returns a Retry-After header indicating how long to wait.
- Need more throughput? Submit a support request to increase quotas (via Azure portal → Quotas and limits tickets)


## Requesting TPM Increases
Submit a quota increase request via !["Azure Support"](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4xPXO648sJKt4GoXAed-0pUMFE1Rk9CU084RjA0TUlVSUlMWEQzVkJDNCQlQCN0PWcu), specifying:
- Model name/version and deployment type
- Target TPM/RPM and workload rationale



## Scenario
If you're getting "limit exceeded" errors even though your prompt is ~1,000 tokens and your limits are:
- Tokens per Minute (TPM): 50,000
- Requests per Minute (RPM): 300
- knowledge files(no of files uploaded eg 51)
…and you’re still within these limits, the likely cause is your knowledge files.

**What’s Actually Happening?**
When using Azure AI Foundry with a “Knowledge” resource (like your 51 files):
The system automatically augments your prompt using a Retrieval-Augmented Generation (RAG) pipeline:
- It searches the knowledge files.
- It adds relevant chunks from those files to your original prompt before sending it to the model.

  As a result:

**Your effective prompt size becomes:**
Prompt tokens + Retrieved knowledge tokens

So, even if your raw prompt is ~1,000 tokens, the total tokens per request could easily be:
3,000–5,000 tokens, or even more — depending on how many documents are retrieved and included in the augmented prompt.
 
**Example**
  |Type     |	Tokens  |
  |---------|---------|
  |Your prompt|	1,000|
|Retrieved content (from 51 files)|	~3,000|
|Completion (response)|	~1,000|
|Total tokens|	~5,000|

If you're making just 10 requests like this per minute, you're already at:
10 × 5,000 = 50,000 TPM → your full quota

**How to Fix or Optimize**

🔹 **Option 1:** Reduce retrieved knowledge tokens
In Azure Foundry, edit your knowledge settings to:
Limit the number of retrieved chunks.
Set a maximum token budget for augmentation (e.g., 2,000)

🔹 **Option 2:** Use a lower context prompt
Only pull the most necessary files.
Filter files before uploading or querying.

🔹 **Option 3:** Increase your quota
Submit a request via Azure Portal to increase TPM and/or RPM.

**Live Debugging**
In Azure AI Studio (Foundry), open:

- **"Prompt flow → Inspect"**
 It shows the exact token count per step, including:
 1. Input tokens
 2. Retrieved content tokens
 3. Output tokens













