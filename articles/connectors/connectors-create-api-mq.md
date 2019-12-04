---
title: 连接到 IBM MQ 服务器
description: 使用 Azure 或本地 IBM MQ 服务器和 Azure 逻辑应用发送和检索消息
services: logic-apps
ms.suite: integration
author: valrobb
ms.author: valthom
ms.reviewer: chrishou, logicappspm
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: ef9e91b526055ece58ce283572deb98cff951653
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789576"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>从 Azure 逻辑应用连接到 IBM MQ 服务器

IBM MQ 连接器发送和检索存储在本地或 Azure 中的 IBM MQ 服务器中的消息。 此连接器包括要在 TCP/IP 网络上与远程 IBM MQ 服务器计算机通信的 Microsoft MQ 客户端。 本文提供了使用 MQ connector 的入门指南。 首先，可以在队列上浏览一条消息，然后尝试其他操作。

IBM MQ 连接器包含这些操作，但不提供触发器：

- 浏览单个消息而不从 IBM MQ 服务器中删除该消息
- 浏览一批消息而不从 IBM MQ 服务器中删除这些消息
- 接收一条消息，并从 IBM MQ 服务器中删除该消息
- 接收一批消息，并从 IBM MQ 服务器中删除这些消息
- 向 IBM MQ 服务器发送一条消息

## <a name="prerequisites"></a>必备组件

* 如果使用的是本地 MQ 服务器，请在网络中的服务器上[安装本地数据网关](../logic-apps/logic-apps-gateway-install.md)。 安装本地数据网关的服务器还必须安装 .NET Framework 4.6，才能使 MQ 连接器正常运行。 还必须在 Azure 中为本地数据网关创建资源。 有关详细信息，请参阅[设置数据网关连接](../logic-apps/logic-apps-gateway-connection.md)。

  但是，如果 MQ 服务器在 Azure 中公开发布或可用，则无需使用数据网关。

* 官方支持的 IBM WebSphere MQ 版本：

  * MQ 7.5
  * MQ 8.0
  * MQ 9。0

* 要添加 MQ 操作的逻辑应用。 此逻辑应用必须使用与本地数据网关连接相同的位置，并且必须已有启动工作流的触发器。 

  MQ 连接器没有任何触发器，因此必须先将触发器添加到逻辑应用。 例如，您可以使用定期触发器。 如果不熟悉逻辑应用，请尝试此[快速入门创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 

## <a name="browse-a-single-message"></a>浏览单个消息

1. 在逻辑应用中的触发器或其他操作下，选择 "**新建步骤**"。 

1. 在搜索框中，键入 "mq"，然后选择此操作：**浏览消息**

   ![浏览消息](media/connectors-create-api-mq/Browse_message.png)

1. 如果没有现有的 MQ 连接，请创建连接：  

   1. 在 "操作" 中，选择 **"通过本地数据网关连接**"。
   
   1. 输入 MQ 服务器的属性。  

      对于“服务器”，可以输入 MQ 服务器名称，或输入后跟冒号和端口号的 IP 地址。
    
   1. 打开 "**网关**" 列表，其中显示了任何以前配置的网关连接。 选择你的网关。
    
   1. 完成后，选择“创建”。 
   
      你的连接类似于以下示例：

      ![连接属性](media/connectors-create-api-mq/Connection_Properties.png)

1. 设置操作的属性：

   * **队列**：指定与连接不同的队列。

   * **MessageId**、 **CorrelationId**、 **GroupId**和其他属性：浏览基于不同 MQ 消息属性的消息

   * **IncludeInfo**：指定**True**可在输出中包含其他消息信息。 或者指定**False**以不在输出中包含其他消息信息。

   * **超时**值：输入一个值，确定消息到达空队列的时间长度。 如果未输入任何内容，则检索队列中的第一个消息，并且不花费时间等待消息出现。

     ![浏览消息属性](media/connectors-create-api-mq/Browse_message_Props.png)

1. **保存**更改，然后**运行**逻辑应用。

   ![保存并运行](media/connectors-create-api-mq/Save_Run.png)

   运行完成后，将显示运行的步骤，你可以查看输出。

1. 若要查看每个步骤的详细信息，请选择绿色复选标记。 若要查看有关输出数据的详细信息，请选择 "**显示原始输出**"。

   ![浏览消息输出](media/connectors-create-api-mq/Browse_message_output.png)  

   下面是一些示例原始输出：

   ![浏览消息原始输出](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. 如果将**IncludeInfo**设置为 true，则将显示以下输出：

   ![浏览消息包括信息](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>浏览多个消息

“浏览消息”操作包含“BatchSize”选项，用于指示应从队列返回的消息数。  如果“BatchSize”没有输入，则返回所有消息。 返回的输出是消息数组。

1. 添加 "**浏览消息**" 操作时，默认情况下会选择第一个以前配置的连接。 若要创建新连接，请选择 "**更改连接**"。 或者，选择其他连接。

1. 逻辑应用运行完成后，以下是 "**浏览消息**" 操作的一些示例输出：

   ![浏览消息输出](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>接收单个消息

“接收消息”操作具有与“浏览消息”操作相同的输入和输出。 使用“接收消息”时，消息会从队列中删除。

## <a name="receive-multiple-messages"></a>接收多个消息

“接收消息”操作具有与“浏览消息”操作相同的输入和输出。 使用“接收消息”时，消息会从队列中删除。

如果在执行浏览或接收时队列中没有消息，则该步骤将失败并显示以下输出：  

![MQ 无消息错误](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>发送消息

添加 "**发送消息**" 操作时，默认情况下会选择第一个以前配置的连接。 若要创建新连接，请选择 "**更改连接**"。 或者，选择其他连接。

1. 选择有效的消息类型：**数据报**、**答复**或**请求**  

   ![发送消息属性](media/connectors-create-api-mq/Send_Msg_Props.png)

1. 逻辑应用完成运行后，以下是 "**发送消息**" 操作的一些示例输出：

   ![发送消息输出](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>连接器参考

有关操作和限制的技术详细信息，请参阅连接器的 OpenAPI （以前称为 Swagger）说明，查看连接器的[参考页](/connectors/mq/)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
