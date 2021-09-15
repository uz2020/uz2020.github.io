---
layout: post
title: MySQL 3.23.49 源码debug
---

编译

```bash
./configure --with-innodb --debug
```

启动

```bash
safe_mysqld --user=mysql --debug &
```


查看日志

```bash
tail -f /tmp/mysqld.trace
```

debug日志

```bash
DBUG_ENTER("mysql_insert")
DBUG_RETURN(0)
```

插入记录

```sql
insert into t2 values(99);
```

```
T@11274: | | exit: 4
T@11274: | <vio_read
T@11274: | >vio_read <!-- 读取sql，共26B，socket descriptor: 12 -->
T@11274: | | enter: sd=12  size=26
T@11274: | | exit: 26
T@11274: | <vio_read
T@11274: | >thr_end_alarm
T@11274: | <thr_end_alarm
T@11274: | >vio_blocking
T@11274: | | enter: set_blocking_mode: 0
T@11274: | <vio_blocking
T@11274: | general: Command on socket (12) = 3 (Query) <!-- packet第一个Byteu确认这是query command -->
T@11274: | query: insert into t2 values(99)
T@11274: | >mysql_parse
T@11274: | | >mysql_init_query
T@11274: | | <mysql_init_query
T@11274: | | >add_table_to_list
T@11274: | | <add_table_to_list
T@11274: | | >mysql_execute_command
T@11274: | | | >mysql_insert
T@11274: | | | | >open_ltable
T@11274: | | | | | >open_table
T@11274: | | | | | | >hash_search
T@11274: | | | | | | | exit: found key at 0
T@11274: | | | | | | <hash_search
T@11274: | | | | | <open_table
T@11274: | | | | | >mysql_lock_tables
T@11274: | | | | | | >my_malloc
T@11274: | | | | | | | my: Size: 24  MyFlags: 0
T@11274: | | | | | | | exit: ptr: a2c6e58
T@11274: | | | | | | <my_malloc
T@11274: | | | | | | >lock_external
T@11274: | | | | | | | >ha_innobase::external_lock
T@11274: | | | | | | | <ha_innobase::external_lock
T@11274: | | | | | | <lock_external
T@11274: | | | | | | >thr_multi_lock
T@11274: | | | | | | | lock: data: a2c6e68  count: 1
T@11274: | | | | | | | >thr_lock
T@11274: | | | | | | | | lock: data: a2c6f6c  thread: 11274  lock: a2c72a0  type: 5
T@11274: | | | | | | | <thr_lock
T@11274: | | | | | | <thr_multi_lock
T@11274: | | | | | <mysql_lock_tables
T@11274: | | | | <open_ltable
T@11274: | | | | >setup_tables
T@11274: | | | | <setup_tables
T@11274: | | | | >setup_fields
T@11274: | | | | <setup_fields
T@11274: | | | | >fill_record
T@11274: | | | | <fill_record
T@11274: | | | | >ha_innobase::write_row
T@11274: | | | | <ha_innobase::write_row
T@11274: | | | | >flush_io_cache
T@11274: | | | | | >my_write
T@11274: | | | | | | my: Fd: 6  Buffer: a1fc140  Count: 55  MyFlags: 20
T@11274: | | | | | <my_write
T@11274: | | | | <flush_io_cache
T@11274: | | | | >innobase_commit
T@11274: | | | | | trans: ending transaction
T@11274: | | | | <innobase_commit
T@11274: | | | | >ha_autocommit_or_rollback
T@11274: | | | | | >ha_commit
T@11274: | | | | | | >innobase_commit
T@11274: | | | | | | | trans: ending transaction
T@11274: | | | | | | <innobase_commit
T@11274: | | | | | <ha_commit
T@11274: | | | | <ha_autocommit_or_rollback
T@11274: | | | | >mysql_unlock_tables
T@11274: | | | | | >thr_multi_unlock
T@11274: | | | | | | lock: data: a2c6e68  count: 1
T@11274: | | | | | | >thr_unlock
T@11274: | | | | | | | lock: data: a2c6f6c  thread: 11274  lock: a2c72a0
T@11274: | | | | | | | lock: No waiting read locks to free
T@11274: | | | | | | <thr_unlock
T@11274: | | | | | <thr_multi_unlock
T@11274: | | | | | >unlock_external
T@11274: | | | | | | >ha_innobase::external_lock
T@11274: | | | | | | | >innobase_commit
T@11274: | | | | | | | | trans: ending transaction
T@11274: | | | | | | | <innobase_commit
T@11274: | | | | | | <ha_innobase::external_lock
T@11274: | | | | | <unlock_external
T@11274: | | | | | >my_free
T@11274: | | | | | | my: ptr: a2c6e58
T@11274: | | | | | <my_free
T@11274: | | | | <mysql_unlock_tables
T@11274: | | | | >send_ok
T@11274: | | | | | >net_flush
T@11274: | | | | | | >vio_is_blocking
T@11274: | | | | | | | exit: 0
T@11274: | | | | | | <vio_is_blocking
T@11274: | | | | | | >net_real_write
T@11274: | | | | | | | >vio_write
T@11274: | | | | | | | | enter: sd=12  size=9
T@11274: | | | | | | | | exit: 9
T@11274: | | | | | | | <vio_write
T@11274: | | | | | | <net_real_write
T@11274: | | | | | <net_flush
T@11274: | | | | <send_ok
T@11274: | | | <mysql_insert
T@11274: | | <mysql_execute_command
T@11274: | | >my_free
T@11274: | | | my: ptr: 0
T@11274: | | <my_free
T@11274: | | >my_free
T@11274: | | | my: ptr: 0
T@11274: | | <my_free
T@11274: | <mysql_parse
T@11274: | info: query ready
T@11274: | >close_thread_tables
T@11274: | | info: thd->open_tables=0xa2bc090
T@11274: | <close_thread_tables
T@11274: | >free_root
T@11274: | <free_root
T@11274: <do_command
T@11274: >do_command
T@11274: | >vio_is_blocking
T@11274: | | exit: 0
T@11274: | <vio_is_blocking
T@11274: | >vio_read
T@11274: | | enter: sd=12  size=4
T@11274: | | vio_error: Got error 11 during read
T@11274: | | exit: -1
T@11274: | <vio_read
T@11274: | info: vio_read returned -1,  errno: 11
T@11274: | >thr_alarm
T@11274: | | enter: thread: T@11274  sec: 28800
T@11274: | | info: reschedule
T@11274: | <thr_alarm
T@11274: | >vio_is_blocking
T@11274: | | exit: 0
T@11274: | <vio_is_blocking
T@11274: | >vio_blocking
T@8201 : | >process_alarm
T@11274: | | enter: set_blocking_mode: 1
T@8201 : | | info: sig: 14 active alarms: 1
T@11274: | <vio_blocking
T@8201 : | <process_alarm
T@11274: | >vio_read
T@11274: | | enter: sd=12  size=4
```

## do_command

1. my_net_read
2. command = (enum enum_server_command) (uchar) packet[0];
3. switch (command)
4. case COM_QUERY
5. DBUG_PRINT("query",("%s",thd->query));
6. mysql_parse()
7. DBUG_PRINT("info",("query ready"));


## mysql_parse

1. mysql_init_query(thd);
2. lex, yyparse
3. mysql_execute_command

## mysql_execute_command

1. switch (lex->sql_command)
2. case SQLCOM_INSERT:
3. check_access()
4. mysql_insert()

## mysql_insert

锁表

1. open_ltable
2. mysql_lock_tables
3. lock_external
4. ha_innobase::external_lock


写入记录

1. setup_tables
2. setup_fields
3. fill_record
4. write_record
5. write_row -> ha_innobase::write_row

## ha_innobase::write_row

1. prebuilt
2. statistics_increment (多线程更新全局变量，加锁)
