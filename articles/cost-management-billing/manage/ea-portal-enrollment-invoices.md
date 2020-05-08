---
title: Azure Enterprise 注册发票
description: 本文介绍如何管理和处理 Azure 企业发票。
author: bandersmsft
ms.author: banders
ms.date: 05/01/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: 6f654e0b043cfc9f5a90fb84156269fecfbff5bf
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691196"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Azure Enterprise 注册发票

本文介绍如何管理和处理 Azure 企业协议 (Azure EA) 发票。 发票代表帐单。 请检查发票以确保其准确。 你还应该熟悉管理发票时可能需要执行的其他任务。

## <a name="change-a-po-number-for-an-overage-invoice"></a>更改超额发票的 PO 编号

Azure Enterprise 门户会自动生成默认的采购订单 (PO) 编号，除非企业管理员在开票日期之前设置一个编号。 在收到自动发送的发票通知电子邮件后的最多 7 天内，企业管理员可以更新 PO 编号。

### <a name="to-update-the-azure-services-purchase-order-number"></a>更新 Azure 服务采购订单编号：

1. 在 Azure Enterprise 门户中，选择“报表” > “使用情况摘要”。  
1. 在右上角选择“编辑 PO 编号”。 
1. 选择“Azure 服务”单选按钮  。
1. 从日期范围下拉菜单中选择一个“发票期间”  。

   在收到发票通知之后、支付发票金额之前的 7 天内可以编辑 PO 编号。
1. 在“PO 编号”  字段中输入新的 PO 编号。
1. 选择“保存”以提交更改  。

### <a name="to-update-the-azure-marketplace-purchase-order-number"></a>若要更新 Azure 市场采购订单编号：

1. 在 Azure Enterprise 门户中，选择“报表” > “使用情况摘要”。  
1. 在右上角选择“编辑 PO 编号”。 
1. 选中“市场”单选按钮。 
1. 从日期范围下拉菜单中选择一个“发票期间”  。

   在收到发票通知之后、支付发票金额之前的 7 天内可以编辑 PO 编号。
1. 在“PO 编号”  字段中输入新的 PO 编号。
1. 选择“保存”以提交更改  。

## <a name="cadence-of-azure-enterprise-billing"></a>Azure Enterprise 计费频率

Microsoft 每年在注册生效日期，针对 Microsoft Azure 服务的任何承诺购买量计费。 对于超出承诺金额的任何用量，Microsoft 将以欠款的形式计费。

- 承诺费用是根据每月费率报出的，每年提前计费。
- 超额费用按月计算，在计费周期结束时以欠款形式计费。

### <a name="billing-intervals"></a>计费时间间隔

计费时间间隔取决于承诺购买的选择方式。 年度承诺的日期范围与以下任何一项相一致：

- 注册周年日期
- 一年期修正订阅的生效日期。

收到超额发票的日期取决于注册开始日期和设置：

- **开始日期在 2018 年 5 月 1 日之前的直接合约**：
  - 如果你签署的是直接企业协议 (EA)，则 Azure 服务（不包括 Azure 市场服务）采用每年计费周期。 计费周期基于周年日期：协议生效的日期。
  - 如果超过了 EA 货币承诺 (MC) 阈值的 150%，则会自动转换为基于周年日期的每季计费周期。 此外，你还会收到 Azure 服务超额发票。
  - 如果未超过 MC 阈值的 150%，则注册将保持采用每年计费周期。 承诺年份结束时，你将收到超额发票。

- **开始日期在 2018 年 5 月 1 日之后的直接合约**：
  - Azure 使用量和单独计费发票采用每月计费周期。
  - 货币承诺未涵盖的任何费用计为应付的超额款项。  

- **登记开始日期在 2018 年 5 月 1 日之前的间接合约**：

  如果你是在开始日期 2018 年 5 月 1 日之前签署间接企业协议 (EA) 的客户，则系统已为你设置每季计费周期。 渠道合作伙伴 (CP) 将直接向你开票。  

- **开始日期在 2018 年 5 月 1 日之后的间接合约**：

  采用每月计费周期。  

### <a name="increase-your-monetary-commitment"></a>提高货币承诺

随时可以提高承诺。 将按该年份的承诺期限的剩余月数计费。 例如，如果你注册的是一年期的修订订阅并在六月增加了承诺使用量，则系统将按当年剩下的六个月对你开票。 还将更新承诺期内最后六个月的承诺使用量。 这些新数量用于确定任何超额费用。

### <a name="overage"></a>超额

如果超额，将对计费周期内超出承诺使用量的使用量或预留使用量计费。 如果要查看为各项计算超额用量的明细，请参阅使用情况摘要报表或联系自己的渠道合作伙伴。

对于发票上的每个项，你将看到：

- **应付金额**：总费用
- **承诺用量**：用于涵盖费用的承诺量
- **净额**：超出承诺的费用

仅对超出承诺的净额计算适用的税款。

系统自动执行超额开票。 发出通知和开具发票的时间取决于计费周期的结束日期。

- 通常在客户计费结束日期后的 7 日内发送超额通知。
- 在发送通知后的 7 到 9 天内发送超额发票。
- 在发送超额通知之后、开票之前的 7 天内，可以查看费用和更新系统生成的 PO 编号。

### <a name="azure-marketplace"></a>Azure 市场

自 2019 年 4 月计费周期起，客户将开始收到一份 Azure 发票，它将所有 Azure 和 Azure 市场的费用合并到一份发票中（而不是分别使用两份单独的发票）。 此项更改不影响澳大利亚、日本或新加坡的客户。

在向合并发票过渡期间，你将收到一份不完整的 Azure 市场发票。 这份单独的最终发票显示计费更新日期之前产生的 Azure 市场费用。 该日期以后产生的 Azure 市场费用显示在 Azure 发票上。 过渡期结束后，所有 Azure 费用和 Azure 市场费用均显示在合并发票上。  

## <a name="adjust-billing-frequency"></a>调整计费频率

客户的计费频率为每年、每季或每月。 计费周期是客户签署协议时确定的。 每月计费是最短的计费间隔。

- 将直接注册的计费周期从每年更改为每季需要经过企业管理员的**批准**。 对于间接注册，需要合作伙伴管理员的批准。 更改将在当前计费季度结束时生效。
- 若要将计费周期从每年或每季更改为每月，需要对协议进行**修正**。  对现有注册计费周期进行任何更改都需要经过企业管理员的批准，或者经过“接收帐单的联系人”的批准。
- 将审批内容**提交**给 [Azure Enterprise 门户支持人员](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)。 选择问题类别：“计费和开票”。 

更改将在当前计费季度结束时生效。

如果已签署“协议修订 M503”，则可以将任何协议的任何计费频率改为每月计费。 

## <a name="credits-and-adjustments"></a>额度和调整

可以在 [Azure Enterprise 门户](https://ea.azure.com)的“报表”部分查看应用于注册的所有额度或调整。 

若要查看额度：

1. 在 [Azure Enterprise 门户](https://ea.azure.com)中选择“报表”部分。 
1. 选择“使用情况摘要”。 
1. 在右上角将 M 视图更改为 C 视图   。
1. 扩展 Azure 服务承诺表中的调整字段。
1. 你将看到应用于你的注册的额度和简短说明。 例如：服务级别协议额度。

## <a name="request-an-invoice-copy"></a>请求发票副本

若要请求发票副本，请与合作伙伴联系。

## <a name="overage-offset"></a>超额抵销

若要向超额应用货币承诺，必须符合以下条件：

- 产生的超额费用尚未支付，并且付款期限在计费服务结束日期后的一年之内。
- 可用货币承诺金额涵盖产生的总费用，包括以往未付的所有 Azure 发票。
- 要完成的计费条款必须完全了结。 计费将在每个月的五号之后完全了结。
- 要抵销的计费周期必须完全了结。
- Azure 承诺折扣 (ACD) 基于实际的新承诺金额减去根据上次消耗量计划的任何资金。 此项要求仅适用于产生的超额费用。 它只对消耗货币承诺的服务有效，因此不适用于 Azure 市场费用。 Azure 市场费用单独计收。

若要完成超额抵销，你或帐户团队可以提出支持请求。 需要经过企业管理员或“接收帐单的联系人”的电子邮件批准。

## <a name="view-price-sheet-information"></a>查看价目表信息

企业管理员可以查看与其 Azure 服务注册关联的价目表。

若要查看当前价目表：

1. 在 Azure Enterprise 门户中，依次选择“报表”、“价目表”。  
2. 查看价目表，或选择“下载”。 

![显示价目表信息的示例](./media/ea-portal-enrollment-invoices/ea-create-view-price-sheet-information.png)

若要下载历史价目表：

1. 在 Azure Enterprise 门户中，依次选择“报表”、“下载使用情况”。  
2. 下载价目表。

![显示旧价目表下载位置的示例](./media/ea-portal-enrollment-invoices/create-ea-view-price-sheet-download-historical-price-list.png)

定价出现差异的部分原因：

- 上一个注册与新注册的定价可能已更改。 由于从协议的开始日期到结束日期，定价对于特定注册是契约性的，因此价格可能会发生变化。
- 转移到新注册后，新协议的定价发生变化。 定价由价目表定义，因此，新注册的价格可能更高。
- 如果注册进入到延长期，定价也会改变。 价格将更改为即用即付费率。

## <a name="request-detailed-usage-information"></a>请求详细的使用情况信息

企业管理员可以查看对其使用情况数据、使用的货币承诺以及与 Azure Enterprise 门户中其他使用量相关的费用的摘要。 费用以摘要形式显示，适用于所有帐户和订阅。

若要查看特定帐户中的详细使用情况，请转到“报表” > “下载使用情况”来下载使用情况详细信息报表。  

> [!NOTE]
> 使用情况详细信息报表不包含任何适用的税费。 从使用服务开始算起，最长可能需要在延迟八小时之后，其费用才会反映在报表中。

对于间接注册，合作伙伴需要启用标记功能，然后你才能看到任何成本相关的信息。

## <a name="reports"></a>报表

企业管理员可以查看对其使用情况数据、使用的货币承诺以及与 Azure Enterprise 门户中其他使用量相关的费用的摘要。 费用以摘要形式显示，适用于所有帐户和订阅。

### <a name="azure-enterprise-reports"></a>Azure Enterprise 报表

- 使用情况摘要和关系图
- 服务使用情况报表
- 余额和费用报表
- 使用情况详细信息报表
- Azure 市场费用报表
- 价目表
- 高级报表下载
- CSV 报表格式

### <a name="to-view-the-usage-summary-reports-and-graphs"></a>查看使用情况摘要报表和关系图：

1. 转到 Azure Enterprise 门户。
1. 在左侧窗格中选择“报表”。 
1. 选择“使用情况摘要”选项卡。 
1. 从左上方的日期范围菜单中选择承诺期限。
1. 在图表上选择某个时段或月份可以查看更多详细信息。
1. 在此选项卡上，可以：
   - 查看显示了每月使用情况的图表，其中细分了使用量、服务额外费用、单独计收的费用和 Azure 市场费用。
   - 在图表下方按部门、帐户和订阅进行筛选。
   - 在“按服务列出的费用”明细与“按层次结构列出的费用”明细之间切换。  
   - 查看 Azure 服务详细信息、单独计费的费用以及 Azure 市场费用。

## <a name="service-usage-report"></a>服务使用情况报表

企业管理员可以在服务使用情况报表页中查看其服务使用情况数据的摘要。 使用情况以摘要形式显示，适用于所有帐户和订阅。 若要查看详细使用情况，可按帐户或订阅筛选报表。

> [!NOTE]
> 在使用日和具体使用情况体现在该报表上日期之间，可能存在最多 5 日的延迟。

### <a name="to-view-the-report"></a>查看报表：

1. 登录到 Azure Enterprise 门户。
1. 在左侧导航区域中选择“报表”。 
1. 选择“使用情况摘要”选项卡。 
1. 选择日期范围。
1. 选择要查看的帐户或订阅。
1. （可选）可以：
   - 将视图更改为“按服务列出费用”或“按层次结构列出费用”，以显示不同的明细。  
   - 查看“服务名称”、“度量单位”、“已用单位数”、“生效费率”和“总成本”的详细信息。

## <a name="download-csv-reports"></a>下载 CSV 报告

企业管理员可以在每月报表下载页中以 CSV 文件的形式下载多份报表。 可下载的报表包括：

- 余额和费用报表
- 使用情况详细信息报表
- Azure 市场费用报表
- 价目表

### <a name="to-download-reports"></a>若要下载报告：

1. 在 Azure Enterprise 门户中选择“报表”。 
1. 从顶部功能区中选择“使用情况下载”  。
1. 选择相应月份报表旁边的“下载”  。

### <a name="csv-report-formatting-issues"></a>CSV 报表格式问题

查看 Azure Enterprise 门户中以欧元为单位的 CSV 报表的客户可能会遇到与逗号和句点相关的格式问题。

例如，你可能会看到：

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| 小时 | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

应会看到：

| ServiceResource | ResourceQtyConsumed | ResourceRate | ExtendedCost |
| --- | --- | --- | --- |
| 小时 | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

此格式问题是 Excel 导入功能的默认设置造成的。 Excel 将所有字段都作为“常规”文本导入，并按数学标准分隔数字。 例如：1,000.00。

如果欧洲货币将句点 (.) 用作为千分位符、将逗号用作小数分隔符 (,)，则无法正确显示。 例如：“1.000,00”。 导入结果根据区域语言设置而异。

### <a name="to-import-the-csv-file-without-formatting-issues"></a>若要导入 CSV 文件且避免出现格式问题：

1. 在 Microsoft Excel 中，转到“文件” > “打开”。  
   系统显示“文本导入向导”。
1. 在“原始数据类型”下，选择“带分隔符”。    默认使用“固定宽度”  。
1. 选择“**下一页**”。
1. 在“分隔符”下，选中“逗号”对应的复选框。  清除“制表符”（如果已选中）。 
1. 选择“**下一页**”。
1. 滚动到“ResourceRate”和“ExtendedCost”列。  
1. 选择“ResourceRate”列。  此列以黑色突出显示。
1. 在“列数据格式”部分下，选择“文本”而不是“常规”。    列标题将从“常规”更改为“文本”。  
1. 对“ExtendedCost”列重复步骤 8 到 9，然后选择“完成”。  

> [!TIP]
> 若要将 CSV 文件设置为在 Excel 中自动打开，必须改用 Excel 中的“打开”功能。  在 Excel 中，转到“文件” > “打开”。  

## <a name="balance-and-charge-report"></a>余额和费用报表

余额和费用报表提供了关于余额、新购买、Azure 市场服务费用、调整和超额费用信息的每月摘要。

Azure 服务承诺摘要报表中的所有行每月都保持不变。 在“新购买详细信息”和“调整详细信息”部分可以找到所有购买和调整的详细信息。  

### <a name="download-the-balance-and-charge-report"></a>下载余额和费用报表

1. 以企业管理员身份登录到 Azure Enterprise 门户。
1. 在左侧窗格中选择“报表”。 
1. 选择“报表下载”选项卡。 
1. 在“余额和费用”列下选择相应月份，然后下载报表。 

## <a name="usage-detail-report"></a>使用情况详细信息报表

使用情况详细信息报表提供注册所用服务和数量的每月摘要。 其中包括有关计量器名称、计量器类型和使用数量的信息。

### <a name="download-the-usage-detail-report"></a>下载使用情况详细信息报表

1. 以企业管理员身份登录到 Azure Enterprise 门户。
1. 在左侧导航区域中选择“报表”。 
1. 选择“下载使用情况”选项卡。 
1. 在“使用情况详细信息”列下选择相应月份，然后下载报表。 

## <a name="azure-marketplace-charges-in-azure-enterprise-portal-reports"></a>Azure Enterprise 门户报表中的 Azure 市场费用

Azure 市场费用有以下两种类型：

- **基于用量：** 以事务单位度量的产品。  例如，虚拟机按小时收费，必应 API 搜索按搜索数量收费。
- **每月费用：** 根据使用量或访问量每月计费。

有关 Azure 市场费用的详细信息，请参阅 [Azure 市场常见问题解答](https://azure.microsoft.com/marketplace/faq/)。

若要在 Azure Enterprise 门户中查看不同的费用：

- **使用情况摘要报表**：显示基于用量的费用**和**每月 Azure 市场费用。
- **市场费用报表**：**仅**显示基于用量的 Azure 市场费用。  不显示一次性费用。

> [!NOTE]
> Azure 市场不适用于 MPSA 注册。

## <a name="advanced-report-download"></a>高级报表下载

如需特定日期范围或帐户的报表，可以使用高级报表下载功能。 从 2016 年 8 月 30 日起，输出文件的格式为 CSV，以容纳更大的记录集。

1. 在 Azure Enterprise 门户中选择“高级报表下载”。 
1. 选择适当的日期范围  。
1. 选择适当的帐户  。
1. 选择“请求使用情况数据”  。
1. 选择“刷新”按钮，直到报表状态更新为“下载”。  
1. 下载报表。

## <a name="reporting-for-non-enterprise-administrators"></a>适用于非企业管理员的报表功能

企业管理员可为部门管理员 (DA) 和帐户所有者 (AO) 授予查看注册中的费用的权限。 拥有访问权限的帐户所有者可以下载特定于其帐户和订阅的 CSV 报表。 他们还可以直观查看 Azure Enterprise 门户的报表部分下的信息。

### <a name="to-enable-access"></a>启用访问权限：

 1. 以企业管理员身份登录到 Azure Enterprise 门户。
 1. 在左侧导航区域中选择“管理”。 
 1. 选择“注册”选项卡。 
 1. 在“注册详细信息”部分下，选择位于“DA 查看费用”或“AO 查看费用”旁边的铅笔图标。   
 1. 选择“启用”。 
 1. 选择“保存”。 

### <a name="to-view-reports"></a>查看报表：

1. 以部门管理员或帐户所有者的身份登录到 Azure Enterprise 门户。
1. 在左侧导航区域中选择“报表”。 
1. 选择“使用情况摘要”选项卡，以直观查看有关帐户和订阅的信息。 
1. 选择“使用情况下载”查看 CSV 报表。 

使用“高级报表下载”功能时，部门管理员和帐户所有者无法查看费用  。 费用显示为 $0。

帐户所有者查看费用的权限扩展到帐户所有者以及对关联订阅拥有权限的所有用户。 如果你是间接客户，必须由渠道合作伙伴来为你启用成本功能。

## <a name="move-usage-data-to-another-enrollment"></a>将使用情况数据移到另一个注册

只有在将转移操作的日期调后时，才会移动使用情况数据。 可通过两个选项将使用情况数据从一个注册移到另一个注册：

- 将帐户从一个注册转移到另一个注册
- 将注册从一个注册转移到另一个注册

使用任一选项都必须向 EA 支持团队提交[支持请求](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)以获得帮助。 

## <a name="azure-ea-pricing-overview"></a>Azure EA 定价概述

本部分提供有关用量计算方式的详细信息。 其中解答了与企业协议中各项 Azure 服务的费用（其计算较为复杂）相关的常见问题。

### <a name="price-protection"></a>价格保护

我们保证客户或渠道合作伙伴收到的价格不高于客户价目表 (CPS) 中显示的价格，或购买 Azure 产品/服务的生效日期当日的价格。 此价格称作基准价格。 对于购买 Azure 产品/服务后引入的服务，将按首次引入服务时应用了相应级别折扣的价格计费。 此价格保护策略在承诺期（一年或三年，具体取决于企业协议）内适用。

### <a name="price-changes"></a>价格更改

Microsoft 可能会在注册有效期内降低当前企业协议中个别 Azure 服务的价格。 较低的价格生效时，我们将把这些降低后的费率扩展到现有客户。 如果后来提高了这些费率，如上所述，注册的价格也不会提高到高于价格保护上限。 但是，与之前降低的价格相比可有所提高。 不管是哪一种情况，我们都会向与注册相关的企业和合作伙伴管理员发送电子邮件，将即将发生的价格更改事宜告诉客户和间接渠道合作伙伴。

### <a name="current-effective-pricing"></a>当前有效定价

客户和渠道合作伙伴可以登录到 [Azure Enterprise 门户](https://ea.azure.com/)并查看注册的价目表页，来查看该注册的当前定价。 如果通过我们的渠道合作伙伴之一间接购买 Azure，则需要通过渠道合作伙伴获取定价更新，除非他们尚未为注册启用显示加成定价功能。

### <a name="introduction-of-new-azure-services"></a>新 Azure 服务简介

我们正在不断地改善 Azure，定期增加与现有服务分开定价的新服务。 某些预览版服务是自动提供的，其他服务则需要客户在 [Azure 帐户门户](https://account.windowsazure.com/PreviewFeatures)中执行操作才能使用。

某些服务最初可能应用其首次引入时的促销价，但后来提高了价格。

服务从预览版变为正式版 (GA) 后，由于应用了完整的性能和可靠性 SLA，价格可能会提高。 此类提价不受普通基准价格保护的约束。 使用这些服务将按提高的费率计费。 若要避免与这些新服务相关的费用，必须选择退出这些服务。

### <a name="introduction-of-regional-pricing"></a>区域定价简介

除了引入新服务外，随着对服务的支持的增加，还会定期将这些服务从单一的全局模型更改为更具区域性的模型。

首次引入某服务的区域化时，基准价格保护适用于这些新区域。 但是，如果之后引入了相同服务的其他区域，则会将该服务视作新服务，并按其各自独立于基准价格保护之外的费率提供。

### <a name="enterprise-devtest"></a>Enterprise 开发/测试

企业管理员可以使帐户管理员能够基于 Enterprise 开发/测试套餐创建订阅。 帐户所有者必须设置底层订阅者所需的 Enterprise 开发/测试订阅。 此配置使有效的 Visual Studio 订阅者能够按 Enterprise 开发/测试优惠费率在 Azure 上运行开发和测试工作负荷。 有关详细信息，请参阅[企业开发/测试](https://azure.microsoft.com/offers/ms-azr-0148p/)。

## <a name="enterprise-agreement-usage-calculations"></a>企业协议用量计算

请参阅 [Azure 服务](https://azure.microsoft.com/services/)和 [Azure 定价](https://azure.microsoft.com/pricing/)了解各项服务的基本公开定价信息、度量单位、常见问题解答和使用情况报表指导。 可在以下部分找到有关 EA 计算的详细信息。

### <a name="enterprise-agreement-units-of-measure"></a>企业协议度量单位

企业协议的度量单位通常不同于其他计划，例如 Microsoft Online Services 协议计划 (MOSA)。 这种区分意味着，对于许多服务而言，合计度量单位是为了提供规范化的定价。 Azure Enterprise 门户的“使用情况摘要”视图中显示的度量单位始终为 Enterprise 度量。 [友好服务名称](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) Excel 文件中提供了每项服务的当前度量单位和换算的完整列表。

### <a name="rounding-rules"></a>舍入规则

在 Azure Enterprise 门户遵循 IEEE 标准银行家舍入法或高斯舍入法逻辑。 遇到数字 5 时，此逻辑将其舍入为最接近的偶数。 而更典型的四舍五入逻辑始终将数字 5 向上舍入为下一个最大的数字。 与标准的 Excel 逻辑相比，此 Azure Enterprise 门户方法实际提供的一组数字的总和更准确。

演示：如果要去除的第一个数字是 5 且它后面没有其他数字或者它后面的数字为 0，则将这组数字舍入为最接近的偶数。 例如，2.315 和 2.325 在舍入为最接近的百分位数时，均为 2.32。

下表显示了为 Azure Enterprise 门户中的舍入和转换规则建模时可使用的 Excel 公式供你参考：

| 场景 | 银行从业者逻辑公式 |
| --- | --- |
| 舍入用法 | =MROUND({_source_}, 0.0002) |
| 舍入定价（2 位小数） | =MROUND({_source_}, 0.02) |
| 舍入定价（0 位小数） | =MROUND({_source_}, 2) |

### <a name="conversion-between-usage-detail-report-and-the-usage-summary-page"></a>使用情况详细信息报表与使用情况摘要页之间的转换

在“下载使用情况数据”报表中可以看到，原始资源用量数据最多取六位小数。 但是，Azure Enterprise 门户中显示的使用情况数据舍入为四位小数，将用于超额单位的用量截断为零位小数。 在转换为 Azure 门户中使用的单位之前，原始用量数据首先舍入为四位数。 然后，转换后的 Enterprise 单位也舍入为四位数。 只能在“下载使用情况”报表中看到转换之前实际使用的时数，而在 Azure Enterprise 门户中则看不到。

例如：如果使用情况报表中报告的实际 SQL Server 时数为 694.533404。 这些单位被转换为 6.94533404 个单位的计算时间（每个单位 100 小时），然后舍入为 6.9453 并显示在 Azure Enterprise 门户中。

- 若要确定计费总额，可将显示的单位数乘以承诺单价，结果将截断为两位小数。 对日元 (JPY) 和韩元 (KRW)，则将该总额舍入为零位小数（即整数）。
- 对于超额，计费单位将截断为六位数，然后乘以超额单价，得出计费总额。
- 对于托管服务提供商 (MSP) 帐单，在转换为 EA 度量单位后，将与标记为 MSP 的部门相关的所有用量取整（零位小数）。 因此，此用量的总和可能低于 Azure Enterprise 门户中报告的所有用量的总和， 具体取决于 MSP 是尚有货币承诺余额还是已经超额。

### <a name="graduated-pricing"></a>分级定价

企业协议定价目前不包括分级定价（定价随用量的增加而降低）。 从具有分级定价的 MOSA 计划转移到企业协议时，根据企业协议折扣调整后，定价与服务基本层相当。

### <a name="partial-hour-billing"></a>不足一小时的计费

所有计费用量基于转换为部分小时数的分钟数（而不是按整小时的增量）。 按小时列出的 Enterprise 费率应用于总小时数加上部分小时数。

### <a name="average-daily-consumption"></a>平均日使用量

某些服务按月定价，但用量按日报告。 在这种情况下，将每日计算用量，将其除以 31，再根据该计费月内的天数求和。 因此，对于不足 31 日的月份，费率始终不会高于（而是略低于）任何月份的预期费率。

### <a name="compute-hours-conversion"></a>计算小时数的转换

在 2016 年 1 月 28 日之前，A0、A2、A3 与 A4 标准和基本虚拟机及云服务的用量以 A1 虚拟机计量分钟数发出。 A0 VM 统计为 A1 VM 分钟数的分数，而 A2、A3 和 A4 VM 则转换为倍数。 由于此策略给我们的客户造成了某些困扰，因此我们实行了更改，将每分钟用量分配给专用的 A0、A2、A3 和 A4 计量器。

新计量方式于 2016 年 1 月 27 日到 1 月 28 日之间生效。 在过渡期间，在显示用量的 CSV 下载内容中，可以看到以下两项：

- 在 A1 计量器上发出的用量
- 在与部署大小对应的新专用计量器上发出的用量。

| **部署大小** | **2016 年 1 月 26 日当日，用量按 A1 的倍数发布** | **自 2016 年 1 月 27 日起，按专用计价器发布用量** |
| --- | --- | --- |
| A0 | 0.25 个 A1 小时 | 1 个 A0 小时 |
| A2 | 2 个 A1 小时 | 1 个 A2 小时 |
| A3 | 4 个 A1 小时 | 1 个 A3 小时 |
| A4 | 8 个 A1 小时 | 1 个 A4 小时 |

### <a name="regions"></a>区域

对于定价受区域影响的服务，请参阅下表来了解地理位置和区域之间的映射：

| 地域 | 数据传输区域 | CDN 区域 |
| --- | --- | --- |
| 区域 1 | 北欧 <br> 西欧 <br> 美国西部 <br> 美国东部 <br> 美国中北部 <br> 美国中南部 <br> 美国东部 2 <br> 美国中部 | 北美 <br> 欧洲 |
| 区域 2 | 亚太东部 <br> 亚太东南部 <br> 日本东部 <br> 日本西部 <br> 澳大利亚东部 <br> 澳大利亚东南部 | 亚太区 <br> 日本 <br> 拉丁美洲 <br> 中东/非洲 <br> 澳大利亚东部 <br> 澳大利亚东南部 |
| 区域 3 | 巴西南部 |   |

对于驻留在同一数据中心内的服务之间的数据传出不收取任何费用。 例如 Office 365 和 Azure。

### <a name="monetary-commitment-and-unbilled-usage"></a>货币承诺和未开票用途

Azure 货币承诺是提前为 Azure 服务支付的金额。 使用服务时，会消耗货币承诺。 第一方 Azure 服务根据货币承诺计费。 但是，某些费用是单独计收的，而 Azure 市场服务不消耗货币承诺。

### <a name="charges-billed-separately"></a>单独计费

某些由第三方来源提供的产品和服务不使用 Azure 货币承诺。 将这些项作为标准计费周期的超额发票的一部分单独计费。

我们已将所有 Azure 和 Azure 市场费用合并到一份与注册的计费周期相符的发票中。 合并发票不适用于澳大利亚、日本或新加坡的客户。

合并发票首先显示 Azure 用量，后接任何 Azure 市场费用。 澳大利亚、日本或新加坡的客户可在单独的发票上查看 Azure 市场费用。

如果在标准计费周期结束时没有超额的用量，则对单独计费的费用单独开票。 此过程适用于澳大利亚、日本和新加坡的客户。

以下服务单独计费：

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

## <a name="what-to-expect-after-change-of-channel-partner"></a>更改渠道合作伙伴后会发生什么

如果在月中更改了渠道合作伙伴 (COCP)，则客户会收到两份发票，一份对应于上一个关联合作伙伴的使用情况，另一份对应于新合作伙伴的使用情况。

发票将在计费周期结束后的一个月发出。 如果按月计费，则会在 10 月为两个合作伙伴发出 9 月份的发票。 如果计费周期为一季或一年，则客户应收到一份针对上一个关联合作伙伴的使用情况的发票，以及其余针对新合作伙伴使用情况的发票，收到发票的时间取决于计费频率。

## <a name="azure-marketplace-for-ea-customers"></a>面向 EA 客户的 Azure 市场

直接客户可以在 Azure Enterprise 门户上看到 Azure 市场费用。 Azure 市场购买和消耗内容将在货币承诺以外按季或按月以欠款的形式计费。

间接客户可以在 Azure Enterprise 门户的“管理订阅”页上找到其 Azure 市场订阅，但定价处于隐藏状态。  客户应该联系其授权解决方案提供商 (LSP) 了解有关 Azure 市场费用的信息。

对于每月或每年新的重复性 Azure 市场购买，在购买 Azure 市场项时将按周期全额计费。 这些项目会在原始购买的同一日在以下期间自动更新。

现有的每月重复费用将在每个日历月的第一日继续更新。 年度费用则在购买周年日更新。

Azure 市场提供的某些第三方经销商服务现在使用企业协议 (EA) 货币承诺余额。 这些服务以前不遵照 EA 货币承诺进行计费并且单独开具发票。 Azure 市场中这些服务采用 EA 货币承诺付款可帮助客户简化购买和支付管理过程。 如需现在使用货币承诺的服务的完整列表，请参阅 [2018 年 3 月 6 日的 Azure 网站更新内容](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/)。

### <a name="partners"></a>合作伙伴

LSP 可以从 Azure Enterprise 门户中的价目表页下载 Azure 市场价目表。 在右上角选择“市场价目表”链接。  Azure 市场价目表显示所有可用服务及其价格。

若要下载价目表：

1. 在 Azure Enterprise 门户中，转到“报表”>“价目表”。   >  
1. 在右上角你的用户名下找到 Azure 市场价目表的链接。
1. 右键单击该链接，并选择“将目标另存为”。 
1. 在“保存”窗口中，将文档的标题更改为 `AzureMarketplacePricelist.zip`，这会将该文件的格式由 .xlsx 更改为 .zip。 
1. 下载完成后，你会得到一个包含特定于国家/地区的价目表的 zip 文件。
1. LSP 应参考各个国家/地区文件中国家/地区特定的定价。 LSP 可以使用“通知”选项卡来了解全新的或已停用的 SKU。 
1. 价格不会频繁变化。 在价格提高和外汇 (FX) 发生变化之前的 30 天，LSP 会收到电子邮件通知。
1. LSP 每季度每个 ISV 的每个注册都会收到一份发票。

### <a name="enabling-azure-marketplace-purchases"></a>启用 Azure 市场购买

企业管理员可为其注册下的所有 Azure 订阅禁用或启用 Azure 市场购买功能。 如果企业管理员禁用购买且 Azure 订阅已经有了 Azure 市场订阅，这些 Azure 市场订阅不会被取消或不会受到影响。

尽管客户可将自己的直接 Azure 订阅转换为 Azure EA 订阅（通过将直接订阅关联到 Azure Enterprise 门户中的注册），但此操作不会自动转换子订阅。

若要启用 Azure 市场购买：

1. 以企业管理员身份登录到 Azure Enterprise 门户。
1. 转到“管理”。 
1. 在“注册详细信息”下，选择“Azure 市场”行项旁边的铅笔图标。  
1. 相应地切换为“启用/禁用”或“仅免费 BYOL SKU”。  
1. 选择“保存”。 

> [!NOTE]
> “BYOL (自带许可)和仅免费”选项会将购买和收购 Azure 市场 SKU 限制为仅 BYOL 和免费 SKU。

获取有关 [Azure Enterprise 门户中的 Azure 市场费用](#azure-marketplace-charges-in-azure-enterprise-portal-reports)的详细信息。

### <a name="services-billed-hourly-for-azure-ea"></a>Azure EA 按小时计费的服务

以下服务按企业协议计费，而不是按 MOSP 中的每月费率计费：

- 应用程序传送网络
- Web 应用程序防火墙

### <a name="azure-remoteapp"></a>Azure RemoteApp

企业协议客户根据其企业协议价格水平为 Azure RemoteApp 付费。 无需额外付费。 标准价格包括初始 40 小时。 无限制价格包括初始 80 小时。 RemoteApp 在 80 小时后停止发出用量。

## <a name="azure-marketplace-faq"></a>Azure 市场常见问题解答

本部分解释 Azure 货币承诺如何应用到 Azure 市场中的某些第三方经销商服务。

### <a name="what-changed-with-azure-marketplace-services-and-ea-monetary-commitment"></a>Azure 市场服务和 EA 货币承诺方面发生了哪些更改？

从 2018 年 3 月 1 日起，某些第三方经销商服务会消耗 EA 货币承诺 (MC)。 以前，除 Azure 虚拟机预留实例 (RI) 外，其他服务会在 EA 货币承诺以外计费，并单独开票。

我们扩展了 MC 的使用范围，现在它包括客户频繁购买的、由第三方发布的一些 Azure 市场服务。 Azure 市场中这些服务采用 EA 货币承诺付款可帮助简化购买和支付管理过程。

### <a name="why-did-we-make-this-change"></a>为何做出这种更改？

客户不断寻找其他方式来利用 MC 提前付款。 此项更改因应了客户的频繁请求，并影响了大部分 Azure 市场客户。

### <a name="how-do-you-benefit"></a>它为客户带来了哪些好处？

客户可获得更简单的计费体验，并且可以更好地消费 EA 货币承诺。 由于这些服务包含在预付的 MC 中，因此 EA 货币承诺更有价值。

### <a name="what-azure-marketplace-services-use-ea-monetary-commitment-and-how-do-i-know"></a>如何知道 Azure 市场服务使用了多少 EA 货币承诺？

购买使用 MC 的服务时，Azure 市场会显示免责声明。 支持 Red Hat、SUSE、Autodesk 和 Oracle 发布的某些服务。 目前，不会从 MC 中减去其他第三方发布的名称类似的服务。 本 FAQ 末尾提供完整的列表。

### <a name="what-if-my-ea-monetary-commitment-runs-out"></a>如果我的 EA 货币承诺已用尽，该怎么办？

如果你已消耗所有 MC 并处于超额状态，与这些服务相关的费用会连同所有其他消耗服务一起显示在下一份超额发票上。 在实施 2018 年 3 月 1 日更改之前，这些费用会连同其他 Azure 市场服务一起开票。

### <a name="why-dont-all-azure-marketplaces-consume-ea-monetary-commitment"></a>为何并非所有 Azure 市场项都消耗 EA 货币承诺？

我们始终致力于在 EA 货币承诺方面提供最佳客户体验。 此项更改满足了大量客户的需求，能应对 Azure 市场中总支出的很大一部分。 将来可能会添加其他服务。

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>这对间接合约和合作伙伴有何影响？

这不会对间接合约客户或合作伙伴产生任何影响。 这些服务与其他其他消费服务一样，受到相同的合作伙伴加价功能的约束。 唯一的变化是，费用将显示在不同的发票上，并在客户的 EA 货币承诺以外支付费用。

### <a name="is-there-a-list-of-azure-marketplace-services-that-consume-ea-monetary-commitment"></a>你们是否提供了消耗 EA 货币承诺的 Azure 市场服务列表？

特定的 Azure 市场套餐可以使用货币承诺资金。 有关参与此计划的产品的完整列表，请参阅[使用货币承诺的第三方服务](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment)。

## <a name="power-bi-reporting"></a>Power BI 报表

Power BI 报表适用于 EA 直接合作伙伴以及有权查看账单信息的间接客户。

### <a name="power-bi-pro"></a>Power BI Pro

Power BI Pro 适用于 EA 客户。 使用 Power BI Pro 可以生成和共享报表，以有效地管理成本数据。 它还提供其他协作和数据刷新功能。 Power BI Pro 提供更高的数据容量和数据流限制。

<!--We plan to add new cost management features for Azure Enterprise customers.

Current Power BI (free) users who use the Microsoft Azure Consumption Insights content pack can get a 60-day free trial of Power BI Pro. After the trial is over, you can continue using Power BI Pro by adding a license.

To sign up for the free Power BI Pro trial:

1. From the gear icon in Power BI, select **Manage personal storage**.
1. Select **Try Pro for free** on the right.

See [Power BI self-service sign up](https://powerbi.microsoft.com/documentation/powerbi-service-self-service-signup-for-power-bi/#power-bi-pro-60-day-trial) for more information on the Power BI Pro free trial.

### Azure EA Power BI Pro trial terms

- **General purpose**: The extended Power BI Pro for the "Microsoft Azure Enterprise" content pack trial offer (the "Offer") is available to existing qualified users during the term of the Offer, to allow them to access certain insights related to their Azure consumption through the use of a specific Power BI content pack.
- **Eligibility**: Users under an Enterprise Agreement (EA) can participate in the Offer if they have a function related to their organization's Azure billing, service, or cost management.
- **Exclusions**:
  - Users already participating in the Extended Power BI Pro trial will continue to qualify under the pre-existing offer and can't enter into the Azure EA Power BI Pro trial offer.
  - Users participating in the Offer can only use Power BI Pro with the Microsoft Azure Enterprise content pack. Any other use of Power BI Pro is prohibited.
  - Term: The Offer began on June 1, 2017 and ended on May 31, 2018.  Acceptance can occur at any time during the 12-month period, though the offer will terminate on May 31, 2018 for all users regardless of when they accepted the Offer.
  -->

### <a name="to-access-microsoft-azure-consumption-insights"></a>若要访问 Microsoft Azure Consumption Insights：

1. 转到 [Microsoft Azure Consumption Insights](https://app.powerbi.com/getdata/services/azureconsumption?cpcode=MicrosoftAzureConsumptionInsights&amp;getDataForceConnect=true&amp;WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26)。
1. 选择“立即获取”。 
1. 提供注册编号和月数，然后选择“下一步”。 
1. 提供要连接的 API 访问密钥。 可以在 [Enterprise 门户](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26)中找到注册的密钥。
1. 选择“登录”自动启动导入过程。 
1. 完成后，导航窗格中将显示新的仪表板、报表和模型。 选择仪表板查看导入的数据。

> [!TIP]
>
> - 若要了解如何为注册生成 API 密钥，请参阅 [Enterprise 门户](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26)上的 API 报表帮助文件。
> - 有关将 Power BI 连接到 Azure 消耗计划的详细信息，请参阅 [Microsoft Azure Consumption Insights](/power-bi/desktop-connect-azure-cost-management)。

### <a name="to-access-the-legacy-power-bi-ea-content-pack"></a>访问旧版 Power BI EA 内容包：

1. 转到 [Power BI 网站](https://app.powerbi.com/getdata/services/azure-enterprise)。
1. 使用有效的工作或学校帐户登录。

   工作或学校帐户可以与用于通过 Azure Enterprise 门户访问注册的帐户相同或不同。
1. 在服务仪表板上，依次选择“Microsoft Azure Enterprise”、“连接”。  
1. 在“连接到 Azure Enterprise”屏幕上，填写以下字段： 
    - Azure 环境 URL：[https://ea.azure.com](https://ea.azure.com/)
    - 月数：1 到 36
    - 注册编号：你的注册编号
1. 选择“**下一页**”。
1. 在“身份验证密钥”框中输入 API 密钥。 

    可以在 Azure Enterprise 门户中的“下载使用情况”选项卡下获取 API 密钥。  选择“API 访问密钥”，然后将该密钥粘贴到“帐户密钥”框中。  
1. 在 Power BI 中加载数据大约需要 5-30 分钟，具体取决于数据集的大小。

## <a name="reports-faq"></a>报表常见问题解答

本部分解答有关报表的常见问题。

### <a name="why-is-my-cost-showing-as-0"></a>为什么我的成本显示为 $0？

对于**直接注册**客户，企业管理员可以向帐户所有者和部门管理员提供对使用情况报表中费用/定价信息的访问权限。 执行以下步骤:

1. 在 Azure Enterprise 门户的左侧导航区域中，选择“管理”。 
1. 选择 DA（部门管理员）查看费用旁边的蓝色铅笔图标。
1. 选择“启用”并保存  。
1. 选择 AO（帐户所有者）查看费用旁边的蓝色铅笔图标。
1. 选择“启用”并保存  。

> [!NOTE]
> 如果你是帐户所有者或部门管理员，请联系企业管理员启用定价功能。

对于**间接注册**客户，请联系合作伙伴来检查他们是否已为你启用了定价功能。 此操作只能由合作伙伴完成。 为你启用此功能后，你就能以企业管理员身份查看注册的费用和定价。

如果合作伙伴想要为帐户所有者和部门管理员启用“查看费用”的功能，请按照“直接注册”下的步骤操作。 

### <a name="why-is-there-no-sku-information-on-my-usage-detail-report"></a>为何使用情况详细信息报表中没有 SKU 信息？

使用情况详细信息报表不包含 SKU 信息。 但是，该报表确实包含使用情况信息。你可以下载价目表报表来获取 SKU 信息。

### <a name="why-doesnt-the-total-amount-on-azure-marketplace-match-the-reports-for-usage-summary-and-detail"></a>Azure 市场的总额为何与使用情况摘要和详细信息的报表不相符？

Azure 市场费用报表仅显示基于用量的费用。 不显示一次性费用。 查看使用情况摘要页可以获取最新的基于用量的费用和一次性费用。

### <a name="why-is-there-no-information-on-my-api-report"></a>我的 API 报表中为何未显示任何信息？

API 密钥每六个月过期。 如果你遇到问题，企业管理员应生成新的 API 密钥。 请记得按照“API 报表常见问题解答”中的步骤操作。

### <a name="why-isnt-my-power-bi-report-working"></a>我的 Power BI 报表为何不正常工作？

如果遇到 Power BI 的问题，请在 [Power BI 支持团队](https://support.powerbi.com)的配合下记录支持票证。

### <a name="why-dont-my-resource-tags-show-on-my-reports"></a>为什么我的报表中未显示我的资源标记

资源标记托管在 Azure 门户上。 可以在 [Azure 门户](https://portal.azure.com)中联系 Azure 订阅团队。 请按照[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)一文中的步骤操作。

### <a name="why-does-my-resource-rate-change-every-day"></a>为什么资源费率每天都在变化？

详细使用情况报表中的资源费率是计算所得的值。 它表示对某项服务收取的月平均费率。 资源费率是根据一个服务单位的每月承诺和每月超额费用的平均值计算得出的。 在月结算日前按承诺和超额费率收费的这部分用量不断变化。 因此，列出的资源费率在当月内也会变化。 资源费率在月末之后的第五日锁定。

### <a name="glossary-of-processes-for-calculating-the-resource-rate"></a>用于计算资源费率的过程的术语表

- 原始单位总数  ：详细使用情况报表中的消耗数量。
- 每单位 MOCP 资源  ：上游使用情况系统使用不同的单位发出每项服务的用量。 （预设）
- **每单位消耗量：** Azure Enterprise 度量单位。 （预设）
- **价格：** Azure Enterprise 门户中的单价。
- 总成本  ：详细使用情况报表中的应付费用，或者 Azure Enterprise 门户中的承诺用量加上超额。

### <a name="charges-calculations"></a>费用计算

- **转换为消耗的 MOCP 资源** = `ROUND(Total RAW Units * MOCP Resource Per Unit,4)`
- **转换为消耗的单位** = `Consumed MOCP Resources / Consumption per Unit`
- **计算总费用** = `Consumed Units * Price`

### <a name="logic-in-the-usage-calculation-logic"></a>使用情况计算逻辑中的逻辑

**资源费率** = `Total Cost /(Total RAW Units / MOCP Resource Per Unit)`

资源费率根据费用派生。 它可能与价目表中的实际单价不符。

在“下载使用情况数据”报表中可以看到，原始资源用量数据最多取六位小数。 此数据用于超额费用计算。 但是，Azure Enterprise 门户中显示的使用情况数据舍入为四位小数，将用于超额单位的用量截断为零位小数。 在 Azure Enterprise 门户下，所有超额用量只收取整单位的费用。 对于作为超额收费或混合几个月收费的用量，你可能会看到，单价和资源费率大不相同。

## <a name="next-steps"></a>后续步骤

- 以下 Excel 文件提供了有关 Azure 服务的详细信息，并会在每个月的 6 日和 20 日更新：

   | 标题 | 说明 | 文件名 |
   | --- | --- | --- |
   | [友好服务名称](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) | 列出所有有效服务，包括： <br>  <ul><li>服务类别</li>   <li>友好服务名称</li>   <li>承诺名称和构成部分编号</li> <li>消耗计划名称和构成部分编号</li>   <li>度量单位</li>   <li>报告的用量与显示的 Enterprise 门户用量之间的转换系数</li></ul> | Friendly\_Service\_Names.xlsx |
   | [服务下载字段](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) | 此电子表格提供使用情况下载报表中服务相关字段的所有可能的组合的一览。 | Service\_Download\_Fields.xlsx |

- 有关发票和费用的信息，请参阅[了解 Azure 企业协议帐单](../understand/review-enterprise-agreement-bill.md)。
- 若要开始使用 Azure Enterprise 门户，请参阅 [Azure EA 门户入门](ea-portal-get-started.md)。
