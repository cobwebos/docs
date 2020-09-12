---
title: 测试工具包的测试用例
description: 介绍由 ARM 模板测试工具包运行的测试。
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: dda8e92c17029126e7f473a6aee03acfc970e04b
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378111"
---
# <a name="default-test-cases-for-arm-template-test-toolkit"></a>ARM 模板测试工具包的默认测试用例

本文介绍[模板测试工具包](test-toolkit.md)运行的默认测试。 它提供通过测试或未通过测试的示例。 它包括每个测试的名称。

## <a name="use-correct-schema"></a>使用正确的架构

测试名称：DeploymentTemplate 架构正确

在模板中，必须指定有效的架构值。

下面的示例通过了此测试。

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

## <a name="parameters-must-exist"></a>必须存在参数

测试名称：参数属性必须存在

模板应具有参数元素。 参数对于使模板在不同环境中可重用非常重要。 在模板中，为部署到不同环境时会发生更改的值添加参数。

下面的示例通过了此测试：

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

测试名称：必须引用参数

为了尽量避免混淆，请删除模板中已定义但未使用的任何参数。 此测试将查找未在模板中使用的任何参数。 消除未使用的参数还可以更轻松地部署模板，因为无需提供不必要的值。

## <a name="secure-parameters-cant-have-hardcoded-default"></a>安全参数不能包含硬编码的默认值

测试名称：安全字符串参数不能有默认值

不要为模板中的安全参数提供硬编码的默认值。 空字符串可以作为默认值。

在包含敏感值（如密码）的参数上使用类型 SecureString 或 SecureObject 。 当参数使用安全类型时，不会将参数的值记录或存储在部署历史记录中。 此操作可防止恶意用户发现敏感值。

但是，当你提供默认值时，可访问该模板或部署历史记录的任何人都可以发现该值。

下面的示例未通过此测试：

```json
"parameters": {
    "adminPassword": {
        "defaultValue": "HardcodedPassword",
        "type": "SecureString"
    }
}
```

下一个示例通过了此测试：

```json
"parameters": {
    "adminPassword": {
        "type": "SecureString"
    }
}
```

## <a name="environment-urls-cant-be-hardcoded"></a>环境 Url 不能硬编码

测试名称： **DeploymentTemplate 不能包含硬编码 Uri**

不要在模板中硬编码环境 Url。 请改用 [环境函数](template-functions-deployment.md#environment) 在部署期间动态获取这些 url。 有关被阻止的 URL 主机的列表，请参阅该 [测试用例](https://github.com/Azure/arm-ttk/blob/master/arm-ttk/testcases/deploymentTemplate/DeploymentTemplate-Must-Not-Contain-Hardcoded-Uri.test.ps1)。

下面的示例将 **无法** 进行此测试，因为 URL 是硬编码的。

```json
"variables":{
    "AzureURL":"https://management.azure.com"
}
```

与[concat](template-functions-string.md#concat)或[uri](template-functions-string.md#uri)一起使用时，该测试也**会失败**。

```json
"variables":{
    "AzureSchemaURL1": "[concat('https://','gallery.azure.com')]",
    "AzureSchemaURL2": "[uri('gallery.azure.com','test')]"
}
```

下面的示例通过了此测试。

```json
"variables": {
    "AzureSchemaURL": "[environment().gallery]"
},
```

## <a name="location-uses-parameter"></a>位置使用参数

测试名称：不应硬编码位置

模板用户可访问的模板区域可能有限。 将资源位置设置为 `"[resourceGroup().location]"` 时，可能会在其他用户无法访问的区域中创建资源组。 这会导致这些用户无法使用模板。

定义每个资源的位置时，请使用默认为资源组位置的参数。 提供此参数后，用户可以在方便的情况下使用默认值，也可以指定其他位置。

下面的示例未通过此测试，因为资源上的位置设置为 `resourceGroup().location`。

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

下一个示例使用位置参数，但未通过此测试，因为位置参数默认为硬编码的位置。

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

应创建一个默认为资源组位置但允许用户提供不同值的参数。 下面的示例通过了此测试。

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

测试名称：资源应具有位置

应将资源的位置设置为[模板表达式](template-expressions.md)或 `global`。 模板表达式通常使用上一测试中所述的位置参数。

下面的示例将 **无法** 进行此测试，因为位置不是表达式或 `global` 。

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

下面的示例通过了此测试。

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

下一个示例也通过了此测试。

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

测试名称：VM 大小应为参数

不要硬编码虚拟机大小。 提供一个参数，使模板的用户可以修改部署的虚拟机的大小。

下面的示例未通过此测试。

```json
"hardwareProfile": {
    "vmSize": "Standard_D2_v3"
}
```

相反，提供参数。

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

测试名称：最小值和最大值为数字

如果为参数定义最小值和最大值，请将其指定为数字。

下面的示例未通过此测试：

```json
"exampleParameter": {
    "type": "int",
    "minValue": "0",
    "maxValue": "10"
},
```

应以数字形式提供值。 下面的示例通过了此测试：

```json
"exampleParameter": {
    "type": "int",
    "minValue": 0,
    "maxValue": 10
},
```

如果只提供最小值或最大值中的一个但未提供另一个值，也会收到此警告。

## <a name="artifacts-parameter-defined-correctly"></a>项目参数定义正确

Test name： **伪像参数**

当包含 `_artifactsLocation` 和 `_artifactsLocationSasToken` 的参数时，请使用正确的默认值和类型。 必须满足以下条件才能通过此测试：

* 如果提供一个参数，就必须提供另一个参数
* `_artifactsLocation` 必须是字符串
* `_artifactsLocation` 在主模板中必须具有默认值
* `_artifactsLocation` 在嵌套模板中不能有默认值 
* `_artifactsLocation` 必须使用 `"[deployment().properties.templateLink.uri]"` 或原始存储库 URL 作为其默认值
* `_artifactsLocationSasToken` 必须是 secureString
* `_artifactsLocationSasToken` 的默认值只能为空字符串
* `_artifactsLocationSasToken` 在嵌套模板中不能有默认值 

## <a name="declared-variables-must-be-used"></a>必须使用已声明的变量

测试名称：必须引用变量

为了尽量避免混淆，请删除已定义但未使用的任何变量。 此测试将查找未在模板中使用的任何变量。

## <a name="dynamic-variable-should-not-use-concat"></a>动态变量不应使用 concat

测试名称：动态变量引用不应使用 Concat

有时，你需要基于另一个变量或参数的值来动态构造变量。 设置值时，不要使用 [concat](template-functions-string.md#concat) 函数。 改用包含可用选项的对象，并在部署过程中动态获取对象中的一个属性。

下面的示例通过了此测试。 在部署过程中会动态设置 currentImage 变量。

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

测试名称：apiVersions 应为最新版本

每个资源的 API 版本应使用最新版本。 测试将根据可用于该资源类型的版本评估你使用的版本。

## <a name="use-hardcoded-api-version"></a>使用硬编码 API 版本

测试名称：不允许使用提供程序 apiVersions

资源类型的 API 版本决定了哪些属性可用。 在模板中提供硬编码的 API 版本。 不要检索在部署过程中确定的 API 版本。 你不知道哪些属性可用。

下面的示例未通过此测试。

```json
"resources": [
    {
        "type": "Microsoft.Compute/virtualMachines",
        "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
        ...
    }
]
```

下面的示例通过了此测试。

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

测试名称：模板不应包含空白

不要将属性硬编码为空值。 空值包括 Null 和空字符串、对象或数组。 如果已将属性设置为空值，请从模板中删除该属性。 但是，可以在部署过程中将属性设置为空值，例如通过参数设置。

## <a name="use-resource-id-functions"></a>使用资源 ID 函数

测试名称：ID 应派生自 ResourceID

指定资源 ID 时，请使用资源 ID 函数之一。 允许使用的函数包括：

* [resourceId](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)

不要使用 concat 函数创建资源 ID。 下面的示例未通过此测试。

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

下一个示例通过此测试。

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>ResourceId 函数具有正确的参数

测试名称：ResourceId 不应包含

生成资源 ID 时，不要为可选参数使用不必要的函数。 默认情况下，[resourceId](template-functions-resource.md#resourceid) 函数使用当前订阅和资源组。 不需要提供这些值。  

下面的示例未通过此测试，因为不需要提供当前订阅 ID 和资源组名称。

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

下一个示例通过此测试。

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

对于 `reference` 和 `list*`，当使用 `concat` 构造资源 ID 时，无法通过测试。

## <a name="dependson-best-practices"></a>dependsOn 最佳实践

测试名称： **DependsOn 最佳实践**

设置部署依赖项时，不要使用 [if](template-functions-logical.md#if) 函数来测试某个条件。 如果一个资源依赖于[基于条件部署](conditional-resource-deployment.md)的资源，设置依赖项时应与任何其他资源一样。 条件资源未部署时，Azure 资源管理器会自动将其从所需依赖项中删除。

下面的示例未通过此测试。

```json
"dependsOn": [
    "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

下一个示例通过此测试。

```json
"dependsOn": [
    "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>嵌套或链接的部署不能使用调试

测试名称：部署资源不能使用调试

使用 Microsoft.Resources/deployments 资源类型定义[嵌套或链接的模板](linked-templates.md)时，可以为该模板启用调试。 当你需要测试该模板时，可以进行调试，但如果你已准备好在生产中使用该模板，则应该关闭调试。

## <a name="admin-user-names-cant-be-literal-value"></a>管理员用户名不能是文本值

测试名称：adminUsername 不应为文本

设置管理员用户名时，不要使用文本值。

下面的示例未通过此测试：

```json
"osProfile":  {
    "adminUserName":  "myAdmin"
},
```

相反，请使用参数。 下面的示例通过了此测试：

```json
"osProfile": {
    "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>使用最新的 VM 映像

测试名称：VM 映像应使用最新版本

如果模板包含带有映像的虚拟机，请确保它使用的是最新版本的映像。

## <a name="use-stable-vm-images"></a>使用稳定的 VM 映像

测试名称： **虚拟机不应为预览版**

虚拟机不应使用预览映像。

下面的示例未通过此测试。

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest-preview"
}
```

下面的示例通过了此测试。

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
},
```

## <a name="dont-use-managedidentity-extension"></a>不要使用 ManagedIdentity 扩展

测试名称：不能使用 ManagedIdentityExtension

不要将 ManagedIdentity 扩展应用于虚拟机。 有关详细信息，请参阅[如何停止使用虚拟机托管标识扩展并开始使用 Azure 实例元数据服务](../../active-directory/managed-identities-azure-resources/howto-migrate-vm-extension.md)。

## <a name="outputs-cant-include-secrets"></a>输出不能包含机密

测试名称：输出不能包含机密

不要在输出部分包含任何可能公开机密的值。 模板的输出存储在部署历史记录中，因此恶意用户可能会发现该信息。

下面的示例未通过此测试，因为它在输出值中包含了安全参数。

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

下面的示例未通过此测试，因为它在输出中使用了 [list*](template-functions-resource.md#list) 函数。

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