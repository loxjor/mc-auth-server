# MC Auth Server

Yggdrasil-совместимый сервер авторизации для Minecraft.  
Используется совместно с [MC Launcher](https://github.com/loxjor/minecraft-launcher).

---

## ✨ Возможности

- Регистрация и вход (Yggdrasil API)
- RSA-4096 подпись текстур
- Загрузка и раздача скинов (PNG, по email)
- Дефолтный скин для пользователей без своего
- Смена ника с кулдауном 24 часа
- Хранение данных в JSON (без нативных зависимостей)
- Готов к запуску в Docker

---

## 🚀 Запуск

### Docker (рекомендуется)

Укажи свой IP или домен в `docker-compose.yml`:

```yaml
environment:
  - PUBLIC_HOST=1.2.3.4:3000
```

```bash
docker compose up -d
```

### Без Docker

```bash
npm install
npm start
```

Сервер запустится на `http://localhost:3000`.  
При первом старте автоматически генерируется RSA-4096 пара ключей.

---

## 🔌 API

| Метод | Путь | Описание |
|-------|------|----------|
| GET | `/` | Yggdrasil metadata (authlib-injector) |
| GET | `/api/health` | Проверка работоспособности |
| POST | `/api/register` | Регистрация |
| PUT | `/api/username` | Смена ника (кулдаун 24 ч) |
| POST | `/api/skin` | Загрузка скина (base64 PNG) |
| DELETE | `/api/skin` | Сброс скина |
| GET | `/api/skin/info` | Информация о скине текущего пользователя |
| POST | `/api/skin/default` | Установка дефолтного скина для всех |
| GET | `/skins/:file` | Получение PNG-скина (fallback → default.png) |
| POST | `/authserver/authenticate` | Вход |
| POST | `/authserver/validate` | Проверка токена |
| POST | `/authserver/refresh` | Обновление токена |
| POST | `/authserver/invalidate` | Инвалидация токена |
| POST | `/authserver/signout` | Выход по логину/паролю |
| POST | `/sessionserver/session/minecraft/join` | Присоединение к серверу |
| GET | `/sessionserver/session/minecraft/hasJoined` | Проверка игрока на сервере |
| GET | `/sessionserver/session/minecraft/profile/:uuid` | Профиль игрока |

---

## ⚙️ Переменные окружения

| Переменная | По умолчанию | Описание |
|------------|-------------|----------|
| `PORT` | `3000` | Порт сервера |
| `PUBLIC_HOST` | `localhost:PORT` | Хост для URL скинов и Yggdrasil meta |

---

## 📁 Структура

```
mc-auth-server/
├── assets/
│   └── default.png          # Дефолтный скин
├── src/
│   ├── index.js             # Express-приложение
│   ├── database.js          # JSON-хранилище
│   ├── crypto.js            # RSA-4096 ключи и подпись
│   ├── skins.js             # Работа с PNG-файлами
│   └── routes/
│       ├── api.js
│       ├── authserver.js
│       └── sessionserver.js
├── Dockerfile
├── docker-compose.yml
└── package.json
```

> Данные (`db.json`, ключи, скины пользователей) хранятся в `data/` — не попадают в git.

---

## 📜 Лицензия

[MIT](LICENSE)
