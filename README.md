# Commands for operation check

docker-compose exec db_master /bin/bash
CREATE USER 'repl'@'%' IDENTIFIED BY 'repl';
GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%';

mysqldump --master-data=2 --hex-blob --all-databases --lock-all-tables -u root -p > /dump/mysql_bkup_dump.sql

mysql -uroot -p < /dump/mysql_bkup_dump.sql

mysql -urepl -p -h mysql_host_master -P 3306

CHANGE MASTER TO \
MASTER_HOST='mysql_host_master', \
MASTER_PORT=3306, \
MASTER_USER='repl', \
MASTER_PASSWORD='repl', \
MASTER_LOG_FILE='mysql-bin.xxxxx', \
MASTER_LOG_POS=xxx;

CREATE USER 'repl'@'%' IDENTIFIED BY 'repl';
GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%';

mysql -uroot -p < /dump/mysql_bkup_dump.sql

mysql -urepl -p -h mysql_host_slave_1 -P 3306

CHANGE MASTER TO \
MASTER_HOST='mysql_host_slave_1', \
MASTER_PORT=3306, \
MASTER_USER='repl', \
MASTER_PASSWORD='repl', \
MASTER_LOG_FILE='mysql-bin.xxxx', \
MASTER_LOG_POS=xxxx;

START SLAVE;

SHOW SLAVE STATUS\G;

## for check

insert into hoge_table (id, name) values ('111', 'aaaa');

select * from hoge_table;

insert into hoge_table (id, name) values ('222', 'iiii');
insert into hoge_table (id, name) values ('333', 'uuuu');
insert into hoge_table (id, name) values ('444', 'eeee');

truncate fuga_table;
select * from fuga_table;

insert into fuga_table (id, name) values ('111', 'oooo');

