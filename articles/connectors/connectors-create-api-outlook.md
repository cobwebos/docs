---
title: "在 Azure 逻辑应用中使用 Outlook.com 连接器 | Microsoft Docs"
description: "使用 Azure 应用服务创建逻辑应用。 Outlook.com 连接器允许管理邮件、日历和联系人。 可以执行各种操作，例如发送邮件、安排会议、添加联系人等。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 87113c85-d158-4dd5-9ed5-5748130003d6
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: e55519bd329812d2e8ad35c9d774dcbe382d68aa
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-outlookcom-connector"></a>Outlook.com 连接器入门
Outlook.com 连接器允许管理邮件、日历和联系人。 可以执行各种操作，例如发送邮件、安排会议、添加联系人等。

若要立即开始创建逻辑应用，请参阅[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-a-connection-to-outlookcom"></a>创建到 Outlook.com 的连接
要使用 Outlook.com 创建逻辑应用，必须先创建**连接**，然后提供以下属性的详细信息：

| 属性 | 必选 | 说明 |
| --- | --- | --- |
| 令牌 |是 |提供 Outlook.com 凭据 |

创建连接后，可使用它执行操作并侦听触发器，如本文所述。

> [!INCLUDE [Steps to create a connection to Outlook.com](../../includes/connectors-create-api-outlook.md)]
>

## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/outlook/)中查看在 Swagger 中定义的触发器和操作，并查看限制。

## <a name="more-connectors"></a>更多连接器
返回到 [API 列表](apis-list.md)。