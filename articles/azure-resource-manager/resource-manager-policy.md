---
title: "Azure Resource Manager 策略 | Microsoft Docs"
description: "介绍如何使用 Azure 资源管理器策略来防止订阅、资源组或单个资源等不同的范围发生冲突。"
services: azure-resource-manager
documentationcenter: na
author: ravbhatnagar
manager: timlt
editor: tysonn
ms.assetid: abde0f73-c0fe-4e6d-a1ee-32a6fce52a2d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2016
ms.author: gauravbh;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: bdc759341e1f9707ddf688512249c3297d85c29b


---
# <a name="use-policy-to-manage-resources-and-control-access"></a>使用策略来管理资源和控制访问
Azure 资源管理器现在可让你通过自定义策略来控制访问。 使用策略可以防止组织中的用户违反管理组织资源所需的惯例。 

你可以创建策略定义来描述会明确遭到拒绝的操作或资源。 可以在所需范围（例如订阅、资源组或是单个资源）分配这些策略定义。 策略由所有子资源继承。 因此，如果将策略应用到资源组，则会将其应用到该资源组中的所有资源。

本文介绍可用于创建策略的策略定义语言的基本结构。 还将介绍如何在不同范围内应用这些策略。

## <a name="how-is-it-different-from-rbac"></a>策略与 RBAC 有什么不同？
策略与基于角色的访问控制之间有几个重要的差别，但首先你必须了解策略是和 RBAC 一起工作的。 若要使用策略，必须通过 RBAC 完成身份验证。 不同于 RBAC，策略是默认的允许和明确拒绝系统。 

RBAC 着重于**用户**在不同的范围可执行的操作。 例如，将特定用户添加到所需范围的资源组的参与者角色后，该用户便可对该资源组做出更改。 

策略着重于各种范围的**资源**操作。 例如，通过策略，你可以控制可预配的资源类型，或限制可以预配资源的位置。

## <a name="common-scenarios"></a>常见方案
一个常见方案是为了费用分摊而要求提供部门标记。 组织可能仅在关联恰当的成本中心时允许操作，否则会拒绝请求。 本策略有助于组织向恰当的成本中心收取所执行操作的费用。

另一个常见方案是组织可能想要控制创建资源的位置。 或者它们可能想要通过仅允许预配特定类型的资源，来控制对资源的访问。

同样地，组织可以控制服务类别或为资源强制运行所需的命名约定。

使用策略可轻松实现这些方案。

## <a name="policy-definition-structure"></a>策略定义结构
策略定义是使用 JSON 创建的。 它包含定义操作的一个或多个条件/逻辑运算符，以及告知满足条件时发生的情况的效果。 该架构在 [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json) 中发布。 

基本上，策略包含以下元素：

**条件/逻辑运算符：**可通过一组逻辑运算符操作的条件集。

**效果：**满足条件时所发生的情况 - 拒绝或审核。 审核效果会发出警告事件服务日志。 例如，管理员可以创建策略，如果有人创建大型 VM，则此策略会引发审核事件。 管理员可以随后审查日志。

    {
      "if" : {
          <condition> | <logical operator>
      },
      "then" : {
          "effect" : "deny | audit | append"
      }
    }

## <a name="policy-evaluation"></a>策略评估
创建资源时对策略进行评估。 对于模板部署，在模板中创建每个资源时会评估策略。 

> [!NOTE]
> 当前，策略不对不支持标记、种类和位置的资源类型进行评估，例如 Microsoft.Resources/deployments 资源类型。 将来会添加此支持。 若要避免向后兼容问题，创作策略时应显式指定类型。 例如，未指定类型的标记策略会应用于所有类型。 在此情况下，如果有嵌套资源不支持标记，并且部署资源类型已添加到策略评估中，则模板部署可能失败。 
> 
> 

## <a name="logical-operators"></a>逻辑运算符
支持的逻辑运算符和语法是：

| 运算符名称 | 语法 |
|:--- |:--- |
| Not |"not" : {&lt;condition  or operator &gt;} |
| And |"allOf" : [ {&lt;condition  or operator &gt;},{&lt;condition  or operator &gt;}] |
| 或 |"anyOf" : [ {&lt;condition  or operator &gt;},{&lt;condition  or operator &gt;}] |

资源管理器可让你通过嵌套的运算符在策略中指定复杂逻辑。 例如，你可以拒绝在指定资源类型的特定位置创建资源。 本主题提供了嵌套运算符的示例。

## <a name="conditions"></a>条件
条件评估**字段**或**源**是否符合特定的准则。 支持的条件名称和语法是：

| 条件名称 | 语法 |
|:--- |:--- |
| 等于 |"equals" : "&lt;value&gt;" |
| Like |"like" : "&lt;value&gt;" |
| Contains |"contains" : "&lt;value&gt;" |
| In |"in" : [ "&lt;value1&gt;","&lt;value2&gt;" ] |
| ContainsKey |"containsKey" : "&lt;keyName&gt;" |
| Exists |"exists" : "&lt;bool&gt;" |

### <a name="fields"></a>字段
条件是使用字段和源构成的。 字段显示用于描述资源状态的资源请求负载属性。 源表示请求本身的特征。 

支持以下字段和源：

字段：**name**、**kind**、**type**、**location**、**tags**、**tags*** 和 **property alias**。 

### <a name="property-aliases"></a>属性别名
属性别名可在策略定义中用于访问特定于资源类型的属性，例如设置和 SKU。 它适用于所有具有属性的 API 版本。 可以通过 REST API（未来将添加 Powershell 支持）检索别名：

    GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01

以下示例介绍了别名的定义： 如你所见，别名在不同的 API 版本中定义路径，无论属性名称是否更改。 

    "aliases": [
        {
          "name": "Microsoft.Storage/storageAccounts/sku.name",
          "paths": [
            {
              "path": "properties.accountType",
              "apiVersions": [
                "2015-06-15",
                "2015-05-01-preview"
              ]
            },
            {
              "path": "sku.name",
              "apiVersions": [
                "2016-01-01"
              ]
            }
          ]
        }
    ]

目前，支持的别名为：

| 别名名称 | 说明 |
| --- | --- |
| {resourceType}/sku.name |支持的资源类型：Microsoft.Compute/virtualMachines、<br />Microsoft.Storage/storageAccounts、<br />Microsoft.Web/serverFarms、<br /> Microsoft.Scheduler/jobcollections、<br />Microsoft.DocumentDB/databaseAccounts、<br />Microsoft.Cache/Redis、<br />Microsoft.CDN/profiles |
| {resourceType}/sku.family |支持的资源类型为 Microsoft.Cache/Redis |
| {resourceType}/sku.capacity |支持的资源类型为 Microsoft.Cache/Redis |
| Microsoft.Compute/virtualMachines/imagePublisher | |
| Microsoft.Compute/virtualMachines/imageOffer | |
| Microsoft.Compute/virtualMachines/imageSku | |
| Microsoft.Compute/virtualMachines/imageVersion | |
| Microsoft.Cache/Redis/enableNonSslPort | |
| Microsoft.Cache/Redis/shardCount | |
| Microsoft.SQL/servers/version | |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveId | |
| Microsoft.SQL/servers/databases/requestedServiceObjectiveName | |
| Microsoft.SQL/servers/databases/edition | |
| Microsoft.SQL/servers/databases/elasticPoolName | |
| Microsoft.SQL/servers/elasticPools/dtu | |
| Microsoft.SQL/servers/elasticPools/edition | |

目前，策略仅适用于 PUT 请求。 

## <a name="effect"></a>效果
策略支持三种类型的效果 - **deny**、**audit** 和 **append**。 

* Deny 将在审核日志中生成一个事件，并使请求失败
* Audit 将在审核日志中生成一个事件，但不会使请求失败
* Append 会将定义的字段集添加到请求 

对于 **append**，必须提供以下详细信息：

    ....
    "effect": "append",
    "details": [
      {
        "field": "field name",
        "value": "value of the field"
      }
    ]

值可以是字符串或 JSON 格式对象。 

## <a name="policy-definition-examples"></a>策略定义示例
现在来看看如何定义策略以实现前述方案。

### <a name="chargeback-require-departmental-tags"></a>退款：要求提供部门标记
以下策略拒绝没有含有“costCenter”键的标记的请求。

    {
      "if": {
        "not" : {
          "field" : "tags",
          "containsKey" : "costCenter"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

如果不存在任何标记，以下策略将附加具有预定义值的 costCenter 标记。 

    {
      "if": {
        "field": "tags",
        "exists": "false"
      },
      "then": {
        "effect": "append",
        "details": [
          {
            "field": "tags",
            "value": {"costCenter":"myDepartment" }
          }
        ]
      }
    }

如果存在除 costCenter 标记以外的其他标记，以下策略将附加具有预定义值的 costCenter 标记。 

    {
      "if": {
        "allOf": [
          {
            "field": "tags",
            "exists": "true"
          },
          {
            "field": "tags.costCenter",
            "exists": "false"
          }
        ]

      },
      "then": {
        "effect": "append",
        "details": [
          {
            "field": "tags.costCenter",
            "value": "myDepartment"
          }
        ]
      }
    }


### <a name="geo-compliance-ensure-resource-locations"></a>地区合规性：确保资源位置
以下示例显示的策略将拒绝位置不是北欧或西欧的请求。

    {
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="service-curation-select-the-service-catalog"></a>服务策展：选择服务目录
以下示例显示只允许对 Microsoft.Resources/\*、Microsoft.Compute/\*、Microsoft.Storage/\*、Microsoft.Network/\* 类型的服务执行操作的策略。 拒绝任何其他操作。

    {
      "if" : {
        "not" : {
          "anyOf" : [
            {
              "field" : "type",
              "like" : "Microsoft.Resources/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Compute/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Storage/*"
            },
            {
              "field" : "type",
              "like" : "Microsoft.Network/*"
            }
          ]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="use-approved-skus"></a>使用批准的 SKU
以下示例演示如何使用属性别名来限制 SKU。 在此示例中，只有 Standard_LRS 和 Standard_GRS 已被批准用于存储帐户。

    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "allof": [
                {
                  "field": "Microsoft.Storage/storageAccounts/sku.name",
                  "in": ["Standard_LRS", "Standard_GRS"]
                }
              ]
            }
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    }


### <a name="naming-convention"></a>命名约定
以下示例演示如何使用“like”条件支持的通配符。 该条件指明，如果名称符合所述模式 (namePrefix\*nameSuffix)，则拒绝请求。

    {
      "if" : {
        "not" : {
          "field" : "name",
          "like" : "namePrefix*nameSuffix"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### <a name="tag-requirement-just-for-storage-resources"></a>仅针对存储资源的标记要求
以下示例演示如何嵌套逻辑运算符，以要求仅对存储资源使用应用程序标记。

    {
        "if": {
            "allOf": [
              {
                "not": {
                  "field": "tags",
                  "containsKey": "application"
                }
              },
              {
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
              }
            ]
        },
        "then": {
            "effect": "audit"
        }
    }

## <a name="create-and-assign-a-policy"></a>创建和分配策略
若要应用策略，需要创建策略定义，然后在某范围内应用该策略。 

### <a name="rest-api"></a>REST API
可以使用[用于策略定义的 REST API](https://docs.microsoft.com/rest/api/resources/policydefinitions) 来创建策略。 REST API 可让你创建和删除策略定义，以及获取现有定义的信息。

若要创建策略，请运行：

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

对于 api-version，请使用 *2016-04-01*。 包括类似于下方示例的请求正文：

    {
      "properties":{
        "policyType":"Custom",
        "description":"Test Policy",
        "policyRule":{
          "if" : {
            "not" : {
              "field" : "tags",
              "containsKey" : "costCenter"
            }
          },
          "then" : {
            "effect" : "deny"
          }
        }
      },
      "name":"testdefinition"
    }

可以通过[用于策略分配的 REST API](https://docs.microsoft.com/rest/api/resources/policyassignments)，在所需范围内应用策略定义。 REST API 可让你创建和删除策略分配，以及获取现有分配的信息。

若要创建策略分配，请运行：

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

{policy-assignment} 是策略分配的名称。 对于 api-version，请使用 *2016-04-01*。 

使用类似于下方示例的请求正文：

    {
      "properties":{
        "displayName":"VM_Policy_Assignment",
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
      "name":"VMPolicyAssignment"
    }

### <a name="powershell"></a>PowerShell
可以使用 New-AzureRmPolicyDefinition cmdlet 创建策略定义。 以下示例将创建仅允许北欧和西欧资源的策略。

    $policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy '{    
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }'            

执行输出存储在 $policy 对象中，稍后可在分配策略期间使用。 对于策略参数，也可以提供包含策略的 .json 文件路径，而不是指定内联策略。

    New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain     regions" -Policy "path-to-policy-json-on-disk"

可以使用 New-AzureRmPolicyAssignment cmdlet 将策略应用到所需范围：

    New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

此处的 $policy 是执行 New-AzureRmPolicyDefinition cmdlet 后返回的策略对象。 此处的范围是指定的资源组的名称。

若要删除策略分配，请使用：

    Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

可以分别通过 Get-AzureRmPolicyDefinition、Set-AzureRmPolicyDefinition 和 Remove-AzureRmPolicyDefinition cmdlet 来获取、更改或删除策略定义。

同样，可以分别通过 Get-AzureRmPolicyAssignment、Set-AzureRmPolicyAssignment 和 Remove-AzureRmPolicyAssignment 来获取、更改或删除策略分配。

### <a name="azure-cli"></a>Azure CLI
可以结合策略定义命令使用 Azure CLI 来创建策略定义。 以下示例将创建仅允许北欧和西欧资源的策略。

    azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy-string '{    
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }'    


可以指定包含策略的 .json 文件路径，而无需指定内联策略。

    azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy "path-to-policy-json-on-disk"

可以使用策略分配命令将策略应用到所需范围：

    azure policy assignment create --name regionPolicyAssignment --policy-definition-id /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/<policy-name> --scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

此处的范围是指定的资源组的名称。 如果 policy-definition-id 参数的值未知，可以通过 Azure CLI 获取该值。 

    azure policy definition show <policy-name>

若要删除策略分配，请使用：

    azure policy assignment delete --name regionPolicyAssignment --scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

可以分别通过策略定义 show、set 和 delete 命令获取、更改或删除策略定义。

同样，可以分别通过策略定义 show 和 delete 命令获取、更改或删除策略分配。

## <a name="policy-audit-events"></a>策略审核事件
在应用策略之后，即可看到与策略相关的事件。 可以转到门户、使用 PowerShell 或 Azure CLI 获取此数据。 

### <a name="powershell"></a>PowerShell
若要查看拒绝效果相关的所有事件，可以使用以下 PowerShell 命令：

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/deny/action"} 

若要查看审核效果相关的所有事件，可以使用以下命令：

    Get-AzureRmLog | where {$_.OperationName -eq "Microsoft.Authorization/policies/audit/action"} 

### <a name="azure-cli"></a>Azure CLI
若要查看资源组中与拒绝效果相关的所有事件，可以使用以下 CLI 命令：

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.value == \"Microsoft.Authorization/policies/deny/action\")"

若要查看审核效果相关的所有事件，可以使用以下 CLI 命令：

    azure group log show ExampleGroup --json | jq ".[] | select(.operationName.value == \"Microsoft.Authorization/policies/audit/action\")"

## <a name="view-a-policy"></a>查看策略
使用 PowerShell、Azure CLI 或 REST API 查看策略。 部署失败后，可能需要查看策略并查看拒绝该部署的规则。 错误消息包括策略定义的 ID。

### <a name="powershell"></a>PowerShell
若要获取策略，请使用以下 cmdlet：

    (Get-AzureRmPolicyAssignment -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/{definition-name}").Properties.policyRule | ConvertTo-Json

这将返回策略定义的 JSON。

### <a name="azure-cli"></a>Azure CLI
若要获取策略，请使用以下命令：

    azure policy definition show {definition-name} --json

### <a name="rest-api"></a>REST API
若要获取策略，请使用[获取策略定义](https://docs.microsoft.com/rest/api/resources/policydefinitions#PolicyDefinitions_Get)操作。

## <a name="next-steps"></a>后续步骤
* 有关企业可如何使用 Resource Manager 有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](resource-manager-subscription-governance.md)。




<!--HONumber=Nov16_HO3-->


