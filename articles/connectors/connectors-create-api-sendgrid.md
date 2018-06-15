---
title: SendGrid | Microsoft Docs
description: 使用 Azure 应用服务创建逻辑应用。 SendGrid Connection 提供程序可使你发送电子邮件和管理收件人列表。
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: bc4f1fc2-824c-4ed7-8de8-e82baff3b746
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 0b34a76ecaf4997cbf66c3d026cd770aa8aa080d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295833"
---
# <a name="get-started-with-the-sendgrid-connector"></a>SendGrid 连接器入门
SendGrid Connection 提供程序可使你发送电子邮件和管理收件人列表。

若要立即开始创建逻辑应用，请参阅[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-a-connection-to-sendgrid"></a>创建到 SendGrid 的连接
要使用 SendGrid 创建逻辑应用，必须先创建**连接**，然后提供以下属性的详细信息： 

| 属性 | 必选 | 说明 |
| --- | --- | --- |
| ApiKey |是 |提供 SendGrid Api 密钥 |

> [!INCLUDE [Steps to create a connection to SendGrid](../../includes/connectors-create-api-sendgrid.md)]
> 


创建连接后，可使用它执行操作并侦听触发器。

## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/sendgrid/)中查看在 Swagger 中定义的触发器和操作，并查看限制。

## <a name="more-connectors"></a>更多连接器
返回到 [API 列表](apis-list.md)。