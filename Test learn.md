# 一、单元测试（Unit Testing）：

目的：单元测试是针对软件中最小的可测试单元（通常是函数、方法或类）进行的测试。其目的是验证单个单元的功能是否正确，以确保其在隔离环境中的行为符合预期。

范围：单元测试的范围仅限于测试单个单元，通常通过模拟或替代依赖来隔离被测试单元。

执行方式：单元测试通常由开发人员编写，并在开发过程中频繁执行，以验证代码的正确性和稳定性。

# 二、集成测试（Integration Testing）：

目的：集成测试是验证多个组件或模块在一起协同工作的测试。其目的是确保各个组件之间的交互和集成正常，并检测潜在的集成问题。

范围：集成测试的范围比单元测试更广，涵盖多个组件、模块或服务之间的交互。它可以包括数据库、API、消息队列等外部依赖的测试。

执行方式：集成测试可以由开发人员或专门的测试团队编写和执行。它通常在开发阶段的后期或集成阶段进行，以确保各个组件的集成是正确的。

# 三、E2E测试（End-to-End Testing）

目的：E2E 测试是模拟真实用户场景，测试整个应用程序或系统的各个组件在集成状态下的行为。其目的是验证整个系统的功能、流程和交互是否按预期工作。

范围：E2E 测试覆盖整个应用程序或系统，包括用户界面、后端逻辑、数据库和外部服务的集成。它模拟用户从开始到结束的完整工作流程。

执行方式：E2E 测试通常由测试团队编写和执行，使用自动化测试框架来模拟用户操作，并验证系统的行为和结果。它可以在开发阶段的后期或系统部署前执行。

# 四、三者之间的联系与区别

## 4.1 联系：

1.这三种测试类型都是为了提高软件质量、发现问题和确保系统的正确性。

2.它们在不同层次和范围上相互补充，从最小的单元到整个系统，逐步验证系统的各个部分。

3.单元测试和集成测试可以由开发人员编写和执行，而 E2E 测试通常由测试团队负责。

4.单元测试和集成测试通常在开发过程中频繁执行，而 E2E 测试可能是一个更终验收的阶段。

5.所有这些测试类型都有助于提前发现和修复问题，减少后期的调试和修复成本。

## 4.2 区别

1.单元测试关注单个单元的功能和逻辑，而集成测试和 E2E 测试涉及多个组件或模块之间的协同工作。

2.单元测试是在隔离环境中执行的，而集成测试和 E2E 测试涉及到真实环境和外部依赖。

3.单元测试通常有更小的范围和更高的粒度，而集成测试和 E2E 测试的范围更广泛，更接近真实用户场景。

4.单元测试和集成测试通常由开发人员编写，而 E2E 测试可能需要测试团队的专门知识和技能。

5.在进行单元测试时，通过编写针对单个函数、方法或类的测试用例，这些测试用例覆盖了各种输入情况和预期输出。通过执行这些测试用例，可以验证代码的正确性，并捕获潜在的错误和异常行为。

#  四、AAA模式

在单元测试中，通常按照三个主要部分来组织测试方法：Arrange、Act 和 Assert，也被称为 AAA 模式。

Arrange（准备）：在这个部分，我们准备测试所需的对象实例、数据和环境。这包括创建类的实例、设置输入参数和初始化测试数据。

Act（操作）：在这个部分，我们执行要测试的操作或调用要测试的方法。这是对被测试代码进行实际操作的步骤。

Assert（断言）：在这个部分，我们验证操作的结果是否符合预期。我们使用断言方法来检查实际输出与期望输出之间的匹配性。

# 五、xunit示例

[官方文档(rider版)](https://xunit.net/docs/getting-started/netfx/jetbrains-rider)

XUnit 是一种单元测试框架，它提供了一套用于编写、运行和组织单元测试的工具和规范。
xUnit.net 支持两种不同主要类型的单元测试：事实和理论。在描述事实与理论之间的差异时，我们喜欢说：

[Fact]是永远正确的测试。他们测试不变条件。

[Theory]是仅适用于一组特定数据的测试。

示例

```
[Theory]
[InlineData(2, 2, 4)]
[InlineData(3, 3, 6)]
[InlineData(2, 2, 5)]
public void MyTheory(int x, int y, int sum)
{
   Assert.Equal(sum, Calculator.Add(x, y));
}
```

向此类测试输入不正确的总和值将导致其失败，而不是因为计算器或测试错误。

![image.png](https://upload-images.jianshu.io/upload_images/29177961-773c235cae6170a2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

虽然我们只编写了 3 个测试方法，但测试运行器实际上运行了 5 个测试；这是因为每个理论及其数据集都是一个单独的测试。另请注意，运行程序会准确告诉您哪组数据失败，因为它显示参数值。

# 六、Shouldly

[Shoudly官方文档](https://docs.shouldly.org/documentation/equality/shouldbe)

Shouldly 是一个断言库，它提供了一种更自然、更可读的方式来编写断言语句。它提供了一系列的断言方法，如 ShouldBe、ShouldNotBeNull、ShouldContain 等，用于检查值、对象、集合和异常等方面的条件。

这是旧的断言方式：

```
Assert.That(contestant.Points, Is.EqualTo(1337));
```

报错

```
Expected 1337 but was 0
```

Shoudly的断言方式:

```
contestant.Points.ShouldBe(1337);
```

报错

```
contestant.Points should be 1337 but was 0
```

人们可能很容易低估它的用处。另一个并排的例子：

```
Assert.That(map.IndexOfValue("boo"), Is.EqualTo(2));
// -> Expected 2 but was -1

map.IndexOfValue("boo").ShouldBe(2);
// -> map.IndexOfValue("boo") should be 2 but was -1
```

Shouldly使用ShouldBe语句之前的代码来报告错误，这使得诊断更容易。

# 七、NSubstitute

NSubstitute是一个用于.NET 平台的 mocking 框架，它用于创建和管理测试中的替代（substitute）对象。NSubstitute 的主要目的是帮助开发人员在单元测试中创建模拟对象，以便更容易地隔离和测试代码的行为。

```
public void TestCalculatorAdd()
{
    // 创建 Calculator 的模拟对象
    var calculator = Substitute.For<Calculator>();

    // 设置 Add 方法的行为和返回值
    calculator.Add(2, 3).Returns(5);

    // 调用 Add 方法
    int result = calculator.Add(2, 3);

    // 使用断言来验证 Add 方法的行为和返回值
    Assert.AreEqual(5, result);

    // 验证 Add 方法是否被调用了一次，传入的参数为 2 和 3
    calculator.Received(1).Add(2, 3);
}
```

ps:关于两种方式的区别：

```
Calculator calculator = new Calculator();
```

这种方式是直接实例化了 Calculator 类的实际对象。

所以，当调用 calculator.Add(2, 3) 时，实际的 Add 方法会执行，并返回真实的结果。

这种方式适用于当你想要测试真实对象的行为，而不是模拟或控制它的行为。

```
var calculator = Substitute.For<Calculator>(); 
calculator.Add(2, 3).Returns(5);
```

这种方式使用 NSubstitute 的框架创建了 Calculator 类的模拟对象。

使用 Returns(5) 方法设置模拟对象的 Add 方法在接收参数为 2 和 3 时的返回值为 5。这意味着当调用 calculator.Add(2, 3) 时，模拟对象会返回预定义的结果。

这种方式适用于当你想要测试被测试代码与外部依赖项的交互并控制其行为。
