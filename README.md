Развернуть базу на мастере и настроить так, чтобы реплицировались таблицы:
| bookmaker |
| competition |
| market |
| odds |
| outcome |

Команда для извлечения текущего пароля root для mysql:
cat /var/log/mysqld.log | grep 'root@localhost:' | awk '{print $11}'

подключаюсь с полученным паролем
mysql -uroot -p'3_0Pf:Py)yU/'

В консоли СУБД задаю свой сильный пароль на обоих серверах запросом в консоли mysql
ALTER USER USER() IDENTIFIED BY 'Qq-123456';
<img src="./screenshots/password.png"></img>

Настройка репликации
mysql -uroot -p'Qq-123456'

убеждаюсь в том, что server-id на мастере и слейве разные, следующим запросом:
select @@server_id;

<img src="./screenshots/server_id_master.png"></img>
<img src="./screenshots/server_id_slave.png"></img>


убеждаюсь, что GTID включен
show variables like 'gtid_mode';
<img src="./screenshots/gtid_mode_on.png"></img>

создаю тестовую базу bet:
create database bet;
<img src="./screenshots/create_db.png"></img>

загружаю дамп и проверяю
mysql -uroot -p'Qq-123456' -D bet < /vagrant/bet.dmp
mysql -uroot -p'Qq-123456'
USE bet;
SHOW TABLES;
<img src="./screenshots/dump.png"></img>

создаю пользователя для репликации repl с паролем !OtusLinux2018 и даю ему права на репликацию:
CREATE USER 'repl'@'%' IDENTIFIED BY '!OtusLinux2018';
SELECT user,host FROM mysql.user where user='repl';
GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%' IDENTIFIED BY '!OtusLinux2018';
<img src="./screenshots/create_repl_user.png"></img>
Дамплю базу для последующего залива на слэйв и игнорирурую таблицы по заданию
mysqldump --all-databases --triggers --routines --master-data --ignore-table=bet.events_on_demand --ignore-table=bet.v_same_event -uroot -p > master.sql

<img src="./screenshots/create_dump_on_master.png"></img>

Таблицы bet.events_on_demand и bet.v_same_event при репликации уже игнорируются посредством параметров в файле конфигурации 05-binlog.cnf

захожу на slave подключаюсь к СУБД
mysql -uroot -p'Qq-123456'

заливаю дамп с мастера и проверяю, что база есть и без игнорируемых таблиц
SOURCE /vagrant/master.sql
смотрю базу bet
SHOW DATABASES LIKE 'bet';
подключаюсь к ней
USE bet;
смотрю таблицы в bet
SHOW TABLES;
<img src="./screenshots/show_tables_slave.png"></img>

Подключаю и запускаю репликацию на slave следующими запросами:
CHANGE MASTER TO MASTER_HOST = "192.168.56.150", MASTER_PORT = 3306, MASTER_USER = "repl", MASTER_PASSWORD = "!OtusLinux2018", MASTER_AUTO_POSITION = 1;
START SLAVE;
SHOW SLAVE STATUS\G
<img src="./screenshots/start_slave.png"></img>

Видно, что gtid и репликация работают, две таблицы bet.events_on_demand и bet.v_same_event игнорируются.

Проверка репликации
на мастере
USE bet;
INSERT INTO bookmaker (id,bookmaker_name) VALUES(1,'bdfbwgwq');
SELECT * FROM bookmaker;
<img src="./screenshots/repl_check.png"></img>

