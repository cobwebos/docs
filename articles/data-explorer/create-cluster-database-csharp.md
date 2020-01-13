---
title: 使用 C# 创建 Azure 数据资源管理器群集和数据库
description: 了解如何使用 C# 创建 Azure 数据资源管理器群集和数据库
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 7dc032d52a8cb3c5c54cf57c7ae7bf697796b5cc
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910605"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>使用 C# 创建 Azure 数据资源管理器群集和数据库

> [!div class="op_single_selector"]
> * [门户](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Azure Resource Manager 模板](create-cluster-database-resource-manager.md)

Azure 数据资源管理器是一项快速、完全托管的数据分析服务，可实时分析从应用程序、网站和 IoT 设备等流式传输的海量数据。 若要使用 Azure 数据资源管理器，请先创建群集，再在该群集中创建一个或多个数据库。 然后将数据引入（加载）到数据库，以便对其运行查询。 本文介绍如何使用C#创建群集和数据库。

## <a name="prerequisites"></a>必备组件

* 如果尚未安装 Visual Studio 2019，可以下载并使用**免费**的[Visual Studio 2019 社区版](https://www.visualstudio.com/downloads/)。 在安装 Visual Studio 的过程中，请确保启用“Azure 开发”。
* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。

## <a name="install-c-nuget"></a>安装C# Nuget

* 安装 [Azure 数据资源管理器 (Kusto) nuget 包](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)。
* 安装 [Microsoft.IdentityModel.Clients.ActiveDirectory nuget 包](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)以进行身份验证。

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
   | clusterName | mykustocluster | 所需的群集名称。|
   | skuName | *Standard_D13_v2* | 将用于群集的 SKU。 |
   | 层 | *Standard* | SKU 层。 |
   | 容量 | *数字* | 群集实例的数目。 |
   | resourceGroupName | *testrg* | 将在其中创建群集的资源组名称。 |

    > [!NOTE]
    > **创建群集**是一个长时间运行的操作，因此强烈建议使用 CreateOrUpdateAsync，而不是 CreateOrUpdate。 

1. 运行以下命令，检查群集是否已成功创建：

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

如果结果包含带 `Succeeded` 值的 `ProvisioningState`，则表示已成功创建群集。

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>在 Azure 数据资源管理器群集中创建数据库

1. 使用以下代码创建数据库：

    ```csharp
    var hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    var softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    var databaseName = "mykustodatabase";
    var database = new Database(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);

    await kustoManagementClient.Databases.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, database);
    ```

   |**设置** | **建议的值** | **字段说明**|
   |---|---|---|
   | clusterName | mykustocluster | 将在其中创建数据库的群集的名称。|
   | databaseName | mykustodatabase | 数据库名称。|
   | resourceGroupName | *testrg* | 将在其中创建群集的资源组名称。 |
   | softDeletePeriod | *3650:00:00:00* | 供查询使用的数据的保留时间。 |
   | hotCachePeriod | *3650:00:00:00* | 数据将在缓存中保留的时间。 |

2. 若要查看已创建的数据库，请运行以下命令：

    ```csharp
    kustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName);
    ```

现在，你有了一个群集和一个数据库。

## <a name="clean-up-resources"></a>清理资源

* 如果你打算追随我们的其他文章，请保留你创建的资源。
* 若要清理资源，请删除群集。 删除群集时，也会删除其中的所有数据库。 使用以下命令删除群集：

    ```csharp
    kustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>后续步骤

* [使用 Azure 数据资源管理器 .NET Standard SDK（预览版）引入数据](net-standard-ingest-data.md)
