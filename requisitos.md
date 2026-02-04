Para esse desafio, você vai desenvolver um chatbot no Telegram utilizando N8N que informe a temperatura atual de qualquer cidade do Brasil. O chatbot deve receber o nome da cidade e estado via mensagem, consultar com a API gratuita do OpenWeather, processar a resposta e devolver ao usuário uma mensagem curta, clara e amigável com a temperatura. O chatbot deve funcionar conforme o vídeo abaixo.

## Requisitos do Workflow do chatbot no N8N

Os seguintes pontos **devem ser obrigatoriamente atendidos no chatbot**. Eles serão usados como critérios para a avaliação do seu projeto.

### Requisitos obrigatórios do Workflow N8N

1. **Trigger inicial no workflow**
    - O workflow deve iniciar com um **Telegram Trigger node** configurado para receber mensagens de texto. (A criação do bot no Telegram e o token serão tratados fora do escopo do workflow.)
2. **Captura e formatação da entrada**
    - Capture o texto recebido e o insira em uma variável chamada`queue` usando um **Set node** ou expressão.
    - Remover espaços, normalize a acentuação/letras minúsculos e ajuste o texto conforme as regras da API da OpenWeather.
3. **Chamada à OpenWeather usando variável de ambiente**
    - Inclua um nó **HTTP Request** que consulte o endpoint da OpenWeather:
        
        `https://api.openweathermap.org/data/2.5/weather`
        
    - Ative a opção **“Send Query Parameters” dentro do nó e insira os parâmetros com os seus respectivos valores:**
    - queue (a mensagem formatada)
    - units (defina em graus celsius)
    - lang (português brasileiro)
    - appid (a sua`OPENWEATHER_API_KEY` )
4. **Extração e formatação dos dados**
    - Do JSON retornado extraia a temperatura da cidade.
    - Converta a temperatura para um valor arredondado quando apropriado e prepare uma string final com o nome da cidade e a temperatura atual.
5. **Validação da resposta e tratamento de erro**
    - Usar um **IF node** (ou lógica equivalente) para verificar se a resposta contém os campos esperados e se o status HTTP indica sucesso.
    - Em caso de cidade não encontrada ou resposta inválida, encaminhar fluxo para o nó que envia a mensagem de erro.
6. **Resposta formatada ao usuário via Telegram**
    - Use o **Telegram Send Message node** para enviar a resposta formatada:
    **Exemplo:** `🌤️ A temperatura em Belo Horizonte é de 25°C.`
    - Em caso de erro, enviar ao usuário: `❌ Cidade não encontrada. Use o formato Cidade,UF (ex.: São Paulo,SP).`
7. **Exportação e documentação do workflow**
    - Exportar o workflow como `workflow-telegram-chatbot.json`.
    - Incluir no repositório um **README.md** com a descrição do chatbot, instruções claras de importação e como inserir as de credenciais do Telegram e OpenWeather, com as variáveis esperadas (`OPENWEATHER_API_KEY`, `TELEGRAM_BOT_TOKEN`).
    - Garanta e Verifique que o JSON do seu workflow exportado **não** contenha credenciais ou tokens embutidos.

### Requisitos opcionais

1. **Uso do Google Gemini para melhoria de saída**
- Incluir um **nó Google Gemini** para melhorar a mensagem final (reescrita natural, tom, tradução, desambiguação).
- **Configuração recomendada do Gemini:** temperatura baixa (0–0.2) para respostas determinísticas; instruir saída em português e em formato simples (ex.: `{"message":"...","ok":true}`).
- **Obrigatoriedade de fallback:** se o Gemini for usado, o workflow **deve** também conter um **Function/Code node** que gere a mesma `message` de forma determinística (fallback). A automação avaliadora usará o fallback quando não houver credenciais Gemini disponíveis, garantindo avaliação sem custos.
- No **README**, documentar onde o nó Gemini foi colocado e como ativá-lo com instruções para inserir as credenciais no N8N. **Não incluir chaves no repositório.**

---

## Entrega

A entrega deve incluir **obrigatoriamente dois itens:** 

- Arquivo JSON do seu workflow exportado do N8N. o nome do arquivo deve ser: `workflow-chatbot-telegram.json`
- Arquivo markdown `README.md` contendo a descrição do projeto, Passos para importar o workflow dentro do N8N e como inserir as de credenciais do Telegram e OpenWeather no N8N, com as variáveis esperadas (`OPENWEATHER_API_KEY`, `TELEGRAM_BOT_TOKEN`) e como executar o chatbot.  (ex.: enviar uma cidade de teste e o que esperar de retorno).

**Arquivo opcional:** `docker-compose.yml` se você utiliza o Docker para rodar o N8N localmente.

### Checklist antes de enviar o projeto

- **Workflow funcional:** teste com pelo menos 3 cidades.
- **Tratamento de erros:** teste com uma cidade inexistente e veja a resposta de erro.
- **Mensagens amigáveis:** verifique a formatação da mensagem de temperatura ao usuário.
- **Credenciais fora do repositório:** confirme que nenhum token real está no JSON ou README.

### Observações finais sobre a entrega

- O repositório deve ser **público** para permitir avaliação automática.
- Não suba arquivos com segredos reais. Use apenas o `README.md` para documentar as variáveis necessárias.
- Se desejar, inclua instruções adicionais no `README.md` para facilitar a reprodução do ambiente (ex.: comandos Docker, versões recomendadas do N8N).

