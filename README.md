# 委托

## 委托的定义

委托功能类似于C++当中的函数指针，它存储着一系列具有相同签名和返回类型的方法的地址，调用委托的时候，它所指向的方法都会被执行。

在 C# 中，委托允许将方法的引用作为参数进行传递或者赋值给一个变量。委托的基本语法如下：

public delegate 返回类型 委托名(参数类型1 参数1, 参数类型2 参数2, ...);

示例：

```c#
public delegate int MathOperation(int a, int b)；
```

这里定义了一个委托 `MathOperation`，它可以指向任何接受两个 `int` 参数并返回一个 `int` 类型结果的方法。

## 委托的本质

-是一种类型安全的函数指针

-是引用类型，继承自`System.MulticastDelegate`

-可以指向静态方法或实例方法

## 委托的用法

### 委托基本用法

一旦定义了委托类型，就可以创建委托实例，并将其指向一个符合签名的方法。以下是一个创建委托并调用方法的例子：

```c#
 public class Program
 {
     public delegate int MathOperation(int a, int b);
     public static int Add(int a, int b)
     {
         return a + b;
     }
     public static int Subtract(int a, int b)
     {
         return a - b;
     }
     static void Main()
     {
         // 创建委托实例并指向 Add 方法
         MathOperation operation = new MathOperation(Add);
         Console.WriteLine(operation(10, 5)); // 输出 15
         // 将委托指向 Subtract 方法
         operation = new MathOperation(Subtract);
         Console.WriteLine(operation(10, 5)); // 输出 5
         Console.ReadKey();
     }
 }
```

在这个例子中，`MathOperation` 是委托类型，指向接受两个整数并返回整数的方法。我们通过创建委托实例并赋值给不同的方法来实现方法的切换。

### 多播委托

```c#
 public delegate void Notify(string message);
 public class Program
 {
     public static void SendEmail(string message)
     {
         Console.WriteLine("Sending Email: " + message);
     }
     public static void SendSMS(string message)
     {
         Console.WriteLine("Sending SMS: " + message);
     }
     public static void Main()
     {
         Notify notify = new Notify(SendEmail);
         notify += SendSMS; // 添加方法
         notify("Hello World!"); // 输出：Sending Email 和 Sending SMS
         Console.ReadLine();
     }
 }
```

### 内置委托

`Action`：无返回值的方法类型，通常用于处理不需要返回值的操作。

`Func`：有返回值的方法类型，最多可以接受 16 个输入参数。

`Predicate`：返回布尔值的方法类型，通常用于条件判断。

```c#
// Action示例
Action<string> greet = name => Console.WriteLine("Hello, " + name);
greet("Alice"); // 输出 "Hello, Alice"
// Func示例
Func<int, int, int> add = (a, b) => a + b;
Console.WriteLine(add(10, 5)); // 输出 15
// Predicate示例
Predicate<int> isEven = num => num % 2 == 0;
Console.WriteLine(isEven(4)); // 输出 True
```

### 泛型委托

```c#
// 定义一个泛型委托，用于比较两个相同类型的对象
delegate bool CompareDelegate<T>(T x, T y);
internal class Program
{
    static void Main(string[] args)
    {
        // 使用int类型的比较
        PerformComparison(5, 5, IntEquals);
        PerformComparison(3, 7, IntEquals);
        // 使用string类型的比较
        PerformComparison("hello", "world", StringLengthEquals);
        PerformComparison("apple", "banana", StringLengthEquals);
        // 使用Lambda表达式
        PerformComparison( 10.5, 10.5, (x,  y) => x == y);
        Console.ReadLine();
    }
    // 比较两个整数是否相等
    static bool IntEquals(int a, int b)
    {
        return a == b;
    }
    // 比较两个字符串长度是否相等
    static bool StringLengthEquals(string a, string b)
    {
        return a.Length == b.Length;
    }
    // 通用的比较方法
    static void PerformComparison<T>(T obj1, T obj2, CompareDelegate<T> comparer)
    {
        bool result = comparer(obj1, obj2);
        Console.WriteLine($"比较结果: {result}");
    }
}
```

### 匿名方法与 Lambda 表达式

**匿名方法**

匿名方法是委托的一种定义方式，它不需要指定方法名，而是直接在委托实例化时提供方法体。

示例：

```c#
MathOperation operation = delegate(int a, int b)
{
  return a + b;
};
Console.WriteLine(operation(10, 5)); // 输出 15
```

**Lambda表达式**

Lambda 表达式是一种更简洁的匿名方法表示方式，使用 => 符号来分隔参数和方法体。Lambda 表达式使得代码更加简洁和易读。

示例：

```c#
MathOperation operation = (a, b) => a + b;
Console.WriteLine(operation(10, 5)); // 输出 15
```

### 异步委托

委托还可以用于异步编程。通过 `BeginInvoke` 和 `EndInvoke` 方法，委托可以异步调用方法，而不阻塞当前线程。

```c#
public delegate void LongRunningOperation();
internal class Program
{
    public static void DoWork()
    {
        Console.WriteLine("Starting work...");
        Thread.Sleep(4000);  // 模拟长时间运行的操作
        Console.WriteLine("Work done.");
    }
    public static void Main()
    {
        //创建委托实例
        LongRunningOperation operation = new LongRunningOperation(DoWork);
        // 异步执行
        IAsyncResult result = operation.BeginInvoke(null, null);
        Console.WriteLine("Main method continues running while work is in progress.",5);
        operation.EndInvoke(result);  // 等待异步操作完成
        Console.ReadLine();
    }
}
```

## 委托的优缺点

### 优点

灵活性：委托提供了灵活的方式来调用方法，可以将方法引用作为参数传递，或者赋值给委托实例。

解耦合：委托将方法的调用者和被调用方法解耦，提高了代码的可维护性。

支持多播和异步：委托可以指向多个方法，支持异步调用，适用于事件驱动和回调函数等应用场景。

### 缺点

性能开销：委托作为对象引用会引入一定的性能开销，特别是在频繁调用的场景中。

调试复杂性：多播委托可能导致调试变得复杂，因为委托可能指向多个方法，调用时顺序不可控。

```c#
 public class Program
 {
     static void EmptyMethod() { }
     static void Main()
     {
         const int iterations = 100000000;
         // 直接方法调用
         var sw1 = Stopwatch.StartNew();
         for (int i = 0; i < iterations; i++)
         {
             EmptyMethod();
         }
         sw1.Stop();
         // 委托调用
         Action action = EmptyMethod;
         var sw2 = Stopwatch.StartNew();
         for (int i = 0; i < iterations; i++)
         {
             action();
         }
         sw2.Stop();
         Console.WriteLine($"直接调用: {sw1.ElapsedMilliseconds}ms");
         Console.WriteLine($"委托调用: {sw2.ElapsedMilliseconds}ms");
         Console.ReadLine(); 
     }
 }
```

# 事件

## 事件的概念

委托与事件密切相关。C# 中的事件常常使用委托来定义。事件提供了一种机制，允许对象通知其他对象其状态的变化。事件通常由一个委托来声明，允许其他对象（事件订阅者）注册事件处理方法。

## 事件的五大组成

事件拥有者、事件成员、事件响应者、事件处理器、事件订阅

## 标准事件模式

```c#
public class Button
{
    public delegate void ClickEventHandler(object sender, EventArgs e); //定义一个委托
    public event ClickEventHandler Click;//声明一个委托事件

    public void OnClick()
    {
        Click?.Invoke(this, EventArgs.Empty); // 触发事件
    }
}
public class Program
{
    public static void Main()
    {
        Button button = new Button();
        button.Click += (sender, e) => Console.WriteLine("Button clicked!");//订阅

        button.OnClick(); // 输出 "Button clicked!"
        Console.ReadLine();
    }
}
```

在这个例子中，Button 类定义了一个 Click 事件，它使用 `ClickEventHandler` 委托来封装事件处理方法。当按钮被点击时，OnClick 方法触发该事件，所有订阅该事件的方法都会被执行。

## 事件的其他用法

### 自定义参数事件

```c#
 // 定义一个自定义事件参数的类，必须继承自 EventArgs
 public class TemperatureChangedEventArgs : EventArgs
 {
     // 私有字段
     private readonly double _oldTemperature;
     private readonly double _newTemperature;
     private readonly DateTime _changeTime;
     // 构造函数，初始化事件参数
     public TemperatureChangedEventArgs(double oldTemp, double newTemp, DateTime changeTime)
     {
         _oldTemperature = oldTemp;
         _newTemperature = newTemp;
         _changeTime = changeTime;
     }
     // 只读属性，获取变化前的温度
     public double OldTemperature
     {
         get { return _oldTemperature; }
     }
     // 只读属性，获取变化后的温度
     public double NewTemperature
     {
         get { return _newTemperature; }
     }
     // 只读属性，获取温度变化的时间
     public DateTime ChangeTime
     {
         get { return _changeTime; }
     }
     // 计算温度变化的差值
     public double TemperatureDifference
     {
         get { return _newTemperature - _oldTemperature; }
     }
 }
 // 温度监控类，包含事件定义
 public class TemperatureMonitor
 {
     private double _currentTemperature;
     // 定义事件，使用自定义的事件参数类型
     public event EventHandler<TemperatureChangedEventArgs> TemperatureChanged;
     // 当前温度属性
     public double CurrentTemperature
     {
         get { return _currentTemperature; }
         set
         {
             if (_currentTemperature != value)
             {
                 double oldTemp = _currentTemperature;
                 _currentTemperature = value;
                 // 当温度变化时，触发事件
                 OnTemperatureChanged(oldTemp, _currentTemperature);
             }
         }
     }
     // 触发事件的受保护方法
     protected virtual void OnTemperatureChanged(double oldTemp, double newTemp)
     {
         // 创建事件参数实例
         TemperatureChangedEventArgs args = new TemperatureChangedEventArgs(
             oldTemp,
             newTemp,
             DateTime.Now);
         // 触发事件（线程安全的方式）
         TemperatureChanged?.Invoke(this, args);
     }
 }
 // 测试类
 class Program
 {
     static void Main(string[] args)
     {
         // 创建温度监控实例
         TemperatureMonitor monitor = new TemperatureMonitor();
         // 订阅温度变化事件
         monitor.TemperatureChanged += Monitor_TemperatureChanged;
         // 改变温度，这将触发事件
         Console.WriteLine("设置温度为 25.5");
         monitor.CurrentTemperature = 25.5;
         Console.WriteLine("设置温度为 30.2");
         monitor.CurrentTemperature = 30.2;
         Console.WriteLine("设置相同的温度 30.2");
         monitor.CurrentTemperature = 30.2; // 不会触发事件，因为温度没变
         Console.WriteLine("设置温度为 28.7");
         monitor.CurrentTemperature = 28.7;
     }
     // 事件处理方法
     private static void Monitor_TemperatureChanged(object sender, TemperatureChangedEventArgs e)
     {
         Console.WriteLine($"温度变化事件:");
         Console.WriteLine($"时间: {e.ChangeTime:HH:mm:ss}");
         Console.WriteLine($"旧温度: {e.OldTemperature}°C");
         Console.WriteLine($"新温度: {e.NewTemperature}°C");
         Console.WriteLine($"变化量: {e.TemperatureDifference}°C");
         Console.WriteLine();
         Console.ReadLine();
     }
 }
```

### 事件访问器

```c#
    // 定义一个消息发布者类
    public class MessagePublisher
    {
        // 用于存储事件处理程序的列表
        private List<EventHandler<string>> _messageHandlers = new List<EventHandler<string>>();
        // 事件访问器的锁对象，用于线程安全
        private readonly object _lock = new object();
        // 自定义事件访问器的消息事件
        public event EventHandler<string> MessageReceived
        {
            add
            {
                // 添加事件处理程序时的自定义逻辑
                lock (_lock)
                {
                    Console.WriteLine($"添加事件处理程序: {value.Method.Name}");

                    // 检查是否已经添加过该处理程序
                    if (!_messageHandlers.Contains(value))
                    {
                        _messageHandlers.Add(value);
                        Console.WriteLine($"事件处理程序 {value.Method.Name} 添加成功");
                    }
                    else
                    {
                        Console.WriteLine($"事件处理程序 {value.Method.Name} 已存在，不再重复添加");
                    }
                }
            }
            remove
            {
                // 移除事件处理程序时的自定义逻辑
                lock (_lock)
                {
                    Console.WriteLine($"尝试移除事件处理程序: {value.Method.Name}");

                    if (_messageHandlers.Contains(value))
                    {
                        _messageHandlers.Remove(value);
                        Console.WriteLine($"事件处理程序 {value.Method.Name} 移除成功");
                    }
                    else
                    {
                        Console.WriteLine($"事件处理程序 {value.Method.Name} 不存在，无法移除");
                    }
                }
            }
        }
        // 触发事件的方法
        public void PublishMessage(string message)
        {
            // 创建事件参数
            EventArgs e = EventArgs.Empty;
            // 临时保存事件处理程序列表，避免在遍历时被修改
            EventHandler<string>[] handlers;
            lock (_lock)
            {
                handlers = _messageHandlers.ToArray();
            }
            // 调用所有事件处理程序
            foreach (var handler in handlers)
            {
                try
                {
                    handler?.Invoke(this, message);
                }
                catch (Exception ex)
                {
                    Console.WriteLine($"事件处理程序 {handler.Method.Name} 执行出错: {ex.Message}");
                }
            }
        }
    }
    // 测试类
    class Program
    {
        static void Main(string[] args)
        {
            // 创建消息发布者
            var publisher = new MessagePublisher();
            // 定义事件处理程序1
            void HandleMessage1(object sender, string message)
            {
                Console.WriteLine($"处理程序1收到消息: {message}");
            }
            // 定义事件处理程序2
            void HandleMessage2(object sender, string message)
            {
                Console.WriteLine($"处理程序2收到消息: {message}");
                throw new Exception("故意抛出的异常，测试错误处理");
            }
            void HandleMessage3(object sender, string message)
            {
                Console.WriteLine($"处理程序3收到消息: {message}");
            }
            // 订阅事件
            Console.WriteLine("订阅事件处理程序1");
            publisher.MessageReceived += HandleMessage1;
            Console.WriteLine("\n再次订阅事件处理程序1（应该不会重复添加）");
            publisher.MessageReceived += HandleMessage1;
            Console.WriteLine("\n订阅事件处理程序2");
            publisher.MessageReceived += HandleMessage2;
            Console.WriteLine("\n订阅事件处理程序3");
            publisher.MessageReceived += HandleMessage3;
            // 发布消息
            Console.WriteLine("\n发布第一条消息");
            publisher.PublishMessage("Hello World!");
            // 移除事件处理程序
            Console.WriteLine("\n移除事件处理程序2");
            publisher.MessageReceived -= HandleMessage2;
            // 发布另一条消息
            Console.WriteLine("\n发布第二条消息");
            publisher.PublishMessage("Goodbye World!");
            // 尝试移除不存在的事件处理程序
            Console.WriteLine("\n尝试移除不存在的事件处理程序");
            publisher.MessageReceived -= HandleMessage2;
            Console.ReadLine();
        }
    }
```

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20250615193839266.png" alt="image-20250615193839266" style="zoom: 80%;" />



## 实际应用案例

在两个窗口中，如何实现空间状态的同步？

1使用共享变量来传递

2使用委托事件

（详情见代码）

## 总结

通过理解委托和事件的关系，你可以构建松耦合、可扩展的应用程序架构。事件本质上是对委托的封装，提供了更好的安全性和更清晰的语义表达。

\1. **命名规范**：

  \- 委托类型：以`EventHandler`结尾

  \- 事件：使用现在时或过去时动词（如`Click`/`Clicked`）

\2. **线程安全考虑**：

  EventHandler? handler = SomeEvent;

  handler?.Invoke(this, EventArgs.Empty);

\3. **避免内存泄漏**：

  // 订阅

  publsher.Event += HandleEvent;

  // 记得取消订阅

  publisher.Event -= HandleEvent;

\4. **使用泛型EventHandler<T>**：

  public event EventHandler<TemperatureChangedEventArgs>? TemperatureChanged;



