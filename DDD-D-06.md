## 第6章。使用领域事件的战术设计 

![](https://lh4.googleusercontent.com/hsrS2tKUSjLESl6VEhZsPKiM53qtbmNcS6ea1mo3JF_wgrN4RosaQhsUAckPtYfsOhYZJ9LKU3wWgtVwNkeR7hB5mJ-WqKIVvvi44gYkpLpCLLtB_-NyhZaadMUxZIP9j4RYoH8)
在前面的章节中，你已经看到了一些关于领域事件的使用方法。领域事件是在一个有边界的上下文中发生的一些具有业务意义的事件的记录。现在你已经知道，领域事件是战略设计的一个非常重要的工具。然而，在战术设计中，经常会出现领域事件被概念化并成为你的核心领域的一部分。为了看到使用领域事件所带来的全部力量，考虑因果一致性的概念。如果一个业务域提供了因果一致性，它的因果相关的操作（一个操作导致另一个操作）被分布式系统的每个依赖节点以相同的顺序看到 \[Causal\] 。这意味着因果相关的操作必须以特定的顺序发生，因此，除非另一件事在它之前发生，否则一件事不能发生。也许这意味着，一个聚合体不能被创建或修改，直到清楚地知道另一个聚合体发生了特定的操作。 

1. 苏发了一条消息说："我的钱包丢了！" 
2. 加里回答说："这太可怕了！" 
3. 苏发了一条消息说："别担心，我找到了我的钱包！" 
4. 加里回答说："那很好！" 

如果这些信息在分布式节点上被复制，但不按因果顺序，就会出现加里对 "我的钱包丢了！"的信息说："这太好了！"消息 "太棒了！"与 "我丢了钱包！"没有直接或因果关系，这绝对不是加里想要的，苏或其他任何人都可以阅读。因此，如果没有以适当的方式实现因果关系，整个领域就会是错误的，或者至少是误导的。这种因果关系的、线性化的系统架构可以通过创建和发布正确排序的领域事件来轻易实现。 

从战术设计的努力来看，领域事件在你的领域模型中成为现实，因此可以在你自己的Bounded Context和其他人中发布和使用。这是一种非常强大的方式来通知感兴趣的听众已经发生的重要事件。现在你将学习如何对领域事件进行建模并在你的Bounded Context中使用它们。 

### 设计、实施和使用领域事件 

下面将指导你完成在你的Bounded Context中有效设计和实现Domain Events所需的步骤。在这之后，你还会看到如何使用领域事件的例子。 

![](https://lh5.googleusercontent.com/X7RUizf9SpHYz8BziHhwXvpdKYUUfw41xNF7lLzblz6HfqcdHbSguXJwM1O3F7A4G8EkduASrv5xWz-jQlJTFOcLNccqYr4iIvV4w2Jcv0MYIn5V4e99fUgTxHO26mS7rQOOJZ0)

这段C#代码可能被认为是每个领域事件应该支持的最小接口。你通常想传达你的领域事件发生的日期和时间，所以这由OccurredOn属性提供。这个细节不是绝对必要的，但它通常是有用的。所以你的领域事件类型可能会实现这个接口。 

你必须注意如何命名你的领域事件类型。你使用的词应该反映你的模型的泛在语言。这些词将在你的模型中的事件和外部世界之间形成一座桥梁。你必须很好地沟通你的事件，这一点至关重要。

![](https://lh4.googleusercontent.com/SS1ruKs72a5Kjtb3tpqAQrFsz6wv_1jOCO8upbgEDso4OX5SK3ak7Vy-7NaxrbW8dJgB1Uq3U3Zb8GGvdANiNvbFLhs4dvy2JvScpztHqTVLNDgAqQBxx9kK5W_vGp-CebrXuaI)

你的领域事件类型名称应该是对过去发生的陈述，也就是过去式的动词。这里有一些来自敏捷项目管理背景的例子：例如，ProductCreated，说明一个Scrum产品在过去某个时间被创建。其他领域事件有 `ReleaseScheduled`、`SprintScheduled`、`BacklogItemPlanned` 和`BacklogItemCommitted`。每一个名字都清楚而简洁地说明了在你的核心域中发生了什么。 

![](https://lh3.googleusercontent.com/DTdeB7YB1d-bcofrvXcHrcudLQeuXsgFLHc5mg_4neTwJAgNpFmUUr1FhNhTx54_Jgk_COg_KgbYMe7NL9LGpm93Su59bn3FVjyu-J5O3EfCKL23zMfy8ZrdrXWV0UDT6jYSm-M)

它是领域事件的名称和它的属性的组合，完全传达了领域模型中发生的事情的记录。但是，领域事件应该拥有哪些属性呢？ 

![](https://lh4.googleusercontent.com/YTMB_WCC9ipWB292bTK_vJpn9Kh9wH2_xY1GuKtyBOBnDjG94AS_9-rF8ebdKABBkvBCgtDm75nRprCLMJ6HS4RhMpk90KZ8VSl5cZldInE6CcEKHl9iurKlGAUWQuWAeafjt9g)

问问你自己，"导致领域事件被发布的应用刺激是什么？"在`ProductCreated`的例子中，有一个命令导致了它（命令只是一个方法/动作请求的对象形式）。该命令被命名为CreateProduct。所以你可以说，ProductCreated是CreateProduct命令的结果。 

![](https://lh4.googleusercontent.com/v9zLBdTBOYIAum5KIJv3GjjWuBtf80V9V_bTi-GUPApb16_KONW8o7zNdQLPznl32B0g8wg3A1z5HiLH3U5D_1TW9606GE6HrEbw-rvmzxQuvSuhIT1nij6D1XfOzMF5hiEsbpI)

CreateProduct命令有许多属性。
(1) 识别订阅租户的tenantId，
(2) 识别正在创建的唯一产品的productId，
(3) 产品名称，和
(4) 产品描述。

这些属性中的每一个都是对创造一个产品来说是必不可少的。 

![](https://lh3.googleusercontent.com/XNSchTsHPa-ua-tjDHh0MtuV1RFLA2eHOtiu_Hl2UTVESONpGRRr9xWnB8o1Mc8rgimBSLOymSGMlatbGQavA53e-Sc-ZE-kXjfBkHEf9hbCCqOyXzdr7X95i_7DGtytTk_Ohuo)

因此，ProductCreated域事件应该持有所有的属性，这些属性是由导致它被创建的命令提供的。
(1) tenantId , 
(2) productId , 
(3) name , 和 
(4) description 。

这将完全准确地告知所有订阅者在模型中发生了什么；也就是说，一个产品被创建了，它是为用tenantId标识的租户创建的，产品用productId唯一标识，并且产品有分配给它的名称和描述。 

![](https://lh5.googleusercontent.com/0hE6yjxymKMWkgHztIs6EC_KhF-TRmEXMm_04y7PnM0uJhkOqnj6o-ioUOsu4idusv24_UOOr_JLHboZq_uXp-n4YVvapuw9zdr0SQK_6R5MjQXEwjdqXRHbuZQjmA6M-I37PPQ)

这五个例子让你对敏捷项目管理上下文发布的各种领域事件所应包含的属性有了一个很好的了解。例如，当一个`BacklogItem` 被提交给一个Sprint时，`BacklogItemCommitted`领域事件被实例化并发布。这个领域事件包含租户ID、被提交的BacklogItem的BacklogItemId和被提交的Sprint的SprintId。 

正如第4章 "使用上下文映射的战略设计 "中所描述的那样，有时一个领域事件可以用额外的数据来充实。这对那些不想回过头来查询你的约束性上下文以获得他们所需的额外数据的消费者来说特别有帮助。即便如此，你也必须小心，不要让一个领域事件充满了太多的数据，以至于失去了它的意义。例如，考虑BacklogItemCommitted的问题，它持有BacklogItem的整个状态。根据这个领域事件，实际发生了什么？所有额外的数据可能使它变得不清楚，除非你要求消费者对你的

BacklogItem元素。另外，考虑使用BacklogItemUpdated来显示BacklogItem的完整状态，而不是提供BacklogItemCommitted。BacklogItem发生了什么是非常不清楚的，因为消费者必须将最新的BacklogItemUpdated与之前的BacklogItemUpdated进行比较，以了解BacklogItem实际发生了什么。 

![](https://lh5.googleusercontent.com/XycuhzmlpbaXo6Wi14RYpJRGAzRBPU8mUyw90eVutwyam8U92jgXmWJ5lvIliTSruD2y9YXR8puhYGYb0Ku3dVhol420adn9ioUHOoriwroiAZU586x9DKz7qPfTAoTLMHruJqk)

为了使领域事件的正确使用更加清晰，让我们走过一个场景。产品所有者将一个 BacklogItem 提交给一个 Sprint 。该命令本身导致BacklogItem和Sprint被加载。然后该命令在BacklogItem Aggregate上执行。这导致 BacklogItem 的状态被修改，然后 `BacklogItemCommitted` 领域事件作为一个结果被发布。 

![](https://lh4.googleusercontent.com/oTWNnUFiG-7LVoUaScXmAB-gigrD_vG9vBVDnmXooBHMTks4d2JGXXuodWYXkAGdvUfRXfW1MRy_2CGGTjDKdAg_9VCk3twzhSFkQgLTgOv6NH9pnk23csOBJ73JESVyMZyKSU8)

重要的是，修改后的聚合体和域事件要在同一个事务中一起保存。如果你使用的是对象关系映射工具，你会把聚合（Aggregate）保存到一个表中，把域事件保存到一个事件存储表中，然后提交事务。如果你使用的是 Event Sourcing，那么Aggregate的状态就完全由Domain Events本身来表示。

在本章的下一节将讨论事件源。无论哪种方式，将领域事件持久化在事件存储中都会保留其相对于整个领域模型所发生的事情的因果顺序。

![](https://lh3.googleusercontent.com/YwquNzGamp0TL1qwU5jFGFkbFHCurPW9nCONNhBPTry78Dr3jCYZWxnmuGKJ08WzhKZN0CUK72aR2E9MqSf41yAtyT0OFyNaaEiQdKIfsw84VT50QXx_rLkOb84RLgpCwfrL_uw)

一旦你的领域事件被保存到事件商店，它就可以被发布给任何感兴趣的人。这可能是在你自己的Bounded Context内，也可能是向外部的Bounded Context。这是你的方式告诉世界在你的核心域中发生了值得注意的事情。请注意，仅仅按照因果顺序保存领域事件并不能保证它将以同样的顺序到达其他分布式节点。因此，识别正确的因果关系也是消费的边界上下文的责任。可能是领域事件类型本身可以表明因果关系，也可能是与领域事件相关的元数据，如序列或因果标识符。序列或因果标识符将表明是什么导致了这个领域事件，如果还没有看到原因，消费者必须等待应用新到达的事件，直到它的原因到达。在某些情况下，有可能忽略那些已经被与后来的事件相关的行动所取代的潜在领域事件；在这种情况下，因果关系具有可被忽略的影响。 

![](https://lh5.googleusercontent.com/1qvynE3y7cqSbrHUgFMtw5zgB-LiASK-PGJzrO_tCo6V7iXpc9xKZs8IkB2nK3ahOHHqXmMIcYDFQxyXraQVruOUS9AnLKpr_AAxgYJ-9tdCeQzrIFIGL0cgw5zxUfEKt_53PNQ)

还有一点值得注意，那就是什么会导致域事件。尽管通常是由用户界面发出的基于用户的命令导致事件的发生，但有时域事件可以由一个不同的来源引起。这可能是来自一个过期的计时器，例如在工作日结束时或在一周、一月或一年结束时。在这样的情况下，不会是一个命令导致的事件，因为某个时间段的结束是一个事实。你不能拒绝某个时间段已经过期的事实，如果企业关心这个事实，那么时间过期被建模为域事件，而不是命令。 

更重要的是，这样一个即将到期的时间段通常会有一个描述性的名字，它将成为泛在语言的一部分。例如，"财政年度结束 "可能是你的企业需要反应的一个重要事件。此外，华尔街下午4点（16:00）被称为 "市场关闭"，而不仅仅是下午4点。因此，你有一个基于时间的特定领域事件的名称。

命令与领域事件不同的是，在某些情况下，命令可以被拒绝，因为它不合适，例如由于某些资源（产品、资金等）的供应和可用性，或另一种业务层面的验证。所以，一个命令可以被拒绝，但一个领域事件是一个历史问题，在逻辑上不能被拒绝。即便如此，为了响应一个基于时间的领域事件，可能是应用程序需要生成一个或多个命令，以要求应用程序执行一些动作集。 

![](https://lh5.googleusercontent.com/77tohgIWSXZb0YryPf2a00aiZJ84B9N3iEx1yn1LayMP1iAIp3ISub4Vb4xYUG4-21CFawVplqCTPrz-Oeg9VvddVgyt_NQOh-PRJ8QCcWIJjxpwr2CRN6F8irwG1b_KjC76J5Y)

### 活动采购 

事件源可以被描述为持久化所有发生在一个聚合体实例上的领域事件，作为该聚合体实例的变化记录。你不是把聚合体的状态作为一个整体来保存，而是把发生在它身上的所有单独的领域事件来保存。让我们来看看这是如何支持的。 

一个聚合体实例发生的所有领域事件，按照它们最初发生的顺序，组成了它的事件流。事件流从该聚合体实例发生的第一个领域事件开始，一直持续到最后一个领域事件发生。当一个给定的聚合体实例发生新的领域事件时，它们会被附加到其事件流的末尾。将事件流重新应用到聚合体上，可以将其状态从持久化中重新组合到内存中。换句话说，当使用事件源时，一个因任何原因从内存中移除的聚合体将完全由其事件流重建。在上图中，第一个发生的领域事件是BacklogItemPlanned；第
接下来是BacklogItemStoryDefined；而刚刚发生的事件是BacklogItemCommitted。完整的事件流目前由这三个事件组成，它们遵循图中描述和看到的顺序。 

就像前面描述的那样，每个发生在特定聚合体实例上的域事件都是由一个命令引起的。在上图中，刚刚处理的是CommitBacklogItemToSprint命令，这导致了BacklogItemCommitted领域事件的发生。 

![](https://lh5.googleusercontent.com/G25g9n8c46HMOvqKCIB5cElYPs5DxZE20Tu1EDNxdYrCzteOGOKpBvAcLi-lxzaY8FZlPVZozqgOyuwTXc3tBImyoadEyCdrnqQ1kEUM4C-WSD3-IJjcjHnong83LdI7LOgwC84)

事件存储只是一个连续的存储集合或表，所有的域事件都被附加在上面。因为事件存储是只附加的，它使存储机制变得非常快，所以你可以计划使用事件源的核心域具有非常高的吞吐量、低延迟，并且能够高度扩展。 

### 绩效意识 

如果你最关心的问题之一是性能，你会很想知道缓存和快照的情况。首先，性能最高的聚合体是那些缓存在内存中的聚合体，每次使用它们时都不需要从存储中重新组建。使用Actor模型，将actors作为Aggregates\[Reactive\]是保持Aggregates状态缓存的最简单方法之一。 

另一个供你使用的工具是快照，你的聚合体被从内存中驱逐的加载时间可以被优化重组，而不需要从事件流中重新加载每个领域事件。这相当于在数据库中维护你的聚合体（对象、角色或记录）的一些增量状态的快照。快照在《实施领域驱动设计》\[IDDD\]和《使用角色模型的反应式消息传递模式》\[Reactive\] 中有更详细的讨论。 

使用事件源的最大优势之一是，它可以保存在你的核心域中曾经发生的所有事件的记录，在个人发生层面上。这对你的业务有很大帮助，原因有很多，你今天可以想象，比如合规性和分析。 

和那些你以后才会意识到的。也有技术上的优势。例如，软件开发者可以使用事件流来检查使用趋势并调试他们的源代码。你可以在《实施领域驱动设计》\[IDD\]中找到关于事件源技术的报道。另外，当你使用事件源时，你几乎肯定有义务使用CQRS。你也可以在 Implementing Domain-Driven Design \[IDDD\] 中找到关于这个主题的讨论。 

### 摘要

在本章中，你学到了。 

- 如何创建和命名你的域名事件 
- 定义和实现一个标准的领域事件接口的重要性 - 命名好你的领域事件是特别重要的 
- 如何定义你的领域事件的属性 
- 一些领域事件可能是由命令引起的，而另一些可能是由于检测到其他一些变化的状态而发生的，如日期或时间。 
- 如何将你的域名事件保存到事件库中 
- 如何在保存后发布你的域名事件 
- 关于事件源以及如何存储和使用你的领域事件来表示你的聚合体的状态 

关于领域事件和集成的彻底处理，请参见《实施领域驱动设计》\[IDDD\]第8章和第13章。