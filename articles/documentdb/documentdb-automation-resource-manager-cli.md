---
title: "Azure Cosmos DB 自动化 - Azure CLI 2.0 | Microsoft Docs"
description: "使用 Azure CLI 2.0 创建并管理 Azure Cosmos DB 帐户。 Azure Cosmos DB 是高度可用的全球分布式数据库。"
services: cosmosdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 6158c27f-6b9a-404e-a234-b5d48c4a5b29
ms.custom: quick start create
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/20/2017
ms.author: dimakwan
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6150914e623cb6c54f1257c772be62150ab81e17
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="create-an-azure-cosmos-db-account-using-the-azure-cli"></a>使用 Azure CLI 创建 Azure Cosmos DB 帐户

以下指南介绍了使用 Azure CLI 2.0 中提供的预览版命令自动管理 Azure Cosmos DB 数据库帐户的命令。 还介绍了用于管理[多区域数据库帐户][scaling-globally]中的帐户密钥和故障转移优先级的命令。 更新数据库帐户可以修改一致性策略以及添加/删除区域。 对于 Azure Cosmos DB 数据库帐户的跨平台管理，可使用 [Azure Powershell](documentdb-manage-account-with-powershell.md)、[资源提供程序 REST API][rp-rest-api] 或 [Azure 门户](documentdb-create-account.md)。

## <a name="getting-started"></a>入门

按照[如何安装和配置 Azure CLI 2.0][install-az-cli2] 中的说明使用 Azure CLI 2.0 设置开发环境。

执行以下命令并按照屏幕上的步骤登录到 Azure 帐户。

```azurecli
az login
```

如果目前没有[资源组](../azure-resource-manager/resource-group-overview.md#resource-groups)，请创建资源组：

```azurecli
az group create --name <resourcegroupname> --location <resourcegrouplocation>
az group list
```

`<resourcegrouplocation>` 必须是已正式推出 Azure Cosmos DB 的区域之一。 [Azure 区域页面](https://azure.microsoft.com/regions/#services)提供当前的区域列表。

### <a name="notes"></a>说明

* 执行“az documentdb -h”可获取可用命令的完整列表，或访问[参考页][az-documentdb-ref]。
* 执行“az documentdb &lt;command&gt; -h”可获取每个命令的必需和可选参数的详细信息列表。

## <a name="register-your-subscription-to-use-azure-cosmos-db"></a>注册订阅以使用 Azure Cosmos DB

此命令注册订阅，通过 CLI 使用 Azure Cosmos DB。

```azurecli
az provider register -n Microsoft.DocumentDB 
```

## <a id="create-documentdb-account-cli"></a>创建 Azure Cosmos DB 数据库帐户

此命令可创建 Azure Cosmos DB 数据库帐户。 可以将新数据库帐户配置为具有特定[一致性策略](documentdb-consistency-levels.md)的单区域或[多区域][scaling-globally]。

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account. The account 
                                    name must be unique.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the Azure Cosmos DB database account.
                                    Allowed values: BoundedStaleness, Eventual, Session, Strong.
    --ip-range-filter             : Firewall support. Specifies the set of IP addresses or IP
                                    address ranges in CIDR form to be included as the allowed list
                                    of client IPs for a given database account. IP addresses/ranges
                                    must be comma separated and must not contain any spaces.
    --kind                        : The type of Azure Cosmos DB database account to create.  Allowed
                                    values: GlobalDocumentDB, MongoDB, Parse.  Default:
                                    GlobalDocumentDB.
    --locations                   : Space separated locations in 'regionName=failoverPriority'
                                    format. E.g "East US"=0 "West US"=1. Failover priority values
                                    are 0 for write regions and greater than 0 for read regions. A
                                    failover priority value must be unique and less than the total
                                    number of regions. Default: single region account in the
                                    location of the specified resource group.
    --max-interval                : When used with Bounded Staleness consistency, this value
                                    represents the time amount of staleness (in seconds) tolerated.
                                    Accepted range for this value is 1 - 100.  Default: 5.
    --max-staleness-prefix        : When used with Bounded Staleness consistency, this value
                                    represents the number of stale requests tolerated. Accepted
                                    range for this value is 1 - 2,147,483,647.  Default: 100.
```

```azurecli
az documentdb create -g <resourcegroupname> -n <uniquedocumentdbaccountname> --kind <typeofdatabaseaccount>
```

示例: 

    az documentdb create -g rg-test -n docdb-test
    az documentdb create -g rg-test -n docdb-test --kind MongoDB
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 "South Central US"=2
    az documentdb create -g rg-test -n docdb-test --ip-range-filter "13.91.6.132,13.91.6.1/24"
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 --default-consistency-level BoundedStaleness --max-interval 10 --max-staleness-prefix 200

### <a name="notes"></a>说明 
* 位置必须是已正式推出 Azure Cosmos DB 的区域。 [Azure 区域页面](https://azure.microsoft.com/regions/#services)提供当前的区域列表。
* 若要启用门户访问，请在 ip-range-filter 中包含你所在区域的 Azure 门户的 IP 地址（按照[配置 IP 访问控制策略](documentdb-firewall-support.md#configure-ip-policy)中的指定）。

## <a id="update-documentdb-account-cli"></a>更新 Azure Cosmos DB 数据库帐户

此命令可更新 Azure Cosmos DB 数据库帐户属性。 这包括一致性策略和数据库帐户所在的位置。

> [!NOTE]
> 此命令可添加和删除区域，但不可修改故障转移优先级。 若要修改故障转移优先级，请参阅[以下内容](#modify-failover-priority-powershell)。

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the Azure Cosmos DB database account.
                                    Allowed values: BoundedStaleness, Eventual, Session, Strong.
    --ip-range-filter             : Firewall support. Specifies the set of IP addresses or IP address
                                    ranges in CIDR form to be included as the allowed list of client
                                    IPs for a given database account. IP addresses/ranges must be comma
                                    separated and must not contain any spaces.
    --locations                   : Space separated locations in 'regionName=failoverPriority' format.
                                    E.g "East US"=0. Failover priority values are 0 for write regions
                                    and greater than 0 for read regions. A failover priority value must
                                    be unique and less than the total number of regions.
    --max-interval                : When used with Bounded Staleness consistency, this value represents
                                    the time amount of staleness (in seconds) tolerated. Accepted range
                                    for this value is 1 - 100.
    --max-staleness-prefix        : When used with Bounded Staleness consistency, this value represents
                                    the number of stale requests tolerated. Accepted range for this
                                    value is 1 - 2,147,483,647.
```

示例: 

    az documentdb update -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 "South Central US"=2
    az documentdb update -g rg-test -n docdb-test --ip-range-filter "13.91.6.132,13.91.6.1/24"
    az documentdb update -g rg-test -n docdb-test --default-consistency-level BoundedStaleness --max-interval 10 --max-staleness-prefix 200

## <a id="add-remove-region-documentdb-account-cli"></a>从 Azure Cosmos DB 数据库帐户中添加删除区域

若要在现有 Azure Cosmos DB 数据库帐户中添加或删除区域，请使用带 `--locations` 标志的 [update](#update-documentdb-account-cli) 命令。 以下示例演示如何创建新帐户，随后在该帐户中添加和删除区域。

示例：

    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1
    az documentdb update -g rg-test -n docdb-test --locations "East US"=0 "North Europe"=1 "South Central US"=2


## <a id="delete-documentdb-account-cli"></a>删除 Azure Cosmos DB 数据库帐户

此命令可删除现有 Azure Cosmos DB 数据库帐户。

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

示例：

    az documentdb delete -g rg-test -n docdb-test

## <a id="get-documentdb-properties-cli"></a>获取 Azure Cosmos DB 数据库帐户的属性

此命令可获取现有 Azure Cosmos DB 数据库帐户的属性。

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

示例：

    az documentdb show -g rg-test -n docdb-test

## <a id="list-account-keys-cli"></a> 列出帐户密钥

创建 Azure Cosmos DB 帐户时，服务生成两个主访问密钥，可用于访问 Azure Cosmos DB 帐户时的身份验证。 提供两个访问密钥后，Azure Cosmos DB 支持在不中断 Azure Cosmos DB 帐户连接的情况下重新生成密钥。 还可以使用只读密钥，用于对只读操作进行身份验证。 有两个读写密钥（主密钥和辅助密钥）和两个只读密钥（主密钥和辅助密钥）。

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

示例：

    az documentdb list-keys -g rg-test -n docdb-test

## <a id="list-connection-strings-cli"></a> 列出连接字符串

对于 MongoDB 帐户，可以使用以下命令检索将 MongoDB 应用连接到数据库帐户的连接字符串。

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

示例：

    az documentdb list-connection-strings -g rg-test -n docdb-test

## <a id="regenerate-account-key-cli"></a> 重新生成帐户密钥

应定期更改 Azure Cosmos DB 帐户访问密钥，使连接更安全。 将分配两个访问密钥，从而可以在使用一个访问密钥保持连接到 Azure Cosmos DB 帐户的同时，再生成另一个访问密钥。

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
    --key-kind          [Required]: The access key to regenerate.  Allowed values: primary, primaryReadonly,
                                    secondary, secondaryReadonly.
```

示例：

    az documentdb regenerate-key -g rg-test -n docdb-test --key-kind secondary

## <a id="modify-failover-priority-cli"></a>修改 Azure Cosmos DB 数据库帐户的故障转移优先级

对于多区域数据库帐户，可以更改 Azure Cosmos DB 数据库帐户所在的各个区域的故障转移优先级。 有关 Azure Cosmos DB 数据库帐户中故障转移的详细信息，请参阅[使用 Azure Cosmos DB 全球分布数据](documentdb-distribute-data-globally.md)。

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
    --failover-policies [Required]: Space separated failover policies in 'regionName=failoverPriority' format.
                                    E.g "East US"=0 "West US"=1.
```

示例：

    az documentdb failover-priority-change "East US"=1 "West US"=0 "South Central US"=2

## <a name="next-steps"></a>后续步骤

* 若要使用 .NET 进行连接，请参阅[使用 .NET 进行连接和查询](../cosmos-db/create-documentdb-dotnet.md)。
* 若要使用 .NET Core 进行连接，请参阅[使用 .NET Core 进行连接和查询](../cosmos-db/create-documentdb-dotnet-core.md)。
* 若要使用 Node.js 进行连接，请参阅[使用 Node.js 和 MongoDB 应用进行连接和查询](../cosmos-db/create-mongodb-nodejs.md)。

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[scaling-globally]: https://azure.microsoft.com/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet
[install-az-cli2]: https://docs.microsoft.com/cli/azure/install-az-cli2
[az-documentdb-ref]: https://docs.microsoft.com/cli/azure/documentdb
[az-documentdb-create-ref]: https://docs.microsoft.com/cli/azure/documentdb#create
[rp-rest-api]: https://docs.microsoft.com/rest/api/documentdbresourceprovider/

