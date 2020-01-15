---
title: 发布服务目录托管应用
description: 演示如何创建适用于组织中成员的 Azure 托管应用程序。
author: tfitzmac
ms.topic: tutorial
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: de2b79c5016b44011a14c1071eab6579f3a0b6df
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75649054"
---
# <a name="create-and-publish-a-managed-application-definition"></a>创建并发布托管应用程序定义

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

可以创建和发布适用于组织中成员的 Azure [托管应用程序](overview.md)。 例如，IT 部门可发布符合组织标准的托管应用程序。 这些托管应用程序通过服务目录（而不是 Azure 市场）提供。

若要发布服务目录的托管应用程序，必须执行以下操作：

* 创建一个模板，定义要与托管应用程序一起部署的资源。
* 部署托管应用程序时，请定义门户的用户界面元素。
* 创建包含必需模板文件的 .zip 包。
* 确定需要对客户订阅中的资源组具有访问权限的用户、组或应用程序。
* 创建指向 .zip 包并请求标识访问权限的托管应用程序定义。

对于本文，托管应用程序只具有存储帐户。 它用于说明发布托管应用程序的步骤。 有关完整示例，请参阅 [Azure 托管应用程序的示例项目](sample-projects.md)。

本文中的 PowerShell 示例需要 Azure PowerShell 6.2 或更高版本。 如果需要，请[更新版本](/powershell/azure/install-Az-ps)。

## <a name="create-the-resource-template"></a>创建资源模板

每个托管应用程序定义均包含一个名为 **mainTemplate.json** 的文件。 可在其中定义要部署的 Azure 资源。 该模板与常规资源管理器模板并没有不同。

创建一个名为 **mainTemplate.json** 的文件。 该名称区分大小写。

将以下 JSON 添加到该文件。 它定义用于创建存储帐户的参数，并指定存储帐户的属性。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        },
        "storageAccountType": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
        }
    }
}
```

保存 mainTemplate.json 文件。

## <a name="defining-your-create-experience-using-createuidefinitionjson"></a>使用 CreateUiDefinition.json 定义创建体验

作为发布者，可以使用 **createUiDefinition.json** 文件定义创建体验，该文件为创建托管应用程序的用户生成界面。 使用[控件元素](create-uidefinition-elements.md)（包括下拉框、文本框和密码框）来定义用户如何为每个参数提供输入。

创建一个名为 **createUiDefinition.json** 的文件（此名称区分大小写）

将以下起始 JSON 添加到该文件并保存。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
   "version": "0.1.2-preview",
   "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "storageConfig",
                "label": "Storage settings",
                "subLabel": {
                    "preValidation": "Configure the infrastructure settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Storage settings",
                "elements": [
                    {
                        "name": "storageAccounts",
                        "type": "Microsoft.Storage.MultiStorageAccountCombo",
                        "label": {
                            "prefix": "Storage account name prefix",
                            "type": "Storage account type"
                        },
                        "defaultValue": {
                            "type": "Standard_LRS"
                        },
                        "constraints": {
                            "allowedTypes": [
                                "Premium_LRS",
                                "Standard_LRS",
                                "Standard_GRS"
                            ]
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
            "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
            "location": "[location()]"
        }
    }
}
```

若要了解详细信息，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。

## <a name="package-the-files"></a>将文件打包

将这两个文件添加到名为 app.zip 的 .zip 文件。 这两个文件必须都位于该 .zip 文件的根级别。 如果将它们放在文件夹中，则会在创建托管应用程序定义时收到错误，称所需文件不存在。 

将包上传到可从中使用程序包的一个可访问位置。 

```powershell
New-AzResourceGroup -Name storageGroup -Location eastus
$storageAccount = New-AzStorageAccount -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context

New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzStorageBlobContent -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

## <a name="create-the-managed-application-definition"></a>创建托管应用程序定义

### <a name="create-an-azure-active-directory-user-group-or-application"></a>创建 Azure Active Directory 用户组或应用程序

下一步是选择代表客户来管理资源的用户组或应用程序。 根据分配的角色，此用户组或应用程序对托管资源组具有权限。 角色可以是任何基于角色的访问控制 (RBAC) 的内置角色，如 Owner 或 Contributor。 也可以授权个体用户来管理资源，但通常会将此权限分配给用户组。 若要创建新的 Active Directory 用户组，请参阅[在 Azure Active Directory 中创建组并添加成员](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

需要提供用户组的对象 ID 以用于管理资源。 

```powershell
$groupID=(Get-AzADGroup -DisplayName mygroup).Id
```

### <a name="get-the-role-definition-id"></a>获取角色定义 ID

接下来，需要获取希望将其访问权限授予用户、用户组或应用程序的 RBAC 内置角色的角色定义 ID。 通常，你会使用“Owner”、“Contributor”或“Reader”角色。 以下命令展示了如何获取“Owner”角色的角色定义 ID：

```powershell
$ownerID=(Get-AzRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>创建托管应用程序定义

如果还没有用于存储托管应用程序定义的资源组，请立即创建一个：

```powershell
New-AzResourceGroup -Name appDefinitionGroup -Location westcentralus
```

现在，创建托管应用程序定义资源。

```powershell
$blob = Get-AzStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

### <a name="make-sure-users-can-see-your-definition"></a>请确保用户可以看到你的定义

你可以访问托管应用程序定义，但你希望确保组织中的其他用户可以访问它。 至少授予他们对定义的读者角色。 他们可能已从订阅或资源组继承了此级别的访问权限。 若要查看谁可以访问定义并添加用户或组，请参阅[使用基于角色的访问控制来管理对 Azure 订阅资源的访问权限](../../role-based-access-control/role-assignments-portal.md)。

## <a name="next-steps"></a>后续步骤

* 若要将托管应用程序发布到 Azure 市场，请参阅[市场中的 Azure 托管应用程序](publish-marketplace-app.md)。
* 若要部署托管应用程序实例，请参阅[通过 Azure 门户部署服务目录应用](deploy-service-catalog-quickstart.md)。
