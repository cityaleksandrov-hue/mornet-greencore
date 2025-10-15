# Contributing to MorNet GreenCore

Спасибо за интерес к участию в разработке MorNet GreenCore! Этот документ содержит руководство по процессу разработки и стандартам кода.

## Процесс разработки

### 1. Создание Issue

Перед началом работы создайте issue в GitHub с описанием:
- Проблемы, которую вы хотите решить
- Функции, которую вы хотите добавить
- Улучшения, которое вы хотите внести

### 2. Создание ветки

Создайте новую ветку от `main` с соответствующим префиксом:

```bash
# Для новой функции
git checkout -b feature/short-description

# Для исправления бага
git checkout -b bugfix/short-description

# Для hotfix
git checkout -b hotfix/short-description
```

### 3. Разработка

Следуйте стандартам кодирования и best practices, описанным ниже.

### 4. Коммиты

Используйте [Conventional Commits](https://www.conventionalcommits.org/) для сообщений коммитов:

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Типы коммитов:**
- `feat`: новая функция
- `fix`: исправление бага
- `docs`: изменения в документации
- `style`: форматирование кода (без изменения логики)
- `refactor`: рефакторинг кода
- `perf`: улучшение производительности
- `test`: добавление или изменение тестов
- `chore`: изменения в build процессе или вспомогательных инструментах

**Примеры:**

```bash
feat(token-engine): add E-Token minting logic
fix(wallet-service): resolve transaction signing issue
docs(readme): update installation instructions
refactor(api-gateway): simplify authentication middleware
```

### 5. Тестирование

Убедитесь, что все тесты проходят:

```bash
pnpm test
pnpm lint
pnpm typecheck
```

### 6. Pull Request

Создайте Pull Request с описанием:
- Что было сделано
- Почему это было сделано
- Как это было протестировано
- Ссылка на связанный issue

## Стандарты кодирования

### TypeScript

- Используйте строгий режим TypeScript
- Избегайте `any`, используйте конкретные типы
- Используйте интерфейсы для объектов
- Используйте enum для константных значений

### Именование

- **Файлы**: kebab-case (e.g., `user-service.ts`)
- **Классы**: PascalCase (e.g., `UserService`)
- **Функции/методы**: camelCase (e.g., `getUserById`)
- **Константы**: UPPER_SNAKE_CASE (e.g., `MAX_RETRY_COUNT`)
- **Интерфейсы**: PascalCase с префиксом `I` (e.g., `IUser`)

### Форматирование

Проект использует Prettier для автоматического форматирования:

```bash
pnpm format
```

### Линтинг

Проект использует ESLint:

```bash
pnpm lint
pnpm lint:fix
```

## Структура кода

### Backend сервисы (NestJS)

```
service-name/
├── src/
│   ├── modules/           # Feature modules
│   │   └── feature/
│   │       ├── dto/       # Data Transfer Objects
│   │       ├── entities/  # Database entities
│   │       ├── feature.controller.ts
│   │       ├── feature.service.ts
│   │       ├── feature.module.ts
│   │       └── feature.spec.ts
│   ├── common/            # Shared code
│   │   ├── decorators/
│   │   ├── filters/
│   │   ├── guards/
│   │   ├── interceptors/
│   │   └── pipes/
│   ├── config/            # Configuration
│   ├── main.ts            # Entry point
│   └── app.module.ts      # Root module
├── test/                  # E2E tests
├── package.json
└── tsconfig.json
```

### Frontend приложения

```
app-name/
├── src/
│   ├── components/        # Reusable components
│   ├── pages/            # Page components
│   ├── layouts/          # Layout components
│   ├── services/         # API services
│   ├── stores/           # State management
│   ├── utils/            # Utility functions
│   ├── types/            # TypeScript types
│   └── main.ts           # Entry point
├── public/               # Static assets
├── package.json
└── tsconfig.json
```

## Тестирование

### Unit тесты

Пишите unit тесты для всех сервисов и утилит:

```typescript
describe('UserService', () => {
  it('should create a user', async () => {
    // Arrange
    const userData = { email: 'test@example.com' };
    
    // Act
    const user = await userService.create(userData);
    
    // Assert
    expect(user).toBeDefined();
    expect(user.email).toBe(userData.email);
  });
});
```

### E2E тесты

Пишите E2E тесты для критических сценариев:

```typescript
describe('Auth (e2e)', () => {
  it('/auth/login (POST)', () => {
    return request(app.getHttpServer())
      .post('/auth/login')
      .send({ email: 'test@example.com', password: 'password' })
      .expect(200)
      .expect((res) => {
        expect(res.body.accessToken).toBeDefined();
      });
  });
});
```

## Документация

### Комментарии в коде

Используйте JSDoc для документирования функций и классов:

```typescript
/**
 * Creates a new user in the system
 * @param userData - User data for creation
 * @returns Created user entity
 * @throws {ConflictException} If user with email already exists
 */
async createUser(userData: CreateUserDto): Promise<User> {
  // Implementation
}
```

### API документация

Используйте Swagger/OpenAPI для документирования API:

```typescript
@ApiTags('users')
@Controller('users')
export class UserController {
  @ApiOperation({ summary: 'Get user by ID' })
  @ApiResponse({ status: 200, description: 'User found', type: User })
  @ApiResponse({ status: 404, description: 'User not found' })
  @Get(':id')
  async getUser(@Param('id') id: string): Promise<User> {
    // Implementation
  }
}
```

## Code Review

Все Pull Request проходят code review. При review обращайте внимание на:

- Соответствие стандартам кодирования
- Наличие тестов
- Качество документации
- Производительность
- Безопасность
- Обработка ошибок

## Вопросы?

Если у вас есть вопросы, создайте issue или обратитесь к команде разработки: dev@mornet-greencore.io

---

**Спасибо за вклад в MorNet GreenCore!**

