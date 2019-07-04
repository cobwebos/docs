---
title: 在 Azure 门户中查看计费帐户 |Microsoft Docs
description: 了解如何在 Azure 门户中查看计费帐户。
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2018
ms.author: banders
ms.openlocfilehash: 36430e9b0a4554761d53b537d3c32fa57068eabb
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490221"
---
# <a name="view-billing-accounts-in-azure-portal"></a>在 Azure 门户中查看计费帐户  

注册以使用 Azure 时创建计费帐户。 使用您的计费帐户来管理您的发票付款，并跟踪成本。 可以具有多个计费帐户的访问权限。 例如，你可能已注册 Azure 为您个人的项目。 你也可以通过组织的企业协议或 Microsoft 客户协议的访问权限。 对于每种方案，必须具有单独的计费帐户。

Azure 门户当前支持以下类型的计费帐户：

- **Microsoft Online Services 计划**:当你注册 Azure 通过 Azure 网站创建 Microsoft Online Services 计划的计费帐户。 例如，当你注册[Azure 免费帐户](https://azure.microsoft.com/offers/ms-azr-0044p/)，[帐户与即用即付费率](https://azure.microsoft.com/offers/ms-azr-0003p/)或是[Visual studio 订阅者](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)。

- **企业协议**：企业协议的计费帐户创建时你的组织[企业协议 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)若要使用 Azure。

- **Microsoft 客户协议**:你的组织的工作方式与 Microsoft 代表以签署 Microsoft 客户协议时创建的 Microsoft 客户协议的计费帐户。 选择区域中注册的 Azure 网站，通过一些客户[帐户与即用即付费率](https://azure.microsoft.com/offers/ms-azr-0003p/)或升级其[Azure 免费帐户](https://azure.microsoft.com/offers/ms-azr-0044p/)可能就 Microsoft 客户的计费帐户也协议。 有关详细信息，请参阅[开始使用您的计费帐户的 Microsoft 客户协议](billing-mca-overview.md)。

<!--Todo Add section to identify the type of accounts -->

## <a name="scopes-for-billing-accounts"></a>计费帐户的作用域
作用域是用户用于查看和管理计费的计费帐户中的节点。 它是用户管理帐单数据，付款的发票，并且执行常规帐户管理。 

### <a name="microsoft-online-services-program"></a>Microsoft Online Services 计划

|范围  |定义  |
|---------|---------|
|计费帐户     | 表示一个或多个 Azure 订阅所有者 （帐户管理员）。 帐户管理员有权执行各种计费任务，例如创建订阅，查看发票或更改订阅的计费。  |
|订阅     |  表示一组 Azure 资源。 在此作用域生成发票。 它具有其自己的付款方法用来向其发票付款。|


### <a name="enterprise-agreement"></a>企业协议

|范围  |定义  |
|---------|---------|
|计费帐户    | 表示企业协议注册。 在此作用域生成发票。 它是结构化使用部门和注册帐户。  |
|系     |  注册帐户的可选分组。      |
|注册帐户     |  表示单个帐户所有者。 此作用域下创建 azure 订阅。  |


### <a name="microsoft-customer-agreement"></a>Microsoft 客户协议

|范围  |任务  |
|---------|---------|
|计费帐户     |   表示多个 Microsoft 产品和服务的客户协议。 它是结构化使用计费的配置文件和发票部分。   |
|计费配置文件     |  表示发票和其支付方法。 在此作用域生成发票。 它可以包含多个发票部分。      |
|发票部分     |   表示一组在发票中的成本。 订阅和其他购买将关联到此作用域。    |


## <a name="switch-billing-scope-in-the-azure-portal"></a>交换机计费在 Azure 门户中的作用域


1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 搜索**成本管理 + 计费**。

   ![显示了 Azure 门户搜索的屏幕截图](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. 选择**计费的所有作用域**从左上方。

   ![显示所有计费作用域的屏幕截图](./media/billing-view-all-accounts/billing-list-of-accounts.png)

   \* * 则不会看到**计费的所有作用域**如果您仅有权访问一个作用域。

4. 选择要查看详细信息的作用域。



## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如果有疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤
- 了解如何开始[分析成本](../cost-management/quick-acm-cost-analysis.md)。