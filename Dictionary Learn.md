# 一、Dictionary<TKey, TValue> 字典

Dictionary<TKey, TValue> 是一个泛型类，用于表示键值对的集合。（也就是讲一个value和一个唯一的key关联起来）
TKey 表示键的类型，TValue 表示值的类型。

## 1.创建和初始化字典

```
// 创建字典
Dictionary<string, int> ages = new Dictionary<string, int>();

```

## 2.添加和访问元素
可以使用 Add 方法将键值对添加到字典中，并使用索引器（[]）访问字典中的元素：

```
public void Test()
{
    var scores = new Dictionary<string, int>
    {
        // 添加键值对
        { "Alice", 20 },
        { "Bob", 30 },
        { "lizzie", 40 }
    };

    // 访问元素
    var myScore = scores["lizzie"];
    
    Console.WriteLine(myScore);   
}
```

## 3.判断键是否存在
使用 ContainsKey 方法可以检查字典中是否存在指定的键：

```
if (scores.ContainsKey("Alice"))
{
    // 键存在
}
```

## 4.更新元素
可以使用索引器（[]）来更新字典中的元素：

```
scores["Bob"] = 90;
```

## 5.删除元素
使用 Remove 方法可以从字典中删除指定的键值对：

```
scores.Remove("Alice");
```

## 6.遍历字典
可以使用 foreach 循环遍历字典中的键值对：

```
foreach (var pair in scores)
{
    string name = pair.Key;
    int score = pair.Value;
    
    // 处理键值对
}
```

## 7.字典的长度
可以使用 Count 属性获取字典中键值对的数量：

```
int count = scores.Count;
```

# 二、C#中的LINQ查询

## 1.过滤数据 (where):

使用 where 关键字来筛选满足特定条件的元素。

```
var filteredData = collection.Where(item => item.Property > 10);
```

## 2.排序数据 (orderby):

使用 orderby 关键字对元素进行排序。

```
var sortedData = collection.OrderBy(item => item.Property);
```

## 3.投影数据 (select):

使用 select 关键字选择要返回的属性或计算结果。

```
var projectedData = collection.Select(item => item.Property);
```

## 4.聚合数据 (Sum, Count, Average, Min, Max):

使用聚合方法对数据进行统计计算。
```
var sum = collection.Sum(item => item.Property);
var count = collection.Count();
var average = collection.Average(item => item.Property);
var min = collection.Min(item => item.Property);
var max = collection.Max(item => item.Property);
```

## 5.查找元素 (First, FirstOrDefault, Single, SingleOrDefault):

使用这些方法查找满足条件的元素。
```
var firstItem = collection.First(item => item.Property > 5);
var firstOrDefaultItem = collection.FirstOrDefault(item => item.Property > 5);
var singleItem = collection.Single(item => item.Id == 1);
var singleOrDefaultItem = collection.SingleOrDefault(item => item.Id == 1);
```

## 6.分组数据 (group by):

使用 group by 关键字将数据按照指定的键进行分组。

```
var groupedData = collection.GroupBy(item => item.Category);
```

## 7.连接数据 (Join):

使用 Join 方法将两个集合中的元素根据指定的键进行连接。

```
var joinedData = collection1.Join(collection2, item1 => item1.Key, item2 => item2.Key, (item1, item2) => new { item1, item2 });
```

## 8.into

是LINQ查询语法中的关键字，用于引入一个新的标识符，将查询结果进一步处理或命名。

```
var wordGroups1 =
    from w in words
    group w by w[0] into fruitGroup
    where fruitGroup.Count() >= 2
    select new { FirstLetter = fruitGroup.Key, Words = fruitGroup.Count() };
```

into fruitGroup 表示将通过 group by 分组的结果命名为 fruitGroup，以便在 where 子句和 select 子句中引用它。