---
title: 使用 "数据的数据库" 功能在 Azure 中附加数据库数据资源管理器
description: 了解如何使用 "并行数据库" 功能在 Azure 数据资源管理器中附加数据库。
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 447e8a67cedbb8f78e4db9602f603fefd382693c
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162933"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>使用从动数据库在 Azure 中附加数据库数据资源管理器

使用 "**数据库" 数据库**功能，可以将位于不同群集中的数据库附加到 Azure 数据资源管理器群集。 并行**数据库**以*只读*模式附加，使查看数据和对引入**数据库**中的数据运行查询成为可能。 该并行数据库同步领导数据库中的更改。 由于同步，数据的数据可用性中有几秒钟到几分钟的数据延迟。 时间延迟的长度取决于负责人数据库元数据的总体大小。 领导数据库和使用者数据库使用相同的存储帐户来提取数据。 存储由领导者数据库拥有。 "数据访问数据库" 查看数据，无需引入。 由于附加的数据库是只读数据库，因此不能修改数据库中的数据、表和策略，除非[缓存策略](#configure-caching-policy)、[主体](#manage-principals)和[权限](#manage-permissions)。 无法删除附加的数据库。 它们必须由前导者或使用者分离，然后才能将其删除。 

使用 "使用情况" 功能将数据库附加到不同的群集，用作在组织和团队之间共享数据的基础结构。 此功能可用于隔离计算资源，以保护生产环境免受非生产用例的作用。 还可用于将 Azure 数据资源管理器群集的成本关联到对数据运行查询的参与方。

## <a name="which-databases-are-followed"></a>遵循哪些数据库？

* 一个群集可以跟一个数据库、多个数据库或领导者群集的所有数据库。 
* 单个群集可以遵循多个负责人群集中的数据库。 
* 一个群集可以同时包含一个使用者数据库和领导者数据库

## <a name="prerequisites"></a>先决条件

1. 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。
1. 为领导和执行程序[创建群集和 DB](/azure/data-explorer/create-cluster-database-portal) 。
1. 使用[引入概述](/azure/data-explorer/ingest-data-overview)中讨论的各种方法之一将[数据](/azure/data-explorer/ingest-sample-data)引入到领导者数据库。

## <a name="attach-a-database"></a>附加数据库

可以使用多种方法附加数据库。 本文介绍如何使用C#或 Azure 资源管理器模板附加数据库。 若要附加数据库，必须对领导群集和从动机构群集具有权限。 有关权限的详细信息，请参阅[管理权限](#manage-permissions)。

### <a name="attach-a-database-using-c"></a>使用附加数据库C#

#### <a name="needed-nugets"></a>需要 Nuget

* 请安装[kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)。
* 安装[ClientRuntime 进行身份验证](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication)。

#### <a name="code-example"></a>代码示例

```Csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "followerResouceGroup";
var leaderResourceGroup = "leaderResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
var attachedDatabaseConfigurationName = "uniqueNameForAttachedDatabaseConfiguration";
var databaseName = "db"; // Can be specific database name or * for all databases
var defaultPrincipalsModificationKind = "Union"; 
var location = "North Central US";

AttachedDatabaseConfiguration attachedDatabaseConfigurationProperties = new AttachedDatabaseConfiguration()
{
    ClusterResourceId = $"/subscriptions/{leaderSubscriptionId}/resourceGroups/{leaderResourceGroup}/providers/Microsoft.Kusto/Clusters/{leaderClusterName}",
    DatabaseName = databaseName,
    DefaultPrincipalsModificationKind = defaultPrincipalsModificationKind,
    Location = location
};

var attachedDatabaseConfigurations = resourceManagementClient.AttachedDatabaseConfigurations.CreateOrUpdate(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationName, attachedDatabaseConfigurationProperties);
```

### <a name="attach-a-database-using-python"></a>使用 Python 附加数据库

#### <a name="needed-modules"></a>所需模块

```
pip install azure-common
pip install azure-mgmt-kusto
```

#### <a name="code-example"></a>代码示例

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import AttachedDatabaseConfiguration
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
leader_resouce_group_name = "leaderResouceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_Configuration_name = "uniqueNameForAttachedDatabaseConfiguration"
database_name  = "db" # Can be specific database name or * for all databases
default_principals_modification_kind  = "Union"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + leader_subscription_id + "/resourceGroups/" + leader_resouce_group_name + "/providers/Microsoft.Kusto/Clusters/" + leader_cluster_name

attached_database_configuration_properties = AttachedDatabaseConfiguration(cluster_resource_id = cluster_resource_id, database_name = database_name, default_principals_modification_kind = default_principals_modification_kind, location = location)

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.create_or_update(follower_resource_group_name, follower_cluster_name, attached_database_Configuration_name, attached_database_configuration_properties)
```

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板附加数据库

本部分介绍如何使用[Azure 资源管理器模板](../azure-resource-manager/management/overview.md)将数据库附加到现有的群集。 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the cluster to which the database will be attached."
            }
        },
        "attachedDatabaseConfigurationsName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the attached database configurations to create."
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The name of the database to follow. You can follow all databases by using '*'."
            }
        },
        "leaderClusterResourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of the leader cluster."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The default principal modification kind."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('attachedDatabaseConfigurationsName')]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Kusto/clusters', parameters('followerClusterName'))]"
            ],
            "properties": {
                "databaseName": "[parameters('databaseName')]",
                "clusterResourceId": "[parameters('leaderClusterResourceId')]",
                "defaultPrincipalsModificationKind": "[parameters('defaultPrincipalsModificationKind')]"
            }
        }
    ]
}
```

### <a name="deploy-the-template"></a>部署模板 

可以通过[使用 Azure 门户](https://portal.azure.com)或使用 powershell 来部署 Azure 资源管理器模板。

   ![模板部署](media/follower/template-deployment.png)


|**设置**  |**说明**  |
|---------|---------|
|从动群集名称     |  从动群集的名称;模板将部署到的位置。  |
|附加的数据库配置名称    |    附加的数据库配置对象的名称。 该名称可以是在群集级别唯一的任意字符串。     |
|数据库名称     |      要遵循的数据库的名称。 如果要跟踪领导的所有数据库，请使用 "*"。   |
|领导群集资源 ID    |   领导者群集的资源 ID。      |
|默认主体修改种类    |   默认主体修改类型。 可以是 `Union`、`Replace` 或 `None`。 有关默认主体修改类型的详细信息，请参阅[principal 修改 kind control 命令](/azure/kusto/management/cluster-follower?branch=master#alter-follower-database-principals-modification-kind)。      |
|Location   |   所有资源的位置。 领导者和从动者必须位于同一位置。       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>验证数据库是否已成功附加

若要验证是否已成功附加数据库，请在[Azure 门户](https://portal.azure.com)中查找附加的数据库。 

1. 导航到 "从动群集" 并选择 "**数据库**"
1. 在数据库列表中搜索新的只读数据库。

    ![只读数据库](media/follower/read-only-follower-database.png)

或者：

1. 导航到主持人群集并选择 "**数据库**"
2. 检查相关数据库是否被标记为**与他人共享** > **是**

    ![读取和写入附加的数据库](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>使用分离该数据库C# 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>从从动群集分离连接的从动数据库

从动群集可以分离任何附加的数据库，如下所示：

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var followerClusterName = "follower";
var attachedDatabaseConfigurationsName = "uniqueName";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>从领导者群集中分离附加的数据库

领导者群集可以分离任何附加的数据库，如下所示：

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = leaderSubscriptionId
};

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
//The cluster and database that are created as part of the Prerequisites
var followerDatabaseDefinition = new FollowerDatabaseDefinition()
    {
        AttachedDatabaseConfigurationName = "uniqueName",
        ClusterResourceId = $"/subscriptions/{followerSubscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

## <a name="detach-the-follower-database-using-python"></a>使用 Python 分离使用的数据库

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>从从动群集分离连接的从动数据库

从动群集可以分离任何附加的数据库，如下所示：

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
follower_cluster_name = "follower"
attached_database_configurationName = "uniqueName"

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.delete(follower_resource_group_name, follower_cluster_name, attached_database_configurationName)
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>从领导者群集中分离附加的数据库

领导者群集可以分离任何附加的数据库，如下所示：

```python

from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import FollowerDatabaseDefinition
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResourceGroup"
leader_resource_group_name = "leaderResourceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_configuration_name = "uniqueName"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + follower_subscription_id + "/resourceGroups/" + follower_resource_group_name + "/providers/Microsoft.Kusto/Clusters/" + follower_cluster_name


#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.clusters.detach_follower_databases(resource_group_name = leader_resource_group_name, cluster_name = leader_cluster_name, cluster_resource_id = cluster_resource_id, attached_database_configuration_name = attached_database_configuration_name)
```

## <a name="manage-principals-permissions-and-caching-policy"></a>管理主体、权限和缓存策略

### <a name="manage-principals"></a>管理主体

附加数据库时，请指定 **"默认主体修改类型"** 。 默认情况下，保留[授权主体](/azure/kusto/management/access-control/index#authorization)的领导数据库集合

|**种类** |**说明**  |
|---------|---------|
|**Union**     |   附加的数据库主体将始终包含原始数据库主体以及添加到该数据源数据库的额外新主体。      |
|**替换**   |    不会从原始数据库继承主体。 必须为附加的数据库创建新的主体。     |
|无   |   附加的数据库主体只包含原始数据库的主体，而没有其他主体。      |

有关使用控制命令配置授权主体的详细信息，请参阅[用于管理使用者群集的控制命令](/azure/kusto/management/cluster-follower)。

### <a name="manage-permissions"></a>管理权限

对于所有数据库类型，管理只读数据库权限都是相同的。 请参阅[Azure 门户中的管理权限](/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal)。

### <a name="configure-caching-policy"></a>配置缓存策略

数据库管理员可以在宿主群集上修改附加数据库或其任何表的[缓存策略](/azure/kusto/management/cache-policy)。 默认情况下，保留数据库和表级缓存策略的领导者数据库集合。 例如，你可以在领导者数据库上有一个30天的缓存策略用于运行每月报表，并在一天的数据库中使用三天缓存策略来仅查询最新的数据进行故障排除。 若要详细了解如何使用控制命令来配置对数据库或表的缓存策略，请参阅[控制用于管理从动群集的命令](/azure/kusto/management/cluster-follower)。

## <a name="limitations"></a>限制

* 必须在同一区域中进行并行和领导群集。
* 不能在要遵循的数据库上使用[流式引入](/azure/data-explorer/ingest-data-streaming)。
* 使用[客户托管密钥](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault)的数据加密在领导和使用者群集上不受支持。 
* 分离数据库之前，无法删除附加到其他群集的数据库。
* 分离群集之前，无法删除已附加到其他群集的群集。
* 不能停止已连接的使用者或领导者数据库的群集。 

## <a name="next-steps"></a>后续步骤

* 有关使用情况群集配置的详细信息，请参阅[用于管理使用情况群集的控制命令](/azure/kusto/management/cluster-follower)。
