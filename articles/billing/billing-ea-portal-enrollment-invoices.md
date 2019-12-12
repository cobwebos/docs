---
title: Azure 企业注册发票
description: 本文介绍如何管理和处理 Azure 企业发票。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/04/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: 15c2e84742ff0f552c688ccf1ba795f742e288ca
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849881"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Azure 企业注册发票

本文介绍如何管理和处理 Azure 企业发票。 发票代表帐单，因此你应检查其准确性。 你还应该熟悉管理发票时可能需要执行的其他任务。

## <a name="change-a-po-number-for-an-overage-invoice"></a>更改超额发票的 PO 编号

Azure EA 门户会自动生成默认的采购订单 (PO) 编号，除非 EA 管理员在开票日期之前设置一个编号。 在收到自动发送的发票通知电子邮件后的最多 7 天内，EA 管理员可以更新 PO 编号。

还可以为特定帐户或订阅输入采购订单 (PO) 编号。 报表默认使用层次结构中最低级别的采购订单编号集，也就是说：如果未输入订阅级采购订单编号，它将使用帐户级采购订单编号；如果未输入帐户级采购订单编号，则使用部门级采购订单编号。

### <a name="to-update-the-azure-services-purchase-order-number"></a>更新 Azure 服务采购订单编号：

1. 在 Azure EA 门户中，先单击“报表”，再单击“使用情况摘要”   。
1. 在右上角选择“编辑 PO 编号”  。
1. 选择“Azure 服务”单选按钮  。
1. 从日期范围下拉菜单中选择一个“发票期间”  。 在收到发票通知后最多七日或者在对发票付款前（以先到者为准）可以编辑 PO 编号。
1. 在“ **PO 编号**”字段中输入新的 PO 编号。 
1. 单击“ **保存**”以提交更改。 

### <a name="to-update-the-marketplace-purchase-order-number"></a>更新市场采购订单编号：

1. 在 Azure EA 门户中，先单击“报表”，再单击“使用情况摘要”   。
1. 在右上角选择“编辑 PO 编号” 
1. 选择“市场”单选按钮 
1. 从日期范围下拉菜单中选择一个“发票期间”  。 在收到发票通知后最多七日或者在对发票付款前（以先到者为准）可以编辑 PO 编号。
1. 在“ **PO 编号**”字段中输入新的 PO 编号。 
1. 单击“ **保存**”以提交更改。 

## <a name="cadence-of-azure-ea-billing"></a>Azure EA 计费周期

### <a name="billing-intervals"></a>计费时间间隔

Microsoft 每年在登记生效日对 Microsoft Azure 服务的任何承诺购买量计费，对超出承诺金额的任何用量则以欠款形式计费。 承诺费用是根据每月费率报出的，每年提前计费。 超额费用按月计算，在计费周期结束时以欠款形式计费。

年度承诺的期限要么是登记周年日，要么是一年期修订订阅的生效日，具体取决于购买承诺的方式。

收到超额发票的时间取决于登记开始日期和设置。

**开始日期在 2018 年 5 月 1 日之前的直接合约**：已将 Azure 服务（市场服务除外）的 Enterprise Azure (EA) 直接客户的计费周期设置每年计费。 计费周期基于周年日。 周年日即协议生效日。 只有超过货币承诺 (MC) 阈值的 150%，才能收到第一张 Azure 服务超额发票。  一旦服务使用量超过 MC 阈值的 150%，系统就会根据周年日自动将你的计费周期转换为季度。  如果未超过 MC 阈值的 150%，则合约仍然采用按年计费的周期，并且你将于承诺的年末收到超额发票。

**开始日期在 2018 年 5 月 1 日之后的直接合约**：客户的 Azure 使用量和单独计费费用发票的计费周期为按月计费。  Azure 货币承诺未涵盖的费用均被计入超额付款。  

**登记开始日期在 2018 年 5 月 1 日之前的间接合约**：已将 Enterprise Azure (EA) 间接客户的计费周期设置为按季度计费。  通道合作伙伴 (CP) 将直接向客户开票。  

**开始日期在 2018 年 5 月 1 日之后的间接合约**：开始日期为 2018 年 5 月 1 日或之后的任何间接协议按月计费。  

### <a name="increasing-commitment"></a>增加承诺使用量

可随时增加承诺使用量，并按当年承诺期剩余的月数计费。 例如，如果你注册的是一年期的修订订阅并在六月增加了承诺使用量，则系统将按当年剩下的六个月对你开票。 还将更新承诺期内最后六个月的承诺使用量，以确定任何超额费用。

### <a name="overage"></a>超额

如果超额，将对计费周期内超出承诺使用量的使用量或预留使用量计费。 如果要查看为各项计算超额用量的明细，请参阅使用情况摘要报表或联系自己的渠道合作伙伴。

发票上的每一项都有总费用（总额）、费用涵盖的承诺使用量（承诺使用量）以及超出承诺的费用（净额）。  仅对超出承诺的净额计算相应税款。

系统自动执行超额开票。  发出通知和开具发票的时间取决于客户计费周期的结束日期。  通常在客户计费结束日期后七日内发送超额通知，在这段时间内客户可以进入门户、查看费用和更新系统生成的 PO 编号（也可以在发布超额通知前的任何时间更新）。  在此后的 7-9 日发送超额发票。

### <a name="azure-marketplace"></a>Azure 市场

自 2019 年 4 月这个计费周期起，客户将开始收到一张 Azure 发票，它将所有 Azure 和 Azure 市场的费用合并到一张发票中（而不是分别使用两张单独的发票）。 （此更改不影响澳大利亚、日本或新加坡的客户。）在向合并发票过渡期间，你将收到一张部分市场发票。 这张单独的最终发票显示计费更新日之前的市场费用。 该日以后的市场费用显示在 Azure 发票上。 过渡期结束后，所有 Azure 费用和市场费用均显示在合并发票上。  

### <a name="purchase-order-numbers"></a>采购订单编号

默认为采购订单使用系统生成的采购订单编号。 用户在 Enterprise Portal 中使用企业管理员的身份登录并导航到“报表”部分，这样就能更改他们的采购订单编号。 窗口右上角有一个用于 PO 编号的框，企业管理员单击铅笔图标就能编辑 PO 编号。

## <a name="adjust-billing-frequency"></a>调整计费频率

客户的计费频率为每年、每季或每月。 计费周期是客户签署协议时确定的。 每月计费是最短的计费间隔。

将直接注册的计费周期从每年更改为每季需要经过企业管理员的批准。 对于间接注册，需要合作伙伴管理员的批准。 更改将在当前计费季度结束时生效。

若要将每年或每季计费周期更改为每月，需要对协议进行修订。  对现有注册计费周期进行任何更改都需要经过企业管理员的批准，或者协议中指定为“接收帐单的联系人”的批准。  可以在 [Azure EA 门户的“支持”](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)中提交审批请求，然后选择“开单和开票”作为“问题类别”。   更改将在当前计费季度结束时生效。

如果已签署“协议修订 M503”，则可以将任何协议的任何计费频率改为每月计费。 

## <a name="credits-and-adjustments"></a>额度和调整

适用于注册的所有额度或调整显示在 [https://ea.azure.com](https://ea.azure.com) 上的“报告”部分。 

要查看额度，请按以下步骤操作：

1. 选择“报表”部分  。
1. 单击“使用情况摘要”  。
1. 在右上角将 M 视图更改为 C 视图   。
1. 扩展 Azure 服务承诺表中的调整字段。
1. 你将在本行中看到应用于合约的额度以及一个简短的说明，例如：服务级别协议额度。

## <a name="request-an-invoice-copy"></a>请求发票副本

若要请求发票副本，请与合作伙伴联系。

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

## <a name="reports"></a>报告

企业管理员可以查看对其使用情况数据、使用的货币承诺以及与 Enterprise Portal 中其他使用量相关的费用的摘要。 在摘要级别显示所有帐户和订阅的费用。

**EA 报表**

- 使用情况摘要和关系图
- 服务使用情况报表
- 余额和费用报表
- 使用情况详细信息报表
- 市场费用报表
- 价目表
- 高级报表下载
- CSV 报表格式

**查看使用情况摘要报表和关系图**

1. 在 Azure EA 门户中单击左侧导航栏中的“报表”，然后查看“使用情况摘要”选项卡   。
1. 从左上方的日期范围下拉菜单中选择所需的承诺期限。
1. 在图表上选择所需的时间段或月份，以查看更多详细信息。
1. 查看显示了每月使用情况的图，其中细分了使用量、服务额外费用、单独计收的费用和市场费用。
1. 对于选定的月份，可在图表下方按部门、帐户和订阅进行筛选。
1. 在“按服务列出的费用”明细与“按层次结构列出的费用”明细之间切换。
1. 详细查看 Azure 服务、单独计费的费用以及 Azure 市场费用。

## <a name="service-usage-report"></a>服务使用情况报表

企业管理员可以在“服务使用情况报表”页查看其服务使用情况数据的摘要。 虽然是在摘要级别呈现所有帐户和订阅的使用情况，但你也可以按帐户或订阅筛选报表，以查看详细的使用情况。

请注意，在使用日和具体使用情况体现在该报表上日期之间，可能存在最多五日的延迟。

### <a name="to-view-the-report"></a>查看报表：

1. 登录到 Enterprise Portal。
1. 在左侧导航区域，单击“报表”  。
1. 单击“使用情况摘要”选项卡  。
1. 单击所需的日期范围。
1. 选择要查看的帐户或订阅。
1. 将视图从“按服务列出费用”或“按层次结构列出费用”更改为显示不同的明细。
1. 查看详细信息，包括服务名称、度量单位、已使用的单位、有效利率和总成本。

## <a name="download-csv-reports"></a>下载 CSV 报告

企业管理员可以在“每月报表下载”页面将几个报表（包括余额和费用报表、使用情况详细信息报表、市场费用报表以及价目表）下载为 .csv 文件。

## <a name="to-download-reports"></a>若要下载报告：

1. 在 Azure EA 门户中单击“报表”  。
1. 从顶部功能区中选择“使用情况下载”  。
1. 选择相应月份报表旁边的“下载”  。



## <a name="csv-report-formatting"></a>CSV 报表格式

查看 Azure EA 门户中以欧元为单位的 CSV 报表的客户可能会遇到与逗号和句点相关的格式问题。

例如，你可能会看到：

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| 小时 | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

应会看到：

| ServiceResource | ResourceQtyConsumed | ResourceRate | ExtendedCost |
| --- | --- | --- | --- |
| 小时 | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

### <a name="root-cause"></a>根本原因

Excel 将所有字段都作为“常规”文本导入，并按数学标准分隔数字，例如：1,000.00。  于是，将句点 (.) 用作为千分位符、将逗号用作小数分隔符 (,) 的欧洲货币（比如 1.000,00）的显示就不正确。 这可能会根据你的区域语言设置而异。

### <a name="when-importing-the-csv-use-the-following-steps"></a>导入 CSV 时，请按以下步骤操作：

1.    打开 Excel，转到“文件”>“打开”。
1.    系统显示“文本导入向导”。
1.    在“原始数据类型”下，选择“带分隔符”  。  默认使用“固定宽度”  。
1.    点击“下一步”  。
1.    在“分隔符”下，选中“逗号”的复选框。 默认值为“制表符”（取消选中）。
1.    点击“下一步”  。
1.    滚动到“ResourceRate”和“ExtendedCost”列。
1.    选择“ResourceRate”列（系统将使用黑色突出显示它）。
9.    在“列数据格式”部分下，选择“文本”而不是“常规”。  列标题就会由“常规”变为“文本”。
10. 对“ExtendedCost”列重复第 8 步和第 9 步。 选择“完成”。 

如果要将 Excel 关联为自动打开 \*.csv 文件，则必须改为使用 Excel 中的“打开”功能。 打开 Excel，转到“文件”>“打开”。

## <a name="balance-and-charge-report"></a>余额和费用报表

余额和费用报表提供了关于余额、新购买、Azure 市场服务费用、调整和超额费用信息的每月摘要。 Azure 服务承诺摘要报表中所有行每月都保持不变，而所有购买和调整的详细信息显示在“新购买详情”和“调整详情”部分。

### <a name="download-the-balance-and-charge-report"></a>下载余额和费用报表

1. 以企业管理员身份登录到 Azure EA 门户。
1. 在左侧导航区域，单击“报表”  。
1. 单击“报表下载”选项卡  。
1. 在“余额和费用”列下选择相应月份，然后单击以下载报表  。

## <a name="usage-detail-report"></a>使用情况详细信息报表

使用情况详细信息报表对合约所用服务和量的月度摘要，包括计价器名称、计价器类型和使用量信息。

### <a name="download-the-usage-detail-report"></a>下载使用情况详细信息报表

1. 以企业管理员身份登录到 Azure EA 门户。
1. 在左侧导航区域，单击“报表”  。
1. 单击“下载使用情况”选项卡  。
1. 在“使用情况详细信息”列下选择相应月份，然后单击以下载报表  。

## <a name="marketplace-charges-in-azure-ea-portal-reports"></a>Azure EA 门户报表中的市场费用

可在[此处](https://azure.microsoft.com/marketplace/faq/)找到有关市场费用的其他信息。

市场费用有以下两种类型：

- **基于用量：** 以事务单位度量的产品。  例如，虚拟机按小时收费，必应 API 搜索按搜索数量收费。
- 每月费用：  根据使用量/访问量每月计费。

查看 Enterprise Portal 中的不同费用：

1. **使用情况摘要报表 -** 显示基于用量和每月费用的市场费用  。
1. **市场费用报表 -** 仅显示基于用量的市场费用  。  不显示一次性费用。

注意：Azure 市场不可用于 MPSA 合约。

## <a name="advanced-report-download"></a>高级报表下载

如需特定日期范围或帐户的报表，可以使用高级报表下载功能。 从 2016 年 8 月 30 日起，输出文件的格式从 .xlsx 变为 .csv，以便容纳较大的记录集。

1. 选择“高级报表下载”  。
1. 选择适当的日期范围  。
1. 选择适当的帐户  。
1. 选择“请求使用情况数据”  。
1. 选择“刷新”按钮，直到报表状态更新为“下载”  。
1. 下载报表。

## <a name="reporting-for-non-enterprise-administrators"></a>适用于非企业管理员的报表功能

企业管理员可以为部门管理员 (DA) 和帐户所有者 (AO) 启用查看合约上的费用的访问权限。 启用后，帐户所有者就可以下载特定于其帐户和订阅的 .csv 报表，也可以直观地查看 Enterprise Portal 的“报表”部分下的信息。

### <a name="to-enable-access"></a>启用访问权限：

 1. 以企业管理员的身份登录。
 1. 在左侧导航区域中单击“管理”  。
 1. 单击“许可登记表”选项卡  。
 1. 在“合约详细信息”部分下，选择位于以下两项旁的铅笔图标：
    - DA 查看费用
    - AO 查看费用
 1. 选择“启用”。 
 1. 单击“ **保存**”。

### <a name="to-view-reports"></a>查看报表：

1. 以部门管理员或帐户所有者的身份登录到 Azure EA 门户。
1. 在左侧导航区域，单击“报表”  。
1. 单击“使用情况摘要”选项卡直观地查看帐户和订阅相关信息  。
1. 单击“使用情况下载”查看 .csv 报表  。

使用“高级报表下载”功能时，部门管理员和帐户所有者无法查看费用  。 费用显示为 $0。

“AO 查看费用”功能扩展到帐户所有者以及对关联订阅具有权限的所有用户。 如果你是间接客户，必须由渠道合作伙伴来为你启用成本功能。

## <a name="move-usage-data-to-another-enrollment"></a>将使用情况数据移到另一个注册

只有在将转移操作的日期调后时，才会移动使用情况数据。 可通过两个选项将使用情况数据从一个注册移到另一个注册：

- 将帐户从一个注册转移到另一个注册
- 将注册从一个注册转移到另一个注册

使用任一选项都必须向 EA 支持团队提交[支持请求](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)以获得帮助。 

## <a name="azure-ea-pricing-overview"></a>Azure EA 定价概述

本文档详述如何计算使用量，并对与企业计划中各项 Azure 服务（其中的计算更为复杂）相关的常见问题做出解答。

### <a name="price-protection"></a>价格保护

我们保证客户和渠道合作伙伴（就间接渠道而言）收到的价格不高于他们的客户价目表 (CPS) 中的价格或购买 Azure 产品/服务的生效日当日的价格。 此价格称作基准价格。 对于购买后引入的服务，则为首次引入服务时应用了相应级别的折扣的价格。 此价格保护策略在承诺期（一年或三年，因企业计划而异）内均适用。

### <a name="price-changes"></a>价格更改

Microsoft 可能会在登记有效期内降低当前企业计划中个别 Azure 服务的价格。  较低的价格生效时，我们将把这些降低后的费率扩展到现有客户。  如果后来提高了这些价格，如上所述，登记的价格也不会提高到高于客户的价格保护上限，但与之前降低的价格相比可有所提高。  不管是哪一种情况，Microsoft 都会想与登记相关的企业和合作伙伴管理员发送电子邮件，将即将发生的价格更改事宜告诉客户和间接渠道合作伙伴。

### <a name="current-effective-pricing"></a>当前有效定价

客户和渠道合作伙伴登录 [Azure Enterprise Portal](https://ea.azure.com/) 并导航到某登记的价目表页，即可查看该登记的当前定价。  如果通过我们的渠道合作伙伴之一间接购买 Azure，则需要通过渠道合作伙伴获取定价更新（如果他们尚未为合约启用显示加成定价功能）。

### <a name="introduction-of-new-azure-services"></a>新 Azure 服务简介

我们正在不断地改善 Azure，定期增加与现有服务分开定价的新服务。  某些预览服务是自动提供的，其他服务则需要客户在 [Azure 帐户门户](https://account.windowsazure.com/PreviewFeatures)中执行操作才能使用。 另请注意，服务从预览版变为正式版后，由于应用了完整的性能和可靠性 SLA，价格可能会提高。 最后，可能会以首次引入服务时的促销价提供某些服务，但可能会在未来某日提高价格。 由预览版定价或促销价提升为正式版价格的任何行为均不受普通基线价格保护的约束，适用于使用这些不断改善的服务的情况。 客户选择不使用新服务，就可以避免与这些服务相关的费用。

### <a name="introduction-of-regional-pricing"></a>区域定价简介

除了引入新服务外，随着对服务的支持的增加，还会定期将这些服务从单一的全局模型更改为更具区域性的模型。 首次引入某服务的区域化时，价格保护适用于之前登记使用的全局价格未涵盖的新区域。 不过，之后则将为该服务额外引入的区域视作新的服务，按其各自独立于基准价格保护之外的费率提供。

### <a name="enterprise-msdn-devtest"></a>Enterprise MSDN 开发/测试

企业管理员可以使帐户管理员能够基于 EA MSDN 开发/测试套餐创建订阅。 为了使此功能正常运作，帐户所有者需要设置底层 MSDN 订阅者所需的 EA MSDN 开发/测试订阅。 这使 MSDN 有效订阅者能够用开发/测试优惠费率在 Azure 上运行开发和测试工作负荷。 有关详细信息，请参阅 [ 开发/测试](https://azure.microsoft.com/offers/ms-azr-0148p/)。

### <a name="enterprise-program-usage-calculation-guidelines"></a>企业计划使用量计算指南

请参阅 [Azure 服务](https://azure.microsoft.com/services/)和 [Azure 定价](https://azure.microsoft.com/pricing/)了解各项服务基本的公共定价信息、度量单位、FAQ 和使用情况报表指南。 此外，在计算服务使用情况时，还应使用以下企业计划指南。

### <a name="enterprise-program-units-of-measure"></a>企业计划度量单位

企业计划的度量单位通常不同于其他计划，例如 Microsoft 在线订阅协议 (MOSA) 计划。 也就是说，对许多服务而言，合计度量单位是为了提供标准化定价。 Enterprise Portal 的使用情况摘要视图中显示的度量单位始终为 Enterprise 度量。 [友好服务名称](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx)中提供了每项服务的当前度量单位和换算的完整列表。

### <a name="rounding-rules"></a>舍入规则

在 Enterprise Portal 中执行的舍入采用 IEEE 标准银行家舍入法或高斯舍入法逻辑。 遇到数字 5 时，该规则将其舍入为最接近的偶数；而最典型的四舍五入取整函数始终将数字 5 进位为一个较大的数字。 与标准 Excel 逻辑相比，此方法提供的一组数字的总和更准确。

如果要考虑舍入的第一个数字是 5 且它后面没有其他数字或者它后面的数字为 0，则将它前面的数字进一（即进位为最近的偶数）。 例如，2.315 和 2.325 在舍入为最接近的千分位数时，均为 2.32。

值得一提的是，使用 Excel 对 Enterprise Portal 中使用的舍入和换算规则建模时，应使用 MROUND 公式，如下所示。

| 场景 | 银行从业者逻辑公式 |
| --- | --- |
| 舍入用法 | =MROUND({_source_}, 0.0002) |
| 舍入定价（2 位小数） | =MROUND({_source_}, 0.02) |
| 舍入定价（0 位小数） | =MROUND({_source_}, 2) |

**表** **2**  **云服务和虚拟机时数转换**

### <a name="conversion-between-download-usage-file-and-usage-summary-portal-view"></a>“下载使用情况”文件和“使用情况摘要”门户视图之间的转换

如下载使用情况数据报表中所见，原始资源使用量数据在报表中最多取六位小数。 但 Azure Enterprise Portal 将用于承诺单位的使用量舍入为四位小数，将用于超额单位的使用量截为零位小数。 在换算为 Enterprise 度量单位之前，原始使用量首先舍入为四位数，而换算得出的 Enterprise 单位也舍入为四位数。 换算前实际使用的时数仅在下载使用情况报表中显示，不在 UI 本身中显示。

例如，假设下载使用情况报表中报告的实际 SQL Server 时数为 694.533404。 随后，这些单位被换算为 6.94533404 个单位的计算时间（每个单位 100 小时），Enterprise Portal 中将该数字舍入为 6.9453。

然后用承诺单价乘以这些单位，将得到的结果取两位小数，这就是计费总额。 对日元 (JPY) 和韩元 (KRW)，则将该总额舍入为零位小数（即整数）。

这个示例如果是计算超额，则将可计费单位取六位小数，然后乘以超额单价，得出计费总额。 对于托管服务提供商 (MSP) 账单，在超额报表中换算为 EA 度量单位后，将与标记为 MSP 的部门相关的所有用量取整（零位小数）。 这样，该用量的总和可能低于 UI 中报告的所有用量的总和，具体取决于 MSP 是尚有货币承诺余额还是已经超额。

### <a name="graduated-pricing"></a>分级定价

企业计划定价当前不包括分级定价（定价随利用率的增加而降低）。 从具有分级定价的 MOSA 计划向企业计划迁移时，根据企业计划折扣调整后，定价与该服务基本层相当。

### <a name="partial-hour-billing"></a>不足一小时的计费

所有使用量都基于换算为小时数的分钟（按而不是整小时的增量）计费。  按小时列出的企业费率直接应用于总时数（包括非整时数在内）。

### <a name="average-daily-consumption"></a>平均日使用量

按月对服务定价但使用量按日报告时，每日计算使用量，定价除以 31，再根据该计费月内的天数求和。 对于不足 31 日的月份，所得的费率结果始终不会高于（而是略低于）任何月份的预期费率。

### <a name="compute-hours-conversion"></a>计算小时数的转换

以前，A0、A2、A3 和 A4 标准和基本虚拟机和云服务的所有用量都发布为 A1 虚拟机计算分钟数的约数 (A0) 或倍数（A2、A3 和 A4）。 这给我们的客户造成了某些困扰，因此我们实行了更改，将每分钟用量分配给专用的 A0、A2、A3 和 A4 计价器。

新计量方式于 2016 年 1 月 27 日到 1 月 28 日之间生效。 过渡期间，在你的部署的 .csv 下载文件中，你会注意到两个明细项目：一个用于 A1 计价器上发布的用量，另一个用于与你的部署大小相应的新专用计价器上发布的用量。

| **部署大小** | **2016 年 1 月 26 日当日，用量按 A1 的倍数发布** | **自 2016 年 1 月 27 日起，按专用计价器发布用量** |
| --- | --- | --- |
| A0 | 0.25 个 A1 小时 | 1 个 A0 小时 |
| A2 | 2 个 A1 小时 | 1 个 A2 小时 |
| A3 | 4 个 A1 小时 | 1 个 A3 小时 |
| A4 | 8 个 A1 小时 | 1 个 A4 小时 |

### <a name="regions"></a>区域

对于定价受区域影响的服务而言，下表显示的是地理位置和区域之间的映射：

| 地域 | 数据传输区域 | CDN 区域 |
| --- | --- | --- |
| 区域 1 | 北欧 <br> 西欧 <br> 美国西部 <br> 美国东部 <br> 美国中北部 <br> 美国中南部 <br> 美国东部 2 <br> 美国中部 | 北美 <br> 欧洲 |
| 区域 2 | 亚太东部 <br> 亚太东南部 <br> 日本东部 <br> 日本西部 <br> 澳大利亚东部 <br> 澳大利亚东南部 | 亚太区 <br> 日本 <br> 拉丁美洲 <br> 中东/非洲 <br> 澳大利亚东部 <br> 澳大利亚东南部 |
| 区域 3 | 巴西南部 |   |

**表** **4** **- 数据传输区域**

对于驻留在同一数据中心内的服务（例如 O365 和 Azure）之间的数据传出不收取任何费用。

### <a name="monetary-commitment-and-unbilled-usage"></a>货币承诺和未开票用途

Azure 货币承诺是提前为 Azure 服务支付的金额。 使用服务时，会消耗货币承诺。 第一方 Azure 服务使用货币承诺。 但是，单独计收的费用和市场服务存在例外情况。

### <a name="charges-billed-separately"></a>单独计费

某些由第三方来源提供的产品和服务不使用 Azure 货币承诺。 将这些项作为标准计费周期的超额发票的一部分单独计费。

我们已将所有 Azure 和市场费用合并到一张与登记的计费周期相符的发票中。 （这不适用于澳大利亚、日本或新加坡的客户。）

合并的发票首先显示 Azure 使用情况，后跟任何市场费用。 位于澳大利亚、日本或新加坡的客户将继续在单独的发票上查看市场费用。

如果在标准计费周期结束时没有超额的用量，则对单独计费的费用单独开票。 （适用于澳大利亚、日本和新加坡的客户）

**单独计费的服务包括：**

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop 注册用户
- Openlogic
- 远程访问权限 XenApp Essentials 已注册的用户
- Ubuntu Advantage
- Visual Studio Enterprise（每月）
- Visual Studio Enterprise（每年）
- Visual Studio Professional（每月）
- Visual Studio Professional（每年）

## <a name="azure-marketplace-for-ea-customers"></a>面向 EA 客户的 Azure 市场

直接客户可以在 Azure EA 门户上看到市场费用。 市场产品/服务的购买和使用会在货币承诺以外以按季度/按月以及欠款的形式计费。

间接客户可以在 Azure EA 门户的“管理订阅”页上找到其市场订阅，但定价处于隐藏状态。 客户应该联系其 LSP 以了解市场费用的相关信息。

对于每月或每年新的经常性市场购买，在购买市场项目的期间全额计费。 这些项目会在原始购买的同一日在以下期间自动更新。

这不会影响任何经常性市场费用。 每月经常性费用会在每个日历月的第一日继续更新，而年度费用则在购买日的周年日更新。

Azure 市场提供的某些第三方经销商服务现在使用企业协议 (EA) 货币承诺余额。 这些服务以前不遵照 EA 货币承诺进行计费并且单独开具发票。 市场中这些服务采用 EA 货币承诺付款可帮助客户简化购买和支付管理过程。 如需现在使用货币承诺的服务的完整列表，请参阅 [Azure 网站](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/?WT.mc_id=azurebg_email_Trans_33771_1695_Release_Direct_Tier2_March14)。

### <a name="partners"></a>合作伙伴

LSP 可在 Azure EA 门户中导航到价目表并在右上角单击“市场价目表”，这样就能下载特定于市场的价目表  。 市场价目表反映了所有可用服务及其价格。

**要下载价目表，请执行以下步骤：**

1. 导航到“报表”>“价目表”。
1. 在右上角自己的用户名下找到 Azure 市场价目表的链接。
1. 右键单击该链接，选择“将目标另存为”  。
1. 在“保存”窗口中，将文档的标题更改为“AzureMarketplacePricelist.zip”，这会将该文件的格式由 xlsx 更改为 zip  。
1. 下载完成后，你会得到一个包含特定于国家/地区的价目表的 zip 文件。
1. LSP 应参考各个国家/地区文件中国家/地区特定的定价。 还应使用“通知”选项卡查看对于市场而言全新的 SKU 以及已删除的 SKU。
1. 价格更改并不频繁，但出现更改时，会提前 30 天以电子邮件的形式向 LSP 发出价格提高和外汇变化的通知。
1. LSP 每季度每个 ISV 的每个合约都会收到一张发票。

### <a name="enabling-marketplace-purchases"></a>启用市场购买

企业管理员能够为该合约下的所有 Azure 订阅禁用或启用市场购买功能。 如果企业管理员禁用购买且 Azure 订阅已经有了市场订阅，这些市场订阅不会被取消或不会受到影响。

尽管客户可以将自己的直接 Azure 订阅（通过将其关联到 Azure EA 门户中的合约的方式）转换为 EA 订阅，但这种操作不会自动转换子级市场订阅。

**启用市场购买：**

1. 以企业管理员身份登录到 Azure EA 门户。
1. 导航到“管理”  。
1. 在“合约详细信息”下，单击“Azure 市场”明细项目旁边的铅笔图标   。
1. 根据需要在“启用/禁用”或“仅免费 BYOL SKU\*”直接切换   。
1. 单击“ **保存**”。

### <a name="marketplace-charges-in-azure-ea-portal-reports"></a>Azure EA 门户报表中的市场费用

可在[此处](https://azure.microsoft.com/marketplace/faq/)找到有关市场费用的其他信息。

市场费用有以下两种类型：

- **基于用量：** 使用事务单位度量产品。  例如，虚拟机按小时收费，必应 API 搜索按搜索数量收费。
- **不基于用量：** 与用量无关的一次性费用或每月经常性费用。

两种费用都将在市场费用报表中得到体现。

请注意，Azure 市场不可用于 MPSA 合约。

\*“BYOL（自带许可）和仅免费”选项会将购买和收购 Azure 市场 SKU 限制为仅 BYOL 和免费 SKU。

### <a name="services-billed-hourly-for-ea"></a>EA 按小时计费的服务

在 EA 中对于应用程序传送网络和 Web 应用程序防火墙按小时计费，而在 MOSP 中按月计费。

### <a name="remote-app"></a>远程应用

EA 客户根据其 EA 价格水平为远程应用支付费用，不额外付费。 标准价格点包含初始 40 小时，而无限价格点则包含初始 80 小时。 远程应用在 80 小时后停止发出使用量。

## <a name="azure-marketplace-faq"></a>Azure 市场常见问题解答

本 FAQ 文档回顾 Azure 货币承诺更新对 Azure 市场中某些由第三方发布的服务的适用性。

### <a name="what-are-we-changing-with-respect-to-marketplace-services-and-azure-monetary-commitment"></a>我们在市场服务和 Azure 货币承诺方面做了哪些更改？

除了 Azure虚拟机预留实例 (RI) 之外，客户会收到一张单独的发票，其中涵盖他们从 Azure 市场中购买的所有服务。 我们正在扩展 Azure 货币承诺的使用范围，现在它包括一些客户频繁购买的由第三方发布的 Azure 市场服务。

### <a name="why-are-we-making-this-change"></a>为什么要这样更改？

客户不断寻找额外的方式来利用他们以 Azure 货币承诺的形式进行的预付款。  将货币承诺 (MC) 扩展到这些服务，我们响应了客户这一个频繁的请求并影响了大部分 Azure 市场客户。

### <a name="what-is-the-customer-benefit"></a>客户权益有哪些？

客户获得更精简的计费体验，并能够确保自己充分利用 Azure 货币承诺。  向预付款的货币承诺和使用货币承诺 (MC) 的 RI 添加这项权益能增加 Azure 货币承诺的价值。

### <a name="what-services-will-deduct-from-azure-monetary-commitment-and-how-will-my-customer-know"></a>将从 Azure 货币承诺中去掉哪些服务，我的客户如何获知相关信息？

在 Azure 市场购买体验中，我们将通过免责声明对使用货币承诺的各项服务作出区分。 当前支持的发布者纳入了由 Red Hat、SUSE、Autodesk 和 Oracle 发布的某些服务。 货币承诺中不会删除命名约定相似但由上面未指出的其他方发布的服务。 本 FAQ 末尾提供完整的列表。

### <a name="what-if-my-customer-runs-out-of-monetary-commitment"></a>如果客户的货币承诺余额不足，会出现什么情况？

如果客户已用尽货币承诺额度，目前正处于超额状态，与这些服务相关的费用会连同使用的所有其他服务一起显示在下一张超额发票上。  这是一种变化，以前会将这些费用与其他 Azure 市场套餐一起开具在他们自己的发票上。

### <a name="why-are-we-not-enabling-azure-monetary-commitment-for-all-marketplace-purchases"></a>我们为什么不为所有市场购买启用 Azure 货币承诺？

我们始终致力于在 Azure 货币承诺方面提供最佳客户体验。 这项更改能满足大量客户的需求，能应对 Azure 市场中总支出的很大一部分。 将来可能会添加其他服务。

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>这对间接合约和合作伙伴有何影响？

这不会对间接合约客户或合作伙伴产生任何影响。 这些服务与其他其他消费服务一样，受到相同的合作伙伴加价功能的约束。 唯一的变化是显示它们的发票以及通过货币承诺对费用进行支付。

### <a name="list-of-services-that-will-deduct-from-azure-monetary-commitment"></a>将从 Azure 货币承诺中去掉的服务的列表

特定的 Azure 市场套餐可以使用货币承诺资金。 有关参与此计划的产品的完整列表，请参阅 [Azure 货币承诺](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment)。

## <a name="additional-information"></a>其他信息

请查看这些额外的信息源，了解更多详细信息。 这些文件每月更新两次（分别于每月第 6 日和第 20 日更新）。 每个字段的详细信息如下所示：

| 附录标题 | 说明 | URL 命名约定 |
| --- | --- | --- |
| [**友好服务名称**](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) | 提供以下项目的一栏：所有可用服务及其服务类别、友好服务名称、承诺名称及商品编号、消耗名称及商品编号、度量单位以及报告的用量和 Enterprise Portal 中显示的用量之间的换算因数。 | Friendly\_Service\_Names.xlsx |
| [**服务下载字段**](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) | 此电子表格提供使用情况下载报表中服务相关字段的所有可能的组合的一览。 | Service\_Download\_Fields.xlsx |

**表** **5** **- 其他信息源**

## <a name="power-bi-reporting"></a>Power BI 报表

### <a name="power-bi-pro"></a>Power BI Pro

EA 客户现在可使用 Power BI Pro。 使用 Power BI Pro，可以通过额外的协作和数据刷新功能生成和共享报表，以有效地管理成本数据。 Power BI Pro 提供更高的数据容量和数据流限制。 即将向 Azure Enterprise 客户推出激动人心的新成本管理功能。

使用 Microsoft Azure 使用见解内容包的当前 Power BI 免费用户有资格获得 Power BI Pro 的 60 天免费试用。 如果在免费试用后希望继续使用 Power BI Pro，添加许可证就能实现这一点。

要注册免费试用版，请转到齿轮图标，并选择“管理个人存储”  。 然后在右侧选择“免费试用 Pro”  。 参阅 [Power BI 自助注册](https://powerbi.microsoft.com/documentation/powerbi-service-self-service-signup-for-power-bi/#power-bi-pro-60-day-trial)，了解有关 Power BI Pro 免费试用的详细信息。

### <a name="microsoft-azure-ea-power-bi-pro-trial-terms"></a>Microsoft Azure EA Power BI Pro 试用期限

- **常规用途**：Microsoft Azure Enterprise 的扩展 Power BI Pro 内容包试用套餐（简称“套餐”）适用于在套餐的期限内符合资质的现有用户，可供他们使用特定 Power BI 内容包访问与其 Microsoft Azure 的使用相关的某些见解。
- **资格**：企业协议 (EA) 下的用户如果拥有与其组织的 Microsoft Azure 计费、服务以及服务和/或成本管理相关的功能，可以加入该套餐。
- **排除项**：
  - 已参加延长 Power BI Pro 试用的用户继续享有已有套餐的资格，不得享有 Azure EA Power BI Pro 试用套餐。
  - 参加此套餐的用户只能将 Power BI Pro 与 Microsoft Azure Enterprise 内容包一起使用。 禁止 Power BI Pro 的任何其他用法。
  - 期限：本套餐于 2017 年 6 月 1 日开始，于 2018 年 5 月 31 日结束。  在这 12 个月期间，用户随时可以获取该套餐；不过无论获取时间是哪一天，该套餐均将于 2018 年 5 月 31 日终止。

### <a name="to-access-the-microsoft-azure-consumption-insights-content-pack"></a>访问 Microsoft Azure 使用见解内容包：

1. 导航到 [Microsoft Azure 使用见解](https://app.powerbi.com/getdata/services/azureconsumption?cpcode=MicrosoftAzureConsumptionInsights&amp;getDataForceConnect=true&amp;WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26)。 单击“立即获取”  。
1. 提供合约编号和月数。 单击“下一步”。 
1. 提供要连接的 API 访问密钥。 可以在 [Enterprise Portal](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26) 中找到合约的密钥。 单击“登录”  。
1. 导入过程将自动开始。 完成后，导航窗格中将显示新的仪表板、报表和模型。 单击仪表板查看导入的数据。

有关如何为合约生成 API 密钥的详细信息，请访问 [Enterprise Portal](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26) 上的 API 报表帮助文件。 有关新内容包的详细信息，请下载 [Microsoft Azure 使用见解](https://automaticbillingspec.blob.core.windows.net/spec/Microsoft%20Azure%20Consumption%20Insights.docx?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26)文档。

### <a name="to-access-the-legacy-power-bi-ea-content-pack"></a>访问旧版 Power BI EA 内容包：

 1. 导航到 [Power BI 网站](https://app.powerbi.com/getdata/services/azure-enterprise)。
 1. 使用有效的工作或学校帐户登录。
    - 工作或学校帐户可以与用于通过 Azure EA 门户访问合约的帐户相同或不同。
 1. 在服务仪表板上，选择“Microsoft Azure Enterprise”，然后单击“连接”   。
 1. 在“连接到 Azure Enterprise”屏幕上，选择：
    - Azure 环境 URL：[https://ea.azure.com](https://ea.azure.com/)。
    - 月数：在 1 到 36 之间选择一个。
    - 合约编号：输入合约编号。
    - 单击“下一步”。 
 1. 在“身份验证密钥”框中输入 API 密钥。 可以在 Azure Enterprise Portal 中获取 API 密钥，方式是在“下载使用情况”选项卡中单击“API 访问密钥”
    - 将该密钥复制并粘贴到“帐户密钥”框中
 1. 在 Power BI 中加载数据大约需要 5 到 30 分钟，具体取决于数据集的大小。

Power BI 报表适用于 EA 直接合作伙伴以及有权查看账单信息的间接客户。

## <a name="report-faq"></a>报表 FAQ

本文的此部分回答与报表解读相关的常见问题。

### <a name="why-is-my-cost-showing-as-0"></a>为什么我的成本显示为 $0？

**直接合约** 如果你是帐户所有者或部门管理员，请联系 EA 管理员启用定价功能：

1. 在左侧导航区域中单击“管理”  。
1. 单击 DA（部门管理员）查看费用旁边的蓝色铅笔符号。
1. 选择“启用”并保存  。
1. 单击 AO（帐户所有者）查看费用旁边的蓝色铅笔符号。
1. 选择“启用”并保存  。

此操作会向帐户所有者和部门管理员提供对使用情况报表中成本/定价信息的访问权限。

**间接合约** 请咨询合作伙伴是否已为你启用了定价功能。 此操作只能由合作伙伴完成，一旦他们启用了此功能，你就可以以 EA 管理员的身份查看你的登记的成本和定价。

如果希望为帐户所有者和部门管理员启用“查看费用”功能，请按照上面的“直接合约”下所列的步骤操作  。

### <a name="there-is-no-sku-information-on-the-usage-detail-report"></a>使用情况详细信息报表中没有 SKU 信息

使用情况详细信息报表不含 SKU 信息；但你可以在该报表中查看与使用的服务相关的信息。 然后，可以下载价目表报表以获取 SKU 信息。

### <a name="the-total-amount-on-marketplace-does-not-match-on-usage-summary-and-csv-report"></a>市场中的总额与使用情况摘要和 CSV 报表不匹配

市场费用报表仅显示基于用量的市场费用。 不显示一次性费用。 可以查看使用情况摘要页，了解有关基于用量的费用和一次性费用的最新用量。

### <a name="there-is-no-information-on-my-api-report"></a>我的 API 报表上没有任何信息

API 密钥每六个月过期。 如果遇到问题，请生成一个新的 API 密钥。 务必请求 EA 管理员生成新 API 密钥并安装 API 报表 FAQ 中的步骤操作。

### <a name="my-power-bi-report-isnt-working"></a>我的 Power BI 报表无法正常使用

有关 Power BI 的问题，请在 [https://support.powerbi.com](https://support.powerbi.com) 向 Power BI 团队开一阵技术工单，以便获得团队的帮助。

### <a name="my-resource-tags-arent-showing-up-on-my-reports"></a>我的报表上不显示资源标记

资源标记托管在 Azure 门户上。 你可以通过 [https://portal.azure.com](https://portal.azure.com) 联系 Azure 订阅团队。 请按照[本链接](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)（关于提出支持请求）中的步骤操作。

### <a name="why-does-my-resource-rate-change-every-day"></a>为什么资源费率每天都在变化？

详细使用情况报表中的资源费率是计算所得的值，体现的是对某项服务收取的月平均费率。 此费率的计算方式是取一个服务单位的每月承诺和每月超额费用的平均值。 在月结算日前按承诺和超额费率收费的这部分用量不断变化。 因此，当月内的资源费率也会随之变化。 资源费率在月末之后的第五日锁定。

### <a name="glossary-of-processes-for-calculating-the-resource-rate"></a>用于计算资源费率的过程的术语表

原始单位总数  ：在详细使用情况报表中的已使用数量。
每单位 MOCP 资源  ：上游使用情况系统使用不同的单位为每项服务发布使用情况。 （预设）每单位消耗量  ：EA 度量单位。 （预设）价格  ：Azure EA 门户中的单价。
总成本  ：详细使用情况报表中的总成本或者承诺使用量和超额使用量之和。


### <a name="charges-calculation"></a>费用计算

**换算为每单位 MOCP 资源** = = ROUND(Total RAW Units * MOCP Resource Per Unit,4) **单位** = 转换为每单位 MOCP 资源/每单位消耗之后的单位 **消耗量** = 单位 * 价格

### <a name="download-usage-calculation-logic"></a>下载用量计算逻辑

**资源费率** = 总成本/（原始单位总数/每单位 MOCP 资源）

资源费率根据费用而来，往往和价目表中的实际单价不符。

就超额费用计算而言，如下载使用情况数据报表中所见，原始资源使用量数据在报表中最多取六位小数。 但 Azure EA 门户将用于承诺单位的使用量舍入为四位小数，将用于超额单位的使用量截为零位小数。 也就是说在 Azure EA 门户下，对于计作超额收费的所有用量，我们只收取整单位的费用。 对于计作超额收费或混合几个月收费的用量，单价和资源费率大不相同。

## <a name="next-steps"></a>后续步骤
- 有关发票和费用的信息，请参阅[了解 Azure 企业协议帐单](billing-understand-your-bill-ea.md)。
- 若要开始使用 Azure EA 门户，请参阅 [Azure EA 门户入门](billing-ea-portal-get-started.md)。
