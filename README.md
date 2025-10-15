# MorNet GreenCore

**Морфогенетическая сеть для экосистемы GreenCore**

Прозрачная экономическая платформа для токенизации энергии и других ресурсов, обеспечивающая учёт, обмен и аудит всех процессов от сбора телеметрии до финансовых операций.

---

## 🎯 Цель проекта

Создание ядра морфогенетической сети (MorNet) для экосистемы GreenCore, которое обеспечивает:

- **Учёт энергии** - сбор и обработка телеметрии (kWh) с интервалами 15 минут
- **Токенизация** - выпуск E-Token (ERC-1155) и внутреннего индекс-токена GCM (ERC-20)
- **Казначейство** - управление минтом, распределением и резервами токенов через DAO Treasury
- **Обмен** - базовые торговые пары (E↔GCM, GCM↔фиат)
- **Банковская интеграция** - шлюз для работы с банками и CBDC
- **Комплаенс** - KYC/AML через GC-Guard, аудит и MRV-отчёты
- **Прозрачность** - публичные и административные интерфейсы

---

## 🏗️ Архитектура

Проект построен как **монорепозиторий** с использованием современного технологического стека:

### Frontend (Гибридный подход)
- **Admin UI** - Vue 3 + Naive UI (административная панель)
- **Public UI** - React + Next.js (публичный портал)

### Backend (Микросервисы)
- **Identity Service** - SSO, пользователи, роли (RBAC)
- **Wallet Service** - управление кошельками (custodial/non-custodial)
- **Token Engine** - обработка телеметрии, расчёт E_netWh
- **Oracle Service** - блокчейн адаптер, подпись интервалов
- **DAO Treasury** - минт, распределение, резервы токенов
- **Exchange Router** - обменник, торговые пары
- **Bank Gateway** - интеграция с банками и CBDC
- **GC-Guard Adapter** - KYC/AML, политики, аудит
- **Analytics Service** - KPI, ESG/MRV, отчёты
- **API Gateway** - REST/gRPC, аутентификация, валидация

### Data Layer
- **PostgreSQL + TimescaleDB** - профили энергии (временные ряды)
- **Redis** - кэширование
- **WORM-хранилище** - аудиторские логи

### Messaging
- **MQTT** - телеметрия от IoT устройств
- **RabbitMQ** - доменные события между сервисами

### Blockchain
- **Smart Contracts** - ERC-1155 (E-Token), ERC-20 (GCM)
- **Oracle Service** - связь между off-chain и on-chain
- **Indexer** - индексация блокчейн событий

### Edge
- **Edge Agent** - сбор данных с SCADA/PLC через Modbus RTU/TCP

---

## 📁 Структура проекта

```
mornet-greencore/
├── apps/                       # Приложения
│   ├── admin-ui/              # Admin панель (Vue 3)
│   ├── public-ui/             # Публичный портал (React)
│   ├── identity-service/      # Identity/SSO сервис
│   ├── wallet-service/        # Wallet сервис
│   ├── token-engine/          # Token Engine
│   ├── oracle-service/        # Oracle Service
│   ├── dao-treasury/          # DAO Treasury
│   ├── exchange-router/       # Exchange Router
│   ├── bank-gateway/          # Bank Gateway
│   ├── gcguard-adapter/       # GC-Guard Adapter
│   ├── analytics-service/     # Analytics Service
│   ├── api-gateway/           # API Gateway
│   └── edge-agent/            # Edge Agent
├── packages/                   # Общие библиотеки
│   ├── shared/                # Общие утилиты и типы
│   ├── ui-kit/                # UI компоненты
│   ├── smart-contracts/       # Smart Contracts (Solidity)
│   ├── api-schemas/           # OpenAPI схемы, Zod валидация
│   └── config/                # Общие конфигурации
├── infra/                      # Инфраструктура
│   ├── docker/                # Docker Compose
│   ├── kubernetes/            # Kubernetes манифесты
│   ├── helm/                  # Helm charts
│   └── terraform/             # Terraform конфигурации
├── docs/                       # Документация
│   ├── architecture/          # Архитектурная документация
│   ├── api/                   # API документация
│   └── guides/                # Руководства
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
pnpm --filter identity-service dev

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

## 📋 MVP Scope (kWh-only)

**Горизонт:** до января 2026

### Функциональность MVP

1. **Сбор телеметрии**
   - Только kWh с интервалами 15 минут
   - Виртуальные и реальные источники (Electrostation Node)
   - Modbus TCP/RTU поддержка

2. **Расчёт энергии**
   - E_netWh = приоритет boundary meter
   - Fallback: gen - aux - loss × k_safety

3. **Токенизация**
   - Mint E-Token (ERC-1155 batch per interval)
   - GCM как внутренний индекс-токен (ERC-20)
   - Учёт в DAO Treasury

4. **Обменник**
   - Базовые пары: E↔GCM, GCM↔фиат
   - WebSocket тикеры (1/3/5/7 сек)

5. **Банковская интеграция**
   - REST API / ISO-20022
   - Sandbox → Production

6. **Комплаенс**
   - KYC/KYB/AML через GC-Guard
   - Политики минта и резервов
   - MRV-отчёты

7. **UI**
   - Admin App: станции, токены, казначейство, GC-Guard
   - Public App: кабинет инвестора, витрины, DAO

8. **Demo/Live Switch**
   - Переключение виртуальной/реальной телеметрии

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

## 📚 Документация

- [Архитектура](./docs/architecture/) - Подробная архитектурная документация
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

**Дата последнего обновления:** 15 октября 2025

