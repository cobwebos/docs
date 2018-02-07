---
title: "在 Azure 逻辑应用中使用 Slack 连接器 | Microsoft Docs"
description: "在逻辑应用中连接到 Slack"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 04ea4508495b227d6ace4a3105f283c474c51d14
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-slack-connector"></a>Slack 连接器入门
Slack 是一款团队通信工具，可将所有团队通信归于一处，可即时搜索，并且随时随地可用。 

若要立即开始创建逻辑应用，请参阅[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-a-connection-to-slack"></a>创建到 Slack 的连接
要使用 Slack 连接，首先创建**连接**，然后为以下属性提供详细信息： 

| 属性 | 必选 | 说明 |
| --- | --- | --- |
| 令牌 |是 |提供 Slack 凭据 |

按照以下步骤登录 Slack 并在逻辑应用中完成 Slack“连接”配置：

1. 选择“重复周期”
2. 选择“频率”并输入“间隔”
3. 选择“添加操作”  
   ![配置 Slack][1]  
4. 在搜索框中输入“Slack”，并等待搜索返回在名称中带有 Slack 的所有项
5. 选择“Slack - 发布消息”
6. 选择“登录到 Slack”：  
   ![配置 Slack][2]
7. 提供用于登录的 Slack 凭据以向应用程序授权    
   ![配置 Slack][3]  
8. 将重定向到组织的登录页面。 **授权** Slack 与逻辑应用交互：      
   ![配置 Slack][5] 
9. 授权完成后，将重定向到逻辑应用，通过配置“Slack - 获取所有消息”部分完成它。 添加所需的其他触发器和操作。  
   ![配置 Slack][6]
10. 通过选择上方菜单栏中的“保存” 保存工作。

## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/slack/)中查看在 Swagger 中定义的触发器和操作，并查看限制。

## <a name="more-connectors"></a>更多连接器
返回到 [API 列表](apis-list.md)。

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png
