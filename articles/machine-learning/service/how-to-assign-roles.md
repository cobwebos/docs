---
title: 管理 Azure 机器学习工作区中的角色
titleSuffix: Azure Machine Learning
description: 了解如何使用基于角色的访问控制（RBAC）访问 Azure 机器学习工作区。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 07/10/2019
ms.custom: seodec18
ms.openlocfilehash: 0ceb21d6f77fd9694f7cd564c2e89735cf2a774d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497415"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>管理对 Azure 机器学习工作区的访问
[!INCLUDE [aml-applies-to-enterprise-sku](../../../includes/aml-applies-to-enterprise-sku.md)]

本文介绍如何管理对 Azure 机器学习工作区的访问。 [基于角色的访问控制（RBAC）](/azure/role-based-access-control/overview)用于管理对 Azure 资源的访问。 将为 Azure Active Directory 中的用户分配特定角色，从而授予对资源的访问权限。 Azure 既提供内置角色，也提供创建自定义角色的功能。

## <a name="default-roles"></a>默认角色

Azure 机器学习工作区是一种 Azure 资源。 与其他 Azure 资源一样，创建新的 Azure 机器学习工作区时，它附带三个默认角色。 您可以将用户添加到工作区，并将其分配给这些内置角色之一。

| 角色 | 访问级别 |
| --- | --- |
| **读者** | 工作区中的只读操作。 读者可以在工作区中列出和查看资产，但不能创建或更新这些资产。 |
| **参与者** | 查看、创建、编辑或删除工作区中的资产（如果适用）。 例如，参与者可以创建试验、创建或附加计算群集、提交运行和部署 web 服务。 |
| **所有者** | 对工作区的完全访问权限，包括查看、创建、编辑或删除工作区中的资产（如果适用）的功能。 此外，还可以更改角色分配。 |

> [!IMPORTANT]
> 角色访问可划分为 Azure 中的多个级别。 例如，对工作区拥有所有者访问权限的某人可能不具有对包含该工作区的资源组的所有者访问权限。 有关详细信息，请参阅[RBAC 的工作](/azure/role-based-access-control/overview#how-rbac-works)原理。

有关特定内置角色的详细信息，请参阅[Azure 的内置角色](/azure/role-based-access-control/built-in-roles)。

## <a name="manage-workspace-access"></a>管理工作区访问

如果你是工作区的所有者，则可以为工作区添加和删除角色。 你还可以将角色分配给用户。 使用以下链接来了解如何管理访问权限：
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

如果内置角色不足，则可以创建自定义角色。 自定义角色可能在该工作区中具有读取、写入、删除和计算资源权限。 可以使角色在特定的工作区级别、特定的资源组级别或特定的订阅级别可用。

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

## <a name="next-steps"></a>后续步骤

- [企业安全性概述](concept-enterprise-security.md)
- [在虚拟网络内安全地运行试验和推理/评分](how-to-enable-virtual-network.md)
- [教程：定型模型](tutorial-train-models-with-aml.md)
- [资源提供程序操作](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)