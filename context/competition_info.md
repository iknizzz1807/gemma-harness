Overview
Today’s best coding agents rely on massive API models running in the cloud. What if every developer could rely on an autonomous agent equally capable offline, on consumer hardware?

Post-train an open model into a reliable agent that navigates complex codebases and drafts fixes for real software issues, accelerating developer workflows on everyday hardware.

Start

22 days ago
Close

2 months to go
Merger & Entry
Description
Note: there are two active competitions for the Gemma 4 Developer Agent Competition: this competition, and a paper track, where you can document your approach for this prediction competition.

Today’s most capable coding agents rely on internet-connected models that require powerful commercial infrastructure. That puts advanced software engineering out of reach for many developers.

Small local models have made rapid progress, but they still struggle with complex, multi-turn software engineering. Large codebases are hard to navigate, so fixing bugs and adding features reliably is tough for models that fit on a single accelerator.

In this competition, you’ll post-train Gemma 4 to build autonomous software engineering agents designed to amplify developers’ talents. You’ll explore new fine-tuning and reinforcement learning techniques to create agents that can independently navigate repositories and draft solutions, freeing up human engineers to focus on architecture, logic, and review.

Your solution could help put powerful coding assistants in the hands of millions more developers. Sharing how you did it makes advanced coding tools accessible to everyone.

Evaluation
You must provide a zip archive (submission.zip) that contains your Agent Config, comprising system prompts, custom tools, skills, and LoRA adapters. An agent.yaml file must be located at the root of the archive.

submission.zip
├── agent.yaml                  # REQUIRED: Root agent config
├── configs/
│   └── sampling.yaml           # Optional: Generation parameters loaded via !include
├── prompts/
│   ├── system.md               # Optional: System instructions loaded via !include
│   └── analyzer.md
├── sub_agents/
│   └── code_analyzer.yaml      # Optional: Sub-agent or AgentTool YAML configurations
├── adapters/                   # Optional: Fine-tuned PEFT LoRA adapters or model weights
│   ├── main_lora/
│   │   ├── adapter_config.json
│   │   └── adapter_model.safetensors
│   └── tool_lora/
│       ├── adapter_config.json
│       └── adapter_model.safetensors
└── skills/                     # Optional: ADK Skill directories
    └── repo_navigation/
        ├── SKILL.md
        ├── scripts/            # Python or bash scripts executed in a sandbox
        └── resources/          # Domain knowledge markdown files
The config language follows the Google ADK Agent Config specification with additional restrictions to prevent code execution outside of a sandbox. The submissions themselves are compiled into ADK agents to be evaluated.

Issue Scoring
Similar to SWE-Bench, your agent's submitted patches are evaluated by a PASS/FAIL metric. For each issue, the submitted patch is applied to that issue's code repository and that issue's validation tests are run. Your submission's overall score is the percentage of patched repositories that pass the validation tests.

Your agent has a limit of 12 hours to submit patches for all tasks, inclusive of sandbox setup time, but excluding time for patch validation. You may, but are not required, to set per-task time limits in your submission's eval_config.yaml.

Timeline
September 23, 2026 - Start Date.
November 12, 2026 (optional) - Deadline to submit Research Paper.
November 25, 2026 - Entry Deadline. You must accept the competition rules before this date in order to compete.
November 25, 2026 - Team Merger Deadline. This is the last day participants may join or merge teams.
December 2, 2026 - Final Submission Deadline.
All deadlines are at 11:59 PM UTC on the corresponding day unless otherwise noted. The competition organizers reserve the right to update the contest timeline if they deem it necessary.

Prizes
Total Prizes Available: $100,000

The Gemma 4 Developer Agent Competition: $65,000

1st Place: $37,000
2nd Place: $18,000
3rd Place: $10,000
Paper Submission Award (optional): $35,000

Participants are encouraged to submit original, unpublished research submissions on topics including, but not limited to:

Tuning & Optimization: Parameter-efficient fine-tuning and Reinforcement Learning (RL) approaches tailored to improve SWE agent capabilities.
Code Comprehension: Novel techniques for code-graph generation, parsing, and embedding to enhance deep comprehension of complex repositories.
Tasks & Benchmarks: Development of new tasks, evaluation datasets, or resources targeting code structure, graphs, and structured code generation.
Graph Reasoning: Methods and architectures for improving Large Language Model (LLM) reasoning over large-scale graphs.
Top submissions will be highlighted at a Google hosted event (such as a NeurIPS expo workshop) and awarded prizes. Submissions leveraging the accompanying released graph and embedding dataset are highly encouraged, but not a requirement for submission. Submissions must be made by November 12, 2026 at 11:59PM UTC.

To submit, sign up for the paper track.

Model Selection, Budget, and Harness Rules
Model Selection and LoRA Adapters
At present, we only support the gemma-4-31b-it-qat-w4a16-ct Gemma 4 model variant. You must choose this model for every agent and subagent.

You may, but are not required, to include one or more .safetensors format LoRA adapters with your submission. While you are restricted to a single base model, you may use different adapters for each agent in your submission:

Place PEFT LoRA directories (containing adapter_config.json and adapter_model.safetensors) inside adapters/<adapter_name>/.
Reference adapter: <adapter_name> on any LlmAgent in agent.yaml or sub_agents/*.yaml (for example, adapter: main_lora on the root coder agent and adapter: tool_lora on a read-only analyzer AgentTool).
Tool, Prompt, and Skill Rules
!include Directives: Resolves file paths relative to the directory of the file containing the tag. For instance, in agent.yaml, !include prompts/system.md loads prompts/system.md.
Sandboxing: Path traversal outside the submission root (e.g., via ../ or symlinks) is not allowed.
Allowed Tools: Your agent can only request tools provided by the competition harness or custom subagents defined via agent_tool.
Skill Structure & Sandboxing: Each skill must be a directory containing a SKILL.md manifest with YAML frontmatter (name: <skill-name>). Scripts executed via run_skill_script run securely inside the competition's persistent Docker container, sharing filesystem access with run_command and debiting execution time against your central budget. Agents can inspect domain knowledge files using load_skill_resource.
The predefined tools available to your agent are:

run_command(command: str) -> str - Executes a shell command in /bin/bash -c inside /workspace.
submit_patch() -> str - Stages untracked file intents (git add -N .) and captures git diff HEAD from /workspace.
get_status() -> str - Returns live budget consumption and patch status.
read_file(filepath: str, start_line: int | None = None, end_line: int | None = None) -> str - Reads a file from /workspace with 1-indexed inclusive line slicing.
edit_file(filepath: str, old_string: str, new_string: str, allow_multiple: bool = False) -> str - Replaces old_string with new_string in an existing non-empty file inside /workspace.
write_file(filepath: str, content: str) -> str - Creates or overwrites a file at /workspace/<filepath>, automatically creating parent directories (mkdir -p).
get_code_neighbors(node: str, edge_type: str | None = None, max_neighbors: int = 50) -> str - Finds incoming and outgoing neighbors of a symbol (node) in the repository call/dependency graph.
search_similar_code(query: str, k: int = 10) -> str - Finds top-k graph nodes with highest cosine similarity to query in the pre-computed embeddings.
get_code_subgraph(nodes: list[str]) -> str - Extracts the induced subgraph (all nodes and interconnecting edges) for a list of symbols.
See the HARNESS_README.md file in the dataset for detailed information about the submission format and execution environment.

Upgraded Accelerators
This competition has access to Kaggle's pool of powerful new L4x4 machines! These machines offer 96GB of GPU memory enabling submissions with much larger models. See this page for more on these machines.

What you need to know:

Quota usage - Due to their limited availability, notebooks with L4x4s consume GPU quota at twice the rate of the older T4x2 and P100 machines. We may increase this rate as necessary to ensure these machines are available for submission scoring.
Restricted Use - L4s are only available for notebooks attached to this competition. We will build tooling to enforce this if necessary. In the meantime, attempts to circumvent this restriction may be enforced by Kaggle moderation with consequences including team bans from the competition or account bans.
No Internet - All L4 sessions must have internet disabled.

Dataset Description
This competition evaluates AI software engineering agents on their ability to solve real-world Python bug fixes and feature requests.

To assist agents in large codebase navigation, dependency understanding, and semantic retrieval, the competition provides code graphs, graph/node embeddings, and specialized graph tools alongside traditional repository exploration mechanisms.

Participants are provided with a training set (including tasks, code graphs, and embeddings) for local agent development, prompt engineering, and offline validation. Submissions are evaluated against a test set curated under a similar pipeline, with identical graph generation methods, and verification standards.

In this public-facing version of the dataset, you will only see the training set. When your submission is scored, this training set will be replaced with the hidden test set.

Files
tasks.jsonl

Line-delimited JSON file containing the 129 public development tasks. Each JSON object represents one bug-fixing benchmark instance and includes both the reference fix (patch) and verification unit tests (test_patch) for local eval\ uation.

instance_id - unique identifier for the benchmark task combining the repository short name and issue or pull request number (for example, fastapi_11194 or rich_3454).
repo - source GitHub repository in owner/repo format (fastapi/fastapi, Textualize/rich, psf/requests, or encode/httpx).
base_commit - 40-character hexadecimal Git commit SHA representing the repository snapshot immediately prior to the bug fix.
problem_statement - natural-language bug report or feature issue description provided to the agent as the task prompt.
hints_text - optional issue comments or maintainer hints providing additional context (may be empty).
patch - reference unified git diff solution patch that resolves the issue on base_commit (provided in published/tasks.jsonl for local training and analysis; excluded from the hidden rerun/tasks.jsonl test set).
test_patch - unified git diff patch containing the verification unit tests executed by pytest during Phase 2 grading (provided in published/tasks.jsonl for local verification; kept in the private secret/solution.parquet archive during competition scoring).
created_at - ISO-8601 timestamp indicating when the issue or pull request was created.
snapshots/ Directory containing 129 compressed Git working tree archives (snapshots/<instance_id>.tgz, such as fastapi_11194.tgz, requests_7205.tgz, rich_2725.tgz, and httpx_3672.tgz). Each archive contains a full Git repository frozen at base_commit with forward commit history removed so agents can safely run git status, git log, and git diff inside /workspace without leaking future fixes.

graphs/ Directory containing serialized NetworkX Abstract Syntax Tree (AST) call and dependency graphs in JSON format (256 files total: 129 task-named files <instance_id>.json hard-linked to 127 commit-named files <repo_short>_<base_commit>.json). These graphs back the get_code_neighbors and get_code_subgraph agent tools and use the following schema:

directed - boolean flag indicating whether graph edges are directed (true).
multigraph - boolean flag indicating whether multiple edges can connect the same pair of symbols (true).
graph - dictionary of global graph metadata attributes.
nodes - list of code entity node objects in the repository snapshot.
nodes[*].id - unique fully qualified Python symbol path for the module, class, or function (for example, fastapi.routing._prepare_response_content).
nodes[*].name - human-readable qualified symbol name matching id.
nodes[*].text - full Python source code definition of the function, method, or class at base_commit.
edges - list of directed structural relationship objects between code nodes.
edges[*].source - origin symbol id where the call or reference originates.
edges[*].target - destination symbol id being called, imported, or referenced.
edges[*].type - structural relationship category linking source to target (such as calls).
edges[*].key - integer index ($0, 1, \dots$) distinguishing parallel edges in the multigraph.
embeddings/ Directory containing compressed NumPy archives (256 files total: 129 task-named files <instance_id>.npz hard-linked to 127 commit-named files <repo_short>_<base_commit>.npz). These archives power offline semantic code retrieval via the search_similar_code tool.

<node_id>.npy - float32 NumPy array of length 256 containing dense semantic vector features embedding_[0-255] for the corresponding AST graph symbol node_id.
wheels/ Directory of 124 pre-compiled offline Python binary wheels (*.whl) covering historical dependency versions for fastapi, starlette, pydantic, requests, urllib3, rich, httpx, httpcore, pytest, and transitive packages. Mounted read-only at /wheels/ inside the sandbox container so pip can install repository dependencies without internet access.

docker/ Container build specifications and Python standard library compatibility shims used to build the local swebench-sandbox:latest image:

Dockerfile.sandbox - base sandbox container specification providing Python 3.13, git, pytest, and build backends (setuptools, hatchling, flit-core, poetry-core, pdm-backend).
Dockerfile.public - public development container variant pre-configured for the 129 open-source tasks.
imp.py - compatibility shim restoring the legacy imp module removed in Python 3.13 for older test suites.
telnetlib.py - compatibility shim restoring the legacy telnetlib module removed in Python 3.13.
sandbox/setup.py Container initialization script executed inside /workspace when preparing a task sandbox. Inspects pyproject.toml, setup.cfg, or requirements.txt, resolves compatible offline wheels from /wheels/, performs an editable install (pip install --no-index --find-links=/wheels -e .), and creates a clean baseline Git commit so git diff HEAD captures only your agent's edits.

sample_submission/ Ready-to-run baseline ADK submission directory illustrating the declarative YAML schema, multi-agent delegation, and multi-LoRA routing.

HARNESS_README.md In-depth technical guide covering the swegemma, adk-submission, and adk-eval-core libraries, the two-container sandbox lifecycle, tool call signatures, context compaction, and local CLI evaluation commands.

submission.parquet Output file automatically produced at /kaggle/working/submission.parquet during evaluation when the inference script runs your uploaded submission.zip archive against the benchmark tasks:

id - unique task identifier matching instance_id in tasks.jsonl.
prediction - unified git diff patch string generated by your agent relative to base_commit (or NO_PATCH if no changes were produced).
Dataset Generation & Verification Pipeline
Both the training set and test set were produced using an automated curation and two-phase verification harness:

Commit & PR Mining: Filtered repository histories for commits that jointly modify core functional logic (.py) and corresponding unit tests (test_*.py / *_test.py).
Issue Linking & Prompt Extraction: Matched each code change with its original problem description, issue tracker thread, or bug report.
Filtering & De-noising: Excluded mechanical refactors, documentation-only changes, large-scale automated changes (LSCs), and ambiguous issues lacking reproducible acceptance criteria.
Execution Verification Harness:
Fail-to-Pass (Baseline Verification): The test reproducer (test_patch) is applied to base_commit without the fix; tests must fail (non-zero exit code).
Pass-to-Pass (Solution Verification): The reference fix (patch) is applied with test_patch; the entire test suite must pass cleanly (exit code 0).
Near Completion with Frontier Models: For the test set, we check that a larger frontier model can pass the case or get within a single test case of passing.
There are about 120 tasks in the test set, evenly divided between the public and private splits. The test set was curated from a set of private repositories.

Files
524 files

Size
22.42 GB

Type
tgz, json, npz + 8 others

License
Apache 2.0

HARNESS_README.md(55.94 kB)
swegemma Evaluation Harness & Competitor Guide
This document provides a complete technical reference for the swegemma evaluation harness and its companion libraries (adk-submission and adk-eval-core). It explains how competitor submissions are validated and compiled, how the harness orchestrates agent sessions and sandboxes, how the built-in tools behave, and how generated patches are extracted and graded.

Table of Contents
System Architecture & Library Stack
Submission Contract & Declarative Agent Schema (adk-submission)
Model Registry, Single-Model Rule & LoRA Adapters on 4x L4 GPUs
Two-Phase Evaluation Lifecycle
Harness-to-Agent Interaction Protocol
Built-In Tools Reference (swegemma.tools)
Budgets, Operational Limits & Context Management
Patch Extraction & Phase 2 Verification (verify_task)
Local CLI Usage & Output Artifacts
Competitor Best Practices & Important Gotchas
1. System Architecture & Library Stack
The evaluation stack is composed of three cooperating libraries:

Package	Role in the Competition Stack
adk-submission	Declarative Agent Compiler & Inference Server Manager. Compiles untrusted submission directories (agent.yaml, prompts, sub-agents, skills, and PEFT/LoRA adapters) into live Google ADK BaseAgent trees (compile_submission) without executing arbitrary competitor Python code (importlib is never used). Also manages the local VllmServer and TransformersServer processes.
adk-eval-core	Core Evaluation Infrastructure. Provides base task/result models (BenchmarkTask, BaseTaskResult, EvaluationResult), sandbox execution backends (DockerContainer, SubprocessSandbox), resilient 3-tier file editing (apply_replacement), token/cost tracking (TokenBudget, PricingTable), ADK plugins (EventDisplayPlugin, ModelRetryPlugin), and ATIF v1.7 trajectory tracing (SessionTrace).
swegemma	SWE-bench Harness & Scoring Engine. Implements the two-container SWE evaluation lifecycle (Evaluator, run_agent_sandbox, verify_task), binds the 9 sandboxed workspace and code-intelligence tools (SwegemmaContext.create_tools()), constructs the task prompt and continuation nudges, extracts unified git diffs (scripts/inference.py), and runs hermetic pytest + JUnit XML verification (scripts/metric.py).
flowchart TB
    subgraph Submission["Competitor Submission (submission.zip / agent_dir)"]
        YAML["agent.yaml + sub_agents/*.yaml"]
        EvalCfg["eval_config.yaml (Optional Budgets)"]
        Prompts["prompts/*.md, configs/*.yaml"]
        Adapters["adapters/* (Optional LoRA / Weights)"]
    end

    subgraph Host["Harness Process (swegemma + adk-submission + adk-eval-core)"]
        Validator["validate_directory & validate_single_declared_model"]
        Server["Local Inference Server (vLLM / Transformers :8000)"]
        Compiler["compile_submission() -> ADK Runner"]
        Tools["SwegemmaContext (9 Bound Tools + Budget Gate)"]
    end

    subgraph Sandboxes["Isolated Per-Task Sandboxes"]
        ContA["Container A: Agent Sandbox (/workspace)\nSnapshot + Editable Install + Baseline Commit"]
        ContB["Container B: Verification Sandbox (/workspace)\nFresh Snapshot + agent_patch + test_patch + pytest"]
    end

    Submission --> Validator
    EvalCfg --> Host
    Adapters --> Server
    Validator --> Compiler
    Server <-->|"OpenAI-compatible /v1 API"| Compiler
    Compiler <-->|"Tool Calls & JSON Responses"| Tools
    Tools <-->|"docker exec / subprocess"| ContA
    ContA -->|"git add -N . && git diff --binary"| ContB
    ContB -->|"exit_code == 0 & JUnit XML valid"| Score["Resolution Rate [0.0, 1.0]"]
2. Submission Contract & Declarative Agent Schema (adk-submission)
2.1. Declarative-Only Security Model
Competitors do not submit Python code entrypoints (agent.py or agent_fn). Standard ADK's from_config() permits arbitrary dynamic Python imports; adk-submission replaces it with a sandboxed YAML compiler (compile_submission). All agents, sub-agents, workflows, tools, and generation parameters are declared in YAML and resolved against closed host registries (ToolRegistry, ModelRegistry, SkillRegistry, CallbackRegistry).

2.2. Submission Directory Layout
A valid submission directory (packaged at the root of submission.zip, evaluated by scripts/inference.py in Stage 1 to produce submission.parquet) follows this layout:

submission/
├── agent.yaml                  # REQUIRED: Root agent config (or root_agent.yaml; .yml also accepted)
├── eval_config.yaml            # Optional: Per-task evaluation budget & timeout overrides
├── configs/
│   └── sampling.yaml           # Optional: Generation parameters loaded via !include
├── prompts/
│   ├── system.md               # Optional: System instructions loaded via !include
│   └── analyzer.md
├── sub_agents/
│   └── code_analyzer.yaml      # Optional: Sub-agent or AgentTool YAML configurations
├── adapters/                   # Optional: Fine-tuned PEFT LoRA adapters or model weights
│   ├── main_lora/
│   │   ├── adapter_config.json
│   │   └── adapter_model.safetensors
│   └── tool_lora/
│       ├── adapter_config.json
│       └── adapter_model.safetensors
└── skills/                     # Optional: ADK Skill directories (each containing SKILL.md)
    └── repo_navigation/
        └── SKILL.md
Root Config Discovery: Exactly one root config (agent.yaml, agent.yml, root_agent.yaml, or root_agent.yml) must exist in the submission root. Having zero root files raises MissingRootConfigError; having more than one raises MultipleRootConfigsError.
Optional Evaluation Budget Config (eval_config.yaml): Submissions may include an optional eval_config.yaml at the root under an evaluation: key (timeout_seconds, max_tool_calls, max_time_minutes, max_turns) to customize per-task operational budgets during Stage 1 inference (load_submission_eval_config in scripts/inference.py).
Sandboxed !include Directive: YAML files may embed external files using !include <relative_path>:
.md and .txt files are loaded as raw UTF-8 strings (ideal for instruction or global_instruction).
.yaml and .yml files are parsed recursively (up to a maximum include depth of 10 with cycle detection).
Absolute paths, null bytes, .. traversal components, and symlinks resolving outside the submission root are strictly blocked (PathTraversalError).
2.3. Supported Agent Classes (agent_class)
SandboxedAgentConfig supports four discriminated agent types (defaulting to LlmAgent if agent_class is omitted):

LlmAgent (default):
name (str, required): Agent identifier string.
model (str, required unless a default model is set): Model alias from the registry (gemma-4-31b-it-qat-w4a16-ct, gemma-4-12b-it-qat-w4a16-ct, gemma-4-e4b-it-qat-w4a16-ct, or gemma-4-e2b-it-qat-w4a16-ct for competition submissions).
adapter (str | None): Optional adapter name matching a subdirectory or weight file under adapters/.
description (str, up to 1,000,000 chars): Used by parent agents when delegating to this agent.
instruction (str, up to 1,000,000 chars): System prompt for the agent. Supports ADK session state templating ({problem_description}, {hints}, or custom output_key variables).
tools (list): List of tool names (str), AgentTool references (agent_tool: {config_path: ..., skip_summarization: true}), or inline agent dictionaries.
skills (list[str] | None): List of relative paths to skill directories within the submission archive.
sub_agents (list): List of {config_path: ...} references for multi-agent transfer.
output_key (str | None): Saves the agent's final text output into session.state[output_key].
include_contents ("default" | "none"): Controls whether conversation history is passed to the agent.
disallow_transfer_to_parent / disallow_transfer_to_peers (bool | None).
generate_content_config (dict | None): Sampling, seed, and thinking parameters (see constraints below).
SequentialAgent:
Executes sub_agents in sequential order. Accepts name, description, sub_agents, and agent callbacks.
ParallelAgent:
Executes sub_agents concurrently in isolated branches. Accepts name, description, sub_agents, and agent callbacks.
LoopAgent:
Repeatedly executes sub_agents up to max_iterations (int, default 500, bounded to 1..500).
2.4. Enforced Structural Limits & Generation Constraints
Submission constraints in swegemma (build_submission_limits() in swegemma/config.py) are intentionally minimal: aside from security sandboxing (no symlinks, no path traversal, declarative YAML only), the primary hard constraint is a total unpacked submission size of < 3 GiB (3,221,225,472 bytes), including all adapters/ weights, plus token ceilings required by the 4x L4 GPU context window (32,768 tokens).

All other structural limits are set to generous safety ceilings solely to guard against runaway recursion or zip/YAML anchor bombs:

Category	Parameter	Competition Limit / Allowed Range
Primary Archive Constraint	max_total_size_bytes	3 GiB (3,221,225,472 bytes total unpacked size, including adapters/)
Structural Safety Ceilings (SubmissionLimits)	max_file_count	10,000 files
max_yaml_files	1,000 YAML files
max_yaml_size_bytes	50 MiB per YAML file / cumulative !include expansion
max_skill_size_bytes	50 MiB per skill directory
max_instruction_chars	1,000,000 chars per agent (10,000,000 total across all agents)
max_agents	500 total agents in the compiled tree
max_sub_agent_depth	50 levels of nesting (sub_agents and agent_tool)
max_skills	1,000 skills
max_loop_iterations	500 iterations for any LoopAgent
allowed_file_extensions	Strictly required formats only: .yaml, .yml (agent/eval configs), .md, .txt (prompts & SKILL.md), .py (ADK skill scripts), .json (adapter_config.json), and .safetensors (adapter_model.safetensors). Pickle-based weights (.bin, .pt, .pth) and other binary/archive extensions are rejected.
adapter_extensions	.safetensors only (adapters/<name>/adapter_model.safetensors)
Generation Config (GenerationConstraints)	allowed_fields	All schema fields permitted: temperature, top_p, top_k, max_output_tokens, presence_penalty, frequency_penalty, stop_sequences, response_mime_type, seed, thinking_config
max_output_tokens	1 to 32,768 (default 16,384; bounded by 4x L4 vLLM max_model_len=32768)
thinking_config.thinking_budget	0 to 32,768 (default 4,096; bounded by 4x L4 vLLM max_model_len=32768; positive values set enable_thinking: true and forward extra_body.thinking_token_budget to vLLM's ThinkingTokenBudgetLogitsProcessor, while 0 sets enable_thinking: false)
thinking_config.thinking_level	"MINIMAL", "LOW", "MEDIUM", "HIGH", or "NONE" (case-insensitive). Note: When serving via vLLM's OpenAI-compatible /v1 endpoint (especially with LoRA adapters), omit thinking_level and configure include_thoughts: true + thinking_budget: 4096 instead, because thinking_level maps to OpenAI's reasoning_effort parameter (swegemma also sets litellm.drop_params = True).
thinking_config.include_thoughts	true or false. When thinking_budget > 0 (or thinking_level is active), include_thoughts: true retains Part(thought=True) in LlmResponse and subsequent tool-call context (<|channel>thought...<channel|>), whereas include_thoughts: false still enables reasoning on the current turn (enable_thinking: true) while stripping Part(thought=True) from LlmResponse and outgoing message history. When neither thinking_budget nor thinking_level is set, include_thoughts acts as a fallback toggle for enable_thinking.
temperature / top_p / top_k / penalties / seed / stop_sequences	Unrestricted (any valid schema value; temperature >= 0.0, 0.0 <= top_p <= 1.0, top_k >= 1; seed is forwarded in the outgoing request body to LiteLLM/vLLM)
[!WARNING] Execution-altering ADK fields that bypass the sandbox (tools, system_instruction, http_options, safety_settings, or response_schema inside generate_content_config) are excluded from GenerateContentConfig by schema design and will raise a validation error if set.

3. Model Registry, Single-Model Rule & LoRA Adapters on 4x L4 GPUs
3.1. Computational Environment: 4x NVIDIA L4 GPUs (96 GB Total VRAM)
The competition evaluation environment runs on a dedicated machine equipped with 4 × NVIDIA L4 GPUs:

Per-GPU Memory: 24 GB GDDR6 (96 GB total VRAM across 4 GPUs).
vLLM Serving Configuration (setup_vllm_server in scripts/inference.py):
tensor_parallel_size = 4 (shards model weights and KV cache across all 4 L4 GPUs)
gpu_memory_utilization = 0.90 (~21.6 GB usable per GPU / ~86.4 GB usable across 4 GPUs)
max_model_len = 32768 (32,768 tokens maximum combined prompt + reasoning + output context length)
scheduler_reserve_full_isl = False
enable_auto_tool_choice = True, tool_call_parser = "gemma4", reasoning_parser = "gemma4", reasoning_config = {"reasoning_start_str": "<|channel>", "reasoning_end_str": "<channel|>"}
Dynamic LoRA right-sizing from discovered adapters: enable_lora = bool(adapter_map), max_loras = max(1, len(adapter_map)), max_lora_rank = max(detected_ranks) if detected_ranks else 128
Model-specific CLI flags (MODEL_VLLM_EXTRA_ARGS): for gemma-4-12b-it-qat-w4a16-ct, passes --hf-overrides '{"architectures": ["Gemma4ForCausalLM"], "model_type": "gemma4"}'
Base Model Variant	Weight Precision	Approximate Weight Footprint (4 GPUs)	Competition Status
gemma-4-31b-it-qat-w4a16-ct (Starter Kit Default)	W4A16 (INT4 Quantized)	~16–18 GB (~4.5 GB / GPU)	Permitted in Kaggle competition submissions (ALLOWED_MODEL_NAMES)
gemma-4-12b-it-qat-w4a16-ct	W4A16 (INT4 Quantized)	~6–8 GB (~1.8 GB / GPU)	Permitted in Kaggle competition submissions (ALLOWED_MODEL_NAMES)
gemma-4-e4b-it-qat-w4a16-ct	W4A16 (INT4 Quantized)	~3–4 GB (~1.0 GB / GPU)	Permitted in Kaggle competition submissions (ALLOWED_MODEL_NAMES)
gemma-4-e2b-it-qat-w4a16-ct	W4A16 (INT4 Quantized)	~2–3 GB (~0.7 GB / GPU)	Permitted in Kaggle competition submissions (ALLOWED_MODEL_NAMES)
gemma-4-31b-it / gemma-4-27b-it / gemma-4-26b-a4b-it / gemma-4-12b-it / gemma-4-9b-it / gemma-4-e4b-it / gemma-4-e2b-it	bfloat16	~4–62 GB	Registered in models.yaml / _build_cli_model_registry() for local CLI evaluation only
This 4x L4 hardware budget directly governs four operational rules:

Single Base Model Per Submission (ALLOWED_MODEL_NAMES): All agents in a competition submission must share a single declared base model from the four official quantized models (gemma-4-31b-it-qat-w4a16-ct, gemma-4-12b-it-qat-w4a16-ct, gemma-4-e4b-it-qat-w4a16-ct, or gemma-4-e2b-it-qat-w4a16-ct).
Context Window Ceiling (32,768 tokens): max_output_tokens and thinking_budget cannot exceed 32,768 tokens, matching vLLM's max_model_len=32768.
Total Unpacked Submission Size (< 3 GiB including adapters/): Ensures rapid extraction into /kaggle/working/submission and guarantees that submitted LoRA adapters fit within host disk and GPU LoRA buffers (max_lora_rank <= 128).
Host Container Sandboxing (4 GiB RAM / 2 vCPUs per container): Prevents repository test suites inside Container A and Container B from starving the host CPU or system RAM needed by the vLLM engine.
3.2. The Single Base Model Rule
Before launching the inference server, validate_single_declared_model(agent_dir) (scripts/inference.py, wrapping adk_submission.discovery.discover_declared_models) traverses the root agent.yaml, every referenced sub_agents[*].config_path, every tools[*].agent_tool.config_path, and any standalone agent YAML files in the submission directory.

Provider prefixes (openai/, google/, hosted_vllm/, custom/) are stripped (normalize_model_name).
All agents in a submission must declare at most ONE unique base model (len(models) == 1), and in Kaggle competition scoring (scripts/inference.py) that model must be in ALLOWED_MODEL_NAMES (gemma-4-31b-it-qat-w4a16-ct, gemma-4-12b-it-qat-w4a16-ct, gemma-4-e4b-it-qat-w4a16-ct, or gemma-4-e2b-it-qat-w4a16-ct). Declaring multiple base models or an unpermitted model raises ParticipantVisibleError.
3.3. Pre-Registered Model Aliases & Routing
models.yaml and _build_cli_model_registry() (src/swegemma/cli.py) register the following Gemma 4 aliases in ModelRegistry:

gemma-4-31b-it-qat-w4a16-ct, gemma-4-12b-it-qat-w4a16-ct, gemma-4-e4b-it-qat-w4a16-ct, gemma-4-e2b-it-qat-w4a16-ct (Official quantized competition models)
gemma-4-31b-it, gemma-4-31b
gemma-4-27b-it, gemma-4-27b
gemma-4-26b-a4b-it, gemma-4-26b-a4b, diffusiongemma-26b-a4b-it
gemma-4-12b-it, gemma-4-12b
gemma-4-9b-it, gemma-4-9b
gemma-4-e4b-it, gemma-4-e4b
gemma-4-e2b-it, gemma-4-e2b
When the scoring harness starts the local inference server (setup_vllm_server in scripts/inference.py, running VllmServer on 127.0.0.1:8000 with tool_call_parser='gemma4', reasoning_parser='gemma4', max_model_len=32768), it binds the declared model alias (and any discovered adapter names) to LiteLlm(model=f"openai/{served_model}", api_base="http://127.0.0.1:8000/v1") via server_instance.create_model_registry(...) (or fallback LiteLlm(..., num_retries=3) when attaching to an already-active endpoint) and installs the LiteLLM reasoning patch (install_litellm_reasoning_patch()).

3.4. Multi-LoRA Serving (adapters/) & Sizing Guidelines
Even though a submission is restricted to a single base model, different agents in your hierarchy can use different fine-tuned LoRA adapters:

Place PEFT LoRA directories (containing adapter_config.json and adapter_model.safetensors) inside adapters/<adapter_name>/. Adapter names must match [A-Za-z0-9][A-Za-z0-9._-]*.
Reference adapter: <adapter_name> on any LlmAgent in agent.yaml or sub_agents/*.yaml (for example, adapter: main_lora on the root coder agent and adapter: tool_lora on a read-only analyzer AgentTool).
discover_adapters() automatically registers all discovered adapters with the vLLM server, right-sizing enable_lora = bool(adapter_map), max_loras = max(1, len(adapter_map)), and max_lora_rank = max(detected_ranks) if detected_ranks else 128 (passed under --lora-modules name1=path1 name2=path2 ...), and resolve_swegemma_adapter (src/swegemma/harness/agent_runner.py) routes requests from each agent to its respective openai/<adapter_name> model identifier.
Adapter Size Planning (< 3 GiB total budget):
For a 31B model in bfloat16 targeting all linear projections (q_proj, k_proj, v_proj, o_proj, gate_proj, up_proj, down_proj):
Rank r = 16: ~110–220 MB per adapter (easily fits 8 specialized adapters within < 3 GiB).
Rank r = 32: ~220–450 MB per adapter (fits 6–8 adapters within < 3 GiB).
Rank r = 64: ~450–900 MB per adapter (fits 3–6 adapters within < 3 GiB).
Rank r = 128 (max_lora_rank): ~0.9–1.8 GB per adapter (fits 1–3 high-rank adapters within < 3 GiB).
4. Two-Phase Evaluation Lifecycle
Every benchmark task (Task) is evaluated in two completely separate sandbox lifecycles (Container A for agent execution and Container B for verification).

sequenceDiagram
    participant Eval as Evaluator (evaluate.py)
    participant ContA as Container A (Agent Sandbox)
    participant Agent as Compiled ADK Agent
    participant ContB as Container B (Verification Sandbox)

    Eval->>ContA: 1. Start sandbox & setup wheels
    Eval->>ContA: 2. Extract repo snapshot at base_commit (zero future git history)
    Eval->>ContA: 3. Write .git/info/exclude + pip install -e /workspace --no-deps
    Eval->>ContA: 4. Stream cached test site-packages + setup pytest.ini & conftest.py
    Eval->>ContA: 5. git add -A && git commit -m "baseline"
    Eval->>Agent: 6. Start timer (start_agent_session) & send build_agent_prompt()
    loop Until submit_patch(), timeout, budget exhausted, or 3 nudges
        Agent->>ContA: Invoke tools (run_command, read_file, edit_file, etc.)
        ContA-->>Agent: JSON tool response
    end
    Eval->>ContA: 7. Extract agent_patch (submitted_patch or fallback git add -N . && git diff HEAD)
    Eval->>ContA: 8. Stop & clean Container A
    Note over Eval,ContB: If agent_patch is non-empty (or no agent_error), proceed to Phase 2
    Eval->>ContB: 9. Start clean Container B & repeat steps 1–5 (commit "eval_baseline")
    Eval->>ContB: 10. Apply agent_patch (4-pass resilient git apply / patch)
    Eval->>ContB: 11. Reset target test files (git checkout HEAD & git clean -f)
    Eval->>ContB: 12. Apply task.test_patch
    Eval->>ContB: 13. Run hermetic pytest on target test files
    ContB-->>Eval: 14. exit_code == 0 -> resolved = True (score = 1.0)
4.1. Sandbox Hardware & Isolation (ContainerManager vs SubprocessManager)
Docker Backend (--sandbox docker, default):
Image: swebench-sandbox:latest
Network: network_mode="none" (completely air-gapped; no internet or PyPI access)
Memory Limit: 4g (4 GiB; processes exceeding this are killed with exit code 137 / SIGKILL)
CPU Quota: 2 vCPUs (cpu_period=100_000, cpu_quota=200_000)
Working Directory: /workspace (TEST_TMPDIR=/tmp)
Container Reuse (reuse_containers=False default): By default, each phase creates and destroys a fresh container for strict isolation. If warm pooling (reuse_containers=True) is explicitly enabled, /workspace is completely wiped (find /workspace -mindepth 1 -maxdepth 1 -exec rm -rf {} +), temporary patches/stubs in /tmp and /var/tmp are removed, and the container is returned to an idle pool.
Subprocess Backend (--sandbox subprocess, used in environments without a Docker daemon):
Creates an isolated temporary directory swegemma_sandbox_<id>_ with workspace/, tmp/, wheels/, and an isolated virtual environment venv/.
Rewrites shell references to /workspace, /tmp, /wheels, and /usr/local/bin to the sandbox directory paths and executes commands in a new process group (start_new_session=True), terminating the full process group (os.killpg(..., signal.SIGKILL)) on command timeout.
4.2. Container Bootstrap Sequence (container_setup.py)
Before the agent is invoked (in Container A) and before verification runs (in Container B), the harness performs 7 deterministic setup steps:

Wheel Staging (setup_container_wheels): Prepares /wheels inside the sandbox.
Snapshot Extraction (extract_snapshot): Extracts the repository .tgz archive (or reconstructs it from a deduplicated base snapshot + binary patch) into /workspace, removing any circular symlinks.
Zero Future Git History: Repository snapshots are built via git fast-export up to base_commit piped into a fresh git fast-import repository on branch main. Commits after base_commit do not exist in .git.
Git Exclude Configuration (setup_git_exclude): Appends the following patterns to /workspace/.git/info/exclude so build/test artifacts never pollute git diff:
   __pycache__/
   *.pyc
   .pytest_cache/
   *.egg-info/
   build/
   dist/
   .coverage
Editable Package Installation (install_editable_package):
   pip install --no-index --find-links=/wheels --no-build-isolation --no-deps -e /workspace 2>/dev/null || true
Test Dependency Installation (install_test_dependencies): Streams cached unpacked site-packages (<20 MB base wheels and >=20 MB workspace-imported wheels) into the container Python environment and runs /sandbox/setup.py --fast-path <repo>.
Hermetic Test Configuration (setup_workspace_test_config): Writes a standardized /workspace/pytest.ini (disabling anyio and ignoring hardware/device/example directories) and prepends a hermetic test collection hook to /workspace/conftest.py.
Baseline Commit (setup_baseline_commit):
   cd /workspace && git add -A && git commit -m "baseline" --allow-empty -q
[!IMPORTANT] Because setup_workspace_test_config runs before setup_baseline_commit, the harness-generated /workspace/pytest.ini and /workspace/conftest.py are committed into HEAD (baseline). Do not delete or modify /workspace/pytest.ini or /workspace/conftest.py unless required by the task, as changes to them will appear in git diff HEAD.

5. Harness-to-Agent Interaction Protocol
5.1. Session State Variables
When run_agent_sandbox creates the ADK session (app_name='swegemma_eval', user_id='eval_user'), it populates session.state with:

problem_description: task.problem_statement (str)
hints: task.hints_text.strip() (str, only present if task.hints_text is non-empty)
Because Google ADK automatically interpolates {variable_name} placeholders in LlmAgent.instruction from session.state, your agent.yaml or sub-agent instructions can reference {problem_description} Directly.

5.2. Initial User Prompt (build_agent_prompt)
The harness sends a structured initial user message to the root agent containing up to 7 sections (automatically filtering tool-specific rules and sections against _extract_declared_tools(agent) so undeclared tools are never advertised):

Task Header & Problem Statement:
   You are evaluating a software engineering task for repository {task.repo}.

   Problem Statement:
   {task.problem_statement}
Hints (included if task.hints_text is non-empty):
   ## Hints:
   {task.hints_text}
Task Budget (lists all active limits from EvalConfig.budget):
   ## Task Budget (Session terminates when any budget is exhausted)
   - Time allowance: 60.0 minutes
   - Tool calls allowance: <N> calls
   - Max loop iterations: <N> turns
   - Cost budget: $<X.XX> USD
Execution Environment Rules (the read_file line/character limits are omitted if read_file is not declared in the agent hierarchy):
   ## Execution Environment Rules
   - Single command timeout: 300 seconds (commands exceeding this fail without ending the session)
   - Command output limit: 5000 characters
   - File view limit: 150 lines per read_file call
   - File character limit: 10000 characters per read_file call
   - Environment is offline (no network/PyPI access). All repository and test dependencies are ALREADY pre-installed. Do NOT attempt to run pip install or download packages.
Standard Instructions (0–5):
Directs the agent to work strictly under /workspace, inspect existing conventions, verify its implementation using targeted tests or inline assertions before submitting, call submit_patch once complete, and return a final text completion response.
Note on in-sandbox testing: By default, enable_sandbox_testing = True is active in EvalConfig and agent_runner.py, so pytest and unittest are available inside Container A and Instruction 3 reads: "3. Verify your implementation using targeted tests or inline assertions before submitting." (If enable_sandbox_testing is explicitly set to False, the harness masks pytest and unittest inside Container A and adjusts Instruction 3 to advise inline python3 -c "..." assertions).
Code Intelligence Tools (conditionally appended when pre-computed graph .json and embedding .npz files >100 bytes exist for task.repo, listing only the graph/embedding tools actually declared across the agent hierarchy):
   ## Code Intelligence Tools
   This repository has pre-built code graph and embedding data indexed by dotted symbol path (e.g. `pkg.module.Class.method`). Use these tools for fast, targeted navigation:
   - `search_similar_code(query)`: Find functions/classes with similar precomputed embeddings given a symbol name (e.g. `pkg.module.Class.method`, `Class.method`, or `func_name`).
   - `get_code_neighbors(node)`: Find callers and callees of a symbol node (e.g. `pkg.module.Class.method` or `Class.method`).
   - `get_code_subgraph(nodes)`: Get the induced call subgraph connecting a list of symbol nodes.
Workspace Layout: Captures up to 200 breadth-first-selected, hierarchically sorted entries of find . -mindepth 1 -maxdepth 3 inside /workspace (excluding hidden files/directories, __pycache__, *.pyc, *.egg-info, and pruning non-package bulk directories such as docs, docs_src, tests, examples, and benchmarks below depth 1).
5.3. Multi-Turn Loop, Tool Error Recovery, Continuation Nudges & Termination
The harness drives the root agent in an outer while True: loop (agent_runner.py):

Tool Error Recovery (ToolErrorPlugin & Skill Argument Coercion):
ToolErrorPlugin registers on_tool_error_callback on the ADK App so that undeclared/hallucinated tool calls or runtime tool exceptions are caught and returned to the model as structured error dictionaries ({"status": "error", "error_type": "<ExcName>", "error": "<Message>"}) instead of aborting the task.
Both ToolErrorPlugin.before_tool_callback and _wrap_skill_toolset_tools automatically coerce single-element list strings (["name"] $\to$ "name") for skill_name and file_path on ADK SkillToolset tools (load_skill, load_skill_resource, run_skill_script).
Immediate Termination on submit_patch():
During runner.run_async(...), if the agent calls submit_patch(), context.patch_submitted becomes True.
As soon as the agent emits a final text response (is_final_response()) or finishes the current run_async turn with context.patch_submitted == True, the harness immediately breaks out of the agent loop and proceeds to patch extraction and Phase 2 verification.
Continuation Nudge Mechanism (max_nudges = 3):
If an ADK turn ends (runner.run_async returns) without submit_patch() having been called—for instance, because the model emitted a text-only response or hit max_output_tokens—and budgets are still available:
If the turn executed at least one valid tool call (turn_has_tool_call == True), consecutive_nudges resets to 0.
Otherwise, consecutive_nudges increments (1, 2, 3). If consecutive_nudges > 3, the loop terminates.
The harness sends one of three targeted user continuation messages based on how the turn ended:
Condition Detected on Turn Exit	Injected Continuation User Message (nudge_prompt)
Unclosed <|tool_call> tag in last_assistant_text	"Your previous response reached the token limit before the tool call finished closing (<|tool_call|> was cut off). Do NOT repeat your prior reasoning in thought—emit your next tool call immediately, and if calling edit_file or write_file, split the change into smaller incremental edits."
finish_reason contains MAX_TOKENS or LENGTH	"Your previous response reached the token limit while thinking before a tool call was completed. Do NOT repeat your analysis in thought—keep reasoning under a few sentences and emit your next tool call immediately, or call submit_patch when you have completed and verified your changes."
Normal stop without calling submit_patch	"Please continue your work using the available tools, or call submit_patch when you have completed and verified your changes."
6. Built-In Tools Reference (swegemma.tools)
SwegemmaContext.create_tools() registers 9 tools in ToolRegistry. Your agent.yaml (and any sub-agent YAML) can attach any subset of these tools by name under tools:.

All @budget_gated tools return a JSON string with either:

Success: {"status": "ok", ...}
Error: {"status": "error", "error_type": "<Type>", "error_message": "<Message>", "details": {...}}
If budget.tool_calls >= 20 and remaining_tool_calls <= 10, every tool response automatically includes an extra field:

"budget_warning": "Only X tool call(s) remaining (Y/Z used). Finalize your edits and call submit_patch soon."
6.1. Execution & Lifecycle Tools (swegemma/tools/execution.py)
1. run_command(command: str) -> str
Executes a shell command in /bin/bash -c inside /workspace.

Counts toward tool_calls budget: Yes (@budget_gated).
Timeout: min(command_timeout_seconds [300s], max(5, int(remaining_time_seconds))). If a command times out, it returns error_type: "TimeoutExceeded" without ending the agent session (unless the overall session time budget has also expired).
Output Truncation: Both stdout and stderr are truncated to max_stdout_chars (5,000 characters default).
Returns:
If exit_code == 0: json {"status": "ok", "stdout": "...", "stderr": "...", "exit_code": 0}
If exit_code != 0: json { "status": "error", "error_type": "CommandError", "error_message": "...", "details": {"stdout": "...", "stderr": "...", "exit_code": 1} }
2. submit_patch() -> str
Stages untracked file intents (git add -N .), captures git diff --binary _swegemma_baseline 2>/dev/null || git diff --binary HEAD from /workspace, stores it in ctx.submitted_patch, and sets ctx.patch_submitted = True.

Counts toward tool_calls budget: No (@budget_gated(count_tool_call=False)). Can still be called even when tool_calls_used == budget.tool_calls (as long as session wall-clock time remains).
Session Effect: Marks the task patch as submitted; once the agent finishes its response for the current turn, the harness exits the agent loop.
Returns:
  {"status": "ok", "patch_size": 1420, "files_changed": 2}
3. get_status() -> str
Returns live budget consumption and patch status.

Counts toward tool_calls budget: No (@budget_gated(count_tool_call=False)).
Returns:
  {
    "status": "ok",
    "tool_calls_used": 12,
    "patch_submitted": false,
    "patch_size": 0,
    "tool_calls_remaining": 38,
    "max_tool_calls": 50,
    "time_seconds_remaining": 3120.4,
    "max_time_minutes": 60.0,
    "agent_elapsed_seconds": 479.6,
    "max_turns": 500,
    "command_timeout_seconds": 300
  }
6.2. Workspace File Tools (swegemma/tools/workspace.py)
All file paths are resolved relative to /workspace via _resolve_workspace_path(filepath). Leading / or /workspace/ prefixes are stripped automatically ("/workspace/src/app.py" and "src/app.py" both resolve to /workspace/src/app.py). Any path containing .. traversal raises a ValidationError. String-encoded integer and boolean arguments emitted by LLMs (e.g., "10" for start_line or "true" for allow_multiple) are automatically coerced to their target Python types.

4. read_file(filepath: str, start_line: int | None = None, end_line: int | None = None) -> str
Reads a file from /workspace with 1-indexed inclusive line slicing.

Counts toward tool_calls budget: Yes (@budget_gated).
Dual Truncation Cap: Output is capped at 150 lines (max_file_lines) AND 10,000 characters (max_file_chars). If either limit is reached, is_truncated is set to true and end_line reflects the last complete line returned.
Returns:
  {
    "status": "ok",
    "filepath": "src/module.py",
    "content": "...",
    "start_line": 1,
    "end_line": 150,
    "total_lines": 420,
    "is_truncated": true
  }
5. edit_file(filepath: str, old_string: str, new_string: str, allow_multiple: bool = False) -> str
Replaces old_string with new_string in an existing non-empty file inside /workspace using adk-eval-core's 3-tier resilient matching engine (apply_replacement):

Counts toward tool_calls budget: Yes (@budget_gated).
3-Tier Matching Algorithm:
exact: Exact character-for-character substring match (after normalizing \r\n $\to$ \n).
flexible: Line-by-line match after stripping leading/trailing whitespace on each line. Automatically re-indents new_string to preserve the matched block's baseline indentation.
regex: Tokenizes old_string around code delimiters ( ) : [ ] { } > = < and joins tokens with flexible whitespace (\s*), matching across minor line-wrapping or spacing discrepancies.
Uniqueness Check: If old_string matches more than 1 occurrence and allow_multiple=False, returns error_type: "FileEditError" without modifying the file. Also returns FileEditError if the file does not exist, the file is empty (0 bytes), or old_string is empty ("").
Returns (diff is truncated at max_stdout_chars = 5,000 chars):
  {
    "status": "ok",
    "filepath": "src/module.py",
    "occurrences": 1,
    "strategy": "exact",
    "diff": "--- a/src/module.py\n+++ b/src/module.py\n...",
    "is_truncated": false
  }
6. write_file(filepath: str, content: str) -> str
Creates or overwrites a file at /workspace/<filepath>, automatically creating parent directories (mkdir -p).

Counts toward tool_calls budget: Yes (@budget_gated).
Returns:
  {"status": "ok", "filepath": "tests/repro.py", "size": 342}
6.3. Code Intelligence Graph Tools (swegemma/tools/graph.py)
When pre-computed AST/dependency graphs (data/graphs/<repo>.json) and node embeddings (data/embeddings/<repo>.npz) are available for a repository, these three tools query the in-memory NetworkX CustomMultiDiGraph (automatically coercing string-encoded integers for max_neighbors/k and JSON or comma-separated strings for nodes):

7. get_code_neighbors(node: str, edge_type: str | None = None, max_neighbors: int = 50) -> str
Finds incoming and outgoing neighbors of a symbol (node) in the repository call/dependency graph.

Counts toward tool_calls budget: Yes (@budget_gated).
Symbol Resolution: resolve_node_name resolves short names (e.g., "FastAPI.get" or "Request") via 4 tiers: exact match $\to$ . / ; / / suffix match $\to$ case-insensitive match $\to$ substring match.
edge_type: Optional case-insensitive filter (e.g., "calls").
Output Truncation: Neighbor names are capped to max_stdout_chars (5,000 characters default), setting is_truncated: true if truncated.
Returns:
  {"status": "ok", "node": "fastapi.applications.FastAPI", "neighbors": ["..."], "count": 18, "is_truncated": false}
8. search_similar_code(query: str, k: int = 10) -> str
Finds top-k graph nodes with highest cosine similarity to query in the pre-computed .npz embedding archive.

Counts toward tool_calls budget: Yes (@budget_gated).
Query Resolution Note: Because the sandbox runs offline without a live neural embedding server, search_similar_code resolves query against node keys/suffixes stored in the graph and .npz dictionary and computes cosine similarity across all other nodes in the repository (with a lexical token-overlap fallback if query is a multi-word keyword string). Pass a dotted symbol path or short symbol name (e.g., "pkg.module.Class.method", "Class.method", or "parse_header").
Output Truncation: Total code characters across all k returned nodes are capped at max_stdout_chars (5,000 characters default), dividing the budget across the k results with unused characters from shorter snippets rolling over to larger nodes, and setting is_truncated: true when any snippet is truncated.
Returns:
  {
    "status": "ok",
    "query": "HTTPConnection",
    "results": [{"node_name": "...", "code": "...", "similarity": 0.9412}],
    "count": 10,
    "is_truncated": false
  }
9. get_code_subgraph(nodes: list[str]) -> str
Extracts the induced subgraph (all nodes and interconnecting edges) for a list of symbols.

Counts toward tool_calls budget: Yes (@budget_gated).
Output Truncation: Returned nodes and edges lists are capped to max_stdout_chars (5,000 characters default), setting is_truncated: true if truncated.
Returns:
  {
    "status": "ok",
    "nodes": ["node_a", "node_b"],
    "edges": [{"from": "node_a", "to": "node_b", "type": "calls"}],
    "node_count": 2,
    "edge_count": 1,
    "is_truncated": false
  }
7. Budgets, Operational Limits & Context Management
7.1. Default Budgets (EvaluationBudget), eval_config.yaml & Harness Limits (HarnessLimits)
Participants can customize per-task budgets in their submission's eval_config.yaml (under the evaluation: key), which scripts/inference.py loads via load_submission_eval_config():

evaluation:
  timeout_seconds: 300
  max_tool_calls: 10
  max_time_minutes: 1
  max_turns: 50
Parameter	EvalConfig Path	swegemma / inference.py Default	eval_config.yaml Key	Environment Variable Override (scripts/inference.py)
Session Wall-Clock Time	config.budget.time_minutes	60.0 min (1 min in sample submission)	evaluation.max_time_minutes	SWE_MAX_TIME_MINUTES
Max Tool Calls	config.budget.tool_calls	100 in inference.py / None in CLI (10 in sample submission)	evaluation.max_tool_calls	SWE_MAX_TOOL_CALLS
Max LLM Reasoning Turns	config.budget.turns	500 turns (None $\to$ 500; 50 in sample submission)	evaluation.max_turns	SWE_MAX_TURNS / SWE_MAX_LOOP_ITERATIONS
Max Monetary Cost	config.budget.cost_usd	None	—	—
Max Total Tokens	config.budget.total_tokens	None	—	—
Single Command Timeout (Phase 1)	config.harness.command_timeout_seconds	300 sec	evaluation.timeout_seconds	SWE_TIMEOUT_SECONDS
Verification pytest Timeout (Phase 2)	config.harness.verification_timeout_seconds	300 sec	— (fixed at 300 sec on scorer)	SWE_TIMEOUT_SECONDS (metric.py)
Max Command / Diff / Graph Output	config.harness.max_stdout_chars	5,000 chars	—	—
Max Lines per read_file	config.harness.max_file_lines	150 lines	—	—
Max Chars per read_file	config.harness.max_file_chars	10,000 chars	—	—
[!TIP] Container Setup Is Excluded from Agent Time Budget: SwegemmaContext records task_start_time at creation, but agent_start_time is started via context.start_agent_session() only after container creation, wheel installation, and snapshot extraction finish. Your time_minutes budget measures strictly the time spent inside the agent loop.

7.2. Automatic Context Window Compaction & Prefix Caching
To prevent long debugging trajectories from overflowing the 32,768-token context window on vLLM, swegemma configures Google ADK's context management on the App instance (scripts/inference.py):

Events Compaction (EventsCompactionConfig):
compaction_interval = 15
overlap_size = 2
token_threshold = 14,336
event_retention_size = 5
Context Caching (ContextCacheConfig):
min_tokens = 2,048, ttl_seconds = 1,800, cache_intervals = 10
Transient Model Error Retries (ModelRetryPlugin):
Automatically intercepts transient LLM/proxy errors (429, 500, 502, 503, 504, RateLimitError, APIConnectionError, APITimeoutError) and retries up to 5 times with exponential backoff (2.0s initial delay, 2.0x multiplier, max 60.0s, $\pm 20\%$ jitter).
8. Patch Extraction & Phase 2 Verification (verify_task)
8.1. How Patches Are Extracted (submit_patch & Automatic Fallback)
Both explicit submit_patch() calls and the harness fallback run the exact same git commands inside /workspace in Container A:

cd /workspace && git add -N .
cd /workspace && (git diff --binary _swegemma_baseline 2>/dev/null || git diff --binary HEAD)
Why git add -N . Matters: --intent-to-add registers any newly created untracked files in the git index with empty content so that git diff includes both modifications to existing tracked files AND newly created files, while still ignoring everything listed in /workspace/.git/info/exclude (__pycache__/, *.pyc, .pytest_cache/, *.egg-info/, build/, dist/, .coverage).
Protected Test & Config File Stripping (strip_protected_files_from_patch): Both submit_patch() and the unsubmitted-diff fallback strip any diff hunks touching protected test files (test_*.py, *_test.py, or .py files under tests/, test/, testing/) or runner configuration files (conftest.py, pytest.ini, pyproject.toml, tox.ini, setup.cfg, .pytest.ini, sitecustomize.py, usercustomize.py, _swegemma_stubs.py, *.pth) so temporary reproduction tests or runner config edits never pollute agent_patch.
Automatic Unsubmitted Patch Recovery (agent_runner.py, lines 760–805): If an agent finishes, hits max_nudges, exhausts tool_calls or turns, or times out without ever calling submit_patch(), the harness automatically runs git add -N ., git diff --binary _swegemma_baseline, and strip_protected_files_from_patch before tearing down Container A.
If the working tree contains non-empty modifications to non-protected files (agent_patch != ""), Evaluator.evaluate_task (evaluate.py) and scripts/inference.py still record agent_patch in submission.parquet ([id, prediction]) so it is evaluated in Phase 2 verification!
8.2. Phase 2 Verification in Container B (harness/verification.py & scripts/metric.py)
During Stage 2 scoring (score(solution, submission, row_id_column_name) in scripts/metric.py), when agent_patch (prediction) is non-empty, verify_task executes in a fresh Container B:

Fresh Baseline Setup: Extracts the original task snapshot at base_commit, configures synthetic hardware stubs and .git/info/exclude, installs /workspace in editable mode, installs cached test dependencies, writes /workspace/pytest.ini and /workspace/conftest.py, and creates the eval_baseline commit (HEAD).
Protected File Stripping & 4-Pass Resilient Patch Application (strip_protected_files_from_patch & apply_patch_in_container): Strips any diff sections targeting protected test or runner configuration files (strip_protected_files_from_patch) and applies the remaining agent_patch using four fallback passes until one succeeds:
Pass 1: git apply --unsafe-paths (-p1), then -3 (three-way merge), then --ignore-space-change --ignore-whitespace, then --recount.
Pass 2: Symlink-normalized git apply --unsafe-paths (-p1) (resolving symlinked directory prefixes inside /workspace).
Pass 3: Prefixless -p0 git apply --unsafe-paths.
Pass 4: Non-interactive GNU patch -p1 / patch -p0 (--batch --forward and -l), gated by --dry-run.
If all 4 passes fail (exit_code != 0), the task immediately fails with resolved = False (error = "Failed to apply agent patch: ...").
Anti-Tampering Test & Config Reset (_is_protected_test_or_config_path): The harness identifies all file paths referenced in task.test_patch plus any protected test files (test_*.py, *_test.py, or .py files under tests/, test/, testing/) and test runner configuration files (conftest.py, pytest.ini, pyproject.toml, tox.ini, setup.cfg, .pytest.ini, sitecustomize.py, usercustomize.py, _swegemma_stubs.py, *.pth) touched by agent_patch, and forcefully resets them to eval_baseline (HEAD) before applying task.test_patch (filtering tracked pathspecs via git ls-tree so missing pathspecs do not abort git checkout):
   cd /workspace && tracked=$(git ls-tree -r --name-only HEAD -- <files_to_reset> 2>/dev/null) \
     && [ -n "$tracked" ] && git ls-tree -r -z --name-only HEAD -- <files_to_reset> \
     | xargs -0 git checkout HEAD -- 2>/dev/null || true
   cd /workspace && git clean -f -- <files_to_reset> 2>/dev/null || true
[!CAUTION] Any modifications your agent makes to test files or runner configurations (conftest.py, pytest.ini, pyproject.toml, etc.) are stripped and discarded before task.test_patch is applied. Your agent must fix the underlying library code under /workspace, not alter test files or test runner hooks.

Apply task.test_patch & Run Hermetic pytest with JUnit XML: The harness applies task.test_patch, refreshes test dependencies and pytest.ini/conftest.py, and runs:
   cd /workspace && PYTHONSAFEPATH=1 PYTHONNOUSERSITE=1 python3 -s -m pytest <pytest_targets> \
     --junitxml=/tmp/_swegemma_junit_<id>.xml \
     -p no:anyio -o timeout=0 \
     -o python_classes="Test* *Test" -q
Resolution Criterion (resolved), JUnit XML Validation & Competition Score:
A task is marked resolved = True (score = 1.0) if and only if:
pytest exits with return code 0 (test_res.exit_code == 0), and
_validate_junit_xml confirms the JUnit XML report exists, has passed_tests > 0, failures == 0, errors == 0, and every required test node (FAIL_TO_PASS, PASS_TO_PASS, or test functions extracted from test_patch) explicitly passed without being skipped.
The overall competition metric returned by score() in scripts/metric.py is the Resolution Rate:
9. Local CLI Usage & Output Artifacts
9.1. Running Evaluations Locally (swegemma eval)
You can evaluate any submission directory locally against the published training tasks using the swegemma CLI:

swegemma eval \
  --tasks tasks.jsonl \
  --snapshots-dir snapshots \
  --submission-dir sample_submission \
  --results-dir results/run_01 \
  --sandbox docker \
  --max-tool-calls 50 \
  --max-time-minutes 30 \
  --concurrency 2 \
  --display auto
Key CLI flags (swegemma eval):

--task-id <id> / --task-ids <id1> <id2>: Evaluate specific instance_id(s) for rapid debugging.
--sandbox {docker,subprocess}: Choose Docker containers (default) or local subprocess isolation.
--timeout-seconds <sec>: Per-command timeout in seconds for Phase 1 agent tool calls (default 300).
--verification-timeout-seconds <sec>: Timeout in seconds for Phase 2 pytest verification runs (default 300).
--concurrency <N>: Evaluate N tasks in parallel (automatically activates the multi-slot dashboard UI).
--shard-index <k> --num-shards <M>: Run deterministic shard k of M (task_idx % M == k).
--models-yaml <path>: Custom models.yaml file mapping model aliases and token pricing.
--skip-agent-patch: Bypass Phase 1 (agent_patch = "") to verify baseline test failure behavior in Phase 2.
9.2. Results Directory Artifacts (--results-dir)
Every evaluation run writes incremental, crash-safe outputs to --results-dir:

results/run_01/
├── summary.json                        # Aggregate resolution_rate, resolved count, per-repo breakdown, and errors
├── task_results.jsonl                  # Append-only JSONL (one line per finished task with metrics & exit codes)
├── patches/
│   └── <instance_id>.patch             # Exact unified diff extracted from Container A
├── test_outputs/
│   └── <instance_id>.log               # Complete STDOUT/STDERR from Phase 2 pytest verification in Container B
├── traces/
│   └── trace_<instance_id>.json        # Full SessionTrace (ATIF-compatible steps, thoughts, tool calls, token usage)
└── logs/
    └── <instance_id>.log               # Rich formatted transcript of the Phase 1 agent session
10. Competitor Best Practices & Important Gotchas
Keep edit_file Payloads Focused to Avoid <|tool_call> Truncation: When an LLM generates a massive thought block followed by a large edit_file or write_file tool call that hits max_output_tokens, the <|tool_call> tag gets cut off before closing. Set a reasonable thinking_budget (e.g., 4096 with max_output_tokens: 16384) and instruct your agent to apply incremental edits.
Do Not Modify /workspace/pytest.ini or /workspace/conftest.py: The harness writes these two files and commits them into the baseline commit before your agent starts. If your agent deletes or modifies them, those diffs will be included in agent_patch.
Clean Up Temporary Reproduction Scripts Before submit_patch() (or Put Them in /tmp): Because submit_patch() runs git add -N . && git diff HEAD inside /workspace, any untracked reproduction script created inside /workspace (e.g., /workspace/repro.py) will be included in your patch! Put scratch test scripts in /tmp/repro.py via run_command, or delete them from /workspace before calling submit_patch().
Use AgentTool (skip_summarization: true) to Isolate Context Window Usage: File exploration and graph traversal can quickly consume tokens. Delegating code search to a read-only sub-agent wrapped as an agent_tool (like sub_agents/code_analyzer.yaml in the starter kit) keeps intermediate read_file outputs out of the root coder agent's main context history.
Always Call submit_patch() Explicitly, Know That It Is Free, and Do It Last: submit_patch() does not count toward tool_calls (count_tool_call=False), and get_status() is also completely free. However, once a turn completes with patch_submitted == True, the harness immediately terminates the agent loop. Always verify your changes first, clean up any scratch files in /workspace, and call submit_patch() as your final tool action.
Data Explorer
22.42 GB

docker

embeddings

graphs

sample_submission

sandbox

snapshots

wheels

HARNESS_README.md

tasks.jsonl

Summary
524 files


Download All
Metadata

Models
Most Usage
Gemma 4 · gemma-4-31b-it-qat-w4a16-ct
Google · Other · Gemma is a family of lightweight, state-of-the-art open models from Google, built from the same research and technology used to create the Gemini models.
11 users · Best public score: 0.03
ModelBuddy · gemma4
Jeki Wan Taufik · Transformers · An open machine learning model designed for flexible inference, fine-tuning, and AI experimentation
1 user · Best public score: 0.08
Tabular Apex Calibrated Meta-Stacker · v1-l2-isotonic-stacker
Thái Văn Tài · Scikit-Learn · Family-wise L2 ridge/logistic meta-stacker with isotonic probability calibration
1 user
Gemma 4 31B IT QAT W4A16 · w4a16-ct
Dinesh kumar Thiyagarajan · Transformers · Gemma 4 31B instruction-tuned QAT checkpoint in W4A16 compressed-tensors format for efficient multimodal reasoning and inference.
1 user
mt5-base · default
Mousumiroy250 · Transformers
1 user
Gemma 4 · gemma-4-12b-it-qat-w4a16-ct
Google · Other · Gemma is a family of lightweight, state-of-the-art open models from Google, built from the same research and technology used to create the Gemini models.
1 user
Gemma 4 · gemma-4-31b-it-qat-q4_0-unquantized
Google · Transformers · Gemma is a family of lightweight, state-of-the-art open models from Google, built from the same research and technology used to create the Gemini models.
1 user
Gemma 4 · gemma-4-e4b-it
Google · Transformers · Gemma is a family of lightweight, state-of-the-art open models from Google, built from the same research and technology used to create the Gemini models.
1 user
Gemma 4 · gemma-4-31b-it
Google · Transformers · Gemma is a family of lightweight, state-of-the-art open models from Google, built from the same research and technology used to create the Gemini models.
1 user
bert-base-multilingual-cased · default
Muhammad Ukasha · Transformers
1 user

Update on "Kaggle Error" Submissions & Queue Delays
Hi everyone,

I wanted to give an update on the long submission wait times and "Kaggle Error" failures many of you have been seeing over the past couple of days.

What Happened
Due to very high submission volume using L4s, submission jobs have been facing long queue times before actually getting a machine to run. Submissions that waited >15h to start were being marked as “Kaggle Error” even if they eventually did run.

What We Fixed
Extended Queue Timeout: We've drastically extended the queue allowance so submissions will no longer be timed out while waiting to run.
Scoring Submissions that had Queue Timeouts: We are actively reprocessing all affected submissions from the past few days. If your notebook ran to completion and produced a valid submission file, it is being scored and published to the leaderboard automatically. You should already see scores showing up on the leaderboard now.
Queue Wait Times: to even some of the load on the pool, we are implementing a pending submission limit (separate from the 1/day limit). If your team's previous submission is still scoring, you will need to wait until it finishes to submit again.
What This Means for You
No action needed: You do not need to resubmit or contact support to recover an affected run; our batch recovery is processing them directly.
Monitor this post for updates
Thanks for your patience while we sort this out, and sorry for the frustration it caused over the last few days.


32

11

8

2
57 Comments
1 appreciation comment
Hotness
 
Comment here. Be patient, be friendly, and focus on ideas. We're all here to learn and improve!
This comment will be made public once posted.


Post Comment
Pinned comments
Dustin
Kaggle Staff

Posted 5 days ago

The current queue delay is around ~33h, so yes, your submission isn't stuck, its making its way towards the front of a long line of submissions. If its been >33h, its likely running right now and your score will arrive once it finishes, scores have continued to work its just incredibly high demand and finite supply.

Thanks for your patience everyone, we will continue to do what we can.


Reply

React
tammamalasha
Posted 4 days ago

· 520th in this Competition

My submission has been stuck in processing for more than 24 hours, without completing or providing any results.

Unfortunately, this issue has also prevented me from submitting another notebook the following day, causing me to lose valuable competition time and submission opportunities.


Reply

React
Dustin
Kaggle Staff

Posted 2 days ago

Update: Queue delay has fallen to around ~14h, its slowly going down, but most runs should finish within ~24hish of submit now.


Reply

1

2
Yurnero
Posted 2 days ago

· 1st in this Competition

Thanks! That "pending sub" change was very smart. I think we lost at most 2-3 submissions which is basically nothing


Reply

1
All other comments
Kitxuu
Posted 7 hours ago

· 2517th in this Competition

Hi, I’m still encountering the “Kaggle Error” even though my submission log reports “success.” Could you clarify why this issue persists? Will the affected submission be rerun automatically and receive a score later?


Reply

React
Debasish Mohanta
Posted 4 days ago



Hi Team! My previous submission has been running for more than 24 hours, which is preventing me from making any new submissions. Could you please look into this and help resolve it?


Reply

React
tammamalasha
Posted 4 days ago

· 520th in this Competition

Unfortunately, this issue has also prevented me from submitting another notebook the following day, causing me to lose valuable competition time and submission opportunities.


Reply

React
Yurnero
Posted 6 days ago

· 1st in this Competition

Queue Wait Times: to even some of the load on the pool, we are implementing a pending submission limit (separate from the 1/day limit). If your team's previous submission is still scoring, you will need to wait until it finishes to submit again.

Do we have a guarantee that our submissions will score within 48 hours (ideally we want them to score at most within 24 hours)? Otherwise, we will just lose 1 submission.


Reply

React
Ryan Holbrook
Kaggle Staff

Posted 6 days ago

It's possible, yes, that you will lose a daily submission this way. Unfortunately, even after doubling the number of L4 machines, we were still seeing the queue grow without bounds. Since, at the moment, it doesn't look like we'll be able to increase our supply of GPUs, we have to reduce somehow the submission demand instead.


Reply

React
Yurnero
Posted 6 days ago

· 1st in this Competition

Yea, number of participants is just wild this year…

Can Kaggle possibly make it like 1 submission a day per all competitions with 4xL4 inference? So 1 person wont be able to use 3 instances (2 ARC and 1 for gemma competition) daily, for example


Reply

React

6 more replies
Profile picture for Yurnero
Profile picture for Dustin
Profile picture for g john rao
Ayush Thakur
Posted 6 days ago

· 462nd in this Competition

It would be super useful to cancel a pending run ideally with some. visibility on current status of the said pending run. Say I have a better idea to try but because a pending run is stuck for a day, I just cannot plan the next set of experiments. I would imagine many try to make a submission to be able to come to a result overnight and then plan the next day accordingly. Maybe some more control would be nice. I understand this is a fundamental product feature ask but maybe putting it on a roadmap would be appreciated. thanks.


Reply

React
Bharat
Posted 6 days ago

· 539th in this Competition

My submission is running from more than a day now ~27hrs

submission

Now I want to create another submission for today but can't due to the new limit

limit error

So, if a submission is in queue for long time, we can loose one day's submission right? If it is in the queue for 2 days we will loose 2 submissions? May I know what is the new queue time limit? We also can't even stop the existing submission to submit a new one.


Reply

2
Arul Oly
Posted 4 days ago

Mine also has the same issue.


Reply

React
Prometheus
Posted 5 days ago

· 1357th in this Competition

Is this contest even a legitimate contest if no one can submit without error? I can’t even get a base line submission.


Reply

React
Manny_Jain
Posted 5 days ago

· 1623rd in this Competition

@ryanholbrook Could you please extend the final submission deadline by a few days? We’re losing quite a bit of time, and an extension would give everyone a fair chance to plan and complete their remaining submissions.


Reply

React
Dmitry Sokolevski
Posted 6 days ago

· 1515th in this Competition

My latest submission has been running for over 2 days now




Reply

React
Dmitry Sokolevski
Posted 6 days ago

· 1515th in this Competition

Scoring Submissions that had Queue Timeouts

I also have two submission.zip files that didn't work previously with kaggle error; can they be scored, or is that only for notebooks that generated a valid submission.zip?


Reply

React
Dmitry Sokolevski
Posted 5 days ago

· 1515th in this Competition

Upd: ended with notebook timeout


Reply

React
Harsha
Posted 6 days ago

· 561st in this Competition

Submission 56949985 (team Harsha) has been pending since 2026-10-08 10:50 UTC, over 30 hours. Could you check whether it's still queued or needs re-processing? Thanks.````


Reply

React
Haywood Ublome
Posted 6 days ago

· 555th in this Competition

Hi @herbison — following up on Gholamali's question above, since it affects how we test locally. Did the scorer move to swegemma 0.2.10 / adk_submission 0.2.13, and if so, when? The wheelhouse we pulled gives us swegemma 0.2.7 / adk_submission 0.2.12 (the Sep 30 build), so we are validating against those. Three things it would help to know: Which versions is the scorer running right now, and were they changed between Oct 6 and Oct 8? That window is when the "A system error" reports start. Is there anything in 0.2.10 / 0.2.13 that changes how a submission is compiled or run — tool registration, eval_config fields, compaction defaults, the patch collection step? We would rather find out from the changelog than from a failed submission. Where do we get the current wheels, so local validation matches the scorer? If the published wheelhouse is still on 0.2.7 / 0.2.12, could it be refreshed? Also, on the new pending-submission limit: with queue waits of 20-27 h being reported, a submission that sits in the queue past 00:00 UTC costs us that day's submission as well as the slot it is occupying. Could you confirm whether a submission that is still PENDING when the daily limit resets blocks the next day's attempt, and whether there is any way to cancel a queued submission? Thanks for the update and the reruns — our 56894166 came back and scored.


Reply

1
Dustin
Kaggle Staff

Posted 6 days ago

I think Ryan covers that in this comment: https://www.kaggle.com/competitions/gemma-4-developer-agent/discussion/747519#3535930


Reply

React
Shayani Roy
Posted 4 days ago

Hi team! I’ve run into a technical hiccup. My notebook has been running for over 12+ hrs, and it’s blocking my submission, and since I already have an updated version ready, I can’t submit it until this is resolved. Could you please help? Thanks so much.


Reply

React
MY
Posted 6 days ago

· 610th in this Competition

Rather than just making it random and unpredictable with increased timeouts, just make it one submission every 2 days so we can plan our research/submit cycles.


Reply

React
Ahmed Almarwani
Posted 5 days ago

· 1395th in this Competition

Hi @ryanholbrook -- our submission 56996213 has been PENDING since 2026-10-09 06:35 UTC (~40h now), well past the recent ~33h queue estimate, and it's blocking new uploads under the 1-pending limit. Could you confirm it's still progressing (not stuck), and is there any way to expedite or clear it so we can use today's submission slot? Thanks for the updates so far!


Reply

React
Dustin
Kaggle Staff

Posted 4 days ago

33h is the approximate queue wait time, then you have to wait your submission runtime before it gets scored. Your submission looks like it finished and results were posted now.


Reply

React
Wilmer E. Henao
Posted 6 days ago

· 90th in this Competition

I have a submission that already timed out and shows up as failed…

but it's still running.


Reply

React
Varun Agnihotri
Posted 5 days ago

· 1230th in this Competition

My submission is running from more than 24 hours. 

Due, to which I am not even able to make my new submissions. Please, let us know if any action is required from our side.


Reply

React
John Wegis
Posted 5 days ago

· 28th in this Competition

Hi @ryanholbrook, our submission 56980374 (Oct 9, 00:13 UTC) has been PENDING for ~39 hours and is blocking our next submission under the pending limit. Could you confirm it's still queued (not stuck)? We'd like it scored rather than cancelled. Thanks!


Reply

React
Aker
Posted 6 days ago

· 1565th in this Competition

Hello, is this because Kaggle doesn't have sufficient computing resources? Maybe ask Google for more GPUs.


Reply

5

6
Santi M
Posted 5 days ago

· 336th in this Competition

@ryanholbrook @herbison Our submission (ref 56972471) has been in SubmissionStatus.PENDING for over 36 hours (submitted 2026-10-08 20:50 UTC). It is currently blocking our next daily submission due to the pending limit. Could you please cancel or unstick ref 56972471 so we can submit our updated candidate? Thank you!


Reply

React
Ruoxuan Teng
Posted 3 hours ago

Hi @herbison @ryanholbrook,

I’m experiencing a similar issue: my saved notebook version completes successfully and produces submission.zip, but the competition submission fails almost immediately with “Kaggle Error — A system error.”

Latest submission ID: 57214092 Notebook version ID: 358528068 Notebook: https://www.kaggle.com/code/ruoxuanteng/gemma-4-agent-budget-fit-single-agent/notebook?scriptVersionId=358528068

Thank you for your help!


Reply

React
Prakash2006
Posted 21 hours ago

· 431st in this Competition

HI Team..

My submission has been stuck in processing for more than 24 hours, without completing or providing any results.

Unfortunately, this issue has also prevented me from submitting another notebook the following day, causing me to lose valuable competition time and submission opportunities.


Reply

React
Dustin
Kaggle Staff

Posted 21 hours ago

You submitted ~19h ago, your submission is still running.


Reply

React
Prakash2006
Posted 11 hours ago

· 431st in this Competition

So how to fix as it has already been a day for the submission


Reply

React
Santanu Banerjee
Posted a day ago

· 2051st in this Competition

Hi @ryanholbrook @herbison, team InSociEUP here.

Submission 56765863 (2026-10-02 04:43 UTC) has been PENDING for 12 days. It is an accidental duplicate: a single API call created two submissions 0.3 s apart from the same notebook version, and its twin 56765864 was scored normally (0.08).

Since the pending-submission limit went live, every new submission from our team is rejected with "Your team already has 1 pending submissions" (FAILED_PRECONDITION), so we have not been able to submit since 2026-10-08.

Could you cancel or fail 56765863 so the team can submit again? We don't need it scored. Thank you!


Reply

React
Dustin
Kaggle Staff

Posted 15 hours ago

@santanubanerjee9 You had a different kind of "stuck" submission, I think I errored it out for you, can you check?


Reply

React
Bogdan Sburlea
Posted 2 days ago

The submission was marked “Kaggle Error” within minutes, although the packaging notebook completed successfully in 23 seconds and generated a valid submission.zip output.

Does the automatic recovery process also cover immediate “Kaggle Error” failures, or only submissions that exceeded the previous queue timeout?

We would like to avoid consuming another daily submission if the original can be recovered.


Reply

React
Dustin
Kaggle Staff

Posted 2 days ago

The recovery process was a one-time thing, not continuous and was specific to one kind of issues (queue wait timeout). In your case, it seems like you hit submit before your submission.zip was ready, you can try resubmitting it. Kaggle Error should not consume a submission anyway.


Reply

React
HZM
Posted 3 days ago

· 807th in this Competition

May I ask when can we back to normal track? It is very frustrating to keep waiting. Or is there any plan that extend the deadline for the competition?


Reply

React
Jesús Antonio Campo Román
Posted 4 days ago

· 2762nd in this Competition

Good morning, everyone.

If I may, if there are queue congestion issues with the tasks, it would be recommended to enable a specific state such as 'Notebook Enqueued.'

This would allow us to know the real execution time and detect potential infinite loops, since right now it's not possible to know the actual state of the task.

I understand that the queue problem still exists today because my last submission has been in 'Notebook Running' for 16 hours.

Regards…


Reply

React
Chengyi Zhang
Posted 4 days ago

· 452nd in this Competition

Hi Dustin and team, thanks for explaining the queue issue. Could you check whether this announcement also covers our terminal "Notebook Threw Exception" result for submission 56949174?

Our original submission 56913088 ended with "A system error". We made one retry, 56949174, using the same ZIP and notebook version 356090888. The retry's API status is COMPLETE, but error_description is non-empty and public_score is empty.

Checks completed:

The ZIP downloaded from the submission matches our frozen candidate's SHA-256.
The CPU packaging notebook completed successfully in 19 seconds.
Adapter discovery and compilation pass with the official adk_submission 0.2.12 and 0.2.13.
Notebook version: https://www.kaggle.com/code/sinfonia8838/submit-t008-lora-pilot-v001?scriptVersionId=356090888

We do not have the hidden-run traceback. Could staff confirm the failure stage (packaging/compilation, vLLM startup or adapter warmup, or harness execution), the first fatal exception with sensitive details removed, and the wheelhouse/runtime build used for this run? It would also help to know whether the queue/outage announcements specifically apply to 56949174.

We are not requesting hidden task details. We would like enough diagnostic information to choose the next check and avoid another blind resubmission. Thank you.


Reply

React
Mark Hall
Posted 5 days ago

· 790th in this Competition

Thanks for the update!! And here I was questioning my own competence, guess I can worry a little less about things 😆


Reply

React
Szymon Charkiewicz
Posted 5 days ago

· 1290th in this Competition

Hi @ryanholbrook and Kaggle team, one status question plus three budgeting clarifications (following up on 743063 and 745805):

0) Our submission 56960200 (the unchanged official baseline) has been PENDING since Oct 8, 15:24 UTC, i.e. over 34 hours, with no score. A new upload is rejected with "Your team already has 1 pending submissions; please wait for one to finish scoring." Is this normal queue time, or could the submission be stuck? If stuck, should we wait, or cancel and resubmit?

1) 12-hour fix status: is the planned change (unfinished tasks at the 12h limit scored as 0, instead of the whole submission erroring) live now? The last visible answer we found (743964) was "Not yet."

2) Run scope: does one scoring run generate patches for the full hidden test set (~120 tasks, public + private), or only for the public split (~58), with the private split evaluated in a separate rerun after the deadline?

3) Queue vs limit: does time spent waiting in the queue count toward the 12-hour limit?

Thank you!


Reply

React
Ryan Holbrook
Kaggle Staff

Posted 5 days ago

0) It's queued.

1) I may not implement this. Because of how the scoring system works, it's challenging to arrange it so that public and private tasks are treated consistently.

2) The full hidden test set.

3) No.


Reply

1
Silent Jackal
Posted 5 days ago

· 1350th in this Competition

Submission 56972125 (submitted 2026-10-08 20:40 UTC) has shown "Notebook Running" for over a day. It's an old baseline I no longer need, and because of the pending-submission limit it is blocking a new submission. Could it be cancelled? Thanks!


Reply

React
xihaad
Posted 6 days ago

· 2376th in this Competition

It is running from more than a day! What happened actually!


Reply

React
Arun P Idiculla
Posted 6 days ago

· 1979th in this Competition

Hello Kaggle Team & Admins, My recent submission has been stalled in the SubmissionStatus.PENDING queue for more than 26 hours. Here are the details from my submission history:

Competition: Google – The Gemma 4 Developer Agent Competition
Previous Submission (Ref 56971261): Failed with SubmissionStatus.ERROR ("A system error. Please try resubmitting…")
Retry Submission (Ref 56971313): Submitted on 2026-10-08 20:18:49 UTC
Current Status: Still SubmissionStatus.PENDING (no score or failure message returned) Could the competition team please check if the evaluation worker for ref 56971313 is stuck, or terminate/reset the evaluation so it does not block the queue? Thank you!

Reply

React
Rahul kr
Posted 6 days ago

· 2820th in this Competition

my submission has been running for more than 24 hours now what does this mean


Reply

React
Dustin
Kaggle Staff

Posted 6 days ago

It means its likely either still waiting to run (queued), or has started running but hasn't finished yet. The queue is very long right now, like >1 day long.


Reply

React
Pavel Orlov
Posted 6 days ago

If I submit it today, will I have time to check it before the competition ends?


Reply

7
果汁
Posted 6 days ago

· 980th in this Competition

Hi Kaggle team, my second submission of Getting Started - Gemma 4 Developer Agent, Version 1 has shown “Notebook Running” since about one day ago. My first submission of the same version succeeded with a public score of 0.01. I cannot submit my new Gemma 4 SuperAgent, Version 1 because the page says “Your team already has 1 pending submissions.” Could you check whether the pending run is still active and unblock it if it is stuck? My Kaggle username is guoxuezhen. I can provide screenshots or the submission ID if needed.


Reply

React
Ulises Rayon
Posted 6 days ago

· 1676th in this Competition

Thanks for the transparency and the clarity. I think we all understand the gpus situation.


Reply

React
Gholamali Aminian
Posted 7 days ago

· 198th in this Competition

Hi @herbison , did you update packages (swegemma 0.2.10, adk_submission 0.2.13) today?


Reply

React
Ryan Holbrook
Kaggle Staff

Posted 6 days ago

I updated the packages in the wheelhouse yesterday to address some of the edge cases around tool calling and thought budgets. I'll make a more detailed post soon.


Reply

1
RISHAV KUMAR
Posted 5 days ago

· 886th in this Competition

when u say wheelhouse u mean the wheels folder in the data right? @ryanholbrook


Reply

React

Competition Rules
ENTRY IN THIS COMPETITION CONSTITUTES YOUR ACCEPTANCE OF THESE OFFICIAL COMPETITION RULES.
See Section 3.18 for defined terms

The Competition named below is a skills-based competition to promote and further the field of data science. You must register via the Competition Website to enter. To enter the Competition, you must agree to these Official Competition Rules, which incorporate by reference the provisions and content of the Competition Website and any Specific Competition Rules herein (collectively, the "Rules"). Please read these Rules carefully before entry to ensure you understand and agree. You further agree that Submission in the Competition constitutes agreement to these Rules. You may not submit to the Competition and are not eligible to receive the prizes associated with this Competition unless you agree to these Rules. These Rules form a binding legal agreement between you and the Competition Sponsor with respect to the Competition. Your competition Submissions must conform to the requirements stated on the Competition Website. Your Submissions will be scored based on the evaluation metric described on the Competition Website. Subject to compliance with the Competition Rules, Prizes, if any, will be awarded to Participants with the best scores, based on the merits of the data science models submitted. See below for the complete Competition Rules.

You cannot sign up to Kaggle from multiple accounts and therefore you cannot enter or submit from multiple accounts.

1. COMPETITION-SPECIFIC TERMS
1. COMPETITION TITLE
Google - The Gemma 4 Developer Agent Competition

2. COMPETITION SPONSOR
Google LLC

3. COMPETITION SPONSOR ADDRESS
1600 Amphitheatre Parkway, Mountain View, California 94043 USA

4. COMPETITION WEBSITE
https://www.kaggle.com/competitions/gemma-4-developer-agent

5. TOTAL PRIZES AVAILABLE: $65,000
First Prize: $37,000
Second Prize: $18,000
Third Prize: $10,000
6. WINNER LICENSE TYPE
Open Source Apache 2.0

7. DATA ACCESS AND USE
Competition Use and Commercial Apache 2.0

2. COMPETITION-SPECIFIC RULES
In addition to the provisions of the General Competition Rules below, you understand and agree to these Competition-Specific Rules required by the Competition Sponsor:

1. TEAM LIMITS
a. The maximum Team size is five (5). b. Team mergers are allowed and can be performed by the Team leader. In order to merge, the combined Team must have a total Submission count less than or equal to the maximum allowed as of the Team Merger Deadline. The maximum allowed is the number of Submissions per day multiplied by the number of days the competition has been running.

2. SUBMISSION LIMITS
a. You may submit a maximum of one (1) Submission per day. b. You may select up to two (2) Final Submissions for judging.

3. COMPETITION TIMELINE
a. Competition Timeline dates (including Entry Deadline, Final Submission Deadline, Start Date, and Team Merger Deadline, as applicable) are reflected on the competition’s Overview > Timeline page.

4. COMPETITION DATA
a. Data Access and Use.

You may access and use the Competition Data for any purpose, whether commercial or non-commercial, including for participating in the Competition and on Kaggle.com forums, and for academic research and education. The Competition Sponsor reserves the right to disqualify any Participant who uses the Competition Data other than as permitted by the Competition Website and these Rules.
b. Data Security.

You agree to use reasonable and suitable measures to prevent persons who have not formally agreed to these Rules from gaining access to the Competition Data. You agree not to transmit, duplicate, publish, redistribute or otherwise provide or make available the Competition Data to any party not participating in the Competition. You agree to notify Kaggle immediately upon learning of any possible unauthorized transmission of or unauthorized access to the Competition Data and agree to work with Kaggle to rectify any unauthorized transmission or access.
5. WINNER LICENSE
a. Under Section 2.8 (Winners Obligations) of the General Rules below, you hereby grant and will grant the Competition Sponsor the following license(s) with respect to your Submission if you are a Competition winner:

You hereby license and will license your winning Submission and the source code used to generate the Submission under an Open Source Initiative-approved license (see www.opensource.org) that in no event limits commercial use of such code or model containing or depending on such code.

For generally commercially available software that you used to generate your Submission that is not owned by you, but that can be procured by the Competition Sponsor without undue expense, you do not need to grant the license in the preceding Section for that software.

In the event that input data or pretrained models with an incompatible license are used to generate your winning solution, you do not need to grant an open source license in the preceding Section for that data and/or model(s).

b. You may be required by the Sponsor to provide a detailed description of how the winning Submission was generated, to the Competition Sponsor’s specifications, as outlined in Section 2.8, Winner’s Obligations. This may include a detailed description of methodology, where one must be able to reproduce the approach by reading the description, and includes a detailed explanation of the architecture, preprocessing, loss function, training details, hyper-parameters, etc. The description should also include a link to a code repository with complete and detailed instructions so that the results obtained can be reproduced.

6. EXTERNAL DATA AND TOOLS
a. You may use data other than the Competition Data (“External Data”) to develop and test your Submissions. However, you will ensure the External Data is either publicly available and equally accessible to use by all Participants of the Competition for purposes of the competition at no cost to the other Participants, or satisfies the Reasonableness criteria as outlined in Section 2.6.b below. The ability to use External Data under this Section does not limit your other obligations under these Competition Rules, including but not limited to Section 2.8 (Winners Obligations).

b. The use of external data and models is acceptable unless specifically prohibited by the Host. Because of the potential costs or restrictions (e.g., “geo restrictions”) associated with obtaining rights to use external data or certain software and associated tools, their use must be “reasonably accessible to all” and of “minimal cost”. Also, regardless of the cost challenges as they might affect all Participants during the course of the competition, the costs of potentially procuring a license for software used to generate a Submission, must also be considered. The Host will employ an assessment of whether or not the following criteria can exclude the use of the particular LLM, data set(s), or tool(s):

Are Participants being excluded from a competition because of the "excessive" costs for access to certain LLMs, external data, or tools that might be used by other Participants. The Host will assess the excessive cost concern by applying a “Reasonableness” standard (the “Reasonableness Standard”). The Reasonableness Standard will be determined and applied by the Host in light of things like cost thresholds and accessibility.

By way of example only, a small subscription charge to use additional elements of a large language model such as Gemini Advanced are acceptable if meeting the Reasonableness Standard of Sec. 8.2. Purchasing a license to use a proprietary dataset that exceeds the cost of a prize in the competition would not be considered reasonable.

c. Automated Machine Learning Tools (“AMLT”)

Individual Participants and Teams may use automated machine learning tool(s) (“AMLT”) (e.g., Google toML, H2O Driverless AI, etc.) to create a Submission, provided that the Participant or Team ensures that they have an appropriate license to the AMLT such that they are able to comply with the Competition Rules.
7. ELIGIBILITY
a. Unless otherwise stated in the Competition-Specific Rules above or prohibited by internal policies of the Competition Entities, employees, interns, contractors, officers and directors of Competition Entities may enter and participate in the Competition, but are not eligible to win any Prizes. "Competition Entities" means the Competition Sponsor, Kaggle Inc., and their respective parent companies, subsidiaries and affiliates. If you are such a Participant from a Competition Entity, you are subject to all applicable internal policies of your employer with respect to your participation.

8. WINNER’S OBLIGATIONS
a. As a condition to being awarded a Prize, a Prize winner must fulfill the following obligations:

Deliver to the Competition Sponsor the final model's software code as used to generate the winning Submission and associated documentation. The delivered software code should follow these documentation guidelines, must be capable of generating the winning Submission, and contain a description of resources required to build and/or run the executable code successfully. For avoidance of doubt, delivered software code should include training code, inference code, and a description of the required computational environment.
a. To the extent that the final model’s software code includes generally commercially available software that is not owned by you, but that can be procured by the Competition Sponsor without undue expense, then instead of delivering the code for that software to the Competition Sponsor, you must identify that software, method for procuring it, and any parameters or other information necessary to replicate the winning Submission; Individual Participants and Teams who create a Submission using an AMLT may win a Prize. However, for clarity, the potential winner’s Submission must still meet the requirements of these Rules, including but not limited to Section 2.5 (Winners License), Section 2.8 (Winners Obligations), and Section 3.14 (Warranty, Indemnity, and Release).”

b. Individual Participants and Teams who create a Submission using an AMLT may win a Prize. However, for clarity, the potential winner’s Submission must still meet the requirements of these Rules,

Grant to the Competition Sponsor the license to the winning Submission stated in the Competition Specific Rules above, and represent that you have the unrestricted right to grant that license;

Sign and return all Prize acceptance documents as may be required by Competition Sponsor or Kaggle, including without limitation: (a) eligibility certifications; (b) licenses, releases and other agreements required under the Rules; and (c) U.S. tax forms (such as IRS Form W-9 if U.S. resident, IRS Form W-8BEN if foreign resident, or future equivalents).

9. GOVERNING LAW
a. Unless otherwise provided in the Competition Specific Rules above, all claims arising out of or relating to these Rules will be governed by California law, excluding its conflict of laws rules, and will be litigated exclusively in the Federal or State courts of Santa Clara County, California, USA. The parties consent to personal jurisdiction in those courts. If any provision of these Rules is held to be invalid or unenforceable, all remaining provisions of the Rules will remain in full force and effect.

Kaggle Competition Foundational Rules
(Non-editable)

Competition participants must also agree to Kaggle's Foundational Competition Rules. These rules will supersede the competition-specific rules in the event of any conflict.
The following Kaggle Competition Foundational Rules (“ Foundational Rules ”) apply to every competition regardless of whether the Sponsor creates competition-specific rules. Any competition-specific rules provided by the Sponsor are in addition to these rules, and in the case of any conflict or inconsistency, these Foundational Rules control and nullify contrary competition-specific rules.

GENERAL COMPETITION RULES - BINDING AGREEMENT
1. ELIGIBILITY
a. To be eligible to enter the Competition, you must be:

a registered account holder at Kaggle.com;
the older of 18 years old or the age of majority in your jurisdiction of residence (unless otherwise agreed to by Competition Sponsor and appropriate parental/guardian consents have been obtained by Competition Sponsor);
not a resident of Crimea, so-called Donetsk People's Republic (DNR) or Luhansk People's Republic (LNR), Cuba, Iran, or North Korea; and
not a person or representative of an entity under U.S. export controls or sanctions (see: https://www.treasury.gov/resourcecenter/sanctions/Programs/Pages/Programs.aspx).
b. Competitions are open to residents of the United States and worldwide, except that if you are a resident of Crimea, so-called Donetsk People's Republic (DNR) or Luhansk People's Republic (LNR), Cuba, Iran, North Korea, or are subject to U.S. export controls or sanctions, you may not enter the Competition. Other local rules and regulations may apply to you, so please check your local laws to ensure that you are eligible to participate in skills-based competitions. The Competition Host reserves the right to forego or award alternative Prizes where needed to comply with local laws. If a winner is located in a country where prizes cannot be awarded, then they are not eligible to receive a prize.

c. If you are entering as a representative of a company, educational institution or other legal entity, or on behalf of your employer, these rules are binding on you, individually, and the entity you represent or where you are an employee. If you are acting within the scope of your employment, or as an agent of another party, you warrant that such party or your employer has full knowledge of your actions and has consented thereto, including your potential receipt of a Prize. You further warrant that your actions do not violate your employer's or entity's policies and procedures.

d. The Competition Sponsor reserves the right to verify eligibility and to adjudicate on any dispute at any time. If you provide any false information relating to the Competition concerning your identity, residency, mailing address, telephone number, email address, ownership of right, or information required for entering the Competition, you may be immediately disqualified from the Competition.

2. SPONSOR AND HOSTING PLATFORM
a. The Competition is sponsored by Competition Sponsor named above. The Competition is hosted on behalf of Competition Sponsor by Kaggle Inc. ("Kaggle"). Kaggle is an independent contractor of Competition Sponsor, and is not a party to this or any agreement between you and Competition Sponsor. You understand that Kaggle has no responsibility with respect to selecting the potential Competition winner(s) or awarding any Prizes. Kaggle will perform certain administrative functions relating to hosting the Competition, and you agree to abide by the provisions relating to Kaggle under these Rules. As a Kaggle.com account holder and user of the Kaggle competition platform, remember you have accepted and are subject to the Kaggle Terms of Service at www.kaggle.com/terms in addition to these Rules.

3. COMPETITION PERIOD
a. For the purposes of Prizes, the Competition will run from the Start Date and time to the Final Submission Deadline (such duration the “Competition Period”). The Competition Timeline is subject to change, and Competition Sponsor may introduce additional hurdle deadlines during the Competition Period. Any updated or additional deadlines will be publicized on the Competition Website. It is your responsibility to check the Competition Website regularly to stay informed of any deadline changes. YOU ARE RESPONSIBLE FOR DETERMINING THE CORRESPONDING TIME ZONE IN YOUR LOCATION.

4. COMPETITION ENTRY
a. NO PURCHASE NECESSARY TO ENTER OR WIN. To enter the Competition, you must register on the Competition Website prior to the Entry Deadline, and follow the instructions for developing and entering your Submission through the Competition Website. Your Submissions must be made in the manner and format, and in compliance with all other requirements, stated on the Competition Website (the "Requirements"). Submissions must be received before any Submission deadlines stated on the Competition Website. Submissions not received by the stated deadlines will not be eligible to receive a Prize. b. Submissions may not use or incorporate information from hand labeling or human prediction of the validation dataset or test data records. c. If the Competition is a multi-stage competition with temporally separate training and/or test data, one or more valid Submissions may be required during each Competition stage in the manner described on the Competition Website in order for the Submissions to be Prize eligible. d. Submissions are void if they are in whole or part illegible, incomplete, damaged, altered, counterfeit, obtained through fraud, or late. Competition Sponsor reserves the right to disqualify any entrant who does not follow these Rules, including making a Submission that does not meet the Requirements.

5. INDIVIDUALS AND TEAMS
a. Individual Account. You may make Submissions only under one, unique Kaggle.com account. You will be disqualified if you make Submissions through more than one Kaggle account, or attempt to falsify an account to act as your proxy. You may submit up to the maximum number of Submissions per day as specified on the Competition Website. b. Teams. If permitted under the Competition Website guidelines, multiple individuals may collaborate as a Team; however, you may join or form only one Team. Each Team member must be a single individual with a separate Kaggle account. You must register individually for the Competition before joining a Team. You must confirm your Team membership to make it official by responding to the Team notification message sent to your Kaggle account. Team membership may not exceed the Maximum Team Size stated on the Competition Website. c. Team Merger. Teams may request to merge via the Competition Website. Team mergers may be allowed provided that: (i) the combined Team does not exceed the Maximum Team Size; (ii) the number of Submissions made by the merging Teams does not exceed the number of Submissions permissible for one Team at the date of the merger request; (iii) the merger is completed before the earlier of: any merger deadline or the Competition deadline; and (iv) the proposed combined Team otherwise meets all the requirements of these Rules. d. Private Sharing. No private sharing outside of Teams. Privately sharing code or data outside of Teams is not permitted. It's okay to share code if made available to all Participants on the forums.

6. SUBMISSION CODE REQUIREMENTS
a. Private Code Sharing. Unless otherwise specifically permitted under the Competition Website or Competition Specific Rules above, during the Competition Period, you are not allowed to privately share source or executable code developed in connection with or based upon the Competition Data or other source or executable code relevant to the Competition (“Competition Code”). This prohibition includes sharing Competition Code between separate Teams, unless a Team merger occurs. Any such sharing of Competition Code is a breach of these Competition Rules and may result in disqualification. b. Public Code Sharing. You are permitted to publicly share Competition Code, provided that such public sharing does not violate the intellectual property rights of any third party. If you do choose to share Competition Code or other such code, you are required to share it on Kaggle.com on the discussion forum or notebooks associated specifically with the Competition for the benefit of all competitors. By so sharing, you are deemed to have licensed the shared code under an Open Source Initiative-approved license (see www.opensource.org) that in no event limits commercial use of such Competition Code or model containing or depending on such Competition Code. c. Use of Open Source. Unless otherwise stated in the Specific Competition Rules above, if open source code is used in the model to generate the Submission, then you must only use open source code licensed under an Open Source Initiative-approved license (see www.opensource.org) that in no event limits commercial use of such code or model containing or depending on such code.

7. DETERMINING WINNERS
a. Each Submission will be scored and ranked by the evaluation metric stated on the Competition Website. During the Competition Period, the current ranking will be visible on the Competition Website's Public Leaderboard. The potential winner(s) are determined solely by the leaderboard ranking on the Private Leaderboard, subject to compliance with these Rules. The Public Leaderboard will be based on the public test set and the Private Leaderboard will be based on the private test set. b. In the event of a tie, the Submission that was entered first to the Competition will be the winner. In the event a potential winner is disqualified for any reason, the Submission that received the next highest score rank will be chosen as the potential winner.

8. NOTIFICATION OF WINNERS & DISQUALIFICATION
a. The potential winner(s) will be notified by email. b. If a potential winner (i) does not respond to the notification attempt within one (1) week from the first notification attempt or (ii) notifies Kaggle within one week after the Final Submission Deadline that the potential winner does not want to be nominated as a winner or does not want to receive a Prize, then, in each case (i) and (ii) such potential winner will not receive any Prize, and an alternate potential winner will be selected from among all eligible entries received based on the Competition’s judging criteria. c. In case (i) and (ii) above Kaggle may disqualify the Participant. However, in case (ii) above, if requested by Kaggle, such potential winner may provide code and documentation to verify the Participant’s compliance with these Rules. If the potential winner provides code and documentation to the satisfaction of Kaggle, the Participant will not be disqualified pursuant to this paragraph. d. Competition Sponsor reserves the right to disqualify any Participant from the Competition if the Competition Sponsor reasonably believes that the Participant has attempted to undermine the legitimate operation of the Competition by cheating, deception, or other unfair playing practices or abuses, threatens or harasses any other Participants, Competition Sponsor or Kaggle. e. A disqualified Participant may be removed from the Competition leaderboard, at Kaggle's sole discretion. If a Participant is removed from the Competition Leaderboard, additional winning features associated with the Kaggle competition platform, for example Kaggle points or medals, may also not be awarded. f. The final leaderboard list will be publicly displayed at Kaggle.com. Determinations of Competition Sponsor are final and binding.

9. PRIZES
a. Prize(s) are as described on the Competition Website and are only available for winning during the time period described on the Competition Website. The odds of winning any Prize depends on the number of eligible Submissions received during the Competition Period and the skill of the Participants. b. All Prizes are subject to Competition Sponsor's review and verification of the Participant’s eligibility and compliance with these Rules, and the compliance of the winning Submissions with the Submissions Requirements. In the event that the Submission demonstrates non-compliance with these Competition Rules, Competition Sponsor may at its discretion take either of the following actions: (i) disqualify the Submission(s); or (ii) require the potential winner to remediate within one week after notice all issues identified in the Submission(s) (including, without limitation, the resolution of license conflicts, the fulfillment of all obligations required by software licenses, and the removal of any software that violates the software restrictions). c. A potential winner may decline to be nominated as a Competition winner in accordance with Section 3.8. d. Potential winners must return all required Prize acceptance documents within two (2) weeks following notification of such required documents, or such potential winner will be deemed to have forfeited the prize and another potential winner will be selected. Prize(s) will be awarded within approximately thirty (30) days after receipt by Competition Sponsor or Kaggle of the required Prize acceptance documents. Transfer or assignment of a Prize is not allowed. e. You are not eligible to receive any Prize if you do not meet the Eligibility requirements in Section 2.7 and Section 3.1 above. f. If a Team wins a monetary Prize, the Prize money will be allocated in even shares between the eligible Team members, unless the Team unanimously opts for a different Prize split and notifies Kaggle before Prizes are issued.

10. TAXES
a. ALL TAXES IMPOSED ON PRIZES ARE THE SOLE RESPONSIBILITY OF THE WINNERS. Payments to potential winners are subject to the express requirement that they submit all documentation requested by Competition Sponsor or Kaggle for compliance with applicable state, federal, local and foreign (including provincial) tax reporting and withholding requirements. Prizes will be net of any taxes that Competition Sponsor is required by law to withhold. If a potential winner fails to provide any required documentation or comply with applicable laws, the Prize may be forfeited and Competition Sponsor may select an alternative potential winner. Any winners who are U.S. residents will receive an IRS Form-1099 in the amount of their Prize.

11. GENERAL CONDITIONS
a. All federal, state, provincial and local laws and regulations apply.

12. PUBLICITY
a. You agree that Competition Sponsor, Kaggle and its affiliates may use your name and likeness for advertising and promotional purposes without additional compensation, unless prohibited by law.

13. PRIVACY
a. You acknowledge and agree that Competition Sponsor and Kaggle may collect, store, share and otherwise use personally identifiable information provided by you during the Kaggle account registration process and the Competition, including but not limited to, name, mailing address, phone number, and email address (“Personal Information”). Kaggle acts as an independent controller with regard to its collection, storage, sharing, and other use of this Personal Information, and will use this Personal Information in accordance with its Privacy Policy <www.kaggle.com/privacy>, including for administering the Competition. As a Kaggle.com account holder, you have the right to request access to, review, rectification, portability or deletion of any personal data held by Kaggle about you by logging into your account and/or contacting Kaggle Support at <www.kaggle.com/contact>. b. As part of Competition Sponsor performing this contract between you and the Competition Sponsor, Kaggle will transfer your Personal Information to Competition Sponsor, which acts as an independent controller with regard to this Personal Information. As a controller of such Personal Information, Competition Sponsor agrees to comply with all U.S. and foreign data protection obligations with regard to your Personal Information. Kaggle will transfer your Personal Information to Competition Sponsor in the country specified in the Competition Sponsor Address listed above, which may be a country outside the country of your residence. Such country may not have privacy laws and regulations similar to those of the country of your residence.

14. WARRANTY, INDEMNITY AND RELEASE
a. You warrant that your Submission is your own original work and, as such, you are the sole and exclusive owner and rights holder of the Submission, and you have the right to make the Submission and grant all required licenses. You agree not to make any Submission that: (i) infringes any third party proprietary rights, intellectual property rights, industrial property rights, personal or moral rights or any other rights, including without limitation, copyright, trademark, patent, trade secret, privacy, publicity or confidentiality obligations, or defames any person; or (ii) otherwise violates any applicable U.S. or foreign state or federal law. b. To the maximum extent permitted by law, you indemnify and agree to keep indemnified Competition Entities at all times from and against any liability, claims, demands, losses, damages, costs and expenses resulting from any of your acts, defaults or omissions and/or a breach of any warranty set forth herein. To the maximum extent permitted by law, you agree to defend, indemnify and hold harmless the Competition Entities from and against any and all claims, actions, suits or proceedings, as well as any and all losses, liabilities, damages, costs and expenses (including reasonable attorneys fees) arising out of or accruing from: (a) your Submission or other material uploaded or otherwise provided by you that infringes any third party proprietary rights, intellectual property rights, industrial property rights, personal or moral rights or any other rights, including without limitation, copyright, trademark, patent, trade secret, privacy, publicity or confidentiality obligations, or defames any person; (b) any misrepresentation made by you in connection with the Competition; (c) any non-compliance by you with these Rules or any applicable U.S. or foreign state or federal law; (d) claims brought by persons or entities other than the parties to these Rules arising from or related to your involvement with the Competition; and (e) your acceptance, possession, misuse or use of any Prize, or your participation in the Competition and any Competition-related activity. c. You hereby release Competition Entities from any liability associated with: (a) any malfunction or other problem with the Competition Website; (b) any error in the collection, processing, or retention of any Submission; or (c) any typographical or other error in the printing, offering or announcement of any Prize or winners.

15. INTERNET
a. Competition Entities are not responsible for any malfunction of the Competition Website or any late, lost, damaged, misdirected, incomplete, illegible, undeliverable, or destroyed Submissions or entry materials due to system errors, failed, incomplete or garbled computer or other telecommunication transmission malfunctions, hardware or software failures of any kind, lost or unavailable network connections, typographical or system/human errors and failures, technical malfunction(s) of any telephone network or lines, cable connections, satellite transmissions, servers or providers, or computer equipment, traffic congestion on the Internet or at the Competition Website, or any combination thereof, which may limit a Participant’s ability to participate.

16. RIGHT TO CANCEL, MODIFY OR DISQUALIFY
a. If for any reason the Competition is not capable of running as planned, including infection by computer virus, bugs, tampering, unauthorized intervention, fraud, technical failures, or any other causes which corrupt or affect the administration, security, fairness, integrity, or proper conduct of the Competition, Competition Sponsor reserves the right to cancel, terminate, modify or suspend the Competition. Competition Sponsor further reserves the right to disqualify any Participant who tampers with the submission process or any other part of the Competition or Competition Website. Any attempt by a Participant to deliberately damage any website, including the Competition Website, or undermine the legitimate operation of the Competition is a violation of criminal and civil laws. Should such an attempt be made, Competition Sponsor and Kaggle each reserves the right to seek damages from any such Participant to the fullest extent of the applicable law.

17. NOT AN OFFER OR CONTRACT OF EMPLOYMENT
a. Under no circumstances will the entry of a Submission, the awarding of a Prize, or anything in these Rules be construed as an offer or contract of employment with Competition Sponsor or any of the Competition Entities. You acknowledge that you have submitted your Submission voluntarily and not in confidence or in trust. You acknowledge that no confidential, fiduciary, agency, employment or other similar relationship is created between you and Competition Sponsor or any of the Competition Entities by your acceptance of these Rules or your entry of your Submission.

18. DEFINITIONS
a. "Competition Data" are the data or datasets available from the Competition Website for the purpose of use in the Competition, including any prototype or executable code provided on the Competition Website. The Competition Data will contain private and public test sets. Which data belongs to which set will not be made available to Participants. b. An “Entry” is when a Participant has joined, signed up, or accepted the rules of a competition. Entry is required to make a Submission to a competition. c. A “Final Submission” is the Submission selected by the user, or automatically selected by Kaggle in the event not selected by the user, that is/are used for final placement on the competition leaderboard. d. A “Participant” or “Participant User” is an individual who participates in a competition by entering the competition and making a Submission. e. The “Private Leaderboard” is a ranked display of Participants’ Submission scores against the private test set. The Private Leaderboard determines the final standing in the competition. f. The “Public Leaderboard” is a ranked display of Participants’ Submission scores against a representative sample of the test data. This leaderboard is visible throughout the competition. g. A “Sponsor” is responsible for hosting the competition, which includes but is not limited to providing the data for the competition, determining winners, and enforcing competition rules. h. A “Submission” is anything provided by the Participant to the Sponsor to be evaluated for competition purposes and determine leaderboard position. A Submission may be made as a model, notebook, prediction file, or other format as determined by the Sponsor. i. A “Team” is one or more Participants participating together in a Kaggle competition, by officially merging together as a Team within the competition platform.