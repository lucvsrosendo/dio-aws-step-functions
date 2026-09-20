# Orquestração de Workflows Serverless com AWS Step Functions

Este repositório contém a documentação prática, diagramas e aprendizados adquiridos durante o laboratório de **AWS Step Functions** pela [Digital Innovation One (DIO)](https://www.dio.me/).

O objetivo principal deste projeto é consolidar conceitos sobre orquestração de microsserviços, gerenciamento de estados de execução, tratamento de erros e integração serverless no ecossistema AWS.

---

## 📌 Sumário
- [Visão Geral do AWS Step Functions](#-visão-geral-do-aws-step-functions)
- [Principais Conceitos Abordados](#-principais-conceitos-abordados)
- [Estrutura do Workflow (State Machine)](#-estrutura-do-workflow-state-machine)
- [Tratamento de Erros e Retentativas](#-tratamento-de-erros-e-retentativas)
- [Estrutura do Repositório](#-estrutura-do-repositório)
- [Conclusão e Insights](#-conclusão-e-insights)

---

## 🚀 Visão Geral do AWS Step Functions

O **AWS Step Functions** é um serviço de orquestração visual serverless que permite conectar múltiplos serviços da AWS (como AWS Lambda, Amazon SQS, DynamoDB, Amazon SNS e EventBridge) em fluxos de trabalho automatizados e auditáveis.

### Por que utilizar?
* **Desacoplamento:** Separa a lógica de negócios e orquestração do código individual das funções Lambda.
* **Resiliência:** Gerenciamento de estado nativo com suporte a *retries* (tentativas automáticas) e *catchers* (tratamento de exceções).
* **Visibilidade:** Painel gráfico em tempo real para monitorar a execução dos fluxos de trabalho e auditar o histórico de cada transição.

---

## 🛠️ Principais Conceitos Abordados

Durante o laboratório, foram explorados os seguintes estados fundamentais da **Amazon States Language (ASL)**:

* **Task State:** Executa uma unidade de trabalho (ex: invocar uma função AWS Lambda).
* **Choice State:** Toma decisões condicionais no fluxo com base na entrada/saída de dados anteriores.
* **Parallel State:** Executa ramificações concorrentes em paralelo.
* **Map State:** Executa um conjunto de etapas para cada item de uma lista/array.
* **Wait State:** Pausa a execução por um período determinado de tempo ou até uma data/hora específica.
* **Pass State:** Passa seus dados de entrada para a saída, útil para transformações simples ou mocks em testes.
* **Fail / Succeed State:** Encerra a execução explicitamente com status de falha ou sucesso.

---

## ⚙️ Estrutura do Workflow (State Machine)

Abaixo está uma representação em JSON (Amazon States Language - ASL) de um fluxo orquestrado focado em validação condicional e processamento serverless:

```json
{
  "Comment": "Workflow de Exemplo - Processamento Serverless",
  "StartAt": "ProcessarDados",
  "States": {
    "ProcessarDados": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:us-east-1:123456789012:function:MinhaFuncaoLambda",
      "Next": "VerificarSucesso"
    },
    "VerificarSucesso": {
      "Type": "Choice",
      "Choices": [
        {
          "Variable": "$.statusCode",
          "NumericEquals": 200,
          "Next": "Sucesso"
        }
      ],
      "Default": "Falha"
    },
    "Sucesso": {
      "Type": "Succeed"
    },
    "Falha": {
      "Type": "Fail",
      "Error": "ProcessamentoFalhou",
      "Cause": "O status da resposta não foi 200 OK."
    }
  }
}
```

---

## 🛡️ Tratamento de Erros e Retentativas

Um dos maiores benefícios do Step Functions é a resiliência na manipulação de exceções. Exemplo de implementação de `Retry` e `Catch`:

```json
"TaskComTratamentoDeErro": {
  "Type": "Task",
  "Resource": "arn:aws:lambda:us-east-1:123456789012:function:MinhaFuncaoLambda",
  "Retry": [
    {
      "ErrorEquals": ["States.ALL"],
      "IntervalSeconds": 2,
      "MaxAttempts": 3,
      "BackoffRate": 2.0
    }
  ],
  "Catch": [
    {
      "ErrorEquals": ["States.ALL"],
      "Next": "WorkflowDeTratamentoDeErro"
    }
  ],
  "End": true
}
```

---

## 📁 Estrutura do Repositório

```text
├── images/             # Capturas de tela do Workflow e da execução no console AWS
│   ├── workflow-design.png
│   └── execution-success.png
├── state-machine.json  # Definição ASL do workflow desenvolvido
└── README.md           # Documentação do projeto
```

---

## 💡 Conclusão e Insights

* **Manutenibilidade:** Utilizar Step Functions reduz a necessidade de "código cola" dentro das AWS Lambdas, deixando cada função focada em uma única responsabilidade.
* **Economia de Custo:** Com o modelo Serverless, você paga apenas pelo número de transições de estado executadas.
* **Monitoramento Simplificado:** Identificar gargalos em fluxos distribuídos torna-se visual e direto pelo console da AWS.

---

Feito com 💻 durante a formação na [DIO](https://www.dio.me/).
