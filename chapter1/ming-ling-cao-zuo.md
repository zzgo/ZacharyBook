# 数据结构 - 字符串（String）

> 字符串类型：实际上可以是字符串（包括xml，JOSN），还有数字（整形浮点数），二级制（图片，音频，视频），最大不能超过512MB

### set

> set age 23 ex 10  ---&gt; 10s后过期 px 10000毫秒过期
>
> setnx name test --&gt; 不存在键name时，返回1设值成功，存在的话失败0
>
> set age 10 xx --&gt; 如果存在这个age键 ，那么进行设值为10 ，返回1，如果不存在这个age键的话，不会有什么操作

### get

> get age --&gt;存在就返回value，不存在返回nil

### mset

> 批量设值，mset name test age 18 --&gt; 设值了两个key-value  name-test , age-18

### mget

> 批量获取，mget name age --&gt; 返回 test，18
>
> 好处：减少了要去链接的网络开销，没有mget 则会使用多次get命令。批量下mset，mget都会提高响应时间，主要是节省了网络开销这一块的时间

# 常用命令 - 字符串（计数）

### incr

> incr age --&gt; 必须为整数，自加1，非整数返回错误，无age键从0自增返回1

### decr

> decr age 整数age 减1

### incrby 

> incrby age 2   整数age+2

### decrby 

> decrby age 2 整数age-2

### incrbyfloat

> incrbyfloat score 1.1 浮点型score+1.1



