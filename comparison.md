## Autoresearch vs. AlphaEvolve vs. OpenEvolve vs. Berkeley ADRS

### The common thread

All four systems automate the **generate → evaluate → select → iterate** research loop using LLMs. But they differ substantially in scope, architecture, and philosophy.

---

### 1. Karpathy's Autoresearch

**Philosophy**: Minimal, single-purpose, overnight autonomy.

- **Search target**: One file (`train.py`) — a small GPT language model's architecture and hyperparameters
- **LLM role**: A single coding agent (e.g. Claude) proposes one modification per cycle, writing a git commit
- **Evaluation**: Fixed 5-minute training run, scored by validation bits-per-byte (BPB)
- **Selection**: Binary — if BPB improves, keep the commit; otherwise `git reset`. No population, no islands
- **Population size**: **1** — it's a greedy hill-climber, not a population-based evolutionary algorithm
- **Infrastructure**: Single GPU, single agent, single metric, git for version control
- **Scope**: Narrow — specifically neural network architecture/hyperparameter search for LM pretraining

**Strengths**: Dead simple. Anyone with one GPU can run it overnight and wake up to ~100 experiments with a clear improvement trail. Zero infrastructure beyond git + PyTorch.

---

### 2. DeepMind's AlphaEvolve

**Philosophy**: Industrial-scale evolutionary program synthesis for algorithmic discovery.

- **Search target**: Entire codebases and algorithms across math, CS, and engineering
- **LLM role**: An **ensemble** of Gemini Flash (breadth/speed) + Gemini Pro (depth/quality) generates candidate programs
- **Evaluation**: Problem-specific automated evaluators (correctness proofs, benchmark scores, etc.)
- **Selection**: A **program database** ranks solutions by fitness and feeds top performers + diverse samples back into prompts — a proper evolutionary algorithm with population dynamics
- **Population**: Large, maintained across generations with ranking and diversity pressure
- **Infrastructure**: Google-scale compute, proprietary Gemini models
- **Scope**: General-purpose — matrix multiplication algorithms, data center scheduling (Borg), chip design, training kernel optimization

**Key results**: Improved on Strassen's 1969 matrix multiplication algorithm; a heuristic deployed in Google's Borg that recovers 0.7% of global compute.

---

### 3. OpenEvolve

**Philosophy**: Open-source AlphaEvolve for everyone.

- **Search target**: Marked code blocks (`EVOLVE-BLOCK-START/END`) or entire files
- **LLM role**: Configurable **ensemble** — supports Gemini, Claude, Cerebras, etc.
- **Evaluation**: User-defined evaluator functions returning metric dictionaries
- **Selection**: **Island model** — multiple sub-populations evolve semi-independently with configurable exploitation/exploration ratio, periodic migration
- **Population**: Hundreds of programs across multiple islands
- **Infrastructure**: Self-hosted, any LLM API, async pipeline
- **Scope**: General-purpose — algorithm optimization, code improvement, function minimization

**Key difference from AlphaEvolve**: Open, multi-backend LLM support, island-based population diversity. Achieves ~99.96% of AlphaEvolve's circle-packing result.

---

### 4. Berkeley ADRS (AI-Driven Research for Systems)

**Philosophy**: Rigorous evaluation of ADRS as a research methodology for systems problems.

- **Search target**: Core algorithms inside real systems (load balancers, schedulers, KV caches, databases)
- **LLM role**: Uses existing frameworks (OpenEvolve, GEPA, ShinkaEvolve) as tools — not a new system itself, but a **research program evaluating the paradigm**
- **Evaluation**: Real-world traces and benchmarks from production systems
- **Selection**: Varies by framework used
- **Key insight**: Systems research is uniquely suited to ADRS because it has (a) objective metrics, (b) semantic correctness constraints, (c) small interpretable code targets, and (d) cheap simulation
- **Scope**: Systems research specifically — cloud scheduling, database transactions, load balancing

**Standout results**: 13x speedup in MoE load balancing, 35% cost savings in cloud scheduling, 3x KV cache runtime reduction. Notably, the LLMs rediscovered algorithms from unrelated fields (Hamilton's Apportionment method from 18th-century political science applied to expert load balancing).

---

### Summary table

| | Autoresearch | AlphaEvolve | OpenEvolve | Berkeley ADRS |
|---|---|---|---|---|
| **Type** | Greedy hill-climber | Population-based evolution | Island-model evolution | Research methodology |
| **Population** | 1 (single candidate) | Large | Multi-island (hundreds) | Framework-dependent |
| **LLM** | Single agent | Gemini ensemble | Any LLM ensemble | GPT-5, Gemini-3.0 |
| **Target** | 1 file (train.py) | Entire codebases | Marked blocks or files | System algorithms |
| **Domain** | Neural arch search | General algorithms | General algorithms | Systems research |
| **Infra** | 1 GPU + git | Google-scale | Self-hosted | Self-hosted |
| **Open?** | Yes | No | Yes | Frameworks are open |
| **Cost/run** | GPU electricity | N/A (internal) | <$30 for 100 iters | <$30 for 100 iters |

### The key philosophical difference

Autoresearch is a **craftsman's tool** — intentionally minimal, constrained to a single well-defined problem (make this LM train better in 5 minutes), operating as a greedy hill-climber. Its power comes from simplicity: anyone can run it, understand it, and review every commit.

AlphaEvolve/OpenEvolve are **evolutionary search engines** — they maintain diverse populations, use LLM ensembles, and apply selection pressure across generations. They're designed for open-ended algorithmic discovery where the search space is vast and multi-modal.

Berkeley ADRS is a **meta-level contribution** — not a system but a research program demonstrating that the evolutionary-LLM loop is particularly effective for systems problems, because those problems have objective metrics and cheap evaluation. Their most interesting finding is that LLMs can make **cross-domain transfers** that human specialists miss.

---

### Sources

- [AlphaEvolve — Google DeepMind Blog](https://deepmind.google/blog/alphaevolve-a-gemini-powered-coding-agent-for-designing-advanced-algorithms/)
- [AlphaEvolve paper (arXiv)](https://arxiv.org/abs/2506.13131)
- [OpenEvolve — HuggingFace Blog](https://huggingface.co/blog/codelion/openevolve)
- [OpenEvolve GitHub](https://github.com/codelion/openevolve)
- [Berkeley ADRS — "Let the Barbarians In" (ACM SIGOPS)](https://www.sigops.org/2026/let-the-barbarians-in-how-ai-can-accelerate-systems-performance-research/)
- [Berkeley ADRS project page](https://sky.cs.berkeley.edu/project/adrs/)
- [CodeEvolve paper (arXiv)](https://arxiv.org/html/2510.14150v1)
