# Рефакторинг структуры проекта для Стратегии C

**Дата:** 16 октября 2025  
**Версия:** 1.0.0  
**Статус:** ✅ Завершён

---

## 📋 Обзор

Этот документ описывает рефакторинг структуры монорепозитория MorNet GreenCore для поддержки **Стратегии C (Гибридная)** - фазированного подхода к масштабированию от домохозяйств к универсальной платформе токенизации физических активов.

---

## 🎯 Цели рефакторинга

### 1. Модульность
- Разделение приложений на категории (core, integrations, frontends, iot)
- Создание sector-specific plugins для разных секторов экономики

### 2. Масштабируемость
- Подготовка архитектуры к добавлению новых секторов (Tourism, Agriculture, Manufacturing, Municipal)
- Возможность независимой разработки и деплоя компонентов

### 3. Ясность
- Понятная структура для новых разработчиков
- Чёткое разделение ответственности между компонентами

---

## 🔄 Изменения

### До рефакторинга

```
apps/
├── admin-ui/
├── public-ui/
├── identity-service/
├── wallet-service/
├── token-engine/
├── oracle-service/
├── dao-treasury/
├── exchange-router/
├── bank-gateway/
├── gcguard-adapter/
├── analytics-service/
├── api-gateway/
└── edge-agent/

packages/
├── shared/
├── ui-kit/
├── smart-contracts/
├── api-schemas/
└── config/
```

**Проблемы:**
- Все приложения в одной папке (13 приложений)
- Непонятно, что core, что integration
- Нет поддержки sector-specific логики
- Отсутствует Cost Engine (критический сервис)

### После рефакторинга

```
apps/
├── core/                  # Core Services (sector-agnostic)
│   ├── identity-service/
│   ├── wallet-service/
│   ├── token-engine/
│   ├── cost-engine/       ← NEW!
│   ├── oracle-service/
│   ├── dao-treasury/
│   ├── analytics-service/
│   └── api-gateway/
├── integrations/          # External integrations
│   ├── bank-gateway/
│   ├── exchange-router/
│   └── gcguard-adapter/
├── frontends/             # Frontend apps
│   ├── admin-ui/
│   └── public-ui/
└── iot/                   # IoT devices
    └── edge-agent/

packages/
├── shared/
├── ui-kit/
├── smart-contracts/
├── api-schemas/
├── config/
└── sector-plugins/        ← NEW!
    ├── residential/       # MVP 1.0 (Q1 2026)
    ├── tourism/           # MVP 2.0 (Q2 2026)
    ├── agriculture/       # MVP 3.0 (Q3 2026)
    ├── manufacturing/     # MVP 4.0 (Q4 2026)
    └── municipal/         # MVP 5.0 (Q1 2027)
```

**Преимущества:**
- ✅ Чёткое разделение по категориям
- ✅ Понятно, что core, что integration
- ✅ Поддержка sector-specific логики
- ✅ Cost Engine добавлен
- ✅ Готовность к масштабированию

---

## 📦 Новые компоненты

### 1. Cost Engine

**Путь:** `apps/core/cost-engine/`  
**Назначение:** Расчёт себестоимости ресурсов (E/H/W/AGR/SRV/PRD/LOG/PUB/CRB)  
**Технологии:** NestJS, GraphQL, PostgreSQL, RabbitMQ  
**Статус:** 🟡 В разработке (MVP 1.0)

**Ключевые функции:**
- Сбор тарифных данных
- Расчёт себестоимости по формулам
- Интеграция с Oracle Service и Token Engine

**Формулы:**
```
Cost_E = ΔE × Tariff_E × K_time
Cost_H = ΔH × Tariff_H × K_season
Cost_W = ΔW × (Tariff_cold + Tariff_hot × K_hot)
```

### 2. Sector Plugins

**Путь:** `packages/sector-plugins/`  
**Назначение:** Sector-specific логика для разных секторов экономики  
**Технологии:** TypeScript, модульная архитектура

#### 2.1 Residential Plugin (MVP 1.0)

**Сектор:** Жилая недвижимость  
**Ресурсы:** E, H, W  
**Статус:** 🟢 MVP 1.0 (Q1 2026)  
**Готовность:** 0%

**Поддерживаемые устройства:**
- Wi-Fi розетки (Tapo P110, Sonoff POW R3)
- Газовые счётчики
- Водяные счётчики
- Raspberry Pi Edge Agent

**Типичные слоты:**
- S1: 00:00–06:00 (сон)
- S2: 06:00–09:00 (утро)
- S3: 09:00–17:00 (день)
- S4: 17:00–20:00 (вечер)
- S5: 20:00–23:00 (поздний вечер)
- S6: 23:00–00:00 (подготовка ко сну)

#### 2.2 Tourism Plugin (MVP 2.0)

**Сектор:** Туризм и гостиничный бизнес  
**Ресурсы:** E, H, W, SRV  
**Статус:** 🔴 Не начато (Q2 2026)  
**Готовность:** 0%

#### 2.3 Agriculture Plugin (MVP 3.0)

**Сектор:** Сельское хозяйство  
**Ресурсы:** E, H, W, AGR, LOG  
**Статус:** 🔴 Не начато (Q3 2026)  
**Готовность:** 0%

#### 2.4 Manufacturing Plugin (MVP 4.0)

**Сектор:** Производство  
**Ресурсы:** E, H, W, PRD, CRB  
**Статус:** 🔴 Не начато (Q4 2026)  
**Готовность:** 0%

#### 2.5 Municipal Plugin (MVP 5.0)

**Сектор:** Муниципальные услуги  
**Ресурсы:** E, H, W, PUB  
**Статус:** 🔴 Не начато (Q1 2027)  
**Готовность:** 0%

---

## 🔧 Технические изменения

### 1. pnpm-workspace.yaml

**До:**
```yaml
packages:
  - 'apps/*'
  - 'packages/*'
```

**После:**
```yaml
packages:
  # Frontend Applications
  - 'apps/frontends/*'
  
  # Core Services
  - 'apps/core/*'
  
  # Integration Services
  - 'apps/integrations/*'
  
  # IoT Devices
  - 'apps/iot/*'
  
  # Shared Packages
  - 'packages/shared'
  - 'packages/ui-kit'
  - 'packages/smart-contracts'
  - 'packages/api-schemas'
  - 'packages/config'
  
  # Sector Plugins
  - 'packages/sector-plugins/*'
```

### 2. turbo.json

**Добавлены новые задачи:**
- `test:e2e` - E2E тестирование
- `deploy` - деплой приложений
- `globalEnv` - глобальные переменные окружения

### 3. README.md

**Обновлена структура проекта:**
- Добавлены категории (core, integrations, frontends, iot)
- Добавлен Cost Engine
- Добавлены Sector Plugins

---

## 📊 Категории приложений

### Core Services (sector-agnostic)

**Назначение:** Универсальные сервисы, работающие для всех секторов

| Сервис | Назначение | Технологии |
|--------|------------|------------|
| identity-service | SSO, пользователи, роли | NestJS, PostgreSQL |
| wallet-service | Управление кошельками | NestJS, PostgreSQL |
| token-engine | Токенизация ресурсов | NestJS, PostgreSQL, RabbitMQ |
| **cost-engine** | **Расчёт себестоимости** | **NestJS, PostgreSQL, RabbitMQ** |
| oracle-service | Блокчейн адаптер | NestJS, Web3.js |
| dao-treasury | Минт, распределение токенов | NestJS, Web3.js |
| analytics-service | KPI, ESG/MRV, отчёты | NestJS, TimescaleDB |
| api-gateway | REST/gRPC, аутентификация | NestJS, GraphQL Federation |

### Integrations (external services)

**Назначение:** Интеграция с внешними сервисами

| Сервис | Назначение | Технологии |
|--------|------------|------------|
| bank-gateway | Интеграция с банками | NestJS, REST API |
| exchange-router | Обменник, торговые пары | NestJS, PostgreSQL |
| gcguard-adapter | KYC/AML, политики | NestJS, REST API |

### Frontends (user interfaces)

**Назначение:** Пользовательские интерфейсы

| Приложение | Назначение | Технологии |
|------------|------------|------------|
| admin-ui | Административная панель | Vue 3, Naive UI |
| public-ui | Публичный портал | React, Next.js |

### IoT (edge devices)

**Назначение:** Сбор телеметрии с устройств

| Устройство | Назначение | Технологии |
|------------|------------|------------|
| edge-agent | Сбор данных с SCADA/PLC | Python, MQTT, Modbus |

---

## 🚀 Roadmap

### MVP 1.0 (Q1 2026) - Residential

**Scope:**
- ✅ Cost Engine (базовая реализация)
- ✅ Residential Plugin (E/H/W)
- ✅ Интеграция Cost Engine + Token Engine
- ✅ Тестирование на SmartHome Node 01

**Deliverables:**
- Cost Engine v0.1.0
- Residential Plugin v0.1.0
- 10 домохозяйств в пилоте

### MVP 2.0 (Q2 2026) - Tourism

**Scope:**
- Tourism Plugin (E/H/W/SRV)
- Расширение Cost Engine (SRV)
- Интеграция с отелями

**Deliverables:**
- Tourism Plugin v0.1.0
- 5 отелей в пилоте

### MVP 3.0 (Q3 2026) - Agriculture

**Scope:**
- Agriculture Plugin (E/H/W/AGR/LOG)
- Расширение Cost Engine (AGR/LOG)
- Интеграция с фермами

**Deliverables:**
- Agriculture Plugin v0.1.0
- 3 фермы в пилоте

### MVP 4.0 (Q4 2026) - Manufacturing

**Scope:**
- Manufacturing Plugin (E/H/W/PRD/CRB)
- Расширение Cost Engine (PRD/CRB)
- Интеграция с заводами

**Deliverables:**
- Manufacturing Plugin v0.1.0
- 2 завода в пилоте

### MVP 5.0 (Q1 2027) - Municipal

**Scope:**
- Municipal Plugin (E/H/W/PUB)
- Расширение Cost Engine (PUB)
- Интеграция с муниципалитетами

**Deliverables:**
- Municipal Plugin v0.1.0
- 1 муниципалитет в пилоте

---

## 📝 Миграционный план

### Для разработчиков

**Если вы работали с кодом до рефакторинга:**

1. **Обновите локальный репозиторий:**
   ```bash
   git pull origin main
   ```

2. **Переустановите зависимости:**
   ```bash
   pnpm install
   ```

3. **Обновите импорты:**
   - Было: `import { ... } from '@mornet/identity-service'`
   - Стало: `import { ... } from '@mornet/identity-service'` (без изменений)

4. **Обновите пути в конфигурациях:**
   - Было: `apps/identity-service`
   - Стало: `apps/core/identity-service`

### Для CI/CD

**Обновите пути в GitHub Actions:**

```yaml
# До
- name: Build Identity Service
  run: pnpm --filter identity-service build

# После
- name: Build Identity Service
  run: pnpm --filter identity-service build
```

**Примечание:** Имена пакетов не изменились, только физическое расположение в файловой системе.

---

## ✅ Checklist

- [x] Создать категории (core, integrations, frontends, iot)
- [x] Переместить сервисы в категории
- [x] Создать Cost Engine
- [x] Создать Sector Plugins (residential, tourism, agriculture, manufacturing, municipal)
- [x] Обновить pnpm-workspace.yaml
- [x] Обновить turbo.json
- [x] Обновить README.md
- [x] Создать документацию рефакторинга
- [ ] Обновить CI/CD (GitHub Actions)
- [ ] Обновить Docker Compose
- [ ] Обновить Kubernetes манифесты
- [ ] Обновить Helm charts

---

## 🔗 Связанные документы

- [Strategy C Architecture](./strategy-c-hybrid-architecture.md)
- [Pre-Development Architecture](./pre-development-architecture.md)
- [MVP Requirements Analysis](../analysis/mvp-requirements-analysis.md)
- [Project Checkpoint](../analysis/2025-10-16_project-checkpoint.md)

---

## 📊 Статистика

| Метрика | До | После | Изменение |
|---------|---:|------:|---------:|
| Приложений | 13 | 14 | +1 (Cost Engine) |
| Категорий | 1 | 4 | +3 |
| Sector Plugins | 0 | 5 | +5 |
| Строк документации | 3,000 | 5,000+ | +67% |

---

## 👥 Команда

- **Архитектор:** Backend Team
- **Дата рефакторинга:** 16 октября 2025
- **Статус:** ✅ Завершён
- **Следующий шаг:** Обновление CI/CD

---

**Версия:** 1.0.0  
**Последнее обновление:** 16 октября 2025

