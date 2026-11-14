https://github.com/Vero-bho/deploy_noir-bijoux.git
https://github.com/Vero-bho/MY_Noir_Bijoux.git

# Архитектура проекта:
Репозитории приложения и деплоя хранятся отдельно. При локальном запуске каталоги noir-bijoux и deploy_noir-bijoux должны находиться рядом. В docker-compose.yml репозитория деплоя сборка backend и frontend настроена из соседней папки приложения ../noir-bijoux/backend и ../noir-bijoux/frontend.

# Сервисы и порты:
Сервис nginx использует технологию/образ Nginx (образ `public.ecr.aws/nginx/nginx:mainline-alpine-amd64`). Внутри контейнера или как внешний порт сервиса используются порты 80 и 443. На компьютере публикация настроена так: `127.0.0.1:80` перенаправляется на 80, а `127.0.0.1:443` — на 443.

Сервис frontend построен на React, Vite, React Router и Node.js. Внутри контейнера используется порт 3000. Отдельно он не опубликован, но доступен через Nginx.

Сервис backend использует Node.js, Express и Prisma. Внутри контейнера используется порт 4000. Отдельно он не опубликован, но доступен через Nginx.

Сервис Supabase PostgreSQL представляет собой внешнюю управляемую PostgreSQL. Подключения задаются через переменные `DATABASE_URL` и `DIRECT_URL`. В Docker Compose он не запускается, и порт на компьютере не публикует.

Сервис Supabase Storage — это внешнее объектное хранилище. Использует HTTPS и порт 443. В Docker Compose не запускается.

# Переменные окружения и секреты:
Backend: DATABASE_URL (секрет), DIRECT_URL (секрет), PORT, CORS_ORIGIN, NODE_ENV.
Frontend: VITE_API_URL (аргумент сборки), HOST, PORT, NODE_ENV.
Шаблоны переменных находятся в backend/.env.example и frontend/.env.example репозитория приложения. Настоящие значения подключений хранятся локально в backend/.env.

# Команды запуска и остановки:
docker compose config --quiet
docker compose up --build -d
docker compose ps
docker compose logs --tail=100 backend frontend nginx

Для остановки контейнеров:
docker compose down

# Что изменить для публикации на реальном сервере?:
Привязать домен к IP сервера, открыть порты 80/443, настроить Nginx и сертификат Let’s Encrypt, обновить `CORS_ORIGIN` и безопасно задать секреты. Настроить сборку приложения на сервере или загрузку Docker-образов.
