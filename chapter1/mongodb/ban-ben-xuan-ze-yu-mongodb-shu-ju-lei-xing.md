版本

![](/assets/7hsdfw.png)![](/assets/5545dsfgsd.png)![](/assets/djkfaf9823.png)

数据类型

| 数据类型 | 示例 | 说明 |
| :--- | :--- | :--- |
| null | {"key":null} | null表示空值或者不存在该字段 |
| 布尔 | {"key","true"} {"key","false"} | 布尔类型表示真或者假 |
| 32位整数 | {"key":8} | 存储32位整数，但再shell界面显示会被自动转成64位浮点数 |
| 64位整数 | {"key":{"floatApprox":8}} | 存储64位整数，floatApprox意思是使用64位浮点数近似表示一个64位整数 |
| 64位浮点数 | {"key":8.21} | 存储64位整数，shell客户端显示的数字都是这种类型； |
| 字符串 | {"key":"value"} {"key":"8"} | UTF-8格式 |
| 对象ID | {"key":ObjectId\(\)} | 12字节的唯一ID |
| 日期 | {"key":new Date\(\)} |  |
| 代码 | {"key":function\(\){}} |  |
| 二进制数据 |  | 主要存储文件 |
| 未定义 | {"key":undefined} | 值没有定义，null和undefined是不同的 |
| 数组 | {"key":\[16,15,17\]} | 集合或者列表 |
| 内嵌文档 | {"user":{"name":"lison"}} | 子对象 |
| Decimal128 | {"price":NumberDecimal\("2.099"\)} | 3.4版本新增的数据类型，无精度问题 |





