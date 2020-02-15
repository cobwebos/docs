---
title: 将服务主体添加到 Azure Analysis Services 管理员角色 |Microsoft Docs
description: 了解如何将自动化服务主体添加到 Azure Analysis Services 服务器管理员角色
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: b75740e9bff714ad68c93bea7e387e60da2f1c59
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212500"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>将服务主体添加到服务器管理员角色 

 若要自动执行无人参与的 PowerShell 任务，服务主体在托管的 Analysis Services 服务器上必须具备“服务器管理员”权限。 本文介绍如何将服务主体添加到 Azure AS 服务器上的服务器管理员角色。 您可以使用 SQL Server Management Studio 或资源管理器模板来执行此操作。
 
> [!NOTE]
> 对于使用 Azure PowerShell cmdlet 的服务器操作，服务主体也必须属于[Azure 基于角色的访问控制（RBAC）](../role-based-access-control/overview.md)中资源的**所有者**角色。 

## <a name="before-you-begin"></a>开始之前
在完成此项任务之前，必须有一个在 Azure Active Directory 中注册的服务主体。

[创建服务主体 - Azure 门户](../active-directory/develop/howto-create-service-principal-portal.md)   
[创建服务主体 - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>使用 SQL Server Management Studio

你可以使用 SQL Server Management Studio （SSMS）配置服务器管理员。 若要完成此项任务，在 Azure AS 服务器上必须具备[服务器管理员](analysis-services-server-admins.md)权限。 

1. 在 SSMS 中连接至 Azure AS 服务器。
2. 在“服务器属性” **“安全性”中，单击“添加”**  > 。
3. 在“选择用户或组”中，按名称搜索注册的应用，选中以后单击“添加”。

    ![搜索服务主体帐户](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. 验证服务主体帐户 ID，然后单击“确定”。
    
    ![搜索服务主体帐户](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>使用资源管理器模板

还可以通过使用 Azure 资源管理器模板部署 Analysis Services 服务器来配置服务器管理员。 运行部署的标识必须属于[Azure 基于角色的访问控制（RBAC）](../role-based-access-control/overview.md)中资源的**参与者**角色。

> [!IMPORTANT]
> 必须使用格式 `app:{service-principal-client-id}@{azure-ad-tenant-id}`来添加服务主体。

以下资源管理器模板使用已添加到 Analysis Services 管理员角色中的指定服务主体部署 Analysis Services 服务器：

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

## <a name="related-information"></a>相关信息

* [下载 SQL Server PowerShell 模块](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [下载 SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


