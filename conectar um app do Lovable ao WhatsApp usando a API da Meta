# Guia simples: conectar um app do Lovable ao WhatsApp usando a API da Meta

> Material de orientação para uma pessoa no-code.
> Linguagem simples, foco prático e sem depender de programação manual.
> Atualizado em: 14/04/2026

---

## 1) O que você vai montar

Você vai ligar 3 peças:

1. **Lovable** → onde você cria a interface do seu sistema
2. **Lovable Cloud ou Supabase** → onde ficam o backend, os segredos e as automações
3. **WhatsApp Cloud API da Meta** → onde o WhatsApp realmente envia e recebe mensagens

### Em palavras simples

- O usuário clica em um botão no seu app
- O Lovable chama uma função segura no backend
- Essa função envia a mensagem para a Meta
- Quando o cliente responde no WhatsApp, a Meta chama um **webhook**
- O webhook grava a resposta no seu sistema

---

## 2) A forma mais fácil para quem é no-code

A melhor abordagem para quem não programa é esta:

- criar o app no **Lovable**
- ativar **Lovable Cloud** ou conectar **Supabase**
- guardar as chaves da Meta em **Secrets**
- pedir para o próprio Lovable criar uma **Edge Function**
- usar essa função para:
  - enviar mensagens
  - receber respostas via webhook
  - salvar tudo no banco

### Por que essa é a melhor forma?

Porque você **não deve colocar token do WhatsApp no frontend**.
Tudo que fica no frontend pode ser exposto.

---

## 3) O que você precisa antes de começar

Separe estes itens:

- uma conta no **Lovable**
- um projeto com **Lovable Cloud** habilitado ou com **Supabase** conectado
- uma conta em **Meta for Developers**
- um **Business Portfolio** / negócio configurado na Meta
- o produto **WhatsApp** adicionado no app da Meta
- um número de WhatsApp para testes no início ou um número comercial para produção

### Regra importante

Comece com **teste primeiro**.
Não tente ir direto para produção.

---

## 4) Como pensar na arquitetura

```mermaid
flowchart LR
    U[Usuário no app Lovable] --> A[Tela do sistema]
    A --> B[Edge Function no Lovable Cloud ou Supabase]
    B --> C[WhatsApp Cloud API da Meta]
    C --> D[Cliente no WhatsApp]
    D --> C
    C --> E[Webhook do seu sistema]
    E --> F[Banco de dados]
    F --> A
```

### Tradução do desenho

- **Tela do sistema**: onde a pessoa aperta o botão “Enviar WhatsApp”
- **Edge Function**: parte segura que conversa com a Meta
- **Webhook**: porta de entrada para receber respostas e status
- **Banco**: onde você salva conversas, leads, pedidos ou atendimentos

---

## 5) O que a Meta exige na prática

No começo, a Meta normalmente libera um ambiente de teste para você enviar a primeira mensagem.
Depois, para usar número real e operar de verdade, você vai concluir o cadastro do número comercial e demais etapas da plataforma da Meta.

### Conceitos simples que você precisa entender

#### a) Token
É como uma senha de acesso da API.

#### b) Phone Number ID
É o identificador do número dentro da plataforma da Meta.

#### c) Webhook
É a URL do seu sistema que a Meta chama quando chega mensagem nova ou muda o status de uma mensagem.

#### d) Template
É um modelo de mensagem aprovado.
Você usa principalmente quando quer iniciar conversa fora da janela normal de atendimento.

#### e) Janela de 24 horas
Quando o cliente fala com a sua empresa, abre uma janela de 24 horas.
Durante essa janela, você consegue responder com mais liberdade.

---

## 6) Passo a passo simples

## Passo 1 — Criar o app na Meta

No painel da Meta:

1. Acesse **Meta for Developers**
2. Crie um **App**
3. Adicione o produto **WhatsApp**
4. Entre na área de **API Setup**
5. Pegue os dados iniciais:
   - token temporário de teste
   - número de teste ou número conectado
   - phone number id
   - whatsapp business account id

### O que guardar
Crie uma anotação com:

- `WHATSAPP_TOKEN`
- `WHATSAPP_PHONE_NUMBER_ID`
- `WHATSAPP_WABA_ID`
- `META_APP_SECRET` (quando necessário para validações)
- `WEBHOOK_VERIFY_TOKEN` (um texto que você escolhe)

---

## Passo 2 — Preparar o projeto no Lovable

No Lovable, peça algo como:

```text
Crie um sistema simples de atendimento por WhatsApp.
Quero:
- uma tela para cadastrar contatos
- uma tela para listar conversas
- uma ação para enviar mensagem pelo WhatsApp
- um backend seguro usando Lovable Cloud ou Supabase
- uma tabela para salvar contatos, mensagens enviadas, mensagens recebidas e status
- tudo com linguagem simples e interface limpa
```

### Estrutura mínima sugerida

Tabelas ou coleções:

- `contacts`
- `whatsapp_messages`
- `whatsapp_webhook_events`

Campos básicos:

#### contacts
- id
- nome
- telefone
- created_at

#### whatsapp_messages
- id
- contact_id
- direction (`outbound` ou `inbound`)
- message_type
- content
- meta_message_id
- status
- created_at

#### whatsapp_webhook_events
- id
- event_type
- payload_json
- created_at

---

## Passo 3 — Guardar os segredos

No Lovable Cloud ou Supabase, salve os segredos.

Exemplos:

- `WHATSAPP_TOKEN`
- `WHATSAPP_PHONE_NUMBER_ID`
- `WEBHOOK_VERIFY_TOKEN`
- `META_APP_SECRET`

### Regra de ouro

**Nunca coloque esses valores em componentes visuais, páginas, JavaScript do navegador ou campos públicos.**

---

## Passo 4 — Pedir para o Lovable criar a função que envia mensagem

Use um prompt parecido com este:

```text
Crie uma Edge Function segura para enviar mensagem de texto pelo WhatsApp Cloud API da Meta.

Requisitos:
- usar os secrets WHATSAPP_TOKEN e WHATSAPP_PHONE_NUMBER_ID
- receber no body: to, message
- chamar a API oficial da Meta para envio de mensagem de texto
- salvar no banco o telefone, o conteúdo enviado, a data, o status inicial e o id retornado pela Meta
- retornar sucesso ou erro de forma amigável
- nunca expor token no frontend
```

### O que essa função vai fazer

Ela recebe:

- telefone do cliente
- texto da mensagem

E faz:

- envia para a Meta
- recebe a confirmação
- salva no banco
- devolve uma resposta para sua tela

---

## Passo 5 — Criar o botão “Enviar WhatsApp”

Peça ao Lovable:

```text
Na tela de contatos, adicione um botão "Enviar WhatsApp".
Ao clicar:
- abrir um modal simples
- permitir digitar a mensagem
- chamar a Edge Function de envio
- mostrar mensagem de sucesso ou erro
- atualizar o histórico da conversa
```

---

## Passo 6 — Criar o webhook para receber respostas

Agora você precisa da parte de entrada.

Peça ao Lovable:

```text
Crie uma Edge Function para funcionar como webhook da Meta para WhatsApp.

Requisitos:
- responder a validação GET do webhook usando hub.challenge e WEBHOOK_VERIFY_TOKEN
- receber eventos POST da Meta
- salvar o payload bruto em whatsapp_webhook_events
- identificar mensagens recebidas e status de entrega/leitura
- salvar mensagens recebidas em whatsapp_messages
- atualizar status das mensagens enviadas quando chegar status da Meta
- responder 200 OK
```

### Em palavras simples

Esse webhook vai:

- confirmar para a Meta que a URL é sua
- receber novas mensagens dos clientes
- receber status como enviado, entregue e lido
- guardar tudo no banco

---

## Passo 7 — Configurar o webhook na Meta

Depois que o Lovable publicar a função, você vai ter uma URL.

No painel da Meta:

1. Abra a área de **Webhooks**
2. Cole a **Callback URL**
3. Informe o **Verify Token**
4. Clique em verificar
5. Assine os campos ligados a **messages**

### Atenção

Quando a Meta verifica o webhook, ela envia uma chamada GET.
Seu endpoint precisa responder corretamente, senão a validação falha.

---

## Passo 8 — Fazer o primeiro teste

### Teste 1: envio

- abra sua tela no Lovable
- cadastre um contato com DDI e número corretos
- envie uma mensagem de texto
- confirme se ela aparece no WhatsApp do destinatário

### Teste 2: resposta do cliente

- responda a mensagem no WhatsApp
- veja se a Meta chama o webhook
- confira se o banco gravou a mensagem recebida
- veja se a conversa aparece no sistema

### Teste 3: status

- confirme se o sistema mostra estados como:
  - enviado
  - entregue
  - lido

---

## 7) Quando você vai precisar de template

Você vai precisar de **template** quando quiser iniciar conversa fora da janela de atendimento.

### Exemplo simples

Você quer mandar:

- confirmação de pedido
- cobrança
- lembrete de consulta
- aviso de entrega

Se o cliente **não falou recentemente com você**, o mais seguro é usar template aprovado.

---

## 8) Formato do telefone

Uma causa muito comum de erro é o telefone estar no formato errado.

### Regra prática

Use o número com:

- código do país
- código da região, quando aplicável
- número sem espaços e sem símbolos

Exemplo brasileiro:

`5511999998888`

---

## 9) O que normalmente dá erro

## Erro 1 — Token exposto

### Problema
Colocar token no frontend.

### Solução
Usar Secrets + Edge Function.

---

## Erro 2 — Webhook não valida

### Problema
A URL não responde do jeito que a Meta espera.

### Solução
Garantir que o endpoint GET devolva o `hub.challenge` quando o token bater.

---

## Erro 3 — Mensagem não chega

### Problema
Pode ser número incorreto, configuração incompleta, permissões, modo de teste ou envio fora da janela correta.

### Solução
Verificar:

- formato do telefone
- token atual
- número correto na Meta
- webhook ativo
- uso de template quando necessário

---

## Erro 4 — O app envia, mas não recebe respostas

### Problema
O webhook não está assinado corretamente ou não está salvando os eventos.

### Solução
Revisar:

- campo `messages` assinado na Meta
- URL correta
- resposta `200 OK`
- logs da Edge Function

---

## 10) Estrutura recomendada para um MVP

Para um primeiro projeto simples, monte assim:

### Telas
- Dashboard
- Contatos
- Conversas
- Configurações

### Funções
- cadastrar contato
- enviar mensagem manual
- receber resposta automática via webhook
- histórico por contato
- status da mensagem

### Banco
- contatos
- mensagens
- eventos do webhook

---

## 11) Prompt pronto para pedir tudo isso ao Lovable

Copie e cole:

```text
Quero criar um sistema no-code de atendimento por WhatsApp usando Lovable + backend seguro + API oficial da Meta.

Objetivo:
Criar uma aplicação web simples onde eu possa cadastrar contatos, enviar mensagens pelo WhatsApp, receber respostas via webhook e salvar o histórico.

Requisitos funcionais:
- tela de contatos com nome e telefone
- tela de conversa por contato
- botão para enviar mensagem de texto
- histórico completo de mensagens enviadas e recebidas
- atualização de status da mensagem
- interface simples, bonita e fácil de usar

Requisitos técnicos:
- usar Lovable Cloud ou Supabase
- criar tabelas contacts, whatsapp_messages e whatsapp_webhook_events
- usar secrets para armazenar credenciais da Meta
- criar uma Edge Function para enviar mensagem via WhatsApp Cloud API
- criar uma Edge Function para funcionar como webhook da Meta
- salvar payload bruto do webhook
- salvar mensagens recebidas
- atualizar status das mensagens enviadas
- nunca expor token no frontend
- mostrar mensagens de erro em linguagem simples
- incluir logs básicos para facilitar suporte

Quero que você gere tudo com foco em alguém no-code, usando boa organização e nomes fáceis de entender.
```

---

## 12) Prompt pronto só para a parte do envio

```text
Crie uma função segura de backend para enviar mensagens pelo WhatsApp Cloud API da Meta.
Use secrets para token e phone number id.
A função deve receber telefone e texto, enviar a mensagem, salvar o retorno no banco e devolver sucesso ou erro em linguagem simples.
Depois conecte essa função a um botão "Enviar WhatsApp" na tela de contatos.
```

---

## 13) Prompt pronto só para a parte do webhook

```text
Crie uma função de webhook para a Meta WhatsApp.
Ela deve:
- validar requisição GET com verify token
- aceitar eventos POST
- salvar o payload bruto
- identificar mensagens recebidas
- identificar status das mensagens enviadas
- atualizar o histórico da conversa
- responder 200 OK
- criar logs simples para troubleshooting
```

---

## 14) Ordem certa para construir

Siga esta ordem:

1. criar projeto no Lovable
2. conectar Lovable Cloud ou Supabase
3. criar banco
4. salvar secrets
5. criar função de envio
6. criar botão na interface
7. criar webhook
8. validar webhook na Meta
9. testar envio
10. testar resposta
11. testar status
12. só depois pensar em produção

---

## 15) Minha recomendação prática

Se você é totalmente no-code, faça assim:

### Melhor caminho
- **Lovable** para criar a aplicação
- **Lovable Cloud** ou **Supabase** para backend
- **WhatsApp Cloud API da Meta** para envio e recebimento
- **Templates** apenas quando precisar iniciar conversa fora da janela normal

### Evite no começo
- automações muito complexas
- chatbot grande demais
- múltiplos números
- integrações paralelas sem testar a base primeiro

Comece com o básico funcionando:

- cadastrar contato
- enviar mensagem
- receber resposta
- salvar histórico

Quando isso estiver estável, aí sim você evolui.

---

## 16) Checklist final

Antes de publicar, confirme:

- [ ] o projeto no Lovable está funcionando
- [ ] o backend está ativo
- [ ] os secrets foram configurados
- [ ] a função de envio está funcionando
- [ ] o webhook foi validado
- [ ] os eventos estão chegando
- [ ] as mensagens estão sendo salvas
- [ ] os status estão sendo atualizados
- [ ] o token não está exposto no frontend
- [ ] o fluxo foi testado de ponta a ponta

---

## 17) Fontes oficiais consultadas

### Lovable
- https://docs.lovable.dev/integrations/introduction
- https://docs.lovable.dev/integrations/supabase
- https://docs.lovable.dev/integrations/cloud
- https://docs.lovable.dev/introduction/faq
- https://docs.lovable.dev/features/security

### Meta / WhatsApp Business Platform
- https://developers.facebook.com/documentation/business-messaging/whatsapp/get-started
- https://developers.facebook.com/documentation/business-messaging/whatsapp/overview
- https://developers.facebook.com/documentation/business-messaging/whatsapp/messages/send-messages
- https://developers.facebook.com/documentation/business-messaging/whatsapp/templates/overview
- https://developers.facebook.com/documentation/business-messaging/whatsapp/webhooks/overview/
- https://developers.facebook.com/documentation/business-messaging/whatsapp/webhooks/create-webhook-endpoint/
- https://developers.facebook.com/documentation/business-messaging/whatsapp/business-phone-numbers/phone-numbers
- https://developers.facebook.com/documentation/business-messaging/whatsapp/access-tokens/

---

## 18) Resumo em uma frase

**Para uma pessoa no-code, a forma mais segura de conectar Lovable ao WhatsApp da Meta é usar o Lovable para criar a interface, usar backend com Secrets e Edge Functions para falar com a API da Meta e usar webhook para receber as respostas.**
