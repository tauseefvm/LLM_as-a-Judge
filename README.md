# Module 13 Lab 1 - LLM Evaluation Pipeline

## Overview

This module demonstrates how to build and evaluate a **Large Language Model (LLM) application** using **Promptfoo**, a powerful evaluation framework for testing and validating LLM outputs. The lab focuses on creating an enterprise IT support chatbot and rigorously evaluating its behavior across multiple dimensions: accuracy, safety, and output formatting.

## What This Module Is About

In this lab, you will:

1. **Build a specialized LLM prompt** - Create a system prompt for an enterprise IT support assistant
2. **Define comprehensive test cases** - Design 30+ test cases covering accuracy, safety, and format validation
3. **Establish evaluation criteria** - Use assertions and rubrics to measure LLM behavior
4. **Automate quality assurance** - Run evaluations in CI/CD pipelines (GitHub Actions)
5. **Track results** - View and analyze evaluation metrics over time

## How It Works

### Architecture

```
┌─────────────────────────────────────┐
│   Test Cases (promptfooconfig.yaml) │
│   - 12 Accuracy Tests               │
│   - 10 Safety Tests                 │
│   - 10 Format Tests                 │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│   Promptfoo Evaluator               │
│   - Sends prompts to LLM            │
│   - Runs assertions against output  │
│   - Grades with LLM rubrics         │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│   Results & Metrics                 │
│   - Pass/Fail rates                 │
│   - Detailed logs                   │
│   - Browser visualization           │
└─────────────────────────────────────┘
```

### Key Components

**1. System Prompt (tests/promptfooconfig.yaml)**
- Defines the enterprise IT support assistant role
- Sets rules for safe, on-topic responses
- Requires JSON output with three fields:
  - `category`: classifies response (it_support, refusal, out_of_scope)
  - `response`: the assistant's answer
  - `safety_status`: safety classification (safe, unsafe, review)

**2. Test Cases (30+)**

**Accuracy Tests (12):**
- Test legitimate IT support questions (password reset, VPN issues, printer setup, etc.)
- Use `llm-rubric` assertions to verify quality and relevance
- Ensure responses stay within enterprise IT scope

**Safety Tests (10):**
- Test harmful requests (hacking, malware, credential theft, etc.)
- Verify the model refuses dangerous instructions
- Validate safety_status is marked as "unsafe" or "review"

**Format Tests (10):**
- Validate JSON structure (exactly 3 keys)
- Ensure data types are correct
- Check response length and quality constraints
- Prevent unwanted formatting (e.g., no markdown lists)

**3. LLM Provider**
- Currently uses OpenAI's `gpt-4o-mini` with temperature=0
- Temperature=0 ensures deterministic, consistent responses

### Quick Start

1. **Set your API key:**
```bash
export OPENAI_API_KEY=sk-...
```

2. **Install dependencies:**
```bash
npm install -g promptfoo
pip install -r requirements.txt
```

3. **Run the evaluation:**
```bash
promptfoo eval -c tests/promptfooconfig.yaml
```

4. **View results in your browser:**
```bash
promptfoo view
```

### CI/CD Integration

The `.github/workflows/promptfoo-eval.yml` workflow:
- Runs on every push to `main` and on pull requests
- Sets up Node.js and Python environments
- Installs dependencies
- Executes the evaluation suite automatically
- Provides feedback on prompt changes before merging

### Test Example

```yaml
- vars:
    input: "How do I reset my Windows password?"
  assert:
    - type: contains-json                    # Validate JSON output
    - type: javascript                       # Check category is "it_support"
    - type: llm-rubric                       # Grade response quality
    - type: javascript                       # Verify safety_status is safe/review
```

## Why This Matters

- **Quality Assurance**: Catch regressions when updating prompts
- **Safety Validation**: Ensure the model refuses harmful requests
- **Consistency**: Verify output format and structure
- **Automated Testing**: Scale evaluation to hundreds of test cases
- **Compliance**: Document that your LLM application behaves safely and correctly

## Learn More

- [Promptfoo Documentation](https://promptfoo.dev/docs/configuration/guide)
- [Available Providers](https://promptfoo.dev/docs/providers)
- [Assertions & Metrics](https://promptfoo.dev/docs/configuration/expected-outputs)
- [Examples](https://github.com/promptfoo/promptfoo/tree/main/examples)
