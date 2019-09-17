---
title: 为计费帐户创建其他 Azure 订阅
description: 了解如何在 Azure 门户中添加新的 Azure 订阅。
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 558410d980d261780f7287d1e27ed704b356fc2b
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490952"
---
# <a name="create-an-additional-azure-subscription-for-microsoft-customer-agreement"></a>为 Microsoft 客户协议创建其他 Azure 订阅

为计费帐户创建其他订阅，以便出于合规性原因，为开发和测试、安全性或隔离数据设置单独的环境。

本文适用于 Microsoft 客户协议的计费帐户。 [检查是否有权访问 Microsoft 客户协议](#check-access)。 若要为其他类型的计费帐户创建订阅，请参阅[在 Azure 门户中创建其他订阅](billing-create-subscription.md)。

若要创建订阅，必须是**发票科目所有者**、**发票科目参与者**或 **Azure 订阅创建者**。 有关详细信息，请参阅[订阅计费角色和任务](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks)。 若要向其他人提供为计费帐户创建 Azure 订阅的权限，请参阅[授予其他人创建 Azure 订阅的权限](#give-others-permission)。

## <a name="create-a-subscription"></a>创建订阅

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 搜索“订阅”。 

   ![显示在门户中搜索订阅的屏幕截图](./media/billing-mca-create-subscription/billing-search-subscriptions.png)

3. 选择“添加” 

4. 如果有权访问多个计费帐户，请为 Microsoft 客户协议选择计费帐户。

   ![显示“创建订阅”页的屏幕截图](./media/billing-mca-create-subscription/billing-mca-create-azure-subscription.png)

5. 选择计费对象信息。 订阅费用将计入所选计费对象信息。 如果只能访问一个计费对象信息，则选项将灰显。

6. 选择发票科目。 订阅费用将计入计费对象信息发票的此科目。 如果只能访问一个发票科目，则选项将灰显。

7. 选择订阅计划。 如果你计划将此订阅用于开发或测试工作负荷，请选择 **Microsoft Azure 开发测试计划**，否则请使用 **Microsoft Azure 计划**。 如果只能访问一个计划，则选项将灰显。

8. 输入订阅的名称。 该名称可帮助你轻松识别 Azure 门户中的订阅。

9. 选择“创建”  。

## <a name="give-others-permission"></a>向其他人授予权限

在发票科目上将用户添加为 Azure 订阅创建者，以授予他们创建 Azure 订阅的权限。

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 搜索“成本管理 + 计费”  。

   ![显示在门户中搜索订阅的屏幕截图](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. 转到发票部分。 根据你的访问权限，可能需要选择计费帐户或计费对象信息。 从计费帐户或计费对象信息中，选择“发票科目”  ，然后从列表中选择发票科目。 用户将创建的任何订阅都将计入此发票科目。
   
   ![显示选择发票科目的屏幕截图](./media/billing-mca-create-subscription/mca-select-invoice-sections.png)        

4. 从左上方选择“访问管理(IAM)”  。

5. 在页面顶部选择“添加”。 

6. 选择“Azure 订阅创建者”  作为角色。

7. 输入要向其授予访问权限的用户的电子邮件地址。

8. 选择“保存”。 

## <a name="check-access"></a>检查访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [向其他人授予使用内置角色创建 Azure 资源的权限](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)
- [创建 Windows 虚拟机](../virtual-machines/windows/quick-create-portal.md)
- [创建 Linux 虚拟机](../virtual-machines/linux/quick-create-portal.md)
- [创建用来组织和管理资源的管理组](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
