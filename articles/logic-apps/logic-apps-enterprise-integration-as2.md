---
title: 针对 B2B 发送和接收 AS2 消息
description: 使用 Azure 逻辑应用与 Enterprise Integration Pack 交换 B2B 企业集成方案的 AS2 消息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: 0ce813e91750db3cdfa1e651a68fbb82d593eb32
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650550"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>在带有 Enterprise Integration Pack 的 Azure 逻辑应用中交换 AS2 消息以实现 B2B 企业集成

> [!IMPORTANT]
> 原始 AS2 连接器已被弃用，因此请确保改用**AS2 （v2）** 连接器。 此版本提供的功能与原版本相同，是逻辑应用运行时的本机功能，可在吞吐量和消息大小方面提供显著的性能改进。 此外，本机 v2 连接器不需要创建与集成帐户的连接。 相反，如先决条件中所述，请确保将集成帐户链接到计划使用连接器的逻辑应用。

若要在 Azure 逻辑应用中使用 AS2 消息，可以使用 AS2 连接器，它提供用于管理 AS2 通信的触发器和操作。 例如，若要在传输消息时建立安全性和可靠性，可以使用以下操作：

* [ **AS2 编码**操作](#encode)，用于通过消息处置通知（MDN）提供加密、数字签名和确认，帮助支持不可否认性。 例如，此操作会在配置时应用 AS2/HTTP 标头并执行以下任务：

  * 对传出消息进行签名。
  * 加密传出消息。
  * 压缩消息。
  * 传输 MIME 标头中的文件名。

* [ **AS2 解码**操作](#decode)，用于通过消息处置通知（MDN）提供解密、数字签名和确认。 例如，此操作执行以下任务：

  * 处理 AS2/HTTP 标头。
  * 协调接收的 Mdn 与原始出站消息。
  * 更新并关联不可否认数据库中的记录。
  * 写入 AS2 状态报告的记录。
  * 输出以 base64 编码的负载内容。
  * 确定是否需要 Mdn。 根据 AS2 协议，确定 Mdn 应为同步还是异步。
  * 基于 AS2 协议生成同步或异步 Mdn。
  * 设置 Mdn 上的相关标记和属性。

  此操作还会在配置时执行这些任务：

  * 验证签名。
  * 对消息进行解密。
  * 解压缩消息。
  * 选中并不允许消息 ID 重复项。

本文介绍如何向现有逻辑应用添加 AS2 编码和解码操作。

## <a name="prerequisites"></a>必备条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 要从中使用 AS2 连接器的逻辑应用，以及用于启动逻辑应用工作流的触发器。 AS2 连接器仅提供操作，而不提供触发器。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 与 Azure 订阅关联的[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)，并链接到计划使用 AS2 连接器的逻辑应用。 逻辑应用和集成帐户必须存在于同一位置或 Azure 区域中。

* 已使用 AS2 标识限定符在集成帐户中至少定义了两个[贸易合作伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md)。

* 你必须在你的贸易合作伙伴之间创建 AS2[协议](../logic-apps/logic-apps-enterprise-integration-agreements.md)，并将该协议存储在集成帐户中，然后才能使用 as2 连接器。

* 如果使用[Azure Key Vault](../key-vault/key-vault-overview.md)进行证书管理，请检查保管库密钥是否允许**加密**和**解密**操作。 否则，编码和解码操作将失败。

  在 Azure 门户中转到密钥保管库中的密钥，查看密钥**允许的操作**，并确认已选择 "**加密**和**解密**" 操作，例如：

  ![检查保管库密钥操作](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>为 AS2 消息编码

1. 如果尚未这样做，请在[Azure 门户](https://portal.azure.com)中的逻辑应用设计器中打开逻辑应用。

1. 在设计器中，将新操作添加到逻辑应用。

1. 在“选择操作”和搜索框下，选择“全部”。 在搜索框中，输入 "as2 编码"，并确保选择 AS2 （v2）操作： **As2 编码**

   ![选择 "AS2 编码"](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. 现在提供这些属性的信息：

   | properties | 说明 |
   |----------|-------------|
   | **要编码的消息** | 消息负载 |
   | **AS2 来源** | AS2 协议指定的消息发送方的标识符 |
   | **AS2 到** | AS2 协议指定的消息接收方的标识符 |
   |||

   例如：

   ![消息编码属性](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

> [!TIP]
> 如果在发送已签名或加密的消息时遇到问题，请考虑尝试不同的 SHA256 算法格式。 AS2 规范不提供有关 SHA256 格式的任何信息，因此每个提供程序都使用其自己的实现或格式。

<a name="decode"></a>

## <a name="decode-as2-messages"></a>为 AS2 消息解码

1. 如果尚未这样做，请在[Azure 门户](https://portal.azure.com)中的逻辑应用设计器中打开逻辑应用。

1. 在设计器中，将新操作添加到逻辑应用。

1. 在“选择操作”和搜索框下，选择“全部”。 在搜索框中，输入 "as2 解码"，并确保选择 AS2 （v2）操作： **As2 解码**

   ![选择 "AS2 解码"](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. 对于**要编码的消息**和**消息标头**属性，请从以前的触发器或操作输出中选择这些值。

   例如，假定逻辑应用通过请求触发器接收消息。 你可以从该触发器中选择输出。

   ![从请求输出中选择正文和标头](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>示例

若要尝试部署完全正常运行的逻辑应用和示例 AS2 方案，请参阅 [AS2 逻辑应用模板和方案](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)。

## <a name="connector-reference"></a>连接器参考

有关此连接器的更多技术详细信息（如连接器的 Swagger 文件所述的操作和限制），请参阅[连接器的参考页](https://docs.microsoft.com/connectors/as2/)。 

> [!NOTE]
> 对于[integration service 环境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中的逻辑应用，此连接器的原始 ISE 标记版本会改用[ise 消息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
