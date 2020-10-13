---
title: 发送和接收针对 B2B 的 AS2 消息
description: 将 Azure 逻辑应用与 Enterprise Integration Pack 配合使用，以便交换针对 B2B 企业集成方案的 AS2 消息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: 1845de8332287f85e96b4e29c40caa66cb8704d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856664"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>在带有 Enterprise Integration Pack 的 Azure 逻辑应用中交换 AS2 消息以实现 B2B 企业集成

> [!IMPORTANT]
> 不推荐使用原始 AS2 连接器，因此除非需要跟踪功能，否则请改用 **AS2 (v2) ** 连接器。 除跟踪外，v2 连接器提供与原始版本相同的功能，是逻辑应用运行时的固有功能，并且在消息大小、吞吐量和延迟方面有显著的性能改进。 此外，v2 连接器不需要创建与集成帐户的连接。 但要求执行先决条件中所述的操作，确保将集成帐户关联到你计划在其中使用连接器的逻辑应用。

若要在 Azure 逻辑应用中使用 AS2 消息，可以使用 AS2 连接器，此连接器提供支持和管理 AS2 (版本 1.2) 通信的触发器和操作。 例如，若要在传输消息时确保安全性和可靠性，可以使用以下操作：

* [**AS2 编码**操作](#encode)：可以通过消息处置通知 (MDN) 提供加密、数字签名和确认功能，这有助于为不可否认性提供支持。 例如，此操作会应用 AS2/HTTP 标头并执行以下任务（在配置好以后）：

  * 对传出消息进行签名。
  * 加密传出消息。
  * 压缩消息。
  * 在 MIME 标头中传输文件名。

* [**AS2 解码**操作](#decode)：可以通过消息处置通知 (MDN) 提供解密、数字签名和确认功能。 例如，该操作执行以下任务：

  * 处理 AS2/HTTP 标头。
  * 协调收到的 MDN 和原始的出站消息。
  * 更新并关联不可否认性数据库中的记录。
  * 写入 AS2 状态报告的记录。
  * 输出 base64 编码的有效负载内容。
  * 确定是否需要 MDN。 根据 AS2 协议，确定 MDN 应该是同步还是异步。
  * 根据 AS2 协议生成同步或异步 MDN。
  * 在 MDN 上设置关联令牌和属性。

  此操作还会在进行了配置的情况下执行以下任务：

  * 验证签名。
  * 对消息进行解密。
  * 解压缩消息。
  * 检查并禁止消息 ID 重复。

本文介绍如何向现有的逻辑应用添加 AS2 编码和解码操作。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 要从其使用 AS2 连接器的逻辑应用，以及用于启动逻辑应用工作流的触发器。 AS2 连接器只提供操作，不提供触发器。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 一个[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)，该帐户与 Azure 订阅相关联，并已关联到你计划在其中使用 AS2 连接器的逻辑应用。 逻辑应用和集成帐户必须位于同一位置或 Azure 区域。

* 至少两个使用 AS2 标识限定符在集成帐户中定义的[贸易合作伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md)。

* 在使用 AS2 连接器之前，必须在贸易合作伙伴之间制定 AS2 [协议](../logic-apps/logic-apps-enterprise-integration-agreements.md)，并将该协议存储在集成帐户中。

* 如果使用 [Azure Key Vault](../key-vault/general/overview.md) 进行证书管理，请检查保管库密钥是否允许“加密”  和“解密”  操作。 否则，编码和解码操作会失败。

  在 Azure 门户中，转到密钥保管库中的密钥，查看密钥的“允许的操作”  ，确认已选择“加密”  和“解密”  操作，例如：

  ![检查保管库密钥操作](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>为 AS2 消息编码

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 在设计器中，将新操作添加到逻辑应用。

1. 在“选择操作”和搜索框下，选择“全部”   。 在搜索框中输入“as2 编码”，确保选择 AS2 (v2) 操作：**AS2 编码**

   ![选择“AS2 编码”](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. 现在提供以下属性的信息：

   | 属性 | 说明 |
   |----------|-------------|
   | **要编码的消息** | 消息有效负载 |
   | **AS2 发件人** | AS2 协议指定的消息发送方的标识符 |
   | **AS2 收件人** | AS2 协议指定的消息接收方的标识符 |
   |||

   例如：

   ![消息编码属性](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

> [!TIP]
> 如果在发送已签名或加密的消息时遇到问题，请考虑尝试不同的 SHA256 算法格式。 AS2 规范不提供有关 SHA256 格式的任何信息，因此每个提供者都使用其自己的实现或格式。

<a name="decode"></a>

## <a name="decode-as2-messages"></a>为 AS2 消息解码

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 在设计器中，将新操作添加到逻辑应用。

1. 在“选择操作”和搜索框下，选择“全部”   。 在搜索框中输入“as2 解码”，确保选择 AS2 (v2) 操作：**AS2 解码**

   ![选择“AS2 解码”](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. 对于“要编码的消息”和“消息标头”属性，   ，请从以前的触发器或操作输出中选择以下值。

   例如，假定逻辑应用通过“请求”触发器接收消息。 可以选择该触发器的输出。

   ![从请求输出中选择正文和标头](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>示例

若要尝试部署完全正常运行的逻辑应用和示例 AS2 方案，请参阅 [AS2 逻辑应用模板和方案](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)。

## <a name="connector-reference"></a>连接器参考

有关此连接器的更多技术方面的详细信息，例如操作和限制（如此连接器的 Swagger 文件所述），请参阅[连接器的参考页](/connectors/as2/)。 

> [!NOTE]
> 对于[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 中的逻辑应用，此连接器的 ISE 标记版本使用 [ISE 的 B2B 消息限制](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
