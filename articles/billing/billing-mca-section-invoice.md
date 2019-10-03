---
title: 在发票上创建科目以管理成本 - Azure
description: 了解如何按发票科目管理成本。
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.topic: conceptual
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: ba2a274397aa1941ef2be03ce97b0f0c9ce96a31
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709568"
---
# <a name="create-sections-on-your-invoice-to-organize-your-costs"></a>在发票上创建科目以管理成本

在发票上创建科目，以按部门、开发环境或根据组织的需要管理成本。 然后授予其他人创建向该科目计费的 Azure 订阅的权限。 然后，对订阅的任何使用费和购买费都将记入该科目。 可以在 Azure 门户中查看发票上该科目的总费用，或在 Azure 成本分析中查看这些费用。 有关详细信息，请参阅[按发票科目查看交易](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections)。

本文适用于 Microsoft 客户协议的计费帐户。 [检查是否有权访问 Microsoft 客户协议](#check-access-to-a-microsoft-customer-agreement)。

## <a name="create-an-invoice-section-in-the-azure-portal"></a>在 Azure 门户中创建发票科目

若要创建发票科目，需要是**计费对象信息所有者**或**计费对象信息参与者**。 有关详细信息，请参阅[管理计费对象信息的发票科目](billing-understand-mca-roles.md#manage-invoice-sections-for-billing-profile)。

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 搜索“成本管理 + 计费”。 

   ![显示了 Azure 门户搜索的屏幕截图](./media/billing-mca-section-invoice/billing-search-cost-management-billing.png)

3. 从左侧窗格中选择“发票科目”  。 根据你的访问权限，可能需要选择计费对象信息或计费帐户，然后选择“发票科目”  。

   ![显示发票科目列表的屏幕截图](./media/billing-mca-section-invoice/mca-select-invoice-sections.png)

4. 在页面顶部选择“添加”。 

5. 输入发票科目的名称并选择计费对象信息。 你会看到此计费对象信息发票上的科目反映了你分配给该科目的每个订阅和购买项的使用情况。 

   ![显示发票科目创建页的屏幕截图](./media/billing-mca-section-invoice/mca-create-invoice-section.png)

6. 选择“创建”  。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查对 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤

- [为 Microsoft 客户协议创建其他 Azure 订阅](billing-mca-create-subscription.md)
- [向其他人授予创建 Azure 订阅的权限](billing-mca-create-subscription.md#give-others-permission)
- [从其他计费帐户中的用户获取 Azure 订阅的计费所有权](billing-mca-request-billing-ownership.md)
