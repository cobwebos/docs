<properties
	pageTitle="排查 Azure 注册问题 | Microsoft Azure"
	description="对注册 Azure 时可能发生的问题的常见原因进行故障排除。"
	services="billing"
	documentationCenter=""
	authors="jiangchen79"
	manager="felixwu"
	editor=""
	tags="top-support-issue"/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/30/2016"
	ms.author="cjiang"/>

# 排查 Azure 注册问题
本文针对注册 Azure 时可能发生的问题，帮助你对常见的原因进行故障排除。

> [AZURE.NOTE] 如果你对本文中的任何点需要更多帮助，可以联系 [MSDN Azure 和堆栈溢出论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。或者，你也可以在 [Azure 支持站点](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409)上提出 Azure 支持事件。有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题](https://azure.microsoft.com/support/faq/)。

## 我在输入注册信息时出现错误。
帐户个人资料信息是否正确？ 
你的个人资料详细信息会用来生成帐单，因此务必输入正确的信息。不正确的字段示例如下：
- 不正确的名或姓。
- 无效的电话号码。
- 无效的地址。
- 使用非标准字符。

建议
- 若要成功注册，请输入正确的信息。

## 在尝试注册时，我在帐户验证期间未收到短信或接到来电。
- 请确认你的电话号码可以接收短信。
- 再次确认你输入的电话号码，包括下拉菜单中的国家/地区代码选择。
- 请确保你的电话可以接收短信 (SMS)（如果使用“发送短信”）或接听来电（如果选择“呼叫我”替代项）。
- 如果使用手机，请确保手机连接能力良好。
- 如果选择“发送短信”，最多等候 4 分钟，消息传送系统就会将文本代码发送给你。
- 一旦收到短信，请在文本框中插入代码，然后单击“验证”按钮以继续。

建议：
- 如果你的电话未收到短信 (SMS)，请使用“呼叫我”替代验证方法。
- 如果使用短信和“呼叫我”方法的电话验证步骤均失败，请使用其他电话号码。
- 电话验证过程中不能使用 VOIP 电话号码。

**注意：**你可以在稍后更新[个人资料信息](https://account.windowsazure.com/Profile)，以更改你的首选电话号码。

## 我的信用卡不被接受。
请确保注册时使用的付款方式是 Azure 激活或付款所支持的方式。
- 不接受虚拟和预付信用卡/借记卡。
- 根据帐户所属的国家/地区，接受的信用卡/借记卡提供商会有所不同。

建议：
- 有关使用信用卡或借记卡的注册问题常见原因，请参阅[当我尝试注册 Azure 时，我的信用卡不被接受](billing-credit-card-fails-during-azure-sign-up.md)一文。

## 我无法激活 MSDN、BizSpark、BizSparkPlus 或 MPN 之类的 Azure 权益计划。
检查你的资格：
通过权益计划渠道验证你是否符合所选计划的资格：
- MSDN：
  - 在 [MSDN 帐户页](https://msdn.microsoft.com/subscriptions/manage/default.aspx)中验证你的资格状态。
  - 如果你无法验证资格状态，请联系 [MSDN 订阅客服中心](https://msdn.microsoft.com/subscriptions/contactus.aspx)
- MPN
  - 登录到 [MPN 门户](https://mspartner.microsoft.com/en/us/Pages/Locale.aspx)并验证你的资格状态。如果你有相应的[云平台能力](https://mspartner.microsoft.com/en/us/pages/membership/cloud-platform-competency.aspx)，可能符合其他权益的资格。
  - 如果你无法验证资格状态，请联系 [MPN 支持](https://mspartner.microsoft.com/en/us/Pages/Support/Premium/contact-support.aspx)。
- Bizpark：
  - 登录到 [BizSpark 门户](https://www.microsoft.com/bizspark/default.aspx#start-two)，然后验证 BizSpark 和 BizSpark Plus 的资格状态。
  如果你无法验证资格状态，请向 [BizSpark 团队](mailto:bizspark@microsoft.com?subject=BizSpark%20Support&body=Thank%20you%20for%20contacting%20BizSpark.%20Please%20provide%20as%20much%20of%20the%20following%20information%20as%20possible,%20as%20it%20will%20help%20expedite%20our%20response%20to%20you.%0aContact%20name:%0aStartup%20name:%0aMicrosoft%20Account/Live%20ID:%0aSpecific%20description%20of%20issue%20experienced%20or%20question:%0a%0aThank%20you,%0a%0aThe%20BizSpark%20Team)发送电子邮件，以联系 BizSpark 支持

建议：
- 如果你尝试激活新的权益订阅，但在注册体验期间遇到任何错误，请确认你已在 [Azure 订阅页](http://account.windowsazure.com/Subscriptions)完成订阅设置。可能需要几分钟，订阅才会显示为活动状态。当订阅激活之后，你会收到电子邮件。如果你的订阅状态持续挂起超过四分钟，请联系 [Azure 支持](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409)以获得帮助。

## 我无法激活新的 Azure 开放许可订阅。
必须具备有效的 OSA 密钥以及至少一个与之关联的 Azure 开放许可令牌，才能激活新的 Azure 开放许可订阅。

建议：
- 如果没有 OSA 密钥，请联系 [Microsoft Pinpoint](http://pinpoint.microsoft.com/) 中列出的其中一个 Microsoft 合作伙伴。

## 我不能激活 Azure 免费试用版。
你曾经使用过 Azure 订阅吗？ Azure 使用条款协议限制任何从未使用过 Azure 的用户只能激活免费试用版一次。如果你有任何其他类型的 Azure 订阅，你将无法激活免费试用版。

建议：
- 如果你以前激活过 Azure 订阅，以至于免费试用版激活失败，请考虑购买即用即付订阅。你也可能有资格获得权益产品/服务。若要了解详细信息，请参阅 [Microsoft Azure 产品/服务详细信息页](https://azure.microsoft.com/support/legal/offer-details/)，而非 Azure 免费试用版计划。请注意，权益计划需要特定的先决条件。

<!---HONumber=Mooncake_0801_2016-->