---
title: 迁移到群集配置-Azure HDInsight 的精细基于角色的访问
description: 了解如何迁移到群集配置的精细基于角色的访问所需的更改。
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 0422d848ccdf9ba82e68813de64eec863ee4ad29
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006227"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>迁移到群集配置的精细基于角色的访问

我们引入了一些重要的更改，以支持多个细粒度的基于角色的访问以获取敏感信息。 当属于这些更改时，某些**可能需要操作**如果你正在使用它的[受影响的实体/方案](#am-i-affected-by-these-changes)。

## <a name="what-is-changing"></a>有什么变化？

以前，机密可以获得通过 HDInsight API 由群集用户拥有所有者、 参与者或读者[RBAC 角色](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)。
今后，这些机密将不再具有读取者角色的用户可以访问。 我们还将通过引入新的 HDInisght 群集操作员角色是能检索机密，而不被授予管理权限的参与者或所有者。 总结：

| 角色                                  | 以前                                                                                       | Now       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| 读取器                                | -读取访问，包括机密                                                                   | -读取访问权限，**不包括**机密 |           |   |   |
| HDInsight 群集运算符<br>（新的角色） | 不适用                                                                                              | -读/写访问权限，包括机密         |   |   |
| 参与者                           | -读/写访问权限，包括机密<br>-创建和管理所有类型的 Azure 资源。     | 无变化 |
| 所有者                                 | -读/写访问权限包括机密<br>的对所有资源完全访问权限<br>-委派给其他人的访问权限 | 无变化 |

了解如何将 HDInsight 群集操作员角色分配添加到用户以授予他们读/写访问权限对群集密钥，请参阅以下部分中，[将 HDInsight 群集操作员角色分配添加到用户](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)。

## <a name="am-i-affected-by-these-changes"></a>我会受到这些更改？

以下实体和方案将受到影响：

- [API](#api)：使用的用户`/configurations`或`/configurations/{configurationName}`终结点。
- [适用于 Visual Studio Code 的 azure HDInsight 工具](#azure-hdinsight-tools-for-visual-studio-code)版本 ___ 和更低。
- [用于 IntelliJ 的 azure 工具包](#azure-toolkit-for-intellij)版本 __ 和更低。
- [用于 Eclipse 的 azure 工具包](#azure-toolkit-for-eclipse)版本 __ 和更低。
- [用于.NET 的 SDK](#sdk-for-net)
    - [版本 1.x 或 2.x](#versions-1x-and-2x):使用的用户`GetClusterConfigurations`， `GetConnectivitySettings`， `ConfigureHttpSettings`，`EnableHttp`或`DisableHttp`ConfigurationsOperationsExtensions 类的方法。
    - [版本 3.x 及更高版本](#versions-3x-and-up):使用的用户`EnableHttp`， `DisableHttp`， `Update`，或`Get`方法从`ConfigurationsOperationsExtensions`类。
- [用于 Python 的 SDK](#sdk-for-python):使用的用户`get`或`update`ConfigurationsOperations 类的方法。
- [用于 Java 的 SDK](#sdk-for-java):使用的用户`update`或`get`ConfigurationsInner 类的方法。
- [SDK for Go](#sdk-for-go):使用的用户`Get`或`Update`ConfigurationsClient 结构中的方法。

下面各部分 （或使用更高版本的链接） 若要查看迁移的步骤为你的方案，请参阅。

### <a name="api"></a>API

将更改或不推荐使用以下 Api:

- [**GET /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) （删除敏感信息） https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations/{configurationName}?api-version={api-version}
    - 以前用于获取单独的配置类型 （包括机密）。
    - 此 API 调用现在将包含机密省略返回单独的配置类型。 若要获取所有配置，包括机密，使用新[POST /configurations]()调用。 若要获取只是网关设置，请使用新[POST /getGatewaySettings]()调用。
- [**GET /configurations** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configurations) （已弃用） https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations?api-version={api-version}
    - 之前用于获取所有配置 （包括机密）
    - 将不再支持此 API 调用。 若要获取今后的所有配置，请使用新[POST /configurations]()调用。 若要使用省略的敏感参数获取配置，请使用[GET /configurations/ {configurationName}]()调用。
- [**POST /configurations/{configurationName}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#change-connectivity-settings) (deprecated) https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations/{configurationName}?api-version={api-version}
    - 以前用于更新网关凭据。
    - 将弃用，不再支持此 API 调用。 使用新[POST /updateGatewaySettings]()相反。

已添加 Api 以下替换：</span>

- **POST /configurations** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations?api-version={api-version}
    - 使用此 API 来获取所有配置，包括机密。
- **POST /getGatewaySettings** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/getGatewaySettings?api-version={api-version}
    - 使用此 API 来获取网关设置。
- **POST /updateGatewaySettings** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/updateGatewaySettings?api-version={api-version}
    - 使用此 API 更新网关设置 （用户名和/或密码）。

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>用于 Visual Studio Code 的 azure HDInsight 工具

如果使用版本 1.1.1 或更低版本，请更新到[最新版本的 Azure HDInsight Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false)以避免中断。

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ

如果使用版本 3.21.0 或更低版本，请更新到[最新版本的 Azure Toolkit for IntelliJ 插件](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij)以避免中断。

### <a name="azure-toolkit-for-eclipse"></a>用于 Eclipse 的 Azure 工具包

如果使用 2019年-03-29 版本或更低版本，请更新到最新版本的 Azure Toolkit for Eclipse 以避免中断。

### <a name="sdk-for-net"></a>用于 .NET 的 SDK

#### <a name="versions-1x-and-2x"></a>版本 1.x 和 2.x

请更新到[版本 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) HDInsight sdk for.NET。 如果要使用这些更改影响的方法，则可能需要最少的代码修改：

- `ClusterOperationsExtensions.GetClusterConfigurations` 将**不再返回敏感参数**，例如存储密钥 （core 站点） 或 HTTP 凭据 （网关）。
    - 若要检索所有配置，包括敏感参数使用`ClusterOperationsExtensions.ListConfigurations`今后。  请注意，具有读取者角色的用户将不能使用此方法。 这样便可以精细控制哪些用户可以访问群集的敏感信息。
    - 若要检索仅 HTTP 网关凭据，请使用`ClusterOperationsExtensions.GetGatewaySettings`。

- `ClusterOperationsExtensions.GetConnectivitySettings` 现已弃用，已由`ClusterOperationsExtensions.GetGatewaySettings`。

- `ClusterOperationsExtensions.ConfigureHttpSettings` 现已弃用，已由`ClusterOperationsExtensions.UpdateGatewaySettings`。

- `ConfigurationsOperationsExtensions.EnableHttp` 和`DisableHttp`现已弃用。 现在始终启用 HTTP，因此不再需要这些方法。

#### <a name="versions-3x-and-up"></a>版本 3.x 及更高版本

请更新到[5.0.0 版](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0)HDInsight sdk for.NET。 如果要使用这些更改影响的方法，则可能需要最少的代码修改：

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) 将**不再返回敏感参数**，例如存储密钥 （core 站点） 或 HTTP 凭据 （网关）。
    - 若要检索所有配置，包括敏感参数使用`ConfigurationOperationsExtensions.List`今后。  请注意，具有读取者角色的用户将不能使用此方法。 这样便可以精细控制哪些用户可以访问群集的敏感信息。 
    - 若要检索仅 HTTP 网关凭据，请使用`ClusterOperationsExtensions.GetGatewaySettings`。 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) 现已弃用，已由`ClusterOperationsExtensions.UpdateGatewaySettings`。 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) 并[ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet)现已弃用。 现在始终启用 HTTP，因此不再需要这些方法。

### <a name="sdk-for-python"></a>用于 Python 的 SDK

请更新到[1.0.0 版](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/)HDInsight sdk for Python。 如果要使用这些更改影响的方法，则可能需要最少的代码修改：

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) 将**不再返回敏感参数**，例如存储密钥 （core 站点） 或 HTTP 凭据 （网关）。
    - 若要检索所有配置，包括敏感参数，使用[ `ConfigurationsOperations.list` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)今后。  请注意，具有读取者角色的用户将不能使用此方法。 这样便可以精细控制哪些用户可以访问群集的敏感信息。 
    - 若要检索仅 HTTP 网关凭据，请使用[ `ConfigurationsOperations.get_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)。
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-resource-group-name--cluster-name--tags-none--custom-headers-none--raw-false----operation-config-) 现已弃用，已被取代[ `ClusterOperationsExtensions.update_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)。

### <a name="sdk-for-java"></a>用于 Java 的 SDK

请更新到[1.0.0 版](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/)HDInsight sdk for Java。 如果要使用这些更改影响的方法，则可能需要最少的代码修改：

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) 将**不再返回敏感参数**，例如存储密钥 （core 站点） 或 HTTP 凭据 （网关）。
    - 若要检索所有配置，包括敏感参数使用`ConfigurationsInner.list`今后。  请注意，具有读取者角色的用户将不能使用此方法。 这样便可以精细控制哪些用户可以访问群集的敏感信息。 
    - 若要检索仅 HTTP 网关凭据，请使用`ConfigurationsOperations.get_gateway_settings`。
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) 现已弃用，已由`ClusterOperationsExtensions.update_gateway_settings`。

### <a name="sdk-for-go"></a>SDK For Go

请更新到[版本 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight)的 HDInsight SDK for Go。 如果要使用这些更改影响的方法，则可能需要最少的代码修改：

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) 将**不再返回敏感参数**，例如存储密钥 （core 站点） 或 HTTP 凭据 （网关）。
    - 若要检索所有配置，包括敏感参数，使用[ `ConfigurationsClient.list` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List)今后。  请注意，具有读取者角色的用户将不能使用此方法。 这样便可以精细控制哪些用户可以访问群集的敏感信息。 
    - 若要检索仅 HTTP 网关凭据，请使用[ `ClustersClient.get_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings)。
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) 现已弃用，已被取代[ `ClustersClient.update_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings)。

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>将 HDInsight 群集操作员角色分配添加到用户

具有的用户[参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor)或[所有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)角色可授予给用户的 HDInsight 群集操作员角色，你想要具有读/写访问权限对 HDInsight 群集机密等群集网关凭据和存储帐户密钥。

### <a name="using-the-azure-cli"></a>使用 Azure CLI

若要添加此角色分配的最简单方法是通过 Azure CLI 中使用以下命令：

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

> [!NOTE]
> 此命令必须由用户具有运行参与者或所有者角色，因为只有他们可以授予这些权限。 `--assignee`是要向其分配的 HDInsight 群集操作员角色的用户的电子邮件地址。

上面的命令授予此角色在订阅级别。 若要改为只需授予此角色在资源组级别，可以修改该命令如下所示：

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

### <a name="using-the-azure-portal"></a>使用 Azure 门户

或者，可以使用 Azure 门户将 HDInsight 群集操作员角色分配添加到用户。 请参阅本文档中，[访问管理对 Azure 资源使用 RBAC 和 Azure 门户-添加角色分配](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)。
