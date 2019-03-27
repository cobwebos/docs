---
title: 使用服务主体自动完成 Azure Analysis Services 任务 | Microsoft Docs
description: 了解如何创建服务主体以自动完成 Azure Analysis Services 任务。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: ee4a78148c36c7838edcc36f8f76ad612ac87ab0
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487951"
---
# <a name="automation-with-service-principals"></a>使用服务主体进行自动化

服务主体是在租户中创建的 Azure Active Directory 应用程序资源，用于执行无人参与的资源和服务级别操作。 服务主体是特殊类型的用户标识，具有应用程序 ID 和密码或证书。 服务主体只具有特定任务所需的权限，这些任务是按分配的角色和权限来定义的。 

在 Analysis Services 中，服务主体可以与 Azure 自动化、PowerShell 无人参与模式、自定义客户端应用程序和 Web 应用配合使用，以便自动完成常见的任务。 例如，预配服务器、部署模型、数据刷新、垂直缩放、暂停/恢复等操作均可使用服务主体自动完成。 权限通过角色成员身份分配给服务主体，十分类似于常规的 Azure AD UPN 帐户。

## <a name="create-service-principals"></a>创建服务主体
 
可以通过 Azure 门户或 PowerShell 创建服务主体。 若要了解更多信息，请参阅以下文章：

[创建服务主体 - Azure 门户](../active-directory/develop/howto-create-service-principal-portal.md)   
[创建服务主体 - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>在 Azure 自动化中存储凭据和证书资产

服务主体凭据和证书可以安全地存储在 Azure 自动化中进行 Runbook 操作。 若要了解更多信息，请参阅以下文章：

[Azure 自动化中的凭据资产](../automation/automation-credentials.md)   
[Azure 自动化中的证书资产](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>将服务主体添加到服务器管理员角色

在使用服务主体进行 Analysis Services 服务器管理操作之前，必须将其添加到服务器管理员角色。 有关详细信息，请参阅[将服务主体添加到服务器管理员角色](analysis-services-addservprinc-admins.md)。

## <a name="service-principals-in-connection-strings"></a>连接字符串中的服务主体

服务主体 appID 和密码或证书可以在连接字符串中使用，与 UPN 很类似。

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

使用服务主体进行与资源管理操作时[Az.AnalysisServices](/powershell/module/az.analysisservices)模块，请使用`Connect-AzAccount`cmdlet。 将服务主体与 [SQLServer](https://www.powershellgallery.com/packages/SqlServer) 模块配合使用以进行服务器操作时，请使用 `Add-AzAnalysisServicesAccount` cmdlet。 

以下示例使用 appID 和密码执行模型数据库刷新操作：

```powershell
Param (

        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force

$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

Add-AzAnalysisServicesAccount -Credential $Credential -ServicePrincipal -TenantId $TenantId -RolloutEnvironment "westcentralus.asazure.windows.net"

Invoke-ProcessTable -Server "asazure://westcentralus.asazure.windows.net/myserver" -TableName "MyTable" -Database "MyDb" -RefreshType "Full"
```

### <a name="amo-and-adomd"></a>AMO 和 ADOMD 

通过客户端应用程序和 Web 应用进行连接时，由 NuGet 提供的 [AMO 和 ADOMD 客户端库](analysis-services-data-providers.md) 15.0.2 及更高版本的可安装包支持在连接字符串中使用服务主体，可以使用 `app:AppID` 语法以及密码或 `cert:thumbprint`。 

以下示例使用 `appID` 和 `password` 执行模型数据库刷新操作：

```C#
string appId = "xxx";
string authKey = "yyy";
string connString = $"Provider=MSOLAP;Data Source=asazure://westus.asazure.windows.net/<servername>;User ID=app:{appId};Password={authKey};";
Server server = new Server();
server.Connect(connString);
Database db = server.Databases.FindByName("adventureworks");
Table tbl = db.Model.Tables.Find("DimDate");
tbl.RequestRefresh(RefreshType.Full);
db.Model.SaveChanges();
```

## <a name="next-steps"></a>后续步骤
[使用 Azure PowerShell 进行登录](https://docs.microsoft.com/powershell/azure/authenticate-azureps)   
[将服务主体添加到服务器管理员角色](analysis-services-addservprinc-admins.md)   
