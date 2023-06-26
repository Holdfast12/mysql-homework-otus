Развернуть базу на мастере и настроить так, чтобы реплицировались таблицы:
| bookmaker |
| competition |
| market |
| odds |
| outcome |

Команда для извлечения текущего пароля root длф mysql:
cat /var/log/mysqld.log | grep 'root@localhost:' | awk '{print $11}'

подключаюсь с полученным паролем
mysql -uroot -p'4e58<kg6pIG#'

В консоли СУБД задаю свой сильный пароль
ALTER USER USER() IDENTIFIED BY 'Qq123456';