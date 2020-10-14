---
title: 将服务主体添加到 Azure Analysis Services 管理员角色 | Microsoft Docs
description: 了解如何将自动化服务主体添加到 Azure Analysis Services 服务器管理员角色
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: 8e751d210ab472d82e4abd613d37069a8c591f52
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92013927"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>将服务主体添加到服务器管理员角色 

 若要自动执行无人参与的 PowerShell 任务，服务主体在托管的 Analysis Services 服务器上必须具备“服务器管理员”权限。 本文介绍如何将服务主体添加到 Azure AS 服务器上的服务器管理员角色。 可以使用 SQL Server Management Studio 或资源管理器模板来执行此操作。 

> [!NOTE]
> 必须直接将服务主体添加到服务器管理员角色。 不支持先将服务主体添加到安全组，然后再将该安全组添加到服务器管理员角色。 

## <a name="before-you-begin"></a>开始之前
在完成此项任务之前，必须有一个在 Azure Active Directory 中注册的服务主体。

[创建服务主体 - Azure 门户](../active-directory/develop/howto-create-service-principal-portal.md)   
[创建服务主体 - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>使用 SQL Server Management Studio

可以使用 SQL Server Management Studio (SSMS) 配置服务器管理员。 若要完成此项任务，在 Azure AS 服务器上必须具备[服务器管理员](analysis-services-server-admins.md)权限。 

1. 在 SSMS 中连接至 Azure AS 服务器。
2. 在“服务器属性” > “安全性”中，单击“添加”  。
3. 在“选择用户或组”中，按名称搜索注册的应用，选中以后单击“添加” 。

    ![搜索服务主体帐户](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. 验证服务主体帐户 ID，然后单击“确定”。
    
    ![搜索服务主体帐户](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>使用资源管理器模板

还可以通过使用 Azure 资源管理器模板部署 Analysis Services 服务器来配置服务器管理员。 运行部署的身份必须在 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md) 中属于资源的“参与者”角色。

> [!IMPORTANT]
> 必须使用格式 `app:{service-principal-client-id}@{azure-ad-tenant-id}` 来添加服务主体。

以下资源管理器模板部署了一个 Analysis Services 服务器，并将指定的服务主体添加到 Analysis Services 管理员角色：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "analysisServicesServerName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "analysisServicesSkuName": {
            "type": "string"
        },
        "analysisServicesCapacity": {
            "type": "int"
        },
        "servicePrincipalClientId": {
            "type": "string"
        },
        "servicePrincipalTenantId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('analysisServicesServerName')]",
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('analysisServicesSkuName')]",
                "capacity": "[parameters('analysisServicesCapacity')]"
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "[concat('app:', parameters('servicePrincipalClientId'), '@', parameters('servicePrincipalTenantId'))]"
                    ]
                }
            }
        }
    ]
}
```

## <a name="using-managed-identities"></a>使用托管标识

还可以将托管标识添加到 Analysis Services 管理员列表。 例如，你可能有一个[使用系统分配托管标识的逻辑应用](../logic-apps/create-managed-service-identity.md)，并且想要授予其管理 Analysis Services 服务器的能力。

在 Azure 门户和 API 的大多数部分中，托管标识都是使用其服务主体对象 ID 进行标识的。 但是，Analysis Services 要求使用其客户端 ID 来标识它们。 要获取服务主体的客户端 ID，可以使用 Azure CLI：

```bash
az ad sp show --id <ManagedIdentityServicePrincipalObjectId> --query appId -o tsv
```

或者，可以使用 PowerShell：

```powershell
(Get-AzureADServicePrincipal -ObjectId <ManagedIdentityServicePrincipalObjectId>).AppId
```

然后，可以将此客户端 ID 与租户 ID 结合使用，以将托管标识添加到 Analysis Services 管理员列表中，如上所述。

## <a name="related-information"></a>相关信息

* [下载 SQL Server PowerShell 模块](/sql/ssms/download-sql-server-ps-module)   
* [下载 SSMS](/sql/ssms/download-sql-server-management-studio-ssms)