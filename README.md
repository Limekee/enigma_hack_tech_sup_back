# Автоматизированная система обработки обращений службы поддержки газовой службы с AI, NLP и асинхронной архитектурой.

🎯 Описание проекта
Кейс: Создание автоматизированной системы для службы поддержки газовой службы.

# Dataflow
Пользователь → IMAP → NLP → AI(RAG) → PostgreSQL → Redis → Telegram Bot → Frontend → SMTP → Пользователь
  1)Пользователь пишет письмо с ФИО, телефоном, email, объектом, заводским номером газоанализатора
  2)Backend поллит IMAP-сервер, получает входящие сообщения
  3)NLP-модуль извлекает сущности (Natasha/Regex), определяет сентимент (Dostoevsky), суммирует (Sumy)
  4)AI (через API) генерирует ответ используя RAG базу знаний
  5)PostgreSQL сохраняет данные тикета
  6)Redis Pub/Sub уведомляет Telegram-бот
  7)Сотрудник редактирует/подтверждает на фронте
  8)SMTP отправляет ответ пользователю
# Userflow
  -Сотрудник получает уведомления в Telegram
  -Работает с тикетами(которые предварительно были обработаны нашей llm) на Frontend (редактирование, аналитика, отправка)

# Ключевые принципы:
  -Decoupling через Redis Pub/Sub
  -Async I/O для высокой производительности
  -Type Safety с Pydantic моделями
  -Containerization через Docker

# Локальный запуск
bash
  // Клонировать репозиторий
  git clone https://github.com/Limekee/enigma_hack_tech_sup_back.git
  cd enigma_hack_tech_sup_back
  
  //Копировать и настроить .env
  cp .env.example .env
  //Настроить DATABASE_URL, REDIS_URL, IMAP/SMTP credentials
  
  //Запуск через Docker
  docker-compose up -d
  
  //Или локально
  pip install -r requirements.txt
  uvicorn app.main:app --reload --host 0.0.0.0 --port 8000

# API Документация
После запуска доступна по адресу: http://localhost:8000/docs

# Основные API эндпоинт
| Метод | Эндпоинт                  | Описание                |
| ----- | ------------------------- | ----------------------- |
| POST  | /api/v1/tickets/          | Создать тикет из письма |
| GET   | /api/v1/tickets/          | Список тикетов          |
| GET   | /api/v1/tickets/{id}      | Тикет по ID             |
| PUT   | /api/v1/tickets/{id}      | Обновить тикет          |
| POST  | /api/v1/tickets/{id}/send | Отправить ответ         |
| GET   | /api/v1/analytics/        | Статистика              |

# Docker Compose сервисы
services:
  backend:     //FastAPI приложение
  db:          //PostgreSQL
  redis:       //Pub/Sub брокер
  

# Мониторинг и логи
Docker логи
docker-compose logs -f backend

FastAPI логи в debug режиме
LOG_LEVEL=debug uvicorn app.main:app --reload
