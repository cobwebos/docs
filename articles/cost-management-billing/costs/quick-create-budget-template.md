---
title: 快速入门 - 使用 Azure 资源管理器模板创建预算
description: 本快速入门介绍如何使用 Azure 资源管理器模板创建预算。
author: bandersmsft
ms.author: banders
tags: azure-resource-manager
ms.service: cost-management-billing
ms.topic: quickstart
ms.date: 07/28/2020
ms.custom: subject-armqs
ms.openlocfilehash: 70408a3ed6638ec76af113c24cc3c8190a44f55c
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87445994"
---
# <a name="quickstart-create-a-budget-with-an-arm-template"></a>快速入门：使用 ARM 模板创建预算

可以通过成本管理中的预算来计划并推动组织责任制。 可以通过预算对特定时期使用或订阅的 Azure 服务进行核算。 可以通过预算将他人的支出通知给本人，方便他们对成本进行前摄性管理，并且可以监视一段时间的支出情况。 超出所创建的预算阈值时，会触发通知。 不会影响资源，也不会停止你对资源的使用。 可以使用预算来比较和跟踪支出，就像分析成本一样。 本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）创建预算。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

如果你有新订阅，则无法立即创建预算或使用其他成本管理功能。 最多可能需要 48 小时才能使用所有成本管理功能。

以下 Azure 帐户类型和范围都支持预算：

- Azure 基于角色的访问控制 (Azure RBAC) 范围
    - 管理组
    - 订阅
- 企业协议范围
    - 计费帐户
    - 部门
    - 注册帐户
- 个人协议
    - 计费帐户
- Microsoft 客户协议范围
    - 计费帐户
    - 计费配置文件
    - 发票科目
    - 客户
- AWS 范围
    - 外部帐户
    - 外部订阅

若要查看预算，你至少需要对 Azure 帐户具有读取访问权限。

对于 Azure EA 订阅，必须拥有读取访问权限才能查看预算。 若要创建和管理预算，必须具有参与者权限。

每个订阅支持以下 Azure 权限或范围，以便按用户和组进行预算。 有关范围的详细信息，请参阅[了解并使用范围](understand-work-scopes.md)。

- 所有者 - 可以为订阅创建、修改或删除预算。
- 参与者和成本管理参与者 - 可以创建、修改或删除自己的预算。 可以修改其他人创建的预算的预算金额。
- 读者和成本管理读者 - 可以查看他们有权访问的预算。

若要详细了解如何分配对成本管理数据的权限，请参阅[分配对成本管理数据的访问权限](assign-access-acm-data.md)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/create-budget)。

:::code language="json" source="~/quickstart-templates/create-budget/azuredeploy.json" :::

模板中定义了一个 Azure 资源：

* [Microsoft.Consumption/budgets](/azure/templates/microsoft.consumption/budgets)：创建 Azure 预算。

## <a name="deploy-the-template"></a>部署模板

1. 选择下图登录到 Azure 并打开一个模板。 该模板将创建预算。

   [![部署到 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

2. 选择或输入以下值。

   :::image type="content" source="./media/quick-create-budget-template/create-budget-using-template-portal.png" alt-text="资源管理器模板，创建预算，部署门户]" lightbox="./media/quick-create-budget-template/create-budget-using-template-portal.png" :::
   
    * 订阅：选择一个 Azure 订阅。
    * 资源组：如果需要，请选择现有资源组或创建新资源组。
    * 区域：选择 Azure 区域。 例如“美国中部”。
    * **预算名称**：为预算输入一个名称。 此名称在资源组中应是唯一的。 只允许输入字母数字、下划线和连字符。
    * 数量：输入要通过预算跟踪的总成本。
    * **时间粒度**：输入预算覆盖的时间。 允许的值为“每月”、“每季度”或“每年”。 预算将在该时间粒度结束时重置。
    * **开始日期**：以 YYYY-MM-DD 格式输入预算月份的第一天作为开始日期。 将来的开始日期不能晚于自当前日期算起的三个月。 可以使用“时间粒度”时段指定过去的开始日期。
    * **结束日期**：以 YYYY-MM-DD 格式输入预算的结束日期。 
    * 第一个阈值：输入第一个通知的阈值。 当成本超过阈值时，会发送通知。 该值始终为百分比，并且必须介于 0 和 1000 之间。
    * 第二个阈值：输入第二个通知的阈值。 当成本超过阈值时，会发送通知。 该值始终为百分比，并且必须介于 0 和 1000 之间。
    * **联系人角色**：输入超出阈值时要将预算通知发送到的联系人角色列表。 默认值为“所有者”、“参与者”和“读取者”。 预期格式为 `["Owner","Contributor","Reader"]`。
    * 联系人电子邮件：输入超出阈值时要将预算通知发送到的电子邮件地址列表。 预期格式为 `["user1@domain.com","user2@domain.com"]`。
    * 联系人组：输入超出阈值时要将预算通知发送到的操作组资源 ID 列表（作为完整资源 URI）。 此字段接受字符串数组。 预期格式为 `["action group resource ID1","action group resource ID2"]`。 如果你不想使用操作组，请输入 `[]`。
    * 资源组筛选器值：输入要筛选的资源组名称的列表。 预期格式为 `["Resource Group Name1","Resource Group Name2"]`。 如果你不想应用筛选器，请输入 `[]`。 
    * 计量类别筛选器值：输入 Azure 服务计量器类别的列表。 预期格式为 `["Meter Category1","Meter Category2"]`。 如果你不想应用筛选器，请输入 `[]`。
   
3. 根据你的 Azure 订阅类型，执行以下操作之一：
   - 选择“查看 + 创建”。
   - 查看条款和条件，选择“我同意上述条款和条件”，然后选择“购买”。

4. 如果选择“审阅 + 创建”，则将验证模板。 选择“创建”。  

   ![资源管理器模板，预算，部署门户通知](./media/quick-create-budget-template/resource-manager-template-portal-deployment-notification.png)

使用 Azure 门户部署模板。 除了 Azure 门户之外，还可以使用 Azure PowerShell、Azure CLI 和 REST API。 若要了解其他部署模板，请参阅[部署模板](../../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="validate-the-deployment"></a>验证部署

可以在 Azure 门户中导航到“成本管理 + 计费”> 选择范围 >“预算”来验证是否已创建该预算 。 或者，使用以下 Azure CLI 或 Azure PowerShell 脚本来查看预算。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az consumption budget list
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzConsumptionBudget
```

---

## <a name="clean-up-resources"></a>清理资源

如果不再需要预算，请使用以下某种方法将其删除：

### <a name="azure-portal"></a>Azure 门户

导航到“成本管理 + 计费”> 选择“计费范围”>“预算”> 选择“预算”> 然后选择“删除预算”  。

### <a name="command-line"></a>命令行

可以使用 Azure CLI 或 Azure PowerShell 删除预算。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the budget name:" &&
read budgetName &&
az consumption budget delete --budget-name $budgetName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$budgetName = Read-Host -Prompt "Enter the budget name"
Remove-AzConsumptionBudget -Name $budgetName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个用于部署的 Azure 预算。 若要详细了解 Azure 成本管理和计费以及 Azure 资源管理器，请继续阅读以下文章。

- 阅读[成本管理和计费](../cost-management-billing-overview.md)概述
- 在 Azure 门户中[创建预算](tutorial-acm-create-budgets.md)
- 了解有关 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)的详细信息
