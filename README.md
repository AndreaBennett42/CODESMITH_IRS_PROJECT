# CODESMITH IRS RAG

This repository contains the RAG portion of the IRS project. It provides a hybrid retrieval workflow built to help engineers search legacy tax-related code and supporting sources, identify impact zones, and gather evidence before making COBOL changes.

## Project Purpose

The broader project focused on teaching IRS engineers how to scan a legacy COBOL codebase for impact zones and inject updated tip-deduction logic safely. This repository extends that work by providing a RAG-based search tool that helps engineers:

- retrieve evidence from multiple tax-related sources
- identify relevant logic and impact zones
- return cited findings instead of unsupported guesses
- support decision making before COBOL changes are applied

## Quick Start

Prerequisites:
- Python 3.12
- Ollama installed locally

Setup:

```bash
git clone <your-repo-url>
cd <repo-name>
python3.12 -m venv .venv_ai
source .venv_ai/bin/activate
pip install -r requirements.txt
ollama pull llama3
ollama pull nomic-embed-text
```

Run:

```bash
python hybrid_rag.py --debug-retrieval
```

Sample query:

"Find guidance under Section 224 regarding the deduction for qualified tips, including transition relief"

## Optional Commands

Environment check:

```bash
./env_check.sh
```

Additional debug modes:

```bash
python hybrid_rag.py --plain-debug
python hybrid_rag.py --rich-debug
```

## Retrieval Flow

### 1. Direct File vector search
- searches chunks from `./direct-file`
- returns score, file and line references, and chunk metadata

### 2. Tax-Calculator vector search
- searches chunks from `./Tax-Calculator/taxcalc`
- returns score, file and line references, and chunk metadata

### 3. Fact-graph search
- searches XML facts and dependencies from the `FACTS_XML` directory
- returns graph score, fact path, source file and line, and dependency count

## Output

If no query is provided, the script prompts with `Enter your audit query:`.

Expected output sections:
- `Key Findings`
- `Potential Mismatches or Missing Logic`
- `High-Confidence References`
- `Evidence Gaps`
- `Next Steps`

## Config

Configured through environment variables such as:
- `DIRECT_FILE_CODE_DIR=./direct-file`
- `TAX_CALC_CODE_DIR=./Tax-Calculator/taxcalc`
- `FACTS_XML=./direct-file/backend/src/main/resources/tax`

## Notes

- `FACTS_XML` supports directory indexing across XML files
- this RAG workflow supports analysis for the larger IRS project and is not a standalone production tax engine
