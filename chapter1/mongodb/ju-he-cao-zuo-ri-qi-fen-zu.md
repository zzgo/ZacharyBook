### mongodb聚合利用日期分组

```
$dayOfYear: 返回该日期是这一年的第几天。（全年366天）
$dayOfMonth: 返回该日期是这一个月的第几天。（1到31）
$dayOfWeek: 返回的是这个周的星期几。（1：星期日，7：星期六）
$year: 返回该日期的年份部分
$month： 返回该日期的月份部分（between 1 and 12.）
$week： 返回该日期是所在年的第几个星期（between 0 and 53）
$hour： 返回该日期的小时部分
$minute: 返回该日期的分钟部分
$second: 返回该日期的秒部分（以0到59之间的数字形式返回日期的第二部分，但可以是60来计算闰秒。）
$millisecond：返回该日期的毫秒部分（between 0 and 999.）
$dateToString：
    { $dateToString: { format: <formatString>, date: <dateExpression> } }
```

1

```
%Y  Year (4 digits, zero padded)    0000-9999
%m  Month (2 digits, zero padded)   01-12
%d  Day of Month (2 digits, zero padded)    01-31
%H  Hour (2 digits, zero padded, 24-hour clock) 00-23
%M  Minute (2 digits, zero padded)  00-59
%S  Second (2 digits, zero padded)  00-60
%L  Millisecond (3 digits, zero padded) 000-999
%j  Day of year (3 digits, zero padded) 001-366
%w  Day of week (1-Sunday, 7-Saturday)  1-7
%U  Week of year (2 digits, zero padded)    00-53
%%  Percent Character as a Literal  %
```

假设有这么一个集合

```
{
  "_id" : 1,
  "item" : "abc",
  "price" : 10,
  "quantity" : 2,
  "date" : ISODate("2014-01-01T08:15:39.736Z")
}
```

我们在执行：

```
db.sales.aggregate(
   [
     {
       $project: {
          yearMonthDay: { $dateToString: { format: "%Y-%m-%d", date: "$date" } },
          time: { $dateToString: { format: "%H:%M:%S:%L", date: "$date" } }
       }
     }
   ]
)
```

结果就是：

```
{ "_id" : 1, "yearMonthDay" : "2014-01-01", "time" : "08:15:39:736" }
```

$dateToString，这个需要mongodb3.0+支持。

再执行：

```
db.sales.aggregate(
   [
     {
       $project:
         {
           year: { $year: "$date" },
           month: { $month: "$date" },
           day: { $dayOfMonth: "$date" },
           hour: { $hour: "$date" },
           minutes: { $minute: "$date" },
           seconds: { $second: "$date" },
           milliseconds: { $millisecond: "$date" },
           dayOfYear: { $dayOfYear: "$date" },
           dayOfWeek: { $dayOfWeek: "$date" },
           week: { $week: "$date" }
         }
     }
   ]
)
```

得到的结果就是

```
{
  "_id" : 1,
  "year" : 2014,
  "month" : 1,
  "day" : 1,
  "hour" : 8,
  "minutes" : 15,
  "seconds" : 39,
  "milliseconds" : 736,
  "dayOfYear" : 1,
  "dayOfWeek" : 4,
  "week" : 0
}
```



