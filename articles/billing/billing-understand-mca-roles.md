---
title: 了解 Microsoft 客户协议的计费管理角色 - Azure
description: 了解 Azure 中 Microsoft 客户协议中计费帐户的计费角色。
author: amberbhargava
manager: amberbhargava
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: f143297ce3a3e41174cbd6cf59ca3df29f8ebc63
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223513"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>了解 Azure 中的 Microsoft 客户协议管理角色

若要管理 Microsoft 客户协议的计费帐户，请使用以下部分所述的角色。 这些角色为 Azure 用来控制对资源的访问的内置角色提供了补充。 有关详细信息，请参阅 [Azure 资源的内置角色](../role-based-access-control/built-in-roles.md)。

本文适用于 Microsoft 客户协议的计费帐户。 检查你是否有权访问 Microsoft 客户协议。

## <a name="billing-role-definitions"></a>计费角色的定义

下表描述了用来管理计费帐户、计费配置文件和发票科目的计费角色。

|角色|说明|
|---|---|
|计费帐户所有者|管理计费帐户的所有内容|
|计费帐户参与者|管理计费帐户的所有内容，但权限除外|
|计费帐户读取者|查看计费帐户中所有内容的只读视图|
|计费配置文件所有者|管理计费配置文件的所有内容|
|计费配置文件参与者|管理计费配置文件的所有内容，但权限除外|
|计费配置文件读取者|查看计费配置文件中所有内容的只读视图|
|发票管理者|查看和支付计费配置文件的发票|
|发票科目所有者|管理发票科目中的所有内容|
|发票科目参与者|管理发票科目的所有内容，但权限除外|
|发票科目读取者|查看发票科目中所有内容的只读视图|
|Azure 订阅创建者|创建 Azure 订阅|

## <a name="billing-account-roles-and-tasks"></a>计费帐户角色和任务

使用计费帐户可以管理组织的帐单。 可以使用计费帐户来组织成本、监视费用和发票，以及控制组织的计费访问。 有关详细信息，请参阅[了解计费帐户](billing-mca-overview.md#your-billing-account)。

下表显示了在计费帐户上下文中完成任务时所需的角色。

### <a name="manage-billing-account-permissions-and-properties"></a>管理计费帐户权限和属性

|任务|计费帐户所有者|计费帐户参与者|计费帐户读取者|
|---|---|---|---|
|查看计费帐户的现有权限|✔|✔|✔|
|向其他人授予查看和管理计费帐户的权限|✔|✘|✘|
|查看公司名称、地址等计费帐户属性|✔|✔|✔|

### <a name="manage-billing-profiles-for-billing-account"></a>管理计费帐户的计费配置文件

|任务|计费帐户所有者|计费帐户参与者|计费帐户读取者|
|---|---|---|---|
|查看帐户中的所有计费配置文件|✔|✔|✔|

### <a name="manage-invoices-for-billing-account"></a>管理计费帐户的发票

|任务|计费帐户所有者|计费帐户参与者|计费帐户读取者|
|---|---|---|---|
|查看帐户中的所有发票|✔|✔|✔|
|在帐户中下载发票、Azure 使用情况和费用文件、价目表和税务文档|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-account"></a>管理计费帐户的发票科目

|任务|计费帐户所有者|计费帐户参与者|计费帐户读取者|
|---|---|---|---|
|查看帐户中的所有发票科目|✔|✔|✔|

### <a name="manage-transactions-for-billing-account"></a>管理计费帐户的交易

|任务|计费帐户所有者|计费帐户参与者|计费帐户读取者|
|---|---|---|---|
|查看帐户的所有计费交易|✔|✔|✔|
|查看为帐户购买的所有产品|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-account"></a>管理计费帐户的订阅

|任务|计费帐户所有者|计费帐户参与者|计费帐户读取者|
|---|---|---|---|
|查看计费帐户中的所有 Azure 订阅|✔|✔|✔|

## <a name="billing-profile-roles-and-tasks"></a>计费配置文件角色和任务

使用计费配置文件可以管理发票和付款方式。 对于使用计费配置文件购买的 Azure 订阅和其他产品，将会生成月份发票。 可以使用所需的付款方式来支付发票。 有关详细信息，请参阅[了解计费配置文件](billing-mca-overview.md#billing-profiles)。

下表显示了在计费配置文件上下文中完成任务时所需的角色。

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>管理计费配置文件的权限、属性和策略

|任务|计费配置文件所有者|计费配置文件参与者|计费配置文件读取者|发票管理者|计费帐户所有者|计费帐户参与者|计费帐户读取者
|---|---|---|---|---|---|---|---|
|查看计费配置文件的现有权限|✔|✔|✔|✔|✔|✔|✔|
|向其他人授予查看和管理计费配置文件的权限|✔|✘|✘|✘|✘|✘|✘|
|查看计费配置文件的属性，例如 PO 编号、电子邮件发票首选项，等等|✔|✔|✔|✔|✔|✔|✔|
|更新计费配置文件属性 |✔|✔|✘|✘|✘|✘|✘|
|查看对计费配置文件应用的策略，例如启用 Azure 预留项购买、启用 Azure 市场购买，等等|✔|✔|✔|✔|✔|✔|✔|
|对计费配置文件应用策略 |✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-invoices-for-billing-profile"></a>管理计费配置文件的发票

|任务|计费配置文件所有者|计费配置文件参与者|计费配置文件读取者|发票管理者|计费帐户所有者|计费帐户参与者|计费帐户读取者
|---|---|---|---|---|---|---|---|
|查看计费配置文件的所有发票|✔|✔|✔|✔|✔|✔|✔|
|下载计费配置文件的发票、Azure 使用情况和费用文件、价目表和税务文档|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>管理计费配置文件的发票科目

|任务|计费配置文件所有者|计费配置文件参与者|计费配置文件读取者|发票管理者|计费帐户所有者|计费帐户参与者|计费帐户读取者
|---|---|---|---|---|---|---|---|
|查看计费配置文件的所有发票科目|✔|✔|✔|✔|✔|✔|✔|
|为计费配置文件创建新的发票科目|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-transactions-for-billing-profile"></a>管理计费配置文件的交易

|任务|计费配置文件所有者|计费配置文件参与者|计费配置文件读取者|发票管理者|计费帐户所有者|计费帐户参与者|计费帐户读取者
|---|---|---|---|---|---|---|---|
|查看计费配置文件的所有计费交易|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-payment-methods-for-billing-profile"></a>管理计费配置文件的付款方式

|任务|计费配置文件所有者|计费配置文件参与者|计费配置文件读取者|发票管理者|计费帐户所有者|计费帐户参与者|计费帐户读取者
|---|---|---|---|---|---|---|---|
|查看计费配置文件的付款方式|✔|✔|✔|✔|✔|✔|✔|
|跟踪计费配置文件的 Azure 剩余额度|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>管理计费配置文件的订阅

|任务|计费配置文件所有者|计费配置文件参与者|计费配置文件读取者|发票管理者|计费帐户所有者|计费帐户参与者|计费帐户读取者
|---|---|---|---|---|---|---|---|
|查看计费配置文件的所有 Azure 订阅|✔|✔|✔|✔|✔|✔|✔|

## <a name="invoice-section-roles-and-tasks"></a>发票科目角色和任务

发票科目可用于组织发票中的成本。 可以创建一个科目来按部门、开发环境或根据组织的需求组织成本。 向其他人授予为科目创建 Azure 订阅的权限 然后，订阅的任何使用费和购买项目会显示在发票科目中。 有关详细信息，请参阅[了解发票科目](billing-mca-overview.md#invoice-sections)。

下表显示了在发票科目上下文中完成任务时所需的角色。

### <a name="manage-invoice-section-permissions-and-properties"></a>管理发票科目的权限和属性

|任务|发票科目所有者|发票科目参与者|发票科目读取者|Azure 订阅创建者|计费帐户所有者|计费帐户参与者|计费帐户读取者 | |
|---|---|---|---|---|---|---|---|---|
|查看对发票科目的所有权限|✔|✔|✔|✔|✔|✔|✔| |
|向其他人授予查看和管理发票科目的权限|✔|✘|✘|✘|✘|✘|✘| |
|查看发票科目属性|✔|✔|✔|✔|✔|✔|✔| |
|更新发票科目属性|✔|✔|✘|✘|✘|✘|✘|✘|

### <a name="manage-products-for-invoice-section"></a>管理发票科目的产品

|任务|发票科目所有者|发票科目参与者|发票科目读取者|Azure 订阅创建者|计费帐户所有者|计费帐户参与者|计费帐户读取者
|---|---|---|---|---|---|---|---|
|查看在发票科目中购买的所有产品|✔|✔|✔|✘|✔|✔|✔|
|管理发票科目的产品计费，例如取消、禁用自动续订，等等|✔|✔|✘|✘|✘|✘|✘|
|更改产品的发票科目|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>管理发票科目的订阅

|任务|发票科目所有者|发票科目参与者|发票科目读取者|Azure 订阅创建者|计费帐户所有者|计费帐户参与者|计费帐户读取者
|---|---|---|---|---|---|---|---|
|查看发票科目的所有 Azure 订阅|✔|✔|✔|✘|✔|✔|✔|
|更改订阅的发票科目|✔|✔|✘|✘|✘|✘|✘|
|从其他计费帐户中的用户请求订阅的计费所有权|✔|✔|✘|✘|✘|✘|✘|

## <a name="subscription-billing-roles-and-tasks"></a>订阅计费角色和任务

下表显示了在订阅上下文中完成任务时所需的角色。

|任务|发票科目所有者|发票科目参与者|发票科目读取者|Azure 订阅创建者|
|---|---|---|---|---|
|创建 Azure 订阅|✔|✔|✘|✔|
|更新订阅的成本中心|✔|✔|✘|✘|
|更改订阅的发票科目|✔|✔|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>在 Azure 门户中管理计费角色

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 搜索“成本管理 + 计费”。 

   ![显示了 Azure 门户搜索的屏幕截图](./media/billing-understand-mca-roles/billing-search-cost-management-billing.png)

3. 在要其授予访问权限的范围（例如计费帐户、计费配置文件或发票科目）选择“访问控制(IAM)”。 

4. “访问控制(IAM)”页将列出已在该范围分配到每个角色的用户和组。

   ![显示计费帐户的管理员列表的屏幕截图](./media/billing-understand-mca-roles/billing-list-admins.png)

5. 若要向用户授予访问权限，请在页面顶部选择“添加”。  在“角色”下拉列表中选择一个角色。 输入要向其授予访问权限的用户的电子邮件地址。 选择“保存”以分配该角色。 

   ![显示如何将管理员添加到计费帐户的屏幕截图](./media/billing-understand-mca-roles/billing-add-admin.png)

6. 若要删除用户的访问权限，请选择要删除其角色分配的用户。 选择“删除”。

   ![显示如何从计费帐户中删除管理员的屏幕截图](./media/billing-understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查对 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员
如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤

请参阅以下文章了解计费帐户：

- [开始使用 Microsoft 客户协议的计费帐户](billing-mca-overview.md)
- [为 Microsoft 客户协议的计费帐户创建 Azure 订阅](billing-mca-create-subscription.md)
