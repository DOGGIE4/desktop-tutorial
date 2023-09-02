AutoMapper 提供了多种选项和方法来定义属性映射的规则。以下是一些常用的选项和方法：

1.CreateMap<TSource, TDestination>()：

使用该方法创建源类型到目标类型的映射配置。例如：

```
CreateMap<Source, Destination>();
```

2.ForMember(dest => dest.Property, opt => opt.MapFrom(src => src.OtherProperty))：

使用该方法指定属性之间的映射关系。可以通过 lambda 表达式指定源属性到目标属性的映射规则。例如：

```
ForMember(dest => dest.Property, opt => opt.MapFrom(src => src.OtherProperty));
```

3.Ignore()：

使用该方法忽略某个属性的映射，即不将源属性映射到目标属性。例如：

```
ForMember(dest => dest.Property, opt => opt.Ignore());
```

4.MapFrom()：

使用该方法指定目标属性从源对象中的哪个属性进行映射。例如：

 ```
ForMember(dest => dest.Property, opt => opt.MapFrom(src => src.OtherProperty));
```

5.ConvertUsing()：

使用该方法指定自定义的类型转换器来进行属性映射。

可以创建一个实现 ITypeConverter<TSource, TDestination> 接口的类型转换器，并在映射配置中使用 ConvertUsing() 方法指定使用该转换器。

```
ConvertUsing(new CustomConverter());
```

6.Condition()：

使用该方法指定一个条件来决定是否应用属性映射规则。

可以根据源对象的某个属性值进行条件判断，决定是否进行属性映射。例如：

```
ForMember(dest => dest.Property, opt => opt.MapFrom(src => src.OtherProperty))
    .Condition(src => src.OtherProperty != null);
```

AutoMapper还提供了一些高级选项和方法，用于处理更复杂的映射需求。以下是一些常用的高级选项和方法：

1.BeforeMap() 和 AfterMap()：

使用这些方法可以在映射前或映射后执行自定义的操作。

可以通过 lambda 表达式指定要执行的操作。例如：

```
BeforeMap((src, dest) => { /* 在映射前执行的操作 */ })
AfterMap((src, dest) => { /* 在映射后执行的操作 */ })
```

2.ConstructUsing()：

使用该方法可以指定在创建目标对象时使用的构造函数。

可以传递一个 lambda 表达式来创建目标对象。例如：

```
ConstructUsing(src => new Destination(src.Property))
```

3.ForMember() 中的 Condition()：

除了用于简单的条件判断外，Condition() 还可以接收一个 lambda 表达式，允许执行更复杂的条件判断逻辑。例如：

```
ForMember(dest => dest.Property, opt => opt.MapFrom(src => src.OtherProperty))
    .Condition((src, dest, context) => context.Options.Items["SomeKey"] == "SomeValue");
```

4.ConvertUsing() 中的 MapFrom()：

ConvertUsing() 方法可以与 MapFrom() 方法结合使用，以提供更灵活的类型转换。

可以指定源属性到目标属性的映射规则，或者使用自定义转换器进行转换。例如：

```
ConvertUsing(src => new Destination(src.Property), opts => opts.MapFrom(src => src.OtherProperty))
```

5.IncludeMembers()：

使用该方法可以指定在映射过程中包含其他成员的映射规则。

可以将其他成员的映射规则合并到当前配置中。例如：

```
IncludeMembers(src => src.OtherMember)
```