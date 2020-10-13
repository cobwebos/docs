---
title: 管理工作区中的角色
titleSuffix: Azure Machine Learning
description: 了解如何使用基于角色的访问控制 (RBAC) 访问 Azure 机器学习工作区。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: Blackmist
ms.author: nigup
author: nishankgu
ms.date: 07/24/2020
ms.custom: how-to, seodec18
ms.openlocfilehash: ab94af9ec172a3e88d523024c1e00d3a0d944798
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91873075"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>管理对 Azure 机器学习工作区的访问权限

本文介绍了如何管理对 Azure 机器学习工作区的访问权限。 使用 azure [RBAC)  (azure 基于角色的访问控制](/azure/role-based-access-control/overview)来管理对 Azure 资源的访问。 Azure Active Directory 中的用户可获得特定角色，这些角色授予了对资源的访问权限。 Azure 提供内置角色和创建自定义角色的功能。

## <a name="default-roles"></a>默认角色

Azure 机器学习工作区是一种 Azure 资源。 与其他 Azure 资源一样，当创建新的 Azure 机器学习工作区时，它附带三个默认角色。 可以将用户添加到工作区，并将他们分配给这些内置角色之一。

| 角色 | 访问级别 |
| --- | --- |
| **读者** | 工作区中的只读操作。 读取者可以在工作区中列出和查看资产，包括[数据存储](how-to-access-data.md)凭据。 读取者不能创建或更新这些资产。 |
| **参与者** | 在工作区中查看、创建、编辑或删除（如果适用）资产。 例如，参与者可以创建试验、创建或附加计算群集、提交运行和部署 Web 服务。 |
| **所有者** | 对工作区的完全访问权限，包括能够在工作区中查看、创建、编辑或删除（如果适用）资产。 此外，还可以更改角色分配。 |
| **自定义角色** | 允许你自定义对工作区中特定控件或数据平面操作的访问权限。 例如，提交运行、创建计算、部署模型或注册数据集。 |

> [!IMPORTANT]
> 在 Azure 中，角色访问的作用域可以限定为多个级别。 例如，对工作区具有所有者访问权限的人可能没有对包含工作区的资源组的所有者访问权限。 有关详细信息信息，请参阅 [RBAC 工作原理](/azure/role-based-access-control/overview#how-rbac-works)。

有关特定内置角色的详细信息，请参阅 [Azure 的内置角色](/azure/role-based-access-control/built-in-roles)。

## <a name="manage-workspace-access"></a>管理工作区访问权限

如果你是工作区的所有者，则可以为工作区添加和删除角色。 还可以将角色分配给用户。 使用以下链接了解如何管理访问权限：
- [Azure 门户 UI](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Azure Resource Manager 模板](/azure/role-based-access-control/role-assignments-template)

如果已安装 [Azure 机器学习 CLI](reference-azure-machine-learning-cli.md)，则可使用 CLI 命令为用户分配角色：

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

`user` 字段是 Azure Active Directory 实例中现有用户的电子邮件地址，该实例中包含工作区父订阅。 下面是此命令的用法示例：

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

> [!NOTE]
> “az ml workspace share”命令对 Azure Active Directory B2B 的联合帐户不起作用。 请使用 Azure UI 门户而不是命令。


## <a name="azure-machine-learning-operations"></a>Azure 机器学习操作

适用于许多操作和任务的 Azure 机器学习内置操作。 有关完整列表，请参阅 [Azure 资源提供程序操作](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)。

## <a name="create-custom-role"></a>创建自定义角色

如果内置角色不够，可以创建自定义角色。 自定义角色可能具有该工作区中的读取、写入、删除和计算资源权限。 可以使角色在特定工作区级别、特定资源组级别或特定订阅级别可用。

> [!NOTE]
> 必须是该级别资源的所有者，才能在该资源中创建自定义角色。

要创建自定义角色，请首先构造角色定义 JSON 文件，指定角色的权限和作用域。 以下示例定义了名为“Data Scientist Custom”的自定义角色，其作用域为特定工作区级别：

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

> [!TIP]
> 可以更改 `AssignableScopes` 字段，以在订阅级别、资源组级别或特定工作区级别设置此自定义角色的作用域。
> 上述自定义角色只是一个示例，请参阅我们建议的一些 [Azure 机器学习服务自定义角色](#customroles)。

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

有关自定义角色的详细信息，请参阅 [Azure 自定义角色](/azure/role-based-access-control/custom-roles)。 有关可用于自定义角色的操作（Actions 和 NotActions）的详细信息，请参阅[资源提供程序操作](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)。

## <a name="frequently-asked-questions"></a>常见问题


### <a name="q-what-are-the-permissions-needed-to-perform-some-common-scenarios-in-the-azure-machine-learning-service"></a>问： 在 Azure 机器学习服务中执行一些常见方案需要哪些权限？

下表汇总了 Azure 机器学习活动以及在最小作用域内执行它们所需的权限。 例如，如果可以使用某个工作区作用域（第 4 列）执行某个活动，自然也可以使用具有该权限的所有更高的作用域：

> [!IMPORTANT]
> 此表中以 `/` 开头的所有路径都是相对于 `Microsoft.MachineLearningServices/` 的**相对路径**：

| 活动 | 订阅级作用域 | 资源组级作用域 | 工作区级作用域 |
| ----- | ----- | ----- | ----- |
| 创建新工作区 | 不是必需 | 所有者或参与者 | 不适用（在创建后成为所有者或继承更高作用域角色） |
| 请求订阅级别 Amlcompute 配额或设置工作区级别配额 | 所有者、参与者或自定义角色 </br>允许 `/locations/updateQuotas/action`</br> （在订阅范围内） | 未授权 | 未授权 |
| 新建计算群集 | 不是必需 | 不是必需 | 所有者、参与者或自定义角色允许：`/workspaces/computes/write` |
| 新建计算实例 | 不是必需 | 不是必需 | 所有者、参与者或自定义角色允许：`/workspaces/computes/write` |
| 提交任何类型的运行 | 不是必需 | 不是必需 | 所有者、参与者或自定义角色允许：`"/workspaces/*/read", "/workspaces/environments/write", "/workspaces/experiments/runs/write", "/workspaces/metadata/artifacts/write", "/workspaces/metadata/snapshots/write", "/workspaces/environments/build/action", "/workspaces/experiments/runs/submit/action", "/workspaces/environments/readSecrets/action"` |
| 发布管道终结点 | 不是必需 | 不是必需 | 所有者、参与者或自定义角色允许：`"/workspaces/pipelines/write", "/workspaces/endpoints/pipelines/*", "/workspaces/pipelinedrafts/*", "/workspaces/modules/*"` |
| 在 AKS/ACI 资源上部署已注册的模型 | 不是必需 | 不是必需 | 所有者、参与者或自定义角色允许：`"/workspaces/services/aks/write", "/workspaces/services/aci/write"` |
| 针对已部署的 AKS 终结点进行评分 | 不是必需 | 不是必需 | 所有者、参与者或自定义角色允许：在使用 `"/workspaces/services/aks/score/action", "/workspaces/services/aks/listkeys/action"` `"/workspaces/read"` 令牌) 身份验证时不使用 Azure Active Directory AUTH) 或 (时 ( |
| 使用交互式笔记本访问存储 | 不是必需 | 不是必需 | 所有者、参与者或自定义角色允许：`"/workspaces/computes/read", "/workspaces/notebooks/samples/read", "/workspaces/notebooks/storage/*", "/workspaces/listKeys/action"` |
| 创建新的自定义角色 | 所有者、参与者或自定义角色允许 `Microsoft.Authorization/roleDefinitions/write` | 不是必需 | 所有者、参与者或自定义角色允许：`/workspaces/computes/write` |

> [!TIP]
> 如果第一次尝试创建工作区时遇到失败，请确保角色允许 `Microsoft.MachineLearningServices/register/action`。 可以通过此操作将 Azure 机器学习资源提供程序注册到 Azure 订阅。

### <a name="q-are-we-publishing-azure-built-in-roles-for-the-machine-learning-service"></a>问： 是否会针对机器学习服务发布 Azure 内置角色？

我们目前不会针对机器学习服务发布 [Azure 内置角色](/azure/role-based-access-control/built-in-roles)。 内置角色在发布后无法更新，我们仍在根据客户方案和反馈改进角色定义。 

<a id="customroles"></a>

### <a name="q-are-there-some-custom-role-templates-for-the-most-common-scenarios-in-machine-learning-service"></a>问： 对于机器学习服务中的最常见方案，是否有一些自定义角色模板？

有。下面的一些常见方案具有建议的自定义角色定义，你可以将其作为基础来定义自己的自定义角色：

* __Data Scientist Custom__：允许数据科学家在工作区中执行所有操作，但以下操作**除外**：

    * 创建计算
    * 将模型部署到生产 AKS 群集
    * 在生产环境中部署管道终结点

    `data_scientist_custom_role.json` :
    ```json
    {
        "Name": "Data Scientist Custom",
        "IsCustom": true,
        "Description": "Can run experiment but can't create or delete compute or deploy production endpoints.",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/*/action",
            "Microsoft.MachineLearningServices/workspaces/*/delete",
            "Microsoft.MachineLearningServices/workspaces/*/write"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/*/write",
            "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
            "Microsoft.Authorization/*",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/services/aks/write",
            "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
            "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __Data Scientist Restricted Custom__：一个限制性更强的角色定义，允许的操作中不包含通配符。 它可以在工作区中执行所有操作，但以下操作**除外**：

    * 创建计算
    * 将模型部署到生产 AKS 群集
    * 在生产环境中部署管道终结点

    `data_scientist_restricted_custom_role.json` :
    ```json
    {
        "Name": "Data Scientist Restricted Custom",
        "IsCustom": true,
        "Description": "Can run experiment but can't create or delete compute or deploy production endpoints",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/computes/start/action",
            "Microsoft.MachineLearningServices/workspaces/computes/stop/action",
            "Microsoft.MachineLearningServices/workspaces/computes/restart/action",
            "Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/read",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/write",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/delete",
            "Microsoft.MachineLearningServices/workspaces/notebooks/samples/read",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action",
            "Microsoft.MachineLearningServices/workspaces/pipelinedrafts/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
            "Microsoft.MachineLearningServices/workspaces/environments/write",
            "Microsoft.MachineLearningServices/workspaces/models/write",
            "Microsoft.MachineLearningServices/workspaces/modules/write",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/write", 
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/delete",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/write",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listNodes/action",
            "Microsoft.MachineLearningServices/workspaces/environments/build/action"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/computes/write",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/delete",
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.Authorization/*",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/preview/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/preview/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/schema/read",    
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/schema/read",
            "Microsoft.MachineLearningServices/workspaces/datastores/write",
            "Microsoft.MachineLearningServices/workspaces/datastores/delete"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __MLOps Custom__：允许将角色分配给服务主体，并使用该角色自动执行 MLOps 管道。 例如，若要针对已发布的管道提交运行，可使用以下代码：

    `mlops_custom_role.json` :
    ```json
    {
        "Name": "MLOps Custom",
        "IsCustom": true,
        "Description": "Can run pipelines against a published pipeline endpoint",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/read",
            "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/read",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/read",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/read",
            "Microsoft.MachineLearningServices/workspaces/environments/read",    
            "Microsoft.MachineLearningServices/workspaces/metadata/secrets/read",
            "Microsoft.MachineLearningServices/workspaces/modules/read",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/read",
            "Microsoft.MachineLearningServices/workspaces/datastores/read",
            "Microsoft.MachineLearningServices/workspaces/environments/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
            "Microsoft.MachineLearningServices/workspaces/environments/build/action",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/computes/write",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/delete",
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.Authorization/*"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __Workspace Admin__：允许在工作区范围中执行所有操作，但以下操作**除外**：

    * 创建一个新工作区
    * 分配订阅或工作区级别配额

    工作区管理员也无法创建新的角色， 而只能在其工作区的作用域内分配现有内置角色或自定义角色：

    `workspace_admin_custom_role.json` :
    ```json
    {
        "Name": "Workspace Admin Custom",
        "IsCustom": true,
        "Description": "Can perform all operations except quota management and upgrades",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/*/action",
            "Microsoft.MachineLearningServices/workspaces/*/write",
            "Microsoft.MachineLearningServices/workspaces/*/delete",
            "Microsoft.Authorization/roleAssignments/*"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/write"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

<a name="labeler"></a>
* __Labeler Custom__：允许你定义一个只能在作用域内标记数据的角色：

    `labeler_custom_role.json` :
    ```json
    {
        "Name": "Labeler Custom",
        "IsCustom": true,
        "Description": "Can label data for Labeling",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/read",
            "Microsoft.MachineLearningServices/workspaces/labeling/projects/read",
            "Microsoft.MachineLearningServices/workspaces/labeling/labels/write"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/labeling/projects/summary/read"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>问： 如何列出我的订阅中的所有自定义角色？

在 Azure CLI 中运行以下命令。

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-operations-supported-by-the-machine-learning-service"></a>问： 如何查找机器学习服务支持的操作？

在 Azure CLI 中运行以下命令。

```azurecli-interactive
az provider operation show –n Microsoft.MachineLearningServices
```

还可以在[资源提供程序操作](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)的列表中找到它们。


### <a name="q-what-are-some-common-gotchas-when-using-azure-rbac"></a>问： 使用 Azure RBAC 时，有哪些常见注意事项？

使用 Azure 基于角色的访问控制 (Azure RBAC) 时，请注意以下几点：

- 在 Azure 中创建资源时，例如创建工作区时，你不会直接成为工作区的所有者。 你的角色继承自你在该订阅中获得相应授权的最高作用域角色。 例如，如果你是网络管理员，有权创建机器学习工作区，则会为你分配该工作区的网络管理员角色，而不是所有者角色。
- 针对同一 Azure Active Directory 用户的两个角色分配具有冲突的 Actions/NotActions 部分时，如果操作在某个角色的 NotActions 中列出，但也在另一个角色中作为 Actions 列出，则此类操作可能不会生效。 若要详细了解 Azure 如何分析角色分配，请参阅 [Azure RBAC 如何确定用户是否有权访问资源](/azure/role-based-access-control/overview#how-azure-rbac-determines-if-a-user-has-access-to-a-resource)
- 若要在 VNet 中部署计算资源，需要显式拥有以下操作的权限：
    - “Microsoft.Network/virtualNetworks/join/action”（在 VNet 资源上）。
    - “Microsoft.Network/virtualNetworks/subnet/join/action”（在子网资源上）。
    
    若要详细了解如何将 RBAC 与网络配合使用，请参阅[网络内置角色](/azure/role-based-access-control/built-in-roles#networking)。

- 新的角色分配有时可能需要长达 1 小时才能生效，覆盖整个堆栈的缓存权限。

### <a name="q-what-permissions-do-i-need-to-use-a-user-assigned-managed-identity-with-my-amlcompute-clusters"></a>问： 我需要具有哪些权限才能将用户分配的托管标识用于我的 Amlcompute 群集？

若要在 Amlcompute 群集上分配用户分配的标识，必须有写入权限才能创建计算，并拥有 [托管标识操作员角色](/azure/role-based-access-control/built-in-roles#managed-identity-operator)。 若要详细了解如何将 RBAC 与托管标识配合使用，请阅读[如何管理用户分配的标识](/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal)


### <a name="q-do-we-support-role-based-access-control-on-the-studio-portal"></a>问： 工作室门户上是否支持基于角色的访问控制？

Azure 机器学习工作室支持 Azure 基于角色的访问控制 (Azure RBAC)。 

> [!IMPORTANT]
> 在你为工作区中的数据科学家分配了具有特定权限的自定义角色后，系统会自动对用户隐藏相应的操作（例如添加一个计算按钮）。 隐藏这些项可防止用户在使用它们时看到控件返回来自服务的“未经授权的访问”通知，从而避免混乱。

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>问： 如何查找我的订阅中某个角色的角色定义？

在 Azure CLI 中运行以下命令。 `<role-name>` 的格式应与上述命令返回的格式相同。

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>问： 如何更新角色定义？

在 Azure CLI 中运行以下命令。

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

你需要对新角色定义的整个作用域具有权限。 例如，如果此新角色的作用域跨三个订阅，则你需要对所有三个订阅都具有权限。 

> [!NOTE]
> 角色更新可能需要花费 15 分钟到一小时才能应用于该作用域中的所有角色分配。


### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>问： 在工作区中执行配额操作需要哪些权限？ 

你需要订阅级别权限才能在工作区中执行任何与配额相关的操作。 这意味着，只有当你在订阅作用域具有写入权限时，才能为你的托管计算资源设置订阅级配额或工作区级配额。 


## <a name="next-steps"></a>后续步骤

- [企业安全性概述](concept-enterprise-security.md)
- [虚拟网络隔离和隐私概述](how-to-network-security-overview.md)
- [教程：训练模型](tutorial-train-models-with-aml.md)
- [资源提供程序操作](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)
