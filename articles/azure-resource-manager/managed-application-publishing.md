---
title: "创建和发布 Azure 服务目录托管应用程序 | Microsoft Docs"
description: "演示如何创建适用于组织中成员的 Azure 托管应用程序。"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/23/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 39b74984ec2f89ed39753963de7fe3ff79577c9e
ms.contentlocale: zh-cn
ms.lasthandoff: 08/25/2017

---
# <a name="publish-a-managed-application-for-internal-consumption"></a>发布托管应用程序供内部使用

可以创建和发布适用于组织中成员的 Azure [托管应用程序](managed-application-overview.md)。 例如，IT 部门可发布确保符合组织标准的托管应用程序。 这些托管应用程序通过服务目录（而不是 Azure Marketplace）提供。

若要发布服务目录的托管应用程序，必须执行以下操作：

* 创建包含三个必需模板文件的 .zip 包。
* 确定需要对客户订阅中的资源组具有访问权限的用户、组或应用程序。
* 创建指向 .zip 包并请求标识访问权限的托管应用程序定义。

## <a name="create-a-managed-application-package"></a>创建托管应用程序包

第一步是创建三个必需模板文件。 将所有三个文件打包为一个 .zip 文件，并将其上传到可访问位置，例如存储帐户。 创建托管应用程序定义时，会向此 .zip 文件传递一个链接。

* **applianceMainTemplate.json**：此文件定义作为托管应用程序的一部分预配的 Azure 资源。 该模板与常规资源管理器模板并没有不同。 例如，若要通过托管应用程序创建存储帐户，则 applianceMainTemplate.json 应包含：

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]",
            "apiVersion": "2016-01-01",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {}
  }
  ```

* **mainTemplate.json**：创建托管应用程序时，用户会部署此模板。 该模板定义的托管应用程序资源是一种 Microsoft.Solutions/appliances 资源类型。 此文件包含 applianceMainTemplate.json 中的资源所需的所有参数。

  需要在此模板中设置两个重要属性。 第一个是 applianceDefinitionId 属性，即托管应用程序定义的 ID。 稍后将在本主题中创建定义。 设置此值时，必须确定用于存储托管应用程序定义的订阅和资源组。 并且，必须确定定义的名称。 该 ID 的格式为：

  `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Solutions/applianceDefinitions/<definition-name>`

  第二个是 managedResourceGroupId 属性，即在其中创建 Azure 资源的资源组的 ID。 可以为此资源组名称分配值，也可以让用户提供名称。 该 ID 的格式为：

  `/subscriptions/<subscription-id>/resourceGroups/<resoure-group-name>`。

  下例演示了一个 mainTemplate.json 文件。 它为已部署的资源指定资源组。 设置定义 ID 设置为使用名为“storageApp”的定义和名为“managedApplicationGroup”的资源组。 可以更改这些值以使用不同的名称。 在定义 ID 中提供自己的订阅 ID。

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        }
    },
    "variables": {
        "managedRGId": "[concat(resourceGroup().id,'-application-resources')]",
        "managedAppName": "[concat('managedStorage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Solutions/appliances",
            "name": "[variables('managedAppName')]",
            "apiVersion": "2016-09-01-preview",
            "location": "[resourceGroup().location]",
            "kind": "ServiceCatalog",
            "properties": {
                "managedResourceGroupId": "[variables('managedRGId')]",
                "applianceDefinitionId": "/subscriptions/<subscription-id>/resourceGroups/managedApplicationGroup/providers/Microsoft.Solutions/applianceDefinitions/storageApp",
                "parameters": {
                    "storageAccountNamePrefix": {
                        "value": "[parameters('storageAccountNamePrefix')]"
                    }
                }
            }
        }
    ]
  }
  ```

* **applianceCreateUiDefinition.json**：Azure 门户使用此文件生成用户界面，供用户创建托管应用程序。 定义用户如何为每个参数提供输入。 可以使用诸如下拉列表、文本框、密码框和其他输入工具之类的选项。 若要了解如何为托管应用程序创建 UI 定义文件，请参阅 [CreateUiDefinition 入门](managed-application-createuidefinition-overview.md)。

  下例演示了一个 applianceCreateUiDefinition.json 文件，它使用户能够通过文本框指定存储帐户名称的前缀。

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {
                "name": "storageAccounts",
                "type": "Microsoft.Common.TextBox",
                "label": "Storage account name prefix",
                "defaultValue": "storage",
                "toolTip": "Provide a value that is used for the prefix of your storage account. Limit to 11 characters.",
                "constraints": {
                    "required": true,
                    "regex": "^[a-z0-9A-Z]{1,11}$",
                    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-11 characters long."
                },
                "visible": true
            }
        ],
        "steps": [],
        "outputs": {
            "storageAccountNamePrefix": "[basics('storageAccounts')]"
        }
    }
  }
  ```

所需的所有文件都准备就绪后，将其打包为一个 .zip 文件。 三个文件必须都位于该 .zip 文件的根级别。 如果将它们放在文件夹中，则会在创建托管应用程序定义时收到错误，称所需文件不存在。 将包上传到可从中使用程序包的一个可访问位置。 本文其余部分假定 .zip 文件位于可公开访问的存储 blob 容器中。

## <a name="create-an-azure-active-directory-user-group-or-application"></a>创建 Azure Active Directory 用户组或应用程序

第二步是选择代表客户管理资源的用户组或应用程序。 根据分配的角色，此用户组或应用程序对托管资源组具有权限。 角色可以是任何基于角色的访问控制 (RBAC) 的内置角色，如 Owner 或 Contributor。 也可以授权个体用户来管理资源，但通常会将此权限分配给用户组。 若要创建新的 Active Directory 用户组，请参阅[在 Azure Active Directory 中创建组并添加成员](../active-directory/active-directory-groups-create-azure-portal.md)。

需要提供用户组的对象 ID 以用于管理资源。 下例演示了如何从该组的显示名称获取对象 ID：

```azurecli-interactive
az ad group show --group exampleGroupName
```

示例命令将返回以下输出：

```azurecli
{
    "displayName": "exampleGroupName",
    "mail": null,
    "objectId": "9aabd3ad-3716-4242-9d8e-a85df479d5d9",
    "objectType": "Group",
    "securityEnabled": true
}
```

若仅需检索对象 ID，请使用：

```azurecli-interactive
groupid=$(az ad group show --group exampleGroupName --query objectId --output tsv)
```

## <a name="get-the-role-definition-id"></a>获取角色定义 ID

接下来，需要获取希望将其访问权限授予用户、用户组或应用程序的 RBAC 内置角色的角色定义 ID。 通常，你会使用“Owner”、“Contributor”或“Reader”角色。 以下命令展示了如何获取“Owner”角色的角色定义 ID：


```azurecli-interactive
az role definition list --name owner
```

该命令将返回以下输出：

```azurecli
{
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
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

需要具有前面的示例中“name”属性的值。 可以按以下步骤操作实现仅检索该属性：

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

## <a name="create-the-managed-application-definition"></a>创建托管应用程序定义

如果还没有用于存储托管应用程序定义的资源组，请立即创建一个：

```azurecli-interactive
az group create --name managedApplicationGroup --location westcentralus
```

现在，创建托管应用程序定义资源。

```azurecli-interactive
az managedapp definition create \
  --name storageApp \
  --location "westcentralus" \
  --resource-group managedApplicationGroup \
  --lock-level ReadOnly \
  --display-name myteststorageapp \
  --description storageapp \
  --authorizations "$groupid:$roleid" \
  --package-file-uri <uri-path-to-zip-file>
```

前面的示例中使用的参数有：

* **resource-group**：在其中创建托管应用程序定义的资源组的名称。
* lock-level：在托管资源组上放置的锁的类型。 它防止客户对此资源组执行不良操作。 当前，ReadOnly 是唯一受支持的锁级别。 当指定了 ReadOnly 时，客户只能读取托管资源组中存在的资源。
* authorizations：描述用于授予对托管资源组权限的主体 ID 和角色定义 ID。 它是以 `<principalId>:<roleDefinitionId>` 格式指定的。 另外还可以为此属性指定多个值。 如果需要多个值，则应当以 `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>` 形式指定它们。 请以空格分隔多个值。
* **package-file-uri**：包含模板文件的托管应用程序包的位置，可以是 Azure 存储 blob。

## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅[托管应用程序概述](managed-application-overview.md)。
* 有关文件示例，请参阅[托管应用程序示例](https://github.com/Azure/azure-managedapp-samples/tree/master/samples)。
* 有关使用服务目录托管应用程序的信息，请参阅[使用服务目录托管应用程序](managed-application-consumption.md)。
* 有关将托管应用程序发布到 Azure Marketplace 的信息，请参阅 [Marketplace 中的 Azure 托管应用程序](managed-application-author-marketplace.md)。
* 有关从 Marketplace 使用托管应用程序的信息，请参阅[在 Marketplace 中使用 Azure 托管应用程序](managed-application-consume-marketplace.md)。
* 若要了解如何为托管应用程序创建 UI 定义文件，请参阅 [CreateUiDefinition 入门](managed-application-createuidefinition-overview.md)。

