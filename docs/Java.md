## 基础

### Object

Object 的 equals 方法是比较的对象的内存地址，而 String 的 equals 方法比较的是字符串的值是否相等

equals() 方法被覆盖过，则 hashCode() 方法也必须被覆盖

对于基本数据类型来说，== 比较的是值。对于引用数据类型来说，==比较的是对象的内存地址

### String

被 final 关键字修改之后的 String 会被编译器当做常量来处理

#### StringBuffer（安全）