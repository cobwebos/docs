---
title: 创建另外的 Azure 订阅
description: 了解如何在 Azure 门户中添加新的 Azure 订阅。
services: billing
documentationcenter: ''
author: amberb
manager: amberb
editor: ''
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: banders
ms.openlocfilehash: 33fa553e9148a1ac838e6bf53fc83cf7eadd7499
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75992577"
---
# <a name="create-an-additional-azure-subscription"></a>创建另外的 Azure 订阅

可以在 Azure 门户中为[企业协议 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)、[Microsoft 客户协议](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/)或 [Microsoft 合作伙伴协议](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement)计费帐户创建另一个订阅。 你可能希望添加另外的订阅以避免达到订阅限制，为安全性创建单独的环境，或者因为合规性原因而隔离数据。

如果有一个 Microsoft Online Services 计划 (MOSP) 计费帐户，则可在 [Azure 注册门户](https://account.azure.com/signup?offer=ms-azr-0003p)中创建其他订阅。

若要详细了解计费帐户并确定计费帐户的类型，请参阅[在 Azure 门户中查看计费帐户](view-all-accounts.md)。

## <a name="permission-required-to-create-azure-subscriptions"></a>创建 Azure 订阅所需的权限

若要创建订阅，需要以下权限：

|计费帐户  |权限  |
|---------|---------|
|企业协议 (EA) |  企业协议许可登记表上的“帐户所有者”角色。 有关详细信息，请参阅[了解 Azure 中的 Azure 企业协议管理角色](understand-ea-roles.md)。    |
|Microsoft 客户协议 (MCA) |  发票科目、计费对象信息或计费帐户上的所有者或参与者角色。 或发票科目上的 Azure 订阅创建者角色。  有关详细信息，请参阅[订阅计费角色和任务](understand-mca-roles.md#subscription-billing-roles-and-tasks)。    |
|Microsoft 合作伙伴协议 (MPA) |   CSP 合作伙伴组织中的“全局管理员”和“管理员代理”角色。 若要了解详细信息，请参阅[合作伙伴中心 - 为用户分配角色和权限](https://docs.microsoft.com/partner-center/permissions-overview)。  用户需要登录合作伙伴租户才能创建 Azure 订阅。   |

## <a name="create-a-subscription-in-the-azure-portal"></a>在 Azure 门户中创建订阅

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 搜索“订阅”。

   ![其中显示了在门户中搜索订阅的屏幕截图](./media/create-subscription/billing-search-subscription-portal.png)

1. 选择 **添加** 。

   ![其中显示了“订阅”视图中的“添加”按钮的屏幕截图](./media/create-subscription/subscription-add.png)

1. 如果有权访问多个计费帐户，请选择要为其创建订阅的计费帐户。

1. 填充表单，并单击“创建”。 下面的表列出了表单上每种计费帐户的字段。

**企业协议**

|字段  |定义  |
|---------|---------|
|名称     | 该显示名称可帮助你轻松识别 Azure 门户中的订阅。  |
|产品     | 如果计划将此订阅用于开发或测试工作负荷，请选择“EA 开发/测试”，否则请使用“Microsoft Azure 企业”。 DevTest 套餐必须处于启用状态，否则注册帐户无法创建 EA 开发/测试订阅。|

**Microsoft 客户协议**

|字段  |定义  |
|---------|---------|
|计费配置文件     | 订阅费用将计入所选计费对象信息。 如果只能访问一个计费对象信息，则选项将灰显。     |
|发票科目     | 订阅费用会显示在计费对象信息发票的此科目中。 如果只能访问一个发票科目，则选项将灰显。  |
|套餐     | 如果你计划将此订阅用于开发或测试工作负荷，请选择 Microsoft Azure 开发测试计划，否则请使用 Microsoft Azure 计划。 如果只为计费对象信息启用了一个计划，则选项将灰显。  |
|名称     | 该显示名称可帮助你轻松识别 Azure 门户中的订阅。  |

**Microsoft 合作伙伴协议**

|字段  |定义  |
|---------|---------|
|客户    | 已为选定客户创建此订阅。 如果只有一个客户，则选项将灰显。  |
|Reseller    | 将为客户提供服务的经销商。 这是可选字段，仅适用于 CSP 双层模型中的间接提供商。 |
|名称     | 该显示名称可帮助你轻松识别 Azure 门户中的订阅。  |

## <a name="create-an-additional-azure-subscription-programmatically"></a>以编程方式创建另一个 Azure 订阅

你还可以通过编程方式创建另外的订阅。 有关详细信息，请参阅[以编程方式创建 Azure 订阅](../../azure-resource-manager/management/programmatically-create-subscription.md)。

## <a name="next-steps"></a>后续步骤

- [添加或更改 Azure 订阅管理员](add-change-subscription-administrator.md)
- [将资源移到新资源组或订阅中](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [创建用来组织和管理资源的管理组](../../governance/management-groups/create.md)
- [取消针对 Azure 的订阅](cancel-azure-subscription.md)

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。
