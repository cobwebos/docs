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
ms.date: 07/05/2017
ms.author: cjiang
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: 647509ea36e487aca5db661adb3268e845988f78
ms.contentlocale: zh-cn
ms.lasthandoff: 07/06/2017

---
# <a name="troubleshoot-sign-up-issues-for-azure"></a>排查 Azure 注册问题
如果不能注册 Azure，请使用本文中的提示来解决常见问题。 如果在注册期间遇到信用卡问题，请参阅[借记卡或信用卡在注册 Azure 时被拒绝](billing-credit-card-fails-during-azure-sign-up.md)。 如果你有 Azure 帐户但无法登录，请参阅[无法登录以管理 Azure 订阅](billing-cannot-login-subscription.md)。

## <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>“使用卡进行身份验证”部分中的进度条挂起

若要通过卡完成身份验证，则你的浏览器必须允许第三方 cookie。

![注册时使用卡进行身份验证的进度条挂起屏幕截图](./media/billing-troubleshoot-azure-sign-up-issues/identity-verification-hangs.PNG)

使用以下步骤更新浏览器的 cookie 设置。

1. 如果使用的是 Chrome，请转到“设置” > “显示高级设置” > “隐私” > “内容设置”。 取消选中“阻止第三方 cookie 和站点数据”。
2. 如果使用的是 Edge，请转到“设置” > “查看高级设置” > “Cookie”。 选择“不阻止 cookie”。
3. 刷新 Azure 注册页，并检查问题是否得以解决。
4. 如果刷新后未解决此问题，请退出并重新启动你的浏览器，然后重试。

## <a name="credit-card-form-doesnt-support-my-billing-address"></a>信用卡窗体不支持我的帐单地址
你的帐单地址需要位于你在“关于你”部分中选择的国家/地区内。 请确保你选择了正确的国家/地区。

## <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>注册帐户验证过程中没有收到短信或电话
虽然它通常更快，但可能需要花费多达四分钟验证码才会送达。 输入的电话号码仅用于验证，不会存储为帐户的联系号码。

下面是一些其他提示：
* 电话验证过程中不能使用 VOIP 电话号码。
* 再次确认你输入的电话号码，包括下拉菜单中所选择的国家/地区代码。
* 如果你的手机未收到短信 (SMS)，请尝试使用“呼叫我”选项。
* 确保你的手机可以收到从基于美国的号码打来的电话或发来的 SMS 消息。

收到短信或呼叫时，在文本框中输入你收到的代码。

## <a name="credit-card-declined-or-not-accepted"></a>信用卡被拒绝或不被接受
虚拟或预付信用卡或借记卡不能作为 Azure 订阅有效的付款选项。 若要查看可能导致你的卡被拒绝的其他原因，请参阅[借记卡或信用卡在注册 Azure 时被拒绝](billing-credit-card-fails-during-azure-sign-up.md)。

## <a name="free-trial-is-not-available"></a>“免费试用版不可用”
是否曾经使用过 Azure 订阅？ Azure 使用条款协议仅向 Azure 新用户授予免费试用版激活权限。 如果已拥有任何其他类型的 Azure 订阅，则无法激活免费试用版。 请考虑注册[即用即付订阅](https://azure.microsoft.com/offers/ms-azr-0003p/)。

## <a name="i-saw-a-charge-on-my-free-trial-account"></a>我看到我的免费试用帐户上产生了费用
在注册后，你可能会看到你的信用卡帐户上存在少量验证费用，这在 3 到 5 天内会被删除。 如果你为如何管理成本而担忧，请阅读有关[防止意外成本](https://docs.microsoft.com/azure/billing/billing-getting-started)的详细信息。

## <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>无法激活 MSDN、BizSpark、BizSparkPlus 或 MPN 等 Azure 权益计划
请确保使用正确的登录凭据。 然后检查权益计划，以确保你符合资格。 

* MSDN
  * 在 [MSDN 帐户页](https://msdn.microsoft.com/subscriptions/manage/default.aspx)中验证资格状态。
  * 如果无法验证资格状态，请联系 [MSDN 订阅客服中心](https://msdn.microsoft.com/subscriptions/contactus.aspx)
* BizSpark
  * 登录到 [BizSpark 门户](https://www.microsoft.com/bizspark/default.aspx#start-two)，然后验证 BizSpark 和 BizSpark Plus 的资格状态。
  * 如果无法验证你的状态，可以[在 BizSpark 论坛上获取帮助](http://aka.ms/bzforums)。
* MPN
  * 登录到 [MPN 门户](https://mspartner.microsoft.com/en/us/Pages/Locale.aspx)并验证资格状态。 如果拥有相应的[云平台能力](https://mspartner.microsoft.com/en/us/pages/membership/cloud-platform-competency.aspx)，可能会符合其他权益的资格。
  * 如果无法验证资格状态，请联系 [MPN 支持](https://mspartner.microsoft.com/en/us/Pages/Support/Premium/contact-support.aspx)。

## <a name="cant-activate-new-azure-in-open-subscription"></a>无法激活新的 Azure 开放许可订阅
若要创建 Azure 开放许可订阅，必须具备有效的在线服务激活 (OSA) 密钥以及至少一个与之关联的 Azure 开放许可令牌。 如果没有 OSA 密钥，请联系 [Microsoft Pinpoint](http://pinpoint.microsoft.com/) 中列出的其中一个 Microsoft 合作伙伴。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。
如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

