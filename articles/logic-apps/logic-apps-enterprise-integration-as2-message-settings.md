---
title: AS2 消息设置-Azure 逻辑应用
description: 参考指南适用于 AS2 发送和接收 Azure 逻辑应用使用 Enterprise Integration Pack 中的设置
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ead92094b9af1dff56ff68e1ff58a3a4cdd9dca5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "63769446"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>在 Azure 逻辑应用使用 Enterprise Integration Pack 中的 AS2 消息设置的参考

此参考介绍可以设置用于指定 AS2 协议如何处理发送和接收贸易合作伙伴之间消息的属性。 设置这些属性根据与其交换消息的合作伙伴达成的协议。

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>AS2 接收设置

![选择"接收设置"](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| 属性 | 需要 | 描述 |
|----------|----------|-------------|
| **替代消息属性** | 否 | 属性设置将覆盖对传入的消息的属性。 |
| **应对消息进行签名** | 否 | 指定是否必须进行数字签名的所有传入消息。 如果需要签名，从**证书**列表中，选择现有来宾合作伙伴公共证书用于验证消息签名。 如果您没有证书，详细了解[将证书添加](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 |
| **应对消息进行加密** | 否 | 指定是否必须加密所有传入的消息。 未加密的消息会被拒绝。 如果需要加密，从**证书**列表中，选择现有主机合作伙伴私有证书用于解密传入消息。 如果您没有证书，详细了解[将证书添加](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 |
| **应对消息进行压缩** | 否 | 指定是否必须压缩所有传入的消息。 未压缩的消息会被拒绝。 |
| **不允许消息 ID 重复项** | 否 | 指定是否允许具有重复 Id 的消息。 如果不允许重复的 Id，选择的检查之间的天数。 此外可以选择是否挂起重复项。 |
| **MDN 文本** | 否 | 指定默认消息处置通知 (MDN) 所需发送到邮件发件人。 |
| **发送 MDN** | 否 | 指定是否要将为接收的消息的同步 Mdn 发送。  |
| **发送已签名的 MDN** | 否 | 指定是否发送已签名的 Mdn 的接收的消息。 如果需要签名，从**MIC 算法**列表中，选择要使用的消息进行签名的算法。 |
| **发送异步 MDN** | 否 | 指定是否以异步方式发送 Mdn。 如果在选择异步 Mdn **URL**框中，指定将 Mdn 发送到的位置的 URL。 |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>AS2 发送设置

![选择"发送设置"](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| 属性 | 需要 | 描述 |
|----------|----------|-------------|
| **启用消息签名** | 否 | 指定是否必须进行数字签名所有传出消息。 如果需要签名，请选择这些值： <p>-从**签名算法**列表中，选择要使用的消息进行签名的算法。 <br>-从**证书**列表中，选择现有主机合作伙伴私有证书的消息进行签名。 如果您没有证书，详细了解[将证书添加](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 |
| **启用消息加密** | 否 | 指定是否必须加密所有传出消息。 如果需要加密，请选择这些值： <p>-从**加密算法**列表中，选择要用于加密消息的来宾合作伙伴公共证书算法。 <br>-从**证书**列表中，选择现有来宾合作伙伴私有证书传出消息进行加密。 如果您没有证书，详细了解[将证书添加](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 |
| **启用消息压缩** | 否 | 指定是否必须压缩所有传出消息。 |
| **展开 HTTP 标头** | 否 | 将 HTTP`content-type`标头展开为单行。 |
| **传输 MIME 标头中的文件名称** | 否 | 指定是否在 MIME 标头中包含的文件的名称。 |
| **请求 MDN** | 否 | 指定是否为所有传出消息中接收消息处置通知 (Mdn)。 |
| **请求已签名的 MDN** | 否 | 指定是否要接收的所有传出消息已签名的 Mdn。 如果需要签名，从**MIC 算法**列表中，选择要使用的消息进行签名的算法。 |
| **请求异步 MDN** | 否 | 指定是否以异步方式接收 Mdn。 如果在选择异步 Mdn **URL**框中，指定将 Mdn 发送到的位置的 URL。 |
| **启用 NRR** | 否 | 指定是否要求不可否认接收 (NRR)。 此通信属性提供的证据，按址接收该数据。 |
| **SHA2 算法格式** | 否 | 指定要用于签名传出 AS2 消息或 MDN 的标头中的 MIC 算法格式 |
||||

## <a name="next-steps"></a>后续步骤

[交换 AS2 消息](../logic-apps/logic-apps-enterprise-integration-as2.md)
