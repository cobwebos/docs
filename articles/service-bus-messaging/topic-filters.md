---
title: Azure 服务总线主题筛选器 | Microsoft Docs
description: 本文介绍订阅者如何通过指定筛选器来定义希望从主题接收的消息。
services: service-bus-messaging
documentationcenter: ''
author: spelluru
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: spelluru
ms.openlocfilehash: 6e780268d4b8c1a512ce82b1ca10a2f6b7b894b7
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125751"
---
# <a name="topic-filters-and-actions"></a>主题筛选器和操作

订阅者可以定义他们希望从主题接收的消息。 这些消息采用一个或多个命名订阅规则的形式指定。 每个规则都包含一个选择特定的消息的条件和一个对所选消息进行批注的操作。 对于每个匹配规则条件，订阅会生成消息的副本，这对于每个匹配规则可能会以不同方式进行批注。

每个新创建的主题订阅都具有初始默认订阅规则。 如果未显式指定规则的筛选条件，则应用的筛选器是 **true** 筛选器，通过它可以将所有消息都选择到订阅中。 默认规则没有关联批注操作。

服务总线支持三个筛选条件：

-   *布尔筛选器* - 通过 **TrueFilter** 和 **FalseFilter** 可以为订阅选择所有到达消息 (**true**) 或不选择任何到达消息 (**false**)。

-   *SQL 筛选器* - **SqlFilter** 包含类似 SQL 的条件表达式，它会在代理中针对到达的消息的用户定义属性和系统属性进行计算。 所有系统属性在条件表达式中必须带有前缀 `sys.`。 [筛选条件的 SQL 语言子集](service-bus-messaging-sql-filter.md)测试属性（ `EXISTS` ）、null 值（ `IS NULL` ）、逻辑非/和/或关系运算符、简单数字算法以及与的简单文本模式匹配是否存在 `LIKE` 。

-   *相关筛选器* - **CorrelationFilter** 包含一组条件，这些条件按照到达消息的一个或多个用户和系统属性进行匹配。 常见的用途是与**CorrelationId**属性匹配，但应用程序也可以选择与以下属性匹配：

    - **ContentType**
     - **标识**
     - **MessageId**
     - **ReplyTo**
     - **ReplyToSessionId**
     - **SessionId** 
     - **自**
     - 任何用户定义的属性。 
     
     当到达消息的某个属性值等于相关筛选器中指定的值时，便存在匹配。 对于字符串表达式，比较会区分大小写。 指定多个匹配属性时，筛选器会将它们合并为逻辑“与”条件，这意味着若要使筛选器匹配，所有条件都必须匹配。

所有筛选器都会计算消息属性。 筛选器无法计算消息正文。

复杂筛选器规则需要处理能力。 具体而言，使用 SQL 筛选规则会导致订阅、主题和命名空间级别的总体消息吞吐量降低。 只要有可能，应用程序就会选择依赖于 SQL 的筛选器上的关联筛选器，因为它们的处理效率要高得多，而且对吞吐量的影响更小。

## <a name="actions"></a>操作

使用 SQL 筛选条件，可以定义可通过添加、删除或替换属性及其值对消息进行批注的操作。 操作[使用类似 SQL 的表达式](service-bus-messaging-sql-filter.md)，该表达式有一点依赖于 SQL UPDATE 语句语法。 对消息进行匹配后，以及在将消息选入订阅之前，会对该消息执行操作。 对消息属性进行的更改是复制到订阅中的消息所专有的。

## <a name="usage-patterns"></a>使用模式

主题的最简单使用方案是每个订阅都获取发送到主题的每个消息的副本，这样可实现广播模式。

筛选器和操作可实现更进一步的两组模式：分区和路由。

分区使用筛选器以可预测且互相排斥的方式在多个现有主题订阅间分发消息。 当系统进行扩大以在功能相同的隔离舱（每个隔离舱包含总体数据的子集；例如客户配置文件信息）中处理许多不同上下文时，可使用分区模式。 借助分区，发布者可将消息提交到主题中，而无需了解分区模型。 消息随后会移动到正确的订阅，分区的消息处理程序随后会从该订阅检索它。

路由使用筛选器以可预测、但不一定独占的方式在主题订阅间分发消息。 通过与[自动转发](service-bus-auto-forwarding.md)功能相结合，主题筛选器可以用于在服务总线命名空间中创建复杂路由图，以便在 Azure 区域中进行消息分发。 通过使 Azure Functions 或 Azure 逻辑应用充当 Azure 服务总线命名空间之间的桥梁，可以创建直接集成到业务线应用程序中的复杂全局拓扑。


> [!NOTE]
> 目前 Azure 门户不允许为订阅指定筛选规则。 可使用任何受支持的 Sdk 或 Azure 资源管理器模板来定义订阅规则。 

## <a name="next-steps"></a>后续步骤
请参阅以下示例： 

- [.NET-带有筛选器的基本发送和接收教程](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET-主题筛选器](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Azure Resource Manager 模板](https://docs.microsoft.com/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)


