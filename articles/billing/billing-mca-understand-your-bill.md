---
title: 了解 Microsoft 客户协议发票中的费用 - Azure
description: 了解如何阅读和理解发票中的费用。
author: jureid
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: e9c74fd18bf80dfb8fa406912795fb53e655bd88
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226153"
---
# <a name="understand-charges-on-your-microsoft-customer-agreement-invoice"></a>了解 Microsoft 客户协议发票中的费用

可以通过分析每笔交易来了解发票中的费用。 在 Microsoft 客户协议的计费帐户中，每月将为每个计费配置文件生成一份发票。 该发票包括上个月的所有费用。 可以在 Azure 门户中查看发票。 有关详细信息，请参阅[下载 Microsoft 客户协议的发票](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement)。

本文适用于 Microsoft 客户协议的计费帐户。 [检查你是否有权访问 Microsoft 客户协议](#check-access-to-a-microsoft-customer-agreement)。

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>在 Azure 门户中查看发票的交易

1. 登录到 [Azure 门户](https://www.azure.com)。

2. 搜索“成本管理 + 计费”。 

    ![显示如何在 Azure 门户中搜索“成本管理 + 计费”的屏幕截图](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 在页面左侧选择“所有事务”。  根据你的访问权限，可能需要选择某个计费帐户、计费配置文件或发票科目，然后选择“所有交易”。 

4. “所有交易”页显示以下信息：

    ![显示计费交易列表的屏幕截图](./media/billing-understand-your-bill-mca/mca-billed-transactions-list.png)

    |列  |定义  |
    |---------|---------|
    |Date     | 交易日期  |
    |发票 ID     | 从中计收交易费的发票的标识符。 提交支持请求时，请与 Azure 支持人员共享该 ID，以加速支持请求的处理 |
    |交易类型     |  交易类型，例如购买、取消和使用费  |
    |产品系列     | 产品类别，例如，适用于虚拟机的计算产品，或适用于 Azure SQL 数据库的数据库产品|
    |产品 SKU     | 用于标识产品实例的唯一代码 |
    |金额     |  交易额      |
    |发票科目     | 交易显示在计费配置文件的发票的此科目中 |
    |计费配置文件     | 交易显示在计费配置文件的发票中 |

5. 搜索发票 ID 以筛选发票的交易。

### <a name="view-transactions-by-invoice-sections"></a>按发票科目查看交易

发票科目可帮助你组织计费配置文件发票的成本。 有关详细信息，请参阅[了解发票科目](billing-mca-overview.md#invoice-sections)。 生成发票后，计费配置文件中所有科目的费用将显示在发票上。

下图显示了示例发票中“会计部门”发票科目的费用。

![按发票科目信息显示详细信息的示例插图](./media/billing-understand-your-bill-mca/invoicesection-details.png)

识别发票科目的费用后，可以在 Azure 门户中查看交易，以了解费用。

1. 在 Azure 门户中转到“所有交易”页，以查看发票的交易。 有关详细信息，请参阅[在 Azure 门户中查看发票的交易](#view-transactions-for-an-invoice-in-the-azure-portal)。

2. 按发票科目名称筛选以查看交易。

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>查看未结费用以估算下一份发票

在 Microsoft 客户协议的计费帐户中，将会估算费用，在开票之前，这些费用将视为未结。 可以在 Azure 门户中查看未结费用，以估算下一份发票。 未结费用是估算的，不包括税费。 下一份发票中的实际费用不同于未结费用。

### <a name="view-summary-of-pending-charges"></a>查看未结费用的摘要

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 搜索“成本管理 + 计费”。 

   ![显示如何在 Azure 门户中搜索“成本管理 + 计费”的屏幕截图](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 选择一个计费配置文件。 根据你的访问权限，可能需要选择一个计费帐户。 在该计费帐户中选择“计费配置文件”，然后选择一个计费配置文件。 

4. 选择屏幕顶部的“摘要”选项卡。 

5. 费用部分将显示本月至今累计费用，以及上个月的费用。

   ![显示如何在 Azure 门户中搜索“成本管理 + 计费”的屏幕截图](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

本月至今累计费用是当前月份的未结费用，将在生成当月的发票时计收。 如果上个月的发票仍未生成，则上个月的费用也是未结的，将显示在下一份发票中。

### <a name="view-pending-transactions"></a>查看未结的交易

识别未结费用时，可以通过分析产生这些费用的每笔交易来了解费用。 此时，未结使用费不会显示在“所有交易”页上。 可以在 Azure 订阅页上查看未结使用费。 有关详细信息，请参阅[查看未结使用费](#view-pending-usage-charges)

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 搜索“成本管理 + 计费”。 

   ![显示如何在 Azure 门户中搜索“成本管理 + 计费”的屏幕截图](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 选择一个计费配置文件。 根据你的访问权限，可能需要选择一个计费帐户。 在该计费帐户中选择“计费配置文件”，然后选择一个计费配置文件。 

4. 在页面左侧选择“所有事务”。 

5. 搜索“未结”。  使用“时间跨度”筛选器查看当前月份或上一月份的未结费用。 

   ![显示未结交易列表的屏幕截图](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>查看未结使用费

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 搜索“成本管理 + 计费”。 

   ![显示如何在 Azure 门户中搜索“成本管理 + 计费”的屏幕截图](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 选择一个计费配置文件。 根据你的访问权限，可能需要选择一个计费帐户。 在该计费帐户中选择“计费配置文件”，然后选择一个计费配置文件。 

4. 在页面左侧选择“所有订阅”。 

5. Azure 订阅页将显示计费配置文件中每个订阅的当月费用和上个月费用。 本月至今累计费用是当前月份的未结费用，将在生成当月的发票时计收。 如果上个月的发票仍未生成，则上个月的费用也是未结的。

    ![显示计费配置文件的 Azure 订阅列表的屏幕截图](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>分析 Azure 使用费

使用 Azure 使用情况和费用 CSV 文件来分析基于用量的费用。 可以下载发票文件，或未结费用的文件。 有关详细信息，请参阅[获取 Azure 计费发票和每日使用情况数据](billing-download-azure-invoice-daily-usage-date.md)。

### <a name="view-detailed-usage-by-invoice-section"></a>按发票科目查看详细使用情况

可以筛选 Azure 使用情况和费用文件，以核对发票科目的使用费。

以下步骤引导你核对“会计部门”发票科目的计算费用：

![按发票科目信息显示详细信息的示例插图](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | 发票 PDF | Azure 使用情况和费用 CSV |
 | --- | --- |
 |会计部门 |invoiceSectionName |
 |使用费 - Microsoft Azure 计划 |productOrderName |
 |计算 |serviceFamily |

1. 将 CSV 文件中的“invoiceSectionName”列筛选为“会计部门”。  
2. 将 CSV 文件中的“productOrderName”列筛选为“Microsoft Azure 计划”。  
3. 将 CSV 文件中的“serviceFamily”列筛选为“Microsoft.Compute”。  

![显示按发票科目筛选的使用情况和费用文件的屏幕截图](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-invoice-section.png)


### <a name="view-detailed-usage-by-subscription"></a>按订阅查看详细使用情况

可以筛选 Azure 使用情况和费用 CSV 文件，以核对订阅的使用费。 若要查看计费配置文件中的所有订阅，请参阅[查看未结使用费](#view-pending-usage-charges)。

识别订阅的费用时，请使用 Azure 使用情况和费用 CSV 文件来分析费用。

下图显示了 Azure 门户中的订阅列表。

![显示计费配置文件的 Azure 订阅列表的屏幕截图](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list-highlighted.png)

将 Azure 使用情况和费用 CSV 文件中的“subscriptionName”列筛选为“WA_Subscription”，以查看 WA_Subscription 的详细使用费。  

![显示按订阅筛选的使用情况和费用文件的屏幕截图](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>支付帐单

有关支付帐单的说明显示在发票的底部。 [了解如何支付](billing-mca-understand-your-invoice.md#how-to-pay)

如果已支付帐单，可以在 Azure 门户中的“发票”页上检查付款状态。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查对 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要详细了解发票和详细的使用情况，请参阅：

- [如何获取 Azure 计费发票和每日使用数据](billing-download-azure-invoice-daily-usage-date.md)
- [了解 Microsoft 客户协议发票中的术语](billing-mca-understand-your-invoice.md)
- [了解 Microsoft 客户协议使用情况 CSV 中的术语](billing-mca-understand-your-usage.md)
