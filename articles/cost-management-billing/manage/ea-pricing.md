---
title: 查看和下载组织的 Azure 定价
description: 了解如何查看和下载定价或使用组织的定价估计成本。
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 1dd0dfad0c68e18ae272d5d94fedd67d24be9ae1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "77200772"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>查看和下载组织的 Azure 定价

签订了 Azure 企业协议 (EA)、Microsoft 客户协议 (MCA) 或 Microsoft 合作伙伴协议 (MPA) 的 Azure 客户可以在 Azure 门户中查看和下载其定价。 [了解如何检查计费帐户类型](#check-your-billing-account-type)。

## <a name="download-pricing-for-an-enterprise-agreement"></a>下载企业协议的定价

只有某些管理角色可以访问你的组织的 EA 定价信息，具体取决于企业管理员为你的组织设置的策略。 有关详细信息，请参阅[了解 Azure 中的 Azure 企业协议管理角色](understand-ea-roles.md)。

1. 以企业管理员身份登录到 [Azure 门户](https://portal.azure.com/)。
1. 搜索“成本管理 + 计费”。 

   ![显示了 Azure 门户搜索的屏幕截图](./media/ea-pricing/portal-cm-billing-search.png)

1. 在计费帐户下，选择“使用情况 + 费用”。 

   ![其中显示了“计费”下的使用情况和费用的屏幕截图](./media/ea-pricing/ea-pricing-usage-charges-nav.png)

1. 对相应月份选择![显示 Azure 门户搜索的屏幕截图](./media/ea-pricing/download-icon.png)“下载”  。

1. 在“价目表”  下，选择“下载 csv”  。

   ![其中显示了价目表“下载 csv”按钮的屏幕截图](./media/ea-pricing/download-ea-price-sheet.png)

## <a name="download-pricing-for-an-mca-or-mpa-account"></a>下载 MCA 或 MPA 帐户的定价

如果签订了 MCA，必须是计费对象信息所有者、参与者、读者或发票管理者才能查看和下载定价。 如果签订了 MPA，则必须在合作伙伴组织中具有全局管理员和管理员代理角色，才能查看和下载定价。

### <a name="download-price-sheets-for-billed-charges"></a>下载计费费用的价目表

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。 
1. 选择计费对象信息。 根据你的访问权限，可能需要先选择一个计费帐户。
1. 选择“发票”  。
1. 在发票网格中，找到与要下载的价目表对应的发票行。
1. 单击行末尾的省略号 (`...`)。
![显示选中省略号的屏幕截图](./media/ea-pricing/billingprofile-invoicegrid-new.png)

1. 若要查看所选发票中服务的价格，请选择“发票价目表”  。
1. 若要查看给定计费周期内所有 Azure 服务的价格，请选择“Azure 价目表”  。

![显示带价目表的上下文菜单的屏幕截图](./media/ea-pricing/contextmenu-pricesheet01.png)

### <a name="download-price-sheets-for-the-current-billing-period"></a>下载当前计费周期的价目表

如果签订了 MCA，则可以下载当前计费周期的定价。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索“成本管理 + 计费”。 
1. 选择计费对象信息。 根据你的访问权限，可能需要先选择一个计费帐户。
1. 在“概述”  区域中，找到“本月至今累计费用”下面的下载链接。
1. 选择“Azure 价目表”  。
![显示从“概述”下载的屏幕截图](./media/ea-pricing/open-pricing01.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>使用 Azure 定价计算器估计成本

还可以通过 Azure 定价计算器使用组织的定价来估计成本。

1. 转到 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator)。
1. 在右上角，选择“登录”  。
1. 在“程序和产品/服务” > “许可计划”下，选择“企业协议 (EA)”。
1. 在“程序和产品/服务” > “所选协议”下，选择“未选择任何项”。

    ![其中显示了价目表“下载 csv”按钮的屏幕截图](./media/ea-pricing/ea-pricing-calculator-estimate.png)

1. 选择组织。
1. 选择“应用”。 
1. 搜索产品，然后将其添加到你的预估中。
1. 将根据适用于你所选组织的定价显示估计的价格。

## <a name="check-your-billing-account-type"></a>检查计费帐户类型
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>后续步骤

如果你是 EA 客户，请参阅：

- [管理对 Azure 的 EA 账单信息的访问权限](manage-billing-access.md)
- [查看和下载 Microsoft Azure 发票](../understand/download-azure-invoice.md)
- [查看和下载 Microsoft Azure 使用情况与费用](../understand/download-azure-daily-usage.md)
- [了解企业协议客户的帐单](../understand/review-enterprise-agreement-bill.md)

如果你签订了 Microsoft 客户协议，请参阅：

- [了解 Microsoft 客户协议的价目表中的术语](mca-understand-pricesheet.md)
- [查看和下载 Microsoft Azure 发票](../understand/download-azure-invoice.md)
- [查看和下载 Microsoft Azure 使用情况与费用](../understand/download-azure-daily-usage.md)
- [查看和下载计费对象信息的税单](../understand/mca-download-tax-document.md)
- [了解计费对象信息的发票上的费用](../understand/review-customer-agreement-bill.md)
