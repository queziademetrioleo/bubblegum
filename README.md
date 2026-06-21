# bubblegum

**Claude Code com backends baratos** — GLM 5.2 e DeepSeek V4 Pro, sem proxy, sem tradução de formato.

```
bubblegum              # GLM 5.2 (default)
bubblegum --deepseek   # DeepSeek V4 Pro
bubblegum --anthropic  # Anthropic oficial
```

## Backends

| Flag | Modelo | Fornecedor | Endpoint | Preço input/output (por 1M tokens) |
|------|--------|------------|----------|-------------------------------------|
| `--glm` | GLM 5.2 | Zhipu AI (智谱) | Z.AI | ~$0.70 / ~$2.80 |
| `--deepseek` | DeepSeek V4 Pro | DeepSeek | api.deepseek.com | $0.44 / $0.87 |
| `--anthropic` | Claude Opus/Sonnet | Anthropic | api.anthropic.com | $3.00 / $15.00 |

### GLM 5.2

- **744B parâmetros** MoE (~40B ativos por token)
- **1 milhão de tokens** de contexto nativo
- **Licença MIT** — pesos abertos, uso comercial liberado
- Endpoint **Anthropic-compatível nativo** via Z.AI
- Modelos: `glm-5.2` (principal) + `glm-4.7` (subagentes)

### DeepSeek V4 Pro

- **685B parâmetros** MoE (~37B ativos)
- **128K tokens** de contexto
- Endpoint Anthropic-compatível via `api.deepseek.com/anthropic`
- Modelos: `deepseek-v4-pro` (principal) + `deepseek-v4-flash` (subagentes)
- Cache automático de contexto (custo cai ~90% após primeira request)

## Instalação

```bash
# Clone
git clone https://github.com/queziademetrioleo/bubblegum.git
cd bubblegum

# Instale
chmod +x bubblegum
sudo mv bubblegum /usr/local/bin/bubblegum
# ou sem sudo:
# mv bubblegum ~/.local/bin/bubblegum
```

## Configuração

```bash
# GLM 5.2 (obtenha em https://api.z.ai)
export ZAI_API_KEY="sua-chave-zai"

# DeepSeek V4 (obtenha em https://platform.deepseek.com/api_keys)
export DEEPSEEK_API_KEY="sk-..."

# Backend padrão (opcional)
export BUBBLEGUM_DEFAULT="deepseek"  # ou "glm"
```

Adicione ao `~/.zshrc` ou `~/.bashrc` e `source` depois.

## Uso

```bash
# GLM 5.2 (default)
bubblegum

# DeepSeek V4
bubblegum --deepseek

# Anthropic normal
bubblegum --anthropic

# Ajuda
bubblegum --help
```

Dentro do Claude Code:

```
/status        # confirmar modelo ativo
/effort max   # esforço máximo para tarefas complexas
```

## Como funciona

Claude Code lê variáveis de ambiente para decidir pra onde mandar as chamadas de API. O `bubblegum` configura:

```
ANTHROPIC_BASE_URL          → endpoint do backend
ANTHROPIC_AUTH_TOKEN        → chave de API
ANTHROPIC_DEFAULT_*_MODEL   → nome do modelo
```

Sem proxy local, sem tradução de formato — ambos GLM e DeepSeek já expõem endpoints compatíveis com Anthropic Messages API.

## Windows (PowerShell)

```powershell
# bubblegum.ps1
param(
  [switch]$DeepSeek,
  [switch]$Anthropic
)

if ($Anthropic) {
  Remove-Item Env:ANTHROPIC_BASE_URL -ErrorAction SilentlyContinue
  claude @args
  exit 0
}

if ($DeepSeek) {
  if (-not $env:DEEPSEEK_API_KEY) {
    Write-Error "Defina DEEPSEEK_API_KEY"
    exit 1
  }
  $env:ANTHROPIC_AUTH_TOKEN = $env:DEEPSEEK_API_KEY
  $env:ANTHROPIC_BASE_URL = "https://api.deepseek.com/anthropic"
  $env:ANTHROPIC_DEFAULT_OPUS_MODEL = "deepseek-v4-pro"
  $env:ANTHROPIC_DEFAULT_SONNET_MODEL = "deepseek-v4-pro"
  $env:ANTHROPIC_DEFAULT_HAIKU_MODEL = "deepseek-v4-flash"
  $env:CLAUDE_CODE_SUBAGENT_MODEL = "deepseek-v4-flash"
  claude @args
  exit 0
}

# Default: GLM
if (-not $env:ZAI_API_KEY) {
  Write-Error "Defina ZAI_API_KEY"
  exit 1
}
$env:ANTHROPIC_AUTH_TOKEN = $env:ZAI_API_KEY
$env:ANTHROPIC_BASE_URL = "https://api.z.ai/api/anthropic"
$env:ANTHROPIC_DEFAULT_OPUS_MODEL = "glm-5.2"
$env:ANTHROPIC_DEFAULT_SONNET_MODEL = "glm-5.2"
$env:ANTHROPIC_DEFAULT_HAIKU_MODEL = "glm-4.7"
$env:CLAUDE_CODE_SUBAGENT_MODEL = "glm-4.7"
claude @args
```

## Créditos

Inspirado no [deepclaude](https://github.com/aattaran/deepclaude) do @aattaran.

## Licença

MIT
