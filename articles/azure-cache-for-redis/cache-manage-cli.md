---
title: 使用 Azure 经典 CLI 管理 Redis 的 Azure 缓存
description: 了解如何在任何平台上安装 Azure 经典 CLI、如何使用它连接到 Azure 帐户，以及如何从经典 CLI 创建和管理 Azure Redis 缓存。
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: yegu
ms.openlocfilehash: e2b1ed693ea57e3414d465a57a5ba2b1203f67c5
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372000"
---
# <a name="how-to-create-and-manage-azure-cache-for-redis-using-the-azure-classic-cli"></a>如何使用 Azure 经典 CLI 创建和管理 Azure Redis 缓存
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [Azure 经典 CLI](cache-manage-cli.md)
>

Azure 经典 CLI 是从任何平台管理 Azure 基础结构的好办法。 本文演示了如何使用 Azure 经典 CLI 创建和管理 Azure Redis 缓存实例。

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]
> [!NOTE]
> 有关最新的 Azure CLI 示例脚本，请参阅 [Azure Redis 缓存示例](cli-samples.md)。

## <a name="prerequisites"></a>必备条件
要使用 Azure 经典 CLI 创建和管理 Azure Redis 缓存实例，必须完成以下步骤。

* 必须具有 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。
* [安装 Azure 经典 CLI](../cli-install-nodejs.md)。
* 将 Azure CLI 安装与个人 Azure 帐户或者工作或学校 Azure 帐户关联，然后使用 `azure login` 命令从经典 CLI 登录。
* 在运行以下任何命令之前，通过运行 `azure config mode arm` 命令将经典 CLI 切换到资源管理器模式下。 有关更多详细信息，请参阅[使用 Azure 经典 CLI 管理 Azure 资源和资源组](../xplat-cli-azure-resource-manager.md)。

## <a name="azure-cache-for-redis-properties"></a>Azure Redis 缓存属性
在创建和更新 Azure Redis 缓存实例时使用以下属性。

| properties | 开关 | 说明 |
| --- | --- | --- |
| name |-n, --name |Azure Redis 缓存的名称。 |
| 资源组 |-g, --resource-group |资源的名称。 |
| location |-l, --location |要创建缓存的位置。 |
| 大小 |-z, --size |Azure Redis 缓存的大小。 有效的值: [C0、C1、C2、C3、C4、C5、C6、P1、P2、P3、P4] |
| sku |-x, --sku |Redis SKU。 应为以下值之一：[Basic、Standard、Premium] |
| EnableNonSslPort |-e, --enable-non-ssl-port |Azure Redis 缓存的 EnableNonSslPort 属性。 如果要为缓存启用非 SSL 端口，请添加此标志 |
| Redis 配置 |-c, --redis-configuration |Redis 配置。 在此处输入配置键和值的 JSON 格式字符串。 格式："{"":"","":""}" |
| Redis 配置 |-f, --redis-configuration-file |Redis 配置。 在此处输入包含配置键和值的文件的路径。 文件输入项的格式：{"":"","":""} |
| 分片计数 |-r, --shard-count |要在启用群集的高级群集缓存上创建的分片数。 |
| 虚拟网络 |-v, --virtual-network |在 VNet 中托管缓存时，指定要在其中部署 Azure Redis 缓存的虚拟网络的确切 ARM 资源 ID。 格式示例：/subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| key type |-t, --key-type |要续订的密钥类型。 有效值：[Primary, Secondary] |
| StaticIP |-p，--static ip \<静态 ip\> |在 VNET 中托管缓存时，指定缓存在子网中的唯一 IP 地址。 如果未提供此值，系统将从子网中选择一个。 |
| 子网 |t，--subnet \<子网\> |在 VNET 中托管缓存时，指定要在其中部署缓存的子网。 |
| VirtualNetwork |-v，--虚拟网络 \<虚拟网络\> |在 VNet 中托管缓存时，指定要在其中部署 Azure Redis 缓存的虚拟网络的确切 ARM 资源 ID。 格式示例：/subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| 订阅 |-s, --subscription |订阅标识符。 |

## <a name="see-all-azure-cache-for-redis-commands"></a>查看所有 Azure Redis 缓存命令
要查看所有 Azure Redis 缓存命令及其参数，请使用 `azure rediscache -h` 命令。

    C:\>azure rediscache -h
    help:    Commands to manage your Azure Cache for Redis(s)
    help:
    help:    Create an Azure Cache for Redis
    help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Delete an existing Azure Cache for Redis
    help:      rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    List all Azure Cache for Redis within your Subscription or Resource Group
    help:      rediscache list [options]
    help:
    help:    Show properties of an existing Azure Cache for Redis
    help:      rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Change settings of an existing Azure Cache for Redis
    help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Renew the authentication key for an existing Azure Cache for Redis
    help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
    help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help  output usage information
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="create-an-azure-cache-for-redis"></a>创建 Azure Redis 缓存
要创建 Azure Redis 缓存，请使用以下命令：

    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

有关此命令的详细信息，请运行 `azure rediscache create -h` 命令。

    C:\>azure rediscache create -h
    help:    Create an Azure Cache for Redis
    help:
    help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -l, --location <location>                                Location to create cache.
    help:      -z, --size <size>                                        Size of the Azure Cache for Redis. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
    help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
    help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Azure Cache for Redis. Add this flag if you want to enable the Non SSL Port for your cache
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
    help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
    help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
    help:      -t, --subnet <subnet>                                    Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
    help:      -p, --static-ip <static-ip>                              Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
    help:      -s, --subscription <id>                                  the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="delete-an-existing-azure-cache-for-redis"></a>删除现有的 Azure Redis 缓存
要删除 Azure Redis 缓存，请使用以下命令：

    azure rediscache delete [--name <name> --resource-group <resource-group> ]

有关此命令的详细信息，请运行 `azure rediscache delete -h` 命令。

    C:\>azure rediscache delete -h
    help:    Delete an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-all-azure-cache-for-redis-within-your-subscription-or-resource-group"></a>列出订阅或资源组中的所有 Azure Redis 缓存
要列出订阅或资源组中的所有 Azure Redis 缓存，请使用以下命令：

    azure rediscache list [options]

有关此命令的详细信息，请运行 `azure rediscache list -h` 命令。

    C:\>azure rediscache list -h
    help:    List all Azure Cache for Redis within your Subscription or Resource Group
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

## <a name="show-properties-of-an-existing-azure-cache-for-redis"></a>显示现有 Azure Redis 缓存的属性
要显示现有 Azure Redis 缓存的属性，请使用以下命令：

    azure rediscache show [--name <name> --resource-group <resource-group>]

有关此命令的详细信息，请运行 `azure rediscache show -h` 命令。

    C:\>azure rediscache show -h
    help:    Show properties of an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

<a name="scale"></a>

## <a name="change-settings-of-an-existing-azure-cache-for-redis"></a>更改 Azure Redis 缓存的属性设置
更改现有 Azure Redis 缓存的属性设置，请使用以下命令：

    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]

有关此命令的详细信息，请运行 `azure rediscache set -h` 命令。

    C:\>azure rediscache set -h
    help:    Change settings of an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
    help:
    help:    Options:
    help:      -h, --help                                               output usage information
    help:      -v, --verbose                                            use verbose output
    help:      -vv                                                      more verbose with debug output
    help:      --json                                                   use json output
    help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>                    Name of the Resource Group
    help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
    help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
    help:      -s, --subscription <subscription>                        the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="renew-the-authentication-key-for-an-existing-azure-cache-for-redis"></a>为现有 Azure Redis 缓存续订身份验证密钥
要为现有 Azure Redis 缓存续订身份验证密钥，请使用以下命令：

    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

为 `Primary` 指定 `Secondary` 或 `key-type`。

有关此命令的详细信息，请运行 `azure rediscache renew-key -h` 命令。

    C:\>azure rediscache renew-key -h
    help:    Renew the authentication key for an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
    help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)

## <a name="list-primary-and-secondary-keys-of-an-existing-azure-cache-for-redis"></a>列出现有 Azure Redis 缓存的主密钥和辅助密钥
要列出现有 Azure Redis 缓存的主密钥和辅助密钥，请使用以下命令：

    azure rediscache list-keys [--name <name> --resource-group <resource-group>]

有关此命令的详细信息，请运行 `azure rediscache list-keys -h` 命令。

    C:\>azure rediscache list-keys -h
    help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
    help:
    help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
    help:
    help:    Options:
    help:      -h, --help                             output usage information
    help:      -v, --verbose                          use verbose output
    help:      -vv                                    more verbose with debug output
    help:      --json                                 use json output
    help:      -n, --name <name>                      Name of the Azure Cache for Redis.
    help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
    help:      -s, --subscription <subscription>      the subscription identifier
    help:
    help:    Current Mode: arm (Azure Resource Management)
