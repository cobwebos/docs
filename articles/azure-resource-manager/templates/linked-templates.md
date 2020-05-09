---
title: 用于部署的链接模板
description: 介绍如何使用 Azure 资源管理器模板中的链接模板创建一个模块化的模板的解决方案。 演示如何传递参数值、指定参数文件和动态创建的 URL。
ms.topic: conceptual
ms.date: 04/29/2020
ms.openlocfilehash: f71d8cc62daf68b158bed444da1446e016194b56
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82609300"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>部署 Azure 资源时使用链接模版和嵌套模版

若要部署复杂的解决方案，可以将模板分解为许多相关模板，然后通过主模板将它们一起部署。 相关模板可以是嵌入在主模板内的单独文件或模板语法。 本文使用术语 "**链接模板**" 引用通过主模板的链接引用的单独模板文件。 它使用术语**嵌套模板**指代主模板内嵌套的模板语法。

对于中小型解决方案，单个模板更易于理解和维护。 可以查看单个文件中的所有资源和值。 对于高级方案，使用链接模板可将解决方案分解为目标组件。 可以轻松地将这些模板重复用于其他方案。

如需教程，请参阅[教程：创建链接的 Azure 资源管理器模板](template-tutorial-create-linked-templates.md)。

> [!NOTE]
> 对于链接模板或嵌套模板，只能使用[增量](deployment-modes.md)部署模式。
>

## <a name="nested-template"></a>嵌套模板

若要嵌套模板，请将[部署资源](/azure/templates/microsoft.resources/deployments)添加到主模板。 在 **template** 属性中，指定模板语法。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "name": "nestedTemplate1",
      "apiVersion": "2019-10-01",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "Incremental",
        "template": {
          <nested-template-syntax>
        }
      }
    }
  ],
  "outputs": {
  }
}
```

以下示例通过嵌套模板部署一个存储帐户。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "nestedTemplate1",
      "apiVersion": "2019-10-01",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-04-01",
              "name": "[parameters('storageAccountName')]",
              "location": "West US",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
  }
}
```

### <a name="expression-evaluation-scope-in-nested-templates"></a>嵌套模板中的表达式计算范围

使用嵌套模板时，可以指定是在父模板的作用域内还是在嵌套模板的作用域内计算模板表达式。 作用域决定了如何对参数、变量和函数（例如 [resourceGroup](template-functions-resource.md#resourcegroup) 和 [subscription](template-functions-resource.md#subscription)）进行求解。

可以通过 `expressionEvaluationOptions` 属性设置作用域。 默认情况下，`expressionEvaluationOptions` 属性设置为 `outer`，这意味着它使用父模板作用域。 将值设置为`inner` ，使表达式在嵌套模板范围内进行计算。

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2019-10-01",
  "name": "nestedTemplate1",
  "properties": {
  "expressionEvaluationOptions": {
    "scope": "inner"
  },
  ...
```

以下模板演示了如何根据作用域对模板表达式进行求解。 它包含一个名为 `exampleVar` 的变量，父模板和嵌套模板中都定义了此变量。 它返回此变量的值。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "exampleVar": "from parent template"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "variables": {
            "exampleVar": "from nested template"
          },
          "resources": [
          ],
          "outputs": {
            "testVar": {
              "type": "string",
              "value": "[variables('exampleVar')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
    "messageFromLinkedTemplate": {
      "type": "string",
      "value": "[reference('nestedTemplate1').outputs.testVar.value]"
    }
  }
}
```

的值会`exampleVar`根据中`scope` `expressionEvaluationOptions`属性的值发生变化。 下表显示了这两个作用域的结果。

| `expressionEvaluationOptions` `scope` | 输出 |
| ----- | ------ |
| 内部 | from nested template |
| outer（或默认值） | from parent template |

下面的示例部署一个 SQL Server，并检索要用于密码的密钥保管库机密。 作用域设置为， `inner`因为它会动态创建密钥保管库 ID （ `adminPassword.reference.keyVault`请参阅外部模板`parameters`中），并将其作为参数传递到嵌套模板。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where the resources will be deployed."
      }
    },
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the keyvault that contains the secret."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "The name of the secret."
      }
    },
    "vaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group that contains the keyvault."
      }
    },
    "vaultSubscription": {
      "type": "string",
      "defaultValue": "[subscription().subscriptionId]",
      "metadata": {
        "description": "The name of the subscription that contains the keyvault."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

> [!NOTE]
>
> 当作用域设置为 `outer` 时，对于已在嵌套模板中部署的资源，无法在嵌套模板的 outputs 节中使用 `reference` 函数。 若要返回嵌套模板中已部署资源的值，请使用`inner` "作用域" 或 "将嵌套模板转换为链接模板"。

## <a name="linked-template"></a>链接模板

若要链接模板，请将[部署资源](/azure/templates/microsoft.resources/deployments)添加到主模板。 在 **templateLink** 属性中，指定要包括的模板的 URI。 以下示例链接到部署新存储帐户的模板。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

引用链接模板时，的值`uri`不能是本地文件，也不能是只能在本地网络上使用的文件。 必须提供可下载为**http**或**https**的 URI 值。 

> [!NOTE]
>
> 您可以使用最终解析为使用**http**或**https**的参数（例如，使用如下所示的`_artifactsLocation`参数）来引用模板：`"uri": "[concat(parameters('_artifactsLocation'), '/shared/os-disk-parts-md.json', parameters('_artifactsLocationSasToken'))]",`



资源管理器必须能够访问该模板。 一种做法是将链接模板放入存储帐户，并对该项使用 URI。

### <a name="parameters-for-linked-template"></a>链接模板的参数

可以在外部文件中或以内联方式为链接模板提供参数。 提供外部参数文件时，请使用 **parametersLink** 属性：

```json
"resources": [
  {
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2019-10-01",
  "name": "linkedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
      "contentVersion":"1.0.0.0"
    },
    "parametersLink": {
      "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
      "contentVersion":"1.0.0.0"
    }
  }
  }
]
```

若要以内联方式传递参数值，请使用 **parameters** 属性。

```json
"resources": [
  {
   "type": "Microsoft.Resources/deployments",
   "apiVersion": "2019-10-01",
   "name": "linkedTemplate",
   "properties": {
     "mode": "Incremental",
     "templateLink": {
      "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
      "contentVersion":"1.0.0.0"
     },
     "parameters": {
      "StorageAccountName":{"value": "[parameters('StorageAccountName')]"}
    }
   }
  }
]
```

不能同时使用内联参数和指向参数文件的链接。 同时指定 `parametersLink` 和 `parameters` 时，部署将失败，并出现错误。

## <a name="contentversion"></a>contentVersion

无需为`templateLink`或`parametersLink`属性提供`contentVersion`属性。 如果未提供`contentVersion`，则部署当前版本的模板。 如果提供内容版本值，它必须与链接的模板中的版本相匹配；否则，部署失败并产生错误。

## <a name="using-variables-to-link-templates"></a>使用变量来链接模板

前面的示例演示了用于模板链接的硬编码 URL 值。 此方法可能适用于简单的模板，但它不适用于大型模块化模板。 相反，可以创建一个存储主模板的基 URL 的静态变量，并从基 URL 动态创建用于链接模板的 URL。 此方法的好处是可以轻松地移动或派生模板，因为只需更改主模板中的静态变量。 主模板会在整个分解后的模板中传递正确的 URI。

以下示例演示如何使用基 URL 来创建两个用于链接模板的 URL（**sharedTemplateUrl** 和 **vmTemplate**）。

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[uri(variables('templateBaseUrl'), 'shared-resources.json')]",
  "vmTemplateUrl": "[uri(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

还可以使用 [deployment()](template-functions-deployment.md#deployment) 获取当前模板的基 URL，并使用该 URL 来获取同一位置其他模板的 URL。 如果模板位置发生变化或者想要避免对模板文件中的 URL 进行硬编码，则此方法非常有用。 仅当链接到带有 URL 的远程模板时，才会返回 templateLink 属性。 如果使用的是本地模板，该属性不可用。

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

最终，将在`uri` `templateLink`属性的属性中使用该变量。

```json
"templateLink": {
 "uri": "[variables('sharedTemplateUrl')]",
 "contentVersion":"1.0.0.0"
}
```

## <a name="using-copy"></a>使用副本

若要使用嵌套的模板创建资源的多个实例，请在 **Microsoft.Resources/deployments** 资源的级别添加副本元素。 或者，如果作用域是 inner，则可以在嵌套模板中添加副本。

以下示例模板展示了如何将副本与嵌套的模板配合使用。

```json
"resources": [
  {
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2019-10-01",
  "name": "[concat('nestedTemplate', copyIndex())]",
  // yes, copy works here
  "copy":{
    "name": "storagecopy",
    "count": 2
  },
  "properties": {
    "mode": "Incremental",
    "expressionEvaluationOptions": {
    "scope": "inner"
    },
    "template": {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
      {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(variables('storageName'), copyIndex())]",
      "location": "West US",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "StorageV2"
      // Copy works here when scope is inner
      // But, when scope is default or outer, you get an error
      //"copy":{
      //  "name": "storagecopy",
      //  "count": 2
      //}
      }
    ]
    }
  }
  }
]
```

## <a name="get-values-from-linked-template"></a>从链接模板中获取值

若要从链接模板中获取输出值，请使用如下所示的语法检索属性值：`"[reference('deploymentName').outputs.propertyName.value]"`。

从链接模板获取输出属性时，属性名称不能包含短划线。

以下示例演示如何引用链接模板和检索输出值。 链接模板返回一条简单的消息。  首先，链接的模板：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [],
  "outputs": {
    "greetingMessage": {
      "value": "Hello World",
      "type" : "string"
    }
  }
}
```

主模板部署链接模板并获取返回值。 请注意，该模板按名称引用部署资源，并使用链接模板返回的属性的名称。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[uri(deployment().properties.templateLink.uri, 'helloworld.json')]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
    "messageFromLinkedTemplate": {
      "type": "string",
      "value": "[reference('linkedTemplate').outputs.greetingMessage.value]"
    }
  }
}
```

与其他资源类型一样，可以设置链接模板和其他资源之间的依赖关系。 当其他资源需要链接模板的输出值时，请确保在部署这些资源之前部署链接模板。 或者，当链接模板依赖于其他资源时，请确保在部署链接模板之前部署其他资源。

下面的示例演示了一个模板，该模板部署一个公共 IP 地址并返回该公共 IP 的 Azure 资源的资源 ID：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "publicIPAddresses_name": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2018-11-01",
      "name": "[parameters('publicIPAddresses_name')]",
      "location": "eastus",
      "properties": {
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 4
      },
      "dependsOn": []
    }
  ],
  "outputs": {
    "resourceID": {
      "type": "string",
      "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
    }
  }
}
```

若要在部署负载平衡器时使用上述模板中的公共 IP 地址，请链接到该模板，并声明对`Microsoft.Resources/deployments`资源的依赖项。 负载均衡器上的公共 IP 地址设置为链接模板的输出值。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "loadBalancers_name": {
      "defaultValue": "mylb",
      "type": "string"
    },
    "publicIPAddresses_name": {
      "defaultValue": "myip",
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/loadBalancers",
      "apiVersion": "2018-11-01",
      "name": "[parameters('loadBalancers_name')]",
      "location": "eastus",
      "properties": {
        "frontendIPConfigurations": [
          {
            "name": "LoadBalancerFrontEnd",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                // this is where the output value from linkedTemplate is used
                "id": "[reference('linkedTemplate').outputs.resourceID.value]"
              }
            }
          }
        ],
        "backendAddressPools": [],
        "loadBalancingRules": [],
        "probes": [],
        "inboundNatRules": [],
        "outboundNatRules": [],
        "inboundNatPools": []
      },
      // This is where the dependency is declared
      "dependsOn": [
        "linkedTemplate"
      ]
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[uri(deployment().properties.templateLink.uri, 'publicip.json')]",
          "contentVersion": "1.0.0.0"
        },
        "parameters":{
          "publicIPAddresses_name":{"value": "[parameters('publicIPAddresses_name')]"}
        }
      }
    }
  ]
}
```

## <a name="deployment-history"></a>部署历史记录

资源管理器将每个模板作为部署历史记录中的单独部署进行处理。 包含三个链接模板或嵌套模板的主模板在部署历史记录中显示为：

![部署历史记录](./media/linked-templates/deployment-history.png)

部署后，可以使用历史记录中这些不同的条目来检索输出值。 以下模板创建一个公共 IP 地址并输出该 IP 地址：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "publicIPAddresses_name": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2018-11-01",
      "name": "[parameters('publicIPAddresses_name')]",
      "location": "southcentralus",
      "properties": {
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Static",
        "idleTimeoutInMinutes": 4,
        "dnsSettings": {
          "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
        }
      },
      "dependsOn": []
    }
  ],
  "outputs": {
    "returnedIPAddress": {
      "type": "string",
      "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
    }
  }
}
```

以下模板链接到前面所述的模板。 它创建三个公共 IP 地址。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-10-01",
      "name": "[concat('linkedTemplate', copyIndex())]",
      "copy": {
        "count": 3,
        "name": "ip-loop"
      },
      "properties": {
        "mode": "Incremental",
        "templateLink": {
        "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
        "contentVersion": "1.0.0.0"
        },
        "parameters":{
          "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
        }
      }
    }
  ]
}
```

部署后，可使用以下 PowerShell 脚本检索输出值：

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
  $name = 'linkedTemplate' + $i;
  $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
  Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

还可以使用 Bash shell 中的 Azure CLI 脚本：

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
  name="linkedTemplate$i";
  deployment=$(az deployment group show -g examplegroup -n $name);
  ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
  echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>保护外部模板

尽管链接模板必须可从外部使用，但它无需向公众正式发布。 可以将模板添加到只有存储帐户所有者可以访问的专用存储帐户。 然后，在部署期间创建共享访问签名 (SAS) 令牌，用于启用访问。 将该 SAS 令牌添加到链接模板的 URI。 即使令牌作为安全字符串传入，链接模板的 URI（包括 SAS 令牌）也将记录在部署操作中。 若要限制公开，请设置令牌的到期时间。

也可将参数文件限制为通过 SAS 令牌进行访问。

目前，无法链接到位于 [Azure 存储防火墙](../../storage/common/storage-network-security.md)后面的存储帐户中的模板。

以下示例演示在链接到模板时如何传递 SAS 令牌：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  "containerSasToken": { "type": "securestring" }
  },
  "resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2019-10-01",
    "name": "linkedTemplate",
    "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
      "contentVersion": "1.0.0.0"
    }
    }
  }
  ],
  "outputs": {
  }
}
```

在 PowerShell 中，使用以下命令获取容器的令牌并部署模板。 注意，**containerSasToken** 参数是在模板中定义的。 它不是 **New-AzResourceGroupDeployment** 命令中的参数。

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

对于 Bash Shell 中的 Azure CLI，使用以下代码获取容器的令牌并部署模板：

```azurecli-interactive
#!/bin/bash

expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
  --resource-group ManageGroup \
  --name storagecontosotemplates \
  --query connectionString)
token=$(az storage container generate-sas \
  --name templates \
  --expiry $expiretime \
  --permissions r \
  --output tsv \
  --connection-string $connection)
url=$(az storage blob url \
  --container-name templates \
  --name parent.json \
  --output tsv \
  --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az deployment group create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>示例模板

以下示例演示了链接模板的常见用法。

|主模板  |链接模板 |说明  |
|---------|---------| ---------|
|[Hello World](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[链接模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | 从链接模板返回字符串。 |
|[使用公共 IP 地址的负载均衡器](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[链接模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |从链接模板返回公共 IP 地址，并在负载均衡器中设置该值。 |
|[多个 IP 地址](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [链接模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |在链接模板中创建多个公共 IP 地址。  |

## <a name="next-steps"></a>后续步骤

* 若要浏览教程，请参阅[教程：创建链接的 Azure 资源管理器模板](template-tutorial-create-linked-templates.md)。
* 若要了解如何为资源定义部署顺序，请参阅[在 Azure 资源管理器模板中定义依赖项](define-resource-dependency.md)。
* 若要了解如何定义一个资源而创建多个实例，请参阅[在 Azure 资源管理器中创建多个资源实例](copy-resources.md)。
* 有关在存储帐户中设置模板和生成 SAS 令牌的步骤，请参阅[使用 Resource Manager 模板和 Azure PowerShell 部署资源](deploy-powershell.md)或[使用 Resource Manager 模板和 Azure CLI 部署资源](deploy-cli.md)。
