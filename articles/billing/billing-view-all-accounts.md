---
title: 在 Azure 门户中查看计费帐户 | Microsoft Docs
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
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 6df787dbc06f7ee56ba9a26c8382396bf3b6e74a
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719604"
---
# <a name="view-billing-accounts-in-azure-portal"></a>在 Azure 门户中查看计费帐户  

当你注册使用 Azure 时，系统将为你创建计费帐户。 可以使用计费帐户管理发票、付款并跟踪成本。 可以访问多个计费帐户。 例如，可以为个人项目注册 Azure。 也可通过组织的企业协议或 Microsoft 客户协议进行访问。 每个这样的方案都会有单独的计费帐户。

Azure 门户当前支持以下类型的计费帐户：

- **Microsoft Online Services 计划**：Microsoft Online Services 计划的计费帐户是通过 Azure 网站注册 Azure 时创建的。 例如，当你注册 [Azure 免费帐户](https://azure.microsoft.com/offers/ms-azr-0044p/)、[采用即用即付费率的帐户](https://azure.microsoft.com/offers/ms-azr-0003p/)或作为 [Visual Studio 订阅者](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)时。

- **企业协议**：当组织签署使用 Azure 的[企业协议 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) 时，将为企业协议创建一个计费帐户。

- **Microsoft 客户协议**：当组织与 Microsoft 代表一起签署 Microsoft 客户协议时，将会为 Microsoft 客户协议创建一个计费帐户。 特定区域的某些客户通过 Azure 网站注册[采用即用即付费率的帐户](https://azure.microsoft.com/offers/ms-azr-0003p/)或者升级其 [Azure 免费帐户](https://azure.microsoft.com/offers/ms-azr-0044p/)，这些客户可能也有一个适用于 Microsoft 客户协议的计费帐户。 有关详细信息，请参阅 [Microsoft 客户协议的计费帐户入门](billing-mca-overview.md)。

<!--Todo Add section to identify the type of accounts -->

## <a name="scopes-for-billing-accounts"></a>计费帐户的范围
范围是计费帐户中的一个节点，用户使用该节点查看和管理计费。 用户可以在其中管理计费数据、付款、发票，以及执行常规的帐户管理。 

### <a name="microsoft-online-services-program"></a>Microsoft Online Services 计划

|范围  |定义  |
|---------|---------|
|计费帐户     | 表示一个或多个 Azure 订阅的单个所有者（帐户管理员）。 帐户管理员有权执行各种计费任务，例如创建订阅、查看发票或更改订阅的账单。  |
|Subscription     |  表示 Azure 资源的分组。 发票在此范围生成。 它自带付款方式，用于发票付款。|


### <a name="enterprise-agreement"></a>企业协议

|范围  |定义  |
|---------|---------|
|计费帐户    | 表示企业协议注册。 发票在此范围生成。 它通过部门和注册帐户来构建。  |
|部门     |  对注册帐户进行的可选分组。      |
|注册帐户     |  表示单个帐户所有者。 Azure 订阅在此范围创建。  |


### <a name="microsoft-customer-agreement"></a>Microsoft 客户协议

|范围  |任务  |
|---------|---------|
|计费帐户     |   表示针对多个 Microsoft 产品和服务的客户协议。 它通过计费对象信息和发票科目构建。   |
|计费配置文件     |  表示发票及其付款方式。 发票在此范围生成。 它可以有多个发票科目。      |
|发票科目     |   表示发票中的一组成本。 订阅和其他购买关联到此范围。    |


## <a name="switch-billing-scope-in-the-azure-portal"></a>在 Azure 门户中切换计费范围


1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 搜索“成本管理 + 计费”。 

   ![显示了 Azure 门户搜索的屏幕截图](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. 在左侧选择“所有计费范围”。 

   ![显示所有计费范围的屏幕截图](./media/billing-view-all-accounts/billing-list-of-accounts.png)

   ** 如果你只能访问一个范围，则看不到“所有计费范围”选项。 

4. 选择要查看详细信息的范围。



## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤
- 了解如何开始[分析成本](../cost-management/quick-acm-cost-analysis.md)。