---
title: 查看和下载 Azure 使用情况和费用 |Microsoft Docs
description: 介绍如何下载或查看 Azure 的每日使用情况和费用。
keywords: 计费使用情况，使用费用，使用情况下载，请查看使用情况，azure 发票，azure 使用情况
services: billing
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 520d3f6a45b44ba2023dee34642f796689f48221
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60918964"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>查看和下载 Azure 使用情况和费用

如果你是 EA 客户，或者具有[Microsoft 客户协议](#check-your-access-to-a-microsoft-customer-agreement)，可以下载 Azure 用量和费用[Azure 门户](https://portal.azure.com/)。 有关其他订阅，请转到[Azure 帐户中心](https://account.azure.com/Subscriptions)若要下载使用情况。

只有特定角色有权获得 Azure 使用情况信息，如帐户管理员或企业管理员。 若要详细了解如何获取计费信息，请参阅[使用角色管理对 Azure 计费的访问](billing-manage-access.md)。

如果有[Microsoft 客户协议](#check-your-access-to-a-microsoft-customer-agreement)，您必须是账单资料所有者、 参与者、 读取器，或发票管理器来查看你的 Azure 使用情况和费用。 若要了解 Microsoft 客户协议计费角色的详细信息，请参阅[计费的配置文件角色和任务](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)。

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

若要查看和下载 EA 客户的使用情况数据，必须是企业管理员，帐户所有者，或与视图的部门管理费用启用策略。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索*成本管理 + 计费*。

    ![显示了 Azure 门户搜索的屏幕截图](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. 选择“使用情况 + 费用”。 
1. 针对要下载的月份，选择“下载”。 

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>为 Microsoft 客户协议下载使用情况

如果你有 Microsoft 客户协议，可以为你计费的配置文件中下载 Azure 使用情况和费用。 您必须是计费配置文件所有者、 参与者、 读取器，或发票管理器下载 Azure 使用情况和费用 CSV。

### <a name="download-usage-for-billed-charges"></a>下载使用情况的计费费用

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“成本管理 + 计费”  中进行搜索。
3. 选择计费的配置文件。 具体取决于您的访问权限，可能需要首先选择计费帐户。
4. 选择“发票”  。
5. 在发票网格中，查找对应于你想要下载的使用情况的发票。
6. 单击省略号 (`...`) 的行的末尾。

    ![显示在行尾的省略号的屏幕截图](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)

7. 在下载上下文菜单中，选择**Azure 使用情况和费用**。

     ![显示 Azure 使用情况和费用所选的屏幕截图](./media/billing-download-azure-usage/contextmenu-usage.png)

### <a name="download-usage-for-pending-charges"></a>下载使用情况的挂起的费用

当前计费期间，还可以下载月-日使用情况。 尚不计费有这些使用费。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“成本管理 + 计费”  中进行搜索。
3. 选择计费的配置文件。 具体取决于您的访问权限，可能需要首先选择计费帐户。
4. 在中**概述**边栏选项卡中找到的月-日费用下方的下载链接。
5. 选择**Azure 使用情况和费用**。

    ![从概述显示下载的屏幕截图](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>检查你对 Microsoft 客户协议的访问
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如果有疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要了解有关发票和使用情况费用的详细信息，请参阅：

- [了解 Microsoft Azure 详细使用条款](billing-understand-your-usage.md)
- [了解 Microsoft Azure 账单](billing-understand-your-bill.md)
- [查看和下载 Microsoft Azure 发票](billing-download-azure-invoice.md)
- [查看和下载你的组织的 Azure 定价](billing-ea-pricing.md)

如果你有 Microsoft 客户协议，请参阅：

- [了解有关 Microsoft 客户协议 Azure 术语的详细使用情况](billing-mca-understand-your-usage.md)
- [了解 Microsoft 客户协议发票费用](billing-mca-understand-your-bill.md)
- [查看和下载 Microsoft Azure 发票](billing-download-azure-invoice.md)
- [查看并下载适用于你的 Microsoft 客户协议的税务文档](billing-mca-download-tax-document.md)
- [查看和下载你的组织的 Azure 定价](billing-ea-pricing.md)
