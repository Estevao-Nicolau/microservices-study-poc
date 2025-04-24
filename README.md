# FIAP‑Store – Migração do Monólito para Microsserviços 🛒

> Repositório‐exemplo que documenta a estratégia de refatoração de um monólito hexagonal para uma arquitetura baseada em microsserviços usando **Saga Orquestrada**.

[![Build](https://img.shields.io/github/actions/workflow/status/seu‑usuario/fiap‑store/ci.yml?label=build)](../../actions)
[![License](https://img.shields.io/github/license/seu‑usuario/fiap‑store)](/LICENSE)

---
<p align="center">
  <img src="https://github.com/user-attachments/assets/1571b025-10ac-4030-937b-af350c34a361" 
       alt="Monólito FIAP Store – visão atual">
</p>

## ✨ Visão Geral

O monólito original (figura) concentra todos os domínios de negócio num único deploy, conectado a uma base MySQL e a diversos adaptadores externos (pagamentos, logística, notificações). 
As principais dores identificadas foram:

| Problema | Impacto |
| -------- | ------- |
| Escalabilidade horizontal limitada | É preciso escalar a aplicação inteira para suportar pico de um módulo. |
| Lentidão no ciclo de deploy | Pequenas mudanças exigem rebuild e reteste de tudo. |
| Acoplamento forte entre domínios | Alterações em um módulo impactam vários outros. |
| Confiabilidade reduzida | Falha em um adaptador externo derruba todo o checkout. |
| Evolução tecnológica engessada | Não é possível adotar bancos NoSQL ou linguagens diferentes por domínio. |

> **Objetivo**: decompor o monólito em microsserviços independentes, orquestrados por Saga, melhorando escalabilidade, deploy contínuo e resiliência.

---

## 🗺️ Arquitetura Proposta

| Serviço | Responsabilidade | Banco de Dados |
| ------- | ---------------- | -------------- |
| **Order‑Service** | Criar pedido e expor endpoints REST. | MongoDB |
| **Orchestrator‑Service** | Orquestrar a Saga e coordenar compensações. | — |
| **Product‑Validation‑Service** | Validar existência/validade de produtos. | PostgreSQL |
| **Payment‑Service** | Processar pagamento via gateway. | PostgreSQL |
| **Inventory‑Service** | Dar baixa/rollback de estoque. | PostgreSQL |


### Fluxo de Eventos (simplificado)

1. `Order‑Service` publica **OrderCreated**  
2. `Orchestrator` → `Product‑Validation‑Service`  
3. Produto válido? Se sim, → `Payment‑Service`; senão, cancela pedido  
4. Pagamento ok → `Inventory‑Service`; falhou → compensa pagamento  
5. Estoque debitado → `OrderCompleted`; erro → compensa estoque & cancela

---

## 🚀 Benefícios

* **Escala independente** por domínio  
* **Deploy contínuo** sem travar equipes  
* **Polyglot persistence** conforme a necessidade  
* **Observabilidade granular** (tracing, métricas)  
* **Caminho para novos domínios** como Loyalty‑Service, Shipping‑Service

---

## 🔧 Stack

| Camada | Tecnologia |
| ------ | ---------- |
| Orquestração de eventos | Kafka (ou RabbitMQ) |
| Runtime | Kubernetes + Helm |
| Segurança | JWT (edge) & mTLS (interservice) |
| Observabilidade | Prometheus, Grafana, Jaeger |
| CI/CD | GitHub Actions |

---

## 📂 Estrutura do Repositório






Prova de conceito 100 % educativa que demonstra, passo a passo, como desenhar e subir uma arquitetura de microsserviços moderna em Java 17 + Spring Boot 3, com comunicação assíncrona via Apache Kafka, persistência combinada em PostgreSQL e MongoDB, orquestração em Docker Compose e observabilidade básica. Ideal para estudantes e profissionais que desejam aprender, clonar, experimentar e evoluir seus conhecimentos em microsserviços.
