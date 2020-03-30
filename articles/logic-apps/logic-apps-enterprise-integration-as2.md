---
title: 发送和接收 B2B 的 AS2 消息
description: 将 Azure 逻辑应用与企业集成包一起使用，为 B2B 企业集成方案交换 AS2 消息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: 0ce813e91750db3cdfa1e651a68fbb82d593eb32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650550"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>在带有 Enterprise Integration Pack 的 Azure 逻辑应用中交换 AS2 消息以实现 B2B 企业集成

> [!IMPORTANT]
> 正在弃用原始 AS2 连接器，因此请确保改用**AS2 （v2）** 连接器。 此版本提供与原始版本相同的功能，是逻辑应用运行时的本机功能，并在吞吐量和消息大小方面提供了显著的性能改进。 此外，本机 v2 连接器不需要创建与集成帐户的连接。 相反，如先决条件中所述，请确保将集成帐户链接到计划使用连接器的逻辑应用。

要在 Azure 逻辑应用中处理 AS2 消息，可以使用 AS2 连接器，该连接器提供用于管理 AS2 通信的触发器和操作。 例如，为了在传输消息时建立安全性和可靠性，可以使用以下操作：

* [ **AS2 编码**操作](#encode)，用于通过消息处置通知 （MDN） 提供加密、数字签名和确认，这有助于支持非否认。 例如，此操作应用 AS2/HTTP 标头，并在配置时执行这些任务：

  * 对传出消息进行签名。
  * 加密传出消息。
  * 压缩消息。
  * 在 MIME 标头中传输文件名。

* [ **AS2 解码**操作](#decode)，用于通过消息处置通知 （MDN） 提供解密、数字签名和确认。 例如，此操作执行以下任务：

  * 处理 AS2/HTTP 标头。
  * 将收到的 MDN 与原始出站消息进行协调。
  * 更新和关联非否认数据库中的记录。
  * 写入 AS2 状态报告的记录。
  * 将有效负载内容输出为基本编码。
  * 确定是否需要 MN。 根据 AS2 协议，确定 MDN 是同步的还是异步的。
  * 根据 AS2 协议生成同步或异步 MDN。
  * 设置 MDN 上的关联令牌和属性。

  此操作在配置时还会执行以下任务：

  * 验证签名。
  * 解密邮件。
  * 解压缩消息。
  * 检查并禁止邮件 ID 重复。

本文演示如何将 AS2 编码和解码操作添加到现有逻辑应用。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 要从何处使用 AS2 连接器的逻辑应用和启动逻辑应用工作流的触发器。 AS2 连接器仅提供操作，不提供触发器。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 与 Azure 订阅关联的[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)，并链接到计划使用 AS2 连接器的逻辑应用。 逻辑应用和集成帐户必须存在于同一位置或 Azure 区域中。

* 使用 AS2 标识限定符已在集成帐户中定义的至少两个[贸易伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md)。

* 在使用 AS2 连接器之前，您必须在贸易伙伴之间创建 AS2[协议](../logic-apps/logic-apps-enterprise-integration-agreements.md)，并将该协议存储在集成帐户中。

* 如果将[Azure 密钥保管库](../key-vault/key-vault-overview.md)用于证书管理，请检查保管库密钥是否允许**加密**和**解密**操作。 否则，编码和解码操作将失败。

  在 Azure 门户中，转到密钥保管库中的密钥，查看密钥的**允许操作**，并确认已选择**加密**和**解密**操作，例如：

  ![检查保管库密钥操作](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>为 AS2 消息编码

1. 如果尚未在[Azure 门户](https://portal.azure.com)中，请在逻辑应用设计器中打开逻辑应用。

1. 在设计器中，向逻辑应用添加新操作。

1. 在“选择操作”和搜索框下，选择“全部”********。 在搜索框中，输入"as2 编码"，并确保选择 AS2 （v2） 操作 **：AS2 编码**

   ![选择"AS2 编码"](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. 现在提供有关这些属性的信息：

   | properties | 描述 |
   |----------|-------------|
   | **要编码的消息** | 消息有效负载 |
   | **AS2 从** | AS2 协议指定的邮件发件人的标识符 |
   | **AS2 到** | AS2 协议指定的消息接收方的标识符 |
   |||

   例如：

   ![消息编码属性](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

> [!TIP]
> 如果在发送签名或加密消息时遇到问题，请考虑尝试不同的 SHA256 算法格式。 AS2 规范不提供有关 SHA256 格式的任何信息，因此每个提供程序都使用自己的实现或格式。

<a name="decode"></a>

## <a name="decode-as2-messages"></a>为 AS2 消息解码

1. 如果尚未在[Azure 门户](https://portal.azure.com)中，请在逻辑应用设计器中打开逻辑应用。

1. 在设计器中，向逻辑应用添加新操作。

1. 在“选择操作”和搜索框下，选择“全部”********。 在搜索框中，输入"as2 解码"，并确保选择 AS2 （v2） 操作 **：AS2 解码**

   ![选择"AS2 解码"](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. 要**对"消息"进行编码**和**消息标头**属性，请从以前的触发器或操作输出中选择这些值。

   例如，假设您的逻辑应用通过请求触发器接收消息。 您可以从该触发器中选择输出。

   ![从请求输出中选择正文和标头](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>示例

若要尝试部署完全正常运行的逻辑应用和示例 AS2 方案，请参阅 [AS2 逻辑应用模板和方案](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)。

## <a name="connector-reference"></a>连接器参考

有关此连接器的更多技术详细信息（如连接器的 Swagger 文件所述的操作和限制），请参阅[连接器的参考页](https://docs.microsoft.com/connectors/as2/)。 

> [!NOTE]
> 对于[集成服务环境 （ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的逻辑应用，此连接器的原始 ISE 标记版本使用[ISE 消息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
