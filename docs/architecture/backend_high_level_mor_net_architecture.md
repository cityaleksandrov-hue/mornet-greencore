# MorNet / GreenCore — Backend High-Level Architecture (Дополнительное описание)

**Версия:** 1.0  
**Дата:** 15.10.2025  
**Тип:** Приложение к архитектурному документу v1.4.0  

---

## 1. Цель документа

Настоящее описание предназначено для уточнения внутренней логики и структуры **Core Backend** платформы **MorNet / GreenCore**, как дополнение к пункту §3 базового архитектурного протокола.  

Фокус — на **высокоуровневой архитектуре**, взаимодействии микросервисов, их ролях и обмене данными. Вопросы безопасности, CI/CD, DevOps и комплаенса здесь не рассматриваются.

---

## 2. Общая структура Backend-уровня

Backend состоит из четырёх основных слоёв:

1. **API Layer** — единая точка входа для фронтендов и внешних систем.  
2. **Application Layer** — ядро бизнес-логики, реализованное через микросервисы NestJS.  
3. **Messaging Layer** — слой обмена событиями между сервисами и телеметрией.  
4. **Data Layer** — хранение данных, журналов, токенов и блокчейна.  

---

## 3. Схема взаимодействия компонентов

```mermaid
flowchart TB
    subgraph API Layer
        GW[API Gateway (REST/gRPC)\nAuth / Validation / Routing]
        DOCS[OpenAPI / Zod Schemas]
    end

    subgraph Application Layer (NestJS)
        ID[Identity / SSO]
        WL[Wallet Service]
        TE[Token Engine]
        OR[Oracle Service]
        TR[Treasury / DAO]
        EX[Exchange Router]
        BK[Bank / CBDC Gateway]
        GC[GC-Guard Adapter]
        AN[Analytics / Reports]
    end

    subgraph Messaging Layer
        MQ[RabbitMQ\nDomain Events]
        MT[MQTT\nTelemetry Bus]
    end

    subgraph Data Layer
        PG[(PostgreSQL + TimescaleDB)]
        RS[(Redis Cache)]
        WD[(WORM Storage / Audit Logs)]
        BC[(Blockchain EVM / ERC-1155/20 Indexer)]
    end

    GW --> ID & WL & TE & OR & TR & EX & BK & GC & AN
    ID --> PG
    WL --> PG
    TE --> MQ & MT
    OR --> BC
    TR --> PG
    EX --> MQ
    BK --> PG
    GC --> WD
    AN --> RS & PG
    MQ --> TR & EX & AN
    MT --> TE
```

---

## 4. Назначение и логика каждого слоя

| Слой | Назначение | Инструменты / технологии | Примечания |
|------|-------------|---------------------------|-------------|
| **API Layer** | Обеспечивает REST/gRPC-доступ, маршрутизацию запросов, аутентификацию и rate-limiting. | NestJS Gateway, Fastify, OpenAPI, Zod | Поддерживает документацию и валидацию схем. |
| **Application Layer** | Основная бизнес-логика: учёт энергии, кошельки, токенизация, DAO, обменник, банк-шлюз, аналитика. | NestJS (CQRS/EventBus), TypeORM | Микросервисы независимы, но связаны событиями и API. |
| **Messaging Layer** | Асинхронный обмен доменными событиями и телеметрией. | RabbitMQ, MQTT | RabbitMQ — системные события; MQTT — IoT-телеметрия от станций. |
| **Data Layer** | Хранилище данных, кэшей, журналов, токенов, индексов и блокчейна. | PostgreSQL + TimescaleDB, Redis, WORM, EVM Indexer | Поддержка исторических данных и MRV-аудита. |

---

## 5. Сервисы Application Layer

| Модуль | Роль | Основные API | Примечания |
|---------|------|--------------|-------------|
| **Identity / SSO** | Пользователи, роли, OAuth2, JWT, Keycloak-интеграция | `/auth/*` | Единая авторизация между фронтами. |
| **Wallet Service** | Учёт кошельков, транзакций, ключей | `/wallet/*` | Поддержка custodial/non-custodial режимов. |
| **Token Engine** | Расчёт E_netWh, создание токенов | `/telemetry/intervals` | Интеграция с Edge и Oracle. |
| **Oracle Service** | Подпись данных, анти-дублирование, верификация | `/oracle/sign` | Привязка к блокчейну. |
| **DAO Treasury** | Минт и распределение токенов, резервы, отчёты | `/treasury/*` | Центр управления внутренними токенами. |
| **Exchange Router** | Обменные операции (E↔GCM↔GCC), котировки | `/exchange/*` | Интеграция с Quote Engine и Bank Gateway. |
| **Bank / CBDC Gateway** | Взаимодействие с банками и цифровыми валютами | `/bank/*` | ISO 20022 API, эскроу, статусы. |
| **GC-Guard Adapter** | KYC/KYB/AML, комплаенс и аудит | `/gcguard/*` | Взаимодействие с правовыми реестрами. |
| **Analytics / Reports** | Отчётность, ESG, MRV, KPI | `/analytics/*` | Форматы PDF/CSV, метрики ESG. |

---

## 6. Потоки взаимодействия (кратко)

1. **Electrostation → Token Engine** — телеметрия (MQTT / REST).  
2. **Token Engine → Oracle** — расчёт и подпись eNetWh.  
3. **Oracle → DAO Treasury** — минт токенов.  
4. **Treasury ↔ Exchange Router** — обмен и ликвидность.  
5. **Exchange ↔ Bank Gateway** — эскроу, платёжные статусы.  
6. **GC-Guard → Treasury / Bank** — аудит, AML, MRV.  
7. **Analytics** — сбор и агрегация всех событий.  

---

## 7. Принципы масштабирования и изоляции

- **Микросервисная модель:** каждый сервис можно развернуть независимо.  
- **Асинхронная связность:** все критические события проходят через RabbitMQ (Domain Bus).  
- **Edge-ориентированность:** обработка телеметрии частично переносится на Edge Agent.  
- **Шардирование БД:** PostgreSQL / TimescaleDB распределены по доменам (energy, wallet, treasury).  
- **Резерв и аудит:** все критичные события дублируются в WORM-хранилище.  

---

## 8. Связь с другими слоями системы

- **Frontend:** подключение через API Gateway, авторизация через SSO.  
- **Blockchain:** Oracle Service подписывает данные, DAO Treasury минтит и хранит индексы токенов.  
- **Bank:** Bank Gateway взаимодействует с внешними API ISO 20022.  
- **GC-Guard:** обеспечивает проверку транзакций, MRV и ESG-комплаенс.  

---

## 9. Перспективное развитие Backend (Post-MVP)

| Направление | Описание | План реализации |
|--------------|-----------|-----------------|
| **Event Sourcing / CQRS** | Внедрение механизма событийных потоков и разделения команд/запросов | Q2 2026 |
| **GraphQL Gateway** | Альтернатива REST для аналитических интерфейсов | Q3 2026 |
| **Horizontal Sharding** | Автоматическое распределение нагрузки между узлами | Q3 2026 |
| **Serverless Analytics Layer** | Переход аналитики на функции (Lambda / FaaS) | Q4 2026 |
| **AI-Assisted GC-Guard** | Автоматизация аудита и выявления аномалий | Q1 2027 |

---

**Итог:** Данный документ фиксирует структуру и взаимосвязи backend-компонентов, служит приложением для технических команд, участвующих в проектировании и развитии ядра MorNet / GreenCore.