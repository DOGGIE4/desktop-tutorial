# 一、AutoMapper是什么？

AutoMapper 是一个开源的对象映射库。Automapper可以快速实现对象之间的映射，主要作用是将一个对象的属性值映射到另一个对象中，从而实现对象之间的转换。

# 二、举个例子🌰 用反射实现一个简易的 AutoMapper

```
public static TTarget Map<TSource, TTarget>(TSource source, TTarget target)
{
    //获取源对象和目标对象的类型信息
    var sourceType = typeof(TSource);
    var targetType = typeof(TTarget);

    //GetProperties(）方法获取源对象中的所有属性
    foreach (var sourceProperty in sourceType.GetProperties()) 
    {
        //用源对象属性的名称来查找目标对象中的属性
        var targetProperty = targetType.GetProperty(sourceProperty.Name);

        //如果目标对象不为空且可以输入，就将源对象的值映射到另一个对象中
        if (targetProperty != null && targetProperty.CanWrite)
        {
            var value = sourceProperty.GetValue(source);
            targetProperty.SetValue(target, value);
        }
    }

    return target;
}
```

# 三、AutoMapper的使用

## 1.先创建映射规则

创建一个继承自 Profile 的类 SmartFaqMapping，并在构造函数中通过 CreateMap 方法定义了一个从 UserQuestion 到 UserQuestionDto 的映射规则。

```
public class SmartFaqMapping : Profile
{
   public SmartFaqMapping()
   {
      CreateMap<UserQuestion, UserQuestionDto>();
   }
}
```

## 2. 使用映射器对象执行映射

Handler中

```
public class GetUserQuestionsForReviewRequestHandler : IRequestHandler<GetUserQuestionsForReviewRequest, GetUserQuestionsForReviewResponse>
{
    private readonly ISmartFaqService _smartFaqService;

    public GetUserQuestionsForReviewRequestHandler(ISmartFaqService smartFaqService)
    {
        _smartFaqService = smartFaqService;
    }
    
    public async Task<GetUserQuestionsForReviewResponse> Handle(IReceiveContext<GetUserQuestionsForReviewRequest> context, CancellationToken cancellationToken)
    {
       return await _smartFaqService.GetUserQuestionsForReviewResponseAsync(context.Message, cancellationToken).ConfigureAwait(false);
    }
}
```

Service层
先注入一个IMapper的对象，用于执行对象映射操作。这个 IMapper 对象将 UserQuestion 对象映射到 UserQuestionDto 对象。
在 Handle 方法中，创建了一个 UserQuestion 对象，使用 _mapper 对象将其映射到 UserQuestionDto 对象。

```
public class SmartFaqService : ISmartFaqService
{
    private readonly IMapper _mapper;

    public SmartFaqService(IMapper mapper)
    {
        _mapper = mapper;
    }

    public async Task<GetUserQuestionsForReviewResponse> GetUserQuestionsForReviewResponseAsync(
        GetUserQuestionsForReviewRequest response,
        CancellationToken cancellationToken)
        {
            {
           
                var userQuestion = new UserQuestion()
                {
                    Id = 1,
                    AskBy = "AskBy",
                    Question = "question",
                    RasaPredictedQid = 10
                };

                var userQuestionDto = _mapper.Map<UserQuestionDto>(userQuestion);

                return new GetUserQuestionsForReviewResponse();
            }
        }
}
```

通过问题列表转换为DTO对象列表，就可以把数据库查到的数据转换为前段可以使用的对象啦🎉