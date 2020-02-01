---
title: 使用创建 Azure 数据资源管理器群集和数据库C#
description: 了解如何使用创建 Azure 数据资源管理器群集和数据库C#
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 2d800dc401b0d85b26a71817a1a70d66539203ae
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902121"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>使用创建 Azure 数据资源管理器群集和数据库C#

> [!div class="op_single_selector"]
> * [端口](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Azure 资源管理器模板](create-cluster-database-resource-manager.md)

Azure 数据资源管理器是一种快速、完全托管的数据分析服务，用于实时分析来自应用程序、网站、IoT 设备等的大量数据流。 若要使用 Azure 数据资源管理器，首先要创建群集，并在该群集中创建一个或多个数据库。 然后，将数据引入（加载）到数据库中，以便可以对其运行查询。 本文介绍如何使用C#创建群集和数据库。

## <a name="prerequisites"></a>Prerequisites

* 如果尚未安装 Visual Studio 2019，可以下载并使用**免费**的[Visual Studio 2019 社区版](https://www.visualstudio.com/downloads/)。 请确保在 Visual Studio 安装过程中启用**Azure 开发**。
* 如果你没有 Azure 订阅，请在开始之前创建一个[免费的 azure 帐户](https://azure.microsoft.com/free/)。

## <a name="install-c-nuget"></a>安装C# Nuget

* 安装[Azure 数据资源管理器（Kusto） nuget 包](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)。
* 安装[system.identitymodel nuget 包](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)进行身份验证。

## <a name="authentication"></a>身份验证
为了运行本文中的示例，我们需要 Azure AD 应用程序和可访问资源的服务主体。 选中 "[创建 Azure AD 应用程序](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)" 以创建免费的 Azure AD 应用程序，并在订阅范围内添加角色分配。 它还演示如何获取 `Directory (tenant) ID`、`Application ID`和 `Client Secret`。

## <a name="create-the-azure-data-explorer-cluster"></a>创建 Azure 数据资源管理器群集

1. 使用以下代码创建群集：

    ```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
    var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
    var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
    var credential = new ClientCredential(clientId, clientSecret);
    var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

    var kustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = subscriptionId
    };

    var resourceGroupName = "testrg";
    var clusterName = "mykustocluster";
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var cluster = new Cluster(location, sku);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```

   |**设置** | **建议的值** | **字段说明**|
   |---|---|---|
   | clusterName | *mykustocluster* | 群集所需的名称。|
   | skuName | *Standard_D13_v2* | 将用于群集的 SKU。 |
   | 单层 | *标准* | SKU 层。 |
   | 功能 | *number* | 群集实例的数目。 |
   | resourceGroupName | *testrg* | 将在其中创建群集的资源组名称。 |

    > [!NOTE]
    > **创建群集**是一个长时间运行的操作，因此强烈建议使用 CreateOrUpdateAsync，而不是 CreateOrUpdate。 

1. 运行以下命令，检查是否已成功创建群集：

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

如果结果包含具有 `Succeeded` 值 `ProvisioningState`，则已成功创建群集。

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>在 Azure 数据资源管理器群集中创建数据库

1. 使用以下代码创建数据库：

    ```csharp
    var hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    var softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    var databaseName = "mykustodatabase";
    var database = new ReadWriteDatabase(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);

    await kustoManagementClient.Databases.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, database);
    ```

        [!NOTE]
        If you are using C# version 2.0.0 or below, use Database instead of ReadWriteDatabase.

   |**设置** | **建议的值** | **字段说明**|
   |---|---|---|
   | clusterName | *mykustocluster* | 要在其中创建数据库的群集的名称。|
   | Database | *mykustodatabase* | 数据库的名称。|
   | resourceGroupName | *testrg* | 将在其中创建群集的资源组名称。 |
   | softDeletePeriod | *3650:00:00:00* | 数据将保持可用于查询的时间量。 |
   | hotCachePeriod | *3650:00:00:00* | 数据将在缓存中保留的时间。 |

2. 运行以下命令以查看创建的数据库：

    ```csharp
    kustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName) as ReadWriteDatabase;
    ```

现在，你有了一个群集和一个数据库。

## <a name="clean-up-resources"></a>清理资源

* 如果你打算追随我们的其他文章，请保留你创建的资源。
* 若要清理资源，请删除群集。 删除群集时，它还会删除其中的所有数据库。 使用以下命令删除群集：

    ```csharp
    kustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>后续步骤

* [使用 Azure 数据资源管理器 .NET Standard SDK 引入数据（预览版）](net-standard-ingest-data.md)
