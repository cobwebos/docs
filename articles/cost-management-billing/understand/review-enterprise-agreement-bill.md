---
title: 查看 Azure 企业协议帐单
description: 了解如何阅读并理解 Azure 企业协议的使用情况和帐单。
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: tutorial
ms.date: 05/07/2020
ms.author: banders
ms.openlocfilehash: 4d39b487550fb8566faab428f55bd38572523587
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83657515"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>了解 Azure 企业协议帐单

当签署企业协议的 Azure 客户超出组织的信用额度或使用信用额度未涵盖的服务时，将会收到发票。

组织的信用额度包括货币承诺。 货币承诺是组织提前支付的 Azure 服务使用费。 可以联系 Microsoft 客户经理或经销商，将货币承诺资金添加到企业协议。

本教程仅适用于签订了 Azure 企业协议的 Azure 客户。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 查看已开票费用
> * 查看服务超额费用
> * 查看市场发票

## <a name="prerequisites"></a>先决条件

只有企业管理员才能查看和确认发票上的费用。 有关详细信息，请参阅[了解 Azure 中的 Azure 企业协议管理角色](../manage/understand-ea-roles.md)。 如果你不知道谁是组织的企业管理员，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="review-invoiced-charges-for-most-customers"></a>查看大多数客户的已开票费用

本部分不适用于澳大利亚、日本或新加坡的 Azure 客户。

当计费周期中发生以下任何事件时，将收到 Azure 发票：

- **服务超额**：组织的使用费超过了信用余额。
- **单独计费**：组织使用的服务未包括在信用额度中。 无论信用余额如何，都会为以下服务开具发票：
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - 已注册的用户
    - Openlogic
    - 远程访问权限 XenApp Essentials 已注册的用户
    - Ubuntu Advantage
    - Visual Studio Enterprise（每月）
    - Visual Studio Enterprise（每年）
    - Visual Studio Professional（每月）
    - Visual Studio Professional（每年）
- **市场费用**：组织的额度不涵盖 Azure 市场购买和使用。 因此，不管信用余额如何，都会收到市场费用的发票。 在 Enterprise Portal 中，企业管理员可以启用和禁用市场购买。

你的发票将首先显示Azure 使用费用及其相关成本，然后显示任何市场费用。 如果你有额度余额，它将其应用于 Azure 使用情况，并且发票将显示 Azure 使用情况和市场使用情况，而不会产生任何费用。

将 Enterprise Portal 的“报告” > “使用情况摘要”中显示的合计总金额与 Azure 发票进行比较。  “使用情况摘要”中的金额不含税。

登录到 [Azure EA 门户](https://ea.azure.com)。 然后，选择“报告”。 在选项卡的右上角，将视图从“M”切换到“C”，然后匹配发票上的周期。   

![显示“使用情况摘要”中“M + C”选项的屏幕截图。](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

**总使用量**和 **Azure 市场**的合计金额应与发票上的**总应收金额**相匹配。 若要获取有关费用的详细信息，请转到**下载使用情况**。  

![显示“下载使用情况”选项卡的屏幕截图](./media/review-enterprise-agreement-bill/ea-portal-download-usage.png)

## <a name="review-invoiced-charges-for-other-customers"></a>查看其他客户的已开票费用

本部分仅适用于澳大利亚、日本或新加坡的 Azure 客户。

当发生以下任何事件时，将收到一张或多张 Azure 发票：

- **服务超额**：组织的使用费超过了信用余额。
- **单独计费**：组织使用的服务未包括在信用额度中。 将为以下服务开发票：
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - 已注册的用户
    - Openlogic
    - 远程访问权限 XenApp Essentials 已注册的用户
    - Ubuntu Advantage
    - Visual Studio Enterprise（每月）
    - Visual Studio Enterprise（每年）
    - Visual Studio Professional（每月）
    - Visual Studio Professional（每年）
- **市场费用**：Azure 市场采购和使用不包括在组织的额度中，需单独计费。 在 Enterprise Portal 中，企业管理员可以启用和禁用市场购买。

在计费周期内，如果由于服务超额和单独计费而产生费用，你会收到一个发票。 它包括这两种类型的费用。 市场费用始终单独开票。

## <a name="review-service-overage-charges-for-other-customers"></a>查看其他客户的服务超额费用

本部分仅适用于你在澳大利亚、日本或新加坡的情况。

将 Enterprise Portal 的“报告” > “使用情况摘要”中的总用量与服务超额发票进行比较。  服务超额发票包括超出组织信用额度的用量，和/或信用额度未涵盖的服务。 “使用情况摘要”中的金额不含税。

登录到 [Azure EA 门户](https://ea.azure.com)，然后选择“报告”。 在选项卡的右上角，将视图从“M”切换到“C”，然后匹配发票上的周期。   

![显示“使用情况摘要”中“M + C”选项的屏幕截图。](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

“总用量”应与服务超额发票上的“总应收金额”相匹配。  若要获取有关费用的其他信息，请转到“下载使用情况” > “高级报告下载”。  此报告不包括预留的税款或费用，也不包括市场费用。  

![显示“下载使用情况”选项卡上的“高级报告下载”的屏幕截图。](./media/review-enterprise-agreement-bill/ea-portal-download-usage-advanced.png)

下表列出发票上显示的以及企业门户“使用情况摘要”中显示的款项和说明：

|发票上的款项|“使用情况摘要”中的款项|说明|
|---|---|---|
|总应收金额|总用量|应用信用额度之前特定周期的税前使用费总额。|
|承诺用量|承诺用量|在该特定周期应用的信用额度。|
|总销售额|总超额|超出信用额的使用费总额。 此金额不含税。|
|税额|不适用|应用到特定周期的总销售额的税额。|
|总金额|不适用|应用信用额度并添加税额后应付的发票金额。|

### <a name="review-marketplace-invoice"></a>查看市场发票

本部分仅适用于你在澳大利亚、日本或新加坡的情况。

将企业门户的“报告” > “使用情况摘要”中的 Azure 市场总额与市场发票进行比较。  市场发票仅适用于 Azure 市场采购和使用。 **使用情况摘要**上的金额已包含由发布者确定的税款。

登录到[企业门户](https://ea.azure.com)，然后选择“报告”。 在选项卡的右上角，将视图从“M”切换到“C”，然后匹配发票上的周期。   

![显示“使用情况摘要”中“M + C”选项的屏幕截图。](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)  

“Azure 市场”总额应与市场发票上的“总销售额”相匹配。  若要获取有关基于用量的费用的其他信息，请转到“下载使用情况”。 在“市场费用”下，选择“下载”。  市场价格包括由发布者确定的税款。 客户不会从发布者那里收到用于收取交易税的单独发票。

![显示“市场费用”下的“下载”选项的屏幕截图。](./media/review-enterprise-agreement-bill/ea-portal-download-usage-marketplace.png)

## <a name="view-price-sheet-information"></a>查看价目表信息

企业管理员可以查看与其 Azure 服务注册关联的价目表。

若要查看当前价目表：

1. 在 Azure Enterprise 门户中，依次选择“报表”、“价目表”。 
2. 查看价目表，或选择“下载”。

![显示价目表信息的示例](./media/review-enterprise-agreement-bill/ea-create-view-price-sheet-information.png)

若要下载历史价目表：

1. 在 Azure Enterprise 门户中，依次选择“报表”、“下载使用情况”。 
2. 下载价目表。

![显示旧价目表下载位置的示例](./media/review-enterprise-agreement-bill/create-ea-view-price-sheet-download-historical-price-list.png)

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
1. 从顶部功能区中选择“使用情况下载”。
1. 选择相应月份报表旁边的“下载”。

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
1. 在“原始数据类型”下，选择“带分隔符”。   默认使用“固定宽度”。
1. 选择“**下一页**”。
1. 在“分隔符”下，选中“逗号”对应的复选框。 清除“制表符”（如果已选中）。
1. 选择“**下一页**”。
1. 滚动到“ResourceRate”和“ExtendedCost”列。 
1. 选择“ResourceRate”列。 此列以黑色突出显示。
1. 在“列数据格式”部分下，选择“文本”而不是“常规”。   列标题将从“常规”更改为“文本”。 
1. 对“ExtendedCost”列重复步骤 8 到 9，然后选择“完成”。 

> [!TIP]
> 若要将 CSV 文件设置为在 Excel 中自动打开，必须改用 Excel 中的“打开”功能。 在 Excel 中，转到“文件” > “打开”。 

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
1. 选择适当的日期范围。
1. 选择适当的帐户。
1. 选择“请求使用情况数据”。
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

使用“高级报表下载”功能时，部门管理员和帐户所有者无法查看费用。 费用显示为 $0。

帐户所有者查看费用的权限扩展到帐户所有者以及对关联订阅拥有权限的所有用户。 如果你是间接客户，必须由渠道合作伙伴来为你启用成本功能。

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

    可以在 Azure Enterprise 门户中的“下载使用情况”选项卡下获取 API 密钥。选择“API 访问密钥”，然后将该密钥粘贴到“帐户密钥”框中。 
1. 在 Power BI 中加载数据大约需要 5-30 分钟，具体取决于数据集的大小。

## <a name="reports-faq"></a>报表常见问题解答

本部分解答有关报表的常见问题。

### <a name="why-is-my-cost-showing-as-0"></a>为什么我的成本显示为 $0？

对于**直接注册**客户，企业管理员可以向帐户所有者和部门管理员提供对使用情况报表中费用/定价信息的访问权限。 执行以下步骤:

1. 在 Azure Enterprise 门户的左侧导航区域中，选择“管理”。
1. 选择 DA（部门管理员）查看费用旁边的蓝色铅笔图标。
1. 选择“启用”并保存。
1. 选择 AO（帐户所有者）查看费用旁边的蓝色铅笔图标。
1. 选择“启用”并保存。

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

- 原始单位总数：详细使用情况报表中的消耗数量。
- 每单位 MOCP 资源：上游使用情况系统使用不同的单位发出每项服务的用量。 （预设）
- **每单位消耗量：** Azure Enterprise 度量单位。 （预设）
- **价格：** Azure Enterprise 门户中的单价。
- 总成本：详细使用情况报表中的应付费用，或者 Azure Enterprise 门户中的承诺用量加上超额。

### <a name="charges-calculations"></a>费用计算

- **转换为消耗的 MOCP 资源** = `ROUND(Total RAW Units * MOCP Resource Per Unit,4)`
- **转换为消耗的单位** = `Consumed MOCP Resources / Consumption per Unit`
- **计算总费用** = `Consumed Units * Price`

### <a name="logic-in-the-usage-calculation-logic"></a>使用情况计算逻辑中的逻辑

**资源费率** = `Total Cost /(Total RAW Units / MOCP Resource Per Unit)`

资源费率根据费用派生。 它可能与价目表中的实际单价不符。

在“下载使用情况数据”报表中可以看到，原始资源用量数据最多取六位小数。 此数据用于超额费用计算。 但是，Azure Enterprise 门户中显示的使用情况数据舍入为四位小数，将用于超额单位的用量截断为零位小数。 在 Azure Enterprise 门户下，所有超额用量只收取整单位的费用。 对于作为超额收费或混合几个月收费的用量，你可能会看到，单价和资源费率大不相同。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 查看已开票费用
> * 查看服务超额费用
> * 查看市场发票

继续阅读下一篇文章，详细了解如何使用 Azure EA 门户。

> [!div class="nextstepaction"]
> [Azure EA 门户入门](../manage/ea-portal-get-started.md)
