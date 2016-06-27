<properties
	pageTitle="了解 Azure 应用商店收费 | Azure"
	description="说明如何了解与应用商店订单相关的费用。"
	services="billing"
	documentationCenter=""
	authors="jiangchen79"
	manager="felixwu"
	editor=""
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.date="05/10/2016"
	wacn.date=""/>

# 了解 Azure 应用商店收费
本文概述了如何了解与应用商店订单相关的收费。在查看帐单时要注意的是不同的定价类型（仅按基本价格定价、仅按使用情况定价，以及按基本价格 + 使用情况定价）。请注意，你下的每个应用商店订单都会有自己的每月计费周期。下面将详细介绍这些主题。

## 查看你的应用商店帐单
登录 [Azure 帐户门户](https://account.windowsazure.cn/subscriptions/)以后，即可查看和下载当前和过去的帐单。

若要查看或下载帐单，请执行以下步骤：

1. 使用 Org ID 登录到 Azure 帐户门户。
2. 单击“应用商店”。所显示的列表是一个完整列表，其中包含通过你的 Azure 帐户下的所有应用商店订单。你会注意到，这些订单按父级 Azure 订阅名称和 ID 分组。

    ![应用商店订单](./media/billing-understand-your-azure-marketplace-charges/marketplace-orders.png)

3. 单击任意显示的订单将会转到该订单当前计费周期的摘要性概述。如需对不同定价模型的说明，可访问 [Marketplace 常见问题](https://azure.microsoft.com/marketplace/faq/)。

    ![订单摘要](./media/billing-understand-your-azure-marketplace-charges/order-summary.png)

摘要视图包含每个订单的以下信息：
- 价格
- 计费周期
- 购买日期
- 关联的父订阅
- 计费货币

请注意，我们对此信息的显示方式进行了一些更改。以前，我们显示的订单的费用含税。此内容在更新后，所显示的价格可能含税，也可能不含税，具体取决于你所在的国家/地区。此外，价格的位置已从右侧导航移至中心。为了跟踪这些费用所关联到的 Azure 订阅，我们还将 Azure 订阅名称和 ID 以及“更改付款方式”选项添加到了右侧导航。

## 更新付款方式和订单管理
摘要页上有用户选项，因此你可以更新付款模型和订单管理功能：

> [AZURE.NOTE] 如果你是使用组织 ID 来更改个人信息，则需向支持部门登记票证。

若要更新付款方式，请单击页面右侧的“更改付款方式”链接。

![订单摘要](./media/billing-understand-your-azure-marketplace-charges/order-summary.png)

此链接会将你带到另一门户，你可以在其中对首选付款方式进行更改。

若要更改付款方式，请按以下步骤操作：

1. 单击“更改付款方式”。

    ![订阅](./media/billing-understand-your-azure-marketplace-charges/subscriptions.jpg)

2. 选择你想要将付款方式更改为何种付款方式。你可以通过“付款方式”选项来选择信用卡。你可以通过“添加新的付款方式”选项来添加新的信用卡。

    ![更改付款方式](./media/billing-understand-your-azure-marketplace-charges/change-payment-method.jpg)

## 订单管理
在摘要性概述页上，你将看到“管理订单”链接。此链接会将你带到你所在的门户，你可以在该门户中查看所有的应用商店订单、查看服务运行状况以及请求支持。

![门户](./media/billing-understand-your-azure-marketplace-charges/portal.jpg)

若要请求支持，请单击“帮助 + 支持”磁贴。此时会显示以下“帮助 + 支持”页，你可以在其中执行以下操作：
- 打开新的支持请求。
- 管理现有的支持请求。
- 管理资源运行状况。

![请求支持](./media/billing-understand-your-azure-marketplace-charges/request-support.jpg)

## 帐单历史记录
摘要页上的一项新功能是能够查看基于使用量的产品/服务（例如虚拟机）。现在，你可以根据计费周期下载当前期间和已结算期间的使用量。若要查看使用量，请根据你要查看的期间单击“下载使用量”链接。

如果你想查看实际费用，请单击“查看费用”。此时你会被重定向到另一门户，你可以在其中查看所有费用，包括税。如果你使用的是组织 ID，则不会启用“查看费用”按钮，你需要提交支持票证才能请求查看费用摘要。

![帐单历史记录](./media/billing-understand-your-azure-marketplace-charges/billing-history.png)

若要查看实际费用，请按以下步骤操作：

1. 单击“帐单历史记录”页上的“查看费用”。

	此时将打开一个新的选项卡，显示“[订单历史记录](https://account.microsoft.com/billing/orders#/)”。

    请注意，如果你尝试访问计费和帐户管理门户中的原始使用数据，则需单击页面底部的支持链接来呼叫支持部门。

    ![订单历史记录](./media/billing-understand-your-azure-marketplace-charges/order-history.jpg)

2. 确定你要查看的订单，然后单击“详细信息”即可查看费用明细，其中包括小计、税和总费用。

    ![订单详细信息](./media/billing-understand-your-azure-marketplace-charges/order-details.jpg)

<!---HONumber=Mooncake_0620_2016-->