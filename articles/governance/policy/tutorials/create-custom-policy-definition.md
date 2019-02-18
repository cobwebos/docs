---
title: 创建自定义策略定义
description: 创建 Azure Policy 的自定义策略定义以强制实施自定义业务规则。
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/12/2019
ms.topic: tutorial
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: ddda2a8bf1fab4e4c48c647237617d8f705f0561
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112081"
---
# <a name="create-a-custom-policy-definition"></a>创建自定义策略定义

客户可以通过自定义策略定义来定义自己的 Azure 使用规则。 这些规则通常强制实施：

- 安全做法
- 成本管理
- 组织特定的规则（例如命名或位置）

无论创建自定义策略的业务推动因素是什么，定义新自定义策略的步骤都是相同的。

创建自定义策略之前，请查看[策略示例](../samples/index.md)，以确定是否存在符合需求的策略。

遵循以下步骤创建自定义策略：

> [!div class="checklist"]
> - 确定业务要求
> - 将每个要求映射到 Azure 资源属性
> - 将属性映射到别名
> - 确定要使用的效果
> - 撰写策略定义

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="identify-requirements"></a>确定要求

在创建策略定义之前，必须了解策略的意图。 本教程将使用常见的企业安全要求作为目标来演示相关步骤：

- 必须在每个存储帐户中启用 HTTPS
- 必须在每个存储帐户中启用 HTTP

要求中应该明确规定“正常”和“不正常”资源状态。

尽管我们已定义资源的预期状态，但尚未定义如何处理不合规的资源。 Policy 支持多种[效果](../concepts/effects.md)。 本教程将业务要求定义为阻止创建不符合业务规则的资源。 为了满足此目标，我们将使用“[拒绝](../concepts/effects.md#deny)”效果。 我们还需要使用相应的选项来暂停特定分配的策略。 因此，我们将使用“[已禁用](../concepts/effects.md#disabled)”效果，并将其设为策略定义中的[参数](../concepts/definition-structure.md#parameters)。

## <a name="determine-resource-properties"></a>确定资源属性

根据业务要求，要使用 Policy 审核的 Azure 资源是存储帐户。
但是，我们不知道要在策略定义中使用的属性。 Policy 将会评估资源的 JSON 表示形式，因此，我们需要了解可在该资源中使用的属性。

可通过多种方式确定 Azure 资源的属性。 本教程将介绍其中的每种方式：

- 资源管理器模板
  - 导出现有资源
  - 创建体验
  - 快速入门模板 (GitHub)
  - 模板参考文档
- Azure 资源浏览器

### <a name="resource-manager-templates"></a>资源管理器模板

可通过多种方式查找包含所要管理的属性的[资源管理器模板](../../../azure-resource-manager/resource-manager-tutorial-create-encrypted-storage-accounts.md)。

#### <a name="existing-resource-in-the-portal"></a>门户中的现有资源

查找属性的最简单方法是查找相同类型的现有资源。 已使用所要强制实施的设置配置的资源也会提供用于比较的值。
在 Azure 门户中，找到该特定资源的“自动化脚本”页（在“设置”下）。

![“自动化脚本”页](../media/create-custom-policy-definition/automation-script.png)

针对存储帐户执行此操作会显示以下示例所示的模板：

```json
...
"resources": [{
    "comments": "Generalized from resource: '/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount'.",
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "name": "[parameters('storageAccounts_mystorageaccount_name')]",
    "apiVersion": "2018-07-01",
    "location": "westus",
    "tags": {
        "ms-resource-usage": "azure-cloud-shell"
    },
    "scale": null,
    "properties": {
        "networkAcls": {
            "bypass": "AzureServices",
            "virtualNetworkRules": [],
            "ipRules": [],
            "defaultAction": "Allow"
        },
        "supportsHttpsTrafficOnly": false,
        "encryption": {
            "services": {
                "file": {
                    "enabled": true
                },
                "blob": {
                    "enabled": true
                }
            },
            "keySource": "Microsoft.Storage"
        }
    },
    "dependsOn": []
}]
...
```

“属性”下面提供了名为 **supportsHttpsTrafficOnly**、设置为 **false** 的值。 此属性似乎是我们所要查找的属性。 此外，该资源的**类型**为 **Microsoft.Storage/storageAccounts**。 该类型告知我们，要将策略限定于此类型的资源。

#### <a name="create-a-resource-in-the-portal"></a>在门户中创建资源

另一种方式是通过门户中的资源创建体验。 通过门户创建存储帐户时，“高级”选项卡下会提供“需要安全传输”选项。
此属性具有“已禁用”和“已启用”选项。 信息图标包含附加文本，确认此选项可能是我们所需的属性。 但是，门户不会在此屏幕上显示属性名称。

在“查看 + 创建”选项卡上，页面底部提供了“下载自动化模板”链接。 选择该链接会打开用于创建所配置的资源的模板。 在这种情况下，我们会看到两段重要信息：

```json
...
"supportsHttpsTrafficOnly": {
    "type": "bool"
}
...
"properties": {
    "accessTier": "[parameters('accessTier')]",
    "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
}
...
```

此信息告知属性类型，同时确认 **supportsHttpsTrafficOnly** 是我们正在查找的属性。

#### <a name="quickstart-templates-on-github"></a>GitHub 上的快速入门模板

GitHub 上的 [Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates)包含数百个针对不同资源生成的资源管理器模板。 使用这些模板能够十分方便地查找所需的资源属性。 某些属性似乎是我们所要查找的属性，但它们控制了其他某个对象。

#### <a name="resource-reference-docs"></a>资源参考文档

若要验证 **supportsHttpsTrafficOnly** 是否为正确的属性，请在存储提供商网站上查看[存储帐户资源](/azure/templates/microsoft.storage/2018-07-01/storageaccounts)的资源管理器模板参考。
属性对象包含有效参数的列表。 选择 [StorageAccountPropertiesCreateParameters-object](/azure/templates/microsoft.storage/2018-07-01/storageaccounts#storageaccountpropertiescreateparameters-object) 链接会显示可接受的属性表。 若要满足业务要求，**supportsHttpsTrafficOnly** 必须存在，并且说明必须与所要查找的内容相匹配。

### <a name="azure-resource-explorer"></a>Azure 资源浏览器

浏览 Azure 资源的另一种方式是使用 [Azure 资源浏览器](https://resources.azure.com)（预览版）。 此工具使用订阅的上下文，因此，你需要在网站中使用 Azure 凭据进行身份验证。 完成身份验证后，可按提供程序、订阅、资源组和资源进行浏览。

找到存储帐户资源并查看属性。 在此处还可以查看 **supportsHttpsTrafficOnly** 属性。 选择“文档”选项卡，可以看到，属性说明与我们在前面的参考文档中找到的信息相匹配。

## <a name="find-the-property-alias"></a>查找属性别名

我们已识别资源属性，但需要将该属性映射到[别名](../concepts/definition-structure.md#aliases)。

可通过多种方式确定 Azure 资源的别名。 本教程将介绍其中的每种方式：

- Azure CLI
- Azure PowerShell
- Azure Resource Graph

### <a name="azure-cli"></a>Azure CLI

在 Azure CLI 中，`az provider` 命令组用于搜索资源别名。 我们将根据前面获取的有关 Azure 资源的详细信息来筛选 **Microsoft.Storage** 命名空间。

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for type Microsoft.Storage
az provider show --namespace Microsoft.Storage --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

在结果中，查看名为 **supportsHttpsTrafficOnly** 的存储帐户支持的别名。 存在此别名意味着可以编写策略来强制实施我们的业务要求！

### <a name="azure-powershell"></a>Azure PowerShell

在 Azure PowerShell 中， `Get-AzPolicyAlias` cmdlet 用于搜索资源别名。
我们将根据前面获取的有关 Azure 资源的详细信息来筛选 **Microsoft.Storage** 命名空间。

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.Storage
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.Storage').Aliases
```

与在 Azure CLI 中一样，结果会显示名为 **supportsHttpsTrafficOnly** 的存储帐户支持的别名。

### <a name="azure-resource-graph"></a>Azure Resource Graph

[Azure Resource Graph](../../resource-graph/overview.md) 是一个新的预览版服务。 它是用于查找 Azure 资源属性的另一种方法。 下面是使用 Resource Graph 查找单个存储帐户的示例查询：

```Query
where type=~'microsoft.storage/storageaccounts' | limit 1
```

```azurecli-interactive
az graph query -q "where type=~'microsoft.storage/storageaccounts' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'microsoft.storage/storageaccounts' | limit 1"
```

结果类似于在资源管理器模板中和通过 Azure 资源浏览器查找后获得的结果。 但是，Azure Resource Graph 结果还包含[别名](../concepts/definition-structure.md#aliases)详细信息。 下面是存储帐户别名的示例输出：

```json
"aliases": {
    "Microsoft.Storage/storageAccounts/accessTier": null,
    "Microsoft.Storage/storageAccounts/accountType": "Standard_LRS",
    "Microsoft.Storage/storageAccounts/enableBlobEncryption": true,
    "Microsoft.Storage/storageAccounts/enableFileEncryption": true,
    "Microsoft.Storage/storageAccounts/encryption": {
        "keySource": "Microsoft.Storage",
        "services": {
            "blob": {
                "enabled": true,
                "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
            },
            "file": {
                "enabled": true,
                "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
            }
        }
    },
    "Microsoft.Storage/storageAccounts/encryption.keySource": "Microsoft.Storage",
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyname": null,
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyvaulturi": null,
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyversion": null,
    "Microsoft.Storage/storageAccounts/encryption.services": {
        "blob": {
            "enabled": true,
            "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
        },
        "file": {
            "enabled": true,
            "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
        }
    },
    "Microsoft.Storage/storageAccounts/encryption.services.blob": {
        "enabled": true,
        "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
    },
    "Microsoft.Storage/storageAccounts/encryption.services.blob.enabled": true,
    "Microsoft.Storage/storageAccounts/encryption.services.file": {
        "enabled": true,
        "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
    },
    "Microsoft.Storage/storageAccounts/encryption.services.file.enabled": true,
    "Microsoft.Storage/storageAccounts/networkAcls": {
        "bypass": "AzureServices",
        "defaultAction": "Allow",
        "ipRules": [],
        "virtualNetworkRules": []
    },
    "Microsoft.Storage/storageAccounts/networkAcls.bypass": "AzureServices",
    "Microsoft.Storage/storageAccounts/networkAcls.defaultAction": "Allow",
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*]": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].action": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].id": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].state": [],
    "Microsoft.Storage/storageAccounts/primaryEndpoints": {
        "blob": "https://mystorageaccount.blob.core.windows.net/",
        "file": "https://mystorageaccount.file.core.windows.net/",
        "queue": "https://mystorageaccount.queue.core.windows.net/",
        "table": "https://mystorageaccount.table.core.windows.net/"
    },
    "Microsoft.Storage/storageAccounts/primaryEndpoints.blob": "https://mystorageaccount.blob.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.file": "https://mystorageaccount.file.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.queue": "https://mystorageaccount.queue.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.table": "https://mystorageaccount.table.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.web": null,
    "Microsoft.Storage/storageAccounts/primaryLocation": "eastus2",
    "Microsoft.Storage/storageAccounts/provisioningState": "Succeeded",
    "Microsoft.Storage/storageAccounts/sku.name": "Standard_LRS",
    "Microsoft.Storage/storageAccounts/sku.tier": "Standard",
    "Microsoft.Storage/storageAccounts/statusOfPrimary": "available",
    "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly": false
}
```

可以通过 [Cloud Shell](https://shell.azure.com) 使用 Azure Resource Graph（预览版），快速轻松地浏览资源的属性。

## <a name="determine-the-effect-to-use"></a>确定要使用的效果

确定如何处理不合规的资源几乎与确定最初要评估的项一样重要。 针对不合规资源做出的每种可能响应称为[效果](../concepts/effects.md)。 效果控制是否要记录、阻止不合规的资源、在其中追加数据，或者将一个部署关联到其中，使该资源恢复合规状态。

在本示例中，“拒绝”是所需的效果，因为我们不希望在 Azure 环境中创建不合规的资源。 “审核”是策略效果的第一个合理选项，它确定策略在设置为“拒绝”之前的影响。 使更改每个分配的效果变得更轻松的方法之一是将效果参数化。 有关详细信息，请参阅下面的[参数](#parameters)。

## <a name="compose-the-definition"></a>撰写定义

我们现已获得属性详细信息和打算管理的别名。 接下来，我们将撰写策略规则本身。 如果你不熟悉策略语言，请参考[策略定义结构](../concepts/definition-structure.md)了解如何构建策略定义。 以下空白模板显示了策略定义的外观：

```json
{
    "properties": {
        "displayName": "<displayName>",
        "description": "<description>",
        "mode": "<mode>",
        "parameters": {
                <parameters>
        },
        "policyRule": {
            "if": {
                <rule>
            },
            "then": {
                "effect": "<effect>"
            }
        }
    }
}
```

### <a name="metadata"></a>元数据

前三个组成部分是策略元数据。 由于我们知道要为哪些对象创建规则，因此可以轻松提供这些组成部分的值。 [Mode](../concepts/definition-structure.md#mode) 主要与标记和资源位置相关。 由于我们不需要将评估范围限制为支持标记的资源，因此将对 **mode** 使用 _all_ 值。

```json
"displayName": "Deny storage accounts not using only HTTPS",
"description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
"mode": "all",
```

### <a name="parameters"></a>parameters

尽管我们未使用参数来更改评估，但确实需要使用一个参数来允许更改**效果**以进行故障排除。 定义 **effectType** 参数，并将其值限制为 **Deny** 和 **Disabled**。 这两个选项与我们的业务要求相符。 完成的参数块如以下示例所示：

```json
"parameters": {
    "effectType": {
        "type": "string",
        "defaultValue": "Deny",
        "allowedValues": [
            "Deny",
            "Disabled"
        ],
        "metadata": {
            "displayName": "Effect",
            "description": "Enable or disable the execution of the policy"
        }
    }
},
```

### <a name="policy-rule"></a>策略规则

撰写[策略规则](../concepts/definition-structure.md#policy-rule)是生成自定义策略定义的最后一步。 我们已指定两条语句用于测试：

- 存储帐户的**类型**是否为 **Microsoft.Storage/storageAccounts**
- 存储帐户的 **supportsHttpsTrafficOnly** 不为 **true**

由于这两条语句都需要为 true，因此将使用 **allOf** [逻辑运算符](../concepts/definition-structure.md#logical-operators)。 将 **effectType** 参数传递给效果，而不是进行静态声明。 完成的规则如以下示例所示：

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        },
        {
            "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
            "notEquals": "true"
        }
    ]
},
"then": {
    "effect": "[parameters('effectType')]"
}
```

### <a name="completed-definition"></a>完成的定义

定义策略的所有三个组成部分后，下面是完成的定义：

```json
{
    "properties": {
        "displayName": "Deny storage accounts not using only HTTPS",
        "description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
        "mode": "all",
        "parameters": {
            "effectType": {
                "type": "string",
                "defaultValue": "Deny",
                "allowedValues": [
                    "Deny",
                    "Disabled"
                ],
                "metadata": {
                    "displayName": "Effect",
                    "description": "Enable or disable the execution of the policy"
                }
            }
        },
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Storage/storageAccounts"
                    },
                    {
                        "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
                        "notEquals": "true"
                    }
                ]
            },
            "then": {
                "effect": "[parameters('effectType')]"
            }
        }
    }
}
```

完成的定义可用于创建新策略。 门户和每个 SDK（Azure CLI、Azure PowerShell 和 REST API）以不同的方式接受定义，因此，请检查各自的命令，以验证用法是否正确。 然后使用参数化的效果将定义分配到相应的资源，以管理存储帐户的安全性。

## <a name="review"></a>审阅

在本教程中，你已成功完成以下任务：

> [!div class="checklist"]
> - 确定了业务要求
> - 将每个要求映射到了 Azure 资源属性
> - 将属性映射到了别名
> - 确定了要使用的效果
> - 撰写了策略定义

## <a name="next-steps"></a>后续步骤

接下来，请使用自定义策略定义来创建并分配策略：

> [!div class="nextstepaction"]
> [创建并分配策略定义](../how-to/programmatically-create.md#create-and-assign-a-policy-definition)