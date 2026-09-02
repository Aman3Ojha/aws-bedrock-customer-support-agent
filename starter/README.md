# Project Starter Files

This folder contains the infrastructure and setup scripts for the Customer Support Chatbot project.

## Setup Order

1. `cloudformation-tool.yaml` — Deploy DynamoDB + Lambda + IAM roles
2. `setup_gateway.py` — Create AgentCore Gateway and register the Lambda tool
3. `create_harness.py` — Create/update the AgentCore managed harness from system_prompt.txt
4. `chat.py` — Test the chatbot in a multi-turn terminal session
5. `generate-eval-dataset.py` — Run test suite and produce JSONL for Bedrock Evaluations
6. `cloudformation-testing.yaml` — Deploy S3 bucket + evaluation role
7. `cleanup_agentcore.py` — Delete harness, gateway target, and gateway

## Files

| File | Description |
|---|---|
| `cloudformation-tool.yaml` | Creates DynamoDB table, Lambda function, and IAM roles |
| `cloudformation-testing.yaml` | Creates S3 bucket and Bedrock evaluation IAM role |
| `create_bug_report.py` | Lambda function code — writes bug reports to DynamoDB |
| `setup_gateway.py` | Creates AgentCore Gateway and registers create_bug_report tool |
| `create_harness.py` | Creates or updates the AgentCore managed harness |
| `chat.py` | Multi-turn terminal chat client |
| `generate-eval-dataset.py` | Runs test suite through harness, produces JSONL output |
| `cleanup_agentcore.py` | Deletes all AgentCore resources |
| `online_shop_faq.md` | FAQ document embedded in the system prompt |
| `requirements.txt` | Python dependencies (boto3 1.43.76+) |
| `harness-tests-template.json` | Template for creating your test suite |

## Requirements

- AWS CLI v2
- Python 3.9+
- boto3 >= 1.43.76
- AWS account with Bedrock + AgentCore access in us-east-1
- Nova Pro model enabled in Bedrock Model access
