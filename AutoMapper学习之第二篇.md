
# 一、Smartfaq中的使用

我们要将 GetUserQuestionsForReviewRequest 类型的请求对象中的属性赋值给 GetUseQuestionsQuery 类型的查询对象

## 1.先创建映射规则：

```
public class SmartFaqMapping : Profile
{
   public SmartFaqMapping()
   {
      CreateMap<UserQuestion, UserQuestionDto>();
      CreateMap<GetUserQuestionsForReviewRequest, GetUseQuestionsQuery>();
   }
}
```

CreateMap<TSource, TDestination>()
所以 我们是把UserQuestion映射到UserQuestionDto中，把GetUserQuestionsForReviewRequest映射到GetUseQuestionsQuery中

## 2.使用映射器对象执行映射

```
public async Task<GetUserQuestionsForReviewResponse> GetUserQuestionsForReviewResponseAsync(
        GetUserQuestionsForReviewRequest request, CancellationToken cancellationToken)
    {
        var getUseQuestionsQuery = _mapper.Map<GetUseQuestionsQuery>(request);

        var (count, userQuestions) = await _smartFaqDataProvider
            .GetUserQuestionsAsync(getUseQuestionsQuery, cancellationToken).ConfigureAwait(false);

        return new GetUserQuestionsForReviewResponse
        {
            Total = count,
            Data = userQuestions
        };
    }
}
```

Map<TDestination>(object source);
Map<TDestination>(object source) 方法接受一个对象 source，并将其映射到目标类型 TDestination 的对象中。
request 的对象映射为类型为 GetUseQuestionsQuery 的对象，从而实现了两个对象之间的转换。具体来说，我们将 request 对象中的属性值映射到 GetUseQuestionsQuery 对象中的属性值，以便在操作中使用。

我们使用 AutoMapper 将 request 对象映射为 GetUseQuestionsQuery类型的对象，并将映射后的结果存储在 getUseQuestionsQuery 变量中。然后，我们调用 _smartFaqDataProvider.GetUserQuestionsAsync 方法，将 getUseQuestionsQuery 变量作为参数传递给该方法，以获取用户问题列表的响应结果。

# 二、Person中的使用

## 1. 先创建映射规则

```
public class PeopleMapping : Profile
{
   public PeopleMapping()
   {
      CreateMap<CreatPeopleDto, Person>();
      CreateMap<UserQuestionDto, Person>();
   }
}
```

CreateMap<TSource, TDestination>()

即：CreatPeopleDto映射到Person中

## 2.使用映射器对象执行映射

```
public async Task<PeopleCreatedEvent> AddPersonAsync(CreatePeopleCommand command, CancellationToken cancellationToken)
{
    var response = await _personDataProvider.CreatAsync(_mapper.Map<Person>(command.person), cancellationToken).ConfigureAwait(false) > 0
        ? "数据写入成功"
        : "数据写入失败";
            
    return new PeopleCreatedEvent
    {
        result = response
    };
}
```

Map<TDestination>(object source);
把command.person映射为Person，映射结果存储在response中

# 二、DTO

DTO 是一种简单的数据结构，用于封装数据并传输到其他层。通常情况下，DTO 是在应用程序的不同层之间传递数据的一种方式，例如在控制器和服务层之间、服务层和数据访问层之间等。

在实践中，DTO 可以帮助我们实现以下目标：

1.分离业务逻辑和数据访问逻辑：DTO 可以帮助我们将业务逻辑和数据访问逻辑分离，从而提高代码的可维护性和可测试性。

2.提高性能：DTO 可以避免传输不必要的数据，从而提高性能。

3.简化数据传输：DTO 可以将多个实体对象的属性封装在一个对象中，从而简化数据传输。

## 举例

使用DTO类UserQuestionDto的集合作为响应对象GetUserQuestionsForReviewResponse的Data属性的类型。
```
 public async Task<GetUserQuestionsForReviewResponse> GetUserQuestionsForReviewResponseAsync(
        GetUserQuestionsForReviewRequest request, CancellationToken cancellationToken)
 {
     var getUseQuestionsQuery = _mapper.Map<GetUseQuestionsQuery>(request);

     var (count, userQuestions) = await _smartFaqDataProvider
         .GetUserQuestionsAsync(getUseQuestionsQuery, cancellationToken).ConfigureAwait(false);

     return new GetUserQuestionsForReviewResponse
     {
         TTotal = count,
         TData = userQuestions
     };
 }
```

GetUserQuestionsForReviewResponse 类中的 Data 属性是一个列表，其中每个元素都是 UserQuestionDto 类型的对象，用于封装用户问题信息并在应用程序的不同层之间传递。

```
public class GetUserQuestionsForReviewResponse : IResponse
{
    public List<UserQuestionDto> Data { get; set; }
    public int Total { get; set; }
}
```

定义一个DTO类UserQuestionDto，它包含了用户问题的各个属性，例如Id、CreatedAt、Question等。

```
public class UserQuestionDto
{
    public int Id { get; set; }

    public int CreatedAt { get; set; }

    public string Question { get; set; }

    public int RasaPredictedQid { get; set; }

    public decimal? RasaConfidence { get; set; }

    public int AnyqPredictedQid { get; set; }

    public decimal? AnyqConfidence { get; set; }

    public int CorrectQid { get; set; }
    
    public int ModelPredictedQid { get; set; }

    public decimal? ModelConfidence { get; set; }

    public int Status { get; set; }

    public string Remark { get; set; }

    public string AskBy { get; set; }
}
```

GetUserQuestionsForReviewResponse 类中的 Data 属性是一个列表，其中每个元素都是 UserQuestionDto 类型的对象，用于封装用户问题信息并在应用程序的不同层之间传递。

这三段代码实现了将一个请求对象转换成一个响应对象的过程，其中涉及到了DTO（数据传输对象）的使用。在控制器中调用GetUserQuestionsForReviewResponseAsync方法时，可以更加方便地将获取到的用户问题列表转换成DTO对象并组装成响应对象返回给客户端。这样可以避免暴露实体类的属性，并使得响应对象更加灵活和可维护。