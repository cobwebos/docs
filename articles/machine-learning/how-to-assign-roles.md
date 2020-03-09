---
title: 管理工作区中的角色
titleSuffix: Azure Machine Learning
description: 了解如何使用基于角色的访问控制（RBAC）访问 Azure 机器学习工作区。
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
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2020
ms.locfileid: "78932619"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>管理对 Azure 机器学习工作区的访问
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何管理对 Azure 机器学习工作区的访问。 [基于角色的访问控制（RBAC）](/azure/role-based-access-control/overview)用于管理对 Azure 资源的访问。 将为 Azure Active Directory 中的用户分配特定角色，从而授予对资源的访问权限。 Azure 既提供内置角色，也提供创建自定义角色的功能。

## <a name="default-roles"></a>默认角色

Azure 机器学习工作区是一种 Azure 资源。 与其他 Azure 资源一样，创建新的 Azure 机器学习工作区时，它附带三个默认角色。 您可以将用户添加到工作区，并将其分配给这些内置角色之一。

| 角色 | 访问级别 |
| --- | --- |
| **读取者** | 工作区中的只读操作。 读者可以列出和查看工作区中的资产，但不能创建或更新这些资产。 |
| **参与者** | 查看、创建、编辑或删除（如果适用）工作区中的资产。 例如，参与者可以创建试验、创建或附加计算群集、提交运行和部署 Web 服务。 |
| **所有者** | 对工作区的完全访问权限，包括可以查看、创建、编辑或删除（如果适用）工作区中的资产。 此外，可以更改角色分配。 |

> [!IMPORTANT]
> 角色访问可划分为 Azure 中的多个级别。 例如，对工作区拥有所有者访问权限的某人可能不具有对包含该工作区的资源组的所有者访问权限。 有关详细信息，请参阅[RBAC 的工作](/azure/role-based-access-control/overview#how-rbac-works)原理。

有关特定内置角色的详细信息，请参阅[Azure 的内置角色](/azure/role-based-access-control/built-in-roles)。

## <a name="manage-workspace-access"></a>管理工作区访问

如果你是工作区的所有者，则可以为工作区添加和删除角色。 还可以为用户分配角色。 请通过以下链接了解如何管理访问权限：
- [Azure 门户 UI](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Azure 资源管理器模板](/azure/role-based-access-control/role-assignments-template)

如果已安装[AZURE 机器学习 cli](reference-azure-machine-learning-cli.md)，还可以使用 cli 命令向用户分配角色。

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

"`user`" 字段是工作区父订阅所在 Azure Active Directory 实例中现有用户的电子邮件地址。 下面是有关如何使用此命令的示例：

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>创建自定义角色

如果内置角色不足，可以创建自定义角色。 自定义角色可能在该工作区中具有读取、写入、删除和计算资源权限。 可以使角色在特定的工作区级别、特定的资源组级别或特定的订阅级别可用。

> [!NOTE]
> 您必须是该级别的资源所有者才能在该资源中创建自定义角色。

若要创建自定义角色，请首先构造角色定义 JSON 文件，该文件指定角色的权限和作用域。 以下示例定义了在特定工作区级别范围内的名为 "数据科研" 的自定义角色：

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

你可以更改 "`AssignableScopes`" 字段，以便在订阅级别、资源组级别或特定工作区级别设置此自定义角色的作用域。

此自定义角色可执行工作区中的所有操作，但以下操作除外：

- 不能创建或更新计算资源。
- 它无法删除计算资源。
- 它无法添加、删除或更改角色分配。
- 它无法删除工作区。

若要部署此自定义角色，请使用以下 Azure CLI 命令：

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

部署之后，此角色将在指定的工作区中可用。 现在，你可以在 Azure 门户中添加并分配此角色。 或者，可以使用 `az ml workspace share` CLI 命令将此角色分配给用户：

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

有关自定义角色的详细信息，请参阅[Azure 资源的自定义角色](/azure/role-based-access-control/custom-roles)。

有关自定义角色可用的操作（操作）的详细信息，请参阅[资源提供程序操作](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)。


## <a name="frequently-asked-questions"></a>常见问题


### <a name="q-what-are-the-permissions-needed-to-perform-various-actions-in-the-azure-machine-learning-service"></a>Q. 在 Azure 机器学习服务中执行各种操作所需的权限有哪些？

下表汇总了 Azure 机器学习活动以及在最小范围内执行它们所需的权限。 例如，如果可以使用工作区范围（第4列）执行活动，则具有该权限的所有更高的作用域也将自动运行。 此表中的所有路径都是 `Microsoft.MachineLearningServices/`的**相对路径**。

| 活动 | 订阅级别范围 | 资源组级别范围 | 工作区级别范围 |
|---|---|---|---|
| 创建新工作区 | 不是必需 | 所有者或参与者 | 不适用（在创建后成为所有者或继承更高作用域角色） |
| 创建新的计算群集 | 不是必需 | 不是必需 | 所有者、参与者或自定义角色允许： `workspaces/computes/write` |
| 创建新的笔记本 VM | 不是必需 | 所有者或参与者 | 不可能 |
| 创建新的计算实例 | 不是必需 | 不是必需 | 所有者、参与者或自定义角色允许： `workspaces/computes/write` |
| 数据平面活动，如提交运行、访问数据、部署模型或发布管道 | 不是必需 | 不是必需 | 所有者、参与者或自定义角色允许： `workspaces/*/write` <br/> 请注意，你还需要一个已注册到工作区的数据存储，以允许 MSI 访问你的存储帐户中的数据。 |


### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>Q. 如何实现列出订阅中的所有自定义角色？

在 Azure CLI 中运行以下命令。

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>Q. 如何实现在我的订阅中查找角色的角色定义？

在 Azure CLI 中运行以下命令。 请注意，`<role-name>` 的格式应与上述命令返回的格式相同。

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>Q. 如何实现更新角色定义？

在 Azure CLI 中运行以下命令。

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

请注意，你需要对新角色定义的整个作用域具有权限。 例如，如果此新角色的作用域跨越三个订阅，则需要拥有所有三个订阅的权限。 

> [!NOTE]
> 角色更新可能需要15分钟到1小时才能应用于该范围内的所有角色分配。
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>Q. 能否定义阻止更新工作区版本的角色？ 

是的，你可以定义一个阻止更新工作区版本的角色。 由于工作区更新是对工作区对象的修补调用，因此可以通过在 JSON 定义中的 `"NotActions"` 数组中放置以下操作来实现此目的： 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>Q. 在工作区中执行配额操作需要哪些权限？ 

你需要订阅级别权限才能在工作区中执行任何与配额相关的操作。 这意味着，只有在订阅作用域具有写入权限时，才会设置托管计算资源的订阅级别配额或工作区级别配额。 


## <a name="next-steps"></a>后续步骤

- [企业安全性概述](concept-enterprise-security.md)
- [在虚拟网络内安全地运行试验和推理/评分](how-to-enable-virtual-network.md)
- [教程：定型模型](tutorial-train-models-with-aml.md)
- [资源提供程序操作](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)
