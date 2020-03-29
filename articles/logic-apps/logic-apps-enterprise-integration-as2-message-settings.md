---
title: AS2 消息设置
description: 使用企业集成包在 Azure 逻辑应用中发送和接收设置的 AS2 参考指南
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ad047a30b901d71604c775e9882b0f242f094638
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793027"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>使用企业集成包的 Azure 逻辑应用中的 AS2 消息设置参考

此引用描述您可以设置的属性来指定 AS2 协议如何处理在贸易伙伴之间发送和接收的消息。 基于您与您交换消息的合作伙伴的协议设置这些属性。

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>AS2 接收设置

![选择"接收设置"](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| properties | 必选 | 描述 |
|----------|----------|-------------|
| **替代消息属性** | 否 | 使用属性设置覆盖传入邮件的属性。 |
| **对消息进行签名** | 否 | 指定是否必须对所有传入邮件进行数字签名。 如果需要签名，请从 **"证书"** 列表中选择现有来宾合作伙伴公共证书以验证邮件上的签名。 如果没有证书，可以了解有关[添加证书](../logic-apps/logic-apps-enterprise-integration-certificates.md)的更多信息。 |
| **对消息进行加密** | 否 | 指定是否必须加密所有传入邮件。 未加密的消息会被拒绝。 如果需要加密，请从**证书**列表中选择用于解密传入消息的现有主机合作伙伴专用证书。 如果没有证书，可以了解有关[添加证书](../logic-apps/logic-apps-enterprise-integration-certificates.md)的更多信息。 |
| **对消息进行压缩** | 否 | 指定是否必须压缩所有传入消息。 未压缩的消息会被拒绝。 |
| **禁止邮件 ID 重复** | 否 | 指定是否允许具有重复的号数据交换的邮件。 如果不允许重复的已号文件，请选择支票之间的天数。 您还可以选择是否挂起重复项。 |
| **MDN 文本** | 否 | 指定要发送到邮件发件人的默认邮件处置通知 （MDN）。 |
| **发送 MDN** | 否 | 指定是否为接收的消息发送同步 MN。  |
| **发送已签名的 MDN** | 否 | 指定是否为已接收的消息发送已签名的 MN。 如果需要签名，请从**MIC 算法**列表中选择用于对消息进行签名的算法。 |
| **发送异步 MDN** | 否 | 指定是否异步发送 MDN。 如果在**URL**框中选择异步 MDN，请指定用于发送 MDN 的位置的 URL。 |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>AS2 发送设置

![选择"发送设置"](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| properties | 必选 | 描述 |
|----------|----------|-------------|
| **启用消息签名** | 否 | 指定是否必须对所有传出邮件进行数字签名。 如果需要签名，请选择以下值： <p>- 从**签名算法**列表中，选择用于对消息进行签名的算法。 <br>- 从**证书**列表中，选择用于对消息进行签名的现有主机合作伙伴专用证书。 如果没有证书，可以了解有关[添加证书](../logic-apps/logic-apps-enterprise-integration-certificates.md)的更多信息。 |
| **启用消息加密** | 否 | 指定是否必须加密所有传出邮件。 如果需要加密，请选择以下值： <p>- 从**加密算法**列表中，选择用于加密消息的来宾合作伙伴公共证书算法。 <br>- 从**证书**列表中，选择用于加密传出消息的现有来宾合作伙伴专用证书。 如果没有证书，可以了解有关[添加证书](../logic-apps/logic-apps-enterprise-integration-certificates.md)的更多信息。 |
| **启用消息压缩** | 否 | 指定是否必须压缩所有传出消息。 |
| **展开 HTTP 标头** | 否 | 将 HTTP`content-type`标头放在一行上。 |
| **在 MIME 标头中传输文件名** | 否 | 指定是否将文件名包括在 MIME 标头中。 |
| **请求 MDN** | 否 | 指定是否接收所有传出消息的消息处置通知 （MDN）。 |
| **要求对 MDN 进行签名** | 否 | 指定是否接收所有传出消息的已签名 MN。 如果需要签名，请从**MIC 算法**列表中选择用于对消息进行签名的算法。 |
| **要求发送异步 MDN** | 否 | 指定是否异步接收 MDN。 如果在**URL**框中选择异步 MDN，请指定用于发送 MDN 的位置的 URL。 |
| **启用 NRR** | 否 | 指定是否要求非拒绝接收 （NRR）。 此通信属性提供数据作为寻址接收的证据。 |
| **SHA2 算法格式** | 否 | 指定用于在传出 AS2 消息或 MDN 的标头中签名的 MIC 算法格式 |
||||

## <a name="next-steps"></a>后续步骤

[交换 AS2 消息](../logic-apps/logic-apps-enterprise-integration-as2.md)
