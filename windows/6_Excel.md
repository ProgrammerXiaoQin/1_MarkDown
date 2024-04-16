1. `VLOOKUP (lookup_value, table_array, col_index_num, [range_lookup])`
	- `lookup_value` 要查找的值
	- `table_array` 查找的数据区域
	- `col_index_num` 结果在数据区域的第几列第四
	- `range_lookup` 数据的匹配方式，0表示精确匹配，1表示近似匹配
>注：
>1. 要查找值必须在查找数据区域的第一列
>2. 遇到重复值，只能返回一个结果
>3. 不能查找要查找的值左侧数据
>4. 对于外部工作簿的引用，需要使用特殊的语法，即使用方括号`[]`将工作簿名和表名包围起来，并用感叹号分隔，例如`[workbookname]SheetName!$A$1:$E$10
