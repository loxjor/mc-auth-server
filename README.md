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

## 🚀 Установка и запуск

### Вариант 1 — Docker (рекомендуется)

Самый простой способ. Подходит для любого Linux VPS.

#### Требования
- [Docker](https://docs.docker.com/engine/install/) + [Docker Compose](https://docs.docker.com/compose/install/) (входит в Docker Desktop)

#### 1. Скачай репозиторий

```bash
git clone https://github.com/loxjor/mc-auth-server.git
cd mc-auth-server
```

#### 2. Укажи свой IP или домен

Открой `docker-compose.yml` и замени значение `PUBLIC_HOST`:

```yaml
environment:
  - PORT=3000
  - PUBLIC_HOST=1.2.3.4:3000   # ← твой внешний IP или домен (напр. auth.example.com:3000)
```

> Это нужно для того, чтобы Minecraft мог загружать скины — он обращается по этому адресу.

#### 3. Запусти

```bash
docker compose up -d
```

#### 4. Проверь

```bash
curl http://localhost:3000/api/health
# → {"status":"ok","time":"..."}
```

#### Управление

```bash
docker compose down          # остановить
docker compose logs -f       # смотреть логи
docker compose up -d --build # перезапустить после обновления
```

#### Обновление до новой версии

```bash
git pull
docker compose up -d --build
```

> **Данные не теряются** — `db.json`, RSA-ключи и скины пользователей хранятся в папке `./data/`, которая монтируется как volume.

---

### Вариант 2 — Без Docker (Node.js)

#### Требования
- [Node.js](https://nodejs.org/) 18 или новее

#### 1. Скачай репозиторий

```bash
git clone https://github.com/loxjor/mc-auth-server.git
cd mc-auth-server
```

#### 2. Установи зависимости

```bash
npm install
```

#### 3. Укажи свой хост (если сервер публичный)

Создай файл `.env` в корне проекта:

```env
PORT=3000
PUBLIC_HOST=1.2.3.4:3000
```

#### 4. Запусти

```bash
npm start
```

Сервер запустится на `http://localhost:3000`.  
При первом старте автоматически генерируются RSA-4096 ключи.

---

## 🔧 Подключение лаунчера

1. Запусти лаунчер
2. На экране входа нажми **«Изменить»** рядом с адресом сервера
3. Введи `http://ВАШ_IP:3000` (или домен)
4. Зарегистрируйся и войди

---

## ⚙️ Переменные окружения

| Переменная | По умолчанию | Описание |
|------------|-------------|----------|
| `PORT` | `3000` | Порт сервера |
| `PUBLIC_HOST` | `localhost:PORT` | Хост для URL скинов и Yggdrasil meta. Должен быть доступен с машины игрока |

---

## 🔌 API

<details>
<summary>Показать все эндпоинты</summary>

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

</details>

---

## 📁 Структура

```
mc-auth-server/
├── assets/
│   └── default.png          # Дефолтный скин
├── src/
│   ├── index.js
│   ├── database.js
│   ├── crypto.js
│   ├── skins.js
│   └── routes/
│       ├── api.js
│       ├── authserver.js
│       └── sessionserver.js
├── Dockerfile
├── docker-compose.yml
└── package.json
```

> `data/` создаётся автоматически при первом запуске и не попадает в git.

---

## 📜 Лицензия

[MIT](LICENSE)
