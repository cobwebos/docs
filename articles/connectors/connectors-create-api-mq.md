---
title: 连接到 IBM MQ 服务器-Azure 逻辑应用
description: 发送和检索 Azure 或本地的 IBM MQ 服务器和 Azure 逻辑应用的消息
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: valrobb
ms.author: valthom
ms.reviewer: chrishou, LADocs
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: a2894799946d069916b27a4f5bcc7bd3244705b2
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273123"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>从 Azure 逻辑应用连接到 IBM MQ 服务器

IBM MQ 连接器发送和检索存储在 IBM MQ 服务器在本地或 Azure 中的消息。 此连接器包括要在 TCP/IP 网络上与远程 IBM MQ 服务器计算机通信的 Microsoft MQ 客户端。 本文提供了使用 MQ 连接器的初学者指南。 您可以首先浏览队列中的单个消息，然后再尝试其他操作。

IBM MQ 连接器包括这些操作，但提供了任何触发器：

- 浏览单个消息，而不从 IBM MQ 服务器中删除消息
- 浏览一批消息，而不从 IBM MQ 服务器中删除消息
- 接收单个消息并从 IBM MQ 服务器中删除消息
- 接收一批消息并从 IBM MQ 服务器中删除消息
- 将一条消息发送到 IBM MQ 服务器

## <a name="prerequisites"></a>必备组件

* 如果使用本地 MQ server 上，[安装在本地数据网关](../logic-apps/logic-apps-gateway-install.md)在网络中的服务器上。 安装本地数据网关的服务器还必须安装的.NET Framework 4.6 MQ 连接器工作。 您必须在本地数据网关在 Azure 中创建资源。 有关详细信息，请参阅[设置数据网关连接](../logic-apps/logic-apps-gateway-connection.md)。

  但是，如果 MQ 服务器公开可用或在 Azure 中可用，您无需使用数据网关。

* 官方支持的 IBM WebSphere MQ 版本：

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0

* 你想要添加 MQ 操作逻辑应用。 此逻辑应用必须使用相同的位置作为你的本地数据网关连接，并且必须已启动工作流的触发器。 

  MQ 连接器没有任何触发器，因此必须先将触发器添加到逻辑应用。 例如，可以使用重复触发器。 如果您熟悉逻辑应用，请尝试以下[创建第一个逻辑应用的快速入门](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 

## <a name="browse-a-single-message"></a>浏览单个消息

1. 在逻辑应用，在另一个操作或触发器中选择**新步骤**。 

1. 在搜索框中，键入"mq"，然后选择以下操作：**浏览消息**

   ![浏览消息](media/connectors-create-api-mq/Browse_message.png)

1. 如果没有现有 MQ 连接，创建的连接：  

   1. 在操作中，选择**通过本地数据网关连接**。
   
   1. 输入 MQ 服务器的属性。  

      对于“服务器”  ，可以输入 MQ 服务器名称，或输入后跟冒号和端口号的 IP 地址。
    
   1. 打开**网关**列表中，其中显示了任何以前配置的网关连接。 选择你的网关。
    
   1. 完成后，选择“创建”  。 
   
      你的连接会如下例所示：

      ![连接属性](media/connectors-create-api-mq/Connection_Properties.png)

1. 设置操作的属性：

   * **队列**:指定从连接不同的队列。

   * **MessageId**， **CorrelationId**， **GroupId**，和其他属性：浏览基于不同 MQ 消息属性的消息

   * **Includeinfo**:指定 **，则返回 True**在输出中包含其他消息信息。 或者，指定**False**输出中不包含其他消息信息。

   * **Timeout**：输入的值来确定多长时间等待消息到达空队列中。 如果未输入任何内容，则检索队列中的第一个消息，并且不花费时间等待消息出现。

     ![浏览消息属性](media/connectors-create-api-mq/Browse_message_Props.png)

1. **保存**所做的更改，然后**运行**逻辑应用。

   ![保存并运行](media/connectors-create-api-mq/Save_Run.png)

   运行完成后，显示了从运行步骤，并可以查看的输出。

1. 若要查看每个步骤的详细信息，请选择绿色的选中标记。 若要查看有关输出数据的详细信息，请选择**显示原始输出**。

   ![浏览消息输出](media/connectors-create-api-mq/Browse_message_output.png)  

   下面是一些示例的原始输出：

   ![浏览消息原始输出](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. 如果您设置**includeinfo**为 true 时，以下显示输出：

   ![浏览消息包含信息](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>浏览多个消息

“浏览消息”  操作包含“BatchSize”  选项，用于指示应从队列返回的消息数。  如果“BatchSize”  没有输入，则返回所有消息。 返回的输出是消息数组。

1. 当您将添加**浏览消息**操作，第一个以前配置的连接在默认情况下处于选中状态。 若要创建新的连接，请选择**更改连接**。 或者，选择不同的连接。

1. 逻辑应用运行完成后，下面是一些示例输出**浏览消息**操作：

   ![浏览消息输出](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>接收单个消息

“接收消息”  操作具有与“浏览消息”  操作相同的输入和输出。 使用“接收消息”  时，消息会从队列中删除。

## <a name="receive-multiple-messages"></a>接收多个消息

“接收消息”  操作具有与“浏览消息”  操作相同的输入和输出。 使用“接收消息”  时，消息会从队列中删除。

如果没有任何消息队列中进行浏览或接收时，步骤会失败与此输出：  

![MQ 无消息错误](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>发送消息

当您将添加**发送消息**操作，第一个以前配置的连接在默认情况下处于选中状态。 若要创建新的连接，请选择**更改连接**。 或者，选择不同的连接。

1. 选择有效的消息类型：**数据报**，**答复**，或**请求**  

   ![发送消息属性](media/connectors-create-api-mq/Send_Msg_Props.png)

1. 逻辑应用完成运行后，下面是一些示例输出**发送消息**操作：

   ![发送消息输出](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>连接器参考

有关操作和限制的技术详细信息，其中描述了连接器的 OpenAPI (以前称为 Swagger) 描述，查看连接器[参考页](/connectors/mq/)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
