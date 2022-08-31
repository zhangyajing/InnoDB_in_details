# 背景
在innodb存储引擎中，通常有链表，vector等数据结果用于管理内存中的页等数据结构。由于链表的查找效率在数据量比较大的时候查询效率比较低，在引擎内部还构建一个hash结构

# 代码
  文件名称                     5.7版本                       8.0版本
  ha0ha.h                      250
  ha0ha.ic                     243
  ha0ha.cc                     552
  ha0storage.h                 140
  ha0storage.ic                147
  ha0storage.cc                183
  hash0hash.h                  578
  hash0hash.ic                 280
  hash0hash.cc                 366
                           

在5.7的版本中，hash相关的代码共2739行， 8.0版本共xxx行

# 函数梳理
## ha0ha
   // ha0ha.h
   1. ha_search_and_get_data
   2. ha_search_and_update_if_found_func
   3. ib_create
   4. ha_clear
   5. ha_insert_for_fold_func
   6. ha_search_and_delete_if_found
   7. ha_remove_all_nodes_to_page
   8. ha_validate
   9. ha_print_info
   10. hash_assert_can_modify
   11. hash_assert_can_search
   // ha0ha.ic  里面包含了部分上面ha0ha.h中声明的函数
   12. ha_delete_hash_node
   13. ha_node_get_data
   14. ha_node_set_data_func
   15. ha_chain_get_next
   16. ha_chain_get_first
   17. ha_search_with_data
   // ha0ha.cc 包含ha0ha.h和ha0ha.ic中的代码
   18. ha_btr_search_latch_x_locked


## ha0storage
   // ha0storage.h
   // hash storage 数据结构 ha_storage_t
   // 宏
   ha_storage_put
   ha_storage_put_str
   ha_storage_put_str_memlim
   // 函数
   1. ha_storage_create
   2. ha_storage_put_memlim
   3. ha_storage_empty
   4. ha_storage_free
   5. ha_storage_get_size

   // ha0storage.ic
   // 数据结构
   1. ha_storage_t
   2. ha_storage_node_t
   // 函数 实现.h头文件中定义的函数
   
   // ha0storage.cc
    1. ha_storage_get

## hash0hash
    // hash0hash.h
    // 数据结构
    1. hash_table_t
    2. hash_cell_t
    TODO: 画图
    3. hash_table_sync_t

    // 函数
    1. hash_create
    2. hash_create_sync_obj
    3. hash_table_free
    4. hash_calc_hash
    5. hash_get_nth_cell
    6. hash_table_clear
    7. hash_get_n_cells
    8. hash_get_sync_obj_index
    9. hash_get_nth_heap
    10. hash_get_heap
    11. hash_get_nth_mutex
    12. hash_get_nth_lock
    13. hash_get_mutext
    14. hash_get_lock
    15. hash_lock_s_confirm
    16. hash_lock_x_confirm
    17. hash_mutex_enter
    18. hash_mutex_exit
    19. hash_mutex_enter_all
    20. hash_mutex_exit_all
    21. hash_mutex_exit_but
    22. hash_lock_s
    23. hash_lock_x
    24. hash_unlock_s
    25. hash_unlock_x
    26. hash_lock_x_all
    27. hash_unlock_x_all
    28. hash_unlock_xall_but
    
    // 数据结构
    1. hash_cell_t
    2. hash_table_t

    // hash0hash.ic 主要的作用就是实现.h中的函数
    
    // hash0hash.cc
    
