# Zadania19
# Платформа «Сбор работ на конкурс»

Веб-приложение для организации конкурсов студенческих/школьных работ.  
Администратор создаёт конкурсы, участники загружают работы, жюри проверяет и оценивает.

## Роли и права доступа

| Роль          | Может                                                                 | Не может                                      |
|---------------|-----------------------------------------------------------------------|-----------------------------------------------|
| **Участник** (participant) | Создавать/редактировать/подавать свои работы<br>Видеть статус своих работ<br>Добавлять комментарии | Видеть чужие работы<br>Менять статус<br>Управлять конкурсами/пользователями |
| **Жюри** (jury)            | Видеть все работы<br>Менять статус работы<br>Оставлять комментарии<br>Запрашивать доработку | Создавать конкурсы<br>Управлять пользователями |
| **Администратор** (admin)  | Всё выше + создавать/редактировать конкурсы<br>Управлять пользователями и ролями | —                                             |

## Сущности (модели)

- **contests** — конкурсы  
  id, title, description, deadline_at, is_active
- **submissions** — работы  
  id, contest_id, user_id, title, description, status (draft/submitted/needs_fix/accepted/rejected)
- **submission_comments** — комментарии к работам  
  id, submission_id, user_id, body
- **attachments** — прикреплённые файлы  
  id, submission_id, user_id, original_name, mime, size, storage_key, status (pending/scanned/rejected), rejection_reason
- **notifications** — уведомления (опционально, для статуса)

## Бизнес-правила (реализованные)

- Редактирование работы доступно только в статусах `draft` и `needs_fix`
- Подача работы (`submit`) возможна только если есть хотя бы один файл со статусом `scanned`
- Перевод в статус `submitted` автоматически запускает проверку
- Жюри/админ меняет статус через допустимые переходы
- Максимум 3 файла на работу
- Максимальный размер файла — 10 МБ
- Разрешённые форматы: pdf, zip, png, jpg, jpeg
- Все файлы хранятся **только в S3-совместимом хранилище** (MinIO / AWS S3)

## Технологии

- PHP 8.1+
- Laravel 10/11
- MySQL / PostgreSQL / SQLite
- Tailwind CSS + Alpine.js
- Laravel Queues + Redis
- Laravel Storage (S3 driver + MinIO)
- Laravel Notifications (база + email — опционально)

## Установка и запуск (полная инструкция)

- Настройка .env:
DB_CONNECTION=mysql
DB_HOST=MySQL-8.0
DB_PORT=3306
DB_DATABASE=konkyrs (название бд)
DB_USERNAME=root
DB_PASSWORD=

FILESYSTEM_DISK=s3

# Beget S3 Configuration
AWS_ACCESS_KEY_ID=L9IOBCEF0DVY5VPQNBOH
AWS_SECRET_ACCESS_KEY=B8M304AkKBaxDFVEIdU1xo0KYDRsd6VCaB0hjToZ
AWS_DEFAULT_REGION=ru-1
AWS_BUCKET=108441407d0b-college
AWS_ENDPOINT=https://s3.ru1.storage.beget.cloud
AWS_USE_PATH_STYLE_ENDPOINT=true
AWS_URL=https://108441407d0b-college.s3.ru1.storage.beget.cloud

- Миграции:
php artisan migrate

- Сидеры:
php artisan db:seed

- Запуск сервер:
php artisan serve

- Запуск очереди (обязательно!):
php artisan queue:work

Загруженную картинку можно найти в БД в attachments

Сделал Никита Цепко Ряльно я 
