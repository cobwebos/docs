---
title: Azure 逻辑应用中的 Wunderlist 连接器 | Microsoft Docs
description: 创建与 Wunderlist 的连接，并使用此连接在逻辑应用中构建工作流。
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 4d1ae30724faa59dcdeffd21be9c67d280d574f6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-wunderlist-connector"></a>Wunderlist 连接器入门
Wunderlist 是待办事项列表和任务管理器，可帮助用户完成其任务。  无论是共享购物单、处理某个项目还是计划度假，使用 Wunderlist 都可轻松捕获、共享和完成列表项。 Wunderlist 在手机、平板电脑和计算机之间即时同步，以便你从任意位置访问所有任务。

若要立即开始创建逻辑应用，请参阅[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-a-connection-to-wunderlist"></a>创建到 Wunderlist 的连接
要使用 Wunderlist 创建逻辑应用，必须先创建**连接**，然后提供以下属性的详细信息：

| 属性 | 必选 | 说明 |
| --- | --- | --- |
| 令牌 |是 |提供 Wunderlist 凭据 |

创建连接后，可使用它执行操作并侦听触发器。

> [!INCLUDE [Steps to create a connection to Wunderlist](../../includes/connectors-create-api-wunderlist.md)]
> 

## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/wunderlist/)中查看在 Swagger 中定义的触发器和操作，并查看限制。

## <a name="more-connectors"></a>更多连接器
返回到 [API 列表](apis-list.md)。