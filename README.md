# PromptQuill

**The multi-language prompt evaluation engine.**

PromptQuill scores prompts across 8 dimensions, detects anti-patterns, runs
adversarial security tests, and produces structured rewrites. It powers the
[PromptForge](https://github.com/j0sp0nc3/promptforge) web app, but ships as
a standalone, zero-dependency library usable from **any** language.

## Why PromptQuill?

- **Zero dependencies.** No npm tree, no pip requirements. Drop in and go.
- **Multi-language parity.** The JS and Python ports return the exact same
  output shape for the same prompt. Same scores, same findings, same grades.
- **Privacy-first.** 100% local evaluation. Your prompts never leave your
  machine. No API calls, no telemetry.
- **Static analysis.** Evaluates the prompt itself — no need to run an LLM.
  Fast, deterministic, free.

## Install

### JavaScript / Node

```bash
npm install promptquill-core
```

```js
const { analyze } = require('promptquill-core');
const result = analyze('You are a Python expert. Review this code and output JSON.');
console.log(result.overallScore);  // 0–100
```

### Python

```bash
pip install promptquill-core
```

```python
from promptquill_core import analyze
result = analyze('You are a Python expert. Review this code and output JSON.')
print(result['overallScore'])  # 0–100
```

### Any language (REST)

```bash
# Run the microservice (from the promptforge repo)
node server.js

# POST from anywhere — C#, Java, Go, Rust, PHP, Ruby, curl
curl -X POST http://localhost:3000/api/analyze \
  -H "Content-Type: application/json" \
  -d '{"prompt":"Your prompt here"}'
```

## Output shape

Both JS and Python return:

```jsonc
{
  "overallScore": 67,          // 0–100
  "grade": "C",                // A | B | C | D | F
  "promptType": "chainOfThought",
  "wordCount": 12,
  "dimensions": {
    "clarity":        { "score": 85, "findings": [...], "suggestions": [...] },
    "specificity":    { "score": 50, "findings": [...], "suggestions": [...] },
    "structure":      { ... },
    "robustness":     { ... },
    "context":        { ... },
    "outputFormat":   { ... },
    "chainOfThought": { ... },
    "safety":         { ... }
  },
  "antiPatterns": [ { "id": "AP003", "name": "...", "severity": "high", "suggestion": "..." }, ... ],
  "suggestions":  [ { "priority": "high", "title": "...", "description": "..." }, ... ]
}
```

## The 8 dimensions

| Dimension | What it measures |
|-----------|------------------|
| Clarity | Action verbs, sentence structure, vagueness |
| Specificity | Numeric constraints, measurable criteria, examples |
| Structure | XML/markdown, lists, delimiters |
| Robustness | Error handling, edge cases, validation |
| Context | Role, domain, audience, tone |
| Output Format | JSON/CSV/markdown, length, schema |
| Chain of Thought | Step-by-step reasoning, decomposition |
| Safety | Anti-hallucination, scope limits, injection guards |

Weights are dynamic: the engine infers the prompt type (system, few-shot,
chain-of-thought, creative, RAG, tool-use, or general) and adjusts the
weight of each dimension accordingly.

## Repository structure

```
promptquill/
├── packages/
│   └── core/                  # The engine, publishable to npm and PyPI
│       ├── promptquill-core.js     # JS (UMD/ESM/CJS)
│       ├── promptquill_core.py     # Python (stdlib only)
│       ├── promptquill-rules.json  # Declarative weights
│       ├── package.json            # npm manifest
│       ├── pyproject.toml          # PyPI manifest
│       └── README.md               # API docs + usage examples
├── LICENSE
└── README.md                  # This file
```

## Publishing (for maintainers)

### npm (JS)

```bash
cd packages/core
npm login          # one-time, with your npm account
npm publish --access public
```

This publishes `promptquill-core` to the public registry. The `files` field
in `package.json` ensures only the JS + rules + README ship (no Python files).

### PyPI (Python)

```bash
cd packages/core
pip install build twine
python -m build
twine upload dist/*
```

This builds a wheel + sdist from `pyproject.toml` and uploads
`promptquill-core` to PyPI.

## Related

- **[PromptForge](https://github.com/j0sp0nc3/promptforge)** — the full web
  application (30+ anti-patterns, 13 adversarial tests, i18n ES/EN,
  templates, history). This library is a simplified, dependency-free subset
  of that engine.

## License

MIT © 2026 j0sp0nc3
