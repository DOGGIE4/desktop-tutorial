# 一、 TimeSpan

时间跨度对象（`TimeSpan`）是.NET Framework中用于表示时间间隔或持续时间的结构。
它表示了一段时间的长度，可以用来进行时间相关的操作，比如计算时间差、添加时间间隔、减去时间间隔等。

`TimeSpan` 包含以下重要的属性和方法：

*   `Days`、`Hours`、`Minutes`、`Seconds`、`Milliseconds`：这些属性允许你获取时间跨度中的不同时间单位的值。例如，`TimeSpan.Days` 可以获取天数部分，`TimeSpan.Hours` 可以获取小时部分，以此类推。

*   `TotalDays`、`TotalHours`、`TotalMinutes`、`TotalSeconds`、`TotalMilliseconds`：这些属性返回时间跨度的总时间长度，以不同的单位表示，比如总天数、总小时数、总分钟数等。

*   `Add` 和 `Subtract` 方法：这些方法用于在时间跨度上进行加法和减法操作。你可以将一个时间跨度添加到另一个时间跨度，或从一个时间跨度中减去另一个时间跨度。

*   `CompareTo` 和 `Equals` 方法：这些方法用于比较两个时间跨度的大小。

*   `FromDays`、`FromHours`、`FromMinutes`、`FromSeconds`、`FromMilliseconds`：这些方法用于创建时间跨度对象，给定一个特定时间单位的值。

`TimeSpan` 在许多情况下非常有用，特别是在处理时间间隔、定时、持续时间等方面。它可以用来执行各种时间相关的计算和操作。

# 二 、举例说明

以下是一些示例，演示了如何使用 `TimeSpan` 对象来表示时间间隔和执行时间相关的操作：

## 1.  创建时间跨度对象：

```
// 创建一个表示2天的时间跨度
TimeSpan twoDays = TimeSpan.FromDays(2);

// 创建一个表示3小时30分钟的时间跨度
TimeSpan threeHoursThirtyMinutes = TimeSpan.FromHours(3.5);
```

## 2. 计算时间差：

```
DateTime start = DateTime.Now;
DateTime end = DateTime.Now.AddHours(3);

TimeSpan duration = end - start;  // 计算两个日期时间之间的时间差
Console.WriteLine($"时间差：{duration.TotalHours} 小时");
```

## 3. 添加和减去时间跨度：

```
TimeSpan oneHour = TimeSpan.FromHours(1);
TimeSpan halfHour = TimeSpan.FromMinutes(30);

TimeSpan sum = oneHour + halfHour; // 添加时间跨度
TimeSpan difference = oneHour - halfHour; // 减去时间跨度

Console.WriteLine($"总时间：{sum.TotalMinutes} 分钟");
Console.WriteLine($"差：{difference.TotalMinutes} 分钟");
```

## 4. 比较时间跨度：

```
TimeSpan time1 = TimeSpan.FromHours(5);
TimeSpan time2 = TimeSpan.FromHours(7);

int comparison = time1.CompareTo(time2);

if (comparison < 0)
{
    Console.WriteLine("time1 小于 time2");
}
else if (comparison > 0)
{
    Console.WriteLine("time1 大于 time2");
}
else
{
    Console.WriteLine("time1 等于 time2");
}
```

这些示例展示了如何创建、计算、比较和执行其他与时间相关的操作。`TimeSpan` 对象对于在编程中处理时间非常有用，可以用于管理时间跨度和持续时间。

# 三、 更改会议时间戳

```
  public List<TencentMeetingRecord> ConvertMillisecondsToSeconds(List<TencentMeetingRecord> meetingRecords, CancellationToken cancellationToken)
    {
        return meetingRecords.Select(record =>
        {
            var startSeconds = TimeSpan.FromMilliseconds(record.RecordStartTime).Ticks;

            var endSeconds = TimeSpan.FromMilliseconds(record.RecordEndTime).Ticks;

            return new TencentMeetingRecord
            {
                RecordStartTime = startSeconds,
                RecordEndTime = endSeconds
            };
        }).ToList();
    }
```

TimeSpan.FromMilliseconds方法被用来从记录的RecordStartTime(以毫秒为单位)创建一个TimeSpan对象。

这里之所以能够通过Ticks属性把它转换成秒数,是利用了ticks和秒数之间的转换关系:

1秒 = 10,000,000 ticks，也就是:1 tick = 0.0000001 秒，所以:ticks数 * 0.0000001 = 秒数
具体到这个代码片段:
TimeSpan对象中的Ticks数 * 0.0000001 = 开始时间对应的秒数

之所以可以直接通过Ticks来获取秒数,是因为TimeSpan类在输出Ticks时会自动把ticks数值按照上述比例转换为秒数。

也就是TimeSpan会在输出时做这个ticks到秒数的转换计算,所以我们可以直接通过Ticks属性获取时间间隔的总秒数。这是利用了TimeSpan的这个自动转换功能,让我们更方便地通过Ticks来获取秒级精度的时间值。