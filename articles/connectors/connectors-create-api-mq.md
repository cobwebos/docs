---
title: "了解如何在 Azure 逻辑应用中使用 MQ 连接器 | Microsoft Docs"
description: "从逻辑应用工作流连接到本地或 Azure MQ 服务器以浏览消息、接收消息以及将消息发送到 WebSphere MQ"
services: logic-apps
author: valthom
manager: anneta
documentationcenter: 
editor: 
tags: connectors
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 06/01/2017
ms.author: valthom; ladocs
ms.openlocfilehash: 24ea1ef4d3c6229707458ef2f2005f90d8273304
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="connect-to-an-ibm-mq-server-from-logic-apps-using-the-mq-connector"></a>使用 MQ 连接器从逻辑应用连接到 IBM MQ 服务器 

Microsoft Connector for MQ 会发送和检索存储在 MQ 服务器本地或 Azure 中的消息。 此连接器包括要在 TCP/IP 网络上与远程 IBM MQ 服务器计算机通信的 Microsoft MQ 客户端。 本文档是使用 MQ 连接器的初学者指南。 建议首先浏览队列中的单个消息，然后尝试其他操作。    

MQ 连接器包括以下操作。 没有任何触发器。

-   浏览单个消息，而不从 IBM MQ 服务器中删除该消息
-   浏览一批消息，而不从 IBM MQ 服务器中删除这些消息
-   接收单个消息，并从 IBM MQ 服务器中删除该消息
-   接收一批消息，并从 IBM MQ 服务器中删除这些消息
-   将单个消息发送到 IBM MQ 服务器 

## <a name="prerequisites"></a>先决条件

* 如果使用本地 MQ 服务器，则在网络中的服务器上[安装本地数据网关](../logic-apps/logic-apps-gateway-install.md)。 如果 MQ 服务器公开可用，或在 Azure 中可用，则不使用或无需数据网关。

    > [!NOTE]
    > 安装本地数据网关的服务器还必须安装 .Net Framework 4.6，才能使 MQ 连接器正常运行。

* 为本地数据网关创建 Azure 资源 - [设置数据网关连接](../logic-apps/logic-apps-gateway-connection.md)。

* 官方支持的 IBM WebSphere MQ 版本：
   * MQ 7.5
   * MQ 8.0

## <a name="create-a-logic-app"></a>创建逻辑应用

1. 在 **Azure 开始面板**中，依次选择“+”（加号）、“Web + 移动”和“逻辑应用”。 
2. 输入“名称”（如 MQTestApp）、“订阅”、“资源组”和“位置”（使用在其中配置本地数据网关连接的位置）。 选择“固定到仪表板”，并选择“创建”。  
![创建逻辑应用](media/connectors-create-api-mq/Create_Logic_App.png)

## <a name="add-a-trigger"></a>添加触发器

> [!NOTE]
> MQ 连接器没有任何触发器。 因此，使用另一个触发器启动逻辑应用，如“重复”触发器。 

1. “逻辑应用设计器”随即打开，在常用触发器列表中选择“重复”。
2. 在重复触发器中选择“编辑”。 
3. 将“频率”设置为“天”，并将“间隔”设置为 **7**。 

## <a name="browse-a-single-message"></a>浏览单个消息
1. 选择“+ 新步骤”，然后选择“添加操作”。
2. 在搜索框中，输入 `mq`，然后选择“MQ - 浏览消息”。  
![浏览消息](media/connectors-create-api-mq/Browse_message.png)

3. 如果没有现有 MQ 连接，则创建连接：  

    1. 选择“通过本地数据网关连接”，然后输入 MQ 服务器的属性。  
    对于“服务器”，可以输入 MQ 服务器名称，或输入后跟冒号和端口号的 IP 地址。 
    2. “网关”下拉列表会列出已配置的所有现有网关连接。 选择你的网关。
    3. 完成后，选择“创建”。 你的连接会类似于以下内容：   
    ![连接属性](media/connectors-create-api-mq/Connection_Properties.png)

4. 在操作属性中，可以：  

    * 使用“队列”属性访问与连接中定义的内容不同的队列名称
    * 使用“MessageId”、“CorrelationId”、“GroupId”和其他属性基于不同 MQ 消息属性来浏览查找消息
    * 将“IncludeInfo”设置为“True”以在输出中包含其他消息信息。 或者，将它设置为“False”以在输出中不包含其他消息信息。
    * 输入“超时”值以确定在空队列中等待消息到达的时间长度。 如果未输入任何内容，则检索队列中的第一个消息，并且不花费时间等待消息出现。  
    ![浏览消息属性](media/connectors-create-api-mq/Browse_message_Props.png)

5. “保存”更改，然后“运行”逻辑应用：  
![“保存”和“运行”](media/connectors-create-api-mq/Save_Run.png)

6. 几秒钟后，会显示运行的步骤，你可以查看输出。 选择绿色复选标记可查看每个步骤的详细信息。 选择“查看原始输出”可查看有关输出数据的其他详细信息。  
![浏览消息输出](media/connectors-create-api-mq/Browse_message_output.png)  

    原始输出：  
    ![浏览消息原始输出](media/connectors-create-api-mq/Browse_message_raw_output.png)

7. 当“IncludeInfo”选项设置为 true 时，会显示以下输出：  
![浏览消息包含信息](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>浏览多个消息
“浏览消息”操作包含“BatchSize”选项，用于指示应从队列返回的消息数。  如果“BatchSize”没有输入，则返回所有消息。 返回的输出是消息数组。

1. 添加“浏览消息”操作时，默认情况下会选择配置的第一个连接。 选择“更改连接”可创建新连接，或选择其他连接。

2. 浏览消息的输出会显示：  
![浏览消息输出](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-a-single-message"></a>接收单个消息
“接收消息”操作具有与“浏览消息”操作相同的输入和输出。 使用“接收消息”时，消息会从队列中删除。

## <a name="receive-multiple-messages"></a>接收多个消息
“接收消息”操作具有与“浏览消息”操作相同的输入和输出。 使用“接收消息”时，消息会从队列中删除。

如果进行浏览或接收时队列中不存在消息，则步骤会失败，同时显示以下输出：  
![MQ 无消息错误](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-a-message"></a>发送消息
1. 添加“发送消息”操作时，默认情况下会选择配置的第一个连接。 选择“更改连接”可创建新连接，或选择其他连接。 有效“消息类型”是“数据报”、“答复”或“请求”。  
![发送消息属性](media/connectors-create-api-mq/Send_Msg_Props.png)

2. 发送消息的输出类似于以下内容：  
![发送消息输出](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/mq/)中查看在 Swagger 中定义的触发器和操作，并查看限制。

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 在我们的 [API 列表](apis-list.md)中了解逻辑应用中的其他可用连接器。
