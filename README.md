# parser_google_news

## Описание
Парсер новостей РБК с добавлением и обновлением куки пользователей


### Технологии
``` 
Python 3.10
SQLITE
requests
selenium
bs4
random
```
# инструкция по запуску и настройке

## в терминале выполнить команду:
git clone git@github.com:OlegZhigulin/parser_rbc_news.git
## создать виртуальное окружение:
python -m venv venv
## установить зависимости:
pip install -r requirements.txt
## запустить парсер:
python parser.py

# Алгоритм работы парсера:
``` 
После запуска создается база данных и 15 пользователей
Парсится главная страница и сохраняются все новости в lists_news
Потом из списка новостей lists_news парсится список урлов urls_lists
Создается массив кортежей data из всех пользователей и случайного урла
Запускается пул процессов с функцией get_request и data
Где функция get_request принимает прифиль и случайный урл
Создается сессия в нее добавляем куки, рандомная задержка, переход на случайный урл, пролистывание, получение куки и обновление в бд
Так как куки в бд хранятся в виде строки, парсим их методом ast.literal_eval() сессия закрывается
```

# Техническое задание
Задача:
Создать проект на базе SQLite,Requests,Selenium,Multiprocessing.
Скрипт будет запускаться ежедневно и перезаписывать устаревшие Cookie.
Получаем свежие новости из google, создаем процесс на каждый профиль из бд, создаем сессию используя уже имеющиеся Cookie, переходим и листаем новости после чего добавляем новые Cookie в базу данных.

Модуль SQLite
- Создаем базу данных Profile
- Создаем таблицу Cookie Profile:
* Уникальной id для каждой строки (Not NULL)
* Дата и время создания записи (Not NULL)
* Значения Cookie
* Дата и время последнего запуска
* Кол-во всего запусков
- Заполняем таблицу 15 значениями (id, текущая дата и время создания)

Модуль Requests
- GET запросом получаем содержимое страницы ('https://www.rbc.ru/')
- Собираем в массив, ссылки на новости ( пример : https://www.rbc.ru/politics/28/05/2023….)
- Если есть проблемы с обрывом соединения, используйте VPN
- Если есть проблемы с get запросом, попробуйте использовать UserAgent вашего браузера

Модуль Selenium
- Создаем сессию (имеющиеся Cookie, добавляем сессии)
- Переходим по рандомной ссылке из массива модуля Requests
- Прокручиваем страницу с рандомной задержкой
- Сохраняем Cookie в SQLite (обновляем значения профиля)
- Закрываем сессию

Модуль Multiprocessing
- Собираем профиля из таблицы Cookie Profile (кол-во процессов)
- Используем Pool для создания процессов на каждый профиль
- Ограничение 5 одновременных процессов
