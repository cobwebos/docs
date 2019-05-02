---
title: 用于 B2B 企业集成的 AS2 消息 - Azure 逻辑应用
description: 交换 Azure 逻辑应用使用 Enterprise Integration Pack 中的 AS2 消息
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: b494f6524e5105a95bc8a24a6fa2521abcca3f7b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64729396"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>在带有 Enterprise Integration Pack 的 Azure 逻辑应用中交换 AS2 消息以实现 B2B 企业集成

若要使用 Azure 逻辑应用中的 AS2 消息，可以使用 AS2 连接器、 提供触发器和操作用于管理 AS2 通信。 例如，若要传递消息时建立安全性和可靠性，可以使用这些操作：

* [**编码为 AS2 消息**操作](#encode)提供加密、 数字签名和确认通过消息处置通知 (MDN)，帮助的支持不可否认性。 例如，此操作应用 AS2/HTTP 标头，并执行这些任务时配置：

  * 对传出消息进行签名。
  * 对传出消息进行加密。
  * 压缩消息。
  * 传输的 MIME 标头中的文件名称。

* [**解码 AS2 消息**操作](#decode)用于提供解密、 数字签名和确认通过消息处置通知 (MDN)。 例如，此操作执行以下任务： 

  * 处理 AS2/HTTP 标头。
  * 协调与原始的出站消息的接收的 Mdn。
  * 更新并关联不可否认数据库中的记录。
  * 写入 AS2 状态报告的记录。
  * 输出有效负载内容作为 base64 编码。
  * 确定是否需要 Mdn。 根据 AS2 协议，确定应是同步或异步 Mdn。
  * 生成基于 AS2 协议的同步或异步 Mdn。
  * 在 Mdn 上设置的相关标记和属性。

  此操作还执行以下任务时配置：

  * 验证签名。
  * 对消息进行解密。
  * 消息进行解压缩。 
  * 检查并不允许消息 ID 重复项。

本文介绍如何添加 AS2 编码和解码操作到现有逻辑应用。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 从你想要使用 AS2 连接器和触发器启动逻辑应用的工作流逻辑应用。 AS2 连接器提供仅操作，不包含触发器。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* [集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)，具有与你的 Azure 订阅相关联并链接到计划要使用 AS2 连接器的逻辑应用。 在逻辑应用和集成帐户必须位于同一位置或 Azure 区域。

* 在至少两台[贸易合作伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md)，已经定义了集成帐户中使用 AS2 标识限定符。

* 可以使用 AS2 连接器之前，必须创建 AS2[协议](../logic-apps/logic-apps-enterprise-integration-agreements.md)贸易合作伙伴和存储之间的集成帐户中该协议。

* 如果您使用[Azure 密钥保管库](../key-vault/key-vault-overview.md)证书管理，请检查你的保管库密钥，允许**Encrypt**并**解密**操作。 否则，编码和解码操作失败。

  在 Azure 门户中，转到密钥保管库，查看你的保管库密钥**允许的操作**，并确认**Encrypt**并**解密**选择操作。

  ![检查保管库密钥操作](media/logic-apps-enterprise-integration-as2/vault-key-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>为 AS2 消息编码

1. 如果你尚未准备好，在[Azure 门户](https://portal.azure.com)，在逻辑应用设计器中打开逻辑应用。

1. 在设计器中，将添加到逻辑应用的新的操作。 

1. 下**选择操作**和搜索框中，选择**所有**。 在搜索框中，输入"编码 as2"，然后选择以下操作：**编码为 AS2 消息**。

   ![选择"编码为 AS2 消息"](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. 如果没有现有连接到集成帐户，系统会提示要现在创建该连接。 命名你的连接，选择你想要连接，并选择集成帐户**创建**。

   ![创建连接到集成帐户](./media/logic-apps-enterprise-integration-as2/as2-create-connection.png)  
 
1. 现在提供这些属性的信息：

   | 属性 | 描述 |
   |----------|-------------|
   | **AS2-From** | 指定 AS2 协议的消息发送方标识符 |
   | **AS2-To** | 指定 AS2 协议的消息接收方标识符 |
   | **body** | 消息负载 |
   |||

   例如：

   ![消息编码属性](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

<a name="decode"></a>

## <a name="decode-as2-messages"></a>为 AS2 消息解码

1. 如果你尚未准备好，在[Azure 门户](https://portal.azure.com)，在逻辑应用设计器中打开逻辑应用。

1. 在设计器中，将添加到逻辑应用的新的操作。 

1. 下**选择操作**和搜索框中，选择**所有**。 在搜索框中，输入"解码 as2"，然后选择以下操作：**解码 AS2 消息**

   ![选择"解码 AS2 消息"](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. 如果没有现有连接到集成帐户，系统会提示要现在创建该连接。 命名你的连接，选择你想要连接，并选择集成帐户**创建**。

   ![创建连接到集成帐户](./media/logic-apps-enterprise-integration-as2/as2-create-connection.png)  

1. 有关**正文**并**标头**，从以前的触发器或操作输出中选择这些值。

   例如，假设逻辑应用接收的消息通过请求触发器。 从该触发器，可以选择输出。

   ![从请求输出中选择正文和标头](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png) 

## <a name="sample"></a>示例

若要尝试部署完全正常运行的逻辑应用和示例 AS2 方案，请参阅 [AS2 逻辑应用模板和方案](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)。

## <a name="connector-reference"></a>连接器参考

有关技术详细信息，如触发器、 操作和限制，如所述的连接器的 OpenAPI (以前称为 Swagger) 文件，请参阅[连接器的参考页](/connectors/as2/)。

## <a name="next-steps"></a>后续步骤

详细了解[Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
