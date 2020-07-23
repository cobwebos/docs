---
title: 测试工具包的测试用例
description: 介绍 ARM 模板测试工具包运行的测试。
ms.topic: conceptual
ms.date: 06/19/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 5c18a2658ba1af9370699004860d1743603e8143
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85255722"
---
# <a name="default-test-cases-for-arm-template-test-toolkit"></a>ARM 模板测试工具包的默认测试用例

本文介绍了随[模板测试工具包](test-toolkit.md)一起运行的默认测试。 它提供通过测试或使测试失败的示例。 它包含每个测试的名称。

## <a name="use-correct-schema"></a>使用正确的架构

测试名称： **DeploymentTemplate 架构正确**

在模板中，必须指定有效的架构值。

下面的示例**通过**此测试。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": []
}
```

模板中的架构属性必须设置为以下架构之一：

* `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json`

## <a name="parameters-must-exist"></a>参数必须存在

测试名称： **Parameters 属性必须存在**

模板应具有 parameters 元素。 参数对于使模板在不同环境中可重用非常重要。 为部署到不同环境时更改的值，将参数添加到模板。

下面的示例**通过**此测试：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
          "type": "string",
          "defaultValue": "linux-vm",
          "metadata": {
            "description": "Name for the Virtual Machine."
          }
      }
  },
  ...
```

## <a name="declared-parameters-must-be-used"></a>必须使用声明的参数

测试名称：**必须引用参数**

若要减少模板中的混乱，请删除已定义但未使用的所有参数。 此测试将查找未在模板中任何位置使用的参数。 消除未使用的参数还可以更轻松地部署模板，因为无需提供不必要的值。

## <a name="secure-parameters-cant-have-hardcoded-default"></a>安全参数不能包含硬编码默认值

测试名称：**安全字符串参数不能有默认值**

不要为模板中的安全参数提供硬编码的默认值。 空字符串对于默认值是正确的。

对包含敏感值（如密码）的参数使用类型**SecureString**或**SecureObject** 。 当参数使用安全类型时，不会记录参数的值，也不会将其存储在部署历史记录中。 此操作可防止恶意用户发现敏感值。

但是，当你提供默认值时，可访问该模板或部署历史记录的任何人都可以发现该值。

下面的示例未**通过**此测试：

```json
"parameters": {
    "adminPassword": {
        "defaultValue": "HardcodedPassword",
        "type": "SecureString"
    }
}
```

下一个示例**通过**此测试：

```json
"parameters": {
    "adminPassword": {
        "type": "SecureString"
    }
}
```

## <a name="location-uses-parameter"></a>Location 使用参数

测试名称：**位置不应进行硬编码**

模板的用户可以使用有限区域。 将资源位置设置为时 `"[resourceGroup().location]"` ，可能会在其他用户无法访问的区域中创建资源组。 阻止这些用户使用该模板。

定义每个资源的位置时，请使用默认为资源组位置的参数。 提供此参数后，用户可以在方便的情况下使用默认值，但也可以指定其他位置。

下面的示例将**无法**进行此测试，因为资源上的位置设置为 `resourceGroup().location` 。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[resourceGroup().location]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ]
}
```

下一个示例使用 location 参数，但未**通过**此测试，因为 location 参数默认为硬编码位置。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "westus"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

而是创建默认为资源组位置的参数，但允许用户提供不同的值。 下面的示例**通过**此测试。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

## <a name="resources-should-have-location"></a>资源应具有位置

测试名称：**资源应具有位置**

应将资源的位置设置为[模板表达式](template-expressions.md)或 `global` 。 模板表达式通常使用上一测试中所述的 location 参数。

下面**的示例将无法进行**此测试，因为位置不是表达式或 `global` 。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "westus",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

下面的示例**通过**此测试。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Maps/accounts",
            "apiVersion": "2020-02-01-preview",
            "name": "demoMap",
            "location": "global",
            "sku": {
                "name": "S0"
            }
        }
    ],
    "outputs": {
    }
}
```

下一个示例还将**通过**此测试。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

## <a name="vm-size-uses-parameter"></a>VM 大小使用参数

测试名称： **VM 大小应为参数**

不要硬编码虚拟机大小。 提供一个参数，使模板的用户可以修改部署的虚拟机的大小。

下面的示例将**无法**进行此测试。

```json
"hardwareProfile": {
    "vmSize": "Standard_D2_v3"
}
```

而是提供参数。

```json
"vmSize": {
    "type": "string",
    "defaultValue": "Standard_A2_v2",
    "metadata": {
        "description": "Size for the Virtual Machine."
    }
},
```

然后，将 VM 大小设置为该参数。

```json
"hardwareProfile": {
    "vmSize": "[parameters('vmSize')]"
},
```

## <a name="min-and-max-values-are-numbers"></a>最小值和最大值为数字

测试名称：**最小值和最大值为数字**

如果为参数定义最小值和最大值，请将其指定为数字。

下面的示例未**通过**此测试：

```json
"exampleParameter": {
    "type": "int",
    "minValue": "0",
    "maxValue": "10"
},
```

相反，请将值作为数字提供。 下面的示例**通过**此测试：

```json
"exampleParameter": {
    "type": "int",
    "minValue": 0,
    "maxValue": 10
},
```

如果提供了最小值或最大值，则还会收到此警告。

## <a name="artifacts-parameter-defined-correctly"></a>已正确定义项目参数

Test name：**伪像-参数**

当包含和的参数 `_artifactsLocation` 时 `_artifactsLocationSasToken` ，请使用正确的默认值和类型。 若要通过此测试，必须满足以下条件：

* 如果提供一个参数，则必须提供另一个参数
* `_artifactsLocation`必须是**字符串**
* `_artifactsLocation`主模板中必须有一个默认值
* `_artifactsLocation`嵌套模板中不能有默认值 
* `_artifactsLocation`必须具有 `"[deployment().properties.templateLink.uri]"` 或原始存储库 URL 的默认值
* `_artifactsLocationSasToken`必须是**secureString**
* `_artifactsLocationSasToken`只能为其默认值提供空字符串
* `_artifactsLocationSasToken`嵌套模板中不能有默认值 

## <a name="declared-variables-must-be-used"></a>必须使用已声明的变量

测试名称：**必须引用变量**

若要减少模板中的混乱，请删除已定义但未使用的任何变量。 此测试将查找未在模板中任何位置使用的任何变量。

## <a name="dynamic-variable-should-not-use-concat"></a>动态变量不应使用 concat

测试名称：**动态变量引用不应使用 Concat**

有时，您需要基于另一个变量或参数的值来动态构造变量。 设置值时，请勿使用[concat](template-functions-string.md#concat)函数。 请改用包含可用选项的对象，并在部署过程中动态获取对象中的一个属性。

下面的示例**通过**此测试。 **CurrentImage**变量是在部署过程中动态设置的。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "osType": {
          "type": "string",
          "allowedValues": [
              "Windows",
              "Linux"
          ]
      }
  },
  "variables": {
    "imageOS": {
        "Windows": {
            "image": "Windows Image"
        },
        "Linux": {
            "image": "Linux Image"
        }
    },
    "currentImage": "[variables('imageOS')[parameters('osType')].image]"
  },
  "resources": [],
  "outputs": {
      "result": {
          "type": "string",
          "value": "[variables('currentImage')]"
      }
  }
}
```

## <a name="use-recent-api-version"></a>使用最新的 API 版本

测试名称：**应为最新的 apiVersions**

每个资源的 API 版本应使用最新版本。 测试将根据可用于该资源类型的版本评估你使用的版本。

## <a name="use-hardcoded-api-version"></a>使用硬编码 API 版本

测试名称：**不允许提供程序 apiVersions**

资源类型的 API 版本确定哪些属性可用。 在模板中提供硬编码的 API 版本。 不要检索在部署过程中确定的 API 版本。 您不知道哪些属性可用。

下面的示例将**无法**进行此测试。

```json
"resources": [
    {
        "type": "Microsoft.Compute/virtualMachines",
        "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
        ...
    }
]
```

下面的示例**通过**此测试。

```json
"resources": [
    {
       "type": "Microsoft.Compute/virtualMachines",
       "apiVersion": "2019-12-01",
       ...
    }
]
```

## <a name="properties-cant-be-empty"></a>属性不能为空

测试名称：**模板不应包含空格**

不要将属性硬编码为空值。 空值包括 null 和空字符串、对象或数组。 如果已将属性设置为空值，请从模板中删除该属性。 但是，可以在部署过程中将属性设置为空值，例如通过参数。

## <a name="use-resource-id-functions"></a>使用资源 ID 函数

测试名称： **Id 应派生自 resourceid**

指定资源 ID 时，请使用资源 ID 函数之一。 允许的函数包括：

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)

不要使用 concat 函数创建资源 ID。 下面的示例将**无法**进行此测试。

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

下一个示例**通过**此测试。

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>ResourceId 函数具有正确的参数

测试名称： **resourceid 不应包含**

生成资源 Id 时，请不要为可选参数使用不必要的函数。 默认情况下， [resourceId](template-functions-resource.md#resourceid)函数使用当前订阅和资源组。 不需要提供这些值。  

下面的示例将**无法**进行此测试，因为你不需要提供当前订阅 ID 和资源组名称。

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

下一个示例**通过**此测试。

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

此测试适用于：

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [reference](template-functions-resource.md#reference)
* [list*](template-functions-resource.md#list)

对于 `reference` 和 `list*` ，当你使用构造资源 ID 时，测试将**失败** `concat` 。

## <a name="dependson-cant-be-conditional"></a>dependsOn 不能为条件

测试名称： **DependsOn 不得为条件**

设置部署依赖项时，请不要使用[if](template-functions-logical.md#if)函数测试条件。 如果一个资源依赖于有[条件地部署](conditional-resource-deployment.md)的资源，请设置依赖关系，就像对任何资源都一样。 条件资源未部署时，Azure 资源管理器会自动将其从所需依赖项中删除。

下面的示例将**无法**进行此测试。

```json
"dependsOn": [
    "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

下一个示例**通过**此测试。

```json
"dependsOn": [
    "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>嵌套或链接的部署不能使用调试

测试名称：**部署资源不得为调试**

使用 " **Microsoft 资源/部署**" 资源类型定义[嵌套或链接的模板](linked-templates.md)时，可以为该模板启用调试。 当你需要测试该模板时，如果你已准备好在生产环境中使用该模板，则可以进行调试。

## <a name="admin-user-names-cant-be-literal-value"></a>管理员用户名不能是文本值

测试名称： **AdminUsername 不应为文本**

设置管理员用户名时，请不要使用文本值。

下面的示例未**通过**此测试：

```json
"osProfile":  {
    "adminUserName":  "myAdmin"
},
```

相反，请使用参数。 下面的示例**通过**此测试：

```json
"osProfile": {
    "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>使用最新 VM 映像

测试名称： **VM 映像应使用最新版本**

如果模板包含带有映像的虚拟机，请确保它使用的是最新版本的映像。

## <a name="use-stable-vm-images"></a>使用稳定 VM 映像

测试名称：**虚拟机-不应作为预览**

虚拟机不应使用预览映像。

下面的示例将**无法**进行此测试。

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest-preview"
}
```

下面的示例**通过**此测试。

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
},
```

## <a name="dont-use-managedidentity-extension"></a>请勿使用对 microsoft.managedidentity 扩展

测试名称：**不能使用 ManagedIdentityExtension**

不要将对 microsoft.managedidentity 扩展应用于虚拟机。 有关详细信息，请参阅[如何停止使用虚拟机托管标识扩展和开始使用 Azure 实例元数据服务](../../active-directory/managed-identities-azure-resources/howto-migrate-vm-extension.md)。

## <a name="outputs-cant-include-secrets"></a>输出不能包含机密

测试名称：**输出不得包含机密**

不要在 "输出" 部分包含可能公开机密的任何值。 模板的输出存储在部署历史记录中，因此恶意用户可能会发现该信息。

下面的示例**未能通过**测试，因为它包含一个输出值中的安全参数。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "secureParam": {
            "type": "securestring"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "string",
            "value": "[concat('this is the value ', parameters('secureParam'))]"
        }
    }
}
```

下面的示例**失败**，因为它在输出中使用[list *](template-functions-resource.md#list)函数。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "object",
            "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2019-06-01')]"
        }
    }
}
```

## <a name="next-steps"></a>后续步骤

若要了解如何运行测试工具包，请参阅[使用 ARM 模板测试工具包](test-toolkit.md)。