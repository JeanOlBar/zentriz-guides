# Claude Code + AWS Bedrock — Guia de Configuração

**Conta AWS:** Ux_Solutions (`741452525714`)

---

## Índice

1. [Instalar AWS CLI](#1-instalar-aws-cli)
2. [Configurar AWS Profile](#2-configurar-aws-profile)
3. [Instalar Node.js](#3-instalar-nodejs)
4. [Instalar Claude Code](#4-instalar-claude-code)
5. [Configurar settings.json](#5-configurar-settingsjson)
6. [Instalar extensão no VSCode](#6-instalar-extensão-no-vscode)
7. [Verificar instalação](#7-verificar-instalação)

---

## 1. Instalar AWS CLI

### macOS

```bash
# Opção A — Homebrew
brew install awscli

# Opção B — Instalador oficial
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
sudo installer -pkg AWSCLIV2.pkg -target /
```

### Windows

Baixe e execute o instalador:
```
https://awscli.amazonaws.com/AWSCLIV2.msi
```

Ou via PowerShell (winget):
```powershell
winget install Amazon.AWSCLI
```

### Linux (Ubuntu/Debian)

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

### Verificar instalação

```bash
aws --version
# aws-cli/2.x.x ...
```

---

## 2. Configurar AWS Profile

O profile vincula suas credenciais à conta **Ux_Solutions** (`741452525714`).

### Obter as credenciais de acesso

1. Acesse o [Console AWS](https://console.aws.amazon.com) com a conta `741452525714`
2. Vá em **IAM → Users → seu usuário → Security credentials**
3. Clique em **Create access key**
4. Escolha **CLI** e anote:
   - `AWS_ACCESS_KEY_ID`
   - `AWS_SECRET_ACCESS_KEY`

### Criar o profile `Ux_Solutions`

```bash
aws configure --profile Ux_Solutions
```

Preencha quando solicitado:
```
AWS Access Key ID:     AKIA...
AWS Secret Access Key: xxxxxxxxxxxxxxxx
Default region name:   us-east-1
Default output format: json
```

Isso cria/atualiza dois arquivos:

**`~/.aws/credentials`**
```ini
[Ux_Solutions]
aws_access_key_id = AKIA...
aws_secret_access_key = xxxxxxxxxxxxxxxx
```

**`~/.aws/config`**
```ini
[profile Ux_Solutions]
region = us-east-1
output = json
```

> No Windows os arquivos ficam em `C:\Users\SeuUsuario\.aws\`

### Testar o profile

```bash
aws sts get-caller-identity --profile Ux_Solutions
```

Resposta esperada:
```json
{
    "UserId": "AIDA...",
    "Account": "741452525714",
    "Arn": "arn:aws:iam::741452525714:user/seu-usuario"
}
```

---

## 3. Instalar Node.js

Claude Code requer **Node.js 18+**.

### macOS

```bash
# Via Homebrew
brew install node

# Via nvm (recomendado para gerenciar versões)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
nvm install --lts
nvm use --lts
```

### Windows

Baixe o instalador LTS em `https://nodejs.org` ou via winget:
```powershell
winget install OpenJS.NodeJS.LTS
```

### Linux

```bash
# Via nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.bashrc
nvm install --lts
nvm use --lts
```

### Verificar

```bash
node --version   # v22.x.x
npm --version    # 10.x.x
```

---

## 4. Instalar Claude Code

```bash
npm install -g @anthropic-ai/claude-code
```

### Verificar instalação

```bash
claude --version
```

---

## 5. Configurar settings.json

Crie (ou edite) o arquivo `~/.claude/settings.json`:

### macOS / Linux

```bash
mkdir -p ~/.claude
nano ~/.claude/settings.json
```

### Windows (PowerShell)

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude"
notepad "$env:USERPROFILE\.claude\settings.json"
```

### Conteúdo do arquivo

```json
{
  "env": {
    "CLAUDE_CODE_USE_BEDROCK": "1",
    "AWS_REGION": "us-east-1",
    "AWS_PROFILE": "Ux_Solutions",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "us.anthropic.claude-sonnet-4-6[1m]",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "us.anthropic.claude-opus-4-7[1m]",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "us.anthropic.claude-haiku-4-5-20251001-v1:0"
  },
  "permissions": {
    "defaultMode": "auto"
  }
}
```

> **`AWS_PROFILE`** deve ser exatamente `Ux_Solutions` (igual ao nome criado no `aws configure`).

---

## 6. Instalar extensão no VSCode

1. Abra o VSCode
2. Vá em **Extensions** (`Ctrl+Shift+X` / `Cmd+Shift+X`)
3. Pesquise: `Claude Code`
4. Instale a extensão **Claude Code** (publisher: Anthropic)
5. Reinicie o VSCode

Após instalar, o painel do Claude Code aparece na barra lateral.

---

## 7. Verificar instalação

### Teste completo via terminal

```bash
# 1. Confirmar profile AWS
aws sts get-caller-identity --profile Ux_Solutions

# 2. Invocar modelo Bedrock diretamente
aws bedrock-runtime invoke-model \
  --model-id us.anthropic.claude-haiku-4-5-20251001-v1:0 \
  --body '{"anthropic_version":"bedrock-2023-05-31","max_tokens":50,"messages":[{"role":"user","content":"Hi"}]}' \
  --content-type application/json \
  --accept application/json \
  --profile Ux_Solutions \
  --region us-east-1 \
  /tmp/output.json && cat /tmp/output.json

# 3. Iniciar Claude Code
claude
```

### No VSCode

1. Abra uma pasta de projeto
2. Clique no ícone Claude Code na barra lateral
3. Digite uma mensagem — se responder, está tudo funcionando

---

## Estrutura de arquivos após configuração

```
~/.aws/
├── credentials       # chaves de acesso (Ux_Solutions)
└── config            # região e output

~/.claude/
└── settings.json     # Bedrock + modelos + permissões
```

---

## Erros comuns

| Erro | Causa | Solução |
|------|-------|---------|
| `Could not load credentials` | Profile não existe ou nome errado | Verificar `aws configure --profile Ux_Solutions` |
| `AccessDeniedException` | Sem permissão no Bedrock | Adicionar policy Bedrock ao usuário IAM |
| `ResourceNotFoundException` | Modelo não ativado | Ativar no console Bedrock → Model access |
| `claude: command not found` | npm global sem permissão | Usar `sudo npm install -g` ou configurar nvm |
| Extensão VSCode não conecta | `settings.json` errado ou `claude` não no PATH | Verificar caminho e reiniciar VSCode |

---

## Referências

- [Claude Code Docs](https://docs.anthropic.com/claude-code)
- [AWS Bedrock — Modelos suportados](https://docs.aws.amazon.com/bedrock/latest/userguide/models-supported.html)
- [AWS CLI — Configurar profiles](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html)
