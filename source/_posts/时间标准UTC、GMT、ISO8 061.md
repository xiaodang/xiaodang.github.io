---
title: 时间标准UTC、GMT、ISO 8061
date: 2021-03-23 15:25:29
tags:
---
日常开发中，时间的格式化、存储、本地化经常让人疑惑，尤其是不同的编程语言中对于时间的处理方式又大相径庭，甚至同一个数据库中都有不同都存储方式，每次都要查文档。

### JavaScript中时间的格式化
```js
# Converts a date to a string following the ISO 8601 Extended Format.
> new Date(1616485415008).toISOString()
'2021-03-23T07:43:35.008Z'
# Converts a date to a string using the UTC timezone.
> new Date(1616485415008).toUTCString()
'Tue, 23 Mar 2021 07:43:35 GMT'
# The toGMTString method converts a date to a string, using Internet GMT conventions.[Deprecated(被遗弃)]
> new Date(1616485415008).toGMTString()
'Tue, 23 Mar 2021 07:43:35 GMT'
```
### UTC VS GMT
`GMT`和`UTC`是用来追踪时间的标准，而不是时间格式的标准。

`GMT`时间是通过地球自转来测量时间，但是地球自转并不规律，而且正在缓慢减速，因此格林尼治平时基于天文观测本身的缺陷，已经被原子钟报时的协调世界时（`UTC`）所取代。`UTC`是目前最主要的世界时间标准。

### IOS-8061 时间格式的标准
`IOS-8061`是表示日期和时间的国际标准。
在日期和时间中，不同的标准可能需要不同的粒度级别，因此定义了六个级别。

`T`作为分隔符用来区分日期和时间，`Z`代表UTC。

六种格式如下:
```
   Year:
      YYYY (eg 1997)
   Year and month:
      YYYY-MM (eg 1997-07)
   Complete date:
      YYYY-MM-DD (eg 1997-07-16)
   Complete date plus hours and minutes:
      YYYY-MM-DDThh:mmTZD (eg 1997-07-16T19:20+01:00)
   Complete date plus hours, minutes and seconds:
      YYYY-MM-DDThh:mm:ssTZD (eg 1997-07-16T19:20:30+01:00)
   Complete date plus hours, minutes, seconds and a decimal fraction of a
second
      YYYY-MM-DDThh:mm:ss.sTZD (eg 1997-07-16T19:20:30.45+01:00)
where:

     YYYY = four-digit year
     MM   = two-digit month (01=January, etc.)
     DD   = two-digit day of month (01 through 31)
     hh   = two digits of hour (00 through 23) (am/pm NOT allowed)
     mm   = two digits of minute (00 through 59)
     ss   = two digits of second (00 through 59)
     s    = one or more digits representing a decimal fraction of a second
     TZD  = time zone designator (Z or +hh:mm or -hh:mm)
```

当需要格式化时间字符串时，始终选择`IOS 8601`。

参考:

[MDN Web Docs:Date](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date)

[Date and Time Formats](https://www.w3.org/TR/NOTE-datetime)

[utc-vs-iso-format-for-time](https://stackoverflow.com/questions/58847869/utc-vs-iso-format-for-time)

