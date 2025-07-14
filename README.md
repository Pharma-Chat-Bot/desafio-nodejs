# Teste Técnico - Backend Node.js com Bayles e RabbitMQ

## Visão Geral

Este teste técnico tem como objetivo avaliar suas habilidades no desenvolvimento de aplicações backend em Node.js, especificamente na implementação de sistemas de mensageria utilizando a biblioteca Bayles em conjunto com RabbitMQ. O candidato deverá demonstrar conhecimento em arquitetura de microserviços, padrões de mensageria assíncrona e boas práticas de desenvolvimento.

## Contexto da Aplicação

Você deve desenvolver uma API REST que funcione como um gateway de mensagens, permitindo o envio e recebimento de mensagens através do RabbitMQ utilizando a biblioteca Bayles. Esta aplicação simula um cenário real onde diferentes serviços precisam se comunicar de forma assíncrona e confiável.

## Objetivos do Teste

O candidato deve demonstrar capacidade para:

- Configurar e integrar RabbitMQ com Node.js
- Utilizar a biblioteca Bayles para gerenciamento de mensagens
- Implementar endpoints REST para interação com o sistema de mensageria
- Aplicar boas práticas de desenvolvimento e tratamento de erros
- Documentar adequadamente o código e a API

## Requisitos Técnicos

### Tecnologias Obrigatórias

- **Node.js** (versão 16 ou superior)
- **Express.js** para criação da API REST
- **Bayles** para integração com RabbitMQ
- **RabbitMQ** como message broker
- **Docker** para containerização (opcional, mas recomendado)

### Dependências Sugeridas

```json
{
  "dependencies": {
    "express": "^4.18.0",
    "bayles": "^1.0.0",
    "cors": "^2.8.5",
    "helmet": "^6.0.0",
    "dotenv": "^16.0.0",
    "joi": "^17.6.0"
  },
  "devDependencies": {
    "nodemon": "^2.0.0",
    "jest": "^29.0.0",
    "supertest": "^6.2.0"
  }
}
```

## Funcionalidades Requeridas

### 1. Endpoint para Envio de Mensagens

**Rota:** `POST /api/messages/send`

**Descrição:** Endpoint responsável por receber mensagens via HTTP e enviá-las para uma fila RabbitMQ utilizando Bayles.

**Payload de Entrada:**
```json
{
  "queue": "nome_da_fila",
  "message": {
    "id": "unique_message_id",
    "content": "conteúdo da mensagem",
    "timestamp": "2024-01-15T10:30:00Z",
    "metadata": {
      "sender": "service_name",
      "priority": "high|medium|low"
    }
  }
}
```

**Resposta de Sucesso:**
```json
{
  "success": true,
  "messageId": "unique_message_id",
  "queueName": "nome_da_fila",
  "timestamp": "2024-01-15T10:30:00Z"
}
```

**Validações Necessárias:**
- Verificar se a fila existe ou criar automaticamente
- Validar formato da mensagem
- Garantir que o ID da mensagem seja único
- Implementar retry em caso de falha na conexão

### 2. Endpoint para Recebimento de Mensagens

**Rota:** `GET /api/messages/receive/:queueName`

**Descrição:** Endpoint que consome mensagens de uma fila específica do RabbitMQ e as retorna via HTTP.

**Parâmetros:**
- `queueName`: Nome da fila a ser consumida
- `limit` (query parameter): Número máximo de mensagens a serem retornadas (padrão: 10)
- `timeout` (query parameter): Tempo limite para aguardar mensagens em segundos (padrão: 5)

**Resposta de Sucesso:**
```json
{
  "success": true,
  "messages": [
    {
      "id": "unique_message_id",
      "content": "conteúdo da mensagem",
      "timestamp": "2024-01-15T10:30:00Z",
      "metadata": {
        "sender": "service_name",
        "priority": "high"
      },
      "receivedAt": "2024-01-15T10:30:05Z"
    }
  ],
  "queueName": "nome_da_fila",
  "totalReceived": 1
}
```

### 3. Endpoint de Status das Filas

**Rota:** `GET /api/queues/status`

**Descrição:** Endpoint que retorna informações sobre o status das filas RabbitMQ.

**Resposta:**
```json
{
  "success": true,
  "queues": [
    {
      "name": "fila_exemplo",
      "messageCount": 15,
      "consumerCount": 2,
      "isActive": true
    }
  ],
  "rabbitMQStatus": "connected",
  "timestamp": "2024-01-15T10:30:00Z"
}
```

## Estrutura do Projeto

```
projeto-bayles/
├── src/
│   ├── controllers/
│   │   ├── messageController.js
│   │   └── queueController.js
│   ├── services/
│   │   ├── baylesService.js
│   │   └── rabbitMQService.js
│   ├── middleware/
│   │   ├── validation.js
│   │   ├── errorHandler.js
│   │   └── rateLimiter.js
│   ├── config/
│   │   ├── database.js
│   │   └── rabbitmq.js
│   ├── utils/
│   │   └── logger.js
│   └── app.js
├── tests/
│   ├── unit/
│   └── integration/
├── docker/
│   ├── Dockerfile
│   └── docker-compose.yml
├── docs/
│   └── api-documentation.md
├── .env.example
├── .gitignore
├── package.json
└── README.md
```

## Implementação Detalhada

### Configuração do Bayles

O candidato deve configurar adequadamente a biblioteca Bayles para conectar com RabbitMQ:

```javascript
// Exemplo de configuração básica
const Bayles = require('bayles');

const baylesConfig = {
  connection: {
    hostname: process.env.RABBITMQ_HOST || 'localhost',
    port: process.env.RABBITMQ_PORT || 5672,
    username: process.env.RABBITMQ_USER || 'guest',
    password: process.env.RABBITMQ_PASS || 'guest',
    vhost: process.env.RABBITMQ_VHOST || '/'
  },
  queues: {
    defaultExchange: 'direct',
    prefetch: 10,
    durable: true
  }
};

const messageService = new Bayles(baylesConfig);
```

### Tratamento de Erros

Implementar tratamento robusto de erros para cenários como:

- Falha na conexão com RabbitMQ
- Fila inexistente
- Mensagem malformada
- Timeout na operação
- Limite de conexões excedido

### Logging e Monitoramento

Implementar sistema de logs estruturado que registre:

- Mensagens enviadas e recebidas
- Erros e exceções
- Métricas de performance
- Status das conexões

## Critérios de Avaliação

### Funcionalidade (30%)

- ✅ Endpoints funcionam conforme especificado
- ✅ Integração correta com RabbitMQ via Bayles
- ✅ Validação adequada de dados de entrada
- ✅ Tratamento de casos de erro

### Qualidade do Código (25%)

- ✅ Estrutura organizada e modular
- ✅ Código limpo e legível
- ✅ Uso de padrões de design apropriados
- ✅ Comentários e documentação

### Tratamento de Erros (20%)

- ✅ Implementação de try-catch adequada
- ✅ Retorno de códigos HTTP apropriados
- ✅ Mensagens de erro informativas
- ✅ Logging estruturado

### Testes (15%)

- ✅ Testes unitários para funções críticas
- ✅ Testes de integração para endpoints
- ✅ Cobertura de casos de erro
- ✅ Mocks apropriados para dependências externas

### Documentação (10%)

- ✅ README.md completo com instruções de instalação
- ✅ Documentação da API
- ✅ Comentários no código
- ✅ Exemplos de uso

## Instruções de Entrega

### Pré-requisitos

1. **RabbitMQ em execução** (pode usar Docker)
2. **Node.js 16+** instalado
3. **Git** para versionamento

### Configuração do Ambiente

1. Clone o repositório base (se fornecido) ou crie um novo projeto
2. Instale as dependências: `npm install`
3. Configure as variáveis de ambiente no arquivo `.env`
4. Inicie o RabbitMQ: `docker run -d --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3-management`
5. Execute a aplicação: `npm start`

### Variáveis de Ambiente

Crie um arquivo `.env` com as seguintes variáveis:

```env
# Servidor
PORT=3000
NODE_ENV=development

# RabbitMQ
RABBITMQ_HOST=localhost
RABBITMQ_PORT=5672
RABBITMQ_USER=guest
RABBITMQ_PASS=guest
RABBITMQ_VHOST=/

# Logging
LOG_LEVEL=info
LOG_FORMAT=json

# Rate Limiting
RATE_LIMIT_WINDOW=15
RATE_LIMIT_MAX_REQUESTS=100
```

### Comandos Disponíveis

```bash
# Instalar dependências
npm install

# Executar em modo desenvolvimento
npm run dev

# Executar testes
npm test

# Executar testes com cobertura
npm run test:coverage

# Executar linting
npm run lint

# Executar em produção
npm start
```

## Exemplos de Uso

### Enviando uma Mensagem

```bash
curl -X POST http://localhost:3000/api/messages/send \
  -H "Content-Type: application/json" \
  -d '{
    "queue": "orders",
    "message": {
      "id": "order_123",
      "content": "Nova ordem de compra",
      "timestamp": "2024-01-15T10:30:00Z",
      "metadata": {
        "sender": "order_service",
        "priority": "high"
      }
    }
  }'
```

### Recebendo Mensagens

```bash
curl -X GET "http://localhost:3000/api/messages/receive/orders?limit=5&timeout=10"
```

### Verificando Status das Filas

```bash
curl -X GET http://localhost:3000/api/queues/status
```

## Desafios Extras (Opcional)

Para candidatos que desejam demonstrar conhecimento avançado:

### 1. Implementar Dead Letter Queue

Configure uma Dead Letter Queue para mensagens que falharam no processamento:

```javascript
const deadLetterConfig = {
  exchange: 'dlx',
  routingKey: 'failed',
  ttl: 86400000 // 24 horas
};
```

### 2. Implementar Rate Limiting

Adicione rate limiting nos endpoints para prevenir abuso:

```javascript
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutos
  max: 100 // máximo 100 requests por IP
});
```

### 3. Implementar Autenticação

Adicione autenticação JWT nos endpoints:

```javascript
const jwt = require('jsonwebtoken');

const authenticateToken = (req, res, next) => {
  const authHeader = req.headers['authorization'];
  const token = authHeader && authHeader.split(' ')[1];
  
  if (!token) {
    return res.sendStatus(401);
  }
  
  jwt.verify(token, process.env.JWT_SECRET, (err, user) => {
    if (err) return res.sendStatus(403);
    req.user = user;
    next();
  });
};
```

### 4. Implementar Métricas

Adicione coleta de métricas usando Prometheus:

```javascript
const prometheus = require('prom-client');

const messagesSentCounter = new prometheus.Counter({
  name: 'messages_sent_total',
  help: 'Total number of messages sent',
  labelNames: ['queue', 'status']
});
```

### 5. Implementar Circuit Breaker

Implemente circuit breaker para conexões com RabbitMQ:

```javascript
const CircuitBreaker = require('opossum');

const options = {
  timeout: 3000,
  errorThresholdPercentage: 50,
  resetTimeout: 30000
};

const breaker = new CircuitBreaker(rabbitMQOperation, options);
```

## Recursos Adicionais

### Documentação Oficial

- [Bayles Documentation](https://github.com/bayles/bayles)
- [RabbitMQ Tutorials](https://www.rabbitmq.com/tutorials.html)
- [Express.js Guide](https://expressjs.com/en/guide/routing.html)

### Ferramentas Úteis

- **RabbitMQ Management UI**: http://localhost:15672 (guest/guest)
- **Postman Collection**: Para testar os endpoints
- **Docker Compose**: Para orquestração de containers

### Padrões de Mensageria

Familiarize-se com os seguintes padrões:

1. **Publisher/Subscriber**: Para broadcast de mensagens
2. **Request/Reply**: Para comunicação síncrona via mensagens
3. **Work Queues**: Para distribuição de tarefas
4. **Routing**: Para roteamento baseado em critérios

## Tempo Estimado

- **Configuração inicial**: 30 minutos
- **Implementação básica**: 3-4 horas
- **Testes e documentação**: 1-2 horas
- **Desafios extras**: 2-3 horas adicionais

**Total recomendado**: 4-6 horas para implementação completa

## Submissão

1. **Código fonte** em repositório Git (GitHub, GitLab, etc.)
2. **README.md** com instruções detalhadas de instalação e uso
3. **Documentação da API** (pode usar Swagger/OpenAPI)
4. **Arquivo .env.example** com variáveis necessárias
5. **Evidências de teste** (screenshots ou logs de execução)

### Checklist de Entrega

- [ ] Código fonte completo e organizado
- [ ] Endpoints funcionando conforme especificação
- [ ] Testes implementados e passando
- [ ] Documentação completa
- [ ] Tratamento de erros implementado
- [ ] Logging estruturado
- [ ] Variáveis de ambiente configuradas
- [ ] README.md com instruções claras

## Contato

Em caso de dúvidas sobre o teste, entre em contato através dos canais fornecidos pela equipe de recrutamento.

---

**Boa sorte e demonstre todo seu conhecimento técnico!**

*Este teste foi elaborado para avaliar competências técnicas específicas e simular cenários reais de desenvolvimento backend com sistemas de mensageria.*
