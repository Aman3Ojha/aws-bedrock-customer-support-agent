# Customer Support Chatbot with Amazon Bedrock AgentCore

![AWS](https://img.shields.io/badge/AWS-Amazon%20Bedrock-orange?logo=amazon-aws)
![Python](https://img.shields.io/badge/Python-3.13-blue?logo=python)
![DynamoDB](https://img.shields.io/badge/Database-DynamoDB-blue?logo=amazon-dynamodb)
![Lambda](https://img.shields.io/badge/Serverless-Lambda-orange?logo=aws-lambda)

A production-grade customer support chatbot built on **Amazon Bedrock AgentCore** and **Amazon Bedrock Flows** for a fictional online shop. The chatbot routes each customer message to exactly one of three behaviors, collects bug diagnostics across multi-turn conversations, persists tickets to DynamoDB via a Lambda tool, and answers FAQ questions from a grounded document.

Built as part of the **AWS AI & ML Scholars — Future AWS Agent Engineer Nanodegree** (Udacity, 2026).

---

## Architecture

```
Customer message
       │
       ▼
AgentCore Managed Harness (Nova Pro)
       │
       ▼
Prompt-based Routing
       │
  ┌────┼────────────────┐
  │    │                │
  ▼    ▼                ▼
Bug  Platform        Other
Report Question      Request
  │    │                │
  ▼    ▼                ▼
AgentCore  Embedded   Human
Gateway    FAQ        Support
  │                  1-800-555-0199
  ▼
AWS Lambda
  │
  ▼
Amazon DynamoDB
(Bug tickets)
```

Also includes a **Bedrock Flow** implementation (visual node graph) on the same shared backend.

---

## What It Does

| Route | Behavior | Safety Rule |
|---|---|---|
| Bug report | Collects description, steps to reproduce, and environment across turns — then calls `create_bug_report` tool | Tool is forbidden until all 3 fields are explicitly provided |
| Platform question | Answers orders, shipping, returns, payment, account, and privacy questions from the embedded FAQ | No policy is invented beyond the FAQ |
| Other / unsupported | Redirects to human support at 1-800-555-0199 | No attempt to answer outside supported scope |

The prompt also treats customer text as untrusted input and rejects attempts to reveal instructions, bypass required fields, or fabricate ticket IDs.

---

## Tech Stack

| Service | Purpose |
|---|---|
| Amazon Bedrock AgentCore | Managed harness — agent loop, session memory, tool execution |
| Amazon Bedrock AgentCore Gateway | Exposes Lambda as callable tool to the model |
| Amazon Bedrock Flows | Visual routing graph (classifier + condition nodes) |
| Amazon Bedrock Evaluations | LLM-as-a-judge automated evaluation |
| AWS Lambda | Bug report tool runtime |
| Amazon DynamoDB | Bug report ticket storage |
| AWS CloudFormation | Infrastructure as code |
| Amazon Nova Pro | Foundation model (`us.amazon.nova-pro-v1:0`) |

---

## Project Structure

```
├── system_prompt.txt          # Main deliverable — all routing and behavior lives here
├── prompt_design.md           # Behavioral contract and design decisions
├── harness-tests.json         # AgentCore test suite (8 tests, all routes)
├── flow-tests.json            # Bedrock Flow test suite (8 tests, all routes)
├── output_eval_dataset.jsonl  # JSONL dataset used for Bedrock Evaluation
├── bug-report-transcript.txt  # Multi-turn chat transcript showing tool call + ticket ID
├── OBSERVATIONS.md            # Before/after evaluation analysis across 3 runs
└── evidence/                  # Screenshots of evaluation results and AWS resources
```

---

## Evaluation Results

| Run | Correctness Score | What Changed |
|---|---|---|
| Run 1 | 0.75 | Baseline |
| Run 2 | 0.875 | Fixed FAQ misrouting + strengthened bug field collection rules |
| Run 3 | 0.875 | Further reinforced partial bug report gate |

- Evaluator: `amazon.nova-pro-v1:0`
- Metric: `Builtin.Correctness`
- 7/8 tests passing consistently

The single failing test (`t1_bug_report_partial`) reflects the inherent tension between a **multi-turn collection design** and a **single-turn evaluation format** — the harness is designed to collect fields across turns, but the evaluator sends only one message per test.

See [OBSERVATIONS.md](OBSERVATIONS.md) for full analysis.

---

## Key Design Decisions

- **Prompt-first routing** — one explicit classification among three mutually exclusive behaviors before any action
- **Stateful collection** — missing bug fields requested one at a time across turns using AgentCore's native session memory
- **Hard tool gate** — no partial arguments, placeholder values, or fabricated ticket IDs ever reach the tool
- **Grounded answers** — platform policy comes only from the embedded FAQ document
- **Safe fallback** — uncovered questions and out-of-scope requests always get the human support hand-off
- **Adversarial hardening** — customer input is explicitly treated as data, not instructions

---

## Evidence

| Screenshot | Description |
|---|---|
| `evidence/01-eval-run1-score-0.75.png` | Baseline evaluation score |
| `evidence/02-eval-run2-score-0.88.png` | Improved evaluation score |
| `evidence/04-eval-per-prompt-table.png` | Per-prompt results table |
| `evidence/06-dynamodb-tickets-table.png` | Real bug tickets in DynamoDB |
| `evidence/08-flow-diagram-full.png` | Full Bedrock Flow diagram |
| `evidence/09-flow-classifier-prompt.png` | Classifier node prompt configuration |
| `evidence/10-flow-condition-node.png` | Condition node routing expressions |

---

## AWS Infrastructure

Deployed in `us-east-1`:
- DynamoDB table: `bug-report-tool-stack-bug-reports`
- Lambda function: `bug-report-tool-stack-create-bug-report`
- AgentCore Gateway: `bug-report-tool-stack-gateway`
- AgentCore Harness: `support_chatbot`

---

## Certificate

Built as part of the **AWS AI & ML Scholars 2026** program  
Nanodegree: **Future AWS Agent Engineer**  
Institution: Udacity × Amazon Web Services

---

## Author

**Aman Ojha**  
[GitHub](https://github.com/Aman3Ojha)

---

*Educational project for a fictional online shop. Not affiliated with or endorsed by Amazon Web Services or Udacity.*
