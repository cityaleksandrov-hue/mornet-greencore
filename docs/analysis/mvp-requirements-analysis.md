# MVP Requirements Analysis: Бизнес-логика vs Production-Ready

**Дата:** 16 октября 2025  
**Версия:** 1.0  
**Цель:** Выделить ТОЛЬКО бизнес-логику для MVP 1.0, исключив production-ready требования

---

## 📋 Методология

### Принципы выделения MVP требований:

1. **Бизнес-логика (MVP):**
   - Функции, которые доказывают ценность продукта
   - Минимум для работы core функционала
   - То, что пользователь видит и с чем взаимодействует

2. **Production-Ready (НЕ MVP):**
   - Сложная авторизация (OAuth, SSO)
   - Продвинутая безопасность (WAF, DDoS protection)
   - Масштабирование (load balancing, auto-scaling)
   - Мониторинг (Prometheus, Grafana, Jaeger)
   - Compliance (полный KYC/AML, audit trails)

---

## 🎯 MVP 1.0: Core Бизнес-логика

### Scope: Residential Sector (Домохозяйства)

**Цель MVP:** Доказать, что мы можем:
1. Собирать телеметрию с домашних устройств
2. Рассчитывать себестоимость потребления (E/H/W)
3. Токенизировать потребление (E/H/W tokens)
4. Позволить пользователям покупать/продавать токены за GCC
5. Конвертировать GCC ↔ фиат (через банк)

---

## 📊 Анализ требований по микросервисам

### 1. Edge Agent (Raspberry Pi)

#### ✅ MVP (Бизнес-логика):

**Функция:** Сбор телеметрии с Wi-Fi розеток

**Требования:**
- Подключение к Wi-Fi розеткам (Tapo P110, Sonoff POW R3)
- Чтение данных (мощность, энергия, напряжение, ток)
- Публикация в MQTT (каждую минуту)
- Локальное хранение (SQLite) на случай потери связи

**Технологии:**
- Python 3.11
- MQTT client (paho-mqtt)
- SQLite
- Tapo/Sonoff API

**Объём работы:** 2-3 недели (1 разработчик)

#### ❌ НЕ MVP (Production-Ready):

- ❌ OTA updates (over-the-air обновления)
- ❌ Remote management (удалённое управление агентом)
- ❌ Failover (автоматическое переключение на резервный брокер)
- ❌ Encryption at rest (шифрование локальной БД)
- ❌ Health checks & monitoring (метрики агента)

---

### 2. MQTT Gateway (MorNet Layer)

#### ✅ MVP (Бизнес-логика):

**Функция:** Приём телеметрии от Edge Agents, нормализация, валидация

**Требования:**
- Подписка на MQTT topics (mornet/+/telemetry)
- Парсинг JSON payload
- Базовая валидация (schema validation)
- Нормализация данных (приведение к единому формату)
- Публикация в RabbitMQ (для Cost Engine)

**Технологии:**
- NestJS
- MQTT client
- RabbitMQ client
- Zod (schema validation)

**Объём работы:** 2 недели (1 разработчик)

#### ❌ НЕ MVP (Production-Ready):

- ❌ TLS/SSL для MQTT (шифрование)
- ❌ MQTT authentication (username/password, certificates)
- ❌ Rate limiting (защита от флуда)
- ❌ Dead letter queue (обработка ошибок)
- ❌ Distributed tracing (Jaeger)

---

### 3. Oracle Service

#### ✅ MVP (Бизнес-логика):

**Функция:** Получение тарифов ЖКХ (электричество, вода, газ)

**Требования:**
- Хранение тарифов в БД (PostgreSQL)
- API для получения тарифов (GET /tariffs/:region)
- Ручное обновление тарифов (через Admin UI)

**Технологии:**
- NestJS
- PostgreSQL
- GraphQL

**Объём работы:** 1 неделя (1 разработчик)

#### ❌ НЕ MVP (Production-Ready):

- ❌ Автоматическое обновление тарифов (парсинг сайтов ЖКХ)
- ❌ Интеграция с внешними API (курсы валют, индексы)
- ❌ Кэширование (Redis)
- ❌ Версионирование тарифов (история изменений)

---

### 4. Cost Engine

#### ✅ MVP (Бизнес-логика):

**Функция:** Расчёт себестоимости потребления (E/H/W)

**Требования:**
- Residential Plugin (расчёт для домохозяйств)
- Формулы:
  - `cost_e = energy_kwh × tariff_e`
  - `cost_h = heat_kwh × tariff_h`
  - `cost_w = water_m3 × tariff_w`
  - `cost_total = cost_e + cost_h + cost_w`
- API для расчёта (POST /cost/calculate)
- Хранение расчётов в БД

**Технологии:**
- NestJS
- PostgreSQL
- GraphQL

**Объём работы:** 2-3 недели (1 разработчик)

#### ❌ НЕ MVP (Production-Ready):

- ❌ Сложные формулы (износ оборудования, труд, логистика)
- ❌ Tourism/Agro/Manufacturing Plugins
- ❌ Batch processing (массовый расчёт)
- ❌ Оптимизация (кэширование, индексы)
- ❌ Audit trail (логирование всех расчётов)

---

### 5. Token Engine

#### ✅ MVP (Бизнес-логика):

**Функция:** Токенизация потребления (E/H/W tokens, GCM, GCC)

**Требования:**
- E/H/W Token Module (эмиссия utility tokens)
- GCM Index Token (расчёт индекса)
  - `GCM = cost_total / GCM_rate`
- GCC Coin (эмиссия/сжигание)
  - Mint: при fiat deposit
  - Burn: при fiat withdrawal
- API для токенизации (POST /token/mint, POST /token/burn)
- Интеграция с блокчейном (Polygon Mumbai testnet)

**Технологии:**
- NestJS
- Hardhat (smart contracts)
- Ethers.js
- PostgreSQL

**Объём работы:** 3-4 недели (1-2 разработчика)

#### ❌ НЕ MVP (Production-Ready):

- ❌ Сложная токеномика (staking, rewards, governance)
- ❌ Multi-chain (Ethereum, BSC, Arbitrum)
- ❌ Upgradeable contracts (proxy pattern)
- ❌ Formal verification (аудит контрактов)
- ❌ Gas optimization (минимизация комиссий)

---

### 6. DAO Treasury

#### ✅ MVP (Бизнес-логика):

**Функция:** Хранение токенов, базовое governance

**Требования:**
- Multi-sig wallet (2-of-3 или 3-of-5)
- Хранение GCC, E/H/W tokens
- Базовое governance (голосование за предложения)
- API для treasury операций

**Технологии:**
- NestJS
- Hardhat (smart contracts)
- Ethers.js
- PostgreSQL

**Объём работы:** 2-3 недели (1 разработчик)

#### ❌ НЕ MVP (Production-Ready):

- ❌ Сложное governance (квадратичное голосование, делегирование)
- ❌ Timelock (задержка выполнения решений)
- ❌ Treasury diversification (инвестиции в DeFi)
- ❌ Automated market maker (AMM для токенов)

---

### 7. Wallet Service

#### ✅ MVP (Бизнес-логика):

**Функция:** Управление кошельками пользователей

**Требования:**
- Создание кошелька (при регистрации)
- Хранение приватных ключей (зашифрованно)
- Баланс токенов (E/H/W, GCM, GCC)
- Отправка/получение токенов
- История транзакций

**Технологии:**
- NestJS
- Ethers.js
- PostgreSQL (зашифрованные ключи)

**Объём работы:** 2 недели (1 разработчик)

#### ❌ НЕ MVP (Production-Ready):

- ❌ Hardware wallet support (Ledger, Trezor)
- ❌ Multi-sig wallets для пользователей
- ❌ Wallet recovery (seed phrase)
- ❌ Integration с MetaMask, WalletConnect
- ❌ Cold storage (офлайн хранение)

---

### 8. Bank Gateway

#### ✅ MVP (Бизнес-логика):

**Функция:** Fiat on/off-ramp (₽ ↔ GCC)

**Требования:**
- Интеграция с 1 банком (Тинькофф или Модульбанк)
- Fiat deposit:
  - Создание виртуального счёта (через API банка)
  - Получение webhook от банка (payment.received)
  - Эмиссия GCC (через Token Engine)
- Fiat withdrawal:
  - Сжигание GCC (через Token Engine)
  - Отправка фиата (через API банка)
- Escrow account (резервирование средств)

**Технологии:**
- NestJS
- Bank API (REST)
- PostgreSQL

**Объём работы:** 3-4 недели (1 разработчик)

#### ❌ НЕ MVP (Production-Ready):

- ❌ Интеграция с несколькими банками
- ❌ Поддержка $/€
- ❌ Автоматический KYC/AML (через банк)
- ❌ Fraud detection (мониторинг подозрительных транзакций)
- ❌ Compliance reporting (автоматические отчёты)

---

### 9. Identity Service

#### ✅ MVP (Бизнес-логика):

**Функция:** Регистрация, логин, базовая авторизация

**Требования:**
- Регистрация (email + password)
- Логин (JWT tokens)
- Базовая авторизация (RBAC: user, admin)
- Профиль пользователя

**Технологии:**
- NestJS
- JWT
- PostgreSQL
- bcrypt (хеширование паролей)

**Объём работы:** 1-2 недели (1 разработчик)

#### ❌ НЕ MVP (Production-Ready):

- ❌ OAuth (Google, Facebook, GitHub)
- ❌ SSO (Single Sign-On)
- ❌ 2FA (двухфакторная аутентификация)
- ❌ KYC/AML (полная верификация)
- ❌ Session management (управление сессиями)

---

### 10. API Gateway

#### ✅ MVP (Бизнес-логика):

**Функция:** Единая точка входа, роутинг запросов

**Требования:**
- GraphQL Federation (объединение микросервисов)
- Базовая авторизация (JWT validation)
- Роутинг к микросервисам

**Технологии:**
- NestJS
- Apollo Federation
- JWT

**Объём работы:** 1-2 недели (1 разработчик)

#### ❌ НЕ MVP (Production-Ready):

- ❌ Rate limiting (защита от DDoS)
- ❌ API versioning (v1, v2)
- ❌ Request/response logging
- ❌ Caching (Redis)
- ❌ Load balancing

---

### 11. Analytics Service

#### ✅ MVP (Бизнес-логика):

**Функция:** Базовые метрики и дашборды

**Требования:**
- Метрики:
  - Количество пользователей
  - Количество домохозяйств
  - Объём потребления (E/H/W)
  - GCC в обращении
  - Транзакции (fiat deposits/withdrawals)
- Простой дашборд (Admin UI)

**Технологии:**
- NestJS
- PostgreSQL (агрегация данных)
- GraphQL

**Объём работы:** 1-2 недели (1 разработчик)

#### ❌ НЕ MVP (Production-Ready):

- ❌ Real-time analytics (WebSocket)
- ❌ Advanced BI (Tableau, Power BI)
- ❌ Predictive analytics (ML модели)
- ❌ Custom reports (пользовательские отчёты)
- ❌ Data export (CSV, Excel)

---

### 12. Admin UI

#### ✅ MVP (Бизнес-логика):

**Функция:** Управление системой (для администраторов)

**Требования:**
- Логин (admin)
- Управление пользователями (список, блокировка)
- Управление домохозяйствами (список, статус)
- Управление тарифами (добавление, редактирование)
- Дашборд (метрики)
- Логи (просмотр последних событий)

**Технологии:**
- Vue 3 + Vite
- Pinia (state management)
- TailwindCSS
- GraphQL (Apollo Client)

**Объём работы:** 3-4 недели (1-2 разработчика)

#### ❌ НЕ MVP (Production-Ready):

- ❌ Advanced admin panel (роли, права доступа)
- ❌ Audit trail (история всех действий админа)
- ❌ Bulk operations (массовые операции)
- ❌ Custom dashboards (настраиваемые дашборды)
- ❌ Notifications (уведомления админу)

---

### 13. Public UI

#### ✅ MVP (Бизнес-логика):

**Функция:** Интерфейс для пользователей (домовладельцев)

**Требования:**
- Регистрация / Логин
- Профиль (редактирование)
- Подключение домохозяйства (добавление Edge Agent)
- Дашборд:
  - Текущее потребление (E/H/W)
  - Баланс токенов (E/H/W, GCM, GCC)
  - История транзакций
- Fiat deposit/withdrawal (через Bank Gateway)
- Покупка/продажа токенов (E/H/W ↔ GCC)

**Технологии:**
- Next.js 14 (React)
- TypeScript
- TailwindCSS
- GraphQL (Apollo Client)

**Объём работы:** 4-5 недель (2 разработчика)

#### ❌ НЕ MVP (Production-Ready):

- ❌ Mobile app (iOS, Android)
- ❌ PWA (Progressive Web App)
- ❌ Real-time updates (WebSocket)
- ❌ Advanced charts (interactive, zoomable)
- ❌ Notifications (push, email, SMS)
- ❌ Multi-language (i18n)

---

## 📊 Сводная таблица: MVP vs Production-Ready

| Микросервис | MVP (недели) | Production-Ready (недели) | Разница |
|-------------|--------------|---------------------------|---------|
| Edge Agent | 2-3 | 6-8 | 2-3x |
| MQTT Gateway | 2 | 4-6 | 2-3x |
| Oracle Service | 1 | 3-4 | 3-4x |
| Cost Engine | 2-3 | 6-8 | 2-3x |
| Token Engine | 3-4 | 8-12 | 2-3x |
| DAO Treasury | 2-3 | 6-8 | 2-3x |
| Wallet Service | 2 | 5-6 | 2-3x |
| Bank Gateway | 3-4 | 8-10 | 2-3x |
| Identity Service | 1-2 | 4-6 | 3-4x |
| API Gateway | 1-2 | 4-5 | 2-3x |
| Analytics Service | 1-2 | 4-6 | 3-4x |
| Admin UI | 3-4 | 8-10 | 2-3x |
| Public UI | 4-5 | 10-12 | 2-3x |
| **ИТОГО** | **28-39 недель** | **76-101 неделя** | **2.7-2.6x** |

**С учётом параллельной разработки:**
- **MVP:** 12-16 недель (3-4 месяца) при команде 8-10 разработчиков
- **Production-Ready:** 30-40 недель (7-10 месяцев) при команде 12-18 разработчиков

---

## 🎯 MVP 1.0 Roadmap (детально)

### Команда (8-10 человек):

- **Backend:** 4-5 разработчиков
- **Frontend:** 2-3 разработчика
- **Blockchain:** 1 разработчик
- **DevOps:** 1 инженер

### Фазы разработки:

#### Фаза 1: Foundation (Недели 1-4)

**Backend:**
- Identity Service (1-2 недели)
- API Gateway (1-2 недели)
- Wallet Service (2 недели)
- Oracle Service (1 неделя)

**Frontend:**
- Настройка проектов (Admin UI, Public UI)
- Базовая авторизация (логин/регистрация)

**DevOps:**
- Docker Compose (локальная разработка)
- GitLab CI/CD (базовые pipelines)

**Blockchain:**
- Smart contracts (E/H/W tokens, GCM, GCC)
- Деплой на Mumbai testnet

---

#### Фаза 2: Core Logic (Недели 5-8)

**Backend:**
- MQTT Gateway (2 недели)
- Cost Engine (2-3 недели)
- Token Engine (3-4 недели)

**Frontend:**
- Admin UI: дашборд, управление тарифами
- Public UI: дашборд, баланс токенов

**IoT:**
- Edge Agent (2-3 недели)

---

#### Фаза 3: Banking & Treasury (Недели 9-12)

**Backend:**
- Bank Gateway (3-4 недели)
- DAO Treasury (2-3 недели)
- Analytics Service (1-2 недели)

**Frontend:**
- Admin UI: управление пользователями, логи
- Public UI: fiat deposit/withdrawal, покупка/продажа токенов

---

#### Фаза 4: Integration & Testing (Недели 13-16)

**Backend:**
- Интеграция всех сервисов
- Unit tests
- Integration tests

**Frontend:**
- E2E tests (Cypress)
- UI/UX полировка

**DevOps:**
- Staging environment (Kubernetes)
- Мониторинг (базовый: logs, errors)

**Пилот:**
- 10 домохозяйств
- Сбор обратной связи

---

### Конец Q1 2026: 🚀 Запуск MVP 1.0

**Target:**
- 100+ домохозяйств
- $10K MRR
- 1,000+ GCC в обращении

---

## 📋 Критерии готовности MVP 1.0

### ✅ Функциональные требования:

1. **Сбор телеметрии:**
   - ✅ Edge Agent собирает данные с Wi-Fi розеток
   - ✅ MQTT Gateway принимает и нормализует данные

2. **Расчёт себестоимости:**
   - ✅ Cost Engine рассчитывает cost_e, cost_h, cost_w, cost_total
   - ✅ Oracle Service предоставляет тарифы

3. **Токенизация:**
   - ✅ Token Engine эмитирует E/H/W tokens
   - ✅ Token Engine рассчитывает GCM
   - ✅ Token Engine эмитирует/сжигает GCC

4. **Fiat on/off-ramp:**
   - ✅ Bank Gateway интегрирован с 1 банком
   - ✅ Fiat deposit работает (₽ → GCC)
   - ✅ Fiat withdrawal работает (GCC → ₽)

5. **UI:**
   - ✅ Admin UI: управление системой
   - ✅ Public UI: дашборд, баланс, транзакции

6. **Blockchain:**
   - ✅ Smart contracts задеплоены на Mumbai testnet
   - ✅ Wallet Service интегрирован с контрактами

### ❌ НЕ требуется для MVP:

- ❌ OAuth, SSO, 2FA
- ❌ KYC/AML (полная верификация)
- ❌ Rate limiting, DDoS protection
- ❌ Prometheus, Grafana, Jaeger
- ❌ Load balancing, auto-scaling
- ❌ Multi-chain, upgradeable contracts
- ❌ Mobile app, PWA
- ❌ Real-time updates (WebSocket)
- ❌ Advanced BI, ML models

---

## 💡 Ключевые инсайты

### 1. MVP = 40% функционала, 80% ценности

**MVP фокусируется на core бизнес-логике:**
- Сбор данных → Расчёт → Токенизация → Обмен → Fiat

**Production-Ready добавляет:**
- Безопасность, масштабируемость, мониторинг, compliance

**Но для доказательства концепции (proof of concept) это НЕ критично.**

---

### 2. Упрощённая авторизация = -50% времени

**MVP:**
- Email + password
- JWT tokens
- Базовый RBAC (user, admin)

**Production-Ready:**
- OAuth, SSO
- 2FA
- KYC/AML
- Session management

**Экономия:** 3-4 недели на Identity Service

---

### 3. Один банк = -60% времени

**MVP:**
- Интеграция с 1 банком (Тинькофф или Модульбанк)
- Только ₽

**Production-Ready:**
- 3+ банка
- $/€
- Автоматический KYC/AML
- Fraud detection

**Экономия:** 5-6 недель на Bank Gateway

---

### 4. Testnet = -80% времени

**MVP:**
- Polygon Mumbai testnet
- Простые контракты (без upgrades)

**Production-Ready:**
- Polygon Mainnet
- Multi-chain (Ethereum, BSC, Arbitrum)
- Upgradeable contracts
- Formal verification, аудит

**Экономия:** 6-8 недель на Blockchain

---

### 5. Базовый UI = -50% времени

**MVP:**
- Простой дизайн (TailwindCSS)
- Базовые компоненты
- Только web (desktop + mobile responsive)

**Production-Ready:**
- Продвинутый дизайн (custom UI kit)
- Анимации, transitions
- Mobile app (iOS, Android)
- PWA

**Экономия:** 6-8 недель на Frontend

---

## 📊 Итоговая оценка

### MVP 1.0:

- **Время:** 12-16 недель (3-4 месяца)
- **Команда:** 8-10 человек
- **Бюджет:** $200-250K
- **Функционал:** 40% от production-ready
- **Ценность:** 80% для доказательства концепции

### Production-Ready:

- **Время:** 30-40 недель (7-10 месяцев)
- **Команда:** 12-18 человек
- **Бюджет:** $500-700K
- **Функционал:** 100%
- **Ценность:** 100% для массового запуска

### Разница:

- **Время:** 2.5-3x быстрее
- **Бюджет:** 2-3x дешевле
- **Риск:** Ниже (ранняя валидация)

---

## 🎯 Рекомендация

**Фокус на MVP 1.0 (Q1 2026):**

1. ✅ Реализовать ТОЛЬКО бизнес-логику
2. ✅ Исключить production-ready требования
3. ✅ Запустить пилот (10 домохозяйств)
4. ✅ Собрать обратную связь
5. ✅ Валидировать концепцию

**После успешного MVP:**

6. Добавить production-ready требования (Q2 2026)
7. Масштабировать на 100+ домохозяйств
8. Запустить MVP 2.0 (Tourism) (Q2 2026)

**Преимущества:**
- Быстрый time-to-market
- Низкий риск
- Ранняя валидация
- Гибкость (можем pivot)

---

**Дата:** 16 октября 2025  
**Версия:** 1.0  
**Автор:** AI Assistant  
**Статус:** ✅ Утверждено

---

## 🔗 Связанные документы

- [Project Checkpoint](./2025-10-16_project-checkpoint.md)
- [Strategy C Architecture](../architecture/strategy-c-hybrid-architecture.md)
- [Pre-Development Architecture Document](../architecture/pre-development-architecture.md) (следующий)

