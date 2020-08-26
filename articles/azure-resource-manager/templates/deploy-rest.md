---
title: 使用 REST API 和模板部署资源
description: 使用 Azure 资源管理器和资源管理器 REST API 将资源部署到 Azure。 资源在 Resource Manager 模板中定义。
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 17ea7da3e0b581ed60d2db97d350a70d5250ef28
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079487"
---
# <a name="deploy-resources-with-arm-templates-and-resource-manager-rest-api"></a>使用 ARM 模板和资源管理器 REST API 部署资源

本文介绍如何将资源管理器 REST API 与 Azure 资源管理器模板 (ARM) 配合使用向 Azure 部署资源。

可以在请求正文中包含模板或链接到文件。 使用文件时，它可以是本地文件，也可以是通过 URI 提供的外部文件。 如果模板位于存储帐户中，可以限制对该模板的访问，并在部署过程中提供共享访问签名 (SAS) 令牌。

## <a name="deployment-scope"></a>部署范围

可将部署目标设定为管理组、Azure 订阅或资源组。 大多数情况下，我们会将部署目标设定为资源组。 使用管理组或订阅部署在指定范围内应用策略和角色分配。 还可以使用订阅部署创建资源组，并将资源部署到该资源组。 你将根据部署范围使用不同的命令。

* 若要部署到资源组，请使用[部署 - 创建](/rest/api/resources/deployments/createorupdate)。 请求将发送到：

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
  ```

* 若要部署到订阅，请使用[部署 - 在订阅范围内创建](/rest/api/resources/deployments/createorupdateatsubscriptionscope)。 请求发送到：

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
  ```

  有关订阅级部署的详细信息，请参阅[在订阅级别创建资源组和资源](deploy-to-subscription.md)。

* 若要部署到管理组，请使用[部署 - 在管理组范围内创建](/rest/api/resources/deployments/createorupdateatmanagementgroupscope)。 请求发送到：

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
  ```

  有关管理组级别部署的详细信息，请参阅[在管理组级别创建资源](deploy-to-management-group.md)。

* 若要部署到租户，请使用[部署 - 在租户范围内创建或更新](/rest/api/resources/deployments/createorupdateattenantscope)。 请求发送到：

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
  ```

  有关租户级别部署的详细信息，请参阅[在租户级别创建资源](deploy-to-tenant.md)。

本文中的示例使用资源组部署。

## <a name="deploy-with-the-rest-api"></a>使用 REST API 进行部署

1. 设置 [常见参数和标头](/rest/api/azure/)，包括身份验证令牌。

1. 如果目前没有资源组，请创建资源组。 提供订阅 ID、新资源组的名称，以及解决方案所需的位置。 有关详细信息，请参阅 [创建资源组](/rest/api/resources/resourcegroups/createorupdate)。

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2019-10-01
   ```

   使用如下所示请求正文：

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. 在部署模板之前，可以预览模板将对环境作出的更改。 使用[假设操作](template-deploy-what-if.md)验证模板是否进行了预期的更改。 假设操作还验证模板是否有错误。

1. 若要部署模板，请在请求 URI 中提供订阅 ID、资源组名称和部署名称。

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-10-01
   ```

   在请求正文中，提供指向模板和参数文件的链接。 有关参数文件的详细信息，请参阅[创建资源管理器参数文件](parameter-files.md)。

   请注意，**mode** 设置为 **Incremental**。 要运行完整部署，请将 **mode** 设置为 **Complete**。 使用完整模式时要小心，因为可能会无意中删除不在模板中的资源。

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental"
    }
   }
   ```

    如果你想要记录响应内容和/或请求内容，请 `debugSetting` 在请求中包括。

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    可以将存储帐户设置为使用共享访问签名 (SAS) 令牌。 有关详细信息，请参阅[使用共享访问签名委托访问权限](/rest/api/storageservices/delegating-access-with-a-shared-access-signature)。

    如果需要为参数（如密码）提供敏感值，请将该值添加到密钥保管库。 在部署过程中检索密钥保管库，如前面的示例所示。 有关详细信息，请参阅[在部署期间传递安全值](key-vault-parameter.md)。

1. 可以将模板和参数包含在请求正文中，而不是链接到模板和参数的文件。 以下示例显示了内联模板和参数的请求正文：

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
              "Standard_LRS",
              "Standard_GRS",
              "Standard_ZRS",
              "Premium_LRS"
            ],
            "metadata": {
              "description": "Storage Account type"
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "variables": {
          "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-02-01",
            "name": "[variables('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
              "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
          }
        ],
        "outputs": {
          "storageAccountName": {
            "type": "string",
            "value": "[variables('storageAccountName')]"
          }
        }
      },
      "parameters": {
        "location": {
          "value": "eastus2"
        }
      }
    }
   }
   ```

1. 要获取模板部署的状态，请使用[部署 - 获取](/rest/api/resources/deployments/get)。

   ```HTTP
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
   ```

## <a name="deployment-name"></a>部署名称

可以为部署命名（例如） `ExampleDeployment` 。

每次运行部署时，会在资源组的部署历史记录中添加一个包含部署名称的条目。 如果运行另一个部署并为其指定了相同的名称，则会将以前的条目替换为当前部署。 如果要在部署历史记录中维护唯一条目，请为每个部署指定唯一名称。

若要创建唯一名称，可以分配一个随机数字。 或者，添加日期值。

如果以相同的部署名称运行对同一资源组的并发部署，则只完成最后一个部署。 任何没有完成的同名部署都将替换为最后一个部署。 例如，如果你运行一个名为 `newStorage` 的部署，它将部署一个名为的存储帐户 `storage1` ，同时运行名为的另一个部署， `newStorage` 该部署将部署名为的存储帐户 `storage2` ，你只部署一个存储帐户。 生成的存储帐户名为 `storage2` 。

但是，如果运行名为的部署 `newStorage` （部署名为的存储帐户 `storage1` ），并且在完成后立即运行名为的另一个部署 `newStorage` ，该部署将部署名为的存储帐户 `storage2` ，则你有两个存储帐户。 一个名为 `storage1` ，另一个名为 `storage2` 。 但是，部署历史记录中只有一个条目。

为每个部署指定唯一名称时，可以在不发生冲突的情况下同时运行它们。 如果运行名为的部署 `newStorage1` ，该部署将部署名为的存储帐户 `storage1` ，同时运行名为的另一个部署，该部署将部署 `newStorage2` 名为的存储帐户 `storage2` ，则部署历史记录中有两个存储帐户和两个条目。

若要避免与并发部署冲突并确保部署历史记录中的唯一条目，请为每个部署指定唯一名称。

## <a name="next-steps"></a>后续步骤

- 若要在出错时回退到成功的部署，请参阅[出错时回退到成功的部署](rollback-on-error.md)。
- 若要指定如何处理存在于资源组中但未在模板中定义的资源，请参阅 [Azure 资源管理器部署模式](deployment-modes.md)。
- 若要了解如何处理异步 REST 操作，请参阅[跟踪异步 Azure 操作](../management/async-operations.md)。
- 若要详细了解模板，请参阅[了解 ARM 模板的结构和语法](template-syntax.md)。

