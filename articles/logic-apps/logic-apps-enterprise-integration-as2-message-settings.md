---
title: AS2 消息设置
description: Azure 逻辑应用中 AS2 发送和接收设置与 Enterprise Integration Pack 的参考指南
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ad047a30b901d71604c775e9882b0f242f094638
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793027"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Azure 逻辑应用中的 AS2 消息设置参考 Enterprise Integration Pack

此参考描述了可设置的属性，这些属性用于指定 AS2 协议处理在贸易合作伙伴之间发送和接收的消息的方式。 根据与你交换消息的合作伙伴的协议，设置这些属性。

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>AS2 接收设置

![选择 "接收设置"](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| properties | 需要 | 描述 |
|----------|----------|-------------|
| **替代消息属性** | No | 用属性设置替代传入消息的属性。 |
| **应对消息进行签名** | No | 指定是否必须对所有传入消息进行数字签名。 如果需要签名，请在 "**证书**" 列表中，选择现有的来宾合作伙伴公共证书来验证消息的签名。 如果没有证书，请详细了解如何[添加证书](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 |
| **应加密消息** | No | 指定是否必须加密所有传入消息。 未加密的消息会被拒绝。 如果需要加密，请在 "**证书**" 列表中，选择用于对传入消息进行解密的现有主机伙伴私有证书。 如果没有证书，请详细了解如何[添加证书](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 |
| **应压缩消息** | No | 指定是否必须压缩所有传入消息。 未压缩的消息会被拒绝。 |
| **禁止消息 ID 重复项** | No | 指定是否允许具有重复 Id 的消息。 如果您不允许重复的 Id，请选择检查的间隔天数。 你还可以选择是否暂停重复项。 |
| **MDN 文本** | No | 指定要发送到消息发送方的默认消息处置通知（MDN）。 |
| **发送 MDN** | No | 指定是否为接收的消息发送同步 Mdn。  |
| **发送签名的 MDN** | No | 指定是否为接收的消息发送已签名的 Mdn。 如果需要签名，请从 " **MIC 算法**" 列表中选择用于对消息进行签名的算法。 |
| **发送异步 MDN** | No | 指定是否异步发送 Mdn。 如果选择 "异步 Mdn"，请在 " **url** " 框中指定要将 mdn 发送到的 url。 |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>AS2 发送设置

![选择 "发送设置"](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| properties | 需要 | 描述 |
|----------|----------|-------------|
| **启用消息签名** | No | 指定是否必须对所有传出消息进行数字签名。 如果需要签名，请选择以下值： <p>-从 "**签名算法**" 列表中，选择要用于对消息进行签名的算法。 <br>-从 "**证书**" 列表中，选择一个现有的主机伙伴私有证书以对消息进行签名。 如果没有证书，请详细了解如何[添加证书](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 |
| **启用消息加密** | No | 指定是否必须加密所有传出消息。 如果需要加密，请选择以下值： <p>-从 "**加密算法**" 列表中，选择要用于加密消息的来宾合作伙伴公共证书算法。 <br>-从 "**证书**" 列表中，选择现有的来宾合作伙伴私有证书以对传出消息进行加密。 如果没有证书，请详细了解如何[添加证书](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 |
| **启用消息压缩** | No | 指定是否必须压缩所有传出消息。 |
| **展开 HTTP 标头** | No | 将 HTTP `content-type` 标头置于单个行。 |
| **在 MIME 标头中传输文件名** | No | 指定是否在 MIME 标头中包含文件名。 |
| **请求 MDN** | No | 指定是否接收所有传出消息的消息处置通知（Mdn）。 |
| **请求签名 MDN** | No | 指定是否接收所有传出消息的已签名 Mdn。 如果需要签名，请从 " **MIC 算法**" 列表中选择用于对消息进行签名的算法。 |
| **请求异步 MDN** | No | 指定是否异步接收 Mdn。 如果选择 "异步 Mdn"，请在 " **url** " 框中指定要将 mdn 发送到的 url。 |
| **启用 NRR** | No | 指定是否要求不可否认接收（NRR）。 此通信属性提供按寻址接收的数据的证据。 |
| **SHA2 算法格式** | No | 指定用于对传出 AS2 消息或 MDN 的标头进行签名的 MIC 算法格式 |
||||

## <a name="next-steps"></a>后续步骤

[交换 AS2 消息](../logic-apps/logic-apps-enterprise-integration-as2.md)
