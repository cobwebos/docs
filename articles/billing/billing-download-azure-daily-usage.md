---
title: 查看和下载 Azure 使用情况与费用
description: 介绍如何下载或查看 Azure 每日使用情况和费用。
keywords: 计费使用情况, 使用费, 使用情况下载, 查看使用情况, Azure 发票, Azure 使用情况
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 23cd7c3765fc99eb5907aa853d7431d5e247aea6
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709716"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>查看和下载 Azure 使用情况与费用

如果你是 EA 客户或签订了 [Microsoft 客户协议](#check-your-access-to-a-microsoft-customer-agreement)，则可以在 [Azure 门户](https://portal.azure.com/)中下载 Azure 使用情况和费用。 对于其他订阅，请转到 [Azure 帐户中心](https://account.azure.com/Subscriptions)下载使用情况。

只有某些角色（例如帐户管理员或企业管理员）有权获取 Azure 使用情况信息。 若要详细了解如何获取计费信息，请参阅[使用角色管理对 Azure 计费的访问](billing-manage-access.md)。

如果你签订了 [Microsoft 客户协议](#check-your-access-to-a-microsoft-customer-agreement)，则必须是计费对象信息所有者、参与者、读者或发票管理者，才能查看 Azure 使用情况和费用。 若要详细了解 Microsoft 客户协议的计费角色，请参阅[计费对象信息角色和任务](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)。

## <a name="download-usage-from-the-account-center-csv"></a>从帐户中心下载使用情况 (.csv)

1. 以帐户管理员身份登录到 [Azure 帐户中心](https://account.windowsazure.com/subscriptions)。

2. 选择需要下载其发票和使用信息的订阅。

3. 选择“帐单”。 

    ![屏幕快照，显示帐单选项](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. 可以查看过去六个计费周期以及当前未计费周期的对帐单。

    ![屏幕快照，显示计费周期、下载发票和日常使用情况的选项，以及每个计费周期的总费用](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. 选择“查看当前对帐单”，查看截至估算生成时的估计费用。  此信息每天只会更新一次，而且可能不包含所有使用信息。 用户的每月发票可能与这项估计有所不同。

    ![屏幕快照，显示“查看当前对帐单”选项](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![屏幕快照，显示当前费用的估计值](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. 选择“下载使用信息”，以 CSV 文件形式下载每日使用数据。  如果看到两个版本可用，请下载版本 2。

    ![屏幕快照，显示“下载使用情况”选项](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

只有帐户管理员可以访问 Azure 帐户中心。 其他计费管理员（例如所有者），可以使用[计费 API](billing-usage-rate-card-overview.md) 获取使用情况信息。

有关每日使用情况的详细信息，请参阅[了解 Microsoft Azure 帐单](billing-understand-your-bill.md)。 如需有关管理成本的帮助，请参阅[通过 Azure 计费和成本管理来防止意外成本](billing-getting-started.md)。

## <a name="download-usage-for-ea-customers"></a>下载 EA 客户的使用情况数据

若要作为 EA 客户查看和下载使用情况数据，你必须是启用了“查看费用策略”的企业管理员、帐户所有者或部门管理员。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。 

    ![显示了 Azure 门户搜索的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. 选择“使用情况 + 费用”。 
1. 针对要下载的月份，选择“下载”。 

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>下载 Microsoft 客户协议使用情况

如果你签订了 Microsoft 客户协议，则可以下载计费对象信息的 Azure 使用情况和费用。 必须是计费对象信息所有者、参与者、读者或发票管理者才能下载 Azure 使用情况和费用 CSV。

### <a name="download-usage-for-billed-charges"></a>下载计费费用的使用情况

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 搜索“成本管理 + 计费”  。
3. 选择计费对象信息。 根据你的访问权限，可能需要先选择一个计费帐户。
4. 选择“发票”  。
5. 在发票网格中，找到与要下载的使用情况对应的发票行。
6. 单击行末尾的省略号 (`...`)。

    ![显示行尾省略号的屏幕截图](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)

7. 在下载上下文菜单中，选择“Azure 使用情况和费用”  。

     ![显示选中“Azure 使用情况和费用”的屏幕截图](./media/billing-download-azure-usage/contextmenu-usage.png)

### <a name="download-usage-for-pending-charges"></a>下载未决费用的使用情况

还可以下载当前计费周期的本月至今使用情况。 这些尚未计费的使用费。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 搜索“成本管理 + 计费”  。
3. 选择计费对象信息。 根据你的访问权限，可能需要先选择一个计费帐户。
4. 在“概述”  区域中，找到“本月至今累计费用”下面的下载链接。
5. 选择“Azure 使用情况和费用”  。

    ![显示从“概述”下载的屏幕截图](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>检查对 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要详细了解发票和使用费用，请参阅：

- [了解 Microsoft Azure 详细使用条款](billing-understand-your-usage.md)
- [了解 Microsoft Azure 账单](billing-understand-your-bill.md)
- [查看和下载 Microsoft Azure 发票](billing-download-azure-invoice.md)
- [查看和下载组织的 Azure 定价](billing-ea-pricing.md)

如果你签订了 Microsoft 客户协议，请参阅：

- [了解有关 Microsoft 客户协议 Azure 详细使用情况的术语](billing-mca-understand-your-usage.md)
- [了解 Microsoft 客户协议发票上的费用](billing-mca-understand-your-bill.md)
- [查看和下载 Microsoft Azure 发票](billing-download-azure-invoice.md)
- [查看和下载 Microsoft 客户协议税单](billing-mca-download-tax-document.md)
- [查看和下载组织的 Azure 定价](billing-ea-pricing.md)
