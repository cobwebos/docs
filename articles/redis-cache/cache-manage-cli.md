---
title: "管理使用 Azure CLI 的 Azure Redis 缓存 |Microsoft 文档"
description: "了解如何在任何平台上安装 Azure CLI、 如何使用它来连接到你的 Azure 帐户，以及如何创建和管理 Redis 缓存从 Azure CLI。"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 964ff245-859d-4bc1-bccf-62e4b3c1169f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: sdanie
ms.openlocfilehash: ba078a870a3998568170cc197bd6698b97b7fadb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-create-and-manage-azure-redis-cache-using-the-azure-command-line-interface-azure-cli"></a>如何创建和管理 Azure Redis 缓存使用 Azure 命令行界面 (Azure CLI)
> [!div class="op_single_selector"]
> * [PowerShell](cache-howto-manage-redis-cache-powershell.md)
> * [Azure CLI](cache-manage-cli.md)
>
>

Azure CLI 是从任何平台管理 Azure 基础结构的好办法。 这篇文章演示了如何创建和管理你使用 Azure CLI 的 Azure Redis 缓存实例。

> [!NOTE]
> 本文适用于以前版本的 Azure CLI。 有关最新的 Azure CLI 2.0 示例脚本，请参阅[Azure CLI Redis cache 示例](cli-samples.md)。
> 
> 

## <a name="prerequisites"></a>必备条件
若要创建和管理使用 Azure CLI 的 Azure Redis 缓存实例，必须完成以下步骤。

* 你必须具有 Azure 帐户。 如果你没有帐户，则可以创建[免费帐户](https://azure.microsoft.com/pricing/free-trial/)只需几分钟。
* [安装 Azure CLI](../cli-install-nodejs.md)。
* 连接你的 Azure CLI 安装与个人 Azure 帐户或者工作或学校 Azure 帐户，并从 Azure CLI 使用登录`azure login`命令。 若要了解差别并进行选择，请参阅[连接到 Azure 订阅从 Azure 命令行界面 (Azure CLI)](../xplat-cli-connect.md)。
* 在运行任何以下命令之前, 将 Azure CLI 切换到资源管理器模式通过运行`azure config mode arm`命令。 有关详细信息，请参阅[使用 Azure CLI 管理 Azure 资源和资源组](../xplat-cli-azure-resource-manager.md)。

## <a name="redis-cache-properties"></a>Redis 缓存属性
创建和更新 Redis 缓存实例时，将使用以下属性。

| 属性 | 开关 | 描述 |
| --- | --- | --- |
| name |-n，--名称 |Redis 缓存的名称。 |
| 资源组 |-g、--资源组 |资源组的名称。 |
| 位置 |-l、--location |若要创建缓存的位置。 |
| 大小 |-z，--大小 |Redis 缓存的大小。 有效值: [C0、 C1、 C2、 C3，C4，C5，C6、 P1、 P2、 P3、 P4] |
| Sku |-x，-sku |Redis SKU。 应为之一: [基本、 标准、 高级] |
| enableNonSslPort |-e，--启用的非 ssl-端口 |Redis 缓存的 EnableNonSslPort 属性。 如果你想要启用非 SSL 端口，以便你的缓存进行，添加此标志 |
| Redis 配置 |-c，--redis 配置 |Redis 配置。 输入配置项和值此处的 JSON 格式的字符串。 格式:"{"":""，"":""}" |
| Redis 配置 |-f、-redis 配置文件 |Redis 配置。 输入包含配置密钥和值的文件的路径。 文件条目的格式: {"":""，"":""} |
| 分片计数 |-r，--分片计数 |若要在高级群集缓存群集上创建的分片数。 |
| 虚拟网络 |-v、--虚拟网络 |当承载你的 VNET 中的缓存时，指定要部署的 redis 缓存中的虚拟网络的确切 ARM 资源 ID。 示例格式： /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| 密钥类型 |-t，-密钥类型 |要续订的密钥的类型。 有效值: [Primary，Secondary] |
| StaticIP |-p，---static-ip < 静态 ip > |当承载你的 VNET 中的缓存时，指定唯一的 IP 地址的缓存子网中。 如果未提供，一个会为你选择子网中。 |
| 子网 |t、--子网<subnet> |当承载你的 VNET 中的缓存时，指定在其中部署缓存子网的名称。 |
| VirtualNetwork |-v、--虚拟网络 < 虚拟网络 > |当承载你的 VNET 中的缓存时，指定要部署的 redis 缓存中的虚拟网络的确切 ARM 资源 ID。 示例格式： /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| 订阅 |-s，--subscription |订阅标识符。 |

## <a name="see-all-redis-cache-commands"></a>查看所有 Redis 缓存命令
若要查看所有 Redis 缓存命令和其参数，请使用`azure rediscache -h`命令。

    C:\>azure rediscache -h
    help:    Commands to manage your Azure Redis Cache(s)
    help:
    help:    Create a Redis Cache
    help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Delete an existing Redis Cache
    help:      rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    List all Redis Caches within your Subscription or Resource Group
    help:      rediscache list [options]
    help:
    help:    Show properties of an existing Redis Cache
    help:      rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Change settings of an existing Redis Cache
    help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Renew the authentication key for an existing Redis Cache
    help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help  output usage information
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="create-a-redis-cache"></a>创建 Redis 缓存
若要创建 Redis 缓存，请使用以下命令：

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

有关此命令的详细信息，请运行`azure rediscache create -h`命令。

    C:\>azure rediscache create -h
    help:    Create a Redis Cache
    help:
    help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -l, --location <location>                                Location to create cache.
    help:      -z, --size <size>                                        Size of the Redis Cache. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
    help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
    help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Redis Cache. Add this flag if you want to enable the Non SSL Port for your cache
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
    help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
    help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
    help:      -t, --subnet <subnet>                                    Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -p, --static-ip <static-ip>                              Required when deploying a redis cache inside an existing Azure Virtual Network
    help:      -s, --subscription <id>                                  the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="delete-an-existing-redis-cache"></a>删除现有 Redis 缓存
若要删除 Redis 缓存，请使用以下命令：

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

有关此命令的详细信息，请运行`azure rediscache delete -h`命令。

    C:\>azure rediscache delete -h
    help:    Delete an existing Redis Cache
    help:
    help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-all-redis-caches-within-your-subscription-or-resource-group"></a>列出订阅或资源组中的所有 Redis 缓存
若要列出订阅或资源组中的所有 Redis 缓存，请使用以下命令：

    azure rediscache list [options]

有关此命令的详细信息，请运行`azure rediscache list -h`命令。

    C:\>azure rediscache list -h
    help:    List all Redis Caches within your Subscription or Resource Group
    help:
    help:    Usage: rediscache list [options]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="show-properties-of-an-existing-redis-cache"></a>显示现有 Redis 缓存的属性
若要显示现有 Redis 缓存的属性，请使用以下命令：

    azure rediscache show [--name <name> --resource-group <resource-group>]

有关此命令的详细信息，请运行`azure rediscache show -h`命令。

    C:\>azure rediscache show -h
    help:    Show properties of an existing Redis Cache
    help:
    help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

<a name="scale"></a>

## <a name="change-settings-of-an-existing-redis-cache"></a>更改现有 Redis 缓存的设置
若要更改现有 Redis 缓存的设置，请使用以下命令：

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

有关此命令的详细信息，请运行`azure rediscache set -h`命令。

    C:\>azure rediscache set -h
    help:    Change settings of an existing Redis Cache
    help:
    help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
    help:      -s, --subscription <subscription>                        the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="renew-the-authentication-key-for-an-existing-redis-cache"></a>为现有 Redis 缓存续订身份验证密钥
若要为现有 Redis 缓存续订身份验证密钥，使用以下命令：

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Specify `Primary` or `Secondary` for `key-type`.

有关此命令的详细信息，请运行`azure rediscache renew-key -h`命令。

    C:\>azure rediscache renew-key -h
    help:    Renew the authentication key for an existing Redis Cache
    help:
    help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
    help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-primary-and-secondary-keys-of-an-existing-redis-cache"></a>现有 Redis 缓存的列表主密钥和辅助密钥
列出现有 Redis 缓存的主密钥和辅助密钥，使用以下命令：

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

有关此命令的详细信息，请运行`azure rediscache list-keys -h`命令。

    C:\>azure rediscache list-keys -h
    help:    Lists Primary and Secondary key of an existing Redis Cache
    help:
    help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Redis Cache.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)
