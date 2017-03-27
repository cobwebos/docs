---
title: "逻辑应用连接器概述 | Microsoft Docs"
description: "可在逻辑应用中使用的连接器概述"
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: ca8dab2e-9b69-4b1e-865d-1facd9f0cdac
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 44a756ba437b2ce9f625017998d9ae7d09acae1f
ms.openlocfilehash: 9cbb258ae9e32549669623e6824dd9b18fa1f68f


---
# <a name="using-connectors-in-a-logic-app"></a>在逻辑应用中使用连接器
连接器提供对服务、协议和平台上的事件、数据和操作的快速访问。  逻辑应用支持的连接器完整列表可[在此处找到](apis-list.md)。  连接器可在逻辑应用中用作触发器或操作，并且可能需要使用经配置的*连接*（例如：授权 Twitter 帐户代表你进行访问或发布）。

## <a name="basics"></a>基础知识
连接器是可作为逻辑应用一部分访问的托管服务，用于与其他服务（如 Dynamics、Azure、Salesforce [等](apis-list.md)）集成。  它们由 Microsoft 部署和管理，因此可在考虑到规模、吞吐量和安全性的情况下生成集成工作流。  可将连接器添加到逻辑应用，方法是在“显示 Microsoft 托管 API”下搜索并选择连接器操作或触发器。

![用于选择触发器的操作菜单][1]

每个连接器操作或触发器都有其自己的要配置的属性集。  可单击信息按钮了解关于操作的详细信息，或参考其文档[了解详细信息](apis-list.md)。

如果要与尚不是连接器的服务或 API 集成，还可通过[自定义连接器](../logic-apps/logic-apps-create-api-app.md)扩展逻辑应用，或通过 HTTP 之类的协议直接向服务调用。

## <a name="triggers"></a>触发器
某些连接器具有触发器，这意味着来自该连接器的事件将触发逻辑应用，并传入任何数据作为触发器的一部分。  触发器始终是逻辑应用中的第一步。  常用触发器包含如下操作：

* 重复周期 - 每小时运行
* 收到 HTTP 请求时
* 向队列添加项目时
* 收到电子邮件时

某些触发器将在发生事件时通过对逻辑应用的通知立即触发，而其他触发器需要针对逻辑应用在服务中检查事件的频率（最多为每隔 15 秒）配置重复周期间隔。  

收到事件后，逻辑应用将触发，并且工作流中的操作将启动。  还将能够访问整个工作流中来自触发器的任何数据（例如“在新推文上”触发器将推文传递到运行中）。

## <a name="actions"></a>操作
大多数连接器具有一个或许多可作为工作流一部分执行的操作。  操作是在运行已从触发器触发之后发生的任务步骤。  若要添加操作，请单击“新步骤”按钮，并搜索要使用的连接器。  选择后（并且在配置任何可能需要的[连接](#connections)后），将看到可配置的操作卡。  可通过单击任意输出令牌选择来自之前步骤的数据，或按需输入任何其他配置。

![配置连接器操作][2]

## <a name="connections"></a>连接
大多数连接器需要配置*连接*后才能使用连接器。  *连接*是访问连接器所需的任何登录或连接配置。  对于使用 OAuth 的连接器，创建连接意味着登录服务（如 Office 365、Salesforce 或 GitHub），在该服务中安全令牌可加密并安全存储在 Azure 机密存储中。  其他连接器（如 FTP 和 SQL）需要包含服务器地址、用户名和密码之类的配置的连接。  这些连接配置详细信息同样加密并安全存储。  只要服务允许，连接将始终能够访问该服务。  对于 Azure Active Directory OAuth 连接（如 Office 365 和 Dynamics），我们可以继续无限期地刷新访问令牌。  其他服务可能对在不刷新的情况下可使用令牌的时长施加限制。  通常，特定操作（如更改密码）将导致所有访问令牌失效。  

可通过单击“浏览”并选择“API 连接”在 Azure 中查看和管理连接。  从 API 连接资源，可以查看、编辑、更新或重新创作任何已创建的连接。

## <a name="next-steps"></a>后续步骤
* [创建第一个逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)
* [了解逻辑应用的常见用途和示例](../logic-apps/logic-apps-examples-and-scenarios.md)
* [企业集成触发器和操作入门](../logic-apps/logic-apps-enterprise-integration-overview.md)

<!--Image References -->
[1]: ./media/connectors-overview/addAction.png
[2]: ./media/connectors-overview/configureAction.png



<!--HONumber=Jan17_HO3-->


