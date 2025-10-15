# Public UI

Публичный портал MorNet GreenCore, построенный на React + Next.js.

## Описание

Public UI предоставляет публичный интерфейс для пользователей и инвесторов платформы MorNet GreenCore:

- Личный кабинет пользователя/инвестора
- Витрина токенов и NFT
- Обменник (E↔GCM, GCM↔фиат)
- DAO интерфейс (голосование, предложения)
- Публичная аналитика и статистика
- Маркетплейс энергетических активов

## Технологический стек

- **React 18** - UI library
- **Next.js 14** - React framework (App Router)
- **Wagmi** - React Hooks for Ethereum
- **ethers.js** - Ethereum library
- **Zustand** - State management
- **TailwindCSS** - Utility-first CSS
- **TypeScript** - Type safety
- **Recharts** - Data visualization
- **React Query** - Data fetching

## Разработка

```bash
# Установка зависимостей
pnpm install

# Запуск dev сервера
pnpm dev

# Сборка для production
pnpm build

# Запуск production сервера
pnpm start

# Линтинг
pnpm lint

# Форматирование
pnpm format
```

## Структура проекта

```
public-ui/
├── app/                # Next.js App Router
│   ├── (auth)/        # Auth routes
│   ├── (dashboard)/   # Dashboard routes
│   ├── layout.tsx     # Root layout
│   └── page.tsx       # Home page
├── components/         # React компоненты
│   ├── ui/            # UI компоненты
│   ├── features/      # Feature компоненты
│   └── layouts/       # Layout компоненты
├── lib/               # Библиотеки и утилиты
│   ├── api/           # API клиенты
│   ├── hooks/         # Custom hooks
│   ├── stores/        # Zustand stores
│   └── utils/         # Утилиты
├── public/            # Статические файлы
├── styles/            # Глобальные стили
├── types/             # TypeScript типы
├── next.config.js     # Next.js конфигурация
└── package.json
```

## Основные функции

### Личный кабинет
- Профиль пользователя
- Портфель токенов (E-Token, GCM)
- История транзакций
- Управление кошельками

### Обменник
- Обмен E↔GCM
- Обмен GCM↔фиат через банковский шлюз
- График цен в реальном времени
- История сделок

### DAO
- Просмотр предложений
- Голосование
- Создание предложений
- История голосований

### Маркетплейс
- Витрина энергетических токенов
- NFT коллекции
- Покупка/продажа токенов
- Аукционы

## Переменные окружения

Создайте файл `.env.local` на основе `.env.example`:

```env
NEXT_PUBLIC_API_URL=http://localhost:3000
NEXT_PUBLIC_CHAIN_ID=1
NEXT_PUBLIC_WALLET_CONNECT_PROJECT_ID=your-project-id
```

## Web3 интеграция

Приложение поддерживает подключение следующих кошельков:

- MetaMask
- WalletConnect
- Coinbase Wallet
- Rainbow Wallet

## SSR и SEO

Приложение использует Next.js App Router с поддержкой:

- Server-Side Rendering (SSR)
- Static Site Generation (SSG)
- Incremental Static Regeneration (ISR)
- Оптимизация SEO
- Open Graph метатеги
- Sitemap и robots.txt

## Доступ

После запуска приложение доступно по адресу: http://localhost:3001

---

**Часть монорепозитория MorNet GreenCore**

