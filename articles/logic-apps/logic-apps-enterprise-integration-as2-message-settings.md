---
title: AS2 消息设置
description: 带 Enterprise Integration Pack 的 Azure 逻辑应用中的 AS2 发送和接收设置的参考指南
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ad047a30b901d71604c775e9882b0f242f094638
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "74793027"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>带 Enterprise Integration Pack 的 Azure 逻辑应用中的 AS2 消息设置参考

此参考描述了可设置的属性，这些属性用于指定 AS2 协议处理在贸易合作伙伴之间发送和接收的消息的方式。 根据要与其交换消息的合作伙伴达成的协议来设置这些属性。

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>AS2 接收设置

![选择“接收设置”](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| properties | 必选 | 说明 |
|----------|----------|-------------|
| **重写消息属性** | 否 | 用属性设置重写传入消息的属性。 |
| **应对消息进行签名** | 否 | 指定是否必须对所有传入消息进行数字签名。 如果需要签名，请在“证书”列表中，选择现有的来宾合作伙伴公共证书来验证消息的签名。  如果没有证书，请详细了解如何[添加证书](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 |
| **应加密消息** | 否 | 指定是否必须对所有传入消息进行加密。 未加密的消息会被拒绝。 如果需要加密，请在“证书”列表中，选择现有的宿主合作伙伴专用证书来解密传入的消息。  如果没有证书，请详细了解如何[添加证书](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 |
| **应压缩消息** | 否 | 指定是否必须对所有传入消息进行压缩。 未压缩的消息会被拒绝。 |
| **禁止消息 ID 重复** | 否 | 指定是否允许消息使用重复 ID。 如果不允许重复 ID，请选择检查的间隔天数。 也可选择是否暂停重复项。 |
| **MDN 文本** | 否 | 指定要发送到消息发送方的默认消息处置通知 (MDN)。 |
| **发送 MDN** | 否 | 指定是否为接收的消息发送同步 MDN。  |
| **发送签名的 MDN** | 否 | 指定是否为接收的消息发送签名的 MDN。 如果需要签名，请从“MIC 算法”  列表中选择用于消息签名的算法。 |
| **发送异步 MDN** | 否 | 指定是否异步发送 MDN。 如果选择异步 MDN，请在“URL”  框中指定要将 MDN 发送到的 URL。 |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>AS2 发送设置

![选择“发送设置”](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| properties | 必选 | 说明 |
|----------|----------|-------------|
| **启用消息签名** | 否 | 指定是否必须对所有传出消息进行数字签名。 如果需要签名，请选择以下值： <p>- 在“签名算法”  列表中，选择用于消息签名的算法。 <br>- 在“证书”列表中，选择现有的宿主合作伙伴专用证书，对消息进行签名。  如果没有证书，请详细了解如何[添加证书](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 |
| **启用消息加密** | 否 | 指定是否必须对所有传出消息进行加密。 如果需要加密，请选择以下值： <p>- 在“加密算法”列表中选择来宾合作伙伴公共证书算法，用于加密消息。  <br>- 在“证书”列表中，选择现有的来宾合作伙伴专用证书来解密传出的消息。  如果没有证书，请详细了解如何[添加证书](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 |
| **启用消息压缩** | 否 | 指定是否必须对所有传出消息进行压缩。 |
| **展开 HTTP 标头** | 否 | 将 HTTP `content-type` 标头置于单个行。 |
| **在 MIME 标头中传输文件名** | 否 | 指定是否在 MIME 标头中包含文件名。 |
| **请求 MDN** | 否 | 指定是否接收所有传出消息的消息处置通知 (MDN)。 |
| **请求签名的 MDN** | 否 | 指定是否接收所有传出消息的已签名 MDN。 如果需要签名，请从“MIC 算法”  列表中选择用于消息签名的算法。 |
| **请求异步 MDN** | 否 | 指定是否异步接收 MDN。 如果选择异步 MDN，请在“URL”  框中指定要将 MDN 发送到的 URL。 |
| **启用 NRR** | 否 | 指定是否要求不可否认性接收 (NRR)。 此通信属性提供数据已按址接收到的证据。 |
| **SHA2 算法格式** | 否 | 指定用于对传出 AS2 消息或 MDN 的标头进行签名的 MIC 算法格式 |
||||

## <a name="next-steps"></a>后续步骤

[交换 AS2 消息](../logic-apps/logic-apps-enterprise-integration-as2.md)
