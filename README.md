<h1 align="center"><a href="https://git.io/typing-svg"><img src="https://readme-typing-svg.herokuapp.com?font=Oswald&size=40&pause=1000&color=000000&random=false&width=650&height=65&lines=%D0%A7%D0%B0%D1%82-%D0%B1%D0%BE%D1%82+%D0%B4%D0%BB%D1%8F+%D0%BE%D1%81%D0%BC%D0%BE%D1%82%D1%80%D0%B0+%D1%81%D1%82%D1%80%D0%B0%D1%85%D0%BE%D0%B2%D1%8B%D1%85+%D0%BE%D0%B1%D1%8A%D0%B5%D0%BA%D1%82%D0%BE%D0%B2" alt="Typing SVG" /></a>
<h3 align="center">Описание</h3>

<div align="justify">Чат-бот, разработанный в рамках кейс-чемпионата от Совкомбанка, предназначен для упрощения процесса осмотра страхуемого имущества клиентами. Благодаря использованию технологий компьютерного зрения, чат-бот позволяет клиенту без специальных экспертных навыков осуществить осмотр имущества и получить оценку его стоимости. Решение включает в себя функции распознавания наличия автомобиля, лобового стекла и читаемого VIN номера на предоставленных фотографиях.</div>
<h3 align="center">Схема сервисов</h3>
<p align="center">
  <img width="600" height="250" src="image-3.png">
</p>
<h3 align="center">Содержимое</h3>

   
<ol>
<li> <b> data</b> 
<ul>
<li>databases.py</li>
<li>logo.jpg</li>
<li>own_token.py</li>
<li>states.py</li></ul>

```ruby
class User(StatesGroup):
    name = State()
    surname = State()
    mail = State()
    login = State()
    password = State()
    phone = State()
    fill_db = State()
```

</li>
<li><b>handlers</b></li>
<ul>
<li>admin...py</li>
<li>heicjpg.py</li>
<li>order...py</li>
<li>status...py</li>
<li>registration...py</li>
<li>authorization...py</li>
<li>history...py</li>
<li>bot_messages...py</li>
<li>user_commands...py</li>
</ul>
<li><b>img</b></li>
<li><b>keyboards</b>
<ul>
<li>fabrics.py</li>
<li>inline.py</li>
<li>reply.py</li>
</ul>
</li>
<li><b>model</b>
<ul>
<li>IMAGIN</li>
<li>modules</li>
<li>veh_detec</li>
</ul>
</li>
<li><b>pictures</b></li>
<li><b>QR</b></li>
</ol>
<h3 align="center">Технологии</h3>
Проект разработан с использованием:
<ul> 
<li> aiogram для работы с Telegram API
<li> io, pillow_heif для обработки изображений
<li> sqlite3 для хранения данных пользователей
<li> cv2, easyocr для распознавания объектов и текста на изображениях
<li> torch, torchvision для моделей глубокого обучения
</ul>

```ruby
Фото хорошего разрешения
Metadata: {'imageTakenTime': '2023:10:15 07:25:14', 'geolocation_lat: 59.8720555', 'geolocation_lng': 30.367008}
['OPEL 0482001711670094 WOLOAHL6785065544 71870 K9 3145 170955 K9 220955 20R XXL0 8']
```

 <h3 align="center">Логгирование работы</h3>

```ruby
import sqlite3
import binascii
import os
import hashlib
from PIL import Image
from aiogram import Bot
from aiogram.types import Message
import base64
if not os.path.exists('data'):
    os.makedirs('data')
from config_reader import config
bot = Bot(config.bot_token.get_secret_value())
conn = sqlite3.connect('data/database', check_same_thread=False)
cursor = conn.cursor()
import sqlite3
def db_table_val(user_id: int, name: str, user_surname: str, mail: str, password: str):
    cursor.execute('INSERT INTO users (name, user_surname, mail, password) VALUES (?, ?, ?, ?)',
                   (name, user_surname, mail, password))
    conn.commit()
def convert_to_binary_data(filename):
    with open(filename, 'rb') as file:
        blob_data = file.read()
    return blob_data
def insert_blob(photo):
    try:
        conn = sqlite3.connect('data/database', check_same_thread=False)
        cursor = conn.cursor()
        print("Подключен к SQLite")
        
        sqlite_insert_blob_query = """INSERT INTO orders
                                  (photo) VALUES (?)"""

        emp_photo = convert_to_binary_data(photo)

        data_tuple = emp_photo
        cursor.execute(sqlite_insert_blob_query, data_tuple)
        conn.commit()
        print("Изображение и файл успешно вставлены как BLOB в таблиу")
        cursor.close()

    except sqlite3.Error as error:
        print("Ошибка при работе с SQLite", error)
    finally:
        if conn:
            print("Соединение с SQLite закрыто")
```

<h2 align="center"> Машинное обучение</h2>

<h3 align="center"> Установка и запуск</h3>
Для запуска бота необходимо выполнить следующие шаги:

<ol>
<li> Зарегистрировать бота через <a href = 'https://t.me/BotFather'>@BotFather </a> в Telegram и получить уникальный токен. </li>
<li> Вставить полученный токен в файл конфигурации ".env". </li>

```ruby
#Put your token here BOT_TOKEN = ''
```

<li> Инициализация бота bot.py 
</li>
</ol>

```ruby
import asyncio
import logging
import sys

from aiogram import Bot, Dispatcher
from data.own_token import token
from handlers import bot_messages, user_commands, registration, authorization, order_auto, status_auto, order_house, status_house, history, admin, registration_insurer, authorization_insurer, orders_insurer

from config_reader import config

async def main():
    bot = Bot(config.bot_token.get_secret_value(), parse_mode = "HTML")
    dp = Dispatcher()

    dp.include_routers(
        user_commands.router,
        registration.router,
        registration_insurer.router,
        authorization.router,
        authorization_insurer.router,
        status_house.router,
        status_auto.router,
        order_auto.router,
        order_house.router,
        admin.router,
        history.router,
        orders_insurer.router,
        bot_messages.router
    )
    await bot.delete_webhook(drop_pending_updates = True)
    await dp.start_polling(bot)

if name == 'main':
    logging.basicConfig(level = logging.INFO, stream = sys.stdout)
    asyncio.run(main())
``` 


При первом запуске будет автоматически инициализирована база данных sqlite3.


<h3 align="center">Примеры использования</h3>
Пользователи могут отправлять фотографии для осмотра через чат-бота. Бот автоматически определит наличие автомобиля, лобового стекла и VIN номера, предоставив соответствующую обратную связь и оценку.
<p align="center">
  <img width="600" height="250" src="image-2.png">
</p>

<h3 align="center">Как внести вклад</h3>
<ul>
<li>Усовершенствование системы безопасности</li>
<li>Расширение функционала чат-бота</li>
<li>Переход на более совершенную СУБД</li>
</ul>
Мы открыты к предложениям по расширению функционала, особенно в области Machine Learning для адаптации бота к осмотру загородных домов. Для внесения предложений, пожалуйста, свяжитесь с нами.

<h3 align="center">Контакты</h3>
Если у вас есть вопросы или предложения, свяжитесь с нами:

Telegram: [@mishantique](https://t.me/mishantique)

Email: michvitko@mail.ru
