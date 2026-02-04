# Projeto N8N Workflow Builder

## Visão Geral do Projeto

Este projeto foi configurado para auxiliar na criação de workflows de alta qualidade no N8N. O Claude tem acesso a ferramentas específicas que permitem interagir diretamente com a instância N8N e aplicar boas práticas de automação.

## Ferramentas Disponíveis

### 1. Servidor MCP do N8N (n8n-mcp)

**Cobertura:**
- 1.084 nodes disponíveis (537 core + 547 community)
- 2.709 templates de workflow
- 99% de cobertura de propriedades de nodes
- 265 variantes de ferramentas com capacidade de IA

**Ferramentas Core (7 ferramentas):**
- Lookup de propriedades e validação de nodes
- Descoberta de operações disponíveis
- Busca de templates
- Descoberta de integrações da comunidade

**Ferramentas de Gerenciamento (13 ferramentas - requer API):**
- Criar, editar e gerenciar workflows
- Executar workflows e recuperar resultados
- Listar e cancelar execuções
- Validar configurações e verificar saúde do sistema

**Configuração Necessária:**
- `N8N_API_URL`: URL da sua instância N8N
- `N8N_API_KEY`: Token de autenticação da API

### 2. Skills do N8N (n8n-skills)

São 7 competências que ativam automaticamente quando consultas relevantes são detectadas:

| Skill | Função | Quando Ativa |
|-------|--------|--------------|
| **Expression Syntax** | Sintaxe de expressões `{{}}` | Escrever expressões, troubleshooting |
| **MCP Tools Expert** | Uso eficaz das ferramentas MCP | Buscar nodes, validar, acessar templates |
| **Workflow Patterns** | 5 padrões arquitetônicos | Criar workflows, conectar nodes |
| **Validation Expert** | Interpretação de erros | Falhas de validação, debugging |
| **Node Configuration** | Configuração de nodes | Configurar nodes, dependências |
| **Code JavaScript** | JavaScript em Code nodes | Escrever código JS, $helpers |
| **Code Python** | Python em Code nodes | Código Python (uso limitado) |

## Sintaxe de Expressões N8N

### Variáveis Core
- `$json` - Dados JSON do item atual
- `$node` - Referência a outros nodes no workflow
- `$now` - Data/hora atual
- `$env` - Variáveis de ambiente
- `$input` - Dados de entrada do node

### Acesso a Dados
```javascript
// Primeiro item
$input.first().json

// Todos os itens
$input.all()

// Dados de webhook (importante!)
$json.body  // dados do webhook estão aqui

// Referência a outro node
$node["Nome do Node"].json
```

### Retorno Correto em Code Nodes
```javascript
// Sempre retornar array de objetos com propriedade json
return [{ json: { resultado: "valor" } }]
```

## Padrões de Workflow (5 Padrões Arquitetônicos)

### 1. Webhook Pattern
- Recebimento de dados externos via HTTP
- Validação de payload
- Processamento e resposta

### 2. HTTP API Pattern
- Integração com APIs externas
- Autenticação e headers
- Tratamento de respostas e erros

### 3. Database Pattern
- Operações CRUD com banco de dados
- Queries otimizadas
- Transações quando necessário

### 4. AI Pattern
- Integração com modelos de IA
- Processamento de prompts
- Gestão de tokens e custos

### 5. Scheduled Pattern
- Execuções agendadas (cron)
- Batch processing
- Limpeza e manutenção periódica

## Diretrizes para Criação de Workflows

### Antes de Criar um Workflow

1. **Entender o Objetivo**: Perguntar claramente qual problema o workflow deve resolver
2. **Mapear Integrações**: Identificar quais serviços/APIs serão conectados
3. **Verificar Credenciais**: Confirmar se as credenciais necessárias já existem na instância
4. **Consultar Skills**: Sempre consultar as skills do N8N para aplicar boas práticas

### Padrões de Qualidade

#### Nomenclatura
- Workflows: usar nomes descritivos em português (ex: "WhatsApp - Atendimento Automatizado")
- Nós: nomear cada nó de forma clara indicando sua função
- Variáveis: usar camelCase para variáveis internas

#### Estrutura
- Sempre incluir nó de tratamento de erros (Error Trigger)
- Usar Sub-workflows para lógicas reutilizáveis
- Implementar logs em pontos críticos do fluxo
- Adicionar notas explicativas em seções complexas

#### Performance
- Evitar loops desnecessários
- Usar batch processing quando possível
- Implementar rate limiting para APIs externas
- Otimizar queries e filtros de dados

#### Segurança
- Nunca fazer hardcode de credenciais ou tokens
- Usar variáveis de ambiente para dados sensíveis
- Implementar validação de inputs
- Sanitizar dados antes de operações com banco de dados

## Fluxo de Trabalho Recomendado

```
1. Receber requisição do usuário
2. Consultar skills do N8N para padrões relevantes
3. Verificar credenciais disponíveis via MCP
4. Propor estrutura do workflow
5. Criar workflow via MCP
6. Testar e validar
7. Documentar funcionalidades
```

## Integrações Comuns (Contexto QAIA)

### WhatsApp (Z-API)
- Recebimento de mensagens (webhook)
- Envio de mensagens de texto, áudio, imagem
- Gerenciamento de conversas
- Integração com agentes IA

### Telegram
- **Trigger**: Telegram Trigger para receber mensagens e callbacks
- **Envio de mensagens**: texto, fotos, documentos, áudio, vídeo
- **Teclados interativos**: Inline keyboards e Reply keyboards
- **Comandos de bot**: Configuração de /comandos personalizados
- **Callbacks**: Tratamento de callback_query para botões inline
- **Grupos e canais**: Gerenciamento de mensagens em grupos
- **Webhooks**: Configuração de webhook vs polling
- **Edição de mensagens**: Atualização de mensagens existentes
- **Credenciais**: Bot Token via @BotFather
- **Rate Limits**: 30 mensagens/segundo para grupos, 1 mensagem/segundo para mesmo usuário

### Banco de Dados
- Supabase (PostgreSQL)
- Redis (cache e estado)
- Notion (CRM)

### IA e Processamento
- OpenAI (GPT-4, Whisper)
- Anthropic (Claude)
- Processamento de áudio/imagem

### Calendário e Agendamento
- Google Calendar
- Google Meet

## Comandos Úteis

Quando o usuário solicitar:

| Solicitação | Ação |
|-------------|------|
| "Criar workflow para..." | Consultar skills → Propor estrutura → Criar via MCP |
| "Listar workflows" | Usar MCP para listar workflows existentes |
| "Debugar workflow X" | Analisar estrutura e logs via MCP |
| "Otimizar workflow X" | Revisar e sugerir melhorias de performance |
| "Duplicar workflow" | Copiar e adaptar workflow existente |

## Respostas e Comunicação

- Responder sempre em **português brasileiro**
- Explicar decisões técnicas de forma clara
- Oferecer alternativas quando houver múltiplas abordagens
- Alertar sobre possíveis limitações ou custos (tokens, rate limits)
- Documentar o workflow criado com instruções de uso

## Code Nodes - Boas Práticas

### JavaScript (Preferido - 95% dos casos)

**Padrões de Acesso a Dados:**
```javascript
// Acessar todos os itens de entrada
const items = $input.all();

// Acessar primeiro item
const firstItem = $input.first();

// Iterar sobre itens
for (const item of items) {
  const data = item.json;
}
```

**Retorno Correto:**
```javascript
// Sempre retornar array de objetos
return items.map(item => ({
  json: {
    ...item.json,
    processado: true
  }
}));
```

### Python (Usar com Cautela)

⚠️ **Limitações importantes:**
- Sem bibliotecas externas (requests, pandas, numpy)
- Apenas stdlib disponível
- Performance inferior ao JavaScript

**Quando usar Python:**
- Processamento de texto complexo com regex
- Cálculos matemáticos específicos
- Quando a lógica já existe em Python

## Tratamento de Erros

Ao encontrar erros:
1. Identificar o nó/ponto de falha
2. Consultar documentação nas skills
3. Propor solução com explicação
4. Implementar correção via MCP
5. Testar novamente

## Versionamento e Backup

### Boas Práticas
- Exportar workflows como JSON antes de grandes alterações
- Manter nomenclatura com versão quando necessário (ex: "Atendimento v2.0")
- Usar tags para categorizar workflows por status (produção, desenvolvimento, teste)
- Documentar changelog de alterações significativas

### Backup
- Exportar workflows críticos periodicamente
- Armazenar JSONs em repositório Git quando possível
- Manter cópia das credenciais em local seguro (sem tokens expostos)

## Monitoramento e Métricas

### Saúde dos Workflows
- Verificar execuções com erro no painel do N8N
- Configurar alertas para falhas críticas (via webhook ou email)
- Monitorar tempo de execução de workflows pesados
- Revisar logs periodicamente para identificar gargalos

### Métricas Importantes
- Taxa de sucesso/falha por workflow
- Tempo médio de execução
- Volume de execuções por período
- Consumo de recursos (memória, CPU)

## Notas Importantes

> ⚠️ **AVISO CRÍTICO: Nunca edite workflows de produção diretamente com IA!**

### Práticas de Segurança com IA
- Criar cópias do workflow antes de editar
- Testar em ambiente de desenvolvimento
- Exportar backups antes de alterações
- Validar todas as mudanças antes do deploy em produção

### Diretrizes Gerais
- Sempre verificar a versão do N8N antes de usar funcionalidades específicas
- Considerar limites de execução e timeout
- Manter workflows modulares e reutilizáveis
- Priorizar simplicidade sobre complexidade desnecessária
- Documentar dependências externas (APIs, webhooks, etc.)

## Links Úteis

### Ferramentas MCP e Skills
- [n8n-MCP Server](https://github.com/czlonkowski/n8n-mcp) - Servidor MCP para integração com N8N
- [n8n-Skills](https://github.com/czlonkowski/n8n-skills) - Skills para Claude Code
- [Dashboard MCP](https://dashboard.n8n-mcp.com) - Painel de controle (100 chamadas/dia grátis)

### Documentação Oficial
- [Documentação Oficial N8N](https://docs.n8n.io/)
- [API do Telegram Bot](https://core.telegram.org/bots/api)
- [Z-API WhatsApp](https://developer.z-api.io/)
- [Supabase Docs](https://supabase.com/docs)
- [OpenAI API](https://platform.openai.com/docs)
- [Anthropic Claude API](https://docs.anthropic.com/)
