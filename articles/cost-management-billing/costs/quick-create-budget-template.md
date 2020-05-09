---
title: 快速入门 - 使用 Azure 资源管理器模板创建预算
description: 本快速入门介绍如何使用 Azure 资源管理器模板创建预算。
author: bandersmsft
ms.author: banders
tags: azure-resource-manager
ms.service: cost-management-billing
ms.topic: quickstart
ms.date: 04/22/2020
ms.custom: subject-armqs
ms.openlocfilehash: de24895334ec4c864e6daae84a6aab47a47d7b9b
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103627"
---
# <a name="quickstart-create-a-budget-with-an-azure-resource-manager-template"></a>快速入门：使用 Azure 资源管理器模板创建预算

可以通过成本管理中的预算来计划并推动组织责任制。 可以通过预算对特定时期使用或订阅的 Azure 服务进行核算。 可以通过预算将他人的支出通知给本人，方便他们对成本进行前摄性管理，并且可以监视一段时间的支出情况。 超出所创建的预算阈值时，会触发通知。 不会影响资源，也不会停止你对资源的使用。 可以使用预算来比较和跟踪支出，就像分析成本一样。 本快速入门介绍如何使用资源管理器模板创建预算。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>先决条件

Azure 资源管理器模板仅支持 Azure 企业协议 (EA) 订阅。 该模板不支持其他订阅类型。

若要创建和管理预算，必须具有参与者权限。 可以为 EA 订阅和资源组创建单独的预算。 但是，不能为 EA 计费帐户创建预算。 对于 Azure EA 订阅，必须拥有读取访问权限才能查看预算。

创建预算后，至少需要对 Azure 帐户拥有读取权限才能查看预算。

如果你有新订阅，则无法立即创建预算或使用其他成本管理功能。 最多可能需要 48 小时才能使用所有成本管理功能。

每个订阅支持以下 Azure 权限或范围，以便按用户和组进行预算。 有关范围的详细信息，请参阅[了解并使用范围](understand-work-scopes.md)。

- 所有者 - 可以为订阅创建、修改或删除预算。
- 参与者和成本管理参与者 - 可以创建、修改或删除自己的预算。 可以修改其他人创建的预算的预算金额。
- 读者和成本管理读者 - 可以查看他们有权访问的预算。

若要详细了解如何分配对成本管理数据的权限，请参阅[分配对成本管理数据的访问权限](assign-access-acm-data.md)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/create-budget)。

:::code language="json" source="~/quickstart-templates/create-budget/azuredeploy.json":::

模板中定义了一个 Azure 资源：

* [Microsoft.Consumption/budgets](/azure/templates/microsoft.consumption/budgets)：创建 Azure 预算。

## <a name="deploy-the-template"></a>部署模板

1. 选择下图登录到 Azure 并打开一个模板。 该模板将创建预算。

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json"><img src="./media/quick-create-budget-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. 选择或输入以下值。

   [![资源管理器模板，创建预算，部署门户](./media/quick-create-budget-template/create-budget-using-template-portal.png)](./media/quick-create-budget-template/create-budget-using-template-portal.png#lightbox)

    * **订阅**：选择一个 Azure 订阅。
    * **资源组**：选择“新建”并输入资源组的唯一名称，然后单击“确定”；或者选择现有的资源组   。
    * **位置**：选择一个位置。 例如“美国中部”。 
    * **预算名称**：为预算输入一个名称。 此名称在资源组中应是唯一的。 只允许输入字母数字、下划线和连字符。
    * **数量**：输入要通过预算跟踪的总成本或使用量。
    * **预算类别**：选择预算的类别，以及预算是要跟踪“成本”还是“用量”   。
    * **时间粒度**：输入预算覆盖的时间。 允许的值为“每月”、“每季度”或“每年”。 预算将在该时间粒度结束时重置。
    * **开始日期**：以 YYYY-MM-DD 格式输入预算月份的第一天作为开始日期。 将来的开始日期不能晚于自当前日期算起的三个月。 可以使用“时间粒度”时段指定过去的开始日期。
    * **结束日期**：以 YYYY-MM-DD 格式输入预算的结束日期。 如果未提供，则默认值设置为从开始日期算起的 10 年。
    * **运算符**：选择一个比较运算符。 可能的值包括 EqualTo、GreaterThan 或 GreaterThanOrEqualTo。
    * **阈值**：输入通知的阈值。 当成本超过阈值时，会发送通知。 该值始终为百分比，并且必须介于 0 和 1000 之间。
    * **联系人电子邮件**：输入超出阈值时要将预算通知发送到的电子邮件地址列表。 预期格式为 `["user1@domain.com","user2@domain.com"]`。
    * **联系人角色**：输入超出阈值时要将预算通知发送到的联系人角色列表。 默认值为“所有者”、“参与者”和“读取者”。 预期格式为 `["Owner","Contributor","Reader"]`。
    * **联系人组**：输入超出阈值时要将预算通知发送到的操作组列表。 此字段接受字符串数组。 预期格式为 `["Action Group Name1","Action Group Name2"]`。 如果你不想使用操作组，请输入 `[]`。
    * **资源筛选器**：输入针对资源的筛选器列表。 预期格式为 `["Resource Filter Name1","Resource Filter Name2"]`。 如果你不想应用筛选器，请输入 `[]`。 如果输入了资源筛选器，则还必须输入“计量器筛选器”值  。
    * **计量器筛选器**：输入针对计量器的筛选器列表，对于“用量”预算类别的预算，必须填写此字段  。 预期格式为 `["Meter Filter Name1","Meter Filter Name2"]`。 如果未输入资源筛选器  ，请输入 `[]`。
    * **我同意上述条款和条件**：选中。

3. 选择“购买”。  成功部署预算后，将会收到通知：

   ![资源管理器模板，预算，部署门户通知](./media/quick-create-budget-template/resource-manager-template-portal-deployment-notification.png)

使用 Azure 门户部署模板。 除了 Azure 门户之外，还可以使用 Azure PowerShell、Azure CLI 和 REST API。 若要了解其他部署模板，请参阅[部署模板](../../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="validate-the-deployment"></a>验证部署

可以在 Azure 门户中导航到“成本管理 + 计费”> 选择范围 >“预算”来验证是否已创建该预算   。 或者，使用以下 Azure CLI 或 Azure PowerShell 脚本来查看预算。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az consumption budget list
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzConsumptionBudget
```

---

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个用于部署的 Azure 预算。 若要详细了解 Azure 成本管理和计费以及 Azure 资源管理器，请继续阅读以下文章。

- 阅读[成本管理和计费](../cost-management-billing-overview.md)概述
- 在 Azure 门户中[创建预算](tutorial-acm-create-budgets.md)
- 了解有关 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)的详细信息