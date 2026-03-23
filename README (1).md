# 📲 n8n — Fluxo de Disparo em Massa via WhatsApp (Repesca de Leads)

Automação completa para disparo de templates WhatsApp para leads B2B, com validação de número, sincronização com Chatwoot e atualização de status no Google Sheets.

---

## 🧭 O que esse fluxo faz?

1. **Gatilho por botão** — verifica a cada minuto se a coluna `Start` na aba *Painel* está como `true`
2. **Lê os leads** sem status na aba *Leads* da planilha
3. **Valida o número de WhatsApp** via Evolution API (testa com e sem o dígito 9)
4. **Salva o número corrigido** na planilha
5. **Verifica no Chatwoot** se o contato já existe
   - Se existe: atualiza os dados (CNPJ, nome)
   - Se não existe: cria o contato
6. **Dispara o template WhatsApp** com os dados do lead (observação, razão social, CNPJ)
7. **Aguarda 10 segundos** entre cada envio para evitar bloqueio
8. **Atualiza o status** na planilha como `Sucesso` ou `Falha`

---

## 🛠️ Pré-requisitos

| Serviço | O que precisa |
|---|---|
| **Google Sheets** | Credencial OAuth2 conectada ao n8n |
| **Evolution API** | Instância configurada e credencial no n8n |
| **Chatwoot** | URL da instância + token de acesso da API |
| **WhatsApp Business API** | Phone Number ID + credencial no n8n |

---

## 📋 Estrutura da Planilha

### Aba `Painel`
| Coluna | Descrição |
|---|---|
| `Start` | Coloque `true` para iniciar o disparo |

### Aba `Leads`
| Coluna | Descrição |
|---|---|
| `razao` | Razão social da empresa |
| `cnpj` | CNPJ da empresa |
| `phone` | Telefone do lead |
| `observacao` | Texto variável do template (ex: nome do produto) |
| `status` | Preenchido automaticamente: `Sucesso` ou `Falha` |
| `Número corrigido` | Preenchido automaticamente após validação |
| `Hora` | Preenchido automaticamente com data/hora do envio |

---

## ⚙️ Como configurar

### 1. Importe o fluxo no n8n
- Acesse seu n8n → **Workflows** → botão **Import** → selecione o arquivo `workflow.json`

### 2. Substitua os placeholders
Busque por esses termos no JSON e substitua com seus valores reais:

| Placeholder | O que colocar |
|---|---|
| `SEU_GOOGLE_SHEETS_ID_AQUI` | ID da sua planilha (está na URL do Google Sheets) |
| `NOME_DA_SUA_CREDENCIAL_GOOGLE` | Nome da credencial Google no n8n |
| `NOME_DA_SUA_INSTANCIA_EVOLUTION` | Nome da instância na Evolution API |
| `NOME_DA_SUA_CREDENCIAL_EVOLUTION` | Nome da credencial Evolution no n8n |
| `SEU_DOMINIO_CHATWOOT` | Ex: `chat.suaempresa.com.br` |
| `SEU_ACCOUNT_ID` | ID da conta no Chatwoot (aparece na URL) |
| `SEU_TOKEN_CHATWOOT_AQUI` | Token de API do Chatwoot (Configurações → Tokens) |
| `SEU_WHATSAPP_PHONE_NUMBER_ID` | Phone Number ID do Meta for Developers |
| `NOME_DO_SEU_TEMPLATE` | Nome exato do template aprovado no WhatsApp |
| `NOME_DA_SUA_CREDENCIAL_WHATSAPP` | Nome da credencial WhatsApp no n8n |

### 3. Configure as credenciais no n8n
- Vá em **Settings → Credentials** e crie/conecte cada serviço

### 4. Ative o fluxo
- Clique em **Active** (toggle no canto superior direito)

### 5. Inicie um disparo
- Na aba **Painel** da planilha, mude a célula `Start` para `true`
- O fluxo detectará em até 1 minuto e iniciará automaticamente

---

## 📌 Observações importantes

- O fluxo testa **3 variações** do mesmo número (original, com 9, sem 9) para maximizar a chance de encontrar o WhatsApp ativo
- Os disparos têm um intervalo de **10 segundos** entre mensagens para seguir as boas práticas da Meta
- O lote é processado de **60 em 60 contatos** com 1 minuto de pausa entre lotes
- Após a execução, a coluna `Start` é automaticamente resetada para `false`

---

## 🔒 Segurança

> **Nunca** commite este arquivo com credenciais reais.  
> Todos os tokens, IDs e URLs sensíveis devem ser configurados diretamente no n8n ou via variáveis de ambiente.

---

## 📦 Tecnologias utilizadas

- [n8n](https://n8n.io/) — plataforma de automação
- [Evolution API](https://evolution-api.com/) — gateway WhatsApp
- [Chatwoot](https://www.chatwoot.com/) — CRM / atendimento
- [WhatsApp Business API](https://developers.facebook.com/docs/whatsapp) — envio de templates
- Google Sheets — base de dados dos leads
