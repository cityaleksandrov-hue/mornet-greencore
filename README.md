# MorfNet GreenCore

**Морфогенетическая сеть для экосистемы GreenCore**

Прозрачная экономическая платформа для токенизации энергии и других ресурсов, обеспечивающая учёт, обмен и аудит всех процессов от сбора телеметрии до финансовых операций.

---

## 🎯 Цель проекта

Создание ядра морфогенетической сети (MorfNet) для экосистемы GreenCore, которое обеспечивает:

- **Учёт ресурсов** - сбор и обработка телеметрии (электричество, газ, вода) в реальном времени
- **Токенизация** - выпуск E-Token (электричество), H-Token (газ/тепло), W-Token (вода) и индекс-токена GCM (ERC-20)
- **Казначейство** - управление минтом, распределением и резервами токенов через DAO Treasury
- **Обмен** - базовые торговые пары (E/H/W ↔ GCM, GCM ↔ фиат)
- **Банковская интеграция** - шлюз для работы с банками и CBDC
- **Комплаенс** - KYC/AML через GC-Guard, аудит и MRV-отчёты
- **Прозрачность** - публичные и административные интерфейсы

---

## 🏗️ Архитектура

Проект построен как **монорепозиторий** с использованием современного технологического стека:

### Frontend (Гибридный подход)
- **Admin UI** - Vue 3 + Naive UI (административная панель)
- **Public UI** - React + Next.js (публичный портал)

### Backend - Core Services (8 микросервисов)
- **Identity Service** - SSO, пользователи, роли (RBAC)
- **Wallet Service** - управление кошельками (custodial/non-custodial)
- **Cost Engine** - расчёт себестоимости ресурсов с учётом тарифов и секторных особенностей
- **Token Engine** - токенизация ресурсов (E/H/W → GCM)
- **Oracle Service** - блокчейн адаптер, подпись данных для смарт-контрактов
- **DAO Treasury** - минт, распределение, резервы токенов
- **Analytics Service** - KPI, ESG/MRV, отчёты и дашборды
- **API Gateway** - единая точка входа, REST/GraphQL, аутентификация

### Backend - Integrations (3 микросервиса)
- **Bank Gateway** - интеграция с банками и CBDC
- **Exchange Router** - обменник, торговые пары
- **GCGuard Adapter** - KYC/AML, политики, аудит

### Data Layer
- **PostgreSQL + TimescaleDB** - временные ряды телеметрии, профили потребления
- **Redis** - кэширование и очереди
- **WORM-хранилище** - неизменяемые аудиторские логи

### Messaging
- **MQTT** - телеметрия от IoT устройств (Raspberry Pi, SmartHome узлы)
- **RabbitMQ** - доменные события между сервисами

### Blockchain
- **Smart Contracts** - ERC-1155 (E/H/W токены), ERC-20 (GCM индекс)
- **Oracle Service** - связь между off-chain и on-chain
- **Indexer** - индексация блокчейн событий

### Edge / IoT
- **Edge Agent** - сбор данных с IoT устройств (Raspberry Pi 4, MQTT)
- **SmartHome Node** - мониторинг домохозяйств (газ, электричество, вода)

---

## 📁 Структура проекта

```
mornet-greencore/
├── apps/                       # Приложения
│   ├── core/                  # Core Services (sector-agnostic)
│   │   ├── identity-service/  # Identity/SSO сервис
│   │   ├── wallet-service/    # Wallet сервис
│   │   ├── cost-engine/       # Cost Engine (расчёт себестоимости)
│   │   ├── token-engine/      # Token Engine (токенизация)
│   │   ├── oracle-service/    # Oracle Service
│   │   ├── dao-treasury/      # DAO Treasury
│   │   ├── analytics-service/ # Analytics Service
│   │   └── api-gateway/       # API Gateway
│   ├── integrations/          # External integrations
│   │   ├── bank-gateway/      # Bank Gateway
│   │   ├── exchange-router/   # Exchange Router
│   │   └── gcguard-adapter/   # GC-Guard Adapter
│   ├── frontends/             # Frontend apps
│   │   ├── admin-ui/          # Admin панель (Vue 3)
│   │   └── public-ui/         # Публичный портал (React)
│   └── iot/                   # IoT devices
│       └── edge-agent/        # Edge Agent (Raspberry Pi)
├── packages/                   # Общие библиотеки
│   ├── shared/                # Общие утилиты и типы
│   ├── ui-kit/                # UI компоненты
│   ├── smart-contracts/       # Smart Contracts (Solidity)
│   ├── api-schemas/           # OpenAPI схемы, Zod валидация
│   ├── config/                # Общие конфигурации
│   └── sector-plugins/        # Sector-specific plugins
│       ├── residential/       # Residential sector (MVP 1.0)
│       ├── tourism/           # Tourism sector (MVP 2.0)
│       ├── agriculture/       # Agriculture sector (MVP 3.0)
│       ├── manufacturing/     # Manufacturing sector (MVP 4.0)
│       └── municipal/         # Municipal sector (MVP 5.0)
├── infra/                      # Инфраструктура
│   ├── docker/                # Docker Compose
│   ├── kubernetes/            # Kubernetes манифесты
│   ├── helm/                  # Helm charts
│   └── terraform/             # Terraform конфигурации
├── docs/                       # Документация
│   ├── architecture/          # Архитектурная документация
│   ├── analysis/              # Аналитические отчёты
│   ├── api/                   # API документация
│   └── guides/                # Руководства
├── logs/                       # Логи систем (не в Git, кроме .md)
│   ├── smarthome-node01/      # Логи SmartHome Node 01
│   ├── edge-agent/            # Логи Raspberry Pi
│   └── backend-services/      # Логи микросервисов
├── package.json               # Root package.json
├── pnpm-workspace.yaml        # PNPM workspace
├── turbo.json                 # Turborepo конфигурация
└── README.md                  # Этот файл
```

---

## 🛠️ Технологический стек

### Monorepo Management
- **Turborepo** - оркестрация сборки и задач
- **pnpm** - менеджер пакетов

### Frontend
- **Vue 3** - Admin UI
- **React + Next.js** - Public UI
- **Naive UI** - UI библиотека для Vue
- **Wagmi + ethers.js** - Web3 интеграция
- **Pinia / Zustand** - State management
- **Vite** - Build tool

### Backend
- **Node.js + TypeScript** - Runtime
- **NestJS** - Фреймворк для микросервисов
- **Fastify** - HTTP сервер
- **Zod** - Валидация схем
- **TypeORM / Prisma** - ORM

### Database
- **PostgreSQL** - Основная БД
- **TimescaleDB** - Временные ряды
- **Redis** - Кэш и очереди

### Messaging
- **MQTT** - IoT телеметрия
- **RabbitMQ** - Event-driven архитектура

### Blockchain
- **Hardhat** - Development environment
- **OpenZeppelin** - Smart contract библиотеки
- **ethers.js** - Ethereum библиотека

### DevOps
- **Docker** - Контейнеризация
- **Kubernetes** - Оркестрация
- **Helm** - Package manager для K8s
- **GitHub Actions** - CI/CD
- **ArgoCD** - GitOps

### Observability
- **Prometheus** - Метрики
- **Grafana** - Визуализация
- **Loki / ELK** - Логи
- **Jaeger** - Трейсинг

---

## 🚀 Быстрый старт

### Предварительные требования

- Node.js >= 18.x
- pnpm >= 8.x
- Docker >= 24.x
- PostgreSQL >= 15.x (или через Docker)

### Установка

```bash
# Клонировать репозиторий
git clone https://github.com/cityaleksandrov-hue/mornet-greencore.git
cd mornet-greencore

# Установить зависимости
pnpm install

# Скопировать .env.example в .env и настроить переменные окружения
cp .env.example .env

# Запустить инфраструктуру (PostgreSQL, Redis, RabbitMQ)
docker-compose up -d

# Запустить миграции БД
pnpm db:migrate

# Запустить все сервисы в dev режиме
pnpm dev
```

### Разработка

```bash
# Запустить конкретное приложение
pnpm --filter admin-ui dev
pnpm --filter cost-engine dev

# Запустить сборку
pnpm build

# Запустить тесты
pnpm test

# Запустить линтер
pnpm lint

# Форматирование кода
pnpm format
```

---

## 📋 MVP 1.0 Scope - Residential Sector

**Горизонт:** до 31 января 2026  
**Сектор:** Домохозяйства (Residential)  
**Цель:** 100+ домохозяйств, $10K MRR

### Функциональность MVP 1.0

#### 1. Сбор телеметрии
- **Ресурсы:** Электричество (кВт·ч), Газ (м³), Вода (м³)
- **Источник данных:** SmartHome Node (Raspberry Pi 4 + MQTT)
- **Частота:** Real-time (каждую минуту)
- **Устройства:** Wi-Fi розетки (Tapo P110), газовые счётчики (BK-G4), водосчётчики

#### 2. Расчёт себестоимости (Cost Engine)
- **Тарифы ЖКХ:** Газ (6 ₽/м³), Электричество (5.7 ₽/кВт·ч), Вода (40 ₽/м³)
- **Residential Plugin:** Учёт домашних паттернов потребления
- **Слоты:** Разбивка дня на временные интервалы с анализом активностей

#### 3. Токенизация (Token Engine)
- **E-Token** (ERC-1155) - токен электричества
- **H-Token** (ERC-1155) - токен газа/тепла
- **W-Token** (ERC-1155) - токен воды
- **GCM** (ERC-20) - индекс-токен (Green Carbon Money)
- **Формула:** GCM = Σ(E_cost + H_cost + W_cost)

#### 4. DAO Treasury
- Минт токенов E/H/W на основе реальной телеметрии
- Управление резервами GCM
- Распределение токенов между участниками

#### 5. Обменник (Exchange Router)
- Базовые пары: E↔GCM, H↔GCM, W↔GCM, GCM↔₽
- WebSocket тикеры для real-time обновлений

#### 6. Банковская интеграция (Bank Gateway)
- Интеграция с 1 российским банком (Тинькофф или Модульбанк)
- Fiat deposits/withdrawals (₽)
- 1:1 backing (1 GCM = 1 ₽)

#### 7. Комплаенс (GCGuard Adapter)
- KYC/AML через банк-партнёр
- Политики минта и резервов
- Аудиторские логи (WORM)

#### 8. UI
- **Admin UI (Vue 3):** Дашборд телеметрии, управление узлами, токены, казначейство
- **Public UI (React):** Кабинет домовладельца, статистика потребления, баланс токенов

#### 9. Реальные данные
- **SmartHome Node 01** - первый тестовый узел
- **Телеметрия:** Реальные данные потребления (газ, электричество, вода)
- **Паттерны:** Утренний пик, вечерний пик, базовая нагрузка
- **Инсайты:** Рекомендации по оптимизации потребления

---

## 🔐 Безопасность и комплаенс

- **TLS/mTLS** - шифрование всех соединений
- **СКЗИ/ГОСТ** - криптография (где применимо)
- **HSM** - аппаратное хранение ключей
- **KYC/AML** - верификация пользователей
- **WORM-логи** - неизменяемые аудиторские записи
- **MRV** - Measurement, Reporting, Verification

Соответствие стандартам:
- ФЗ-115 (AML)
- ФЗ-187 (Криптография)
- ISO 27001 (Information Security)
- FATF (Financial Action Task Force)

---

## 🗺️ Roadmap

| Фаза | Дата | Сектор | Токены | Цель |
|------|------|--------|--------|------|
| **MVP 1.0** | Q1 2026 | Residential | E, H, W | 100+ домов, $10K MRR |
| **MVP 2.0** | Q2 2026 | + Tourism | + SRV | 10+ отелей, $20K MRR |
| **MVP 3.0** | Q3 2026 | + Agriculture | + AGR | Фермы |
| **MVP 4.0** | Q4 2026 | + Manufacturing | + IND | Заводы |
| **MVP 5.0** | Q1 2027 | + Municipal | + MUN | Муниципалитеты |
| **v2.0** | Q2 2027 | Asset-backed GCC | - | Листинг на биржах, $1M+ TVL |

Детальный roadmap: [Strategy C Architecture](./docs/architecture/strategy-c-hybrid-architecture.md)

---

## 📚 Документация

- [Архитектура](./docs/architecture/) - Подробная архитектурная документация
- [Аналитика](./docs/analysis/) - Аналитические отчёты и сводки о работе
- [API](./docs/api/) - API документация для всех сервисов
- [Руководства](./docs/guides/) - Руководства для разработчиков

---

## 🤝 Участие в разработке

### Процесс разработки

1. Создайте feature branch от `main`
2. Внесите изменения
3. Убедитесь, что все тесты проходят (`pnpm test`)
4. Убедитесь, что код отформатирован (`pnpm format`)
5. Создайте Pull Request

### Соглашения

- **Commits** - следуем [Conventional Commits](https://www.conventionalcommits.org/)
- **Branches** - `feature/`, `bugfix/`, `hotfix/`
- **Code Style** - ESLint + Prettier
- **TypeScript** - строгий режим

---

## 📄 Лицензия

Proprietary - все права защищены MorNet GreenCore Team

---

## 📞 Контакты

- **Email:** dev@mornet-greencore.io
- **GitHub:** [cityaleksandrov-hue/mornet-greencore](https://github.com/cityaleksandrov-hue/mornet-greencore)

---

**Дата последнего обновления:** 16 октября 2025

