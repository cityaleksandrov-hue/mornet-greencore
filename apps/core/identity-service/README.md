# Identity Service

Сервис идентификации и аутентификации для платформы MorNet GreenCore.

## Описание

Identity Service обеспечивает централизованную аутентификацию и авторизацию для всех компонентов платформы:

- Регистрация и аутентификация пользователей
- SSO (Single Sign-On) для Admin UI и Public UI
- Управление пользователями и ролями (RBAC)
- JWT токены и refresh tokens
- OAuth2 / OpenID Connect
- Интеграция с внешними провайдерами (Google, GitHub и т.д.)

## Технологический стек

- **NestJS** - Progressive Node.js framework
- **TypeScript** - Type safety
- **TypeORM** - ORM для работы с БД
- **PostgreSQL** - База данных
- **Passport.js** - Authentication middleware
- **JWT** - JSON Web Tokens
- **bcrypt** - Password hashing

## API Endpoints

### Authentication

```
POST   /auth/register       - Регистрация нового пользователя
POST   /auth/login          - Вход в систему
POST   /auth/logout         - Выход из системы
POST   /auth/refresh        - Обновление access token
POST   /auth/forgot-password - Запрос на восстановление пароля
POST   /auth/reset-password  - Сброс пароля
```

### Users

```
GET    /users               - Получить список пользователей
GET    /users/:id           - Получить пользователя по ID
POST   /users               - Создать пользователя
PATCH  /users/:id           - Обновить пользователя
DELETE /users/:id           - Удалить пользователя
GET    /users/me            - Получить текущего пользователя
```

### Roles

```
GET    /roles               - Получить список ролей
GET    /roles/:id           - Получить роль по ID
POST   /roles               - Создать роль
PATCH  /roles/:id           - Обновить роль
DELETE /roles/:id           - Удалить роль
```

## Разработка

```bash
# Установка зависимостей
pnpm install

# Запуск в dev режиме
pnpm dev

# Сборка
pnpm build

# Запуск production
pnpm start

# Тесты
pnpm test

# E2E тесты
pnpm test:e2e

# Линтинг
pnpm lint
```

## Переменные окружения

```env
# Server
PORT=3010
NODE_ENV=development

# Database
DATABASE_URL=postgresql://postgres:password@localhost:5432/mornet_greencore

# JWT
JWT_SECRET=your-secret-key
JWT_EXPIRES_IN=7d
JWT_REFRESH_SECRET=your-refresh-secret
JWT_REFRESH_EXPIRES_IN=30d

# OAuth (optional)
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
GITHUB_CLIENT_ID=
GITHUB_CLIENT_SECRET=
```

## База данных

### Миграции

```bash
# Создать миграцию
pnpm migration:create <name>

# Запустить миграции
pnpm migration:run

# Откатить миграцию
pnpm migration:revert
```

### Сущности

- **User** - пользователи системы
- **Role** - роли для RBAC
- **Permission** - права доступа
- **RefreshToken** - refresh токены

## RBAC (Role-Based Access Control)

Сервис поддерживает гибкую систему ролей и прав:

### Предустановленные роли

- **admin** - полный доступ ко всем ресурсам
- **operator** - управление станциями и токенизацией
- **analyst** - доступ к аналитике и отчётам
- **investor** - доступ к личному кабинету и обменнику
- **user** - базовый доступ

### Права доступа

Права определяются в формате `resource:action`:

```
users:read
users:write
stations:read
stations:write
tokens:mint
treasury:manage
```

## Интеграция с другими сервисами

Identity Service предоставляет middleware для проверки JWT токенов, который используется всеми остальными сервисами:

```typescript
import { JwtAuthGuard } from '@mornet/shared';

@UseGuards(JwtAuthGuard)
@Controller('protected')
export class ProtectedController {
  // ...
}
```

## Безопасность

- Пароли хешируются с использованием bcrypt (10 раундов)
- JWT токены подписываются с использованием HS256
- Refresh tokens хранятся в БД и могут быть отозваны
- Rate limiting для защиты от brute-force атак
- CORS настроен для разрешённых доменов

## Мониторинг

Сервис экспортирует метрики для Prometheus:

- Количество успешных/неуспешных попыток входа
- Время ответа API
- Количество активных пользователей
- Количество выданных токенов

---

**Часть монорепозитория MorNet GreenCore**

