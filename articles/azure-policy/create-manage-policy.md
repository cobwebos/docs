---
title: 使用 Azure 策略来创建和管理策略以强制实施组织符合性
description: 使用 Azure 策略强制执行标准、满足法规遵从性、审核需求、控制成本、维护安全和性能的一致性，并实施企业范围的设计原则。
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/07/2018
ms.topic: tutorial
ms.service: azure-policy
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 1d13c5c0099b8e4c9ed83221edf6a2ee92fc3c8e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194863"
---
# <a name="create-and-manage-policies-to-enforce-compliance"></a>创建和管理策略以强制实施符合性

了解如何在 Azure 中创建和管理策略对于保持与公司标准和服务级别协议的符合性来说非常重要。 本教程介绍如何使用 Azure 策略来执行某些与在组织中创建、分配和管理策略相关的常见任务，例如：

> [!div class="checklist"]
> - 分配策略，对将来创建的资源强制执行条件
> - 创建并分配计划定义，跟踪多个资源的符合性
> - 解决不符合或遭拒绝的资源
> - 在组织中实施新策略

若要分配一个策略用于识别现有资源的当前符合性状态，请参阅快速入门文章。 如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/)。

## <a name="assign-a-policy"></a>分配策略

使用 Azure 策略强制实施符合性的第一步是分配策略定义。 策略定义用于定义实施策略的条件，以及要达到的效果。 在本示例中，我们将分配名为“需要 SQL Server 版本 12.0”的内置策略定义，强制执行“所有 SQL Server 数据库都必须是 v12.0 才视为符合”的条件。

1. 在 Azure 门户中单击“所有服务”，然后搜索并选择“策略”，启动 Azure 策略服务。

   ![搜索策略](media/create-manage-policy/search-policy.png)

2. 选择“Azure 策略”页左侧的“分配”。 分配即为在特定范围内分配策略以供执行。
3. 在“策略 - 分配”页的顶部选择“分配策略”。

   ![分配策略定义](media/create-manage-policy/select-assign-policy.png)

4. 在“分配策略”页上，单击省略号并选择订阅（必需）和资源组（可选），然后选择“范围”。 范围用于确定对其强制执行策略分配的资源或资源组。  然后在“范围”页的底部单击“选择”。

   此示例使用 **Contoso 订阅**。 你的订阅将有所不同。

5. 若要排除一个或多个资源组（如果只是以某个订阅作为范围）或者某个资源组中的特定资源（使用任一范围），可以配置策略分配中的“排除项”。 暂时将此字段留空。

6. 选择“策略定义”旁边的省略号打开可用定义的列表。 可以使用“内置”来筛选策略定义的**类型**，以查看所有相关策略定义及其说明。

7. 选择“需要 SQL Server 版本 12.0”。 如果不能立即找到它，请在搜索框中键入“需要 SQL Server”，然后按 ENTER 或者单击搜索框的外部。 找到并选择策略定义后，单击“可用定义”页底部的“选择”。

   ![查找策略](media/create-manage-policy/select-available-definition.png)

8. “分配名称”中自动填充了所选的策略名称，但可以更改它。 对于本示例，请保留“需要 SQL Server 版本 12.0”。 还可根据需要添加“说明”。 该说明提供有关此策略分配的详细信息。

9. 单击“分配”。

## <a name="implement-a-new-custom-policy"></a>实施新的自定义策略

分配内置的策略定义后，可以使用 Azure 策略执行其他操作。 接下来创建一个新的自定义策略，确保在环境中创建的 VM 不能处于 G 系列，从而节省成本。 这样，当组织中的用户每次尝试创建 G 系列的 VM 时，请求将被拒绝。

1. 选择“Azure 策略”页左侧“创作”下的“定义”。

   ![创作下的定义](media/create-manage-policy/definition-under-authoring.png)

2. 选择页面顶部的“+ 策略定义”。 此时会打开“策略定义”页。
3. 输入以下内容：

   - 策略定义保存到的管理组或订阅。 使用“定义位置”旁边的省略号进行选择。

     > [!NOTE]
     > 若要将此策略定义应用到多个订阅，则位置必须是策略要分配到的订阅所在的管理组。 对于计划定义，也需要确保这一点。

   - 策略定义的名称 - 需要 VM SKU 小于 G 系列
   - 想通过策略定义实现的操作的说明 - 此策略定义强制此范围中创建的所有 VM 具有的 SKU 都小于 G 系列，以减少成本。
   - 从现有的选项中选择，或者为此策略定义创建新的类别。
   - 复制以下 json 代码并根据需要进行更新：
      - 策略参数。
      - 策略规则/条件，此示例中为 - VM SKU 大小等于 G 系列
      - 策略效果，此示例中为“拒绝”。

    json 应如下所示。 将修改后的代码粘贴到 Azure 门户。

    ```json
    {
        "policyRule": {
            "if": {
                "allOf": [{
                        "field": "type",
                        "equals": "Microsoft.Compute/virtualMachines"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/sku.name",
                        "like": "Standard_G*"
                    }
                ]
            },
            "then": {
                "effect": "deny"
            }
        }
    }
    ```

    策略规则中字段属性的值必须是下列其中一项：“名称”、“类型”、“位置”、“标记”或别名。 例如，别名为 `"Microsoft.Compute/VirtualMachines/Size"`。

    若要查看其他 Azure 策略示例，请参阅 [Azure 策略的模板](json-samples.md)。

4. 选择“保存”。

## <a name="create-a-policy-definition-with-rest-api"></a>使用 REST API 创建策略定义

可以通过用于策略定义的 REST API 来创建策略。 借助 REST API，可创建和删除策略定义，以及获取现有定义的相关信息。
若要创建策略定义，请使用以下示例：

```http-interactive
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

包括类似于下方示例的请求正文：

```json
{
    "properties": {
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

## <a name="create-a-policy-definition-with-powershell"></a>使用 PowerShell 创建策略定义

在继续完成 PowerShell 示例之前，请确保已安装最新版本的 Azure PowerShell。 版本 3.6.0 中添加了策略参数。 如果使用较早版本，示例将返回一个错误，指示“找不到参数”。

可使用 `New-AzureRmPolicyDefinition` cmdlet 创建策略定义。

要在文件中创建策略定义，请将路径传递给该文件。 对于外部文件，请使用以下示例：

```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition `
    -Name 'denyCoolTiering' `
    -DisplayName 'Deny cool access tiering for storage' `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

对于本地文件，请使用以下示例：

```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition `
    -Name 'denyCoolTiering' `
    -Description 'Deny cool access tiering for storage' `
    -Policy 'c:\policies\coolAccessTier.json'
```

要使用内联规则创建策略定义，请使用以下示例：

```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition -Name 'denyCoolTiering' -Description 'Deny cool access tiering for storage' -Policy '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "not": {
                    "field": "Microsoft.Storage/storageAccounts/accessTier",
                    "equals": "cool"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

输出存储在 `$definition` 对象中，这会在策略分配过程中使用。
以下示例创建包含参数的策略定义：

```azurepowershell-interactive
$policy = '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of locations that can be specified when deploying storage accounts.",
            "strongType": "location",
            "displayName": "Allowed locations"
        }
    }
}'

$definition = New-AzureRmPolicyDefinition -Name 'storageLocations' -Description 'Policy to specify locations for storage accounts.' -Policy $policy -Parameter $parameters
```

### <a name="view-policy-definitions-with-powershell"></a>使用 PowerShell 查看策略定义

若要查看订阅中的所有策略定义，请运行以下命令：

```azurepowershell-interactive
Get-AzureRmPolicyDefinition
```

此命令可返回所有可用的策略定义，包括内置策略。 返回的每个策略的格式如下：

```
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

## <a name="create-a-policy-definition-with-azure-cli"></a>使用 Azure CLI 创建策略定义

可以将 Azure CLI 与策略定义命令结合使用来创建策略定义。
要使用内联规则创建策略定义，请使用以下示例：

```azurecli-interactive
az policy definition create --name 'denyCoolTiering' --description 'Deny cool access tiering for storage' --rules '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "not": {
                    "field": "Microsoft.Storage/storageAccounts/accessTier",
                    "equals": "cool"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

### <a name="view-policy-definitions-with-azure-cli"></a>使用 Azure CLI 查看策略定义

若要查看订阅中的所有策略定义，请运行以下命令：

```azurecli-interactive
az policy definition list
```

此命令可返回所有可用的策略定义，包括内置策略。 返回的每个策略的格式如下：

```json
{
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",
    "displayName": "Allowed locations",
    "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "policyRule": {
        "if": {
            "not": {
                "field": "location",
                "in": "[parameters('listOfAllowedLocations')]"
            }
        },
        "then": {
            "effect": "Deny"
        }
    },
    "policyType": "BuiltIn"
}
```

## <a name="create-and-assign-an-initiative-definition"></a>创建并分配计划定义

通过计划定义，可以组合某些策略定义以实现首要目标。 创建计划定义，确保定义范围内的资源符合构成计划定义的策略定义。  有关计划定义的详细信息，请参阅 [Azure 策略概述](azure-policy-introduction.md)。

### <a name="create-an-initiative-definition"></a>创建计划定义

1. 选择“Azure 策略”页左侧“创作”下的“定义”。

   ![选择定义](media/create-manage-policy/select-definitions.png)

2. 选择页面顶部的“+ 计划定义”打开“计划定义”页。

   ![计划定义](media/create-manage-policy/initiative-definition.png)

3. 使用“定义位置”旁边的省略号选择用于存储定义的订阅。

4. 输入计划的“名称”和“说明”。

   本示例将确保资源符合有关安全性的策略定义。 因此，计划的名称为“保证安全”，说明为：“创建此计划的目的是处理所有与保护资源相关的策略定义”。

5. 对于“类别”，请从现有的选项中选择，或者创建新类别。

6. 浏览“可用定义”的列表（在“计划定义”页的右半部分），然后选择要添加到此计划的策略定义。 对于“保证安全”计划，请单击策略定义信息旁边的 **+**，或单击策略定义行并选择详细信息页中的“+ 添加”选项，来添加以下内置策略定义：
   - 需要 SQL Server 版本 12.0
   - [Preview]: Monitor unprotected web applications in Security Center.
   - [Preview]: Monitor permissive network across in Security Center.
   - [Preview]: Monitor possible app Whitelisting in Security Center.
   - [Preview]: Monitor unencrypted VM Disks in Security Center.

   从列表中选择策略定义后，该策略定义会添加到“策略和参数”的下面。

   ![计划定义](media/create-manage-policy/initiative-definition-2.png)

7. 单击“ **保存**”。

### <a name="assign-an-initiative-definition"></a>分配计划定义

1. 选择“Azure 策略”页左侧“创作”下的“定义”。
2. 找到前面创建的“保证安全”计划定义并选择它。
3. 选择页面顶部的“分配”，打开“保证安全: 分配计划”页。

   ![分配定义](media/create-manage-policy/assign-definition.png)

   或者，可以右键单击选定的行，或者单击上下文菜单行末尾处的省略号。  然后选择“分配”。

   ![右键单击某个行](media/create-manage-policy/select-right-click.png)

4. 输入以下示例信息，填充“保证安全: 分配计划”页。 可以使用自己的信息。

   - 范围：将计划保存到的订阅将是默认值。  可以更改范围，以将计划分配到订阅保存位置中的某个资源组。
   - 排除项：配置上述范围内的任何资源，以防止向其应用计划分配。
   - 计划定义和分配名称：“保证安全”（预先填充了所分配计划的名称）。
   - 说明：此计划分配旨在实施这组策略定义。

5. 单击“分配”。

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>使用“排除”豁免不符合或遭拒绝的资源

继续以上示例，在通过分配策略定义来要求使用 SQL Server 版本 12.0 以后，通过 12.0 以外的版本创建的 SQL Server 将被拒绝。 本部分介绍如何通过创建单个资源组中的排除项，来解决拒绝尝试创建 SQL Server 的问题。 该排除项可防止对该资源实施策略（或计划）。 在以下示例中，允许在单个资源组中使用任何 SQL Server 版本。 可对资源组应用排除，或者将排除范围缩小到单个资源。

可在两个位置查看由于分配的策略或计划而导致受阻的部署：

- 在部署部署针对的资源组中：选择页面左侧的“部署”，然后单击失败部署的“部署名称”。 随后将会列出带有“禁止”状态的被拒绝资源。 若要确定拒绝该资源的策略或计划和分配，请在“部署概述”页上单击“失败。单击此处了解详细信息 ->”。 页面右侧会打开一个窗口，其中显示了错误信息。 “错误详细信息”下显示了相关策略对象的 GUID。

   ![策略分配拒绝的部署](media/create-manage-policy/rg-deployment-denied.png)

- 在“Azure 策略”页上：选择页面左侧的“符合性”，然后单击“需要 SQL Server 版本 12.0”策略。 在打开的页面上，会看到“拒绝”计数已递增。 在“事件”选项卡下，还会看到谁尝试执行了被策略拒绝的部署。

   ![分配策略的符合性概述](media/create-manage-policy/compliance-overview.png)

在本示例中，Trent Baker（Contoso 的高级虚拟化专家之一）执行了所需的工作。 我们需要为他指定一个例外项，但不希望在任何资源组中使用版本 12.0 以外的 SQL Server。 我们创建了新资源组 **SQLServers_Excluded**，现在要将此资源组指定为此策略分配的例外项。

### <a name="update-assignment-with-exclusion"></a>使用排除项更新分配

1. 在“Azure 策略”页左侧的“创作”下选择“分配”。
2. 浏览所有策略分配并打开“需要 SQL Server 版本 12.0”分配。
3. 设置“排除项”：单击省略号并选择要排除的资源组（在本示例中为 *SQLServers_Excluded*）。

   ![请求排除](media/create-manage-policy/request-exclusion.png)

   > [!NOTE]
   > 根据策略及其效果，也可以将排除项指定为分配范围内某个资源组中的特定资源。 由于本教程使用了“拒绝”效果，对已存在的特定资源设置排除项没有意义。

4. 单击“选择”，并单击“保存”。

在本部分，我们通过对单个资源组创建排除项，解决了拒绝尝试创建受禁 SQL Server 版本的问题。

## <a name="clean-up-resources"></a>清理资源

如果今后不再使用本教程中的资源，请使用以下步骤删除前面创建的所有分配或定义：

1. 在“Azure 策略”页左侧的“创作”下选择“定义”（如果尝试删除分配，则选择“分配”）。
2. 搜索要删除的新计划或策略定义（或分配）。
3. 右键单击定义（或分配）对应的行或选择其末尾的省略号，然后选择“删除定义”（或“删除分配”）。

## <a name="next-steps"></a>后续步骤

在本教程中，你已成功完成以下操作：

> [!div class="checklist"]
> - 分配策略，对将来创建的资源强制执行条件
> - 创建并分配计划定义，跟踪多个资源的符合性
> - 解决不符合或遭拒绝的资源
> - 在组织中实施新策略

若要了解有关策略定义结构的详细信息，请查看以下文章：

> [!div class="nextstepaction"]
> [Azure 策略定义结构](policy-definition.md)