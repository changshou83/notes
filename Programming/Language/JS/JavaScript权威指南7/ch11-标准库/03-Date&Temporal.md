## Date

- 本地时间指的是执行JavaScript代码的电脑设置的时间，UTC时间指的是世界标准时间
- 时间戳
	- `Date.now()`
	- 高精度时间戳：`performance.now()`
		- 返回整数加小数
		- 返回网页加载完成后或Node进行启动执行后经过的时间

### 创建日期对象

```js
// 构造函数
let d1 = new Date();
let d2 = new Date(0);
let d3 = new Date('1970-1-1 00:00:00'); // 因为兼容性问题，不建议使用Date对象解析日期字符串
let d4 = new Date(1970, 0, 1, 0, 0, 0); // 使用当地时间，若想使用UTC，请new Date(Date.UTC(...))
```

### 获取和设置日期

```js
const d5 = new Date();
d5.getDate();          // 月中的第几天
d5.getDay();           // 周中的第几天，从0开始，0表示星期天
d5.getFullYear();      // 完整年份
d5.getHours();         // 天中的第几小时
d5.getMilliseconds();  // 毫秒数
d5.getMinutes();       // 分钟数
d5.getMonth();         // 年中的第几个月，从0开始，0表示一月
d5.getSeconds();       // 秒数
d5.getTime();          // 返回时间戳
// d5.getUTCXxx();

d5.setDate();          // 设置日期
// d5.setXxx();
```

### 格式化和解析日期字符串

```js
const d6 = new Date()
d6.toString();           // 使用本地时区但不按照当地惯例格式化日期和时间
d6.toUTCString();        // 使用UTC时区但不按照当地惯例格式化日期和时间
d6.toISOString();        // 使用UTC时区，返回一个ISO格式的字符串(YYYY-MM-DDTHH:mm:ss.sssZ)
d6.toDateString();       // 使用美式英语，以人类易读的方式返回日期字符串
d6.toTimeString();       // 第一个参数表示地区，第二个参数为日期字符串
d6.toLocaleString();     // 同上
d6.toLocaleDateString(); // 同上
d6.toLocaleTimeString(); // 同上
```

## Temporal API

- [英文文档](https://tc39.es/proposal-temporal/docs/),[中文文档](https://tc39.es/proposal-temporal/docs/zh_CN/index.html),[github](https://github.com/tc39/proposal-temporal),[cookbook](https://tc39.es/proposal-temporal/docs/cookbook.html)
- 简介：提供了处理日期和时间的标准对象和函数
- 进度：Stage 3(2023.02.06)
- 特点
	- 提供了易于使用的接口
	- 对所有时区的出色支持，包括夏令时(PS:中国没有夏令时)
	- 解析一个严格规定的字符串格式(PS:Date解析日期字符串的大坑？)
	- 支持非公历历法(PS:支持农历？)
	- 明确的表示一个特定的日期和时间(PS:而不是用毫秒数？)

### 从用途介绍 API

- [原文链接](https://blog.openreplay.com/is-it-time-for-the-javascript-temporal-api/)

#### 日期字符串

![日期字符串|568](Temporal-Instant-Dates-and-Times.png)

#### 当前时间

```js
Temporal.Now.instant();          // time since the Unix epoch
Temporal.Now.zonedDateTimeISO(); // time in current location
Temporal.Now.timeZone();         // current time zone
```

#### 时区

```js
new Temporal.ZonedDateTime(
	1234567890000,                           // epoch nanoseconds
	Temporal.TimeZone.from('Asia/Beijing'),  // tiemzone
	Temporal.Calendar.from('iso8601')        // calendar
);

Temporal.ZonedDateTime.from('2023-02-06');
Temporal.Instant('2023-02-06').toZonedDateTime('+8:00');
```

#### 时区无关的简单日历

```js
new Temporal.PlainTime(15,0,0);      // 1
Temporal.PlainTime.from('15:00:00'); // 2

new Temporal.PlainDate(2023,2,6);
new Temporal.PlainDateTime(2023,2,6,15,0,0);
new Temporal.PlainYearMonth(2023,2);
new Temporal.PlaiMonthDay(2,6);
```

#### 日期值

```js
const t = Temporal.ZonedDateTime.from('2023-02-06');
const {
	year,month,day,hour,minute,second,millisecond, // 懂得都懂
	dayOfWeek,dayOfYear,weekOfYear,                // 第多少天
	daysInMonth,daysInYear,                        // 有多少天
	inLeapYear                                     // 是否为闰年
} = t;
```

#### 对日期进行排序

- `Temporal.ZonedDateTime.compare(d1,d2)`
	- 日期相同，返回`0`
	- d1在d2后，返回`1`
	- d1在d2前，返回`-1`

```js
const t = [
	'2022-01-01T00:00:00+00:00[Europe/London]',
	'2022-01-01T00:00:00+00:00[Africa/Cairo]',
	'2022-01-01T00:00:00+00:00[America/New_York]'
]
	.map(d => Temporal.ZonedDateTime.from(d))
	.sort(Temporal.ZonedDateTime.compare);
```

#### 日期计算

- add,substract,round
- until,since
- equals

```js
const t = Temporal.ZonedDateTime.from('2023-02-06');

t.add({ hours: 8, minutes: 59 });   // add 8 hours 59 minutes
t.substract({ weeks: 2 });          // subtract 2 weeks
t.round({ smallestUnit: 'month' }); // round to nearest month

// return a Temporal.Duration object
t.until(); // time to t
t.since(); // time since t

const d1 = Temporal.PlainDate.from('2022-01-31');
const d2 = Temporal.PlainDate.from('2023-01-31');
d1.equals(d1); // false
```

#### 格式化时间字符串

```js
const d = new Temporal.PlainDate(2023,2,6);

// toString
d.toString();

// 建议使用 Intl
new Intl.DateTimeFormat('zh-CN').format(d);
new Intl.DateTimeFormat('en-US').format(d);
```
