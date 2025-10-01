# Инструкция для Павла: YClients Парсер

## Что делает система

Ваша система автоматически парсит страницы бронирования YClients и извлекает данные:
- **Дата** бронирования
- **Время** бронирования
- **Цена** услуги
- **Провайдер** (исполнитель)

Данные сохраняются в вашей базе Supabase и обновляются каждые 10 минут автоматически.

## Ваш API ключ

```
yclients_parser_secure_key_2024
```

## Как проверить систему

### 1. Проверить статус системы

```bash
curl -H "X-API-Key: yclients_parser_secure_key_2024" \
     https://server4parcer-parser-4949.twc1.net/status
```

**Что увидите:**
```json
{
  "status": "success",
  "data": {
    "booking_records": 123,  // количество собранных записей
    "url_records": 6,        // количество настроенных URL
    "database_type": "supabase",
    "connected": true
  }
}
```

### 2. Посмотреть собранные данные

```bash
curl -H "X-API-Key: yclients_parser_secure_key_2024" \
     "https://server4parcer-parser-4949.twc1.net/data?limit=10"
```

**Что увидите:** Список бронирований с датами, временем, ценами.

### 3. Запустить парсинг вручную

```bash
curl -X POST -H "X-API-Key: yclients_parser_secure_key_2024" \
     "https://server4parcer-parser-4949.twc1.net/parse/all?active_only=false"
```

**Что увидите:**
```json
{
  "status": "success",
  "message": "Парсер запущен для 6 URL"
}
```

Подождите 2-3 минуты и проверьте данные снова.

### 4. Посмотреть настроенные URL

```bash
curl -H "X-API-Key: yclients_parser_secure_key_2024" \
     https://server4parcer-parser-4949.twc1.net/urls
```

## ⚠️ Важно!

### Используйте curl с header, НЕ браузер!

**❌ НЕ РАБОТАЕТ:**
- Открыть URL в браузере
- Простой curl без header

**✅ РАБОТАЕТ:**
- Curl с `-H "X-API-Key: ваш-ключ"`
- Postman с header "X-API-Key"

### Почему браузер не работает?

Браузер не отправляет заголовок X-API-Key автоматически, поэтому вы видите ошибку:
```json
{"detail":"API-ключ не предоставлен"}
```

## Как добавить новый URL для парсинга

```bash
curl -X POST -H "X-API-Key: yclients_parser_secure_key_2024" \
     -H "Content-Type: application/json" \
     -d '{"url":"https://ваш-новый-url.yclients.com/..."}' \
     https://server4parcer-parser-4949.twc1.net/urls
```

## Автоматическое обновление

Парсер работает автоматически:
- **Интервал**: каждые 10 минут
- **Проверка**: контейнер запускается на TimeWeb
- **Данные**: сохраняются в ваш Supabase

## Где посмотреть данные

### Вариант 1: Через API (как показано выше)

### Вариант 2: В Supabase Dashboard

1. Откройте https://supabase.com/dashboard
2. Войдите в свой проект (tfvgbcq...)
3. Table Editor → booking_data
4. Увидите все собранные записи

## Если что-то не работает

### Проблема: "API-ключ не предоставлен"

**Причина:** Забыли добавить header
**Решение:** Добавьте `-H "X-API-Key: yclients_parser_secure_key_2024"`

### Проблема: Нет данных (booking_records: 0)

**Причина:** Парсер не может сохранить данные
**Решение:**
1. Запустите парсинг вручную (команда выше)
2. Подождите 2 минуты
3. Проверьте статус снова
4. Если все еще 0 - проверьте Supabase permissions

### Проблема: 502 Bad Gateway

**Причина:** Контейнер перезапускается после обновления
**Решение:** Подождите 2-3 минуты

## Контакты

Если возникли проблемы, отправьте:
1. Команду curl которую используете
2. Ошибку которую видите
3. Скриншот если нужно

Система настроена и готова к работе! 🎉
