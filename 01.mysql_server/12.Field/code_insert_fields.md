#1.insert_fields

```cpp
caller:
--setup_wild

//Drops in all fields instead of current '*' field
insert_fields
--Field_iterator_table_ref::set
--create_item
----Field_iterator_table::create_item
--base_list_iterator::replace
--Field_iterator_table::field
--bitmap_set_bit
```

#2.setup_fields

```cpp
/****************************************************************************
** Check that all given fields exists and fill struct with current data
****************************************************************************/
setup_fields
--Item_field::fix_fields
```