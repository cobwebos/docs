---
title: 连接到 IBM MQ 服务器
description: 使用 Azure 或本地 IBM MQ 服务器和 Azure 逻辑应用发送和检索消息
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, estfan, logicappspm
ms.topic: article
ms.date: 05/14/2020
tags: connectors
ms.openlocfilehash: e9e554fdc092e49f5a87049de0e3dc3163105f58
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85609497"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>从 Azure 逻辑应用连接到 IBM MQ 服务器

IBM MQ 连接器会发送和检索存储在 IBM MQ 服务器本地或 Azure 中的消息。 此连接器包括要在 TCP/IP 网络上与远程 IBM MQ 服务器计算机通信的 Microsoft MQ 客户端。 本文是使用 MQ 连接器的初学者指南。 可以首先浏览队列中的单个消息，然后尝试其他操作。

IBM MQ 连接器包含以下操作，但不提供触发器：

- 浏览单条消息，而不从 IBM MQ 服务器中删除该消息。
- 浏览一批消息，而不从 IBM MQ 服务器中删除这些消息。
- 接收单条消息，并从 IBM MQ 服务器中删除该消息。
- 接收一批消息，并从 IBM MQ 服务器中删除这些消息。
- 将单条消息发送到 IBM MQ 服务器。

下面是官方支持的 IBM WebSphere MQ 版本：

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0
  * MQ 9.1

## <a name="prerequisites"></a>先决条件

* 如果使用本地 MQ 服务器，则在网络中的服务器上[安装本地数据网关](../logic-apps/logic-apps-gateway-install.md)。 安装本地数据网关的服务器还必须安装 .NET Framework 4.6，才能使 MQ 连接器正常运行。

  安装完网关以后，还必须在 Azure 中为本地数据网关创建资源。 有关详细信息，请参阅[设置数据网关连接](../logic-apps/logic-apps-gateway-connection.md)。

  如果 MQ 服务器公开可用，或在 Azure 中可用，则不必使用数据网关。

* 要在其中添加 MQ 操作的逻辑应用。 此逻辑应用必须使用与本地数据网关连接相同的位置，并且必须已经有一个用于启动工作流的触发器。

  MQ 连接器没有任何触发器，因此必须先将触发器添加到逻辑应用。 例如，可以使用定期触发器。 如果你不熟悉逻辑应用，请尝试[创建第一个逻辑应用的快速入门](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="create-connection"></a>

## <a name="create-mq-connection"></a>创建 MQ 连接

如果你在添加 MQ 操作时还没有 MQ 连接，系统会提示你创建该连接，例如：

![提供连接信息](media/connectors-create-api-mq/connection-properties.png)

1. 如果要连接到本地 MQ 服务器，请选择“通过本地数据网关连接”。

1. 提供 MQ 服务器的连接信息。

   * 对于“服务器”，可以输入 MQ 服务器名称，或输入后跟冒号和端口号的 IP 地址。

   * 若要使用安全套接字层 (SSL)，请选择“启用 SSL?”。

     MQ 连接器目前仅支持服务器身份验证，不支持客户端身份验证。 有关详细信息，请参阅[连接和身份验证问题](#connection-problems)。

1. 在“网关”部分执行以下步骤：

   1. 从“订阅”列表中，选择与 Azure 网关资源关联的 Azure 订阅。

   1. 从“连接网关”列表中选择要使用的 Azure 网关资源。

1. 完成操作后，选择“创建”。

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>连接和身份验证问题

当逻辑应用尝试连接到本地 MQ 服务器时，可能会出现以下错误：

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* 如果你直接在 Azure 中使用 MQ 连接器，则 MQ 服务器需要使用一个由受信任的[证书颁发机构](https://www.ssl.com/faqs/what-is-a-certificate-authority/)颁发的证书。

* 如果使用的是本地数据网关，请尽可能尝试使用受信任的[证书颁发机构](https://www.ssl.com/faqs/what-is-a-certificate-authority/)颁发的证书。 但如果不能使用此选项，可以使用自签名证书，此类证书不是由受信任的[证书颁发机构](https://www.ssl.com/faqs/what-is-a-certificate-authority/)颁发的，被认为安全性较差。

  若要安装服务器的自签名证书，可以使用 **Windows 证书管理器** (certmgr.msc) 工具。 对于此方案，你需要将证书安装到运行本地数据网关服务的本地计算机上“受信任的根证书颁发机构”级别的“本地计算机”证书存储中。

  1. 在运行本地数据网关服务的计算机上，打开“开始”菜单，找到并选择“管理用户证书”。

  1. 在 Windows 证书管理器工具打开以后，转到“证书 - 本地计算机” >  “受信任的根证书颁发机构”文件夹，然后安装此证书。 

     > [!IMPORTANT]
     > 确保在“证书 - 本地计算机” > “受信任的根证书颁发机构”存储中安装证书。

* MQ 服务器需要你定义要用于 TLS/SSL 连接的密码规范。 但是，.NET 中的 SslStream 不允许你指定密码规范的顺序。 若要解决此限制，可以更改 MQ 服务器配置，使之与连接器在 TLS/SSL 协商中发送的套件中的第一个密码规范相匹配。

  尝试连接时，MQ 服务器会记录一条事件消息，指示连接由于另一端使用了错误的密码规范而失败。 事件消息包含在列表中首先出现的密码规范。 更新通道配置中的密码规范，使之与事件消息中的密码规范匹配。

## <a name="browse-single-message"></a>浏览单条消息

1. 在逻辑应用的触发器或另一操作下，选择“新建步骤”。

1. 在搜索框中输入 `mq`，然后选择“浏览消息”操作。

   ![选择“浏览消息”操作](media/connectors-create-api-mq/browse-message.png)

1. 如果尚未创建 MQ 连接，系统会提示你[创建该连接](#create-connection)。

1. 创建连接后，设置“浏览消息”操作的属性：

   | 属性 | 说明 |
   |----------|-------------|
   | **队列** | 如果不同于连接中指定的队列，则指定该队列。 |
   | “MessageId”、“CorrelationId”、“GroupId”和其他属性 | 根据不同的 MQ 消息属性找到对应的消息 |
   | **IncludeInfo** | 若要在输出中包括其他消息信息，请选择“true”。 若要在输出中省略其他消息信息，请选择“false”。 |
   | **超时** | 输入一个值以确定在空队列中等待消息到达的时间长度。 如果未输入任何内容，则检索队列中的第一个消息，并且不花费时间等待消息出现。 |
   |||

   例如：

   ![“浏览消息”操作的属性](media/connectors-create-api-mq/browse-message-properties.png)

1. 完成后，请在设计器工具栏上选择“保存”。 若要测试应用，请选择“运行”。

   运行完成后，设计器会显示工作流步骤及其状态，以便你查看输出。

1. 若要查看每个步骤的详细信息，请单击该步骤的标题栏。 若要查看某个步骤的输出的详细信息，请选择“显示原始输出”。

   ![浏览消息输出](media/connectors-create-api-mq/browse-message-output.png)

   下面是一些示例性的原始输出：

   ![浏览消息原始输出](media/connectors-create-api-mq/browse-message-raw-output.png)

1. 如果将“IncludeInfo”设置为 **true**，则会显示更多输出内容：

   ![浏览消息包含信息](media/connectors-create-api-mq/browse-message-include-info.png)

## <a name="browse-multiple-messages"></a>浏览多个消息

“浏览消息”操作包含“BatchSize”选项，用于指示要从队列返回的消息数。 如果“BatchSize”没有值，则返回所有消息。 返回的输出是消息数组。

1. 按照前面的步骤操作，但改为添加“浏览消息”操作。

1. 如果尚未创建 MQ 连接，系统会提示你[创建该连接](#create-connection)。 否则，默认情况下，会使用之前配置的第一个连接。 若要创建新连接，请选择“更改连接”。 或者，选择其他连接。

1. 提供该操作的信息。

1. 保存并运行逻辑应用。

   逻辑应用运行完后会显示输出，下面是来自“浏览消息”操作的一些示例输出：

   ![“浏览消息”输出示例](media/connectors-create-api-mq/browse-messages-output.png)

## <a name="receive-single-message"></a>接收单条消息

“接收消息”操作具有与“浏览消息”操作相同的输入和输出。 使用“接收消息”时，消息会从队列中删除。

## <a name="receive-multiple-messages"></a>接收多个消息

“接收消息”操作具有与“浏览消息”操作相同的输入和输出。 使用“接收消息”时，消息会从队列中删除。

> [!NOTE]
> 对没有任何消息的队列运行“浏览”或“接收”操作时，操作会失败，并显示以下输出：
>
> ![MQ 的“无消息”错误](media/connectors-create-api-mq/mq-no-message-error.png)

## <a name="send-message"></a>发送消息

1. 请遵循前面的步骤，但改为添加“发送消息”操作。

1. 如果尚未创建 MQ 连接，系统会提示你[创建该连接](#create-connection)。 否则，默认情况下，会使用之前配置的第一个连接。 若要创建新连接，请选择“更改连接”。 或者，选择其他连接。

1. 提供该操作的信息。 对于“MessageType”，请选择有效的消息类型：“数据报”、“答复”  或“请求”

   ![“发送消息”操作的属性](media/connectors-create-api-mq/send-message-properties.png)

1. 保存并运行逻辑应用。

   逻辑应用完成运行后，下面是“发送消息”操作中的一些示例输出：

   ![“发送消息”输出示例](media/connectors-create-api-mq/send-message-output.png)

## <a name="connector-reference"></a>连接器参考

有关在连接器的 Swagger 说明中描述的操作和限制的技术详细信息，请查看连接器的[参考页](/connectors/mq/)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
