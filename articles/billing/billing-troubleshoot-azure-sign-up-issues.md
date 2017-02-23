---
title: "排查 Azure 注册问题 | Microsoft Docs"
description: "说明如何解决某些常见的 Azure 注册问题。"
services: 
documentationcenter: 
author: JiangChen79
manager: adpick
editor: 
tags: billing,top-support-issue
ms.assetid: a0907da1-cb2d-41d1-a97f-43841fabe355
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: f058194302949d1f5ba7d86e9a693ba229bdc9a2
ms.openlocfilehash: b913fd0d944568e422ffcf86791910a746367add


---
# <a name="i-cant-sign-up-for-azure"></a>不能注册 Azure
如果无法注册 Azure，可以通过几种方法来检查并解决此问题。

## <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>“使用卡进行身份验证”部分中的进度条挂起

在 Azure 注册过程中，有一个称为“使用卡进行身份验证”的部分。 如果进度条挂起：

![注册时使用卡进行身份验证的进度条挂起屏幕截图](./media/billing-troubleshoot-azure-sign-up-issues/identity-verification-hangs.PNG)

当你的浏览器阻止第三方 cookie 时会出现此问题。

### <a name="suggestion"></a>建议

1. 在浏览器设置中允许第三方 cookie。
  * 在 Edge 中，转到“设置”->“查看高级设置”->“Cookie”，选择“不阻止 cookie”。
  * 在 Chrome 中，转到“设置”->“显示高级设置”->“隐私”->“内容设置”，取消选中“阻止第三方 cookie 和站点数据”。
2. 刷新 Azure 注册页，并检查问题是否得以解决。
3. 如果刷新后未解决此问题，请退出并重新启动你的浏览器，然后重试。

## <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>注册帐户验证过程中没有收到短信或电话
* 可能需要最多四分钟来完成传递文字代码。
* 请确认你的电话号码可以接收短信。
* 再次确认你输入的电话号码，包括下拉菜单中的国家/地区代码选择。
* 请确保你的电话可以接收短信 (SMS)（如果使用“发送短信”）或接听来电（如果选择“呼叫我”替代项）。
* 收到短信后，请在文本框中插入代码，然后单击“验证”按钮以继续。

### <a name="suggestions"></a>建议
* 如果你的电话未收到短信 (SMS)，请使用“呼叫我”替代验证方法。
* 如果使用短信和“呼叫我”方法的电话验证步骤均失败，请使用其他电话号码。
* 电话验证过程中不能使用 VOIP 电话号码。

## <a name="credit-card-declined-or-not-accepted"></a>信用卡被拒绝或不被接受
请确保注册时使用的付款方式是受支持的付款方式。 还可以了解有关为何[借记卡或信用卡在注册 Azure 时被拒绝](billing-credit-card-fails-during-azure-sign-up.md)的详细信息。

## <a name="free-trial-is-not-available"></a>“免费试用版不可用”
是否曾经使用过 Azure 订阅？ Azure 使用条款协议仅向 Azure 新用户授予免费试用版激活权限。 如果已拥有任何其他类型的 Azure 订阅，则无法激活免费试用版。

### <a name="suggestion"></a>建议
* 请考虑注册[即用即付订阅](https://azure.microsoft.com/offers/ms-azr-0003p/)。

## <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>无法激活 MSDN、BizSpark、BizSparkPlus 或 MPN 等 Azure 权益计划
确保使用正确的登录凭据并通过权益计划渠道验证是否符合所选计划的资格：

* MSDN
  * 在 [MSDN 帐户页](https://msdn.microsoft.com/subscriptions/manage/default.aspx)中验证资格状态。
  * 如果无法验证资格状态，请联系 [MSDN 订阅客服中心](https://msdn.microsoft.com/subscriptions/contactus.aspx)
* MPN
  * 登录到 [MPN 门户](https://mspartner.microsoft.com/en/us/Pages/Locale.aspx)并验证资格状态。 如果拥有相应的[云平台能力](https://mspartner.microsoft.com/en/us/pages/membership/cloud-platform-competency.aspx)，可能会符合其他权益的资格。
  * 如果无法验证资格状态，请联系 [MPN 支持](https://mspartner.microsoft.com/en/us/Pages/Support/Premium/contact-support.aspx)。
* BizSpark
  * 登录到 [BizSpark 门户](https://www.microsoft.com/bizspark/default.aspx#start-two)，然后验证 BizSpark 和 BizSpark Plus 的资格状态。
  * 如果无法验证资格状态，请[联系 BizSpark 团队](mailto:bizspark@microsoft.com?subject=BizSpark%20Support&body=Thank%20you%20for%20contacting%20BizSpark.%20Please%20provide%20as%20much%20of%20the%20following%20information%20as%20possible,%20as%20it%20will%20help%20expedite%20our%20response%20to%20you.%0aContact%20name:%0aStartup%20name:%0aMicrosoft%20Account/Live%20ID:%0aSpecific%20description%20of%20issue%20experienced%20or%20question:%0a%0aThank%20you,%0a%0aThe%20BizSpark%20Team)

## <a name="cant-activate-new-azure-in-open-subscription"></a>无法激活新的 Azure 开放许可订阅
必须具备有效的 OSA 密钥以及至少一个与之关联的 Azure 开放许可令牌，才能创建新的 Azure 开放许可订阅。

### <a name="suggestion"></a>建议
如果没有 OSA 密钥，请联系 [Microsoft Pinpoint](http://pinpoint.microsoft.com/) 中列出的其中一个 Microsoft 合作伙伴。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。
如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。



<!--HONumber=Feb17_HO2-->


