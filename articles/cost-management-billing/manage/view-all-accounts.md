---
title: 在 Azure 门户中查看计费帐户 | Microsoft Docs
description: 了解如何在 Azure 门户中查看计费帐户。
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 2768d6e146a37e86bb36353f661179ebd7b5033d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75994124"
---
# <a name="billing-accounts-and-scopes-in-the-azure-portal"></a>Azure 门户中的计费帐户和范围

当你注册使用 Azure 时，系统将为你创建计费帐户。 可以使用计费帐户管理发票、付款并跟踪成本。 可以访问多个计费帐户。 例如，可以为个人项目注册 Azure。 也可通过组织的企业协议或 Microsoft 客户协议进行访问。 每个这样的方案都会有单独的计费帐户。

Azure 门户支持以下类型的计费帐户：

- **Microsoft Online Services 计划**：通过 azure 网站注册 azure 时，将创建 Microsoft Online services 计划的计费帐户。 例如，当你注册 [Azure 免费帐户](https://azure.microsoft.com/offers/ms-azr-0044p/)、[采用即用即付费率的帐户](https://azure.microsoft.com/offers/ms-azr-0003p/)或作为 [Visual Studio 订阅者](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)时。

- **企业协议**：当组织签署[企业协议（EA）](https://azure.microsoft.com/pricing/enterprise-agreement/)以使用 Azure 时，将创建企业协议的计费帐户。

- **Microsoft 客户协议**：如果你的组织与 microsoft 代表签署 Microsoft 客户协议，则会创建 Microsoft 客户协议的计费帐户。 对于[使用即用即付费率](https://azure.microsoft.com/offers/ms-azr-0003p/)或[azure 免费帐户](https://azure.microsoft.com/offers/ms-azr-0044p/)的帐户，所选区域中的某些客户也可能具有 Microsoft 客户协议的计费帐户。 有关详细信息，请参阅 [Microsoft 客户协议的计费帐户入门](../understand/mca-overview.md)。

- **Microsoft 合作伙伴协议**：为云解决方案提供商（CSP）合作伙伴创建一个适用于 Microsoft 合作伙伴协议的计费帐户，以在新的商业体验中管理其客户。 合作伙伴需要至少有一位客户使用[Azure 计划](https://docs.microsoft.com/partner-center/purchase-azure-plan)来管理 Azure 门户中的计费帐户。 有关详细信息，请参阅[Microsoft 合作伙伴协议的计费帐户入门](../understand/mpa-overview.md)。

若要确定计费帐户的类型，请参阅[检查计费帐户的类型](#check-the-type-of-your-account)。

## <a name="scopes-for-billing-accounts"></a>计费帐户的范围
作用域是计费帐户中用于查看和管理计费的节点。 您可以在其中管理计费数据、支付、发票，以及执行常规帐户管理。

### <a name="microsoft-online-services-program"></a>Microsoft Online Services 计划

 ![显示 MOSP 层次结构的屏幕截图](./media/view-all-accounts/mosp-hierarchy.png)

|范围  |定义  |
|---------|---------|
|计费帐户     | 表示客户接受使用 Azure 的协议。 它包含一个或多个订阅。  |
|订阅     |  表示 Azure 资源的分组。 发票在此范围生成。 其他计费信息（如支付方法和使用地址）将与此作用域相关联。|

### <a name="enterprise-agreement"></a>企业协议

![显示 EA 层次结构的屏幕截图](./media/view-all-accounts/ea-hierarchy.png)

|范围  |定义  |
|---------|---------|
|计费帐户    | 表示企业协议注册。 它包含一个或多个部门和帐户。 发票在此范围生成。 |
|部门     |  可选的帐户分组，将成本分成逻辑分组并设置预算。     |
|帐户     |  表示单个帐户所有者。 帐户所有者有权创建和管理对注册计费的 Azure 订阅。 |

### <a name="microsoft-customer-agreement"></a>Microsoft 客户协议

![显示 MCA 层次结构的屏幕截图](./media/view-all-accounts/mca-hierarchy.png)

|范围  |任务  |
|---------|---------|
|计费帐户     |   表示客户接受使用 Microsoft 产品和服务的协议。 它包含一个或多个计费配置文件。 |
|计费配置文件     |   表示发票和相关计费信息，例如支付方法和帐单地址。 它包含一个或多个发票部分。 |
|发票科目     |   表示发票中的成本分组。 Azure 订阅和其他购买（如 Azure Marketplace 和应用源产品）与此作用域相关联。    |

### <a name="microsoft-partner-agreement"></a>Microsoft 合作伙伴协议

![显示 MPA 层次结构的屏幕截图](./media/view-all-accounts/mpa-hierarchy.png)

|范围  |任务  |
|---------|---------|
|计费帐户     |   表示在新的商业体验中管理客户的 Microsoft 产品和服务的合作伙伴协议。 它包含一个或多个计费配置文件和客户。   |
|计费配置文件     |   表示货币的发票。     |
|客户    |   表示云解决方案提供商（CSP）合作伙伴的客户。  Azure 订阅和其他购买（如 Azure Marketplace 和应用源产品）与此作用域相关联。  |
|Reseller    |   为客户提供服务的分销商。 它是订阅的可选字段，仅适用于 CSP 两层模型中的间接提供程序。     |

## <a name="switch-billing-scope-in-the-azure-portal"></a>在 Azure 门户中切换计费范围

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 搜索“成本管理 + 计费”。

   ![显示了 Azure 门户搜索的屏幕截图](./media/view-all-accounts/billing-search-cost-management-billing.png)

3. 在 "概述" 页中，选择 "**切换范围**"。

   ![显示计费范围的屏幕截图](./media/view-all-accounts/overview-select-scopes.png)

   > [!Note]
    >
    > 如果你只有一个作用域的访问权限，则你将看不到切换范围。

4. 选择要查看详细信息的范围。

   ![显示计费范围的屏幕截图](./media/view-all-accounts/list-of-scopes.png)

## <a name="check-the-type-of-your-account"></a>检查你的帐户类型
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤
- 了解如何开始[分析成本](../costs/quick-acm-cost-analysis.md)。
