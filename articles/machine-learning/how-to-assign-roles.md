---
title: 管理工作区中的角色
titleSuffix: Azure Machine Learning
description: 了解如何使用基于角色的访问控制 (RBAC) 访问 Azure 机器学习工作区。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 03/06/2020
ms.custom: seodec18
ms.openlocfilehash: 127a0a2b7f7573db91df9347169e90de3e14c4c9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79270090"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>管理对 Azure 机器学习工作区的访问权限
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍了如何管理对 Azure 机器学习工作区的访问权限。 [基于角色的访问控制 (RBAC)](/azure/role-based-access-control/overview) 用于管理对 Azure 资源的访问权限。 Azure Active Directory 中的用户可获得特定角色，这些角色授予了对资源的访问权限。 Azure 提供内置角色和创建自定义角色的功能。

## <a name="default-roles"></a>默认角色

Azure 机器学习工作区是一种 Azure 资源。 与其他 Azure 资源一样，创建新的 Azure 机器学习工作区时，它附带三个默认角色。 可以将用户添加到工作区，并将他们分配给这些内置角色之一。

| 角色 | 访问级别 |
| --- | --- |
| **读取者** | 工作区中的只读操作。 读者可以列出和查看工作区中的资产，但不能创建或更新这些资产。 |
| **参与者** | 查看、创建、编辑或删除（如果适用）工作区中的资产。 例如，参与者可以创建试验、创建或附加计算群集、提交运行和部署 Web 服务。 |
| **所有者** | 对工作区的完全访问权限，包括可以查看、创建、编辑或删除（如果适用）工作区中的资产。 此外，可以更改角色分配。 |

> [!IMPORTANT]
> 在 Azure 中，角色访问的作用域可以限定为多个级别。 例如，对工作区具有所有者访问权限的人可能没有对包含工作区的资源组的所有者访问权限。 有关详细信息信息，请参阅 [RBAC 工作原理](/azure/role-based-access-control/overview#how-rbac-works)。

有关特定内置角色的详细信息，请参阅 [Azure 的内置角色](/azure/role-based-access-control/built-in-roles)。

## <a name="manage-workspace-access"></a>管理工作区访问权限

如果你是工作区的所有者，则可以为工作区添加和删除角色。 还可以为用户分配角色。 请通过以下链接了解如何管理访问权限：
- [Azure 门户 UI](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Azure 资源管理器模板](/azure/role-based-access-control/role-assignments-template)

如果已安装 [Azure 机器学习 CLI](reference-azure-machine-learning-cli.md)，则还可以使用 CLI 命令为用户分配角色。

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

`user` 字段是 Azure Active Directory 实例中现有用户的电子邮件地址，该实例中包含工作区父订阅。 下面是此命令的用法示例：

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>创建自定义角色

如果内置角色不足，可以创建自定义角色。 自定义角色可能具有该工作区中的读取、写入、删除和计算资源权限。 可以使角色在特定工作区级别、特定资源组级别或特定订阅级别可用。

> [!NOTE]
> 必须是该级别资源的所有者，才能在该资源中创建自定义角色。

要创建自定义角色，请首先构造角色定义 JSON 文件，指定角色的权限和作用域。 以下示例定义了名为“数据科学家”的自定义角色，其作用域为特定工作区级别：

`data_scientist_role.json`：
```json
{
    "Name": "Data Scientist",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

可以更改 `AssignableScopes` 字段，以在订阅级别、资源组级别或特定工作区级别设置此自定义角色的作用域。

此自定义角色可以在工作区中执行除以下操作之外的所有操作：

- 创建或更新计算资源。
- 删除计算资源。
- 添加、删除或更改角色分配。
- 删除工作区。

要部署此自定义角色，请使用以下 Azure CLI 命令：

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

部署后，此角色在指定工作区中可用。 现在，可以在 Azure 门户中添加和分配此角色。 或者，可以使用 `az ml workspace share` CLI 命令将此角色分配给用户：

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

有关自定义角色的详细信息，请参阅 [Azure 资源的自定义角色](/azure/role-based-access-control/custom-roles)。

有关可用于自定义角色的操作的详细信息，请参阅[资源提供程序操作](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)。


## <a name="frequently-asked-questions"></a>常见问题


### <a name="q-what-are-the-permissions-needed-to-perform-various-actions-in-the-azure-machine-learning-service"></a>Q. 在 Azure 机器学习服务中执行各种操作需要哪些权限？

下表汇总了 Azure 机器学习活动以及在最小作用域内执行它们所需的权限。 例如，如果可以使用工作区作用域（第 4 列）执行某个活动，则具有该权限的所有更高作用域也将自动工作。 此表中的所有路径都是相对于  **的**相对路径`Microsoft.MachineLearningServices/`。

| 活动 | 订阅级作用域 | 资源组级作用域 | 工作区级作用域 |
|---|---|---|---|
| 创建新工作区 | 不是必需 | 所有者或参与者 | 不适用（在创建后成为所有者或继承更高作用域角色） |
| 新建计算群集 | 不是必需 | 不是必需 | 所有者、参与者或自定义角色允许：`workspaces/computes/write` |
| 新建笔记本 VM | 不是必需 | 所有者或参与者 | 不可能 |
| 新建计算实例 | 不是必需 | 不是必需 | 所有者、参与者或自定义角色允许：`workspaces/computes/write` |
| 数据平面活动，例如提交运行、访问数据、部署模型或发布管道 | 不是必需 | 不是必需 | 所有者、参与者或自定义角色允许：`workspaces/*/write` <br/> 请注意，你还需要有一个已注册到工作区的数据存储，以允许 MSI 访问你的存储帐户中的数据。 |


### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>Q. 如何列出我的订阅中的所有自定义角色？

在 Azure CLI 中运行以下命令。

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>Q. 如何查找我的订阅中某个角色的角色定义？

在 Azure CLI 中运行以下命令。 请注意，`<role-name>` 的格式应与上述命令返回的格式相同。

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>Q. 如何更新角色定义？

在 Azure CLI 中运行以下命令。

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

请注意，你需要对新角色定义的整个作用域具有权限。 例如，如果此新角色的作用域跨三个订阅，则你需要对所有三个订阅都具有权限。 

> [!NOTE]
> 角色更新可能需要花费 15 分钟到一小时才能应用于该作用域中的所有角色分配。
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>Q. 是否可以定义阻止更新工作区版本的角色？ 

是，你可以定义阻止更新工作区版本的角色。 由于工作区更新是对工作区对象的 PATCH 调用，因此可以通过在 JSON 定义中的 `"NotActions"` 数组中放置以下操作来实现此目的： 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>Q. 在工作区中执行配额操作需要哪些权限？ 

要在工作区中执行任何与配额相关的操作，你需要具有订阅级权限。 这意味着，只有当你在订阅作用域具有写入权限时，才能为你的托管计算资源设置订阅级配额或工作区级配额。 


## <a name="next-steps"></a>后续步骤

- [企业安全性概述](concept-enterprise-security.md)
- [在虚拟网络中安全运行试验和推理/评分](how-to-enable-virtual-network.md)
- [教程：定型模型](tutorial-train-models-with-aml.md)
- [资源提供程序操作](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)
