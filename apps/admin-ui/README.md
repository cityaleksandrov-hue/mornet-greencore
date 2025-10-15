# Admin UI

Административная панель MorNet GreenCore, построенная на Vue 3 + Naive UI.

## Описание

Admin UI предоставляет интерфейс для управления всеми аспектами платформы MorNet GreenCore, включая:

- Управление электростанциями (Electrostation Nodes)
- Мониторинг токенизации и минта E-Token
- Управление DAO Treasury (резервы, распределение)
- Панель GC-Guard (KYC/AML, политики, аудит)
- Аналитика и отчётность
- Администрирование пользователей и ролей

## Технологический стек

- **Vue 3** - Progressive JavaScript framework
- **Naive UI** - Vue 3 Component Library
- **Pinia** - State management
- **Vue Router** - Routing
- **Vite** - Build tool
- **TypeScript** - Type safety
- **Axios** - HTTP client
- **Recharts** - Data visualization

## Разработка

```bash
# Установка зависимостей
pnpm install

# Запуск dev сервера
pnpm dev

# Сборка для production
pnpm build

# Preview production build
pnpm preview

# Линтинг
pnpm lint

# Форматирование
pnpm format
```

## Структура проекта

```
admin-ui/
├── src/
│   ├── assets/         # Статические ресурсы
│   ├── components/     # Vue компоненты
│   ├── layouts/        # Layout компоненты
│   ├── pages/          # Страницы приложения
│   ├── router/         # Vue Router конфигурация
│   ├── stores/         # Pinia stores
│   ├── services/       # API сервисы
│   ├── utils/          # Утилиты
│   ├── types/          # TypeScript типы
│   ├── App.vue         # Root компонент
│   └── main.ts         # Entry point
├── public/             # Публичные файлы
├── index.html          # HTML template
├── vite.config.ts      # Vite конфигурация
└── package.json
```

## Основные функции

### Управление электростанциями
- Добавление/редактирование/удаление станций
- Мониторинг телеметрии в реальном времени
- Управление Demo/Live режимом

### Токенизация
- Просмотр интервалов генерации
- Мониторинг процесса минта E-Token
- История токенизации

### DAO Treasury
- Управление резервами
- Распределение токенов
- Финансовая отчётность

### GC-Guard
- Управление KYC/AML процессами
- Настройка политик минта и резервов
- Аудиторские журналы
- MRV-отчёты

## Переменные окружения

Создайте файл `.env.local` на основе `.env.example`:

```env
VITE_API_URL=http://localhost:3000
VITE_WS_URL=ws://localhost:3000
```

## Доступ

После запуска приложение доступно по адресу: http://localhost:5173

Учётные данные по умолчанию (dev):
- Email: admin@mornet-greencore.io
- Password: admin123

---

**Часть монорепозитория MorNet GreenCore**

