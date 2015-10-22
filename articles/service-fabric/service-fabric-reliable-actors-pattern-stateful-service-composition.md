
<properties
   pageTitle="Reliable Actors 有状态服务组合设计模式"
   description="使用有状态参与者维护服务调用之间的状态以及缓存以前的服务结果的 Service Fabric Reliable Actors 设计模式。状态可以是持久或暂时的。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="08/05/2015"
   wacn.date=""/>

# Reliable Actors 设计模式：有状态服务组合
开发人员将过去的十年半时间都用在了构建企业中 N 层无状态服务上。他们在数据库上构建服务，在其他服务上构建高阶服务，并构建业务流程引擎和面向消息的中间件来协调这些服务。随着用户工作负荷的发展（无论是否需要更多交互性或缩放），无状态的面向服务的体系结构都开始显示其弱点。

## 旧方法：SOA 服务
虽然 SOA 服务由于其无状态性质可无缝地水平缩放，但是它们在存储层中形成了瓶颈 — 并发和吞吐量。访问存储的成本变得越来越高昂。作为一种常见做法，大多数开发人员向其解决方案引入了缓存来减少对存储的需求，但是该解决方案不是没有缺点 — 要进行管理的另一层、对缓存的并发访问、语义限制和更改，以及最终一致性。如同先前在智能缓存模式中所详细介绍的那样，虚拟参与者模型为此提供了完美的解决方案。

一些开发人员尝试通过复制其存储层来解决问题。但是，此方法无法很好地缩放，会快速达到 CAP 边界。第二个挑战是围绕不断变化的要求发展起来的；最终用户和企业都需要交互服务 — 以便在数毫秒内而不是通常的数秒内响应请求。为进行响应，开发人员开始在其他服务（在某些情况下有数十个服务）上构建外观服务，以创建以用户为中心的服务。但是组合多个下游服务会很快表现出延迟问题。

开发人员再次转向缓存和内存中对象存储（某些情况下转向其他实现）以满足性能要求。他们开始构建后端辅助角色进程来定期生成缓存，以最大程度减少成本高昂的按需缓存填充。最后，它们开始解构其工作负荷以将异步操作与同步操作隔离以获得更多空间，以便交互操作可响应状态更改，这在 SOA 中特别难以进行。

他们还引入了更多层（如队列和辅助角色），使其解决方案变得更加复杂。从根本上来说，开发人员开始寻找解决方案来构建“有状态服务”，换句话说是并置“状态”和“服务行为”以实现以用户为中心的交互体验。而这正是 Azure Service Fabric 参与者作为服务组合层加入的位置（而不是替代这些服务）。

下图说明了这一点：

![][1]

## 使用参与者的更好解决方案
组合服务时，参与者可以是无状态或有状态。

* 无状态参与者可以用作基础服务的代理。这些参与者可以在 Azure Service Fabric 群集中动态缩放，并且可以缓存与服务相关的特定信息（例如在发现它之后缓存其终结点）。
* 有状态参与者可以在服务调用之间维护状态，以及缓存以前的服务结果。状态可以是持久或暂时的。

此模式还适用于许多方案；在大多数情况下，参与者都需要进行外部调用以便对特定服务调用操作。我们通过一个使用现代电子商务应用程序的示例来进行说明。这些应用程序基于提供各种功能（如用户个人资料管理、建议、购物篮管理、愿望清单管理、购买等）的服务进行构建。

大多数开发人员希望对其体系结构采用以用户为中心的方法，这非常类似于开发社交体验的那些开发人员，因为电子商务体验主要围绕用户和产品。出于性能原因，这通常通过传送缓存最有支持的服务的外观来实现。

现在我们来讨论一下基于参与者的方法。用户参与者可以表示用户的行为（浏览目录、喜欢产品、将商品添加到购物篮、向朋友推荐产品）及其组合状态 — 其个人资料、购物篮中的商品、其朋友推荐的商品列表、其购买历史记录、当前地理位置等。

## 使用有状态参与者
我们首先来看一个示例，其中用户参与者需要从多个服务填充其状态。我们不打算为此示例提供代码示例，因为我们在智能缓存模式中讨论的所有内容在此处也适用。我们可以在登录时激活用户参与者，使用来自后端服务的足够数据填充它。当然，正如我们在本文前面部分许多情况下看到的那样，整个和部分状态可以按需、基于定时器或两种方法都采用一点来进行预填充，并缓存在参与者中。对于此示例，个人资料和愿望清单如下图所示：

![][2]

例如，我们可以经常预填充用户状态并使其在用户登录时准备就绪，或是为每个月访问服务的用户在登录时填充它。我们已在智能缓存部分中看到了这些模式。

当用户 23 登录时，如果尚未激活，则用户参与者 (23) 会激活并从后端服务中提取相关用户个人资料信息和愿望清单。用户参与者可能会缓存该信息以用于后续调用。例如，如果我们需要向愿望清单添加商品，则可以按前面所讨论的那样进行后写或直写。其次，我们来看一个示例，其中用户单击“喜欢”按钮即表示喜欢某个产品。此操作可能需要多次调用多个服务，如下图所示：向目录服务发送“喜欢”、触发下一组建议并可能将更新发布到社交网络。

这如下所示：

![][3]

## 参与者组合和异步通信如何有所帮助
实际上，当我们要将请求/响应样式操作与异步操作组合在一起时，Azure Service Fabric 参与者的效果非常突出。例如，在“喜欢产品”立即将喜欢的商品放入用户愿望清单的同时，发布到社交网络以及触发下一组建议可以是使用缓冲区和计时器的异步操作。

将用户参与者与服务结合使用的另一个重要好处是，参与者可为缓存状态提供自然位置，最重要的是以异步方式来响应状态更改。对于无状态服务，这是特别具有挑战性的方案。例如，用户执行一系列操作（可能属于一个“用户旅程”）。 这些事件可以在参与者中实时捕获，我们可以汇集成一个流，可以在事件发生时或以异步方式基于计时器查询该流，以更改参与者的行为。

此时，SOA 纯粹主义者肯定已注意到，从参与者作为通过独立于语言的协议公开的终结点这层意义上来说，这些不是服务。Azure Service Fabric 参与者既不是互操作组件也不是用于服务互操作的平台。不过，当我们对参与者进行建模，或是采用相同方式对关注分离进行建模时，我们绝对会考虑 SOA 样式服务的粒度。此类服务称为“微服务”。 同样，我们也绝对会将 REST 终结点或 SOAP 终结点作为互操作层置于 Azure Service Fabric 参与者之前。

有状态服务组合也适用于工作流，而不仅仅是事务性方案（如电子商务）。Azure Service Fabric 旨在作为工作流/业务流程引擎，因此可以用于对涉及服务交互的工作流进行建模并维护这些交互的状态。

我们在构建可缩放服务以提供动态体验时看到了“无状态服务”的缺点。Azure Service Fabric 参与者实质上通过将状态和行为结合在一起，可帮助开发人员基于其现有投资来构建可缩放的交互体验。


## 后续步骤
[模式：智能缓存](/documentation/articles/service-fabric-reliable-actors-pattern-smart-cache)

[模式：分布式网络和图形](/documentation/articles/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs)

[模式：资源调控](/documentation/articles/service-fabric-reliable-actors-pattern-resource-governance)

[模式：物联网](/documentation/articles/service-fabric-reliable-actors-pattern-internet-of-things)

[模式：分布式计算](/documentation/articles/service-fabric-reliable-actors-pattern-distributed-computation)

[若干反模式](/documentation/articles/service-fabric-reliable-actors-anti-patterns)

[Service Fabric Actor 简介](/documentation/articles/service-fabric-reliable-actors-introduction)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-2.png
[3]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-3.png

<!---HONumber=74-->