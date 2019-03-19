---
title: 使用参数创建动态蓝图
description: 了解有关静态和动态参数以及如何使用它们创建动态蓝图。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 42a70f7ea21a58f40f7786d6c6f1a51093923f83
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57838011"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>通过参数创建动态蓝图

具有各种项目（如资源组、资源管理器模板、策略或角色分配）的完全定义蓝图可在 Azure 中快速一致地创建对象。 为灵活使用这些可重复使用的设计模式和容器，Azure 蓝图支持参数。 参数在定义和分配期间均创建灵活性，以更改蓝图部署的项目的属性。

一个简单的示例是资源组项目。 创建资源组后，必须向其提供两个所需值：名称和位置。 将资源组添加到蓝图时，如果参数不存在，则应为蓝图的每次使用定义该名称和位置。 这种重复会导致每次使用蓝图时都在同一资源组中创建项目。 资源组内的资源会重复并产生冲突。

> [!NOTE]
> 对于两个不同的蓝图，包含具有相同名称的资源组不是问题。
> 如果包含在蓝图中的资源组已存在，蓝图会继续在该资源组中创建相关项目。 这可能会产生冲突，因为订阅中不能存在具有相同名称和资源类型的两个资源。

使用参数可以解决此问题。 使用蓝图可在分配到订阅期间定义每个项目属性的值。 通过参数还可以重复使用在一个订阅中创建资源组和其他资源的蓝图，且不会产生冲突。

## <a name="blueprint-parameters"></a>蓝图参数

通过 REST API 可以在蓝图自身上创建参数。 这些参数不同于每个受支持项目中的参数。 在蓝图上创建参数时，该蓝图中的项目可使用该参数。 示例可能是资源组命名的前缀。 项目可以使用蓝图参数创建“基本上动态的”参数。 由于还可以在分配期间定义参数，因此，此模式可以实现遵守命名规则的一致性。 有关步骤，请参阅[设置静态参数 - 蓝图级别参数](#blueprint-level-parameter)。

### <a name="using-securestring-and-secureobject-parameters"></a>使用 secureString 和 secureObject 参数

虽然资源管理器模板项目支持“secureString”和“secureObject”类型的参数，但 Azure 蓝图要求每个参数与 Azure Key Vault 连接。
此安全措施可防止将机密与蓝图一起存储的不安全做法，并有利于采用安全模式。 Azure 蓝图支持此安全措施，它可以检测是否在资源管理器模板项目中包含了任一安全参数。 然后，该服务会在分配期间提示输入每个检测到的安全参数的以下 Key Vault 属性：

- Key Vault 资源 ID
- Key Vault 机密名称
- Key Vault 机密版本

如果使用蓝图分配**系统分配的托管标识**，则引用 Key Vault_必须_蓝图定义分配给同一订阅中。

如果使用蓝图分配**用户分配托管标识**，则引用 Key Vault_可能_集中式订阅中存在。 托管的标识必须授予密钥保管库之前蓝图分配适当的权限。

在这两种情况下，密钥保管库必须具有**启用为模板部署到 Azure 资源管理器中访问**上配置**访问策略**页。 有关如何启用此功能的说明，请参阅 [Key Vault - 启用模板部署](../../../managed-applications/key-vault-access.md#enable-template-deployment)。

有关 Azure Key Vault 的详细信息，请参阅 [ 概述](../../../key-vault/key-vault-overview.md)。

## <a name="parameter-types"></a>参数类型

### <a name="static-parameters"></a>静态参数

蓝图定义中定义的参数值称为**静态参数**，因为每次使用蓝图都会部署使用该静态值的项目。 在资源组示例中，这对资源组名称没有意义，但可能对位置有意义。 然后，蓝图的每次分配都会在同一位置创建资源组，无论分配期间其名称为何。 借助这种灵活性，可以选择定义为分配期间必需的内容或可更改的内容。

#### <a name="setting-static-parameters-in-the-portal"></a>在门户中设置静态参数

1. 在左侧窗格中，选择“所有服务”。 搜索并选择“蓝图”。

1. 从左侧页面中选择“蓝图定义”。

1. 单击现有蓝图，然后单击**编辑蓝图**或单击 **+ 创建蓝图**上填写信息并**基础知识**选项卡。

1. 单击“下一步:项目”或单击“项目”选项卡。

1. 添加到蓝图中的项目（具有参数选项）会在“参数”列中显示“填充了 X 个参数，共 Y 个参数”。 单击项目行，编辑项目参数。

   ![蓝图参数](../media/parameters/parameter-column.png)

1. “编辑项目”页会显示适用于所单击项目的值选项。 项目上的每个参数具有标题、值框和复选框。 将框设置为未选中状态，使其称为“静态参数”。 在以下示例中，只有“位置”是“静态参数”，因为它处于未选中状态，同时“资源组名称”已选中。

   ![蓝图静态参数](../media/parameters/static-parameter.png)

#### <a name="setting-static-parameters-from-rest-api"></a>从 REST API 设置静态参数

在每个 REST API URI 中，包含替换为自己的值所使用的变量：

- `{YourMG}` - 替换为管理组的名称
- `{subscriptionId}` - 替换为订阅 ID

##### <a name="blueprint-level-parameter"></a>蓝图级别参数

通过 REST API 创建蓝图时，可以创建[蓝图参数](#blueprint-parameters)。 为此，请使用以下 REST API URI 和正文格式：

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
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
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
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

在此示例中，**principalIds** 属性通过 `[parameters('owners')]` 的值使用 **owners** 蓝图级参数。 使用蓝图级参数在项目中设置参数仍是**静态参数**的示例。 蓝图级参数无法在蓝图分配期间设置，每次分配时都是同一个值。

##### <a name="artifact-level-parameter"></a>项目级别参数

在项目上创建“静态参数”情况相似，但采用直接值而不是使用 `parameters()` 函数。 以下示例创建了两个静态参数：“tagName”和“tagValue”。 每个参数的值直接提供，且不使用函数调用。

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
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

与静态参数相对的是“动态参数”。 此参数未在蓝图中定义，而是在每次分配蓝图期间定义的。 在资源组示例中，使用**动态参数**对资源组名称有意义。 每次分配蓝图时，它将提供不同的名称。

#### <a name="setting-dynamic-parameters-in-the-portal"></a>在门户中设置动态参数

1. 在左侧窗格中，选择“所有服务”。 搜索并选择“蓝图”。

1. 从左侧页面中选择“蓝图定义”。

1. 右键单击要分配的蓝图。 选择**分配蓝图**或单击你想要分配的蓝图，然后单击**分配蓝图**按钮。

1. 上**分配蓝图**页上，找到**项目参数**部分。 具有至少一个“动态参数”的每个项目会显示项目和配置选项。 分配蓝图前，请向参数提供所需值。 在以下示例中，“名称”是“动态参数”，必须对其定义以完成蓝图分配。

   ![蓝图动态参数](../media/parameters/dynamic-parameter.png)

#### <a name="setting-dynamic-parameters-from-rest-api"></a>从 REST API 设置动态参数

在分配期间设置**动态参数**是通过直接输入值完成的。
并不使用函数（如 `parameters()`），提供的值是适当的字符串。
资源组的项目是使用“模板名称”、**name** 和 **location** 属性定义的。 包含的项目的其他所有参数在 **parameters** 下使用 **\<名称\>** 和**值**键对进行定义。 如果为分配期间未提供的动态参数配置了蓝图，则分配将会失败。

- REST API URI

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
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

- 了解如何[蓝图生命周期](lifecycle.md)。
- 了解如何自定义[蓝图先后顺序](sequencing-order.md)。
- 了解如何使利用[蓝图资源锁定](resource-locking.md)。
- 了解如何[更新现有分配](../how-to/update-existing-assignments.md)。
- 使用蓝图赋值的过程中解决的问题[常规故障排除](../troubleshoot/general.md)。