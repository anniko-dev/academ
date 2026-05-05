# Academist

Платформа для выбора вуза, специальности и профессии с навигатором поступления, тестами и контентным разделом.

- Прод: [academist.ru](https://academist.ru/)
- API: `https://api.academist.ru/api`
- CMS: `https://cms.academist.ru`

## Что внутри

Репозиторий объединяет 3 отдельных сервиса:

| Сервис | Путь | Технологии | Назначение |
|---|---|---|---|
| Frontend | `edu_client` | Next.js 13, React 18, TypeScript, Zustand, MUI | Пользовательский интерфейс платформы |
| Backend API | `edu_server` | NestJS, TypeORM, PostgreSQL, Swagger | Бизнес-логика, авторизация, данные по вузам/направлениям/тестам |
| CMS | `edu_news` | Strapi v4 | Контент для журнала и SEO-данные |

## Основные возможности продукта

- Поиск и сравнение вузов.
- Просмотр специальностей, профилей и условий поступления.
- Подбор профессий и профориентационные тесты.
- Контентный раздел (журнал, статьи, новости, теги, категории).
- Личный кабинет пользователя.

## Архитектура

1. `edu_client` обращается к `edu_server` по REST (`/api/...`) для продуктовых данных и пользовательских сценариев.
2. `edu_client` получает медиа и контент из `edu_news` (Strapi).
3. `edu_server` работает с PostgreSQL и отдает Swagger-документацию по пути `/doc`.

## Структура репозитория

```text
academist/
├── edu_client/   # Next.js frontend
├── edu_server/   # NestJS API
└── edu_news/     # Strapi CMS
```

Важно: `edu_client`, `edu_server`, `edu_news` подключены как gitlink (вложенные git-репозитории).  
Для корректного клонирования используйте рекурсивный режим.

## Требования к окружению

- `Node.js` 18 LTS (рекомендуется для совместимости всех сервисов, особенно Strapi).
- `npm` 9+.
- `PostgreSQL` 13+ для `edu_server`.
- Linux/macOS/WSL2.

Пример через `nvm`:

```bash
nvm install 18
nvm use 18
node -v
npm -v
```

## Быстрый старт (локально)

### 1) Клонирование

```bash
git clone --recurse-submodules <repo-url>
cd academist
```

Если уже клонировали без `--recurse-submodules`:

```bash
git submodule update --init --recursive
```

### 2) Установка зависимостей

```bash
npm --prefix edu_client install
npm --prefix edu_server install
npm --prefix edu_news install
```

### 3) Настройка переменных окружения

#### `edu_client`

Используются переменные:

- `NODE_ENV`
- `API_PROTOCOL`
- `API_SERVER_IP`
- `API_SERVER_PORT`
- `ANALYZE` (опционально, для bundle analyzer)

`edu_client/src/services/http.ts` ожидает:

- dev API: `http://localhost:8000/api`
- prod API: `https://api.academist.ru/api`

#### `edu_server`

Используются переменные:

- `PORT`
- `CLIENT` (origin для CORS, например `http://localhost:3000`)
- `DATABASE_HOST`
- `DATABASE_USER`
- `DATABASE_PASSWORD`
- `DATABASE_NAME`
- `MAIL_EMAIL`
- `MAIL_PASSWORD`

#### `edu_news`

Базовый `.env.example` уже есть в `edu_news/.env.example`.

Ключевые переменные:

- `HOST`
- `PORT`
- `APP_KEYS`
- `API_TOKEN_SALT`
- `ADMIN_JWT_SECRET`
- `JWT_SECRET`
- `DATABASE_CLIENT`
- `DATABASE_HOST`
- `DATABASE_PORT`
- `DATABASE_NAME`
- `DATABASE_USERNAME`
- `DATABASE_PASSWORD`

### 4) Запуск сервисов

Откройте 3 терминала:

```bash
# Terminal 1 - CMS (Strapi)
npm --prefix edu_news run develop
```

```bash
# Terminal 2 - API (NestJS)
npm --prefix edu_server run start:dev
```

```bash
# Terminal 3 - Frontend (Next.js)
npm --prefix edu_client run dev
```

Локальные адреса по умолчанию:

- Frontend: `http://localhost:3000`
- API: `http://localhost:8080/api` (или порт из `PORT`)
- Swagger: `http://localhost:8080/doc`
- CMS: `http://localhost:1337`

## Скрипты разработки

### `edu_client`

```bash
npm --prefix edu_client run dev
npm --prefix edu_client run build
npm --prefix edu_client run start
npm --prefix edu_client run lint
npm --prefix edu_client run lint:fix
npm --prefix edu_client run format
npm --prefix edu_client run format:check
```

### `edu_server`

```bash
npm --prefix edu_server run start:dev
npm --prefix edu_server run build
npm --prefix edu_server run start:prod
npm --prefix edu_server run lint
npm --prefix edu_server run test
```

### `edu_news`

```bash
npm --prefix edu_news run develop
npm --prefix edu_news run build
npm --prefix edu_news run start
```

## База данных и миграции (`edu_server`)

Проект использует TypeORM. Доступные команды:

```bash
npm --prefix edu_server run migrate:run
npm --prefix edu_server run migrate:generate -- <MigrationName>
npm --prefix edu_server run migrate:revert
```

## Контентная модель (`edu_news`)

Основные коллекции Strapi:

- `post`
- `category`
- `tag`
- `post-type`

Плагины:

- `@strapi/plugin-i18n`
- `@strapi/plugin-seo`
- `strapi-plugin-transformer`

## Деплой и домены

- UI: [academist.ru](https://academist.ru/)
- API: `api.academist.ru`
- CMS: `cms.academist.ru`

Для production-сборки frontend ориентируется на `https://api.academist.ru/api`.

## Частые проблемы

- Ошибки при установке `sharp`/native модулей: используйте Node 18 LTS и чистую установку зависимостей.
- CORS ошибки: проверьте значение `CLIENT` в `edu_server/.env`.
- Пустые данные на фронте: убедитесь, что одновременно запущены API и CMS.

## Лицензирование

Внутренний проект команды PLATORA/Academist.  
Условия распространения определяются владельцем репозитория.

