

# Bedrock Image Description — LangFlow Flow

This repo contains a LangFlow flow that uses Amazon Bedrock (via `langchain_aws` / `boto3`) to analyze images and generate detailed image descriptions. The flow is exported as JSON and intended to be run/edited in LangFlow (or used as a reference to implement similar pipelines programmatically).

---

## Contents

* `bedrock-img-desc.json` — LangFlow flow export (nodes: Chat Input, Prompt Template, Amazon Bedrock model, Chat Output). 
* `.env.example` — example environment variables (create `.env` from this)
* `README.md` — this file

---

## Features

* Accepts user text input (and optionally files/images) from a Chat Input node.
* Uses a Prompt Template to structure the image analysis prompt.
* Sends the prompt to an Amazon Bedrock model (configurable model id).
* Outputs a detailed description / analysis via Chat Output node.

---

## Prerequisites

* Python 3.10+ (if you run any local scripts leveraging the LangChain / AWS SDK)
* LangFlow (if you plan to open and edit the flow visually)
* `boto3` and `langchain_aws` (for Bedrock access in Python)
* An AWS account with Bedrock access and credentials (Access Key / Secret / optional Session Token)
* (Optional) `pipenv` or `venv` for virtualenv management

---

## Recommended Python packages

```text
boto3
langchain_aws
orjson
fastapi   # if using local FastAPI wrappers
langflow  # if you're working with the project codebase locally
python-dotenv  # for loading .env
```

---

## Environment variables

Create a `.env` file from `.env.example` and set your credentials:

```env
AWS_ACCESS_KEY_ID=YOUR_AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY=YOUR_AWS_SECRET_ACCESS_KEY
AWS_SESSION_TOKEN=OPTIONAL_SESSION_TOKEN
AWS_REGION=us-east-1
BEDROCK_MODEL_ID=anthropic.claude-3-haiku-20240307-v1:0
BEDROCK_ENDPOINT_URL=        # set only if you have a custom endpoint
```

> **Security note:** Never commit real credentials to source control. Use GitHub Secrets or other secrets managers for CI / deployments.

---

## How to open and use the flow (LangFlow)

1. Install LangFlow following official LangFlow docs.
2. Open LangFlow UI and import `bedrock-img-desc.json` via *Flows → Import*.
3. Inspect nodes:

   * **Chat Input** — user message & files
   * **Prompt Template** — the prompt template used to ask the model to analyze images
   * **Amazon Bedrock** — the LLM node (configure `model_id`, region, credentials)
   * **Chat Output** — receives model text and displays it
4. Configure the Amazon Bedrock node with credentials (via environment variables or node inputs).
5. Run/play the flow from LangFlow Playground to test.

---

## Example usage (programmatic)

If you want to call Bedrock programmatically (outside of LangFlow), a minimal pattern using `boto3` + `langchain_aws`:

```python
from langchain_aws import ChatBedrock
import boto3
import os

session = boto3.Session(
    aws_access_key_id=os.environ["AWS_ACCESS_KEY_ID"],
    aws_secret_access_key=os.environ["AWS_SECRET_ACCESS_KEY"],
    aws_session_token=os.environ.get("AWS_SESSION_TOKEN"),
    region_name=os.environ.get("AWS_REGION", "us-east-1"),
)
client = session.client("bedrock-runtime", region_name=os.environ.get("AWS_REGION"))

chat = ChatBedrock(client=client, model_id=os.environ.get("BEDROCK_MODEL_ID"))

prompt = """Analyze the following image and describe it thoroughly.

1. What is the main subject?
2. Describe the setting and background.
3. Analyze the mood, lighting, and colors.
4. Provide a concise summary of what the image is about."""

# The method you call depends on langchain_aws/ChatBedrock API — consult its docs for exact usage.
```

---

## Known issues & troubleshooting

* **KeyError: "title" in Prompt Template**
  If you see `KeyError: "title"` in the Prompt Template component (LangFlow error), check the Prompt Template node in the flow — ensure its `template` field is present and not empty and that any expected labels are set. In some exports an empty `name` or missing template field can cause LangFlow to attempt to access a `title` field that doesn't exist. Re-open the Prompt Template node, verify the template text and any custom fields, then re-run.

* **Auth / connection errors**

  * `botocore` / `boto3` errors usually mean credentials or region are misconfigured.
  * If Bedrock access is blocked, confirm your AWS account has Bedrock enabled and that the IAM role / user has Bedrock runtime permissions.

* **Missing packages**
  If you get `ImportError` for `langchain_aws` or `boto3`, install them with:

  ```bash
  pip install boto3 langchain_aws
  ```

---

## Contributing

1. Fork the repo.
2. Create a branch: `git checkout -b feat/my-change`
3. Commit changes: `git commit -m "Describe change"`
4. Push: `git push origin feat/my-change`
5. Open a pull request.

If you update the flow in LangFlow, export the updated JSON and include it in the repo (`bedrock-img-desc.json`).

---

## How to push to GitHub (quick)

```bash
# initialize (if not already)
git init
git add .
git commit -m "Initial commit — LangFlow Amazon Bedrock image description flow"

# create remote and push
git remote add origin git@github.com:YOUR_USERNAME/YOUR_REPO.git
git branch -M main
git push -u origin main
```

If you have a `.gitignore`, ensure it excludes `.env`, credentials, and other sensitive files.

---

## References & source

This repository contains the LangFlow flow export `bedrock-img-desc.json` (nodes and settings). 

---

