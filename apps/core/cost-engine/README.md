# Cost Engine

**Тип:** Core Service (NestJS + GraphQL)  
**Назначение:** Расчёт себестоимости ресурсов (E/H/W/AGR/SRV/PRD/LOG/PUB/CRB)

## Описание

Cost Engine - это ключевой микросервис MorNet GreenCore, отвечающий за расчёт себестоимости потребления ресурсов на основе телеметрии от Edge-агентов и тарифных данных.

## Основные функции

### 1. Сбор тарифных данных
- Тарифы на электроэнергию (E)
- Тарифы на отопление (H)
- Тарифы на воду (W)
- Тарифы на сельскохозяйственные ресурсы (AGR)
- Тарифы на услуги (SRV)
- Тарифы на производственные ресурсы (PRD)
- Тарифы на логистику (LOG)
- Тарифы на коммунальные услуги (PUB)
- Тарифы на углеродные кредиты (CRB)

### 2. Расчёт себестоимости
- Получение телеметрии от Oracle Service
- Применение тарифов
- Расчёт себестоимости по формулам
- Учёт временных зон (день/ночь)
- Учёт сезонности

### 3. Интеграция
- **Входящие данные:** Oracle Service (телеметрия)
- **Исходящие данные:** Token Engine (для токенизации)

## Технологический стек

- **Backend:** NestJS, TypeScript
- **API:** GraphQL Federation
- **Database:** PostgreSQL (тарифы, история расчётов)
- **Message Queue:** RabbitMQ (для асинхронной обработки)

## Формулы расчёта

### Электроэнергия (E)
```
Cost_E = ΔE × Tariff_E × K_time
где:
- ΔE - потребление электроэнергии (кВт·ч)
- Tariff_E - тариф на электроэнергию (₽/кВт·ч)
- K_time - коэффициент времени суток (1.0 день, 0.7 ночь)
```

### Отопление (H)
```
Cost_H = ΔH × Tariff_H × K_season
где:
- ΔH - потребление газа/тепла (м³ или Гкал)
- Tariff_H - тариф на отопление (₽/м³ или ₽/Гкал)
- K_season - коэффициент сезонности (1.0 зима, 0.5 лето)
```

### Вода (W)
```
Cost_W = ΔW × (Tariff_cold + Tariff_hot × K_hot)
где:
- ΔW - потребление воды (м³)
- Tariff_cold - тариф на холодную воду (₽/м³)
- Tariff_hot - тариф на горячую воду (₽/м³)
- K_hot - доля горячей воды (0.0-1.0)
```

## GraphQL Schema

```graphql
type Cost {
  id: ID!
  nodeId: String!
  slotId: String!
  timestamp: DateTime!
  resource: ResourceType!
  consumption: Float!
  tariff: Float!
  cost: Float!
  currency: String!
}

enum ResourceType {
  E   # Электроэнергия
  H   # Отопление
  W   # Вода
  AGR # Сельское хозяйство
  SRV # Услуги
  PRD # Производство
  LOG # Логистика
  PUB # Коммунальные услуги
  CRB # Углеродные кредиты
}

type Query {
  getCost(nodeId: String!, slotId: String!): Cost
  getCosts(nodeId: String!, from: DateTime!, to: DateTime!): [Cost!]!
  getTariff(resource: ResourceType!, region: String!): Tariff
}

type Mutation {
  calculateCost(input: CalculateCostInput!): Cost!
  updateTariff(input: UpdateTariffInput!): Tariff!
}

input CalculateCostInput {
  nodeId: String!
  slotId: String!
  resource: ResourceType!
  consumption: Float!
  timestamp: DateTime!
}

input UpdateTariffInput {
  resource: ResourceType!
  region: String!
  value: Float!
  currency: String!
  validFrom: DateTime!
}
```

## Database Schema

```sql
-- Таблица тарифов
CREATE TABLE tariffs (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  resource VARCHAR(10) NOT NULL,
  region VARCHAR(100) NOT NULL,
  value DECIMAL(10, 4) NOT NULL,
  currency VARCHAR(3) NOT NULL DEFAULT 'RUB',
  valid_from TIMESTAMP NOT NULL,
  valid_to TIMESTAMP,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW(),
  UNIQUE(resource, region, valid_from)
);

-- Таблица расчётов себестоимости
CREATE TABLE costs (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  node_id VARCHAR(100) NOT NULL,
  slot_id VARCHAR(100) NOT NULL,
  timestamp TIMESTAMP NOT NULL,
  resource VARCHAR(10) NOT NULL,
  consumption DECIMAL(10, 4) NOT NULL,
  tariff DECIMAL(10, 4) NOT NULL,
  cost DECIMAL(10, 2) NOT NULL,
  currency VARCHAR(3) NOT NULL DEFAULT 'RUB',
  created_at TIMESTAMP DEFAULT NOW(),
  UNIQUE(node_id, slot_id, resource)
);

-- Индексы
CREATE INDEX idx_costs_node_id ON costs(node_id);
CREATE INDEX idx_costs_timestamp ON costs(timestamp);
CREATE INDEX idx_tariffs_resource_region ON tariffs(resource, region);
```

## API Endpoints

### REST API (для внутренних сервисов)

```
POST   /api/v1/costs/calculate    # Рассчитать себестоимость
GET    /api/v1/costs/:nodeId      # Получить расчёты для узла
GET    /api/v1/tariffs/:resource  # Получить тариф
PUT    /api/v1/tariffs/:id        # Обновить тариф
```

### GraphQL API (для клиентов)

```
query GetCost($nodeId: String!, $slotId: String!) {
  getCost(nodeId: $nodeId, slotId: $slotId) {
    id
    resource
    consumption
    tariff
    cost
    currency
  }
}

mutation CalculateCost($input: CalculateCostInput!) {
  calculateCost(input: $input) {
    id
    cost
    currency
  }
}
```

## Пример использования

### Расчёт себестоимости для SmartHome Node 01, Slot S5

**Входные данные:**
```json
{
  "nodeId": "SmartHome_Node01",
  "slotId": "S5",
  "timestamp": "2025-10-16T20:28:00Z",
  "resources": [
    {
      "type": "E",
      "consumption": 2.90
    },
    {
      "type": "H",
      "consumption": 2.437
    },
    {
      "type": "W",
      "consumption": 0.09
    }
  ]
}
```

**Выходные данные:**
```json
{
  "nodeId": "SmartHome_Node01",
  "slotId": "S5",
  "costs": [
    {
      "resource": "E",
      "consumption": 2.90,
      "tariff": 5.69,
      "cost": 16.50,
      "currency": "RUB"
    },
    {
      "resource": "H",
      "consumption": 2.437,
      "tariff": 5.99,
      "cost": 14.60,
      "currency": "RUB"
    },
    {
      "resource": "W",
      "consumption": 0.09,
      "tariff": 40.00,
      "cost": 3.60,
      "currency": "RUB"
    }
  ],
  "totalCost": 34.70,
  "currency": "RUB"
}
```

## Зависимости

- **Oracle Service** - источник телеметрии
- **Token Engine** - потребитель расчётов для токенизации
- **PostgreSQL** - хранение тарифов и расчётов
- **RabbitMQ** - асинхронная обработка

## Развёртывание

```bash
# Development
pnpm install
pnpm dev

# Production
pnpm build
pnpm start

# Docker
docker build -t mornet/cost-engine .
docker run -p 3004:3004 mornet/cost-engine
```

## Переменные окружения

```env
PORT=3004
DATABASE_URL=postgresql://user:password@localhost:5432/mornet_cost
RABBITMQ_URL=amqp://localhost:5672
ORACLE_SERVICE_URL=http://localhost:3003
TOKEN_ENGINE_URL=http://localhost:3005
```

## Статус

- **Версия:** 0.1.0 (MVP)
- **Статус:** 🟡 В разработке
- **Готовность:** 0%

## Roadmap

### MVP 1.0 (Q1 2026)
- [x] Архитектурный дизайн
- [ ] Базовая реализация (E/H/W)
- [ ] Интеграция с Oracle Service
- [ ] Интеграция с Token Engine
- [ ] Unit тесты

### v1.1 (Q2 2026)
- [ ] Поддержка Tourism сектора (SRV)
- [ ] Динамические тарифы
- [ ] Кэширование расчётов

### v1.2 (Q3 2026)
- [ ] Поддержка Agriculture сектора (AGR)
- [ ] Прогнозирование расходов
- [ ] ML-модели для оптимизации

---

**Документация:** [Pre-Development Architecture](../../../docs/architecture/pre-development-architecture.md)  
**Команда:** Backend Team  
**Контакт:** backend@mornet.io

