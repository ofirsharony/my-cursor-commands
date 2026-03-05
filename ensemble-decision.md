# Ensemble Decision

Consult three frontier AI models on a problem, find consensus, and make a decision.

## Models

| Provider  | Model              | CLI Command                      |
|-----------|--------------------|----------------------------------|
| Anthropic | Claude Opus 4.6    | `claude --print --model opus`    |
| Google    | Gemini 3.1 Pro     | `gemini -m gemini-3.1-pro-preview` |
| OpenAI    | GPT-5.3-Codex      | `codex -q`                       |

## Process

### Step 1: Frame the Problem

Review the conversation context and identify:
- What problem or decision is the user facing?
- What relevant codebase context exists?
- What constraints apply?

Write a **single, unbiased problem statement** (3-5 sentences) that will be sent identically to all models. Do NOT inject your own opinion into this statement.

If relevant files exist in the workspace, read them and include key context directly in the prompt — external models cannot access the workspace.

### Step 2: Build the Shared Prompt

Construct ONE prompt to send to all three models:

```
Problem: [factual description]
Context: [relevant codebase/project details, inline code snippets if needed]
Constraints: [limitations, requirements, non-negotiables]

Analyze this problem and provide:
1. Your assessment — what's the core issue?
2. Your recommended approach and why
3. Risks, failure modes, and trade-offs of your recommendation
4. Alternative approaches you considered and why you rejected them
5. What could go wrong that nobody is thinking about?

Be direct. Be critical. No hedging.
```

### Step 3: Run All Three Models in PARALLEL

These models are called via their installed CLIs, not through Cursor subagents. Cursor subagents all use the same underlying model — the CLIs give us genuinely different model families.

Launch THREE Task tool calls in a SINGLE message (subagent_type: `shell` for all):

1. **Claude Opus 4.6** (Anthropic)
   ```bash
   claude --print --model opus <<'PROMPT'
   <the shared prompt>
   PROMPT
   ```

2. **Gemini 3.1 Pro** (Google)
   ```bash
   gemini -m gemini-3.1-pro-preview <<'PROMPT'
   <the shared prompt>
   PROMPT
   ```

3. **GPT-5.3-Codex** (OpenAI)
   ```bash
   codex -q <<'PROMPT'
   <the shared prompt>
   PROMPT
   ```

IMPORTANT:
- All three MUST launch in a single message (parallel execution).
- Use heredoc (`<<'PROMPT' ... PROMPT`) to pass the prompt — avoid shell escaping issues with quotes.
- If a model fails (auth, timeout, crash), note it and continue with the others.
- Return the full model response from each subagent.

### Step 4: Synthesize and Decide

Once all responses are back, produce this output:

---

## Ensemble Decision

### Problem
[The problem statement you sent to all models]

### Panel

**Claude Opus 4.6:**
[2-4 bullet summary of key points and recommendation]

**Gemini 3.1 Pro:**
[2-4 bullet summary of key points and recommendation]

**GPT-5.3-Codex:**
[2-4 bullet summary of key points and recommendation]

*(If a model failed, note it here with the error.)*

### Consensus
What do 2+ models agree on? This is the strongest signal.

### Disagreements
Where did they diverge? Analyze *why* — different assumptions, different priorities, or genuine uncertainty?

### Blind Spots
What did none of them address that you think matters?

### Decision
State the final recommendation clearly. Justify it by referencing which models agreed and why the dissenting view (if any) is less compelling in this context.

### Next Steps
- Concrete, actionable items to execute the decision

---

## Rules

- **Same prompt for all models** — consistency is the whole point
- **No pre-biasing** — don't tell external models what you think before they answer
- **Parallel execution** — never run sequentially
- **Fail gracefully** — if only 2 of 3 models respond, proceed with the synthesis. Note which model failed and why. A 2-model ensemble is still valuable.
- **Decide, don't summarize** — the output must end with a clear decision, not "it depends"
