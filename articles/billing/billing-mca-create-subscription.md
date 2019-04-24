---
title: 创建您的计费帐户的其他 Azure 订阅 |Microsoft Docs
description: 了解如何在 Azure 门户中添加新的 Azure 订阅。
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 105b8481486c088a05e3acb95081d3ee55b55f52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371971"
---
# <a name="create-an-additional-azure-subscription-for-microsoft-customer-agreement"></a>为 Microsoft 客户协议创建其他 Azure 订阅

创建为计费帐户设置单独的环境，用于开发和测试安全或隔离数据出于合规性原因的更多订阅。

本文适用于 Microsoft 客户协议向计费帐户。 [检查是否有权访问 Microsoft 客户协议](#check-access-to-a-microsoft-customer-agreement)。 如果你想要创建其他计费帐户的订阅，请参阅[在 Azure 门户中创建另一订阅](billing-create-subscription.md)。

若要创建的订阅，您必须是**发票部分所有者**，**发票部分参与者**，或**Azure 订阅创建者**。 有关详细信息，请参阅[订阅计费角色和任务](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks)。 若要为他人提供创建计费帐户的 Azure 订阅的权限，请参阅[创建 Azure 订阅的权限授予其他人](#give-others-permission-to-create-azure-subscriptions)。

## <a name="create-a-subscription-in-the-azure-portal"></a>在 Azure 门户中创建的订阅

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 搜索“订阅”。

   ![搜索显示在门户中订阅的屏幕截图](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. 选择“添加”

4. 如果你有权访问多个计费帐户，选择为 Microsoft 客户协议的计费帐户。

   ![显示的屏幕截图创建订阅页](./media/billing-mca-create-subscription/billing-mca-create-azure-subscription.png)

5. 选择计费的配置文件。 为你的订阅的费用将反映在账单资料张发票上并将使用它的付款方法付费。 如果你有权访问计费的只有一个配置文件，则所选内容将灰显。

6. 选择发票部分。 在此部分中的计费配置文件的发票将反映你的订阅的费用。 如果你有权访问只有一个发票部分时，所选内容将灰显。

7. 选择的订阅的计划。 选择**Microsoft Azure 开发测试计划**，如果你打算使用此订阅进行开发或测试工作负载的其他使用**Microsoft Azure 计划**。 如果你有权访问只有一个计划，则所选内容将灰显。

8. 输入订阅的名称。 该名称可帮助你轻松地确定在 Azure 门户中的订阅。

9. 选择“创建”。

## <a name="give-others-permission-to-create-azure-subscriptions"></a>授予其他人创建的 Azure 订阅的权限

将用户添加为 Azure 订阅创建者发票部分上，可以为他们提供权限来创建 Azure 订阅。

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在“成本管理 + 计费”中进行搜索。

   ![搜索显示在门户中订阅的屏幕截图](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. 转到发票部分。 具体取决于您的访问权限，可能需要选择计费帐户或帐单的配置文件。 从计费帐户或配置文件中选择**发票部分**，然后发票部分。

4. 选择**访问管理 (IAM)** 从左上方。

5. 在页面顶部选择“添加”。

6. 选择**Azure 订阅创建者**角色。

   ![显示为用户提供 Azure 订阅的创建者角色的屏幕截图](./media/billing-mca-create-subscription/billing-mca-add-azure-subscription-creator.png)

7. 输入要向其授予访问权限的用户的电子邮件地址。

8. 选择“保存”。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查与 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果有疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

- [授予其他人使用的内置角色创建 Azure 资源的权限](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)
- [创建 windows 虚拟机](../virtual-machines/windows/quick-create-portal.md)
- [创建 linux 虚拟机](../virtual-machines/linux/quick-create-portal.md)
- [创建用来组织和管理资源的管理组](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
