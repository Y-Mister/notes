## java虚拟机（二）

#### ***类文件的结构

##### 承接（一）必要的内容

class文件由如下所示数据项组成：

| 类型           | 名称                | 数量                |
| -------------- | ------------------- | ------------------- |
| u4             | magic               | 1                   |
| u2             | minor_version       | 1                   |
| u2             | major_version       | 1                   |
| u2             | constant_pool_count | 1                   |
| cp_inf         | constant_pool       | constant_pool_count |
| u2             | access_flags        | 1                   |
| u2             | this_class          | 1                   |
| u2             | super_class         | 1                   |
| u2             | interfaces_count    | 1                   |
| u2             | interfaces          | interfaces_count    |
| u2             | fields_count        | 1                   |
| field_info     | fields              | fields_count        |
| u2             | methods_count       | 1                   |
| method_info    | methods             | methods_count       |
| u2             | attributes_count    | 1                   |
| attribute_info | attributes          | attributes_count    |



##### 字段表集合  fields_info & fields

> 作用：用于描述接口或这类中申明的变量，字段包括类级变量、实例级变量但不包括方法内部申明的局部变量。

字段表结构：

| 类型            | 名称              | 数量             |
| --------------- | ----------------- | ---------------- |
| u2              | access_flag       | 1                |
| u2              | name_index        | 1                |
| u2              | desccriptor_index | 1                |
| u2              | attributes_count  | 1                |
| attributes_info | attributes        | attributes_count |

字段表的access_flag与类的access_flag类似：ACC_VOLATILE与ACC_FINAL不可同时使用

| 标志名称      | 标志值 | 含义                              |
| ------------- | ------ | --------------------------------- |
| ACC_PUBLIC    | 0x0001 | 是否为public类型                  |
| ACC_PRIVATE   | 0x0002 | 是否为private类型                 |
| ACC_PROTECTED | 0x0004 | 是否为protected类型               |
| ACC_ATATIC    | 0x0008 | 是否为static类型                  |
| ACC_FINAL     | 0x0010 | 是否被申明为final，只有类可设置？ |
| ACC_VOLATILE  | 0X0040 | 是否volatile                      |
| ACC_TRANSIENT | 0X0080 | 是否可被序列化                    |
| ACC_SYNTHETIC | 0X1000 | 是否编译器自动产生                |
| ACC_ENUM      | 0x4000 | 标识这是一个枚举值                |

