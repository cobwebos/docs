---
title: 创建 Azure 资源管理器参数文件
description: 创建用于在 Azure 资源管理器模板部署过程中传入值的参数文件
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: tomfitz
ms.openlocfilehash: 4305213d272172cb89bfdd207b6c8106af3f4939
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983934"
---
# <a name="create-resource-manager-parameter-file"></a>创建资源管理器参数文件

与在脚本中以内联值的形式传递参数相比，可能会发现使用包含参数值的 JSON 文件更为容易。 本文介绍如何创建参数文件。

## <a name="parameter-file"></a>参数文件

参数文件采用以下格式：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "<first-parameter-name>": {
            "value": "<first-value>"
        },
        "<second-parameter-name>": {
            "value": "<second-value>"
        }
    }
}
```

请注意，参数值以纯文本形式存储在参数文件中。 此方法适用于不敏感的值，例如，为资源指定 SKU。 它不适用于敏感值（如密码）。 如果需要将敏感值作为参数传递，请将值存储在密钥保管库中，并在参数文件中引用该密钥保管库。 在部署过程中会安全地检索敏感值。

以下参数文件包括一个纯文本值和一个存储在密钥保管库中的值。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "<first-parameter-name>": {
            "value": "<first-value>"
        },
        "<second-parameter-name>": {
            "reference": {
                "keyVault": {
                    "id": "<resource-id-key-vault>"
                },
                "secretName": "<secret-name>"
            }
        }
    }
}
```

有关使用 key vault 中的值的详细信息，请参阅[在部署过程中使用 Azure Key Vault 传递安全参数值](resource-manager-keyvault-parameter.md)。

## <a name="define-parameter-values"></a>定义参数值

若要确定如何定义参数值，请打开要部署的模板。 查看模板的参数部分。 下面的示例显示模板中的参数。

```json
"parameters": {
    "storagePrefix": {
      "type": "string",
      "maxLength": 11
    },
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ]
    }
}
```

要注意的第一个详细信息是每个参数的名称。 参数文件中的值必须与名称匹配。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
        },
        "storageAccountType": {
        }
    }
}
```

请注意参数的类型。 参数文件中的值必须具有相同的类型。 对于此模板，可以将两个参数都作为字符串提供。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "value": ""
        },
        "storageAccountType": {
            "value": ""
        }
    }
}
```

接下来，查找默认值。 如果参数具有默认值，则可以提供一个值，但不一定要这样做。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "value": "" // This value must be provided.
        },
        "storageAccountType": {
            "value": "" // This value is optional. Template will use default value if not provided.
        }
    }
}
```

最后，查看允许的值和任何限制（如最大长度限制）。 它们告诉您可以为参数提供的值的范围。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "value": "storage"
        },
        "storageAccountType": {
            "value": "Standard_ZRS"
        }
    }
}
```

## <a name="parameter-type-formats"></a>参数类型格式

下面的示例演示不同参数类型的格式。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "exampleString": {
            "value": "test string"
        },
        "exampleInt": {
            "value": 4
        },
        "exampleBool": {
            "value": true
        },
        "exampleArray": {
            "value": [
                "value 1",
                "value 2"
            ]
        },
        "exampleObject": {
            "value": {
                "property1": "value1",
                "property2": "value2"
            }
        }
   }
}
```

## <a name="file-name"></a>文件名

命名参数文件的一般约定是将 **. parameters**添加到模板名称。 例如，如果模板的名称为**azuredeploy.json**，则参数文件名为**azuredeploy.json**。 此命名约定可帮助你了解模板和参数之间的连接。

若要部署到不同的环境，请创建多个参数文件。 命名参数文件时，添加标识其用途的方式。 例如，使用**azuredeploy.json-** **azuredeploy.json--** example. json


## <a name="parameter-precedence"></a>参数优先级

可以在同一部署操作中使用内联参数和本地参数文件。 例如，可以在本地参数文件中指定某些值，并在部署期间添加其他内联值。 如果同时为本地参数文件中的参数和内联参数提供值，则内联值优先。

但是，使用外部参数文件时，不能传递是内联值或来自本地文件的其他值。 将忽略所有内联参数。 提供外部文件中的所有参数值。

## <a name="parameter-name-conflicts"></a>参数名冲突

如果模板包括的一个参数与 PowerShell 命令中的某个参数同名，PowerShell 使用后缀 **FromTemplate** 显示模板的参数。 例如，模板中名为 **ResourceGroupName** 的参数与 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) cmdlet 中的 **ResourceGroupName** 参数冲突。 系统会提示你提供 **ResourceGroupNameFromTemplate** 的值。 你可以使用不用于部署命令的参数名来避免这种混乱。

## <a name="next-steps"></a>后续步骤

- 若要了解如何在模板中定义参数，请参阅[Azure 资源管理器模板中的参数](template-parameters.md)。
- 有关使用 key vault 中的值的详细信息，请参阅[在部署过程中使用 Azure Key Vault 传递安全参数值](resource-manager-keyvault-parameter.md)。
- 有关参数的详细信息，请参阅[Azure 资源管理器模板中的参数](template-parameters.md)。
