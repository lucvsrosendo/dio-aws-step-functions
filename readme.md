# Orquestração de Workflows Serverless com AWS Step Functions

Este repositório contém a documentação prática e os aprendizados adquiridos durante o laboratório de **AWS Step Functions** pela [Digital Innovation One (DIO)](https://www.dio.me/).

O objetivo principal deste projeto é consolidar conceitos sobre orquestração de serviços, estados de execução, tratamento de erros e integração serverless na AWS.

---

## 📌 Sumário
- [Visão Geral do AWS Step Functions](#-visão-geral-do-aws-step-functions)
- [Principais Conceitos Abordados](#-principais-conceitos-abordados)
- [Estrutura do Workflow (State Machine)](#-estrutura-do-workflow-state-machine)
- [Tratamento de Erros e Retentativas](#-tratamento-de-erros-e-retentativas)
- [Como Replicar este Projeto](#-como-replicar-este-projeto)
- [Conclusão e Insights](#-conclusão-e-insights)

---

## 🚀 Visão Geral do AWS Step Functions

O **AWS Step Functions** é um serviço de orquestração visual serverless que permite conectar múltiplos serviços da AWS (como AWS Lambda, Amazon SQS, DynamoDB e SNS) em fluxos de trabalho automatizados e auditáveis.

### Por que utilizar?
* **Desacoplamento:** Separa a lógica de negócios e orquestração do código individual das funções Lambda.
* **Resiliência:** Gerenciamento de estado nativo com suporte a *retries* (tentativas automáticas) e *catchers* (tratamento de exceções).
* **Visibilidade:** Painel gráfico em tempo real para monitorar execuções de fluxos de trabalho.

---

## 🛠️ Principais Conceitos Abordados

 Durante o laboratório, foram explorados os seguintes estados fundamentais da Amazon States Language (ASL):

* **Task State:** Executa uma unidade de trabalho (ex: invocar uma função AWS Lambda).
* **Choice State:** Toma decisões condicionais no fluxo com base na entrada/saída de dados anteriores.
* **Parallel State:** Executa ramificações concorrentes em paralelo.
* **Map State:** Executa um conjunto de etapas para cada item de uma lista/array.
* **Wait State:** Pausa a execução por um período determinado de tempo ou até uma data/hora específica.
* **Pass State:** Passa seus dados de entrada para a saída, útil para transformações simples ou testes.
* **Fail / Succeed State:** Encerra a execução explicitamente com falha ou sucesso.

---

## ⚙️ Estrutura do Workflow (State Machine)

Abaixo está uma representação simplificada em JSON (Amazon States Language - ASL) de um fluxo orquestrado básico com validação condicional:

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
