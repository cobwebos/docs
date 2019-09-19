---
title: Azure 企业注册发票
description: 本文介绍如何管理和处理 Azure 企业发票。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 5e3a6249f19df7d16c7051c43eec4275ec6f4131
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090717"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Azure 企业注册发票

本文介绍如何管理和处理 Azure 企业发票。 发票代表帐单，因此你应检查其准确性。 你还应该熟悉管理发票时可能需要执行的其他任务。

## <a name="incorrect-overage-invoice"></a>错误的超额发票

如果你认为某份超额发票不正确，请参阅[了解 Azure 企业协议帐单](billing-understand-your-bill-ea.md)来帮助核对 Azure 使用费。 如果你认为 Microsoft 错误地向你收取了超额费用，请联系支持人员。

## <a name="change-a-po-number-for-an-overage-invoice"></a>更改超额发票的 PO 编号

Azure EA 门户会自动生成默认的采购订单 (PO) 编号，除非 EA 管理员在开票日期之前设置一个编号。 EA 管理员可以通过在 Azure EA 门户中登录并导航到“报告”来更新 PO 编号。  EA 管理员可以更新 PO 编号。 在显示 PO 编号的右上部分，单击铅笔符号并更新信息。

在创建发票之前，EA 管理员可以在当前计费季度更新 PO 编号。 在收到自动发送的发票通知电子邮件后的最多 7 天内，他们也可以更新 PO 编号。

如果 EA 管理员未更新 PO 编号并需要使用新的 PO 编号，则唯一的做法是占用额度重新开单。 如果客户必须更新 PO 编号，需联系合作伙伴来获得帮助。

## <a name="adjust-billing-frequency"></a>调整计费频率

客户的计费频率为每年、每季或每月。 计费周期是客户签署协议时确定的。 每月计费是最短的计费间隔。

将直接注册的计费周期从每年更改为每季需要经过企业管理员的批准。 对于间接注册，需要合作伙伴管理员的批准。 更改将在当前计费季度结束时生效。

若要将每年或每季计费周期更改为每月，需要对协议进行修订。  对现有注册计费周期进行任何更改都需要经过企业管理员的批准，或者协议中指定为“接收帐单的联系人”的批准。  可以在 [Azure EA 门户的“支持”](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)中提交审批请求，然后选择“开单和开票”作为“问题类别”。   更改将在当前计费季度结束时生效。

如果已签署“协议修订 M503”，则可以将任何协议的任何计费频率改为每月计费。 

## <a name="credits-and-adjustments"></a>额度和调整

适用于注册的所有额度或调整显示在 [https://ea.azure.com](https://ea.azure.com) 上的“报告”部分。  如果你遇到了有关实际额度方面的具体问题，请联系 [Azure EA 门户支持人员](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)。

## <a name="request-an-invoice-copy"></a>请求发票副本

若要请求发票副本，请与合作伙伴联系。

## <a name="understand-your-bill"></a>了解帐单

有关发票和费用的信息，请参阅[了解 Azure 企业协议帐单](billing-understand-your-bill-ea.md)。

## <a name="overage-offset-by-customers"></a>客户的超额抵销

如果客户想要同时使用超额和货币承诺，必须符合以下条件：

- 客户的超额费用应已发生但尚未支付，并且付款期限在计费服务结束日期后的一年之内。
- 可用货币承诺金额应涵盖产生的总费用，包括以往未付的所有 Azure 发票。
- 请求完成的计费条款必须完全了结。 计费将在每个月的五号之后完全了结。
- 请求抵销的计费周期必须完全了结。
- ACD 折扣基于实际的新承诺金额减去根据上次消耗量计划的任何资金。 此项要求仅适用于产生的超额费用。 这只适用于消耗货币承诺的服务，因此不能涵盖市场费用。 市场费用单独计收。
- 如果客户想要完成超额抵销，可以提出支持请求。 或者，可以由帐户团队提出支持请求。 若要完成此过程，需要经过客户 EA 管理员或“接收帐单的联系人”的电子邮件批准。

## <a name="view-price-sheet-information"></a>查看价目表信息

企业管理员可以查看与其 Azure 服务注册关联的价目表。

若要查看当前价目表：

1. 在 Azure EA 门户中，依次单击“报告”、“价目表”。  
2. 查看价目表，或单击“下载”。 

![显示价目表信息的示例](./media/billing-ea-portal-enrollment-invoices/ea-create-view-price-sheet-information.png)

若要下载历史价目表：

1. 在 Azure EA 门户中，依次单击“报告”、“下载使用情况”。  
2. 下载价目表。

![显示旧价目表下载位置的示例](./media/billing-ea-portal-enrollment-invoices/create-ea-view-price-sheet-download-historical-price-list.png)

如果定价存在偏差，部分原因包括：

上一个注册与新注册的定价可能已更改。 发生了价格变化，因为从协议的开始日期到结束日期，定价对于特定的注册是契约性的。 将某个注册转移到新注册后，它会遵从新协议的定价。 定价由客户的价目表定义。 因此，新注册中的价格可能更高。

如果注册进入到延长期，定价也会改变。 价格将更改为即用即付费率。

## <a name="request-detailed-usage-information"></a>请求详细的使用情况信息

企业管理员可以在 Azure EA 门户中查看其使用情况数据、货币承诺消耗量，以及与其他用途相关的费用的摘要。 费用以摘要形式显示，适用于所有帐户和订阅。

若要查看特定帐户中的详细使用情况，可以通过导航到“报告” > “下载使用情况”来下载“使用情况详细信息”报告。   该报告不包含任何适用的税费。 从使用服务开始算起，最长可能需要在延迟八小时之后，其费用才会反映在报告中。

对于间接注册，合作伙伴需要启用标记功能，然后你才能看到任何成本相关的信息。

## <a name="move-usage-data-to-another-enrollment"></a>将使用情况数据移到另一个注册

只有在将转移操作的日期调后时，才会移动使用情况数据。 可通过两个选项将使用情况数据从一个注册移到另一个注册：

- 将帐户从一个注册转移到另一个注册
- 将注册从一个注册转移到另一个注册

使用任一选项都必须向 EA 支持团队提交[支持请求](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)以获得帮助。 

## <a name="monetary-commitment-and-unbilled-usage"></a>货币承诺和未开票用途

Azure 货币承诺是提前为 Azure 服务支付的金额。 使用服务时，会消耗货币承诺。 第一方 Azure 服务使用货币承诺。 但是，单独计收的费用和市场服务存在例外情况。

“单独计收的费用”和“Azure 市场服务”是从 Azure 市场平台部署的第三方服务。   这两个服务之间的主要差别在于其根据第三方发布者建立的协议结构开票的方式。

第三方用途将显示在发票中的“单独计收的费用”下面。  该用途会消耗 Azure 货币承诺余额。 若要查看第三方服务的列表，请参阅 [Azure 市场第三方分销商服务现在使用 Azure 货币承诺](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/)。

显示在发票中“Azure 市场”下面的用途将单独收费。  它不消耗任何货币承诺。 但是，所有费用（包括市场、超额和单独计收的费用）将合并到一份发票。

对于 Azure 第一方超额和 Azure 第三方市场购买，你会收到一份发票。

耗尽货币承诺后，将按月或按季计收使用费。 如果你的注册已在 2018 年 5 月之前启动，则会应用 150% 货币承诺阈值规则，并按季计收超额费用。 如果你的注册在 5 2018 月5日后开始，则所有超额都按月计费。

## <a name="next-steps"></a>后续步骤
- 有关发票和费用的信息，请参阅[了解 Azure 企业协议帐单](billing-understand-your-bill-ea.md)。
- 若要开始使用 Azure EA 门户，请参阅 [Azure EA 门户入门](billing-ea-portal-get-started.md)。
