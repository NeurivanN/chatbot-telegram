# Chatbot Telegram - Temperatura de Cidades

Chatbot no Telegram que informa a temperatura atual de qualquer cidade do Brasil utilizando o node nativo OpenWeatherMap do N8N.

## Funcionalidades

- Recebe o nome da cidade via mensagem no Telegram
- Consulta a temperatura atual usando o node OpenWeatherMap
- Retorna uma mensagem amigável com a temperatura em graus Celsius
- Tratamento de erros para cidades não encontradas

## Arquitetura do Workflow

```
Telegram Trigger → Formatar Entrada → OpenWeatherMap → Validar Resposta
                                                              │
                              ┌───────────────────────────────┴───────────────────┐
                              ↓ (TRUE - cod=200)                                  ↓ (FALSE)
                        Code Fallback → Preparar Mensagem → Enviar Temperatura    Enviar Erro
```

## Pré-requisitos

1. **Instância N8N** (v1.0 ou superior)
2. **Bot do Telegram** (criado via @BotFather)
3. **Conta OpenWeather** (API gratuita)

## Importação do Workflow

### Passo 1: Importar o arquivo JSON

1. Acesse sua instância N8N
2. Clique em **"Add Workflow"** (ou **"+"**)
3. Selecione **"Import from File"**
4. Escolha o arquivo `workflow-chatbot-telegram.json`
5. Clique em **"Import"**

### Passo 2: Configurar Credenciais

#### Telegram Bot (Obrigatório)

1. No Telegram, abra conversa com **@BotFather**
2. Envie `/newbot` e siga as instruções
3. Copie o **Bot Token** gerado
4. No N8N, vá em **Credentials** → **Add Credential**
5. Selecione **"Telegram"**
6. Cole o **Bot Token** no campo apropriado
7. Salve como **"Telegram account"**

**Associar credencial aos nodes:**
- Clique no node **"Telegram Trigger"** → selecione a credencial
- Clique no node **"Enviar Temperatura"** → selecione a credencial
- Clique no node **"Enviar Erro"** → selecione a credencial

#### OpenWeatherMap API (Obrigatório)

1. Acesse [openweathermap.org](https://openweathermap.org/)
2. Crie uma conta gratuita
3. Vá em **API Keys** e copie sua chave
4. No N8N, vá em **Credentials** → **Add Credential**
5. Selecione **"OpenWeatherMap API"**
6. Cole a **API Key** no campo apropriado
7. Salve como **"OpenWeatherMap account"**
8. Associe ao node **"OpenWeatherMap"**

## Ativação do Workflow

1. Após configurar todas as credenciais, clique em **"Activate"** (toggle no canto superior direito)
2. O workflow ficará escutando mensagens do Telegram

## Como Testar

### Teste 1: Cidade válida
Envie uma mensagem para seu bot no Telegram:
```
São Paulo, SP
```

**Resposta esperada:**
```
🌤️ A temperatura em São Paulo é de 25°C.
```

### Teste 2: Outras cidades
```
Rio de Janeiro, RJ
Belo Horizonte, MG
Curitiba, PR
Salvador, BA
```

Ou apenas o nome da cidade:
```
São Paulo
Rio de Janeiro
Brasília
```

### Teste 3: Cidade inexistente
```
CidadeQueNaoExiste
```

**Resposta esperada:**
```
❌ Cidade não encontrada. Use o formato Cidade,UF (ex.: São Paulo,SP).
```

## Estrutura dos Nodes

| Node | Função |
|------|--------|
| **Telegram Trigger** | Recebe mensagens do usuário |
| **Formatar Entrada** | Extrai nome da cidade e adiciona `,BR` para a API |
| **OpenWeatherMap** | Node nativo que consulta a API OpenWeather |
| **Validar Resposta** | Verifica se `cod == 200` (sucesso) |
| **Code Fallback** | Formata a mensagem com temperatura |
| **Preparar Mensagem Final** | Prepara a mensagem para envio |
| **Enviar Temperatura** | Envia resposta de sucesso via Telegram |
| **Enviar Erro** | Envia mensagem de erro via Telegram |

## Detalhes Técnicos

### Formatação da Entrada

O node **Formatar Entrada** processa o texto do usuário:

```javascript
{{ $json.message.text.trim().split(',')[0].trim() }},BR
```

**Transformações:**
- `São Paulo, SP` → `São Paulo,BR`
- `Rio de Janeiro` → `Rio de Janeiro,BR`
- `  Curitiba  ` → `Curitiba,BR`

### Code Fallback

Gera a mensagem formatada com a temperatura:

```javascript
const cidade = $json.name;
const temp = Math.round($json.main.temp);
const mensagemFallback = `🌤️ A temperatura em ${cidade} é de ${temp}°C.`;
```

## Variáveis e Credenciais Esperadas

### Variáveis de Ambiente

| Variável | Descrição | Onde Obter |
|----------|-----------|------------|
| `OPENWEATHER_API_KEY` | Chave da API OpenWeather | [openweathermap.org/api](https://openweathermap.org/api) |
| `TELEGRAM_BOT_TOKEN` | Token do bot do Telegram | [@BotFather](https://t.me/BotFather) no Telegram |

### Credenciais N8N

| Nome | Tipo | Campo Principal | Obrigatório |
|------|------|-----------------|-------------|
| Telegram account | `telegramApi` | `TELEGRAM_BOT_TOKEN` | Sim |
| OpenWeatherMap account | `openWeatherMapApi` | `OPENWEATHER_API_KEY` | Sim |

> **Importante:** As credenciais são configuradas diretamente na interface do N8N (Credentials → Add Credential). O arquivo JSON exportado **não contém** tokens ou chaves de API.

## Solução de Problemas

### Erro: "Cidade não encontrada" para cidades válidas
- Verifique se está digitando o nome corretamente
- Tente apenas o nome da cidade sem o estado (ex.: `São Paulo`)
- Verifique se a credencial OpenWeatherMap está configurada

### Erro: Bot não responde
- Verifique se o workflow está ativado
- Verifique se as credenciais do Telegram estão configuradas em todos os nodes
- Verifique os logs de execução no N8N

### Erro: "Invalid API key"
- Verifique se a chave OpenWeather está correta na credencial
- Chaves novas podem demorar até 2 horas para ativar

## Docker Compose (Opcional)

Se você usa Docker para rodar o N8N localmente:

```yaml
version: '3.8'
services:
  n8n:
    image: n8nio/n8n:latest
    ports:
      - "5678:5678"
    environment:
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=admin
      - N8N_BASIC_AUTH_PASSWORD=senha_segura
      - WEBHOOK_URL=https://seu-dominio.com/
    volumes:
      - n8n_data:/home/node/.n8n

volumes:
  n8n_data:
```

## Licença

Este projeto é disponibilizado para fins educacionais.

---

**Desenvolvido com N8N + Claude Code**
