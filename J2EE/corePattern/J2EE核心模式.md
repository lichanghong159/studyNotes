# 模式和J2EE

## 模式的定义

模式是一个通用，[可重复使用的](https://en.wikipedia.org/wiki/Reusability)在给定的范围内解决普遍发生问题[的软件设计](https://en.wikipedia.org/wiki/Software_design)

具有以下特征：

* 模式是通过经验观察得出的
* 一般来说模式都要按一种专门的结构格式来记录
* 采用模式能够避免重复发明轮子
* 不同模式处于不同的抽象层次上
* 模式要经历不断的改进、完善
* 模式是可以重用的组件
* 模式可以用来让大家交流系统设计和最佳实践
* 多个模式可以用来拼合使用，从而解决一个大型文体。

## 模式的分类

按照功能主要分为

### 功能分类

#### 创建型模式

| 名称                                                         | 描述                                                         | 在*设计模式中* | 在*代码完成*[[13\]](https://en.wikipedia.org/wiki/Software_design_pattern#cite_note-McConnell2004-13) | 其他                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ | -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [抽象工厂](https://en.wikipedia.org/wiki/Abstract_factory_pattern) | 提供用于创建相关或从属对象*族*的接口，而无需指定其具体类。   | 是             | 是                                                           | N / A                                                        |
| [建造者](https://en.wikipedia.org/wiki/Builder_pattern)      | 将复杂对象的构造与其表示分开，允许相同的构造过程创建各种表示。 | 是             | 没有                                                         | N / A                                                        |
| [依赖注入](https://en.wikipedia.org/wiki/Dependency_injection) | 类从注入器接受它需要的对象，而不是直接创建对象。             | 没有           | 没有                                                         | N / A                                                        |
| [工厂方法](https://en.wikipedia.org/wiki/Factory_method_pattern) | 定义用于创建*单个*对象的接口，但让子类决定实例化哪个类。Factory Method允许类将实例化延迟到子类。 | 是             | 是                                                           | N / A                                                        |
| [延迟初始化](https://en.wikipedia.org/wiki/Lazy_initialization) | 延迟创建对象，计算值或其他一些昂贵的过程直到第一次需要的策略。此模式在GoF目录中显示为“虚拟代理”，即[代理](https://en.wikipedia.org/wiki/Proxy_pattern)模式的实现策略。 | 是             | 没有                                                         | PoEAA [[14\]](https://en.wikipedia.org/wiki/Software_design_pattern#cite_note-PoEAA-14) |
| [多例](https://en.wikipedia.org/wiki/Multiton_pattern)       | 确保一个类只有命名实例，并提供对它们的全局访问点。           | 没有           | 没有                                                         | N / A                                                        |
| [对象池](https://en.wikipedia.org/wiki/Object_pool_pattern)  | 通过回收不再使用的对象，避免昂贵的资源获取和释放。可以认为是[连接池](https://en.wikipedia.org/wiki/Connection_pool)和[线程池](https://en.wikipedia.org/wiki/Thread_pool)模式的一般化。 | 没有           | 没有                                                         | N / A                                                        |
| [原型](https://en.wikipedia.org/wiki/Prototype_pattern)      | 使用原型实例指定要创建的对象类型，并从现有对象的“骨架”创建新对象，从而提高性能并将内存占用量降至最低。 | 是             | 没有                                                         | N / A                                                        |
| [资源获取是初始化](https://en.wikipedia.org/wiki/Resource_Acquisition_Is_Initialization)（RAII） | 确保通过将资源绑定到合适对象的生命周期来正确释放资源。       | 没有           | 没有                                                         | N / A                                                        |
| [单例模式](https://en.wikipedia.org/wiki/Singleton_pattern)  | 确保一个类只有一个实例，并提供一个全局访问点。               | 是             | 是                                                           | N / A                                                        |

#### 结构性模式

| 名称                                                         | 描述                                                         | 在*设计模式中* | 在*代码完成*[[13\]](https://en.wikipedia.org/wiki/Software_design_pattern#cite_note-McConnell2004-13) | 其他                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ | -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [适配器](https://en.wikipedia.org/wiki/Adapter_pattern)，包装器或转换器 | 将类的接口转换为客户期望的另一个接口。适配器允许类一起工作，否则由于不兼容的接口。企业集成模式等同于翻译。 | 是             | 是                                                           | N / A                                                        |
| [桥接](https://en.wikipedia.org/wiki/Bridge_pattern)         | 将抽象与其实现分离，允许两者独立变化。                       | 是             | 是                                                           | N / A                                                        |
| [组合](https://en.wikipedia.org/wiki/Composite_pattern)      | 将对象组合成树结构以表示部分整体层次结构。Composite允许客户端统一处理单个对象和对象组合。 | 是             | 是                                                           | N / A                                                        |
| [装饰](https://en.wikipedia.org/wiki/Decorator_pattern)      | 将附加职责附加到动态保持相同接口的对象。装饰器为子类化提供了灵活的替代扩展功能。 | 是             | 是                                                           | N / A                                                        |
| 扩展对象                                                     | 向层次结构添加功能而不更改层次结构。                         | 没有           | 没有                                                         | 敏捷软件开发，原则，模式和实践[[15\]](https://en.wikipedia.org/wiki/Software_design_pattern#cite_note-Agile_Software_Development-15) |
| [门面模式](https://en.wikipedia.org/wiki/Facade_pattern)     | 为子系统中的一组接口提供统一接口。Facade定义了一个更高级别的接口，使子系统更易于使用。 | 是             | 是                                                           | N / A                                                        |
| [享元模式](https://en.wikipedia.org/wiki/Flyweight_pattern)  | 使用共享可以有效地支持大量类似对象。                         | 是             | 没有                                                         | N / A                                                        |
| [前控制器](https://en.wikipedia.org/wiki/Front_controller)   | 该模式与Web应用程序的设计有关。它为处理请求提供了一个集中的入口点。 | 没有           | 没有                                                         | J2EE模式[[16\]](https://en.wikipedia.org/wiki/Software_design_pattern#cite_note-J2EE_Patterns-16) PoEAA [[17\]](https://en.wikipedia.org/wiki/Software_design_pattern#cite_note-PoEAA2-17) |
| [标记](https://en.wikipedia.org/wiki/Marker_interface_pattern) | 用于将元数据与类关联的空接口。                               | 没有           | 没有                                                         | [有效的Java ](https://en.wikipedia.org/wiki/Joshua_Bloch)[[18\]](https://en.wikipedia.org/wiki/Software_design_pattern#cite_note-EffectiveJava-18) |
| [模](https://en.wikipedia.org/wiki/Module_pattern)           | 将几个相关元素（例如，全局使用的类，单例，方法）分组到单个概念实体中。 | 没有           | 没有                                                         | N / A                                                        |
| [代理](https://en.wikipedia.org/wiki/Proxy_pattern)          | 为另一个对象提供代理或占位符以控制对它的访问。               | 是             | 没有                                                         | N / A                                                        |
| [双亲](https://en.wikipedia.org/wiki/Twin_pattern) [[19\]](https://en.wikipedia.org/wiki/Software_design_pattern#cite_note-19) | Twin允许在不支持此功能的编程语言中对多重继承进行建模。       | 没有           | 没有                                                         | N / A                                                        |

#### 行为模式

| 名称                                                         | 描述                                                         | 在*设计模式中* | 在*代码完成*[[13\]](https://en.wikipedia.org/wiki/Software_design_pattern#cite_note-McConnell2004-13) | 其他  |
| ------------------------------------------------------------ | ------------------------------------------------------------ | -------------- | ------------------------------------------------------------ | ----- |
| [黑板](https://en.wikipedia.org/wiki/Blackboard_(design_pattern)) | 用于组合不同数据源的[人工智能](https://en.wikipedia.org/wiki/Artificial_intelligence)模式（参见[黑板系统](https://en.wikipedia.org/wiki/Blackboard_system)） | 没有           | 没有                                                         | N / A |
| [责任链](https://en.wikipedia.org/wiki/Chain_of_responsibility_pattern) | 通过为多个对象提供处理请求的机会，避免将请求的发送者耦合到其接收者。链接接收对象并沿链传递请求，直到对象处理它。 | 是             | 没有                                                         | N / A |
| [命令](https://en.wikipedia.org/wiki/Command_pattern)        | 将请求封装为对象，从而允许对具有不同请求的客户端进行参数化，以及对请求进行排队或记录。它还允许支持可撤销操作。 | 是             | 没有                                                         | N / A |
| [**解释器模式**](https://en.wikipedia.org/wiki/Interpreter_pattern) | 给定一种语言，定义其语法的表示以及使用该表示来解释该语言中的句子的解释器。 | 是             | 没有                                                         | N / A |
| [迭代器](https://en.wikipedia.org/wiki/Iterator_pattern)     | 提供一种顺序访问[聚合](https://en.wikipedia.org/wiki/Aggregate_pattern)对象元素的方法，而不会暴露其基础表示。 | 是             | 是                                                           | N / A |
| [中间人](https://en.wikipedia.org/wiki/Mediator_pattern)     | 定义一个封装一组对象如何交互的对象。介体通过使对象明确地相互引用来促进[松散耦合](https://en.wikipedia.org/wiki/Loose_coupling)，并且它允许它们的交互独立地变化。 | 是             | 没有                                                         | N / A |
| [**备忘录模式**](https://en.wikipedia.org/wiki/Memento_pattern) | 在不违反封装的情况下，捕获并外部化对象的内部状态，以便稍后将对象恢复到此状态。 | 是             | 没有                                                         | N / A |
| [空对象](https://en.wikipedia.org/wiki/Null_Object_pattern)  | 通过提供默认对象来避免空引用。                               | 没有           | 没有                                                         | N / A |
| [观察者](https://en.wikipedia.org/wiki/Observer_pattern)或[发布/订阅](https://en.wikipedia.org/wiki/Publish/subscribe) | 定义对象之间的一对多依赖关系，其中一个对象中的状态更改导致其所有依赖项自动得到通知和更新。 | 是             | 是                                                           | N / A |
| [仆人](https://en.wikipedia.org/wiki/Design_pattern_Servant) | 为一组类定义通用功能。                                       | 没有           | 没有                                                         | N / A |
| [规范](https://en.wikipedia.org/wiki/Specification_pattern)  | 可重组[业务逻辑](https://en.wikipedia.org/wiki/Business_logic)的[布尔](https://en.wikipedia.org/wiki/Boolean_algebra)时尚。 | 没有           | 没有                                                         | N / A |
| [状态](https://en.wikipedia.org/wiki/State_pattern)          | 允许对象在其内部状态更改时更改其行为。该对象似乎会更改其类。 | 是             | 没有                                                         | N / A |
| [策略](https://en.wikipedia.org/wiki/Strategy_pattern)       | 定义一系列算法，封装每个算法，并使它们可互换。策略允许算法独立于使用它的客户端。 | 是             | 是                                                           | N / A |
| [模板方法](https://en.wikipedia.org/wiki/Template_method_pattern) | 在操作中定义算法的骨架，将一些步骤推迟到子类。模板方法允许子类重新定义算法的某些步骤而不改变算法的结构。 | 是             | 是                                                           | N / A |
| [访问者](https://en.wikipedia.org/wiki/Visitor_pattern)      | 表示要对对象结构的元素执行的操作。访问者可以定义新操作，而无需更改其操作元素的类。 | 是             | 没有                                                         | N / A |

#### [并发模式][(https://en.wikipedia.org/wiki/Concurrency_pattern)]

| 名称                                                         | 描述                                                         | 在*POSA2* [[20\]](https://en.wikipedia.org/wiki/Software_design_pattern#cite_note-POSA2-20) | 其他                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [活动对象](https://en.wikipedia.org/wiki/Active_object)      | 将方法执行与驻留在其自己的控制线程中的方法调用分离。目标是通过使用[异步方法调用](https://en.wikipedia.org/wiki/Asynchronous_method_invocation)和用于处理请求的[调度程序](https://en.wikipedia.org/wiki/Scheduling_(computing))来引入并发性。 | 是                                                           | N / A                                                        |
| [止步](https://en.wikipedia.org/wiki/Balking_pattern)        | 仅当对象处于特定状态时才对对象执行操作。                     | 没有                                                         | N / A                                                        |
| [绑定属性](https://en.wikipedia.org/wiki/Binding_properties_pattern) | 组合多个观察者以强制不同对象中的属性以某种方式同步或协调。[[21\]](https://en.wikipedia.org/wiki/Software_design_pattern#cite_note-21) | 没有                                                         | N / A                                                        |
| [计算内核](https://en.wikipedia.org/wiki/Compute_kernel)     | 相同的计算并行多次，不同于非分支指针数学用于共享数组的整数参数，例如[GPU](https://en.wikipedia.org/wiki/GPU)优化的[矩阵乘法](https://en.wikipedia.org/wiki/Matrix_multiplication)或[卷积神经网络](https://en.wikipedia.org/wiki/Convolutional_neural_network)。 | 没有                                                         | N / A                                                        |
| [双重检查锁定](https://en.wikipedia.org/wiki/Double_checked_locking_pattern) | 通过首先以不安全的方式测试锁定标准（“锁定提示”）来减少获取锁的开销; 只有成功的话，实际的锁定逻辑才会继续。在某些语言/硬件组合中实现时可能不安全。因此，它有时可以被认为是[反模式](https://en.wikipedia.org/wiki/Anti-pattern)。 | 是                                                           | N / A                                                        |
| [基于事件的异步](https://en.wikipedia.org/wiki/Event-Based_Asynchronous_Pattern) | 解决了多线程程序中出现的异步模式的问题。[[22\]](https://en.wikipedia.org/wiki/Software_design_pattern#cite_note-PC#2008-22) | 没有                                                         | N / A                                                        |
| [守卫停赛](https://en.wikipedia.org/wiki/Guarded_suspension) | 管理需要获取锁定的操作以及在执行操作之前满足的前提条件。     | 没有                                                         | N / A                                                        |
| [加入](https://en.wikipedia.org/wiki/Join-pattern)           | Join-pattern提供了一种通过消息传递编写并发，并行和分布式程序的方法。与线程和锁的使用相比，这是一种高级编程模型。 | 没有                                                         | N / A                                                        |
| [锁](https://en.wikipedia.org/wiki/Lock_(computer_science))  | 一个线程在资源上放置“锁定”，阻止其他线程访问或修改它。[[23\]](https://en.wikipedia.org/wiki/Software_design_pattern#cite_note-23) | 没有                                                         | PoEAA[[14\]](https://en.wikipedia.org/wiki/Software_design_pattern#cite_note-PoEAA-14) |
| [消息传递设计模式（MDP）](https://en.wikipedia.org/wiki/Messaging_pattern) | 允许在组件和应用程序之间交换信息（即消息）。                 | 没有                                                         | N / A                                                        |
| [监控对象](https://en.wikipedia.org/wiki/Monitor_(synchronization)) | 一种对象，其方法可以[互斥](https://en.wikipedia.org/wiki/Mutual_exclusion)，从而防止多个对象同时错误地尝试使用它。 | 是                                                           | N / A                                                        |
| [反应堆](https://en.wikipedia.org/wiki/Reactor_pattern)      | reactor对象为必须同步处理的资源提供异步接口。                | 是                                                           | N / A                                                        |
| [读写锁定](https://en.wikipedia.org/wiki/Read/write_lock_pattern) | 允许对对象进行并发读访问，但需要对写操作进行独占访问。       | 没有                                                         | N / A                                                        |
| [调度](https://en.wikipedia.org/wiki/Scheduler_pattern)      | 明确控制线程何时可以执行单线程代码。                         | 没有                                                         | N / A                                                        |
| [线程池](https://en.wikipedia.org/wiki/Thread_pool_pattern)  | 创建了许多线程来执行许多任务，这些任务通常组织在队列中。通常，除了线程之外还有许多任务。可以认为是[对象池](https://en.wikipedia.org/wiki/Object_pool)模式的特例。 | 没有                                                         | N / A                                                        |
| [特定于线程的存储](https://en.wikipedia.org/wiki/Thread-Specific_Storage) | 线程本地的静态或“全局”内存。                                 | 是                                                           | N / A                                                        |

### 逻辑分类

#### 表现层

* 拦截过滤器
* 前端控制器
* Context对象
* 应用控制器
* 视图助手
* 复合视图
* 服务到工作者
* 分配器视图

#### 业务层

* 业务代表
* 服务定位器
* 会话门面
* 应用服务
* 业务对象
* 符合实体
* 传输对象
* 传输对象组装器
* 值列表处理器

#### 集成层

* 数据访问对象
* 服务激活器
* 业务领域存储
* Web Service中转

# 表现层设计考虑和不佳实践

## 表现层设计考虑





