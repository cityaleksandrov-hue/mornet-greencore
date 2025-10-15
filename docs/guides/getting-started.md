# Getting Started - Начало работы с MorNet GreenCore

Это руководство поможет вам настроить локальное окружение для разработки и запустить проект MorNet GreenCore.

## Предварительные требования

Перед началом работы убедитесь, что у вас установлены следующие инструменты:

### Обязательные компоненты

- **Node.js** >= 18.0.0 ([скачать](https://nodejs.org/))
- **pnpm** >= 8.0.0 (установка: `npm install -g pnpm`)
- **Docker** >= 24.0.0 ([скачать](https://www.docker.com/))
- **Docker Compose** >= 2.0.0 (обычно идёт в комплекте с Docker Desktop)
- **Git** >= 2.30.0

### Рекомендуемые инструменты

- **Visual Studio Code** с расширениями:
  - ESLint
  - Prettier
  - TypeScript and JavaScript Language Features
  - Docker
  - GitLens
- **Postman** или **Insomnia** для тестирования API
- **DBeaver** или **pgAdmin** для работы с PostgreSQL

## Установка проекта

### Шаг 1: Клонирование репозитория

```bash
git clone https://github.com/cityaleksandrov-hue/mornet-greencore.git
cd mornet-greencore
```

### Шаг 2: Установка зависимостей

Проект использует **pnpm** в качестве менеджера пакетов для эффективного управления зависимостями в монорепозитории.

```bash
pnpm install
```

Эта команда установит все зависимости для всех приложений и пакетов в монорепозитории.

### Шаг 3: Настройка переменных окружения

Скопируйте файл `.env.example` в `.env` и настройте переменные окружения:

```bash
cp .env.example .env
```

Откройте файл `.env` и настройте необходимые параметры. Для локальной разработки базовые настройки из `.env.example` должны работать без изменений.

### Шаг 4: Запуск инфраструктуры

Проект использует Docker Compose для запуска необходимых сервисов (PostgreSQL, Redis, RabbitMQ, MQTT).

```bash
docker-compose up -d
```

Эта команда запустит все необходимые сервисы в фоновом режиме. Проверить статус сервисов можно командой:

```bash
docker-compose ps
```

### Шаг 5: Инициализация базы данных

После запуска PostgreSQL необходимо выполнить миграции базы данных:

```bash
pnpm db:migrate
```

Для заполнения базы данных тестовыми данными (опционально):

```bash
pnpm db:seed
```

### Шаг 6: Запуск приложений

Теперь можно запустить все приложения в режиме разработки:

```bash
pnpm dev
```

Эта команда запустит все микросервисы и frontend приложения одновременно.

Если вы хотите запустить только конкретное приложение:

```bash
# Запустить только Admin UI
pnpm --filter admin-ui dev

# Запустить только Identity Service
pnpm --filter identity-service dev

# Запустить только API Gateway
pnpm --filter api-gateway dev
```

## Доступ к приложениям

После успешного запуска приложения будут доступны по следующим адресам:

### Frontend

- **Admin UI** (Vue 3): http://localhost:5173
- **Public UI** (Next.js): http://localhost:3001

### Backend Services

- **API Gateway**: http://localhost:3000
- **Identity Service**: http://localhost:3010
- **Wallet Service**: http://localhost:3011
- **Token Engine**: http://localhost:3012
- **Oracle Service**: http://localhost:3013
- **DAO Treasury**: http://localhost:3014
- **Exchange Router**: http://localhost:3015
- **Bank Gateway**: http://localhost:3016
- **GC-Guard Adapter**: http://localhost:3017
- **Analytics Service**: http://localhost:3018

### Инфраструктура

- **PostgreSQL**: localhost:5432
- **Redis**: localhost:6379
- **RabbitMQ Management**: http://localhost:15672 (guest/guest)
- **MQTT Broker**: localhost:1883

## Проверка установки

Чтобы убедиться, что всё работает корректно, выполните следующие проверки:

### 1. Проверка сборки

```bash
pnpm build
```

Все приложения должны успешно собраться без ошибок.

### 2. Проверка линтера

```bash
pnpm lint
```

Не должно быть критических ошибок линтера.

### 3. Проверка тестов

```bash
pnpm test
```

Все тесты должны пройти успешно.

### 4. Проверка типов TypeScript

```bash
pnpm typecheck
```

Не должно быть ошибок типизации.

## Структура проекта

Проект организован как монорепозиторий с использованием Turborepo:

```
mornet-greencore/
├── apps/           # Приложения (frontend и backend)
├── packages/       # Общие библиотеки
├── infra/          # Инфраструктурные конфигурации
└── docs/           # Документация
```

Подробнее о структуре проекта читайте в [главном README](../../README.md).

## Полезные команды

### Управление зависимостями

```bash
# Добавить зависимость в конкретный пакет
pnpm --filter admin-ui add vue

# Добавить dev-зависимость
pnpm --filter identity-service add -D @types/node

# Обновить все зависимости
pnpm update

# Удалить зависимость
pnpm --filter wallet-service remove lodash
```

### Работа с Docker

```bash
# Запустить все сервисы
docker-compose up -d

# Остановить все сервисы
docker-compose down

# Просмотреть логи
docker-compose logs -f

# Пересобрать и перезапустить сервисы
docker-compose up -d --build
```

### Работа с базой данных

```bash
# Создать новую миграцию
pnpm db:migration:create <migration-name>

# Применить миграции
pnpm db:migrate

# Откатить последнюю миграцию
pnpm db:migrate:rollback

# Очистить базу данных
pnpm db:reset
```

### Форматирование и линтинг

```bash
# Проверить форматирование
pnpm format:check

# Исправить форматирование
pnpm format

# Запустить линтер
pnpm lint

# Исправить ошибки линтера
pnpm lint:fix
```

## Следующие шаги

После успешной установки и запуска проекта рекомендуем:

1. Изучить [архитектурную документацию](../architecture/)
2. Ознакомиться с [API документацией](../api/)
3. Прочитать [руководство по разработке](./development-workflow.md)
4. Изучить [best practices по безопасности](./security-best-practices.md)

## Возможные проблемы и решения

### Порты уже заняты

Если при запуске Docker Compose вы видите ошибку о занятых портах, измените порты в файле `docker-compose.yml` или остановите процессы, использующие эти порты.

### Ошибки при установке зависимостей

Убедитесь, что используете правильную версию Node.js (>= 18.0.0) и pnpm (>= 8.0.0):

```bash
node --version
pnpm --version
```

### База данных не подключается

Убедитесь, что PostgreSQL запущен и доступен:

```bash
docker-compose ps postgres
```

Проверьте переменную `DATABASE_URL` в файле `.env`.

### Проблемы с правами доступа в Docker

На Linux может потребоваться добавить пользователя в группу docker:

```bash
sudo usermod -aG docker $USER
```

После этого перезайдите в систему.

## Получение помощи

Если у вас возникли проблемы, которые не описаны в этом руководстве:

1. Проверьте [FAQ](./faq.md)
2. Поищите похожие issue в [GitHub репозитории](https://github.com/cityaleksandrov-hue/mornet-greencore/issues)
3. Создайте новый issue с подробным описанием проблемы
4. Обратитесь в команду разработки: dev@mornet-greencore.io

---

**Последнее обновление:** 15 октября 2025

