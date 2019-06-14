---
title: 查看和下载你的组织的 Azure 定价 |Microsoft Docs
description: 了解如何查看和下载定价或估计成本与你的组织定价。
services: ''
documentationcenter: ''
author: adpick
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: d4c64433089f14d845cea8be9adf0ef13675cfd1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127627"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>查看和下载你的组织的 Azure 定价

Azure 客户具有企业协议 (EA) 或[Microsoft 客户协议](#check-your-access-to-a-microsoft-customer-agreement)可以查看和下载它们从 Azure 门户的定价。 如果你有 Microsoft 客户协议，请参阅视图和下载你的 Microsoft 客户协议的定价。

## <a name="view-and-download-ea-pricing"></a>查看和下载 EA 定价

只有某些管理角色可以访问你的组织的 EA 定价信息，具体取决于企业管理员为你的组织设置的策略。 有关详细信息，请参阅[了解 Azure 中的 Azure 企业协议管理角色](billing-understand-ea-roles.md)。

1. 以企业管理员身份登录到 [Azure 门户](https://portal.azure.com/)。
1. 在“成本管理 + 计费”  中进行搜索。

   ![显示了 Azure 门户搜索的屏幕截图](./media/billing-ea-pricing/portal-cm-billing-search.png)

1. 在计费帐户下，选择“使用情况 + 费用”。 

   ![其中显示了“计费”下的使用情况和费用的屏幕截图](./media/billing-ea-pricing/ea-pricing-usage-charges-nav.png)

1. 针对相应的月份选择 ![其中显示了 Azure 门户搜索的屏幕截图](./media/billing-ea-pricing/download-icon.png) **下载**。
1. 在“价目表”  下，选择“下载 csv”  。

   ![其中显示了价目表“下载 csv”按钮的屏幕截图](./media/billing-ea-pricing/download-ea-price-sheet.png)

## <a name="view-and-download-pricing-for-your-microsoft-customer-agreement"></a>查看和下载你的 Microsoft 客户协议的定价

您必须是计费的配置文件所有者、 参与者、 读取器或发票管理器查看和下载定价。 若要了解 Microsoft 客户协议计费角色的详细信息，请参阅[计费的配置文件角色和任务](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)。

### <a name="download-price-sheets-for-the-current-billing-period"></a>下载适用于当前计费周期价目表

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在“成本管理 + 计费”  中进行搜索。
1. 选择计费的配置文件。 具体取决于您的访问权限，可能需要首先选择计费帐户。
1. 在中**概述**边栏选项卡中找到的月-日费用下方的下载链接。
1. 选择**Azure 价目表**。
![从概述显示下载的屏幕截图](./media/billing-ea-pricing/open-pricing.png)

### <a name="download-price-sheets-for-billed-charges"></a>下载计费费用的价目表

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在“成本管理 + 计费”  中进行搜索。
1. 选择计费的配置文件。 具体取决于您的访问权限，可能需要首先选择计费帐户。
1. 选择“发票”  。
1. 在发票网格中，查找对应于你想要下载的价目表的发票。
1. 单击省略号 (`...`) 的行的末尾。
![显示所选的省略号的屏幕截图](./media/billing-ea-pricing/billingprofile-invoicegrid.png)

1. 如果你想要查看所选发票中的服务的价格，请选择**发票价目表**。
1. 如果你想要查看给定计费周期的所有 Azure 服务的价格，请选择**Azure 价目表**。

![显示上下文菜单，其中价目表的屏幕截图](./media/billing-ea-pricing/contextmenu-pricesheet.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>使用 Azure 定价计算器估算成本

此外可以使用组织的定价与 Azure 定价计算器估算成本。

1. 转到 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator)。
1. 在右上角，选择“登录”  。
1. 在“程序和产品/服务” > “许可计划”下，选择“企业协议 (EA)”。   
1. 在“程序和产品/服务” > “所选协议”下，选择“未选择任何项”。   

    ![其中显示了价目表“下载 csv”按钮的屏幕截图](./media/billing-ea-pricing/ea-pricing-calculator-estimate.png)

1. 选择组织。
1. 选择“应用”。 
1. 搜索产品并将其添加到估计中。
1. 将根据适用于你所选组织的定价显示估计的价格。

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>检查你对 Microsoft 客户协议的访问
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="next-steps"></a>后续步骤

如果你是 EA 客户，请参阅：

- [管理对 Azure 的 EA 计费信息的访问](billing-manage-access.md)
- [查看和下载 Microsoft Azure 发票](billing-download-azure-invoice.md)
- [查看和下载你的 Microsoft Azure 使用情况和费用](billing-download-azure-daily-usage.md)
- [了解你的企业协议客户的帐单](billing-understand-your-bill-ea.md)

如果你有 Microsoft 客户协议，请参阅：

- [了解 Microsoft 客户协议的价格表中的条款](billing-mca-understand-pricesheet.md)
- [查看和下载 Microsoft Azure 发票](billing-download-azure-invoice.md)
- [查看和下载你的 Microsoft Azure 使用情况和费用](billing-download-azure-daily-usage.md)
- [查看和下载你计费的配置文件的税务文档](billing-mca-download-tax-document.md)
- [了解计费配置文件的发票费用](billing-mca-understand-your-bill.md)
