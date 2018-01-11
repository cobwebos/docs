---
title: "创建和发布 Azure 服务目录托管应用程序 | Microsoft Docs"
description: "演示如何创建适用于组织中成员的 Azure 托管应用程序。"
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: 46adcdf39625c85dc962a7541b68c5500cf920ee
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/19/2017
---
# <a name="publish-a-managed-application-for-internal-consumption"></a>发布托管应用程序供内部使用

可以创建和发布适用于组织中成员的 Azure [托管应用程序](overview.md)。 例如，IT 部门可发布确保符合组织标准的托管应用程序。 这些托管应用程序通过服务目录（而不是 Azure Marketplace）提供。

若要发布服务目录的托管应用程序，必须执行以下操作：

* 创建一个模板，定义要与托管应用程序一起部署的资源。
* 部署托管应用程序时，请定义门户的用户界面元素。
* 创建包含必需模板文件的 .zip 包。
* 确定需要对客户订阅中的资源组具有访问权限的用户、组或应用程序。
* 创建指向 .zip 包并请求标识访问权限的托管应用程序定义。

对于本文，托管应用程序只包含存储帐户。 它用于说明发布托管应用程序的步骤。 有关完整示例，请参阅 [Azure 托管应用程序的示例项目](sample-projects.md)。

## <a name="create-the-resource-template"></a>创建资源模板

每个托管应用程序定义均包含一个名为 **mainTemplate.json** 的文件。 可在其中定义要预配的 Azure 资源。 该模板与常规资源管理器模板并没有不同。

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
        "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString('storage'))]"
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

## <a name="create-the-user-interface-definition"></a>创建用户接口定义

Azure 门户使用 **createUiDefinition.json** 文件为创建托管应用程序的用户生成用户界面。 定义用户如何为每个参数提供输入。 可以使用诸如下拉列表、文本框、密码框和其他输入工具之类的选项。 若要了解如何为托管应用程序创建 UI 定义文件，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。

创建一个名为 **createUiDefinition.json** 的文件。 该名称区分大小写。

将以下 JSON 添加到该文件。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
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

保存 createUIDefinition.json 文件。

## <a name="package-the-files"></a>将文件打包

将这两个文件添加到名为 app.zip 的 .zip 文件。 这两个文件必须都位于该 .zip 文件的根级别。 如果将它们放在文件夹中，则会在创建托管应用程序定义时收到错误，称所需文件不存在。 

将包上传到可从中使用程序包的一个可访问位置。 

```powershell
New-AzureRmResourceGroup -Name storageGroup -Location eastus
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob

$ctx = $storageAccount.Context

New-AzureStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzureStorageBlobContent -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

## <a name="create-the-managed-application-definition"></a>创建托管应用程序定义

### <a name="create-an-azure-active-directory-user-group-or-application"></a>创建 Azure Active Directory 用户组或应用程序

下一步是选择代表客户来管理资源的用户组或应用程序。 根据分配的角色，此用户组或应用程序对托管资源组具有权限。 角色可以是任何基于角色的访问控制 (RBAC) 的内置角色，如 Owner 或 Contributor。 也可以授权个体用户来管理资源，但通常会将此权限分配给用户组。 若要创建新的 Active Directory 用户组，请参阅[在 Azure Active Directory 中创建组并添加成员](../active-directory/active-directory-groups-create-azure-portal.md)。

需要提供用户组的对象 ID 以用于管理资源。 

![获取组 ID](./media/publish-service-catalog-app/get-group-id.png)

### <a name="get-the-role-definition-id"></a>获取角色定义 ID

接下来，需要获取希望将其访问权限授予用户、用户组或应用程序的 RBAC 内置角色的角色定义 ID。 通常，你会使用“Owner”、“Contributor”或“Reader”角色。 以下命令展示了如何获取“Owner”角色的角色定义 ID：

```powershell
$ownerID=(Get-AzureRmRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>创建托管应用程序定义

如果还没有用于存储托管应用程序定义的资源组，请立即创建一个：

```powershell
New-AzureRmResourceGroup -Name appDefinitionGroup -Location westcentralus
```

现在，创建托管应用程序定义资源。

```powershell
$blob = Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzureRmManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "<group-id>:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

## <a name="create-the-managed-application-by-using-the-portal"></a>使用门户创建托管应用程序

现在，让我们使用门户部署托管应用程序。 可看到包中创建的用户界面。

1. 转到 Azure 门户。 选择“+ 新建”并搜索“服务目录”。

   ![搜索“服务目录”](./media/publish-service-catalog-app/select-new.png)

1. 选择“服务目录托管应用程序”。

   ![选择“服务目录”](./media/publish-service-catalog-app/select-service-catalog.png)

1. 选择“创建”。

   ![选择“创建”](./media/publish-service-catalog-app/select-create.png)

1. 从可用解决方案列表中查找要创建的托管应用程序并选择它。 选择“创建”。

   ![查找托管应用程序](./media/publish-service-catalog-app/find-application.png)

1. 提供托管应用程序所需的基本信息。 指定订阅和要包含托管应用程序的新资源组。 对于位置，选择“美国中西部”。 完成后，选择“确定”。

   ![提供托管应用程序参数](./media/publish-service-catalog-app/provide-basics.png)

1. 提供特定于托管应用程序中的资源的值。 完成后，选择“确定”。

   ![提供资源参数](./media/publish-service-catalog-app/provide-resource-values.png)

1. 模板会验证你提供的值。 如果验证成功，请选择“确定”启动部署。

   ![验证托管应用程序](./media/publish-service-catalog-app/validate.png)

部署完成后，托管应用程序将存在于名为 applicationGroup 的资源组中。 存储帐户将存在于名为 applicationGroup 加上哈希字符串值的资源组中。

## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅[托管应用程序概述](overview.md)。
* 有关示例项目，请参阅 [Azure 托管应用程序的示例项目](sample-projects.md)。
* 若要了解如何为托管应用程序创建 UI 定义文件，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
