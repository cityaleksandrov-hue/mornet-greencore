# Core Services

Основные (core) микросервисы MorNet GreenCore, которые являются **sector-agnostic** (не зависят от конкретного сектора экономики).

## Описание

Core Services предоставляют базовую функциональность платформы, которая используется всеми секторами (residential, tourism, agriculture, manufacturing, municipal).

## Сервисы

### 1. Identity Service
**Назначение:** Управление пользователями, аутентификация, авторизация (RBAC)

**Технологии:** NestJS, PostgreSQL, JWT, OAuth 2.0

**Основные функции:**
- SSO (Single Sign-On)
- Управление пользователями и ролями
- RBAC (Role-Based Access Control)
- OAuth 2.0 / OpenID Connect

---

### 2. Wallet Service
**Назначение:** Управление кошельками (custodial и non-custodial)

**Технологии:** NestJS, PostgreSQL, ethers.js, HSM

**Основные функции:**
- Создание и управление кошельками
- Custodial wallets (управляемые платформой)
- Non-custodial wallets (управляемые пользователем)
- Интеграция с HSM для безопасного хранения ключей

---

### 3. Token Engine
**Назначение:** Обработка телеметрии, расчёт E_netWh, подготовка к токенизации

**Технологии:** NestJS, TimescaleDB, RabbitMQ, MQTT

**Основные функции:**
- Приём телеметрии (E, H, W, AGR, SRV, PRD, LOG, PUB, CRB)
- Расчёт net-значений (E_netWh, H_netWh, W_netWh, etc.)
- Интервалы (15 мин, 1 час, 1 день)
- Подготовка данных для минта

---

### 4. Cost Engine ⭐ NEW!
**Назначение:** Расчёт стоимости ресурсов и конвертация в GCM

**Технологии:** NestJS, PostgreSQL, Redis

**Основные функции:**
- Расчёт стоимости на основе тарифов
- Конвертация в GCM (индекс-токен)
- Поддержка sector-specific тарифов через Sector Plugins
- Кэширование тарифов

**Формула:**
```
Cost_GCM = Σ (Resource_i × Tariff_i)
```

---

### 5. Oracle Service
**Назначение:** Блокчейн адаптер, подпись интервалов, связь off-chain ↔ on-chain

**Технологии:** NestJS, ethers.js, HSM

**Основные функции:**
- Подпись интервалов телеметрии
- Отправка транзакций в блокчейн
- Индексация блокчейн событий
- Верификация on-chain данных

---

### 6. DAO Treasury
**Назначение:** Управление минтом, распределением и резервами токенов

**Технологии:** NestJS, PostgreSQL, ethers.js

**Основные функции:**
- Mint токенов (E-Token, GCM)
- Управление резервами
- Распределение токенов (rewards, staking)
- Governance (DAO голосования)

---

### 7. Analytics Service
**Назначение:** KPI, ESG/MRV отчёты, аналитика

**Технологии:** NestJS, TimescaleDB, Redis

**Основные функции:**
- Расчёт KPI (энергоэффективность, выбросы CO2)
- ESG отчёты (Environmental, Social, Governance)
- MRV (Measurement, Reporting, Verification)
- Дашборды и визуализация

---

### 8. API Gateway
**Назначение:** Единая точка входа для всех API запросов

**Технологии:** NestJS, Fastify, Redis

**Основные функции:**
- REST / gRPC API
- Аутентификация и авторизация
- Rate limiting
- Request validation (Zod schemas)
- Routing к микросервисам

---

## Взаимодействие

```mermaid
graph TD
    A[API Gateway] --> B[Identity Service]
    A --> C[Wallet Service]
    A --> D[Token Engine]
    A --> E[Cost Engine]
    A --> F[Oracle Service]
    A --> G[DAO Treasury]
    A --> H[Analytics Service]
    
    D --> E
    E --> F
    F --> G
    
    B -.auth.-> A
    C -.wallets.-> G
    D -.telemetry.-> H
```

## Разработка

### Запуск всех Core Services:
```bash
pnpm --filter "@mornet/core/*" dev
```

### Запуск конкретного сервиса:
```bash
pnpm --filter @mornet/core/identity-service dev
pnpm --filter @mornet/core/cost-engine dev
```

### Тестирование:
```bash
pnpm --filter "@mornet/core/*" test
```

## Переменные окружения

Каждый сервис имеет свой `.env.example`. Общие переменные:

```env
# Database
DATABASE_URL=postgresql://user:password@localhost:5432/mornet

# Redis
REDIS_URL=redis://localhost:6379

# RabbitMQ
RABBITMQ_URL=amqp://localhost:5672

# JWT
JWT_SECRET=your-secret-key
JWT_EXPIRES_IN=1d
```

## Архитектура

Core Services следуют **Domain-Driven Design (DDD)** и **Clean Architecture**:

```
service/
├── src/
│   ├── domain/           # Бизнес-логика, entities, value objects
│   ├── application/      # Use cases, DTOs
│   ├── infrastructure/   # Database, messaging, external APIs
│   └── presentation/     # Controllers, GraphQL resolvers
├── test/
├── package.json
└── README.md
```

## Зависимости

Core Services зависят от:
- `@mornet/shared` - общие утилиты и типы
- `@mornet/api-schemas` - OpenAPI схемы, Zod валидация
- `@mornet/config` - общие конфигурации

## Масштабирование

Core Services **sector-agnostic**, поэтому:
- ✅ Работают с любым сектором (residential, tourism, agriculture, etc.)
- ✅ Используют Sector Plugins для sector-specific логики
- ✅ Горизонтально масштабируются (Kubernetes)

---

**Часть монорепозитория MorNet GreenCore**

