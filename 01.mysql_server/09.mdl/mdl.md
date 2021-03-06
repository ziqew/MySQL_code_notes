#1.MDL_request::init

```cpp
caller:
--init_one_table


/**
  Initialize a lock request.

  This is to be used for every lock request.

  Note that initialization and allocation are split into two
  calls. This is to allow flexible memory management of lock
  requests. Normally a lock request is stored in statement memory
  (e.g. is a member of struct TABLE_LIST), but we would also like
  to allow allocation of lock requests in other memory roots,
  for example in the grant subsystem, to lock privilege tables.

  The MDL subsystem does not own or manage memory of lock requests.

  @param  mdl_namespace  Id of namespace of object to be locked
  @param  db             Name of database to which the object belongs
  @param  name           Name of of the object
  @param  mdl_type       The MDL lock type for the request.
*/

MDL_request::init
--mdl_key_init
```

#2.MDL_context::acquire_locks

```cpp
/**
  Acquire exclusive locks. There must be no granted locks in the
  context.

  This is a replacement of lock_table_names(). It is used in
  RENAME, DROP and other DDL SQL statements.

  @param  mdl_requests  List of requests for locks to be acquired.

  @param lock_wait_timeout  Seconds to wait before timeout.

  @note The list of requests should not contain non-exclusive lock requests.
        There should not be any acquired locks in the context.

  @note Assumes that one already owns scoped intention exclusive lock.

  @retval FALSE  Success
  @retval TRUE   Failure
*/
MDL_context::acquire_locks
--my_qsort
--acquire_lock
----mdl_request_ptr_cmp
------MDL_key::cmp

```

#3.MDL_context::init
```cpp
//创建connection过程中，初始化mdl_context.
//函数调用：
THD::THD
--MDL_context::init//每一个connection对应一个mdl_context
```

#4. acquire_lock
#4.1 stack
```cpp
说明：首先进行兼容性判断，如果兼容，那么就把ticket加入到队列中，加锁成功。

　　函数调用栈

　　 open_and_lock_tables

　　　　open_table

1. 排他锁使用
　　lock_table_names
　　MDL_context::acquire_locks
2. 共享锁使用
　　open_table_get_mdl_lock
　　MDL_context::try_acquire_lock
```

#4.2 排他锁使用
```cpp
lock_table_names
--MDL_context::acquire_locks
----acquire_lock
```

#4.3 共享锁使用

```cpp
open_table
--open_table_get_mdl_lock
----MDL_context::try_acquire_lock
```

#4.4 acquire_lock

```cpp
acquire_lock
--MDL_context::try_acquire_lock_impl
----MDL_context::find_ticket
------MDL_ticket::has_stronger_or_equal_type
----MDL_context::clone_ticket
------MDL_ticket::create
------ticket->m_lock->m_granted.add_ticket(ticket);
------m_tickets[mdl_request->duration].push_front(ticket);
----MDL_ticket::create
----MDL_map::find_or_insert
------MDL_map_partition::find_or_insert
--------my_hash_search_using_hash_value
--------MDL_lock::create
--------my_hash_insert
--------MDL_map_partition::move_from_hash_to_lock_mutex
----MDL_lock::can_grant_lock
----lock->m_granted.add_ticket(ticket);//lock保存ticket
----m_tickets[mdl_request->duration].push_front(ticket);//context保存tickets
----mdl_request->ticket= ticket;//mdl_request保存tickets
--lock->m_waiting.add_ticket(ticket);//无法直接获取ticket，因为锁兼容不允许。需要lock wait。
--MDL_context::find_deadlock
--MDL_wait::timed_wait
----ENTER_COND
------THD::enter_cond
--------THD::enter_stage
----------MYSQL_SET_STAGE
------------inline_mysql_set_stage
--------------start_stage_v1
----------------my_pthread_getspecific_ptr
------------------my_pthread_getspecific
--------------------my_pthread_getspecific_imp
----------------------pthread_getspecific
----thd_wait_begin (thd=0x0, wait_type=2) at /home/chenhui/mysql-5623-trunk/sql/sql_class.cc:4352
------MYSQL_CALLBACK(thd->scheduler, thd_wait_begin, (thd, wait_type));
----mysql_cond_timedwait
------inline_mysql_cond_timedwait
--------pthread_cond_wait
----thd_wait_end
```

#5.MDL_context::release_lock

```cpp
MDL_context::release_lock
--MDL_lock::Ticket_list::remove_ticket
----m_list.remove(ticket)
----clear_bit_if_not_in_list(ticket->get_type());
--m_tickets[duration].remove(ticket);
--MDL_ticket::destroy(ticket);
```



