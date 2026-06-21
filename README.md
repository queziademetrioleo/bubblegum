# deep-glm

Claude Code com **GLM 5.2** da Zhipu AI (智谱) via endpoint Anthropic-compatível da [Z.AI](https://api.z.ai/api/anthropic).

## Por que?

O GLM 5.2 é um modelo MoE de 744B parâmetros (~40B ativos), contexto de 1M tokens, licença MIT — e o endpoint Anthropic-compatível da Z.AI permite usar diretamente com o Claude Code, sem proxy nem tradução de formato.

| Comando | Backend | Custo relativo |
|---------|---------|-----------------|
| `claude` | Anthropic oficial | $$$ |
| `deepclaude` | DeepSeek V4 Pro | $ |
| `deep-glm` | GLM 5.2 (Z.AI) | $ |

## Instalação

```bash
# 1. Clone o repo
git clone https://github.com/queziademetrioleo/deep-glm.git
cd deep-glm

# 2. Instale no PATH
chmod +x deep-glm
sudo mv deep-glm /usr/local/bin/deep-glm
# ou, sem sudo:
# mv deep-glm ~/.local/bin/deep-glm
```

## Configuração

Gere sua chave em [Z.AI API Keys](https://api.z.ai) e adicione ao seu shell:

```bash
# ~/.zshrc ou ~/.bashrc
export ZAI_API_KEY="sua-chave-zai"
```

Recarregue: `source ~/.zshrc`

## Uso

```bash
deep-glm
```

Dentro do Claude Code, para tarefas complexas:

```
/effort max
```

## Modelos

| Variável | Modelo | Propósito |
|----------|--------|-----------|
| `ANTHROPIC_DEFAULT_OPUS_MODEL` | `glm-5.2` | Tarefas principais |
| `ANTHROPIC_DEFAULT_SONNET_MODEL` | `glm-5.2` | Tarefas normais |
| `ANTHROPIC_DEFAULT_HAIKU_MODEL` | `glm-4.7` | Subagentes / tarefas leves |

## Como funciona

O script configura variáveis de ambiente que o Claude Code lê:

```
ANTHROPIC_BASE_URL   → https://api.z.ai/api/anthropic
ANTHROPIC_AUTH_TOKEN  → $ZAI_API_KEY
ANTHROPIC_DEFAULT_*_MODEL → glm-5.2 / glm-4.7
```

O endpoint da Z.AI é nativamente compatível com o formato Anthropic Messages API — sem necessidade de proxy de tradução.

## Requisitos

- [Claude Code](https://claude.ai/code) instalado
- Chave de API da [Z.AI](https://api.z.ai)
- macOS, Linux, ou Windows (PowerShell — veja abaixo)

## Windows (PowerShell)

```powershell
# deep-glm.ps1
if (-not $env:ZAI_API_KEY) {
  Write-Error "Defina ZAI_API_KEY antes de rodar deep-glm."
  exit 1
}
$env:ANTHROPIC_AUTH_TOKEN = $env:ZAI_API_KEY
$env:ANTHROPIC_BASE_URL = "https://api.z.ai/api/anthropic"
$env:ANTHROPIC_DEFAULT_OPUS_MODEL = "glm-5.2"
$env:ANTHROPIC_DEFAULT_SONNET_MODEL = "glm-5.2"
$env:ANTHROPIC_DEFAULT_HAIKU_MODEL = "glm-4.7"
$env:CLAUDE_CODE_SUBAGENT_MODEL = "glm-4.7"
$env:CLAUDE_CODE_AUTO_COMPACT_WINDOW = "1000000"
if (-not $env:API_TIMEOUT_MS) { $env:API_TIMEOUT_MS = "3000000" }
claude @args
```

## Créditos

Inspirado no [deepclaude](https://github.com/aattaran/deepclaude) de @aattaran.

## Licença

MIT
