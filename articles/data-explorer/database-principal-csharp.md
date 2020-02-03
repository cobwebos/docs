---
title: 使用添加 Azure 数据资源管理器的数据库主体C#
description: 本文介绍如何使用C#为 Azure 数据资源管理器添加数据库主体。
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 797d1253d44739f2026563e3df72bc85a8ef382e
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965029"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-c"></a>使用添加 Azure 数据资源管理器的数据库主体C#

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Azure Resource Manager 模板](database-principal-resource-manager.md)

Azure 数据资源管理器是一项快速且高度可缩放的数据探索服务，适用于日志和遥测数据。 本文介绍如何使用C#为 Azure 数据资源管理器添加数据库主体。

## <a name="prerequisites"></a>必备组件

* 如果尚未安装 Visual Studio 2019，可以下载并使用**免费**的[Visual Studio 2019 社区版](https://www.visualstudio.com/downloads/)。 在安装 Visual Studio 的过程中，请确保启用“Azure 开发”。
* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* [创建群集和数据库](create-cluster-database-csharp.md)。

## <a name="install-c-nuget"></a>安装C# NuGet

* 请安装[kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)。
* 安装[ClientRuntime](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication)进行身份验证。

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>添加数据库主体

下面的示例演示如何以编程方式添加数据库主体。

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var kustoManagementClient = new KustoManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
string principalAssignmentName = "databasePrincipalAssignment1";
string principalId = "xxxxxxxx";//User email, application ID, or security group name
string role = "Admin";//Admin, Ingestor, Monitor, User, UnrestrictedViewers, Viewer
string tenantIdForPrincipal = tenantId;
string principalType = "App";//User, App, or Group

var databasePrincipalAssignment = new DatabasePrincipalAssignment(principalId, role, principalType, tenantId: tenantIdForPrincipal);
await kustoManagementClient.DatabasePrincipalAssignments.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, principalAssignmentName, databasePrincipalAssignment);
```

|**设置** | **建议的值** | **字段说明**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 租户 ID。 也称为目录 ID。|
| subscriptionId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 用于创建资源的订阅 ID。|
| clientId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 可以访问租户中资源的应用程序的客户端 ID。|
| clientSecret | *xxxxxxxxxxxxxx* | 可以访问租户中资源的应用程序的客户端机密。 |
| resourceGroupName | *testrg* | 包含群集的资源组的名称。|
| clusterName | mykustocluster | 群集的名称。|
| databaseName | mykustodatabase | 数据库名称。|
| principalAssignmentName | *databasePrincipalAssignment1* | 数据库主体资源的名称。|
| principalId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 主体 ID，可以是用户电子邮件、应用程序 ID 或安全组名称。|
| 角色 (role) | *管理员* | 数据库主体的角色，可以是 "Admin"、"引入器"、"Monitor"、"User"、"UnrestrictedViewers" 和 "Viewer"。|
| tenantIdForPrincipal | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 主体的租户 ID。|
| PrincipalType | *应用* | 主体的类型，可以是 "用户"、"应用" 或 "组"|

## <a name="next-steps"></a>后续步骤

* [使用 Azure 数据资源管理器 Node 库引入数据](node-ingest-data.md)
