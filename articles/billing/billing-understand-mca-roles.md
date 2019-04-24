---
title: 了解计费管理角色的 Microsoft 客户协议-Azure
description: 了解如何为 Microsoft 客户协议的计费在 Azure 中的帐户的计费角色。
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberbhargava
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 780870cc71e95507a52ba6a9338026f895a96ac1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370842"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>了解 Microsoft 客户协议在 Azure 中的管理角色

若要管理的 Microsoft 客户协议的计费帐户，请使用以下各节中所述的角色。 这些角色是除了 Azure 必须要控制对资源的访问的内置角色。 有关详细信息，请参阅 [Azure 资源的内置角色](../role-based-access-control/built-in-roles.md)。

本文适用于 Microsoft 客户协议向计费帐户。 检查您是否具有对 Microsoft 客户协议的访问。

## <a name="billing-role-definitions"></a>计费角色定义

下表描述用于管理您的计费帐户，计费配置文件，和发票部分计费的角色。

|角色|描述|
|---|---|
|计费帐户所有者|管理所有内容的计费帐户|
|计费帐户参与者|管理计费帐户上的权限之外的所有内容|
|计费帐户读者|只读的所有内容的视图上计费帐户|
|计费对象信息所有者|管理计费配置文件的所有内容|
|计费对象信息参与者|管理计费的配置文件的权限之外的所有内容|
|计费对象信息读者|只读的所有内容的视图上计费配置文件|
|发票管理器|查看和支付发票计费配置文件|
|发票科目所有者|管理发票一节中的所有内容|
|发票科目参与者|管理发票部分上的权限之外的所有内容|
|发票科目读者|只读的所有内容的视图上的发票部分|
|Azure 订阅创建者|创建 Azure 订阅|

## <a name="billing-account-roles-and-tasks"></a>计费帐户角色和任务

计费帐户允许你管理为你的组织计费。 使用的计费帐户组织成本、 监视费和发票和计费的访问控制为你的组织。 有关详细信息，请参阅[了解计费帐户](billing-mca-overview.md#understand-billing-account)。

下表显示为计费帐户的上下文中完成任务需要何种角色。

### <a name="manage-billing-account-permissions-and-properties"></a>管理计费帐户权限和属性

|任务|计费帐户所有者|计费帐户参与者|计费帐户读者|
|---|---|---|---|
|查看现有计费帐户的权限|✔|✔|✔|
|授予其他人查看和管理的计费帐户的权限|✔|✘|✘|
|视图计费帐户属性，例如公司名称、 地址和的详细信息|✔|✔|✔|

### <a name="manage-billing-profiles-for-billing-account"></a>管理计费的计费帐户的配置文件

|任务|计费帐户所有者|计费帐户参与者|计费帐户读者|
|---|---|---|---|
|在帐户中查看所有计费的配置文件|✔|✔|✔|

### <a name="manage-invoices-for-billing-account"></a>管理发票计费帐户

|任务|计费帐户所有者|计费帐户参与者|计费帐户读者|
|---|---|---|---|
|查看帐户中的所有发票|✔|✔|✔|
|下载发票、 Azure 使用情况和费用文件、 价目表和税款的帐户中的文档|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-account"></a>管理发票的部分，了解计费帐户

|任务|计费帐户所有者|计费帐户参与者|计费帐户读者|
|---|---|---|---|
|查看帐户中的所有发票部分|✔|✔|✔|

### <a name="manage-transactions-for-billing-account"></a>管理事务的计费帐户

|任务|计费帐户所有者|计费帐户参与者|计费帐户读者|
|---|---|---|---|
|查看所有计费事务的帐户|✔|✔|✔|
|查看为帐户购买的所有产品|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-account"></a>管理计费帐户的订阅

|任务|计费帐户所有者|计费帐户参与者|计费帐户读者|
|---|---|---|---|
|查看所有 Azure 订阅中计费帐户|✔|✔|✔|

## <a name="billing-profile-roles-and-tasks"></a>计费对象信息角色和任务

计费的配置文件允许你管理你的发票和付款方法。 每月发票为 Azure 订阅和计费配置文件使用购买其他产品生成。 使用的付款方法按发票。 有关详细信息，请参阅[了解计费资料](billing-mca-overview.md#understand-billing-profiles)。

下表显示了需要在计费的配置文件的上下文中完成任务的作用。

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>管理计费的配置文件权限、 属性和策略

|任务|计费对象信息所有者|计费对象信息参与者|计费对象信息读者|发票管理器|计费帐户所有者|计费帐户参与者|计费帐户读者
|---|---|---|---|---|---|---|---|
|查看现有权限计费的配置文件|✔|✔|✔|✔|✔|✔|✔|
|授予其他人查看和管理计费的配置文件的权限|✔|✘|✘|✘|✘|✘|✘|
|查看计费配置文件属性，如采购订单数、 电子邮件发票首选项，和的详细信息|✔|✔|✔|✔|✔|✔|✔|
|更新计费的配置文件属性 |✔|✔|✘|✘|✘|✘|✘|
|查看策略应用于如计费的配置文件启用 Azure 预订购买，启用 Azure marketplace 购买内容，和的详细信息|✔|✔|✔|✔|✔|✔|✔|
|将策略应用于计费的配置文件 |✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-invoices-for-billing-profile"></a>管理发票计费配置文件

|任务|计费对象信息所有者|计费对象信息参与者|计费对象信息读者|发票管理器|计费帐户所有者|计费帐户参与者|计费帐户读者
|---|---|---|---|---|---|---|---|
|查看账单资料的所有发票|✔|✔|✔|✔|✔|✔|✔|
|下载发票、 Azure 使用情况和费用文件、 价目表和税款计费的配置文件的文档|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>管理发票部分，用于计费的配置文件

|任务|计费对象信息所有者|计费对象信息参与者|计费对象信息读者|发票管理器|计费帐户所有者|计费帐户参与者|计费帐户读者
|---|---|---|---|---|---|---|---|
|查看计费的配置文件的所有发票部分|✔|✔|✔|✔|✔|✔|✔|
|创建新的发票计费的配置文件节|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-transactions-for-billing-profile"></a>管理事务进行计费配置文件

|任务|计费对象信息所有者|计费对象信息参与者|计费对象信息读者|发票管理器|计费帐户所有者|计费帐户参与者|计费帐户读者
|---|---|---|---|---|---|---|---|
|查看所有计费事务计费的配置文件|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-payment-methods-for-billing-profile"></a>管理付款方式进行计费配置文件

|任务|计费对象信息所有者|计费对象信息参与者|计费对象信息读者|发票管理器|计费帐户所有者|计费帐户参与者|计费帐户读者
|---|---|---|---|---|---|---|---|
|有关计费的配置文件查看付款方法|✔|✔|✔|✔|✔|✔|✔|
|跟踪你的帐单的配置文件的 Azure 信用额度余额|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>管理计费配置文件的订阅

|任务|计费对象信息所有者|计费对象信息参与者|计费对象信息读者|发票管理器|计费帐户所有者|计费帐户参与者|计费帐户读者
|---|---|---|---|---|---|---|---|
|查看所有 Azure 订阅计费的配置文件|✔|✔|✔|✔|✔|✔|✔|

## <a name="invoice-section-roles-and-tasks"></a>发票科目角色和任务

发票部分，可将组织发票上的成本。 您可以创建一个部分来组织你的成本按部门，开发环境中，或根据组织的需求。 授予其他人创建的部分 Azure 订阅的权限。 任何使用费用和购买订阅则显示发票的部分。 有关详细信息，请参阅[了解发票部分](billing-mca-overview.md#understand-invoice-sections)。

下表显示了所需要的发票部分上下文中完成任务的角色。

### <a name="manage-invoice-section-permissions-and-properties"></a>管理发票部分权限和属性

|任务|发票科目所有者|发票科目参与者|发票科目读者|Azure 订阅创建者|计费帐户所有者|计费帐户参与者|计费帐户读者 | |
|---|---|---|---|---|---|---|---|---|
|查看发票部分上的所有权限|✔|✔|✔|✔|✔|✔|✔| |
|授予其他人查看和管理的发票部分的权限|✔|✘|✘|✘|✘|✘|✘| |
|查看发票部分属性|✔|✔|✔|✔|✔|✔|✔| |
|更新发票部分属性|✔|✔|✘|✘|✘|✘|✘|✘|

### <a name="manage-products-for-invoice-section"></a>管理发票部分的产品

|任务|发票科目所有者|发票科目参与者|发票科目读者|Azure 订阅创建者|计费帐户所有者|计费帐户参与者|计费帐户读者
|---|---|---|---|---|---|---|---|
|查看购买发票部分中的所有产品|✔|✔|✔|✘|✔|✔|✔|
|管理计费发票等取消一节的产品，请关闭自动续订，和的详细信息|✔|✔|✘|✘|✘|✘|✘|
|更改发票部分中的产品|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>管理发票科目的订阅

|任务|发票科目所有者|发票科目参与者|发票科目读者|Azure 订阅创建者|计费帐户所有者|计费帐户参与者|计费帐户读者
|---|---|---|---|---|---|---|---|
|查看所有 Azure 订阅的发票部分|✔|✔|✔|✘|✔|✔|✔|
|更改订阅的发票部分|✔|✔|✘|✘|✘|✘|✘|
|从其他计费帐户中的用户请求订阅的计费所有权|✔|✔|✘|✘|✘|✘|✘|

## <a name="subscription-billing-roles-and-tasks"></a>订阅计费角色和任务

下表显示了需要在订阅的上下文中完成任务的作用。

|任务|发票科目所有者|发票科目参与者|发票科目读者|Azure 订阅创建者|
|---|---|---|---|---|
|创建 Azure 订阅|✔|✔|✘|✔|
|更新订阅的成本中心|✔|✔|✘|✘|
|更改订阅的发票部分|✔|✔|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>管理计费在 Azure 门户中的角色

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在“成本管理 + 计费”中进行搜索。

   ![显示了 Azure 门户搜索的屏幕截图](./media/billing-understand-mca-roles/billing-search-cost-management-billing.png)

3. 选择**访问控制 (IAM)** 如计费帐户、 账单资料或发票部分中，你想要授予访问权限范围内。

4. 访问控制 (IAM) 页列出了用户和组分配给该作用域的每个角色。

   ![显示的计费帐户的管理员列表的屏幕截图](./media/billing-understand-mca-roles/billing-list-admins.png)

5. 若要向用户授予访问权限，请选择**添加**从页面顶部。 在角色下拉列表中选择一个角色。 输入要向其授予访问权限的用户的电子邮件地址。 选择**保存**分配角色。

   ![显示将管理员添加到计费帐户的屏幕截图](./media/billing-understand-mca-roles/billing-add-admin.png)

6. 若要删除用户的访问权限，请选择你想要删除的角色分配的用户。 选择删除。

   ![显示从计费帐户中删除管理员的屏幕截图](./media/billing-understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查与 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员
如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解有关您的计费帐户信息：

- [获取为 Microsoft 客户协议声明与您的计费帐户](billing-mca-overview.md)
- [为 Microsoft 客户协议创建为计费帐户的 Azure 订阅](billing-mca-create-subscription.md)
