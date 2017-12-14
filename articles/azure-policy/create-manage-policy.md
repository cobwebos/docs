---
title: "使用 Azure 策略来创建和管理策略以强制实施组织符合性 | Microsoft Docs"
description: "使用 Azure 策略强制执行标准、满足法规遵从性、审核需求、控制成本、维护安全和性能的一致性，并实施企业范围的设计原则。"
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 12/06/2017
ms.topic: tutorial
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: ba425e938f81ffb37a2c8bc2a764a4db074e9106
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2017
---
# <a name="create-and-manage-policies-to-enforce-compliance"></a>创建和管理策略以强制实施符合性

了解如何在 Azure 中创建和管理策略对于保持与公司标准和服务级别协议的符合性来说非常重要。 本教程介绍如何使用 Azure 策略来执行某些与在组织中创建、分配和管理策略相关的常见任务，例如：

> [!div class="checklist"]
> * 分配策略，对将来创建的资源强制执行条件
> * 创建并分配计划定义，跟踪多个资源的符合性
> * 解决不符合或遭拒绝的资源
> * 在组织中实施新策略

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="assign-a-policy"></a>分配策略

使用 Azure 策略强制实施符合性的第一步是分配策略定义。 策略定义用于定义实施策略的条件，以及要执行的操作。 在此示例中，分配名为“需要 SQL Server 版本 12.0”的内置策略定义，强制执行“所有 SQL Server 数据库都必须是 v12.0 才视为符合”的条件。

1. 通过搜索并选择左侧窗格中的“策略”，启动 Azure 门户中的 Azure 策略服务。

   ![搜索策略](media/assign-policy-definition/search-policy.png)

2. 选择 Azure 策略页左侧窗格中的“分配”。 分配即为在特定范围内分配策略以供执行。
3. 选择“分配”窗格顶部的“分配策略”。

   ![分配策略定义](media/create-manage-policy/select-assign-policy.png)

4. 在“分配策略”页上，单击“策略”字段旁边的![策略定义按钮](media/assign-policy-definition/definitions-button.png)，打开可用定义的列表。

   ![打开可用策略定义](media/create-manage-policy/open-policy-definitions.png)

5. 选择“需要 SQL Server 版本 12.0”。

   ![查找策略](media/create-manage-policy/select-available-definition.png)

6. 提供策略分配的显示名称。 在此示例中，我们使用“需要 SQL Server 版本 12.0”。 还可根据需要添加“说明”。 该说明详细介绍了此策略分配如何确保此环境中创建的所有 SQL Server 版本均为 12.0。
7. 将定价层更改为“标准”，确保策略应用于现有资源。

   Azure 策略内有两个定价层 - 免费和标准。 使用免费层，只能对将来资源强制实施策略；使用标准层，还可对现有资源强制实施策略，更好地了解符合性状态。 由于我们处于受限预览版，尚未发布定价模型，因此你不会收到选择标准的帐单。 若要了解有关定价的详细信息，请参阅 [Azure 策略定价](https://acom-milestone-ignite.azurewebsites.net/pricing/details/azure-policy/)。

8. 选择“范围”- 之前注册的订阅（或资源组）。 范围用于确定对其强制执行策略分配的资源或资源组。 它可以从订阅延伸至资源组。

   此示例使用此订阅 -“Azure 分析容量开发”。 你的订阅将有所不同。

10. 选择“分配”。

## <a name="implement-a-new-custom-policy"></a>实施新的自定义策略

我们现已分配策略定义，接下来将创建新策略，通过确保环境中创建的 VM 不属于 G 系列来节约成本。 这样，当组织中用户每次尝试创建 G 系列的 VM 时，请求将被拒绝。

1. 选择左侧窗格中“创作”下的“定义”。

   ![创作下的定义](media/create-manage-policy/definition-under-authoring.png)

2. 选择“+ 策略定义”。
3. 输入以下内容：

   - 策略定义的名称 - 需要 VM SKU 小于 G 系列
   - 想通过策略定义实现的操作的说明 - 此策略定义强制此范围中创建的所有 VM 具有的 SKU 都小于 G 系列，以减少成本。
   - 将执行策略定义的订阅 - 本示例中，策略定义位于“顾问分析容量开发”中。 你的订阅列表将有所不同。
   - 使用以下内容编写 json 代码：
      - 策略参数。
      - 策略规则/条件，此示例中为 - VM SKU 大小等于 G 系列
      - 策略效果，此示例中为“拒绝”。

    json 应如下所示

    ```json
{
    "policyRule": {
      "if": {
        "allOf": [
          {
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

    若要查看 json 代码的示例，请参阅 [Azure 策略的模板](json-samples.md)一文。

4. 选择“保存”。

## <a name="create-a-policy-definition-with-rest-api"></a>使用 REST API 创建策略定义

可以通过用于策略定义的 REST API 来创建策略。 借助 REST API，可创建和删除策略定义，以及获取现有定义的相关信息。
若要创建策略定义，请使用以下示例：

```
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

```
包括类似于下方示例的请求正文：

```
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

```
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -DisplayName "Deny cool access tiering for storage" `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

对于本地文件，请使用以下示例：

```
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -Description "Deny cool access tiering for storage" `
    -Policy "c:\policies\coolAccessTier.json"
```

要使用内联规则创建策略定义，请使用以下示例：

```
$definition = New-AzureRmPolicyDefinition -Name denyCoolTiering -Description "Deny cool access tiering for storage" -Policy '{
  "if": {
    "allOf": [
      {
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

```
$policy = '{
    "if": {
        "allOf": [
            {
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

$definition = New-AzureRmPolicyDefinition -Name storageLocations -Description "Policy to specify locations for storage accounts." -Policy $policy -Parameter $parameters
```

## <a name="view-policy-definitions"></a>查看策略定义

若要查看订阅中的所有策略定义，请运行以下命令：

```
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

```
az policy definition create --name denyCoolTiering --description "Deny cool access tiering for storage" --rules '{
  "if": {
    "allOf": [
      {
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

## <a name="view-policy-definitions"></a>查看策略定义

若要查看订阅中的所有策略定义，请运行以下命令：

```
az policy definition list
```

此命令可返回所有可用的策略定义，包括内置策略。 返回的每个策略的格式如下：

```
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

通过计划定义，可以组合某些策略定义以实现首要目标。 创建计划定义，确保定义范围内的资源符合构成计划定义的策略定义。  有关计划定义的详细信息，请参阅 [Azure 策略概述](./azure-policy-introduction.md)。

### <a name="create-an-initiative-definition"></a>创建计划定义

1. 选择左侧窗格中“创作”下的“定义”。

   ![选择定义](media/create-manage-policy/select-definitions.png)

2. 选择页面顶部的“计划定义”，然后将显示“计划定义”窗体。
3. 输入计划的名称和说明。

   在此示例中，我们想要确保资源符合有关保证安全的策略定义，计划的名称为“保证安全”，说明为：创建了此计划，处理所有与保护资源相关的策略定义。

   ![计划定义](media/create-manage-policy/initiative-definition.png)

4. 浏览“可用定义”列表，然后选择要添加到该计划的策略定义。 对于“保证安全”计划，添加以下内置策略定义：
   - 需要 SQL Server 版本 12.0
   - 监视安全中心内未受保护的 Web 应用。
   - 监视安全中心内的许可网络。
   - 监视安全中心内列为白名单的可能的应用。
   - 监视安全中心内未加密的 VM 磁盘。

   ![计划定义](media/create-manage-policy/initiative-definition-2.png)

   从列表中选择策略定义后，该策略定义将显示在“策略和参数”下，如上所示。

5. 选择“创建” 。

### <a name="assign-an-initiative-definition"></a>分配计划定义

1. 转到“创作”下的“定义”选项卡。
2. 搜索所创建的“保证安全”计划定义。
3. 选择此计划定义，然后选择“分配”。

   ![分配定义](media/create-manage-policy/assign-definition.png)

4. 输入以下内容，填写“分配”窗体：
   - 名称：保证安全分配
   - 说明：此计划分配旨在在“Azure 顾问容量开发”订阅中强制执行这组策略定义
   - 定价层：标准
   - 想要应用于此分配的范围：Azure 顾问容量开发

5. 选择“分配”。

## <a name="resolve-a-non-compliant-or-denied-resource"></a>解决不符合或遭拒绝的资源

继续以上示例，在分配策略定义、要求使用 SQL server 版本 12.0 以后，通过其他版本创建的 SQL Server 将被拒绝。 本部分介绍如何通过请求排除解决拒绝尝试创建不同版本的 SQL Server 的问题。

1. 选择左侧窗格中的“分配”。
2. 浏览所有策略分配并启动“需要 SQL Server 版本 12.0”分配。
3. 请求排除尝试在其中创建 SQL Server 的资源组。 此示例排除 Microsoft.Sql/servers/databases：baconandbeer/Cheetos 和 baconandbeer/Chorizo。

   ![请求排除](media/create-manage-policy/request-exclusion.png)

   可解决遭拒绝资源的其他方法包括：如果具有需要创建 SQL Server 的强有力理由，请联系与策略相关的联系人；如果具有策略的访问权限，请直接编辑策略。

4. 选择“保存”。

本部分中，通过请求对资源进行排除解决了尝试创建版本为 12.0 的 SQL Server 遭拒绝的问题。

## <a name="clean-up-resources"></a>清理资源

如果计划继续浏览后续教程，请勿清除在本指南中创建的资源。 如果不打算继续学习，请通过以下步骤删除上面创建的所有分配或定义：

1. 选择左侧窗格中的“定义”（如果尝试删除分配，则选择“分配”）。
2. 搜索刚创建的新计划或策略定义（或分配）。
3. 选择定义或分配末端的省略号，并选择“删除定义”（或“删除分配”）。

## <a name="next-steps"></a>后续步骤

在本教程中，你已成功完成以下操作：

> [!div class="checklist"]
> * 分配策略，对将来创建的资源强制执行条件
> * 创建并分配计划定义，跟踪多个资源的符合性
> * 解决不符合或遭拒绝的资源
> * 在组织中实施新策略

若要了解有关策略定义结构的详细信息，请查看以下文章：

> [!div class="nextstepaction"]
> [Azure 策略定义结构](policy-definition.md)
