---
title: 创建参数文件
description: 创建用于在 Azure 资源管理器模板部署过程中传入值的参数文件
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 2b6d942b21594fa608127bb8f403e72295671005
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89276637"
---
# <a name="create-resource-manager-parameter-file"></a>创建资源管理器参数文件

你可能会发现，与在脚本中以内联值的形式传递参数相比，使用包含参数值的 JSON 文件更为容易。 本文介绍如何创建参数文件。

## <a name="parameter-file"></a>参数文件

参数文件采用以下格式：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

请注意，参数值以纯文本形式存储在参数文件中。 此方法适用于非敏感值，例如，为资源指定 SKU。 它不适用于敏感值（例如密码）。 如果需要将敏感值作为参数传递，请将该值存储在 Key Vault 中，并在参数文件中引用该 Key Vault。 在部署过程中会安全地检索敏感值。

以下参数文件包含一个纯文本值和一个存储在 Key Vault 中的值。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

有关使用 Key Vault 中的值的详细信息，请参阅[在部署过程中使用 Azure Key Vault 传递安全参数值](key-vault-parameter.md)。

## <a name="define-parameter-values"></a>定义参数值

若要确定如何定义参数值，请打开要部署的模板。 查看该模板的参数节。 以下示例显示了模板中的参数。

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
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
    },
    "storageAccountType": {
    }
  }
}
```

注意参数的类型。 参数文件中的值的类型必须相同。 对于此模板，可将两个参数都作为字符串提供。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

接下来查看默认值。 如果参数具有默认值，则你可以提供一个值，但不一定要这样做。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

最后，查看允许的值和任何限制（例如最大长度）。 这些限制告知可为参数提供的值的范围。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

参数文件只能包含在模板中定义的参数的值。 如果参数文件包含与模板中的参数不匹配的额外参数，则会收到错误。

## <a name="parameter-type-formats"></a>参数类型格式

以下示例演示不同参数类型的格式。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

## <a name="deploy-template-with-parameter-file"></a>使用参数文件部署模板

若要通过 Azure CLI 传递本地参数文件，请使用 @ 和参数文件的名称。

```azurecli
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

有关详细信息，请参阅[使用 ARM 模板和 Azure CLI 部署资源](./deploy-cli.md#parameters)。

若要通过 Azure PowerShell 传递本地参数文件，请使用 `TemplateParameterFile` 参数。

```azurepowershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

有关详细信息，请参阅[使用 ARM 模板和 Azure PowerShell 部署资源](./deploy-powershell.md#pass-parameter-values)

> [!NOTE]
> 不能在门户中将参数文件与自定义模板边栏选项卡一起使用。

## <a name="file-name"></a>文件名

参数文件的一般命名约定是将 **.parameters** 添加到模板名称。 例如，如果模板名为 **azuredeploy.json**，则参数文件名为 **azuredeploy.parameters.json**。 此命名约定可帮助你查看模板与参数之间的连接。

若要部署到不同的环境，请创建多个参数文件。 为参数文件命名时，请添加一种识别其用途的方式。 例如，使用 **azuredeploy.parameters-dev.json** 和 **azuredeploy.parameters-prod.json**

## <a name="parameter-precedence"></a>参数优先级

可以在同一部署操作中使用内联参数和本地参数文件。 例如，可以在本地参数文件中指定某些值，并在部署期间添加其他内联值。 如果同时为本地参数文件中的参数和内联参数提供值，则内联值优先。

可以通过提供文件的 URI 来使用外部参数文件。 使用外部参数文件时，不能传递是内联值的或来自本地文件的其他值。 会忽略所有内联参数。 提供外部文件中的所有参数值。

## <a name="parameter-name-conflicts"></a>参数名冲突

如果模板包括的一个参数与 PowerShell 命令中的某个参数同名，PowerShell 使用后缀 FromTemplate 显示模板的参数。 例如，模板中名为 **ResourceGroupName** 的参数与 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) cmdlet 中的 **ResourceGroupName** 参数冲突。 系统会提示你提供 **ResourceGroupNameFromTemplate** 的值。 可以使用未用于部署命令的参数名称来避免这种混淆。


## <a name="next-steps"></a>后续步骤

- 若要了解如何在模板中定义参数，请参阅 [Azure 资源管理器模板中的参数](template-parameters.md)。
- 有关使用 Key Vault 中的值的详细信息，请参阅[在部署过程中使用 Azure Key Vault 传递安全参数值](key-vault-parameter.md)。
- 有关参数的详细信息，请参阅 [Azure 资源管理器模板中的参数](template-parameters.md)。
