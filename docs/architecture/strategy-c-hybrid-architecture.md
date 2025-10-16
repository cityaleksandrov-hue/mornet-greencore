# Архитектура Стратегии C: Гибридный подход MorNet GreenCore

**Версия:** 1.0  
**Дата:** 16 октября 2025  
**Стратегия:** C - Гибридная (Energy Hub для Web3 → Универсальная платформа)  
**Статус:** Архитектурный документ

---

## 📋 Содержание

1. [Обзор стратегии](#обзор-стратегии)
2. [Фазированный roadmap](#фазированный-roadmap)
3. [Модульная архитектура](#модульная-архитектура)
4. [Банковская интеграция](#банковская-интеграция)
5. [Технический стек](#технический-стек)
6. [Микросервисы по фазам](#микросервисы-по-фазам)
7. [Data Flow](#data-flow)
8. [Security & Compliance](#security--compliance)
9. [Deployment](#deployment)
10. [Roadmap детально](#roadmap-детально)

---

## 1. Обзор стратегии

### Концепция

**Стратегия C (Гибридная):** Начинаем как "Energy Hub для Web3" (узкий фокус), эволюционируем в "Универсальную платформу токенизации физической экономики" (широкий фокус).

### Ключевые принципы

1. **Модульная архитектура с самого начала**
   - Core (sector-agnostic)
   - Plugins (sector-specific)
   - Легко добавлять новые сектора

2. **Фазированный запуск**
   - Каждая фаза = новый сектор
   - Валидация после каждой фазы
   - Возможность pivot

3. **Быстрый time-to-market**
   - MVP за 3-4 месяца (Q1 2026)
   - Ранняя валидация на домовладельцах

4. **Достижение vision v1.5.0**
   - К Q1 2027 - полная платформа
   - 5+ секторов, 9 типов токенов

### Позиционирование по фазам

| Фаза | Дата | Позиционирование |
|------|------|------------------|
| MVP 1.0 | Q1 2026 | "Energy Hub для домов с blockchain" |
| MVP 2.0 | Q2 2026 | "Energy Hub + Hotel Management" |
| MVP 3.0 | Q3 2026 | "Energy Hub + AgTech" |
| MVP 4.0 | Q4 2026 | "Energy Hub + Industry 4.0" |
| MVP 5.0 | Q1 2027 | "Платформа токенизации физической экономики" |
| v2.0 | Q2 2027 | "Asset-backed Digital Currency Platform" |

---

## 2. Фазированный roadmap

### Фаза 1: MVP 1.0 - Residential (Q1 2026)

**Scope:**
- Сектор: Домохозяйства
- Tokens: E (электричество), H (тепло), W (вода)
- Data Sources: MQTT, Raspberry Pi, Wi-Fi розетки
- Target: 100+ домохозяйств, $10K MRR

**Deliverables:**
- Edge Agent (Raspberry Pi)
- MQTT Gateway
- Cost Engine (базовый, только ЖКХ тарифы)
- Token Engine (E/H/W module)
- DAO Treasury
- Wallet Service
- Admin UI, Public UI

---

### Фаза 2: MVP 2.0 - Tourism (Q2 2026)

**Scope:**
- + Сектор: Туризм, отели
- + Token: SRV (сервис)
- + Data Source: PMS API (Hotel Management Systems)
- Target: 10+ отелей, $20K MRR

**Deliverables:**
- PMS API Connector (новый)
- Tourism Plugin для Cost Engine
- SRV Token Module
- Hotel-specific analytics

---

### Фаза 3: MVP 3.0 - Agriculture (Q3 2026)

**Scope:**
- + Сектор: Агро, фермерство
- + Tokens: AGR (агро), LOG (логистика)
- + Data Source: Farm IoT, агро-сенсоры
- Target: 20+ ферм, $30K MRR

**Deliverables:**
- Farm IoT Gateway (новый)
- Agriculture Plugin для Cost Engine
- AGR/LOG Token Modules
- AgTech analytics

---

### Фаза 4: MVP 4.0 - Manufacturing (Q4 2026)

**Scope:**
- + Сектор: Производство, промышленность
- + Token: PRD (производство)
- + Data Sources: SCADA, OPC UA, промышленная автоматизация
- Target: 5+ заводов, $50K MRR

**Deliverables:**
- SCADA/OPC UA Adapter (новый)
- Manufacturing Plugin для Cost Engine
- PRD Token Module
- Industry 4.0 analytics

---

### Фаза 5: MVP 5.0 - Municipal (Q1 2027)

**Scope:**
- + Сектор: Муниципалитеты, Smart City
- + Token: PUB (публичные услуги)
- + Data Sources: Smart Meters, муниципальные системы
- Target: 2+ муниципалитета, $100K MRR

**Deliverables:**
- Smart Meter Gateway (новый)
- Municipal Plugin для Cost Engine
- PUB Token Module
- Smart City analytics

---

### Фаза 6: v2.0 - Asset-backed GCC (Q2 2027)

**Scope:**
- Asset Registry Service
- Physical Asset Verification
- Audit & Compliance
- GCC как asset-backed stablecoin

**Deliverables:**
- Asset Registry Service (новый)
- Audit Trail System
- Regulatory Compliance Module
- Integration: Росреестр, ЕГРЮЛ, Auditors

---

## 3. Модульная архитектура

### 3.1 Общая схема

```
┌─────────────────────────────────────────────────────────────────┐
│                    PHYSICAL WORLD                               │
│  [SmartHome] [Hotel] [Farm] [Factory] [Municipality]           │
└────────────────────────────┬────────────────────────────────────┘
                             ↓
┌─────────────────────────────────────────────────────────────────┐
│              DATA ACQUISITION LAYER (Plugin System)             │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌────────┐│
│  │  MQTT    │ │  PMS API │ │ Farm IoT │ │  SCADA/  │ │ Smart  ││
│  │ Gateway  │ │ Connector│ │ Gateway  │ │  OPC UA  │ │ Meters ││
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘ └────────┘│
└────────────────────────────┬────────────────────────────────────┘
                             ↓
┌─────────────────────────────────────────────────────────────────┐
│                    MORNET LAYER                                 │
│  - Data Normalization (приведение к единому формату)            │
│  - Encryption (шифрование)                                      │
│  - Validation (проверка целостности)                            │
│  - Routing (маршрутизация к нужным сервисам)                    │
└────────────────────────────┬────────────────────────────────────┘
                             ↓
┌─────────────────────────────────────────────────────────────────┐
│              COST ENGINE (Sector-specific Plugins)              │
│  ┌────────────┐ ┌────────────┐ ┌────────────┐ ┌──────────────┐ │
│  │Residential │ │  Tourism   │ │Agriculture │ │Manufacturing │ │
│  │   Plugin   │ │   Plugin   │ │   Plugin   │ │    Plugin    │ │
│  └────────────┘ └────────────┘ └────────────┘ └──────────────┘ │
│  - Tariff Management                                            │
│  - Depreciation Calculator                                      │
│  - Labor Cost Calculator                                        │
│  - Cost Aggregation                                             │
└────────────────────────────┬────────────────────────────────────┘
                             ↓
┌─────────────────────────────────────────────────────────────────┐
│              TOKEN ENGINE (Token Modules)                       │
│  ┌────┐ ┌────┐ ┌────┐ ┌────┐ ┌────┐ ┌────┐ ┌────┐ ┌────┐      │
│  │ E  │ │ H  │ │ W  │ │AGR │ │SRV │ │PRD │ │LOG │ │PUB │      │
│  └────┘ └────┘ └────┘ └────┘ └────┘ └────┘ └────┘ └────┘      │
│                                                                  │
│  GCM (Index Token) = Σ(Utility Tokens)                         │
│  GCC (Coin) = f(GCM, DAO_treasury, physical_assets)            │
└────────────────────────────┬────────────────────────────────────┘
                             ↓
┌─────────────────────────────────────────────────────────────────┐
│                    DAO GREENCORE                                │
│  - DAO Treasury (казначейство)                                  │
│  - Governance (голосование)                                     │
│  - GC-Guard (комплаенс, аудит)                                  │
│  - Analytics (аналитика экосистемы)                             │
└────────────────────────────┬────────────────────────────────────┘
                             ↓
┌─────────────────────────────────────────────────────────────────┐
│                 BANKING & MARKET LAYER                          │
│  ┌──────────────────┐  ┌──────────────────────────────────────┐│
│  │ Bank Gateway     │  │ Exchange Router                      ││
│  │ (Fiat On/Off)    │  │ (DEX/CEX)                           ││
│  │                  │  │                                      ││
│  │ - Partner Banks  │  │ - Uniswap, PancakeSwap (DEX)        ││
│  │ - Fiat deposits  │  │ - Binance, OKX (CEX)                ││
│  │ - Fiat withdrawals│ │ - P2P trading                       ││
│  └──────────────────┘  └──────────────────────────────────────┘│
└─────────────────────────────────────────────────────────────────┘
```

---

### 3.2 Core Services (sector-agnostic)

Эти сервисы работают для ВСЕХ секторов:

1. **MorNet Layer**
   - Нормализация данных
   - Шифрование
   - Валидация
   - Роутинг

2. **Oracle Service**
   - Получение внешних данных (тарифы, курсы валют, индексы)
   - Интеграция с внешними API

3. **Token Engine Core**
   - Базовая логика токенизации
   - GCM индекс
   - GCC эмиссия

4. **DAO Treasury**
   - Казначейство
   - Governance
   - Voting

5. **Wallet Service**
   - Управление кошельками
   - Транзакции

6. **Identity Service**
   - SSO, KYC/AML
   - Управление пользователями

7. **API Gateway**
   - Единая точка входа
   - Rate limiting, auth

8. **Analytics Service**
   - Метрики, дашборды
   - BI

---

### 3.3 Plugin System

#### Data Acquisition Plugins

**MVP (Q1 2026):**
- MQTT Gateway Plugin

**Q2 2026:**
- PMS API Connector Plugin (отели)

**Q3 2026:**
- Farm IoT Gateway Plugin (агро)

**Q4 2026:**
- SCADA/OPC UA Adapter Plugin (производство)

**Q1 2027:**
- Smart Meter Gateway Plugin (муниципалитеты)

#### Cost Engine Plugins

**MVP (Q1 2026):**
- Residential Plugin
  - ЖКХ тарифы (электричество, вода, газ)
  - Простая формула: cost = Σ(resource × tariff)

**Q2 2026:**
- Tourism Plugin
  - Загрузка номеров
  - Стоимость сервиса
  - Формула: cost = room_nights × rate + services

**Q3 2026:**
- Agriculture Plugin
  - Урожай, полив, удобрения
  - Транспортировка
  - Формула: cost = land × yield + water + fertilizer + transport

**Q4 2026:**
- Manufacturing Plugin
  - Работа станков
  - Износ оборудования
  - Сырьё
  - Формула: cost = machine_hours × rate + depreciation + materials

**Q1 2027:**
- Municipal Plugin
  - Освещение, тепло
  - Обслуживание инфраструктуры
  - Формула: cost = public_service × area × tariff

#### Token Modules

**MVP (Q1 2026):**
- E/H/W Token Module

**Q2 2026:**
- SRV Token Module

**Q3 2026:**
- AGR/LOG Token Modules

**Q4 2026:**
- PRD Token Module

**Q1 2027:**
- PUB Token Module

---

## 4. Банковская интеграция

### 4.1 Ключевой вопрос: Мы сами банк или интегрируемся с банками?

**Ответ: МЫ НЕ БАНК. МЫ ИНТЕГРИРУЕМСЯ С БАНКАМИ-ПАРТНЁРАМИ.**

### 4.2 Почему НЕ свой банк?

**Регуляторные барьеры:**
1. Банковская лицензия (ЦБ РФ)
   - Минимальный капитал: 300 млн ₽ (для небанковской кредитной организации)
   - Минимальный капитал: 1 млрд ₽ (для банка)
   - Процесс получения: 1-2 года
   - Требования к менеджменту, системам, процедурам

2. Compliance требования:
   - ФЗ-115 (AML/CFT)
   - ФЗ-161 (Национальная платёжная система)
   - Инструкция ЦБ 153-И (резервы)
   - Отчётность в ЦБ (ежедневная, еженедельная, месячная)

3. Операционные расходы:
   - Compliance офицеры
   - Аудит
   - Резервы
   - Страхование вкладов (АСВ)

**Вывод: Создание банка - это отдельный бизнес, требующий $10M+ и 2+ лет.**

---

### 4.3 Модель: Bank Gateway (интеграция с партнёрами)

**Концепция:**
MorNet не банк, а **платформа токенизации**, которая интегрируется с банками-партнёрами для fiat on/off-ramp.

**Архитектура:**

```
[User] → [MorNet Wallet] → [Bank Gateway] → [Partner Banks]
                                  ↓
                            [Fiat ↔ GCC]
```

**Функции Bank Gateway:**
1. **Fiat Deposits (ввод фиата)**
   - Пользователь переводит ₽/$/€ на счёт в банке-партнёре
   - Bank Gateway получает уведомление
   - Эмитирует GCC на кошелёк пользователя

2. **Fiat Withdrawals (вывод фиата)**
   - Пользователь сжигает GCC
   - Bank Gateway отправляет запрос в банк-партнёр
   - Банк переводит ₽/$/€ на счёт пользователя

3. **Compliance**
   - KYC/AML проверки (через банк-партнёр)
   - Мониторинг транзакций
   - Отчётность (банк делает сам)

---

### 4.4 Партнёрские банки: Кто и как?

#### Вариант 1: Российские банки (для ₽)

**Потенциальные партнёры:**
1. **Тинькофф Банк**
   - API-first банк
   - Открытое API
   - Опыт с финтехом

2. **Модульбанк**
   - Банк для бизнеса
   - API для интеграций
   - Гибкие условия

3. **Точка (Открытие)**
   - Банк для предпринимателей
   - API, webhooks

4. **Альфа-Банк**
   - Alfa API
   - Большой охват

**Модель интеграции:**
- **API Integration:** REST API банка
- **Webhooks:** Уведомления о платежах
- **Escrow счета:** Резервирование средств для GCC

**Процесс:**
1. Пользователь регистрируется в MorNet
2. Проходит KYC через банк-партнёр (API)
3. Получает виртуальный счёт в банке (API)
4. Пополняет счёт (банковский перевод)
5. Bank Gateway получает webhook от банка
6. Эмитирует GCC на кошелёк пользователя

---

#### Вариант 2: Международные банки (для $/€)

**Потенциальные партнёры:**
1. **Revolut**
   - Финтех-банк
   - API для бизнеса
   - Поддержка криптовалют

2. **Wise (TransferWise)**
   - Международные переводы
   - API
   - Низкие комиссии

3. **Payoneer**
   - Для бизнеса
   - API

**Модель интеграции:**
- Аналогично российским банкам
- Но для международных платежей

---

#### Вариант 3: Криптобанки / Fiat-to-Crypto сервисы

**Потенциальные партнёры:**
1. **Garantex**
   - Российская криптобиржа
   - Fiat-to-crypto
   - Лицензия ЦБ (оператор информационной системы)

2. **Mercuryo**
   - Fiat-to-crypto gateway
   - API для интеграции
   - Поддержка ₽/$/€

3. **Unlimint (Cardpay)**
   - Payment gateway
   - Fiat-to-crypto
   - API

**Модель интеграции:**
- Пользователь покупает GCC напрямую за ₽/$/€
- Через платёжный шлюз партнёра
- MorNet получает уведомление, эмитирует GCC

---

### 4.5 Архитектура Bank Gateway Service

```typescript
// Bank Gateway Service Architecture

interface BankGatewayService {
  // Fiat Deposits
  depositFiat(userId: string, amount: number, currency: string): Promise<Transaction>;
  
  // Fiat Withdrawals
  withdrawFiat(userId: string, amount: number, currency: string): Promise<Transaction>;
  
  // KYC/AML
  verifyUser(userId: string): Promise<KYCStatus>;
  
  // Partner Bank Integration
  connectBank(bankId: string, credentials: BankCredentials): Promise<void>;
  
  // Webhooks from banks
  handleBankWebhook(bankId: string, payload: WebhookPayload): Promise<void>;
}

// Supported Banks
enum BankPartner {
  TINKOFF = 'tinkoff',
  MODULBANK = 'modulbank',
  TOCHKA = 'tochka',
  ALFABANK = 'alfabank',
  REVOLUT = 'revolut',
  WISE = 'wise',
  GARANTEX = 'garantex',
  MERCURYO = 'mercuryo'
}

// Fiat Deposit Flow
async function depositFiat(userId: string, amount: number, currency: string) {
  // 1. User initiates deposit in MorNet UI
  const depositRequest = await createDepositRequest(userId, amount, currency);
  
  // 2. Generate virtual account or payment link via bank API
  const bankAccount = await bankAPI.createVirtualAccount(userId);
  
  // 3. User transfers fiat to this account
  // (happens outside MorNet, in bank's system)
  
  // 4. Bank sends webhook to MorNet when payment received
  // (handled by handleBankWebhook)
  
  // 5. Bank Gateway verifies payment
  const payment = await bankAPI.getPayment(depositRequest.id);
  
  // 6. Emit GCC to user's wallet
  const gcc = await tokenEngine.mintGCC(userId, amount);
  
  // 7. Update escrow balance
  await escrow.deposit(amount, currency);
  
  return { depositRequest, gcc };
}

// Fiat Withdrawal Flow
async function withdrawFiat(userId: string, amount: number, currency: string) {
  // 1. User initiates withdrawal in MorNet UI
  const withdrawalRequest = await createWithdrawalRequest(userId, amount, currency);
  
  // 2. Burn GCC from user's wallet
  await tokenEngine.burnGCC(userId, amount);
  
  // 3. Send fiat via bank API
  const transfer = await bankAPI.transfer({
    from: escrowAccount,
    to: userBankAccount,
    amount,
    currency
  });
  
  // 4. Update escrow balance
  await escrow.withdraw(amount, currency);
  
  return { withdrawalRequest, transfer };
}

// Webhook Handler (from bank)
async function handleBankWebhook(bankId: string, payload: WebhookPayload) {
  // Verify webhook signature
  const isValid = await verifyWebhookSignature(bankId, payload);
  if (!isValid) throw new Error('Invalid webhook signature');
  
  // Parse event
  const event = parseWebhookEvent(payload);
  
  if (event.type === 'payment.received') {
    // User deposited fiat
    const { userId, amount, currency } = event.data;
    
    // Emit GCC
    await tokenEngine.mintGCC(userId, amount);
    
    // Notify user
    await notifications.send(userId, 'Deposit received, GCC emitted');
  }
  
  if (event.type === 'payment.sent') {
    // Fiat withdrawal completed
    const { userId, amount, currency } = event.data;
    
    // Notify user
    await notifications.send(userId, 'Withdrawal completed');
  }
}
```

---

### 4.6 Escrow & Reserves

**Проблема:** Как обеспечить GCC реальными фиатными резервами?

**Решение:** Escrow счета в банках-партнёрах

**Модель:**
1. **1:1 Backing (для начала)**
   - 1 GCC = 1 ₽ (или эквивалент в $)
   - Все фиатные депозиты хранятся на escrow счетах
   - GCC эмитируется только при наличии фиатного обеспечения

2. **Fractional Reserve (в будущем)**
   - Часть GCC обеспечена фиатом
   - Часть GCC обеспечена физическими активами (энергия, и т.д.)
   - Например: 50% fiat, 50% physical assets

**Escrow Architecture:**

```
┌─────────────────────────────────────────────────────────┐
│                 ESCROW ACCOUNTS                         │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  Bank 1 (Tinkoff):     1,000,000 ₽                     │
│  Bank 2 (Modulbank):     500,000 ₽                     │
│  Bank 3 (Revolut):        50,000 $                     │
│                                                          │
│  Total Fiat Reserves:  1,500,000 ₽ + 50,000 $          │
│                                                          │
│  GCC in Circulation:   1,500,000 GCC                    │
│                                                          │
│  Backing Ratio:        100% (1:1)                       │
└─────────────────────────────────────────────────────────┘
```

**Audit & Transparency:**
- Ежедневная сверка резервов
- Публичный dashboard с балансами escrow счетов
- Ежемесячный аудит от независимой компании
- Proof of Reserves (как у Tether, но честный)

---

### 4.7 Сравнение: Свой банк vs Партнёры

| Аспект | Свой банк | Партнёры (Bank Gateway) |
|--------|-----------|-------------------------|
| **Лицензия** | Нужна (1-2 года, $10M+) | Не нужна |
| **Капитал** | 300M-1B ₽ | Минимальный |
| **Time-to-market** | 2+ года | 3-6 месяцев |
| **Compliance** | Полная ответственность | Делегируем банку |
| **Операционные расходы** | Высокие (резервы, АСВ, аудит) | Низкие (комиссии банку) |
| **Контроль** | Полный | Частичный |
| **Риски** | Высокие (регуляторные) | Низкие |
| **Гибкость** | Низкая (жёсткое регулирование) | Высокая (можем менять партнёров) |
| **Масштабируемость** | Ограниченная (одна юрисдикция) | Высокая (разные банки, страны) |

**Вывод: Партнёры (Bank Gateway) - единственный разумный вариант для MVP и даже для v2.0.**

---

### 4.8 Roadmap банковской интеграции

#### Q1 2026 (MVP 1.0):
- ✅ Интеграция с 1 российским банком (Тинькофф или Модульбанк)
- ✅ Fiat deposits (₽)
- ✅ Fiat withdrawals (₽)
- ✅ KYC через банк
- ✅ 1:1 backing (GCC = ₽)

#### Q2 2026 (MVP 2.0):
- ✅ + 1 российский банк (диверсификация)
- ✅ Escrow accounts в 2 банках

#### Q3 2026 (MVP 3.0):
- ✅ + Международный банк (Revolut или Wise)
- ✅ Поддержка $ и €
- ✅ Кросс-валютные операции

#### Q4 2026 (MVP 4.0):
- ✅ + Криптобанк (Garantex или Mercuryo)
- ✅ Прямая покупка GCC за фиат

#### Q1 2027 (MVP 5.0):
- ✅ Fractional reserve (частичное обеспечение физическими активами)
- ✅ Asset-backed GCC (не только фиат, но и энергия, и т.д.)

#### Q2 2027 (v2.0):
- ✅ Полноценный asset-backed stablecoin
- ✅ Аудит и сертификация резервов
- ✅ Листинг на биржах

---

## 5. Технический стек

### 5.1 Backend

**Core:**
- **Node.js 22+** (TypeScript)
- **NestJS** - фреймворк для микросервисов
- **GraphQL Federation** - для объединения микросервисов

**Databases:**
- **PostgreSQL** - основная БД (транзакции, пользователи)
- **TimescaleDB** - для телеметрии (time-series data)
- **Redis** - кэш, очереди

**Message Queue:**
- **RabbitMQ** - для асинхронных задач
- **MQTT (Mosquitto)** - для IoT телеметрии

**Blockchain:**
- **Hardhat** - для разработки smart contracts
- **Ethers.js** - для взаимодействия с blockchain
- **Polygon (Mumbai testnet → Mainnet)** - для деплоя контрактов

---

### 5.2 Frontend

**Admin UI:**
- **Vue 3** + **Vite**
- **Pinia** - state management
- **TailwindCSS** - styling

**Public UI:**
- **Next.js 14** (React)
- **TypeScript**
- **TailwindCSS**

---

### 5.3 Infrastructure

**Containerization:**
- **Docker** - для всех сервисов
- **Docker Compose** - для локальной разработки

**Orchestration:**
- **Kubernetes** - для production
- **Helm** - для управления деплоями

**CI/CD:**
- **GitHub Actions** - автоматизация
- **Turborepo** - для монорепозитория

**Monitoring:**
- **Prometheus** - метрики
- **Grafana** - визуализация
- **Loki** - логи
- **Jaeger** - трейсинг

---

### 5.4 IoT / Edge

**Edge Devices:**
- **Raspberry Pi 4** - основной edge device
- **Python 3.11** - для скриптов телеметрии
- **MQTT** - для передачи данных

**Sensors & Actuators:**
- **Wi-Fi розетки** (Tapo P110, Sonoff POW R3)
- **Датчики температуры** (DS18B20)
- **Датчики воды/газа** (импульсные счётчики)

---

## 6. Микросервисы по фазам

### MVP 1.0 (Q1 2026): 11 микросервисов

#### Backend Services:
1. **identity-service** - SSO, KYC/AML
2. **wallet-service** - Управление кошельками
3. **oracle-service** - Внешние данные (тарифы)
4. **cost-engine** - Расчёт себестоимости (Residential Plugin)
5. **token-engine** - Токенизация (E/H/W Module)
6. **dao-treasury** - DAO казначейство
7. **bank-gateway** - Интеграция с банками (1 банк)
8. **api-gateway** - Единая точка входа
9. **analytics-service** - Метрики, дашборды

#### Frontend:
10. **admin-ui** - Admin панель (Vue 3)
11. **public-ui** - Публичный портал (Next.js)

#### Edge:
- **edge-agent** - Raspberry Pi (Python, MQTT)

---

### MVP 2.0 (Q2 2026): +2 микросервиса

12. **pms-connector** - Интеграция с Hotel PMS
13. **tourism-plugin** - Tourism module для Cost Engine

---

### MVP 3.0 (Q3 2026): +2 микросервиса

14. **farm-iot-gateway** - Farm IoT integration
15. **agriculture-plugin** - Agriculture module для Cost Engine

---

### MVP 4.0 (Q4 2026): +3 микросервиса

16. **scada-adapter** - SCADA/OPC UA integration
17. **manufacturing-plugin** - Manufacturing module для Cost Engine
18. **erp-connector** - ERP/MES integration (SAP, 1C)

---

### MVP 5.0 (Q1 2027): +2 микросервиса

19. **smart-meter-gateway** - Smart Meters integration
20. **municipal-plugin** - Municipal module для Cost Engine

---

### v2.0 (Q2 2027): +3 микросервиса

21. **asset-registry** - Physical Asset Registry
22. **audit-service** - Audit Trail & Compliance
23. **regulatory-module** - Regulatory Compliance

---

## 7. Data Flow

### 7.1 Telemetry Flow (Residential, MVP 1.0)

```
1. [Raspberry Pi] → Собирает данные с Wi-Fi розеток (каждую минуту)
                    ↓
2. [MQTT Broker] ← Публикует телеметрию (topic: mornet/node01/telemetry)
                    ↓
3. [MorNet Layer] ← Подписывается на MQTT, получает данные
                    ↓ Нормализация, валидация
4. [Oracle Service] ← Получает тарифы ЖКХ (API или база данных)
                    ↓
5. [Cost Engine] ← Рассчитывает себестоимость
                   cost = Σ(energy × tariff_e, water × tariff_w, gas × tariff_h)
                    ↓
6. [Token Engine] ← Токенизирует
                    E_tokens = energy_kwh
                    H_tokens = heat_kwh
                    W_tokens = water_m3
                    GCM = cost_total / GCM_rate
                    ↓
7. [DAO Treasury] ← Записывает в блокчейн
                    ↓
8. [Wallet Service] ← Обновляет балансы пользователей
                    ↓
9. [Analytics Service] ← Обновляет метрики, дашборды
```

---

### 7.2 Fiat Deposit Flow

```
1. [User] → Инициирует депозит в Public UI
            ↓
2. [Bank Gateway] → Создаёт виртуальный счёт через API банка
            ↓
3. [User] → Переводит ₽ на виртуальный счёт (в банке)
            ↓
4. [Bank] → Отправляет webhook в Bank Gateway (payment.received)
            ↓
5. [Bank Gateway] → Верифицирует платёж
            ↓
6. [Token Engine] → Эмитирует GCC
            ↓
7. [Wallet Service] → Зачисляет GCC на кошелёк пользователя
            ↓
8. [Escrow] → Обновляет баланс резервов
            ↓
9. [User] → Получает уведомление (GCC зачислены)
```

---

### 7.3 Token Exchange Flow (GCC → E/H/W)

```
1. [User] → Хочет купить E токены (электричество)
            ↓
2. [Exchange Router] → Рассчитывает курс (GCC → E)
            ↓
3. [DAO Treasury] → Проверяет ликвидность E токенов
            ↓
4. [Wallet Service] → Списывает GCC с кошелька пользователя
            ↓
5. [Token Engine] → Зачисляет E токены на кошелёк пользователя
            ↓
6. [DAO Treasury] → Обновляет пулы ликвидности
            ↓
7. [User] → Получает E токены
```

---

## 8. Security & Compliance

### 8.1 Security Layers

1. **Network Security**
   - VPC (Virtual Private Cloud)
   - Firewall rules
   - DDoS protection (Cloudflare)

2. **Application Security**
   - JWT authentication
   - Role-based access control (RBAC)
   - API rate limiting
   - Input validation (Zod schemas)

3. **Data Security**
   - Encryption at rest (AES-256)
   - Encryption in transit (TLS 1.3)
   - Database encryption (PostgreSQL pgcrypto)

4. **Blockchain Security**
   - Multi-sig wallets для DAO Treasury
   - Timelock для критических операций
   - Audit smart contracts (CertiK, OpenZeppelin)

---

### 8.2 Compliance

#### KYC/AML (через банки-партнёры)

**MVP (Q1 2026):**
- Базовый KYC через API банка
- Верификация паспорта
- Проверка адреса

**v2.0 (Q2 2027):**
- Enhanced Due Diligence (EDD) для крупных сумм
- Мониторинг транзакций (AML)
- Интеграция с FATF списками

#### Regulatory Compliance

**Россия:**
- ФЗ-259 (ЦФА) - цифровые финансовые активы
- ФЗ-115 (AML/CFT) - через банки-партнёры
- ФЗ-152 (Персональные данные) - GDPR-like

**Международное:**
- FATF рекомендации
- MiCA (EU) - если выходим в Европу
- SEC (USA) - если листинг на US биржах

---

### 8.3 GC-Guard (Compliance Module)

**Функции:**
1. **Audit Trail**
   - Логирование всех операций
   - Immutable audit log (blockchain)

2. **Regulatory Reporting**
   - Автоматическая генерация отчётов
   - Интеграция с регуляторами

3. **Risk Management**
   - Мониторинг рисков
   - Алерты при подозрительных операциях

4. **Legal Framework**
   - Смарт-контракты с юридической силой
   - Интеграция с Росреестром, ЕГРЮЛ

---

## 9. Deployment

### 9.1 Environments

1. **Development (local)**
   - Docker Compose
   - Локальная БД
   - Mock сервисы

2. **Staging**
   - Kubernetes (Minikube или облако)
   - Тестовая БД
   - Интеграция с тестовыми API банков

3. **Production**
   - Kubernetes (облако: Yandex Cloud, AWS, или on-premise)
   - Production БД (реплики, бэкапы)
   - Реальные API банков

---

### 9.2 Infrastructure as Code

**Terraform:**
- Provisioning инфраструктуры
- VPC, subnets, security groups
- Kubernetes clusters
- Databases (managed PostgreSQL)

**Helm Charts:**
- Деплой микросервисов
- Конфигурация (ConfigMaps, Secrets)
- Версионирование

---

### 9.3 CI/CD Pipeline

```yaml
# .github/workflows/deploy.yml

name: Deploy to Production

on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run tests
        run: pnpm test

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - name: Build Docker images
        run: pnpm build:docker
      - name: Push to registry
        run: docker push ...

  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to Kubernetes
        run: |
          helm upgrade --install mornet ./helm/mornet \
            --namespace production \
            --values ./helm/values.prod.yaml
```

---

## 10. Roadmap детально

### Q4 2025 (Сейчас)

**Неделя 1-2:**
- ✅ Принять решение: Стратегия C (Гибридная)
- ✅ Обновить архитектурную документацию
- ✅ Детальный дизайн модульной архитектуры

**Неделя 3-4:**
- Детальный дизайн Cost Engine
- Детальный дизайн Bank Gateway
- Выбор банка-партнёра (переговоры)

---

### Q1 2026: MVP 1.0 - Residential

**Январь:**
- Разработка Core Services (Identity, Wallet, Oracle, API Gateway)
- Разработка Cost Engine (Residential Plugin)
- Разработка Token Engine (E/H/W Module)

**Февраль:**
- Разработка Bank Gateway (интеграция с 1 банком)
- Разработка DAO Treasury
- Разработка Admin UI, Public UI

**Март:**
- Интеграция всех сервисов
- Тестирование (unit, integration, e2e)
- Деплой в staging
- Пилот: 10 домохозяйств

**Конец Q1:**
- 🚀 Запуск MVP 1.0
- Target: 100+ домохозяйств, $10K MRR

---

### Q2 2026: MVP 2.0 - Tourism

**Апрель:**
- Разработка PMS Connector (интеграция с Hotel PMS)
- Разработка Tourism Plugin для Cost Engine
- Разработка SRV Token Module

**Май:**
- Интеграция с 1-2 отелями (пилот)
- Тестирование

**Июнь:**
- 🚀 Запуск MVP 2.0
- Target: 10+ отелей, $20K MRR

---

### Q3 2026: MVP 3.0 - Agriculture

**Июль:**
- Разработка Farm IoT Gateway
- Разработка Agriculture Plugin для Cost Engine
- Разработка AGR/LOG Token Modules

**Август:**
- Интеграция с 2-3 фермами (пилот)
- Тестирование

**Сентябрь:**
- 🚀 Запуск MVP 3.0
- Target: 20+ ферм, $30K MRR

---

### Q4 2026: MVP 4.0 - Manufacturing

**Октябрь:**
- Разработка SCADA/OPC UA Adapter
- Разработка Manufacturing Plugin для Cost Engine
- Разработка PRD Token Module

**Ноябрь:**
- Интеграция с 1-2 заводами (пилот)
- Тестирование

**Декабрь:**
- 🚀 Запуск MVP 4.0
- Target: 5+ заводов, $50K MRR

---

### Q1 2027: MVP 5.0 - Municipal

**Январь:**
- Разработка Smart Meter Gateway
- Разработка Municipal Plugin для Cost Engine
- Разработка PUB Token Module

**Февраль:**
- Интеграция с 1 муниципалитетом (пилот)
- Тестирование

**Март:**
- 🚀 Запуск MVP 5.0
- Target: 2+ муниципалитета, $100K MRR
- **Позиционирование:** "Платформа токенизации физической экономики"

---

### Q2 2027: v2.0 - Asset-backed GCC

**Апрель:**
- Разработка Asset Registry Service
- Разработка Audit Service
- Разработка Regulatory Module

**Май:**
- Интеграция с Росреестром, ЕГРЮЛ
- Партнёрство с аудиторскими компаниями
- Юридическая экспертиза

**Июнь:**
- 🚀 Запуск v2.0
- GCC как asset-backed stablecoin
- Листинг на биржах (DEX/CEX)

---

## 📊 Метрики успеха

### MVP 1.0 (Q1 2026):
- ✅ 100+ домохозяйств
- ✅ $10K MRR
- ✅ 1,000+ GCC в обращении
- ✅ 1 банк-партнёр

### MVP 2.0 (Q2 2026):
- ✅ 10+ отелей
- ✅ $20K MRR
- ✅ 5,000+ GCC в обращении

### MVP 3.0 (Q3 2026):
- ✅ 20+ ферм
- ✅ $30K MRR
- ✅ 10,000+ GCC в обращении

### MVP 4.0 (Q4 2026):
- ✅ 5+ заводов
- ✅ $50K MRR
- ✅ 50,000+ GCC в обращении

### MVP 5.0 (Q1 2027):
- ✅ 2+ муниципалитета
- ✅ $100K MRR
- ✅ 100,000+ GCC в обращении
- ✅ 5+ секторов активны

### v2.0 (Q2 2027):
- ✅ Asset-backed GCC
- ✅ Листинг на 2+ биржах
- ✅ $1M+ TVL (Total Value Locked)

---

## 📝 Заключение

### Ключевые решения:

1. **Стратегия C (Гибридная)** - начинаем узко, масштабируемся широко
2. **Модульная архитектура** - Core + Plugins
3. **Банки-партнёры** - НЕ свой банк, а Bank Gateway
4. **Фазированный запуск** - новый сектор каждый квартал
5. **Asset-backed GCC** - отложить на v2.0 (Q2 2027)

### Преимущества:

✅ Быстрый time-to-market (Q1 2026)  
✅ Ранняя валидация (домовладельцы)  
✅ Постепенное масштабирование (низкий риск)  
✅ Достижение vision v1.5.0 (к Q1 2027)  
✅ Гибкость (можем pivot после каждой фазы)  
✅ Compliance через банки (не нужна своя лицензия)

### Следующие шаги:

1. **Сегодня:** Принять решение по стратегии
2. **Неделя 1-2:** Детальный дизайн Cost Engine, Bank Gateway
3. **Неделя 3-4:** Выбор банка-партнёра, начало переговоров
4. **Q1 2026:** Разработка и запуск MVP 1.0

**MorNet GreenCore - от "Energy Hub для домов" к "Платформе токенизации физической экономики"** 🚀

---

**Автор:** AI Assistant  
**Дата:** 16 октября 2025  
**Версия:** 1.0  
**Статус:** Архитектурный документ

---

## 🔗 Связанные документы

1. [Strategy Comparison: Energy Hub vs v1.5.0](../analysis/strategy-comparison-energy-hub-vs-v1.5.0.md)
2. [v1.5.0 Critical Changes Analysis](../analysis/v1.5.0-critical-changes-analysis.md)
3. [C4 Architecture MorNet GreenCore](./c4_architecture_mornet_greencore_v10.md)
4. [Backend High-Level Architecture](./backend_high_level_mor_net_architecture.md)

