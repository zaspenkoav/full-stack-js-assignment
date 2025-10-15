# Full Stack JavaScript - Домашнее задание

## Task Management Application (Full Stack)

### Описание

Создайте Full Stack приложение для управления задачами с возможностью регистрации пользователей, аутентификации и CRUD операций над задачами.

**Стек технологий:**
- **Frontend:** React 18+, React Router, CSS Modules/Styled Components/Tailwind
- **Backend:** Node.js + Express (или NestJS)
- **Database:** PostgreSQL или MongoDB
- **Authentication:** JWT
- **Optional:** TypeScript, Docker, тесты

---

## Функциональные требования

### Frontend (React)

#### 1. Страницы приложения

**Публичные страницы:**
- `/login` - страница входа
- `/register` - страница регистрации

**Приватные страницы (требуют аутентификации):**
- `/` или `/tasks` - список задач пользователя
- `/tasks/:id` - детальная страница задачи

#### 2. Функциональность

**Аутентификация:**
- Форма регистрации (email, password, name)
- Форма входа (email, password)
- Хранение JWT токена (localStorage или cookies)
- Logout функциональность
- Защита приватных роутов (redirect на /login если не авторизован)

**Управление задачами:**
- Отображение списка задач текущего пользователя
- Создание новой задачи (title, description, priority, due_date)
- Редактирование задачи
- Удаление задачи
- Изменение статуса задачи (pending, in_progress, completed)
- Фильтрация задач по статусу
- Поиск задач по названию

#### 3. UI/UX требования

- Responsive design (работает на мобильных и desktop)
- Loading states (спиннеры при загрузке данных)
- Error handling (показывать ошибки пользователю)
- Form validation (валидация полей форм)
- Уведомления об успешных операциях (toast/snackbar)

---

### Backend (Node.js + Express)

#### 1. API Endpoints

**Authentication:**
```
POST /api/auth/register
Body: { name, email, password }
Response: { user, token }

POST /api/auth/login
Body: { email, password }
Response: { user, token }

GET /api/auth/me (Protected)
Headers: Authorization: Bearer <token>
Response: { user }
```

**Tasks (все endpoints требуют аутентификации):**
```
GET /api/tasks
Headers: Authorization: Bearer <token>
Query: ?status=completed&search=text
Response: { tasks: [...] }

GET /api/tasks/:id
Response: { task }

POST /api/tasks
Body: { title, description, priority, due_date }
Response: { task }

PUT /api/tasks/:id
Body: { title, description, priority, status, due_date }
Response: { task }

DELETE /api/tasks/:id
Response: { message }
```

#### 2. Технические требования

**Database Schema:**

```sql
-- PostgreSQL schema
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE tasks (
  id SERIAL PRIMARY KEY,
  user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
  title VARCHAR(255) NOT NULL,
  description TEXT,
  status VARCHAR(50) DEFAULT 'pending',
  priority VARCHAR(50) DEFAULT 'medium',
  due_date DATE,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

CREATE INDEX idx_tasks_user_id ON tasks(user_id);
CREATE INDEX idx_tasks_status ON tasks(status);
```

**Или MongoDB schema:**
```javascript
// User schema
{
  name: String,
  email: { type: String, unique: true },
  passwordHash: String,
  createdAt: Date
}

// Task schema
{
  userId: ObjectId,
  title: String,
  description: String,
  status: { type: String, enum: ['pending', 'in_progress', 'completed'] },
  priority: { type: String, enum: ['low', 'medium', 'high'] },
  dueDate: Date,
  createdAt: Date,
  updatedAt: Date
}
```

**Security:**
- Password hashing (bcrypt)
- JWT token generation и validation
- Middleware для проверки аутентификации
- Пользователь может работать только со своими задачами
- Input validation и sanitization
- CORS настройка

**Error Handling:**
- Централизованная обработка ошибок
- Правильные HTTP статус коды
- Понятные error messages для клиента

---

## Дополнительные фичи (опционально, но будет плюсом)

### Frontend
- [ ] TypeScript
- [ ] State management (Redux Toolkit / Zustand / Context API)
- [ ] React Query или SWR для работы с API
- [ ] Unit тесты (Jest + React Testing Library)
- [ ] E2E тесты (Playwright / Cypress)
- [ ] Dark mode
- [ ] Drag & Drop для изменения порядка задач

### Backend
- [ ] TypeScript
- [ ] Unit и integration тесты
- [ ] API documentation (Swagger/OpenAPI)
- [ ] Rate limiting
- [ ] Request logging (Morgan или Winston)
- [ ] Database migrations (Prisma Migrate / TypeORM / Sequelize)
- [ ] Environment-based configuration (.env)
- [ ] Refresh tokens (для JWT)

### DevOps
- [ ] Docker и Docker Compose
- [ ] CI/CD (GitHub Actions / GitLab CI)
- [ ] Deployment instructions
- [ ] Environment variables management

---

## Технические требования к коду

### Общие
- Чистый, читаемый код
- Понятные имена переменных и функций
- Комментарии где необходимо
- Consistent code style (ESLint + Prettier)
- Git commits с понятными сообщениями

### Frontend
- Модульная структура компонентов
- Переиспользуемые компоненты
- Правильное использование React hooks
- Оптимизация производительности (memo, useMemo, useCallback где необходимо)
- Обработка edge cases

### Backend
- MVC или layered architecture (routes, controllers, services, models)
- Middleware для общей логики
- Validation middleware
- Error handling middleware
- Environment variables для конфигурации

---

## Структура проекта

```
task-manager/
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── services/ (API calls)
│   │   ├── hooks/
│   │   ├── utils/
│   │   ├── App.jsx
│   │   └── main.jsx
│   ├── package.json
│   └── vite.config.js (or webpack)
├── backend/
│   ├── src/
│   │   ├── routes/
│   │   ├── controllers/
│   │   ├── models/
│   │   ├── middleware/
│   │   ├── config/
│   │   └── server.js
│   ├── package.json
│   └── .env.example
├── docker-compose.yml (optional)
├── README.md
└── .gitignore
```

---

## Инструкции по запуску

Ваш README.md должен содержать:

1. **Prerequisites:** Node.js версия, база данных
2. **Installation:**
   ```bash
   # Backend
   cd backend
   npm install
   cp .env.example .env
   # настроить .env
   npm run migrate (если есть миграции)
   npm run dev

   # Frontend (в новом терминале)
   cd frontend
   npm install
   npm run dev
   ```
3. **Environment Variables:** список всех необходимых переменных
4. **API Documentation:** основные endpoints
5. **Testing:** как запустить тесты
6. **Docker** (если есть):
   ```bash
   docker-compose up
   ```

---

## Критерии оценки

Проект будет оцениваться по следующим критериям:

### Функциональность (30 баллов)
- [ ] Регистрация и аутентификация работают (8)
- [ ] CRUD операции над задачами (10)
- [ ] Фильтрация и поиск (5)
- [ ] Защита приватных роутов (4)
- [ ] Корректная работа всех фич (3)

### Архитектура и код (25 баллов)
- [ ] Чистая структура проекта (5)
- [ ] Модульность и переиспользуемость (5)
- [ ] Правильное использование паттернов (5)
- [ ] Separation of concerns (5)
- [ ] Читаемость кода (5)

### Frontend качество (20 баллов)
- [ ] Правильное использование React (6)
- [ ] State management (4)
- [ ] UI/UX качество (4)
- [ ] Responsive design (3)
- [ ] Error handling и loading states (3)

### Backend качество (15 баллов)
- [ ] API design (4)
- [ ] Database schema (3)
- [ ] Security implementation (4)
- [ ] Error handling (4)

### Testing (10 баллов)
- [ ] Frontend тесты (5)
- [ ] Backend тесты (5)

### Дополнительно
- [ ] TypeScript (+5)
- [ ] Docker setup (+3)
- [ ] CI/CD (+2)
- [ ] Swagger documentation (+2)
- [ ] Advanced features (+5)

**Максимум:** 100 баллов + 17 бонусных

---

## Сроки и формат сдачи

**Срок выполнения:** 5-7 дней

**Формат сдачи:**
1. GitHub/GitLab repository (публичный или дать доступ)
2. README.md с инструкциями по запуску
3. Working demo (опционально - deploy на Vercel/Railway/Render)

**В README.md укажите:**
- Какие фичи реализованы
- Какие технологии использованы
- Сложности с которыми столкнулись
- Что бы вы улучшили если бы было больше времени

---

## Полезные ресурсы

- [React Documentation](https://react.dev/)
- [Express.js Guide](https://expressjs.com/)
- [JWT Authentication](https://jwt.io/)
- [Prisma ORM](https://www.prisma.io/)
- [React Router](https://reactrouter.com/)

---

## Вопросы?

Если у вас есть вопросы по заданию, не стесняйтесь спрашивать!

Удачи! 🚀
