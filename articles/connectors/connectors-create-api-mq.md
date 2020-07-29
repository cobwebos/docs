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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85609497"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>从 Azure 逻辑应用连接到 IBM MQ 服务器

IBM MQ 连接器会发送和检索存储在 IBM MQ 服务器本地或 Azure 中的消息。 此连接器包括要在 TCP/IP 网络上与远程 IBM MQ 服务器计算机通信的 Microsoft MQ 客户端。 本文提供使用 MQ 连接器的初学者指南。 首先可以在队列上浏览一条消息，然后尝试其他操作。

IBM MQ 连接器包含以下操作，但不提供触发器：

- 浏览单条消息，但不从 IBM MQ 服务器中删除这条消息。
- 浏览一批消息，但不从 IBM MQ 服务器中删除这些消息。
- 接收单条消息，并从 IBM MQ 服务器中删除这条消息。
- 接收一批消息，并从 IBM MQ 服务器中删除这些消息。
- 将单条消息发送到 IBM MQ 服务器。

下面是官方支持的 IBM WebSphere MQ 版本：

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0
  * MQ 9.1

## <a name="prerequisites"></a>先决条件

* 如果使用本地 MQ 服务器，请在网络中的服务器上[安装本地数据网关](../logic-apps/logic-apps-gateway-install.md)。 安装本地数据网关的服务器还必须安装 .NET Framework 4.6，才能使 MQ 连接器正常运行。

  网关安装完成后，还必须在 Azure 中为本地数据网关创建资源。 有关详细信息，请参阅[设置数据网关连接](../logic-apps/logic-apps-gateway-connection.md)。

  如果 MQ 服务器公开发布，或在 Azure 中可用，则不必使用数据网关。

* 要在其中添加 MQ 操作的逻辑应用。 此逻辑应用必须使用与本地数据网关连接相同的位置，并且必须已具有启动工作流的触发器。

  MQ 连接器没有任何触发器，因此必须首先向逻辑应用添加触发器。 例如，可以使用定期触发器。 如果你不熟悉逻辑应用，请尝试[创建第一个逻辑应用的快速入门](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="create-connection"></a>

## <a name="create-mq-connection"></a>创建 MQ 连接

如果在添加 MQ 操作时尚未建立 MQ 连接，则系统将提示你创建连接，例如：

![提供连接信息](media/connectors-create-api-mq/connection-properties.png)

1. 如果要连接到本地 MQ 服务器，请选择“通过本地数据网关连接”。

1. 提供 MQ 服务器的连接信息。

   * 对于“服务器”，可以输入 MQ 服务器名称，或输入后跟冒号和端口号的 IP 地址。

   * 要使用安全套接字层 (SSL)，请选择“是否启用 SSL?”。

     MQ 连接器当前仅支持服务器身份验证，不支持客户端身份验证。 有关详细信息，请参阅[连接和身份验证问题](#connection-problems)。

1. 在“网关”部分，请执行以下步骤：

   1. 在“订阅”列表中，请选择与 Azure 网关资源关联的 Azure 订阅。

   1. 在“连接网关”列表中，选择要使用的 Azure 网关资源。

1. 完成操作后，选择“创建”。

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>连接和身份验证问题

逻辑应用尝试连接到本地 MQ 服务器时，你可能会收到此错误：

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* 如果直接在 Azure 中使用 MQ 连接器，则 MQ 服务器需要使用由受信任的[证书颁发机构](https://www.ssl.com/faqs/what-is-a-certificate-authority/)颁发的证书。

* 如果使用的是本地数据网关，请尝试使用由受信任的[证书颁发机构](https://www.ssl.com/faqs/what-is-a-certificate-authority/)颁发的证书（如果可能）。 但是，如果无法使用此选项，则可以使用自签名证书，该证书不是由受信任的[证书颁发机构](https://www.ssl.com/faqs/what-is-a-certificate-authority/)颁发的并且被认为不太安全。

  要安装服务器的自签名证书，可以使用“Windows 认证管理器”(certmgr.msc) 工具。 对于此方案，在运行本地数据网关服务的本地计算机上，需要在“可信根证书颁发机构”级别的“本地计算机”证书存储处安装证书 。

  1. 在运行本地数据网关服务的计算机上，打开开始菜单，查找并选择“管理用户证书”。

  1. 打开 Windows 认证管理器工具后，转到“证书 - 本地计算机” >  “受信任的根证书颁发机构”文件夹，然后安装证书 。

     > [!IMPORTANT]
     > 请确保在“证书 - 本地计算机” > “受信任的根证书颁发机构”存储中安装证书 。

* MQ 服务器要求你定义要用于 TLS/SSL 连接的密码规范。 但是，.NET 中的 System.net.security.sslstream 不允许你指定密码规范的顺序。 若要解决此限制，可以更改 MQ 服务器配置，使之与连接器在 TLS/SSL 协商中发送的套件中的第一个密码规范相匹配。

  尝试连接时，MQ 服务器会记录一条事件消息，指示连接失败，因为另一端使用了不正确的密码规范。 这条事件消息包含列表中首先出现的密码规范。 更新通道配置中的密码规范，以匹配事件消息中的密码规范。

## <a name="browse-single-message"></a>浏览单条消息

1. 在逻辑应用中的触发器和其他操作下，选择“新建步骤”。

1. 在搜索框中，输入 `mq`，然后选择“浏览消息”操作。

   ![选择“浏览消息”操作](media/connectors-create-api-mq/browse-message.png)

1. 如果尚未创建 MQ 连接，系统将提示你[创建该连接](#create-connection)。

1. 创建连接后，设置“浏览消息”操作的属性：

   | properties | 说明 |
   |----------|-------------|
   | **队列** | 如果与连接中指定的队列不同，请指定该队列。 |
   | “MessageId”、“CorrelationId”、“GroupId”和其他属性   | 浏览基于不同 MQ 消息属性的消息 |
   | **IncludeInfo** | 要在输出中包含其他消息信息，请选择“true”。 要省略输出中的其他消息信息，请选择“false”。 |
   | **超时** | 输入一个值以确定在空队列中等待消息到达的时间长度。 如果未输入任何内容，则检索队列中的第一个消息，并且不花费时间等待消息出现。 |
   |||

   例如：

   ![“浏览消息”操作的属性](media/connectors-create-api-mq/browse-message-properties.png)

1. 完成后，请在设计器工具栏上选择“保存”。 要测试应用，请选择“运行”。

   运行完成后，设计器将显示工作流步骤及其状态，以便你可以查看输出。

1. 要查看有关每个步骤的详细信息，请单击步骤的标题栏。 要查看有关步骤输出的详细信息，请选择“显示原始输出”。

   ![浏览消息输出](media/connectors-create-api-mq/browse-message-output.png)

   以下是一些示例原始输出：

   ![浏览消息原始输出](media/connectors-create-api-mq/browse-message-raw-output.png)

1. 如果将“IncludeInfo”设置为“true”，则会显示附加输出 ：

   ![浏览消息包含信息](media/connectors-create-api-mq/browse-message-include-info.png)

## <a name="browse-multiple-messages"></a>浏览多个消息

“浏览消息”操作包含“BatchSize”选项，以指示从队列返回的消息数 。 如果“BatchSize”没有值，则返回所有消息。 返回的输出是消息数组。

1. 按照前面的步骤操作，但改为添加“浏览消息”操作。

1. 如果尚未创建 MQ 连接，系统将提示你[创建该连接](#create-connection)。 否则，默认情况下，将使用以前配置的第一个连接。 要创建新连接，请选择“更改连接”。 或者，选择其他连接。

1. 提供操作信息。

1. 保存并运行逻辑应用。

   逻辑应用完成运行后，下面是“浏览消息”操作中的一些示例输出：

   ![示例“浏览消息”输出](media/connectors-create-api-mq/browse-messages-output.png)

## <a name="receive-single-message"></a>接收单条消息

“接收消息”操作具有与“浏览消息”操作相同的输入和输出。 使用“接收单条消息”时，会从队列中删除这条消息。

## <a name="receive-multiple-messages"></a>接收多个消息

“接收消息”操作具有与“浏览消息”操作相同的输入和输出。 使用“接收多条消息”时，会从队列中删除这些消息。

> [!NOTE]
> 在没有任何消息的队列上运行浏览或接收操作时，该操作将失败，并显示以下输出：
>
> ![MQ“无消息”错误](media/connectors-create-api-mq/mq-no-message-error.png)

## <a name="send-message"></a>发送消息

1. 按照前面的步骤操作，但改为添加“发送消息”操作。

1. 如果尚未创建 MQ 连接，系统将提示你[创建该连接](#create-connection)。 否则，默认情况下，将使用以前配置的第一个连接。 要创建新连接，请选择“更改连接”。 或者，选择其他连接。

1. 提供操作信息。 对于“MessageType”，请选择有效的消息类型：“数据报”、“回复”或“请求”  

   ![“发送消息操作”的属性](media/connectors-create-api-mq/send-message-properties.png)

1. 保存并运行逻辑应用。

   逻辑应用完成运行后，下面是“发送消息”操作中的一些示例输出：

   ![示例“发送消息”输出](media/connectors-create-api-mq/send-message-output.png)

## <a name="connector-reference"></a>连接器参考

有关操作和限制（请参见连接器的 Swagger 说明）的技术详细信息，请查看连接器的[参考页](/connectors/mq/)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
