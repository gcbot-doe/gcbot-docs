# General Contractor Building

© 2025 ООО «МОСПРОЕКТ-2». Все права защищены.
### Смотрите файл [LICENSE](./LICENSE) для подробностей.

```commandline
gcbot/
├── bot/
│   ├── db/
│   │   ├── __init__.py          # session, get_db()
│   │   ├── models.py            # ORM-модели
│   │   ├── run_db.py            # Создает таблицы, если их еще нет
│   │   └── crud.py              # вспом. функции (get_or_create_user, add_payment …)
│   ├── payments/
│   │   ├── __init__.py          # session, get_db()
│   │   ├── ukassa_service.py   # создание платежа + проверка статуса
│   │   └── poller.py           # Фоновый цикл: каждые 30 секунд опрашиваем платежи с статусом 'pending'
│   ├── __init__.py             # Инициализация бота и импорт команд
│   ├── config.py               # Конфигурация бота (токен и настройки)
│   ├── handlers/
│   │   ├── __init__.py         # Инициализация обработчиков
│   │   ├── commands.py         # Обработчик команд
│   │   └── documents.py        # Обработчик загрузки документов
│   ├── utils/
│   │   ├── __init__.py         # Инициализация утилит
│   │   ├── cswv.py             # Формирование СВОР-файла
│   │   ├── compare.py          # Функция для сравнения файлов Excel
│   │   ├── bring_in_line.py    # Функция для приведения в соответствие кодов расценок в сметах
│   │   ├── ollama_gpt.py       # чат-бот на весах qven
│   │   └── pdf_compare.py      # Сравнение двух pdf-документов
│   └── main.py                 # Главный файл для запуска бота
├── docs/
│   ├── index.md                # краткое описание, функционал, инструкции
│   └── pricing.md              # информация о стоимости или что ПО распространяется
├── .env
├── Dockerfile
├── docker-compose.yml
├── README.md               # Описание проекта и инструкции
└── requirements.txt        # Зависимости проекта

```
# Telegram Excel Comparison Bot

Этот бот для Telegram принимает любое количество Excel файлов по команде `/get_cswv` и на их основе выдает документ СВОР, 
сравнивать PDF-файлы (`/compare_pdf`) с использованием OCR.

## Описание
Бот использует `pytelegrambotapi` и `pandas` для работы с файлами и выполнением проверки на совпадение данных в таблицах.

## Установка и настройка

1. Клонируйте репозиторий:
   ```bash
   git clone https://github.com/yourusername/telegram_excel_bot.git
   cd telegram_excel_bot
   pip install -r requirements.txt
   python bot/main.py
   ```
2. Создайте .env файл и укажите токен бота:
```commandline
BOT_TOKEN=your_telegram_bot_token
```
3. Запустите бота:
```commandline
python -m bot.main
```
Использование Docker
### Установка Docker и Docker Compose

Выполните следующие команды на сервере:

```bash
# Обновляем пакеты
sudo apt-get update

# Устанавливаем зависимости
sudo apt-get install -y ca-certificates curl gnupg

# Добавляем GPG-ключ Docker
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Добавляем репозиторий Docker в apt
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo $VERSION_CODENAME) stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Обновляем индексы пакетов и устанавливаем Docker
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Проверить успешную установку:
docker -v
docker compose version
```
#### Подготовка проекта

#### Создайте ключи доступа к Гитхабу на сервере и назовите его sshkey:
```
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```
#### Копируйте ключ аккаунт на Гитхабе:
```
cat sshkey.pub
```
Переместите ваши ключи, которые вы назвали "sshkey" в ~/.ssh/:
```
mkdir -p ~/.ssh
mv ~/sshkey ~/.ssh/id_rsa
mv ~/sshkey.pub ~/.ssh/id_rsa.pub
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
```
#### Клонируйте репозиторий проекта на сервер:
```
bash
git clone https://github.com/gcbot-doe/gcbot.git
# Или через ssh:
git clone git@github.com:gcbot-doe/gcbot.git 
cd gcbot
```
Создайте файл .env в корне проекта (рядом с docker-compose.yml), если его нет. В .env должны быть ваши секреты:
```
.env
BOT_TOKEN='123:bot_token'
API_KEY='your_api_key'
PORT=50906
```
Соберите docker-образ:
```
docker compose build
docker compose up -d
docker compose ps
```

Если возникли проблемы с docker-файлом:
```commandline
sudo apt-get install -y tesseract-ocr libtesseract-dev
```


Структура проекта

* bot/config.py - Конфигурация бота.
* bot/handlers/commands.py - Обработчик команд, таких как /start и /compare.
* bot/handlers/documents.py - Обработчик для загрузки и обработки файлов.
* bot/utils/compare.py - Функция для сравнения данных в Excel.
* bot/main.py - Главный файл для запуска бота.

