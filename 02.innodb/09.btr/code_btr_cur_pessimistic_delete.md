#1. btr_cur_pessimistic_delete


```cpp
btr_cur_pessimistic_delete
--btr_search_update_hash_on_delete
--page_cur_delete_rec
----page_dir_find_owner_slot
------page_dir_get_nth_slot
------rec_get_n_owned_new//找到owner record
------rec_get_next_ptr_const
------//之后根据owner record找到slot
----page_dir_get_nth_slot
----page_cur_delete_rec_write_log
----page_header_set_ptr(page, page_zip, PAGE_LAST_INSERT, NULL);
------page_header_set_field
----buf_block_modify_clock_inc
----page_rec_set_next(prev_rec, next_rec);//修改指针
----page_dir_slot_set_n_owned(cur_dir_slot, page_zip, cur_n_owned - 1);
----page_mem_free
------free = page_header_get_ptr(page, PAGE_FREE);
------page_rec_set_next(rec, free);
------page_header_set_ptr(page, page_zip, PAGE_FREE, rec);
------garbage = page_header_get_field(page, PAGE_GARBAGE);
------page_header_set_field(page, page_zip, PAGE_GARBAGE,garbage + rec_offs_size(offsets));
------page_header_set_field(page, page_zip, PAGE_N_RECS,page_get_n_recs(page) - 1);
--btr_cur_compress_if_useful
--
```