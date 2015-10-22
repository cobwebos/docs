<properties
   pageTitle="Service Fabric 群集安排放置约束"
   description="Service Fabric 中的放置约束的概念性概述"
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.date="09/03/2015"
   wacn.date=""/>

# 放置约束概述

利用 Service Fabric，开发人员能够将服务副本的位置限制到满足特定条件的节点。这些条件是通过使用适当的服务上下文特定值计算的布尔表达式来表示。


## 功能
通过使用放置约束，你可以：

- 通过定义节点上的 NodeProperties，在不同类型的节点上限制不同类型的服务。

- 将某些约束应用于主副本，但不应用于辅助副本


## 关键概念
NodeProperty - 用户或系统定义的从字符串到值的映射，会随每个节点的变化而改变，即 NodeName。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 后续步骤

有关详细信息：[应用程序方案](/documentation/articles/service-fabric-application-scenarios)。
 

<!---HONumber=74-->