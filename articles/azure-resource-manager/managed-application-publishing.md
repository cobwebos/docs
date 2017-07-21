---
title: "创建和发布 Azure 托管应用程序 | Microsoft Docs"
description: "介绍了 ISV 或合作伙伴如何创建 Azure 托管应用程序"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/08/2017
ms.author: gauravbh; tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 56dd68e328abd6c1dacdf7a8e051ca6b3cd07083
ms.contentlocale: zh-cn
ms.lasthandoff: 05/11/2017


---
# <a name="create-and-publish-an-azure-managed-application"></a>创建和发布 Azure 托管应用程序 

如[托管应用程序概述](managed-application-overview.md)一文中所述，在端到端体验中有两种应用场景。 一种是发布者或 ISV 希望创建供客户使用的托管应用程序。 第二种是客户或使用者使用托管应用程序。 本文重点介绍了第一种应用场景并说明了 ISV 如何创建和发布托管应用程序。 

若要创建托管应用程序，必须创建：

* 一个包含模板文件的包
* 对客户的订阅中的资源组具有访问权限的用户、组或应用程序
* 设备定义

有关文件示例，请参阅[托管应用程序示例](https://github.com/Azure/azure-managedapp-samples/tree/master/samples)。

## <a name="create-managed-application-package"></a>创建托管应用程序包

第一个步骤是创建包含主要模板文件的应用程序包。 发布者或 ISV 需要创建三个文件。 

* 第一个文件名为 **applianceMainTemplate.json**。 此模板文件定义作为托管应用程序的一部分预配的实际资源。 例如，若要创建使用托管应用程序的存储帐户，则 applianceMainTemplate.json 包含： 

  ```json
  {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "storageAccountName": {
              "type": "String"
          }
      },
      "resources": [{
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('storageAccountName')]",
          "apiVersion": "2016-01-01",
          "location": "westus",
          "sku": {
              "name": "Standard_LRS"
          },
          "kind": "Storage",
          "properties": {       
          }
      }],
      "outputs": {      
      }
  }
  ```

* 发布者需要创建的第二个文件是 **mainTemplate.json**。 此模板文件仅包含设备资源 (Microsoft.Solutions/appliances)。 它还包含 applianceMainTemplate.json 中的资源所需的所有参数。 

  在创建托管应用程序期间，需要使用两个重要属性作为输入。 **managedResourceGroupId** 属性是将在其中创建 applianceMainTemplate.json 中定义的资源的资源组的 ID。 该 ID 的格式为：`/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}`

  **applianceDefinitionId** 属性是托管应用程序定义资源的 ID。 该 ID 的格式为：`/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Solutions/applianceDefinitions/{applianceDefinitionName}`

  请针对这两个值提供参数，以便使用者在创建托管应用程序时可以指定它们。 在下面的示例中，对应于这些属性的两个参数是 managedByResourceGroup 和 applianceDefinitonId。

  ```json
  {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "storageAccountName": {
              "type": "String"
          },
          "applianceDefinitionId": {
              "type": "String"
          },
          "managedByResourceGroup": {
              "type": "String"
          },
          "applianceName": {
              "type": "String"
          },
      },
      "variables": {            
      },
      "resources": [{
          "type": "Microsoft.Solutions/appliances",
          "name": "[parameters('applianceName')]",
          "apiVersion": "2016-09-01-preview",
          "location": "[resourceGroup().location]",
          "kind": "ServiceCatalog",
          "properties": {
              "ManagedResourceGroupId": "[parameters('managedByResourceGroup')]",
              "applianceDefinitionId": "[parameters('applianceDefinitionId')]",
              "Parameters": {
                  "storageAccountName": {
                      "value": "[parameters('storageAccountName')]"
                  }             
              }
          }
      }]
  }
  ```

* 包中需要的第三个文件是 **createUiDefinition.json**。 Azure 门户使用此文件为使用者生成用于创建托管应用程序的用户界面。 你需要定义托管应用程序的参数，并定义使用者如何获取每个参数的输入。 可以使用诸如下拉选择器、文本框、密码框和其他输入工具之类的选项。 若要了解如何为托管应用程序创建 UI 定义文件，请参阅 [CreateUiDefinition 入门](managed-application-createuidefinition-overview.md)。

在所有必需文件准备就绪后，将包上传到可以从中使用包的一个可访问位置。


## <a name="create-azure-ad-user-group-or-application"></a>创建 Azure AD 用户组或应用程序
接下来，创建你希望用来代表客户管理资源的用户组或应用程序。 此用户组或应用程序对托管资源组具有角色所描述的权限。 角色可以是任何内置的 RBAC 角色，例如**所有者**或**参与者**。 也可以授权个体用户来管理资源，但通常你分配此权限来使用用户组。 若要创建新的 Active Directory 用户组，请使用：

```azurecli
az ad group create --display-name "name" --mail-nickname "nickname"
```

也可以使用现有组。 你需要具有新创建的或现有用户组的对象 Id。 下面的示例展示了如何从用于创建组的显示名称获取对象 ID。

```azurecli
az ad group show --group "groupName"
```

示例：

```azurecli
az ad group show --group ravAppliancetestADgroup
```

将返回以下输出：

```json 
{
    "displayName": "ravAppliancetestADgroup",
    "mail": null,
    "objectId": "9aabd3ad-3716-4242-9d8e-a85df479d5d9",
    "objectType": "Group",
    "securityEnabled": true
}
```
    
需要上面的 objectId 值。 

## <a name="get-the-role-definition-id"></a>获取角色定义 ID

接下来，你需要获取你希望将其访问权限授予用户、用户组或应用程序的 RBAC 内置角色的角色定义 ID。 通常，你希望使用 "Owner"、"Contributor" 或 "Reader" 角色。 以下命令展示了如何获取 "Owner" 角色的角色定义 ID：

```azurecli
az role definition list --name owner
```

将返回以下输出：

```json
{
    "id": "/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
    "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
    "properties": {
      "assignableScopes": [
    "/"
      ],
      "description": "Lets you manage everything, including access to resources.",
      "permissions": [
    {
      "actions": [
    "*"
      ],
      "notActions": []
    }
      ],
      "roleName": "Owner",
      "type": "BuiltInRole"
    },
    "type": "Microsoft.Authorization/roleDefinitions"
}
```

你需要具有前面的示例中 "name" 属性的值。


## <a name="create-the-managed-application-definition"></a>创建托管应用程序定义

最后一步是创建托管应用程序定义资源。 在创建设备包和授权后，使用以下命令创建设备定义： 

```azurecli
az managedapp definition create -n ravtestAppDef4 -l "westcentralus" 
    --resource-group ravApplianceDefRG3 --lock-level ReadOnly 
    --display-name ravtestappdef --description ravtestdescription  
    --authorizations "9aabd3ad-3716-4242-9d8e-a85df479d5d9:8e3af657-a8ff-443c-a75c-2fe8c4bcb635" 
    --package-file-uri "{path to package}" --debug 
```

前面的示例中使用的参数有：

- resource-group - 将在其中创建设备定义的资源组的名称。
- lock-level - 在托管资源组上放置的锁的类型。 它防止客户对此资源组执行不良操作。 当前，**ReadOnly** 是唯一受支持的锁级别。 当指定了 ReadOnly 时，客户只能读取托管资源组中存在的资源。
- authorizations - 描述用于授予对托管资源组的权限的主体 ID 和角色定义 ID。 它是以 `<principalId>:<roleDefinitionId>` 格式指定的。 还可以为此属性指定多个值。 如果需要多个值，则应当以 `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>` 形式指定此属性。 请以空格分隔多个值。
- package-file-uri - 包含模板文件的设备包的位置，这可以是 Azure 存储 blob。 


## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅 [Azure 托管应用程序概述](managed-application-overview.md)。
* 有关文件示例，请参阅[托管应用程序示例](https://github.com/Azure/azure-managedapp-samples/tree/master/samples)。
* 若要了解使用者体验，请参阅[使用 Azure 托管应用程序](managed-application-consumption.md)。
* 若要了解如何为托管应用程序创建 UI 定义文件，请参阅 [CreateUiDefinition 入门](managed-application-createuidefinition-overview.md)。

