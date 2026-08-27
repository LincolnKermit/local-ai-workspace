# local-ai-workspace


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
