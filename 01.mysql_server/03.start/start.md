#1. mysqld
```cpp
	mysqld_main
	--//调用mysys/My_init.c->my_init()，初始化mysql内部的系统库
	--my_init//Initialize my_sys functions, resources and variables
	----init_glob_errs
	----my_thread_global_init//initialize thread environment
	------my_thread_init
	--load_defaults
	----my_load_defaults//给mysqld启动命令加参数，共90个。
	--init_sql_statement_names
	--sys_var_init
	----mysql_add_sys_var_chain//system schema
	--init_pfs_instrument_array
	--handle_early_options//bootstrap,skip-grant-tables,help, verbose, version
	----sys_var_add_options
	------register_option
	----handle_options
	------my_handle_options
	--adjust_related_options
	--initialize_performance_schema
	----init_sync_class
	----init_thread_class
	----init_table_share
	----init_file_class
	----init_stage_class
	----install_default_setup
	------register_thread_v1
	--------register_thread_class
	------insert_setup_actor//初始情况下所有用户都可以登录%%%
	--------lf_hash_insert
	------insert_setup_object//create mysql/information schema/performance schema DB, 
	--------插入setup_object_array和hash table
	--init_server_psi_keys
	----inline_mysql_mutex_register
	------register_mutex_v1
	----inline_mysql_rwlock_register
	------register_rwlock_v1
	--------REGISTER_BODY_V1
	--my_thread_global_reinit
	--mysql_audit_initialize
	--LOGGER::init_base//初始化日志功能
	----init_pthread_objects
	------mysql_log.init_pthread_objects();
	------mysql_slow_log.init_pthread_objects();
	--//调用load_defaults(conf_file_name, groups, &argc, &argv)，读取配置信息
	--init_common_variables
	----init_thread_environment
	----mysql_init_variables//Initialize MySQL global variables to default values.
	--my_init_signals
	--check_user//检测启动时的用户选项
	--set_user(mysqld_user, user_info);//设置以该用户运行
	--//初始化内部的一些组件，如table_cache, query_cache等。
	--init_server_components//重点！！！！！！！！！！！
	----mdl_init
	----table_def_init
	------Table_cache_manager::init
	----hostname_cache_init
	----/ssd1/chenhui3/dbpath/log/mysql.err
	----binlog related.
	----ha_init_errors//Allow storage engine to give real error messages
	----plugin_init
	----//binlog, mysql_native_password, mysql_old_password, sha256_password, MRG_MYISAM, MEMORY, MyISAM, CSV
	----//PERFORMANCE_SCHEMA,  InnoDB,  INNODB_TRX, INNODB_LOCK_WAITS, INNODB_CMP, INNODB_CMP_RESET
	----//INNODB_CMPMEM, INNODB_CMPMEM_RESET, INNODB_CMP_PER_INDEX, INNODB_CMP_PER_INDEX_RESET
	----//INNODB_BUFFER_PAGE, INNODB_BUFFER_PAGE_LRU，INNODB_BUFFER_POOL_STATS， INNODB_METRICS，
	----//INNODB_FT_DEFAULT_STOPWORD, INNODB_FT_DELETED, INNODB_FT_BEING_DELETED, INNODB_FT_CONFIG
	----//INNODB_FT_INDEX_CACHE, INNODB_FT_INDEX_TABLE, INNODB_SYS_TABLES, INNODB_SYS_TABLESTATS
	----//INNODB_SYS_INDEXES, INNODB_SYS_COLUMNS, INNODB_SYS_FIELDS, INNODB_SYS_FOREIGN, 
	----//INNODB_SYS_FOREIGN_COLS, INNODB_SYS_TABLESPACES, INNODB_SYS_DATAFILES, FEDERATED
	----//BLACKHOLE, partition, 
	------register_builtin//insert to array
	------plugin_load_list
	------plugin_load
	--------init_one_table
	----------MDL_request::init
	--------open_and_lock_tables
	----------open_tables
	--------init_read_record
	------plugin_initialize
	--------innobase_init
	--network_init//初始化网络模块，创建socket监听
	--start_signal_handler();// 创建pid文件
	--mysql_rm_tmp_tables() || acl_init(opt_noacl)//删除tmp_table并初始化数据库级别的权限。
	--init_status_vars(); // 初始化mysql中的status变量
	--start_handle_manager();//创建manager线程
	--handle_connections_sockets();//主要处理函数，处理新的连接并创建新的线程处理
	
```
	
	
#2 innobase_init


	
	
	