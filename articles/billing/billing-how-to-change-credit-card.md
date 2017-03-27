---
title: "更改用于 Azure 的信用卡 | Microsoft Docs"
description: "介绍如何更改用于支付 Azure 订阅的信用卡"
services: 
documentationcenter: 
author: genlin
manager: narmstr
editor: 
tags: billing
ms.assetid: 15252ced-1841-4a66-ae79-2e58af1d3370
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 09c18ca6a967c2930ddd2b16d37f0bc606712ea1
ms.openlocfilehash: 2a4751351561728024417d50aeefba0e5b14a438


---
# <a name="change-the-credit-card-used-to-pay-for-an-azure-subscription"></a>更改用于支付 Azure 订阅的信用卡

本文介绍如何添加、更改或删除与 Azure 帐户关联的信用卡，以及如何付款。

<a id="addcard"></a>
## <a name="add-a-credit-card-as-a-payment-method"></a>添加信用卡作为付款方式

请按照下列步骤将信用卡号添加到帐户。

1. 以帐户管理员身份登录到 [Azure 帐户中心](https://account.windowsazure.com/Subscriptions)。
2. 在“单击订阅以查看详细信息和使用情况”下，选择要添加付款方式的订阅。
3. 在页面右侧，选择“管理付款方式”。
4. 在“管理付款方式”页上，选择“+”添加信用卡。
5. 选择信用卡类型，并提供全部所需的信息。
6. 选择“下一步”返回“管理付款方式”页。 如果添加的信用卡有效，它将添加到可用付款方式列表。

> [!NOTE]
>如果提交信用卡信息之后收到错误消息，请参阅[借记卡或信用卡被拒绝或不被接受](billing-credit-card-fails-during-azure-sign-up.md)一文。
>
>

## <a name="edit-payment-information-for-an-existing-credit-card"></a>编辑现有信用卡的付款信息

按这些步骤验证付款方式，并更新现有信用卡的详细信息。

1. 以帐户管理员身份登录到 [Azure 帐户中心](https://account.windowsazure.com/Subscriptions)。

   > [!NOTE]
   > 只有帐户管理员有权访问帐户中心。 有关帐户管理员的详细信息，请参阅[如何添加或更改 Azure 管理员角色](billing-add-change-azure-subscription-administrator.md)。
   >
   >
2. 在“单击订阅以查看详细信息和使用情况”下面，选择关联到信用卡的订阅。</br> ![selectsub](./media/billing-how-to-change-credit-card/selectsub.png)
3. 在页面右侧，选择“管理付款方式”。</br> ![changesub](./media/billing-how-to-change-credit-card/changesub_new.png)

  你将看到列出了当前付款方式。 选择它以验证所需信息。

  **如何更新现有信用卡的详细信息**

  如果更换了信用卡，但号码保持不变，则只需更新现有信用卡的详细信息（例如失效日期）即可。 但是，如果信用卡号码由于卡丢失、失窃或过期而更改，则需要[将信用卡添加到帐户](#addcard)。 这是因为信用卡号码更改时，卡片上的 CVV 安全代码也会更改。

4. 在“管理付款方式”页上，选择信用卡号旁边的“编辑”。</br> ![changesub](./media/billing-how-to-change-credit-card/editcard_new.png)
5. 在“编辑”页上，确认选择了正确的信用卡类型和卡号。
6. 对信用卡详细信息进行所需的必要更改，然后选择“下一步”返回“选择付款方式”页。

## <a name="change-the-credit-card-that-you-use-to-pay-an-azure-bill"></a>更改用于支付 Azure 帐单的信用卡

1. 以帐户管理员身份登录到 [Azure 帐户中心](https://account.windowsazure.com/Subscriptions)。
2. 在“单击订阅以查看详细信息和使用情况”下面，选择要更新付款方式的订阅。
3. 在页面右侧，选择“管理付款方式”。
4. 在“管理付款方式”页上，单击“使用其他”以选择想要使用的信用卡。

## <a name="remove-a-credit-card-from-the-account"></a>从帐户中删除信用卡
1. 以帐户管理员身份登录到 [Azure 帐户中心](https://account.windowsazure.com/Subscriptions)。
2. 在“单击订阅以查看详细信息和使用情况”下面，选择要更新付款方式的订阅。
3. 在页面右侧，选择“管理付款方式”。
4. 在“管理付款方式”页上，单击“删除”以选择想要删除的信用卡。

   > [!NOTE]
   > 如果信用卡与其他处于活动状态的 Microsoft 订阅相关联，则不能删除信用卡。 如果收到一条提示“该卡正在使用”的错误消息，请确保取消该卡与所有处于活动状态的 Microsoft 订阅的关联。
   >
   >

##  <a name="how-to-make-payments"></a>如何付款

如果已将信用卡或借记卡设置为付款方式，则将自动进行付款。
如果采用[发票付款方式](https://azure.microsoft.com/pricing/invoicing/)，可将付款寄送到发票底部列出的地点。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。



<!--HONumber=Feb17_HO2-->


