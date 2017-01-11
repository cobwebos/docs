---
title: "为 Microsoft Azure 订阅设置计费警报 | Microsoft Docs"
description: "介绍如何对 Azure 帐单设置警报，以免帐单出乎你的意料。"
services: 
documentationcenter: 
author: vikdesai
manager: mbaldwin
editor: 
tags: billing
ms.assetid: 9b7b3eeb-cd9d-4690-86a3-51b1e2a8974f
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: vikdesai
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: ce21f881d95b369073168d205c315d74de9f24ef


---
# <a name="set-up-billing-alerts-for-your-microsoft-azure-subscriptions"></a>为您的 Microsoft Azure 订阅设置帐单警报
是否关注 Azure 订阅每个月花费多少钱？ 如果用户是 Azure 订阅的帐户管理员，可以使用 Azure 计费警报服务来创建自定义的计费警报，以便监控和管理 Azure 帐户的计费活动。

此服务为预览版服务，因此首先要做的事就是注册它。 访问 Azure 帐户管理门户中的[“预览版功能”页](https://account.windowsazure.com/PreviewFeatures)来启用此功能。

## <a name="set-the-alert-threshold-and-email-recipients"></a>设置警报阈值和电子邮件收件人
收到已为订阅启用计费服务的确认电子邮件后，请访问帐户门户中的[订阅页面](https://account.windowsazure.com/Subscriptions)。 单击想要监控的订阅，然后单击“警报”。

![][Image1]

接下来，单击“添加警报”创建第一个警报 - 对于每个订阅，总共可以设置五个帐单警报，每个警报有一个不同的阈值和最多两个电子邮件收件人。

![][Image2]

添加警报时，你需要为其指定唯一的名称，选择费用阈值，并选择要向其发送警报的电子邮件地址。 设置阈值时，可以从“警报条件”列表中选择“帐单合计”或“资金信用”。 对于帐单合计，订阅费用超出阈值时会发送警报。 对于资金信用，当其降至限制以下时会发送警报。 资金信用通常适用于免费试用版以及与 MSDN 帐户关联的订阅。

![][Image3]

Azure 支持任何电子邮件地址并且不验证电子邮件地址是否可正常使用，因此请仔细检查以避免拼写错误。

## <a name="check-on-your-alerts"></a>检查警报
在设置警报后，帐户中心会列出它们并显示你还可以设置多少警报。 对于每个警报，你可以看到其发送日期和时间，是针对帐单合计的警报还是针对资金信用的警报，以及你设置的限制。 日期和时间格式为 24 小时制通用协调时间 (UTC)，日期为 yyyy-mm-dd 格式。 单击列表中某个警报的加号可对其进行编辑，单击垃圾桶可将其删除。

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png



<!--HONumber=Dec16_HO2-->


