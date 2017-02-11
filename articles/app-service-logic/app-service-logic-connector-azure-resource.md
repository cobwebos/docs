---
title: "在逻辑应用中使用 Azure 资源连接器 | Microsoft Docs"
description: "如何创建并配置 Azure 资源连接器或 API 应用，以及在 Azure App Service 中的逻辑应用中使用它"
services: logic-apps
documentationcenter: .net,nodejs,java
author: stepsic-microsoft-com
manager: erikre
editor: 
ms.assetid: a32e5a5c-34d7-4422-b0f7-c5cf7b8abffa
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/01/2016
ms.author: stepsic
translationtype: Human Translation
ms.sourcegitcommit: 7ff7874538f23c8dd8c4aaa789018c77dfe2ab9a
ms.openlocfilehash: 7923735d9ed382ee8c554e7e0c6b1fb4a22208bb


---
# <a name="get-started-with-the-azure-resource-connector-and-add-it-to-your-logic-app"></a>Azure 资源连接器入门及将其添加到逻辑应用
> [!NOTE]
> 此文章版本适用于 2014-12-01 预览架构版的逻辑应用。
> 
> 

使用 Azure 资源连接器能够轻松地管理逻辑应用中的 Azure 资源。

## <a name="create-the-azure-resource-connector"></a>创建 Azure 资源连接器
若要使用 Azure 资源连接器 API 应用，需要首先创建它的一个实例。 可在创建逻辑应用时通过内联完成，或者通过从 Azure 应用商店选择 Azure Resource Manger 连接器 API 应用来完成。

若要对它进行配置，需要设置一个有权在 Azure 中执行任何想要执行的操作的服务主体。 代表设置的服务主体执行所有调用。 这样，可以限定连接器仅使用想要执行的操作，而不使用其他操作。

David Ebbo 已经写了一篇有关如何对此进行设置的[出色的博客](http://blog.davidebbo.com/2014/12/azure-service-principal.html)。 请遵循此博客中的所有说明，并获取**租户 ID**、**客户端 ID** 和**密码**。 这三个字段以及**订阅 ID** 是配置连接器所需要的信息。

## <a name="using-the-azure-resource-connector-in-logic-apps-designer"></a>在逻辑应用设计器中使用 Azure 资源连接器
### <a name="trigger"></a>触发器
连接器中支持两种触发器：

| Name | 说明 |
| --- | --- |
| 发生事件 |针对订阅中的资源发生事件时触发。 |
| 指标超过阈值 |当指标达到特定阈值时触发。 |

### <a name="action"></a>操作
同样，在 Azure 订阅中可提供大量的操作：

对于**资源组**，可以执行以下操作：

| Name | 说明 |
| --- | --- |
| 列出资源组 |列出订阅中所有资源组。 |
| 获取资源组 |根据 id 获取资源组。 |
| 创建资源组 |创建或更新资源组。 |
| 删除资源组 |删除一个资源组。 |

对于**资源**，可以执行以下操作：

| Name | 说明 |
| --- | --- |
| 列出资源 |按不同类型的筛选器列出订阅中的资源。 |
| 获取资源 |按资源 ID 获取单个资源。 |
| 创建或更新资源 |创建资源，或更新现有资源。 必须提供资源的所有属性。 |
| 资源操作 |对资源执行其他任何操作。 需要知道操作的名称和该操作的有效负载（如果有）。 |
| 删除资源 |删除一个资源。 |

对于**资源提供程序**，可以执行以下操作：

| Name | 说明 |
| --- | --- |
| 列出资源提供程序 |列出订阅中所有的可用资源提供程序。 |

对于**资源组部署**，可以执行以下操作：

| Name | 说明 |
| --- | --- |
| 列出部署 |列出资源组中所有的部署。 |
| 获取部署 |按 id 获取模板部署。 |
| 创建部署 |通过提供模板创建新的资源组部署。 |

对于有关资源的**事件**，可以执行以下操作：

| Name | 说明 |
| --- | --- |
| 获取事件 |获取订阅或资源的事件。 |

对于有关资源的**指标**，可以执行以下操作：

| Name | 说明 |
| --- | --- |
| 获取指标 |获取资源 ID 的指标。 |

## <a name="do-more-with-your-connector"></a>使用连接器执行更多操作
现在已经创建了连接器，可以将其添加到使用逻辑应用的业务流中。 请参阅[什么是逻辑应用？](../logic-apps/logic-apps-what-are-logic-apps.md)。

> [!NOTE]
> 若要在注册 Azure 帐户前开始使用 Azure 逻辑应用，请转至[试用逻辑应用](https://azure.microsoft.com/try/app-service/logic/)，可立即在应用服务中创建临时简易版逻辑应用。 你不需要使用信用卡，也不需要做出承诺。
> 
> 

请查看 [Connectors and API apps Reference](http://go.microsoft.com/fwlink/p/?LinkId=529766)（连接器和 API 应用参考）中的 Swagger REST API 参考。

<!--References -->

<!--Links -->
[Creating a Logic app]: app-service-logic-create-a-logic-app.md



<!--HONumber=Feb17_HO2-->


