---
title: "了解帐单 | Microsoft 文档"
description: "了解如何阅读并理解 Azure 订阅的使用情况和计费"
services: 
documentationcenter: 
author: genlin
manager: stevenpo
editor: 
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2016
ms.author: erihur;genli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 8768ccd5a1d04566c5f2e1f26ab328cea570cc61


---
# <a name="understand-your-bill-for-microsoft-azure"></a>了解 Microsoft Azure 帐单
> [!NOTE]
> 对于本文中的任何部分，如需更多帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
> 
> 

Microsoft Azure 订阅的费用因资费套餐而异。 某些资费套餐（例如 Visual Studio Enterprise (MPN) 订户）包含你可以根据需要用于 Azure 服务的每月信用额度。

请注意，可在当前计费周期报告上一个计费周期中的最多 24 小时的延迟使用情况。

有关耗用量和资费套餐的详细信息，请参阅[“Microsoft Azure 购买选项”页](https://azure.microsoft.com/pricing/purchase-options/)。

<!-- The below links cover a complete list of all Microsoft Azure services.

<!-- - [Service Details list (csv1)](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv1.xlsx)
<!-- - [Service Details list (csv2)](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv2.xlsx)

<!-- *NOTE: The **csv1** link refers to the column header names for csv version 1 and **csv2** link refers to the new column header names for csv version 2.  These files are updated monthly.*-->

### <a name="view-or-download-a-bill-for-microsoft-azure"></a>查看或下载 Microsoft Azure 帐单：
1. 使用 Microsoft 帐户或者组织帐户 ID 登录到[帐户中心](https://account.windowsazure.com/subscriptions)。
2. 单击要查看其详细信息和使用量的订阅。
3. 单击“帐单历史记录”
   
    ![摘要 - 帐单 1](./media/billing-understand-your-bill/ContentViewaBillforMA1.png)
4. “帐单历史记录”部分列出了以前的计费周期以及当前未开票期间的对帐单。 当前期间的对帐单是生成估算值时的估计费用。 此信息每天只会更新一次，而且可能不包含截到本日为止的所有使用量。 你的每月帐单可能与这项估计有所不同。  
   
    ![摘要 - 帐单 2](./media/billing-understand-your-bill/ContentViewaBillforMA2.png)
5. 单击“查看当前对帐单”，查看截至估算生成时的估计费用。 此信息每天只会更新一次，而且可能不包含截到本日为止的所有使用量。 你的每月帐单可能与这项估计有所不同。
   
    ![摘要 - 帐单 3](./media/billing-understand-your-bill/ContentViewaBillforMA3.png)
   
    ![摘要 - 帐单 4](./media/billing-understand-your-bill/ContentViewaBillforMA4.png)
6. 单击“下载发票”，查看先前帐单的副本。
   
    ![摘要 - 帐单 5](./media/billing-understand-your-bill/ContentViewaBillforMA5.png)

> [!NOTE]
> 对于国际客户，对帐单上列出的费用仅用于估算目的，因为不同的银行根据不同的兑换率计算费用。
> 
> 

以下是 Microsoft Azure 上提供的两种不同产品/服务的一些示例对帐单。

| 产品/服务类型 | 说明 | 下载 |
|:--- |:--- |:--- |
| 即用即付 |每月应付欠款 |[示例文件](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_ccinvoice_Sample.pdf) |
| 承诺产品/服务 |从预付承诺扣减的费用 |[示例文件](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_invoice_Sample.pdf) |

## <a name="account-information"></a>帐户信息
帐户信息部分中指明了与使用量和配置文件相关的信息。

![标头的值开始缓存响应](./media/billing-understand-your-bill/Header.png)

| 术语 | 说明 |
| --- | --- |
| 发票号 |用于跟踪的唯一发票标识符 |
| 计费周期 |产生使用量的时间范围 |
| 发票日期 |生成发票的日期 |
| 付款方式 |在帐户中使用的付款类型（“发票”或“信用卡”） |
| 帐单邮寄地址 |Microsoft Azure 付款地址 |
| 订阅产品/服务 |购买的订阅产品/服务的类型（即用即付、BizSpark Plus、Azure Pass 等） |
| 帐户所有者电子邮件地址 |注册 Microsoft Azure 帐户时使用的帐户电子邮件地址 |

## <a name="understand-the-invoice-summary"></a>了解发票汇总
帐单的“发票汇总”部分汇总了上次帐单之后的交易以及当前的使用费。

![发票汇总](./media/billing-understand-your-bill/InvoiceSummary.png)

帐单的前期结余、付款和未付余额部分汇总了上次帐单之后的交易。

| 术语 | 说明 |
| --- | --- |
| 前期结余 |上次帐单中结余的总金额 |
| 付款 |应用到上次帐单的总付款金额 |
| 未付余额（从上一计费周期） |上次帐单之后应用到你帐户的任何帐单调整（信用额度或结余） |

## <a name="understand-the-current-charges"></a>了解当前费用
帐单的“当前费用”部分包含有关每月费用的详细信息。 链接已组织成以下小节。

| 术语 | 说明 |
| --- | --- |
| 使用费 |使用费是订阅的总月度费用。 我们会根据你过去一个月的使用量事后向你收取费用。 |
| 折扣 |应用到当前帐单的服务折扣会反映在这个行项目中。 |
| 调整 |杂项调整是应用到当前帐单的其他信用额度或未付费用。 例如，如果你享有 Visual Studio Enterprise with MSDN 优惠，你会在这个行项目中看到每月信用额度。 如果取消订阅，将从你的当前计费周期开始到订阅取消日期为止，对超出产品/服务中包含的每月信用额度的每月使用量进行收费。 |

## <a name="footer-information"></a>页脚信息
![页脚](./media/billing-understand-your-bill/footerinformation.png)

## <a name="understand-the-additional-information"></a>了解其他信息
“其他信息”页提供让你了解发票的其他资源参考、用于查看使用量的链接，以及帐单的其他相关信息。

![其他信息](./media/billing-understand-your-bill/AdditionalInformation.png)

### <a name="detailed-usage"></a>详细使用情况
“详细使用情况”下面的描述中提供了一个链接，单击该链接可定向到帐户中心，可以在其中查看此订阅的详细使用情况。  现在有两个版本可供下载：**.csv 版本 1** 包含旧的命名约定和使用量字段，**.csv 版本 2** 包含每个类别的客户易记名称，以及其他有助于了解在 Microsoft Azure 上使用的服务的字段。 请注意，.csv 版本 1 中没有 Azure Resource Manager 的详细信息。 在 .csv 版本 2 中可以找到 Azure Resource Manager 信息。

### <a name="additional-information-and-useful-resources"></a>其他信息和有用资源
此部分包含有关计算实例大小、SQL DB 费用等简单问题的链接，以及可帮助你进一步回答问题的有用链接。

| 术语 | 说明 |
| --- | --- |
| 买方 |已预先填充了帐户的配置文件地址 |
| 付款说明 |此部分是付款方式为发票时寄送支票、电汇或隔天送达支票的付款说明 |

## <a name="understand-detailed-usage-charges"></a>了解详细的使用费
我们一直在不断努力，以帮助客户轻松管理 Azure 使用量。我们已对报告 Azure 服务使用量和费用的下载使用量文件进行了改进。  下载链接包含两个版本的使用文件：

* **版本 1** 使用预先存在的格式
* **版本 2** 包括其他信息和“每日使用情况”部分中的已更新列名称。  

使用费是订阅的**每月**总费用减去任何信用额度或折扣。 我们会根据你过去一个月的使用量事后向你收取费用。  文件的顶部显示在上月的计费周期内要支付费用的服务的详细信息。  上表列出了每个.csv 版本文件的列名称。

| 版本 1 | 版本 2 | 说明 |
|:--- |:--- | --- |
| 计费周期 |计费周期 |使用资源时的计费周期。 |
| 名称 |测定仪类别 |列出该次使用所属的最上层服务。 |
| 类型 |测定仪子类别 |Azure 服务可能会在此列中按类型进一步定义，这可能会影响费率。 |
| 资源 |测定仪名称 |列出耗用资源的度量单位。 |
| 区域 |测定仪区域 |指明某些服务的数据中心的位置，这些服务根据数据中心位置进行定价。 |
| SKU |SKU |指明每个 Azure 资源的唯一系统标识符。 |
| 计价单位 |计价单位 |指明服务的计价单位。 例如，GB、小时、10,000 秒。 |
| 已耗用 |已耗用数量 |包含计费期间所耗用的资源量。 |
| 附送 |附送数量 |包含当前计费周期免费提供的资源量。 |
| 可计费 |超额数量 |如果已耗用数量超过所提供的数量，则此数据行会显示差异。 我们会针对此数量向你收费。 对于不附送任何数量的即用即付产品/服务，此总数与已耗用数量相同。 |
| 在承诺额范围内 |在承诺额范围内 |包含从您的 6 或 12 个月产品/服务相关承诺用量中缩减的资源费用。 资源费用依时间先后顺序从承诺用量中进行递减。 |
| 货币 |货币 |指明当前计费周期反映的货币。 |
| 超额 |超额 |包含超过您的 6 或 12 个月产品/服务相关承诺用量的资源费用。 |
| 承诺费率 |承诺费率 |包含基于您的 6 或 12 个月产品/服务相关总承诺用量的承诺费率。 |
| 费率 |费率 |费率会显示根据每个可计费单位向你收费的费率。 |
| 值 |值 |显示可计费列乘以费率列的结果。 如果已耗用数量未超过所提供的数量，则此数据行中将不会有任何费用。 |

## <a name="analyze-daily-usage-data"></a>分析每日使用数据
可能会有数千行的日常使用数据，具体取决于你的使用情况。 如果想要分析此数据，请单击“下载使用量”，然后选择逗号分隔变量文件 (.csv) 版本，查看相应计费周期内的每日使用量数据。  您可以下载每个版本的示例 .csv 文件，以供您参考。

| 名称 | 下载 |
|:---:|:---:|
|   详细的使用量 .csv 版本 1 |[示例文件](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv1.xlsx) |
|   详细的使用量 .csv 版本 2 |[示例文件](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv2.xlsx) |

![csv2 快照](./media/billing-understand-your-bill/csv2screenshot.png)

在 .csv 文件中，项目会进行细分，以显示当前计费周期内每个资源的耗用量清单。

![csv 快照](./media/billing-understand-your-bill/csvsnapshotportal.png)

以下列显示了在开始计费期间时影响费率的详细信息：

| 版本 1 | 版本 2 | 说明 |
|:--- |:--- | --- |
| 使用日期 |使用日期 |资源的发出日期。 |
| 名称 |测定仪类别 |列出该次使用所属的最上层服务。 |
| 资源 GUID |测定仪 ID |计费测定仪标识符。  此标识符用于进行计费使用定价。 |
| 类型 |测定仪子类别 |Azure 服务可能会在此列中按类型进一步定义，这可能会影响费率。 |
| 资源 |测定仪名称 |列出耗用资源的度量单位。 |
| 区域 |测定仪区域 |指明某些服务的数据中心的位置，这些服务根据数据中心位置进行定价。 |
| 计价单位 |计价单位 |指明服务的计价单位。 例如，GB、小时、10,000 秒。 |
| 已耗用 |已耗用数量 |包含当日已耗用的资源量。 |
| 子区域 |资源位置 |指明资源正在其中运行的数据中心。 |
| 服务 |已耗用的服务 |你可以利用这个列来跟踪可能无法在名称列中特别列出的单个 Azure 平台服务。 此服务列将指出与使用量相关的特定服务。 |
| 不适用 |资源组 |***添加新列。*** 部署的资源正在其中运行的资源组。 请参阅 [Azure Resource Manager 概述](../azure-resource-manager/resource-group-overview.md) |
| 组件 |实例 ID |正在运行的资源的标识符。 此标识符包含您在资源创建时为其指定的名称。 |
| 不适用 |标记 |***添加新列。*** Azure 中的新资源类型，允许您标记资源。 请参阅[使用标记组织 Azure 资源](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) |
| 其他信息 |其他信息 |与服务相关的其他元数据。 |
| 服务信息 1 |服务信息 1 |此列包含订阅上服务所属的项目名称。 |
| 服务信息 2 |服务信息 2 |这是一个旧字段，用于捕获可选的服务特定元数据。 |

除了一些新字段和对 csv 版本 2 的名称更改之外，将对以下字段中的数据进行标准格式化：

* **实例 ID**：“实例 ID”字段表示用户为已预配的服务指定的标识符。 目前，有两种格式的实例 ID：资源名称或完全限定的资源 ID。 Microsoft Azure 服务正在转换为对实例 ID 使用标准化的完全限定的资源 ID 格式 ***(/subscriptions/<subscription id>/resourcegroups/<resourcegroupname>/providers/<providername>/<resourcename>)***。 在服务转换为采用新格式时，您会看到“实例 ID”数据字段从仅包含资源名称更改为包含资源 ID。 资源 ID 是 [Azure Resource Manager API](https://msdn.microsoft.com/library/azure/dn790567.aspx) 用来标识订阅中资源的格式。

![实例 ID](./media/billing-understand-your-bill/instanceid.png)

* **其他信息**：使用量 .csv 中的“其他信息”列指定了服务特定元数据。 例如，VM 的图像类型。 目前，服务会发出以下多个列中的服务特定元数据：“其他信息”、“服务信息 1”和“服务信息 2”字段。 Microsoft Azure 服务仅对“其他信息”列中的发出服务特定元数据进行标准化。  请参阅以下标准化格式快照：

![additionalinfo_csv2](./media/billing-understand-your-bill/AdditionaInfo_csv2.png)

* **标记**：此列包含用户指定的资源标记。 标记可用于对计费记录进行分组。 例如，您可以使用标记按使用服务的部门分配费用。 详细了解如何[使用标记来组织 Azure 资源](../resource-group-using-tags.md)。 支持发出标记的服务包括：  
  
  * 虚拟机
  * 存储空间；以及
  * 使用 [Azure Resource Manager API](https://msdn.microsoft.com/library/azure/dn790567.aspx) 预配的网络服务

![标记](./media/billing-understand-your-bill/tags.png)

## <a name="next-steps"></a>后续步骤
* [设置计费警报](../billing-set-up-alerts.md)
* [管理付款方式](../billing-how-to-change-credit-card.md)
* [了解 Azure 应用商店收费](../billing-understand-your-azure-marketplace-charges.md)
* [Azure 计费和订阅常见问题](../billing-subscription-faq.md)

> [!NOTE]
> 如果仍有疑问，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。
> 
> 

<!--
OLD MSDN Articles
- [What do I do if my Azure subscription become disabled?](https://msdn.microsoft.com/library/azure/dn736049.aspx)
- [Edit payment information for an existing credit card](https://msdn.microsoft.com/library/azure/dn736053.aspx)
- [Add a new credit card to use as a payment method](https://msdn.microsoft.com/library/azure/dn736057.aspx)
- [Change the credit card on your Microsoft Azure account](https://msdn.microsoft.com/library/azure/dn736050.aspx)
- [Manage your payment method](https://msdn.microsoft.com/library/azure/dn736054.aspx)
-->



<!--Image references-->



<!--HONumber=Nov16_HO3-->


