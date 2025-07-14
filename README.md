# 🧪 Desafio Técnico – Backend Node.js com Bayles e RabbitMQ

## 📝 Descrição

Implemente um serviço HTTP com dois endpoints principais:

1. `POST /send` – Envia uma mensagem via **WhatsApp** usando a biblioteca **Bayles**.
2. `POST /receive` – Simula o recebimento de uma mensagem, que será processada através do **RabbitMQ**.

As mensagens devem transitar pela fila do RabbitMQ tanto para envio quanto para simulação de recebimento. Isso simula um sistema desacoplado e resiliente.

---

## ✅ Requisitos

### Tecnologias obrigatórias

- Node.js (versão 16+)
- Express
- Bayles
- RabbitMQ (via amqplib)
- dotenv (para variáveis de ambiente)

---

## 🛠️ Regras de Implementação

### 1. `POST /send`

- Entrada (body JSON):

  ```json
  {
    "phone": "5511999999999",
    "message": "Olá, mundo!"
  }
