---
title: "在 Azure 逻辑应用中使用 GitHub 连接器 | Microsoft Docs"
description: "使用 Azure 应用服务创建逻辑应用。 GitHub 是用于托管服务的基于 Web 的 Git 存储库。 它提供 Git 的所有已分配的版本控制和源代码管理 (SCM) 功能以及添加其自己的功能。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 8f873e6c-f4c0-4c2e-a5bd-2e953efe5e2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: d4614b0ceff0ec0d36dbb1a136551f985f2fc1a1
ms.contentlocale: zh-cn
ms.lasthandoff: 05/26/2017


---
# <a name="get-started-with-the-github-connector"></a>GitHub 连接器入门
GitHub 是用于托管服务的基于 Web 的 Git 存储库。 它提供 Git 的所有已分配的版本控制和源代码管理 (SCM) 功能以及添加其自己的功能。

若要立即开始创建逻辑应用，请参阅[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="create-a-connection-to-github"></a>创建到 GitHub 的连接
若要使用 GitHub 创建逻辑应用，必须先创建**连接**，然后提供以下属性的详细信息： 

| 属性 | 必选 | 说明 |
| --- | --- | --- |
| 令牌 |是 |提供 GitHub 凭据 |

创建连接后，可使用它执行操作并侦听触发器，如本文所述。 

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/github/)中查看在 Swagger 中定义的触发器和操作，并查看限制。

## <a name="more-connectors"></a>更多连接器
返回到 [API 列表](apis-list.md)。
