# CODESMITH IRS RAG

This folder contains the RAG portion of the IRS project. The hybrid script runs a 3-search retrieval flow over the codebase and fact graph to support evidence-based tax audit analysis.

## Project Purpose
This RAG workflow is used to:
- retrieve evidence from multiple tax-related sources
- identify relevant logic and impact zones
- return cited findings instead of unsupported guesses
- support decision-making before COBOL changes are applied

## Quick Start
```bash
cd <project-root>
source .venv_ai/bin/activate
python hybrid_rag.py --debug-retrieval
```
## Sample Query:
"Find guidance under §224 regarding the deduction for qualified tips, including transition relief"


Optional environment check:
```bash
./env_check.sh
```

Optional debug modes:
```bash
python hybrid_rag.py --plain-debug
python hybrid_rag.py --rich-debug
```

## Runtime And Environment
- Runtime: local macOS
- Active environment: `.venv_ai`
- Main script: `hybrid_rag.py`

Environment checks:
- `./env_check.sh` validates that `.venv_ai` is active
- If the wrong environment is active, it prints `WRONG ENV DETECTED` and activation instructions

## Retrieval Flow

### 1. Direct File vector search
- searches chunks from `./direct-file`
- returns score, file/line refs, and chunk/vector metadata

### 2. Tax-Calculator vector search
- searches chunks from `./Tax-Calculator/taxcalc`
- returns score, file/line refs, and chunk/vector metadata

### 3. Fact-graph search
- searches XML facts and dependencies from the `FACTS_XML` directory
- returns graph score, fact path, source file/line, and dependency count

## Prompt And Output

Prompt behavior:
- if a query is missing, the script prompts:
  - `Enter your audit query:`

Expected output sections:
- `Key Findings`
- `Potential Mismatches or Missing Logic`
- `High-Confidence References`
- `Evidence Gaps`
- `Next Steps`

Output controls:
- enforced structure includes `Evidence Gaps` and `Next Steps`
- retrieval output includes explicit references and metadata

## Debug Retrieval

Available modes:
- `--debug-retrieval` = plain retrieval debug output
- `--plain-debug` = plain-text debug output
- `--rich-debug` = Rich table debug output

Plain debug markers:
- `NOTE > ...`
- `[SEARCH PARAMS] ...`
- `[RESULT n] ...`

## Config

Configured in `.env`:
- `DIRECT_FILE_CODE_DIR=./direct-file`
- `TAX_CALC_CODE_DIR=./Tax-Calculator/taxcalc`
- `FACTS_XML=./direct-file/backend/src/main/resources/tax`

## Notes
- `FACTS_XML` supports directory indexing across all XML files
- text markers are portable across terminals, though colors may vary
- this RAG workflow is supporting analysis for the larger IRS project, not a standalone production tax engine
