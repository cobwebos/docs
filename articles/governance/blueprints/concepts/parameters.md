---
title: 在 Azure 蓝图中通过参数创建动态蓝图
description: 了解有关静态和动态参数以及如何使用它们创建动态蓝图。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: e1a1d736eb9b22cd444a75b94d112abfe3fbe5af
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993572"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>通过参数创建动态蓝图

具有各种项目（如资源组、资源管理器模板、策略或角色分配）的完全定义蓝图可在 Azure 中快速创建和一致地配置对象。 为灵活使用这些可重复使用的设计模式和容器，Azure 蓝图支持参数。 参数在定义和分配期间均创建灵活性，以更改蓝图部署的项目的属性。

一个简单的示例是资源组项目。 创建资源组后，必须向其提供两个所需值：名称和位置。 将资源组添加到蓝图时，如果参数不存在，则应为蓝图的每次使用定义该名称和位置。 这将导致每次使用蓝图时都在同一资源组中创建项目。 虽然资源组本身不是问题，但该资源组内的资源会重复并产生冲突。

> [!NOTE]
> 对于两个不同的蓝图，包含具有相同名称的资源组不是问题。
> 如果包含在蓝图中的资源组已存在，蓝图会继续在该资源组中创建相关项目。 这可能会产生冲突，因为订阅中不能存在具有相同名称和资源类型的两个资源。

此时便可利用参数了。 对于这些属性（资源组情况下为名称和位置属性）的值，蓝图允许在定义蓝图期间不对其进行定义，而是在分配给订阅期间定义其值。 这样便可以重复使用在一个订阅中创建资源组和其他资源的蓝图，且不会产生冲突。

## <a name="blueprint-parameters"></a>蓝图参数

通过 REST API，除了每个受支持的项目外，还可在蓝图自身上创建参数。 在蓝图上创建参数时，该蓝图中的项目可使用该参数。 示例可能是资源组命名的前缀。 然后，该项目可以使用蓝图参数来创建“mostly dynamic”参数，因为该参数仍可在分配期间定义，但将具有可能遵循组织命名规则的一致性。 有关步骤，请参阅[设置静态参数 - 蓝图级别参数](#blueprint-level-parameter)。

### <a name="using-securestring-and-secureobject-parameters"></a>使用 secureString 和 secureObject 参数

虽然资源管理器模板项目支持“secureString”和“secureObject”类型的参数，但 Azure 蓝图要求每个参数与 Azure Key Vault 连接。
这可防止将机密与蓝图一起存储的不安全做法，并鼓励使用安全模式。 Azure 蓝图通过检测资源管理器模板项目中包含任一安全参数的情况，并在蓝图分配期间按每个检测到的安全参数提示以下 Key Vault 属性，从而简化了这一点：

- Key Vault 资源 ID
- Key Vault 机密名称
- Key Vault 机密版本

引用的 Key Vault 必须位于与要向其分配蓝图的同一订阅中，并且必须在 Key Vault 的“访问策略”页上配置“启用对 Azure 资源管理器的访问进行模板部署”。 有关如何启用此功能的说明，请参阅 [Key Vault - 启用模板部署](../../../managed-applications/key-vault-access.md#enable-template-deployment)。
有关 Azure Key Vault 的详细信息，请参阅 [ 概述](../../../key-vault/key-vault-overview.md)。

## <a name="parameter-types"></a>参数类型

### <a name="static-parameters"></a>静态参数

蓝图定义中定义的参数值称为“静态参数”。 这是因为每次使用蓝图都将部署使用该静态值的项目。 在资源组示例中，这对资源组名称没有意义，但可能对位置有意义。 然后，蓝图的每次分配都会在同一位置创建资源组，无论分配期间其名称为何。 这样，便可选择所定义为分配期间必需的内容或可更改的内容。

#### <a name="setting-static-parameters-in-the-portal"></a>在门户中设置静态参数

1. 在 Azure 门户中单击“所有服务”，然后搜索并选择左侧窗格中的“策略”，启动 Azure 蓝图服务。 在“策略”页上，单击“蓝图”。

1. 从左侧页面中选择“蓝图定义”。

1. 单击现有蓝图，然后单击“编辑蓝图”或单击“+ 创建蓝图”，并在“基本信息”选项卡上填写信息。

1. 单击“下一步: 项目”或单击“项目”选项卡。

1. 添加到蓝图中的项目（具有参数选项）会在“参数”列中显示“填充了 X 个参数，共 Y 个参数”。 单击项目行，编辑项目参数。

   ![蓝图参数](../media/parameters/parameter-column.png)

1. “编辑项目”页会显示适用于所单击项目的值选项。 项目上的每个参数具有标题、值框和复选框。 将框设置为未选中状态，使其称为“静态参数”。 在以下示例中，只有“位置”是“静态参数”，因为它处于未选中状态，同时“资源组名称”已选中。

   ![蓝图静态参数](../media/parameters/static-parameter.png)

#### <a name="setting-static-parameters-from-rest-api"></a>从 REST API 设置静态参数

在每个 REST API URI 中，包含替换为自己的值所使用的变量：

- `{YourMG}` - 替换为管理组的名称
- `{subscriptionId}` - 替换为订阅 ID

##### <a name="blueprint-level-parameter"></a>蓝图级别参数

通过 REST API 创建蓝图时，可以创建[蓝图参数](#blueprint-parameters)。 若要执行此操作，请使用以下 REST API URI 和正文格式：

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2017-11-11-preview
  ```

- 请求正文

  ```json
  {
      "properties": {
          "description": "This blueprint has blueprint level parameters.",
          "targetScope": "subscription",
          "parameters": {
              "owners": {
                  "type": "array",
                  "metadata": {
                      "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                  }
              }
          },
          "resourceGroups": {
              "storageRG": {
                  "description": "Contains the resource template deployment and a role assignment."
              }
          }
      }
  }
  ```

创建蓝图级别参数后，便可在添加到该蓝图的项目上使用该参数。
以下 REST API 示例在蓝图上创建角色分配项目，并使用蓝图级别参数。

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2017-11-11-preview
  ```

- 请求正文

  ```json
  {
      "kind": "roleAssignment",
      "properties": {
          "resourceGroup": "storageRG",
          "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
          "principalIds": "[parameters('owners')]"
      }
  }
  ```

在此示例中，“principalIds”属性通过提供 `[parameters('owners')]` 的值，使用了“owners”蓝图级别参数。 使用蓝图级别参数在项目上设置参数仍然是“静态参数”的示例，因为在蓝图分配期间无法设置该参数，并且在每个分配上都是此值。

##### <a name="artifact-level-parameter"></a>项目级别参数

在项目上创建“静态参数”情况相似，但采用直接值而不是使用 `parameters()` 函数。 以下示例创建了两个静态参数：“tagName”和“tagValue”。 每个参数的值直接提供，且不使用函数调用。

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2017-11-11-preview
  ```

- 请求正文

  ```json
  {
      "kind": "policyAssignment",
      "properties": {
          "description": "Apply storage tag and the parameter also used by the template to resource groups",
          "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
          "parameters": {
              "tagName": {
                  "value": "StorageType"
              },
              "tagValue": {
                  "value": "Premium_LRS"
              }
          }
      }
  }
  ```

### <a name="dynamic-parameters"></a>动态参数

与静态参数相对的是“动态参数”。 此参数未在蓝图上定义，而是在蓝图的每次分配期间进行定义。 在资源组示例中，这对资源组名称有意义，并为蓝图的每次分配提供不同名称。

#### <a name="setting-dynamic-parameters-in-the-portal"></a>在门户中设置动态参数

1. 在 Azure 门户中单击“所有服务”，然后搜索并选择左侧窗格中的“策略”，启动 Azure 蓝图服务。 在“策略”页上，单击“蓝图”。

1. 从左侧页面中选择“蓝图定义”。

1. 右键单击想要分配的蓝图，并单击“分配蓝图”，或单击想要分配的蓝图，然后单击“分配蓝图”按钮。

1. 在“分配蓝图”页上，查找“项目参数”部分。 具有至少一个“动态参数”的每个项目会显示项目和配置选项。 分配蓝图前，请向参数提供所需值。 在以下示例中，“名称”是“动态参数”，必须对其定义以完成蓝图分配。

   ![蓝图动态参数](../media/parameters/dynamic-parameter.png)

#### <a name="setting-dynamic-parameters-from-rest-api"></a>从 REST API 设置动态参数

在分配期间设置“动态参数”是通过直接提供所需值完成的。 并不使用函数（如 `parameters()`），提供的值是适当的字符串。 资源组的项目使用“模板名称”以及“名称”和“位置”属性定义。 每个所包含项目的所有其他参数，均在“参数”下使用“\<名称\>”和“值”密钥对进行定义。 如果为分配期间未提供的动态参数配置了蓝图，则分配将失败。

- REST API URI

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2017-11-11-preview
  ```

- 请求正文

  ```json
  {
      "properties": {
          "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}  /providers/Microsoft.Blueprint/blueprints/MyBlueprint",
          "resourceGroups": {
              "storageRG": {
                  "name": "StorageAccount",
                  "location": "eastus2"
              }
          },
          "parameters": {
              "storageAccountType": {
                  "value": "Standard_GRS"
              },
              "tagName": {
                  "value": "CostCenter"
              },
              "tagValue": {
                  "value": "ContosoIT"
              },
              "contributors": {
                  "value": [
                      "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                      "38833b56-194d-420b-90ce-cff578296714"
                  ]
                },
              "owners": {
                  "value": [
                      "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                      "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                  ]
              }
          }
      },
      "identity": {
          "type": "systemAssigned"
      },
      "location": "westus"
  }
  ```

## <a name="next-steps"></a>后续步骤

- 了解[蓝图生命周期](lifecycle.md)
- 了解如何自定义[蓝图排序顺序](sequencing-order.md)
- 了解如何使用[蓝图资源锁定](resource-locking.md)
- 了解如何[更新现有分配](../how-to/update-existing-assignments.md)
- 使用[常规疑难解答](../troubleshoot/general.md)在蓝图分配期间解决问题