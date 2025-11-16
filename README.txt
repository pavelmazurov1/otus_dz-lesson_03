Домашнее задание: Отказоустойчивость приложений (Часть А, расширенный пример под Postgres)

Цель:
Показать отказоустойчивость на уровне:
- базы данных (несколько Зщыепкуы за HAProxy),
- приложений (несколько backend‑инстансов за Nginx).

Состав стенда:
- PostgreSQL слейвы (2 инстанса, без мастера)
- HAProxy (балансирует подключения к слейвам postgres)
- backend (Flask-приложение, обращающееся к БД)
- nginx - балансирует "backend1/2" по HTTP.
- load-test/run.sh - посылает серию запросов на "/data".

Быстрый старт:
$ docker-compose up -d --build
$ ./load-test/run.sh

Проверка отказоустойчивости:

А) Отключение PostgreSQL слейва

1) Запустите нагрузку:
$ ./load-test/run.sh

2) Имитируйте отказ одного слейва
$ docker kill postgres-slave1

3) система продолжает работать через второй слейв. Восстановить узел можно с помощью docker compose restart postgres-slave1)


Б) Отключение backend-инстанса

1) Запустите нагрузку:
$ ./load-test/run.sh

2) Имитируйте отказ одного слейва
$ docker kill backend1

3) nginx продолжает слать трафик на второй бэк

4) Восстановите узел:
$ docker-compose restart backend1


Просмотр логов (примеры):
$ docker-compose logs haproxy
$ docker-compose logs nginx
$ docker logs -f backend1
$ docker logs -f postgres-slave1


Ожидаемый результат:
- Все компоненты работают в связке.
- При отключении одного из PostgreSQL или backend - приложение остаётся доступным.

