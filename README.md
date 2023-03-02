Get stats from wordstat (weekly, monthly), no API
## Система сбора статистики запросов по ключевым словам в системе wordstat

### Цели и задачи
Цель системы состоит в регулярном (еженедельно, ежемесячно) сборе статистики по запросам по ключевым словам (список от пользователей) и доступа к этой информации с помощью систем визуализации (powerBI) или других систем.<br>
Основной потребитель данных – аналитики, маркетинг.

### Технологический стек системы
Python 3.10, PostgreSQL, Selenium<br>
(отладка и разработка jupyter notebook)

### Структура и работа системы
Для хранения данных используется БД PostgreSQL. Она состоит из одной  таблицы (wordstat_weekly, wordstat_monthly). Текущее состояние «частично» нормализованная по 2ой форме. В таблице хранится:
1.	Период (диапазон дат , по которым собрана статистика)
2.	Абсолютное (число поисковых зарпосов)
3.	date_from (дата начала диапазона дат сбора статистики по запросу)
4.	date_to (дата окончания диапазона дат сбора статистики по запросу)
<br>
Таблицы wordstat_weekly, wordstat_monthly – таблицы одинаковые, но отличаются только длительностью диапазона дат. Их появление связано со спецификой запроса бизнеса.<br>
На данный момент сбор данных, предобработку и загрузку данных в SQL осуществляется единым скриптом (скриптов два – один для еженедельного, второй для ежемесячной статистики).<br>
Список ключевых слов грузится из файла - keywords.xlsx (формат надо упрощать – сейчас он избыточно странен, тк предполагалось делать множественные запросы по каждому проекту).<br>
Выгрузка данных о просмотрах осуществляется с сайта wordstat.ru, с применением selenium. Генерится запрос (url), с использованием ключевого слова и формируется веб-страница.<br>
Веб-страница берется в систему как набор dataframe. Интересующая нас информация находится в 8 и 10 таблицах, которые объединяются и к ним добавляется поле с запросом.<br>
На основе запросов по всем ключевым словам формируется общая таблица, в которую добавляются поля date_from и date_to, на основе парсинга поля «Период».
Для добавления данных в таблицу необходимо выбрать новые данные из таблицы и обновить несколько последних (по дате) из БД. Алгоритм такой – выбираем данные из SQL в таблицу, объединяем таблицы (новые данные добавляются к старым) и удаляем дубликаты сохраняя последние данные. После чего таблица в БД целиком обновляется.

### План развития системы

Февраль 2023 года – первый релиз<br>
Март 2023 года – Переход на новый формат файла keywords.xls, оптимизация алгоритма добавления данных в БД<br>
