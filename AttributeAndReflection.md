# 一、Attribute

在C#中，Attribute（特性）是一种用于为代码元素（如类、方法、属性等）添加元数据的语言结构。Attribute 可以在代码中以一种声明式的方式添加，以提供有关代码元素的附加信息和功能。

Attribute 通常使用方括号[]将其放置在代码元素（如类、方法、属性等）的声明之前。
例如，可以在一个类的声明前添加一个 [Serializable] 特性，来表示该类可以被序列化。例如：

```
[Serializable]
public class MyClass
{
    // class implementation here
}

```
在这个例子中，我们使用了 [Serializable] 特性来表示 MyClass 类可以被序列化。这个特性可以帮助序列化器了解如何序列化 MyClass 对象，并在序列化和反序列化时提供一些额外的功能。

.Net 框架提供了两种类型的特性：预定义特性和自定义特性。

## 1、预定义特性

### （1）AttributeUsage

预定义特性 AttributeUsage 描述了如何使用一个自定义特性类。它规定了特性可应用到的项目的类型。
规定该特性的语法如下：

```
[AttributeUsage(
   validon,//规定特性可被放置的语言元素。它是枚举器 AttributeTargets 的值的组合。默认值是 AttributeTargets.All。
   AllowMultiple=allowmultiple,//(可选的）为该特性的 AllowMultiple 属性（property）提供一个布尔值。如果为 true，则该特性是多用的。默认值是 false（单用的）。
   Inherited=inherited//（可选的）为该特性的 Inherited 属性（property）提供一个布尔值。如果为 true，则该特性可被派生类继承。默认值是 false（不被继承）。
)]
```

例如：

```
[AttributeUsage(AttributeTargets.Class | 
AttributeTargets.Constructor | 
AttributeTargets.Field | 
AttributeTargets.Method | 
AttributeTargets.Property,  //可以将该属性标记（注释）到类、构造函数、字段、方法或属性上。
AllowMultiple = true)] //可以在同一个目标上多次使用相同的自定义属性
```

### （2）Conditional

这个预定义特性标记了一个条件方法，其执行依赖于指定的预处理标识符。

它会引起方法调用的条件编译，取决于指定的值，比如 Debug 或 Trace。例如，当调试代码时显示变量的值。

规定该特性的语法如下：

```
[Conditional(
   conditionalSymbol
)]
```

例子：

```
#define DEBUG  // #define DEBUG 指令定义了一个名为 DEBUG 的符号，这将启用调试模式。
using System;
using System.Diagnostics;
public class Myclass
{
   [Conditional("DEBUG")] //只有在代码中定义了 DEBUG 符号时，Message() 方法的调用才会被编译进最终的可执行文件。
   public static void Message(string msg)
   {
      Console.WriteLine(msg);
   }
}
class Test
{
   static void function1()
   {
      Myclass.Message("In Function 1.");
      function2();
   }
   static void function2()
   {
      Myclass.Message("In Function 2.");
   }
   public static void Main()
   {
      Myclass.Message("In Main function.");//调用了 Myclass.Message("In Main function.")
      function1();
      Console.ReadKey();
   }
}
```
输出的内容是：

```
In Main function.
In Function 1.
In Function 2.
```

ps:如果在项目的构建选项中未定义 DEBUG 符号，或者在代码中没有添加 #define DEBUG 指令，那么 Myclass.Message() 方法的调用将被排除，输出将只有 "In Main function."。

## 2、自定义Attribute
创建并使用自定义特性包含四个步骤：

1.声明自定义特性

2.构建自定义特性

3.在目标程序元素上应用自定义特性

4.通过反射访问特性

在 C# 中，可以通过定义自定义 Attribute（特性）来为代码元素添加自定义的元数据。自定义 Attribute 可以通过类来定义，该类必须继承自 System.Attribute 基类，并且必须以 Attribute 结尾

```
// CustomAttribute 特性应用到了 MyClass 类和 MyMethod 方法上。
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = false)]
//创建了一个名为 CustomAttribute 的自定义特性类，它派生自 Attribute 基类。
//这个特性类带有一个带有参数的构造函数和一个打印消息的方法。
public class CustomAttribute : Attribute
{
    private string message;

    public CustomAttribute(string message)
    {
        this.message = message;
    }

    public void PrintMessage()
    {
        Console.WriteLine(message);
    }
}

//将 CustomAttribute 特性应用到了 MyClass 类和 MyMethod 方法上
[Custom("This is a custom attribute example.")]
public class MyClass
{
    [Custom("This is a custom attribute on a method.")]
    public void MyMethod()
    {
        Console.WriteLine("Executing MyMethod.");
    }
}

public class Program
{
    public static void Main() 
    {
        //创建了一个 MyClass 的实例，并调用了 MyMethod 方法
        MyClass myClass = new MyClass();
        myClass.MyMethod();

        //使用 Attribute.GetCustomAttribute 方法获取应用在 MyClass 类的CustomAttribute 对象
        CustomAttribute classAttribute = (CustomAttribute)Attribute.GetCustomAttribute(typeof(MyClass), typeof(CustomAttribute));
        //调用其 PrintMessage 方法来打印消息。
        classAttribute?.PrintMessage();

        // 获取 MyMethod 方法上的 CustomAttribute 对象。
        CustomAttribute methodAttribute = (CustomAttribute)Attribute.GetCustomAttribute(typeof(MyClass).GetMethod("MyMethod"), typeof(CustomAttribute));
        methodAttribute?.PrintMessage();
    }
}
```

运行这段代码，将会输出以下结果：

```
Executing MyMethod.
This is a custom attribute example.
This is a custom attribute on a method.
```

首先调用myclass。mymethod()方法，输出“Executing MyMethod“；

然后通过 Attribute.GetCustomAttribute 方法获取到了 CustomAttribute 对象，并调用其 PrintMessage 方法，打印了特性中的消息，即第二行输出 "This is a custom attribute example."，
同理可得第三行“This is a custom attribute on a method.”

# 二、Reflection

反射指程序可以访问、检测和修改它本身状态或行为的一种能力。
程序集包含模块，而模块包含类型，类型又包含成员。反射则提供了封装程序集、模块和类型的对象。
可以使用反射动态地创建类型的实例，将类型绑定到现有对象，或从现有对象中获取类型。然后，可以调用类型的方法或访问其字段和属性。

使用 C# 反射，可以执行以下操作：

1.获取类型信息：可以使用反射 API 获取类型的信息，如类的名称、命名空间、基类、实现的接口、属性、方法等。

2.创建对象：通过反射，可以在运行时根据类型信息创建对象的实例，而无需在编译时确定类型。

3.调用方法和访问属性：可以使用反射调用对象的方法、访问属性和字段，甚至可以调用私有成员。

4.操作程序集：反射使您能够加载和浏览程序集（包括动态加载的程序集），并检查其中的类型和成员。

5.动态修改对象和类型：您可以使用反射在运行时修改对象的属性和字段的值，添加或移除属性、字段和方法等。

6.创建泛型类型实例：通过反射，可以动态地创建泛型类型的实例，以及调用泛型方法。

通过反射 API 查看特性（Attribute）信息

```
[AttributeUsage(AttributeTargets.Class)]
public class CustomAttribute : Attribute
{
   //CustomAttribute特性具有一个Name属性，用于存储特性的名称。
   public string Name { get; set; }

   public CustomAttribute(string name)
   {
      Name = name;
   }
}

[Custom("SampleClass")]
public class MyClass
{
   public void MyMethod()
   {
      Console.WriteLine("MyMethod is called.");
   }
}

public class Program
{
   static void Main(string[] args)
   {
      //typeof(MyClass)获取MyClass类型的Type对象
      Type type = typeof(MyClass);

      // 获取类的特性
      object[] attributes = type.GetCustomAttributes(true);

      //遍历这些特性
      foreach (var attribute in attributes)
      {
         //特性是CustomAttribute类型的，我们将打印出特性的名称。
         if (attribute is CustomAttribute customAttribute)
         {
            Console.WriteLine("Attribute Name: " + customAttribute.Name);
         }
      }

      Console.ReadLine();
   }
}
```