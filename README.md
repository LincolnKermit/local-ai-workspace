Good news: you can run **Claude Code itself** against your local model. Ollama v0.14.0+ is compatible with the Anthropic Messages API, so Claude Code can talk to any Ollama model — no proxy, no LiteLLM. Same tool, same agent loop, local brain.

**Setup**

```powershell
# 1. Ollama >= 0.14.0
ollama --version

# 2. Model
ollama pull qwen3-coder:30b

# 3. Context — this is the critical part
setx OLLAMA_CONTEXT_LENGTH 32768
setx OLLAMA_FLASH_ATTENTION 1
setx OLLAMA_KV_CACHE_TYPE q8_0

# 4. Point Claude Code at Ollama
setx ANTHROPIC_BASE_URL http://localhost:11434
setx ANTHROPIC_AUTH_TOKEN ollama

# 5. Install + run
npm i -g @anthropic-ai/claude-code
claude --model qwen3-coder:30b
```

Restart your terminal after the `setx` calls.

**Why context matters more than you'd think:** Claude Code sends its system prompt (~6-10K tokens) plus all the tool definitions before your actual request. 32K is the floor — below that the session survives a few turns then silently degrades: edits truncate, tool calls drop arguments. Ollama's own docs recommend 32K minimum too.

**Model choice on your hardware:** `qwen3-coder:30b` is a MoE with ~3B active params. In Q4 it's ~18 GB, so it won't fit in your 2060 Super's 8 GB — it spills into your 32 GB RAM. Expect 8-12 tok/s. Slow, but it's the smallest model whose tool calling is actually reliable. If that's too painful, try `qwen3-coder:7b` — fits fully in VRAM, much faster, but drops tool calls more often on multi-file work.

**On why OpenCode failed:** almost certainly the same thing — insufficient context, or a model that can't emit valid tool JSON consistently. What error did you actually get? If it was a config/provider issue rather than model behavior, that's worth knowing, since Claude Code will hit the same wall.

Two caveats: some Ollama-behind-reverse-proxy setups make Claude Code hang on streaming, but localhost is fine. And if a tool insists on real Anthropic model names, `ollama cp qwen3-coder:30b claude-sonnet-4-5` works as an alias.
