---
title: 了解您的 Microsoft 客户协议的发票-Azure 上的费用
description: 了解如何阅读并了解发票上的费用。
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: ee250589133abb1944ff17e39dc650cbae4279c6
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490673"
---
# <a name="understand-charges-on-your-microsoft-customer-agreement-invoice"></a>了解 Microsoft 客户协议发票上的费用

通过分析各个事务，可以了解发票上产生的费用。 在 Microsoft 客户协议的计费帐户，每个计费的配置文件每月生成发票。 发票包括在前一个月的所有费用。 在 Azure 门户中，可以查看您的发票。 有关详细信息，请参阅[下载发票的 Microsoft 客户协议](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement)。

本文适用于 Microsoft 客户协议向计费帐户。 [检查是否有权访问 Microsoft 客户协议](#check-access-to-a-microsoft-customer-agreement)。

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>在 Azure 门户中的发票的查看事务

1. 登录到 [Azure 门户](https://www.azure.com)。

2. 搜索**成本管理 + 计费**。

    ![显示有关成本管理 + 计费的 Azure 门户搜索的屏幕截图](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 选择**所有事务**从页的左侧。 根据您的访问权限，可能需要选择计费帐户、 账单资料或发票部分，然后选择**所有事务**。

4. 所有事务页显示以下信息：

    ![显示计费的事务列表的屏幕截图](./media/billing-understand-your-bill-mca/mca-billed-transactions-list.png)

    |列  |定义  |
    |---------|---------|
    |Date     | 交易记录的日期  |
    |发票 ID     | 发票计费事务获取的标识符。 如果你提交支持请求，ID 与 Azure 支持共享加快你的支持请求 |
    |事务类型     |  如采购、 cancel 和使用情况收费的事务的类型  |
    |产品系列     | 虚拟机的计算或 Azure SQL 数据库的数据库这样的产品类别|
    |产品 sku     | 唯一标识您的产品的实例的代码 |
    |金额     |  事务量      |
    |发票部分     | 事务显示在此部分中的计费配置文件的发票上 |
    |计费配置文件     | 在此计费配置文件的发票上显示事务 |

5. 搜索要筛选的事务发票的发票 ID。

### <a name="view-transactions-by-invoice-sections"></a>查看事务通过发票部分

发票部分可帮助您组织的帐单的配置文件的发票费用。 有关详细信息，请参阅[了解发票部分](billing-mca-overview.md#invoice-sections)。 生成发票，发票上显示计费的配置文件中的所有部分的费用。

下图显示上一个示例发票的会计部门发票部分的费用。

![示例图中显示的发票部分信息的详细信息](./media/billing-understand-your-bill-mca/invoicesection-details.png)

当你已确定发票部分的费用时，您可以了解费用在 Azure 门户中查看事务。

1. 转到 Azure 门户以查看发票的事务中的所有事务页。 有关详细信息，请参阅[在 Azure 门户中查看的发票事务](#view-transactions-for-an-invoice-in-the-azure-portal)。

2. 通过查看事务的发票节名称进行筛选。

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>查看挂起的费用来估计在下一步发票

在 Microsoft 客户协议的计费帐户，费用进行估计，并视为挂起，直到它们开具发票。 你可以查看挂起的在 Azure 门户来估计下次的账单费用。 挂起的费用估计，它们不含税。 下一步发票上的实际费用将因挂起的费用。

### <a name="view-summary-of-pending-charges"></a>查看挂起的费用的摘要

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 搜索**成本管理 + 计费**。

   ![显示有关成本管理 + 计费的 Azure 门户搜索的屏幕截图](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 选择计费的配置文件。 具体取决于您的访问权限，可能需要选择计费帐户。 从计费帐户，选择**计费配置文件**然后选择计费的配置文件。

4. 选择**摘要**从屏幕顶部的选项卡。

5. 费用部分显示的月份日期和上个月的费用。

   ![显示有关成本管理 + 计费的 Azure 门户搜索的屏幕截图](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

月-日费用为当月是挂起的费用，并按月生成发票计费。 如果仍未生成上个月的发票，则上个月的费用也会挂起，并将在下次的账单上。

### <a name="view-pending-transactions"></a>查看挂起的事务

如果确定挂起的费用，可以通过分析的费用提供的各个事务了解产生的费用。 此时，挂起的使用情况的费用不是所有的事务页上显示。 在 Azure 订阅页上，可以查看挂起的使用费。 有关详细信息，请参阅[查看挂起的使用费用](#view-pending-usage-charges)

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 搜索**成本管理 + 计费**。

   ![显示有关成本管理 + 计费的 Azure 门户搜索的屏幕截图](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 选择计费的配置文件。 具体取决于您的访问权限，可能需要选择计费帐户。 从计费帐户，选择**计费配置文件**然后选择计费的配置文件。

4. 选择**所有事务**从页的左侧。

5. 搜索*挂起*。 使用**Timespan**筛选器来查看挂起的当前或上个月的费用。

   ![显示挂起的事务列表的屏幕截图](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>查看挂起的使用费用

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 搜索*成本管理 + 计费*。

   ![显示有关成本管理 + 计费的 Azure 门户搜索的屏幕截图](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. 选择计费的配置文件。 具体取决于您的访问权限，可能需要选择计费帐户。 从计费帐户，选择**计费配置文件**然后选择计费的配置文件。

4. 选择**的所有订阅**一页的左侧。

5. Azure 订阅页显示的当前版本和上个月的费用为每个订阅计费的配置文件中。 月-日费用为当月是挂起的费用，并按月生成发票计费。 如果仍未生成上个月的发票，则上个月的费用也是挂起。

    ![显示计费的配置文件的 Azure 订阅列表的屏幕截图](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>分析您的 Azure 使用费用

使用 Azure 使用情况和费用 CSV 文件来分析你的基于使用情况的费用。 可以下载该文件为发票或挂起的费用。 有关详细信息，请参阅[获取 Azure 帐单发票和每日使用情况数据](billing-download-azure-invoice-daily-usage-date.md)。

### <a name="view-detailed-usage-by-invoice-section"></a>查看发票部分的详细使用情况

您可以筛选要协调的发票部分使用费的 Azure 使用情况和费用文件。

以下步骤将引导你完成协调的会计部门发票部分的计算费用：

![示例图中显示的发票部分信息的详细信息](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | 发票 PDF | Azure 使用情况和费用 CSV |
 | --- | --- |
 |会计部门 |invoiceSectionName |
 |使用费-Microsoft Azure 计划 |productOrderName |
 |计算 |serviceFamily |

1. 筛选器**invoiceSectionName**到 CSV 文件中的列**会计部门**。
2. 筛选器**productOrderName**到 CSV 文件中的列**Microsoft Azure 计划**。
3. 筛选器**serviceFamily**到 CSV 文件中的列**Microsoft.Compute**。

![显示的使用情况和费用发票一部分筛选的文件的屏幕截图](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-invoice-section.png)


### <a name="view-detailed-usage-by-subscription"></a>按订阅查看详细使用情况

您可以筛选的 Azure 使用情况和费用 CSV 文件来协调你的订阅的使用费用。 若要查看所有订阅计费的配置文件中，请参阅[都查看挂起的使用费用](#view-pending-usage-charges)。

如果确定订阅的费用，使用 Azure 使用情况和费用 CSV 文件分析产生的费用。

下图显示在 Azure 门户中的订阅列表。

![显示计费的配置文件的 Azure 订阅列表的屏幕截图](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list-highlighted.png)

筛选器**subscriptionName**到 Azure 的使用情况和费用 CSV 文件中的列**WA_Subscription** WA_Subscription 查看详细的使用费。

![显示的使用情况，并按订阅筛选的文件的屏幕截图](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>支付帐单

发票底部会显示付费帐单的说明。 [了解如何支付](billing-mca-understand-your-invoice.md#how-to-pay)。

如果您已经支付了你的帐单，可以检查 Azure 门户中的发票页上的付款状态。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查与 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如果有疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要了解有关发票和详细使用情况的详细信息，请参阅：

- [如何获取 Azure 计费发票和每日使用数据](billing-download-azure-invoice-daily-usage-date.md)
- [了解有关 Microsoft 客户协议发票的术语](billing-mca-understand-your-invoice.md)
- [了解 Microsoft 客户协议使用情况 CSV 的术语](billing-mca-understand-your-usage.md)
