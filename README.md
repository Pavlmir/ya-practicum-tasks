# Решение

1. Выбор системы контроля версий
Для проведения ревью кода можно использовать GitHub, GitLab или Bitbucket. Я предпочту GitHub, так как он широко используется и имеет удобный интерфейс для проведения код-ревью.

2. Полезные ссылки для изучения темы
Для студента, работающего над миграцией данных и созданием микросервиса, полезными будут следующие ресурсы:

- Elasticsearch:<br>
[Официальная документация Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html)<br>
[Elasticsearch для начинающих](https://www.youtube.com/watch?v=nMaDw6Dk14k)<br>

- SQLite:<br>
[Официальная документация SQLite](https://www.sqlite.org/docs.html)<br>
[Руководство по SQLite](https://metanit.com/sql/sqlite/)<br>
[SQLite JOIN](https://www.sqlitetutorial.net/sqlite-join/)<br>

- Flask:<br>
[Официальная документация Flask](https://flask.palletsprojects.com/en/stable/)<br>
[Flask Mega-Tutorial](https://habr.com/ru/articles/804245/)<br>
[Flask Logging](https://flask.palletsprojects.com/en/2.0.x/logging/)<br>

- ETL процессы:<br>
[ETL: что это такое и как работает](https://practicum.yandex.ru/blog/chto-takoe-etl/)<br>
[ETL Best Practices](https://tapdata.io/articles/etl-best-practices-comparing-reviewing-different-approaches/)<br>

- Python: <br>
[Переменные окружения в Python](https://docs.python.org/3/library/os.html#os.getenv)
[PEP 8 – стиль кода](https://peps.python.org/pep-0008/)

3. Фидбек студенту
   1. Задача миграции данных между хранилищами<br>
    Код рабочий, но нуждается в оптимизации и обработке ошибок. После исправлений станет гораздо надёжнее и быстрее.
        + Что сделано хорошо:<br>
            * Код достаточно понятен и логично разбит на extract, transform, load.
            * Используется sqlite3, json и bulk из elasticsearch.helpers, что правильно для загрузки данных.
            * Данные очищаются от “N/A”.

        + Можно улучшить:<br>
            * Запросы в SQLite неэффективны и могут приводить к множеству запросов (N+1 проблема).
            * Код плохо обрабатывает ошибки и исключения.
            * Не хватает логирования (print не подходит для продакшена).
            * Переменные с __ в начале (__actors, __writers, __raw_data) не соответствуют стандартам PEP8.

        + Что исправить:<br>
            1. Переписать SQL-запрос, используя JOIN, вместо GROUP_CONCAT в подзапросе.
            2. Добавить обработку ошибок в transform.
            3. Сделать параметры подключения к ES через переменные окружения.
            4. Добавить логирование вместо print.

    2. Задача создания сервиса для поиска по базе фильмов<br>
    Код рабочий, но требует оптимизации и улучшения поиска. После исправлений API станет быстрее и надёжнее.
        + Что сделано хорошо:<br>
            * API реализовано и доступно по http://localhost/api/movies/.
            * Код относительно чистый, без лишнего функционала.
            * Используется Elasticsearch.search() с параметрами body и params.

        + Можно улучшить:<br>
            * Ошибка валидации параметров запроса. Если параметр search отсутствует, в body остаётся None, что может привести к ошибке.
            * Неэффективная работа с Elasticsearch. Клиент создаётся внутри каждого запроса, что замедляет работу.
            * Фильтрация поиска ограничена только title. В тестах Postman ищут по description, genre, actors_names, writers_names и director.
            * Жёстко зашитый IP-адрес 192.168.11.128. Код не будет работать на других машинах.
            * Нет логирования ошибок. Если Elasticsearch не отвечает, в коде просто print('oh('), но это не поможет в продакшене.

        + Что исправить:<br>
	        1. Использовать переменные окружения для подключения к Elasticsearch.
            2.	Создать клиент Elasticsearch один раз при старте Flask.
            3.	Расширить поиск по title, description, genre, actors_names, writers_names, director.
            4.	Добавить логирование ошибок.
            5.	Исправить валидацию параметров запроса.