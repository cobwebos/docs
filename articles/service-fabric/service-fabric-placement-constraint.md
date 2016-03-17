<properties
   pageTitle="Service Fabric 群集业务流程中的放置约束 | Microsoft Azure"
   description="Service Fabric 中的放置约束的概念性概述"
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.date="02/03/2016"
   wacn.date=""/>

# 放置约束概述

使用 Service Fabric，开发人员能够将服务副本的位置限制到满足特定条件的节点。这些条件是通过使用适当的特定于服务上下文的值计算的布尔表达式来表示。


## 功能
通过使用放置约束，你可以：

- 通过定义节点上的 NodeProperties，在不同类型的节点上限制不同类型的服务。

- 将约束同时应用于服务的主副本和辅助副本，或者仅将约束应用于主副本。


## 关键概念
NodeProperty - 用户或系统定义的从字符串到因节点（即 NodeName）而异的值的映射。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 后续步骤

有关详细信息，请参阅：[应用程序方案](/documentation/articles/service-fabric-application-scenarios)。
 

<!---HONumber=Mooncake_0307_2016-->