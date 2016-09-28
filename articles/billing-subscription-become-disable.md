<properties
	pageTitle="如果 Azure 订阅被禁用，该怎么办？| Azure"
	description="介绍 Azure 订阅被禁用的原因以及相关解决方案"
	services=""
	documentationCenter=""
	authors="genlin"
	manager="msmbaldwin"
	editor=""
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.date="07/27/2016"
	wacn.date=""/>

# 如果 Azure 订阅被禁用，该怎么办？

如果 Azure 订阅被禁用，可通过本文所述步骤来重新启用它。禁用 Azure 订阅可能是出于以下原因：

## 你已达到支出限制

为了防止因你的使用量超出所含的产品/服务量而产生意外费用，我们引入了[支出限制](/pricing/spending-limits/)功能。当你用完产品/服务中包含的月度金额时，我们将在该帐单月的剩余时间内禁用你的服务。可以选择移除支出限制，以避免这种情况，并[为 Microsoft Azure 订阅设置帐单警报](/documentation/articles/billing-set-up-alerts/)，以便监视和管理 Azure 帐户的计费活动。

以下是移除支出限制的方法：

1. 登录到 [Azure 帐户中心](https://account.windowsazure.cn/Home/Index)。

2. 单击“订阅”。

3. 选择一个订阅。

4. 如果出现“订阅已达到支出限制并已被禁用以免产生费用”消息，请单击该消息。
	
	也可单击“订阅状态”区域中的“移除支出限制”。
	
5. 选择适合你的以下选项之一：

|选项|效果|
|------|------|
|永久性移除支出限制|移除支出限制，使之在下次计费期间开始时不会自动打开。|
|移除当前计费期间的支出限制|移除支出限制，但它会在下次计费期间开始时自动重新启用。|

>[AZURE.NOTE] 如果使用的是“免费试用”订阅且移除了支出限制，则在免费试用结束时会将订阅转为“即用即付”。

## 你的帐单已过期

全额支付欠款。为此，请参阅文章：[为什么会收到 Azure 订阅欠款到期未付的通知？](/documentation/articles/billing-azure-subscription-past-due-balance/#what-can-you-do-to-resolve-the-issue)

## 帐单金额超出了信用卡限制

若要解决此问题，请使用下列方法之一：

- [切换到其他信用卡](/documentation/articles/billing-how-to-change-credit-card/)。
- 如果用户是企业，则可[切换到发票付款方式](/pricing/invoicing/)。

## 如何重新启用订阅

若要重新启用已禁用的订阅，必须创建支持票证：登录到 [Azure 门户](https://portal.azure.com/)，选择“帮助 + 支持”>“新建支持请求”。

![helpandsupportbutton](./media/billing-how-to-create-billing-support-ticket/helpandsupport.png)

如果无法访问门户，请从 Azure 支持网站或 Azure 帐户中心提交支持请求：

 * 若要从 [Azure 支持网站](https://azure.microsoft.com/support/options/)提交支持票证，请单击“获取支持”。
 * 若要从 [Azure 帐户中心](https://account.windowsazure.com/Subscriptions)提交支持票证，请选择一个订阅，然后单击“联系 Microsoft 支持”。

有关详细信息，请参阅[如何创建针对 Azure 计费和订阅问题的支持票证](/documentation/articles/billing-how-to-create-billing-support-ticket/)。

<!---HONumber=Mooncake_0919_2016-->