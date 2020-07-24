---
title: Azure Cache for Redis 与 Azure Private Link （预览版）
description: Azure 专用终结点是一个网络接口，该接口将你私下并安全地连接到 Azure 缓存，以供 Azure 专用链接提供支持的 Redis。 在本文中，你将了解如何使用 Azure 门户创建 Azure 缓存、Azure 虚拟网络和专用终结点。
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 59d5c6c4a9c3af70eb08e9d5a05d516f4b8b8bdd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096633"
---
# <a name="azure-cache-for-redis-with-azure-private-link-preview"></a>Azure Cache for Redis 与 Azure Private Link （预览版）
Azure 专用终结点是一个网络接口，该接口将你私下并安全地连接到 Azure 缓存，以供 Azure 专用链接提供支持的 Redis。 

在本文中，你将了解如何使用 Azure 门户创建 Azure 缓存、Azure 虚拟网络和专用终结点。  

## <a name="prerequisites"></a>先决条件
* Azure 订阅- [免费创建一个](https://azure.microsoft.com/free/)

> [!NOTE]
  > 此功能目前处于预览阶段，如果你感兴趣，[请](mailto:azurecache@microsoft.com)联系我们。
  >


## <a name="create-a-cache"></a>创建缓存
1. 若要创建缓存，请登录到 [Azure 门户](https://portal.azure.com)并选择“创建资源”  。 

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="选择 "创建资源"。":::
   
1. 在“新建”页上选择“数据库”，然后选择“Azure Cache for Redis”。  

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="为 Redis 选择 "Azure 缓存"。":::
   
1. 在“新建 Redis 缓存”页上配置新缓存的设置。
   
   | 设置      | 建议的值  | 说明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 名称** | 输入任何全局唯一的名称。 | 缓存名称必须是包含 1 到 63 个字符的字符串，只能包含数字、字母或连字符。 该名称必须以数字或字母开头和结尾，且不能包含连续的连字符。 缓存实例的*主机名*将为* \<DNS name> redis.cache.windows.net*。 | 
   | **订阅** | 单击下拉箭头并选择你的订阅。 | 要在其下创建此新的 Azure Cache for Redis 实例的订阅。 | 
   | **资源组** | 单击下拉箭头并选择一个资源组，或者选择“新建”并输入新的资源组名称。 | 要在其中创建缓存和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 | 
   | **位置** | 单击下拉箭头并选择一个位置。 | 选择与要使用该缓存的其他服务靠近的[区域](https://azure.microsoft.com/regions/)。 |
   | **定价层** | 单击下拉箭头并选择一个[定价层](https://azure.microsoft.com/pricing/details/cache/)。 |  定价层决定可用于缓存的大小、性能和功能。 有关详细信息，请参阅[用于 Redis 的 Azure 缓存概述](cache-overview.md)。 |
   
1. 选择“创建”  。 
   
    :::image type="content" source="media/cache-private-link/3-new-cache.png" alt-text="创建用于 Redis 的 Azure 缓存。":::
   
   创建缓存需要花费片刻时间。 可以在 Azure Cache for Redis 的“概述”页上监视进度。  如果“状态”显示为“正在运行”，则表示该缓存可供使用。 
    
    :::image type="content" source="media/cache-private-link/4-status.png" alt-text="Azure Cache for Redis 已创建。":::

## <a name="create-a-virtual-network"></a>创建虚拟网络

在本部分中，你将创建一个虚拟网络和子网。

1. 选择“创建资源”。

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="选择 "创建资源"。":::

2. 在 "**新建**" 页上，选择 "**网络**"，然后选择 "**虚拟网络**"。

    :::image type="content" source="media/cache-private-link/5-select-vnet.png" alt-text="创建虚拟网络。":::

3. 在 "**创建虚拟网络**" 中，在 "**基本**信息" 选项卡中输入或选择以下信息：

    | **设置**          | **值**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **项目详细信息**  |                                                                 |
    | 订阅     | 单击下拉箭头并选择你的订阅。                                  |
    | 资源组   | 下拉，然后选择一个资源组。 |
    | **实例详细信息** |                                                                 |
    | 名称             | 回车**\<virtual-network-name>**                                    |
    | 区域           | 单击**\<region-name>** |

4. 选择 " **Ip 地址**" 选项卡，或选择页面底部的 " **ip 地址**" 按钮。

5. 在 " **IP 地址**" 选项卡中输入以下信息：

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | IPv4 地址空间 | 回车**\<IPv4-address-space>** |

6. 在 "**子网名称**" 下，选择 word**默认值**。

7. 在 "**编辑子网**" 中，输入以下信息：

    | 设置            | 值                      |
    |--------------------|----------------------------|
    | 子网名称 | 回车**\<subnet-name>** |
    | 子网地址范围 | 回车**\<subnet-address-range>**

8. 选择“保存” 。

9. 选择 "**查看**" 和 "创建" 选项卡或选择 "查看" 和 "**创建**" 按钮。

10. 选择“创建” 。


## <a name="create-a-private-endpoint"></a>创建专用终结点 

在本部分中，你将创建一个专用终结点，并将其连接到之前创建的缓存。

1. 搜索 "**专用链接**"，然后按 enter 或从搜索建议中选择它。

    :::image type="content" source="media/cache-private-link/7-create-private-link.png" alt-text="搜索专用链接。":::

2. 在屏幕左侧选择 "**专用终结点**"。

    :::image type="content" source="media/cache-private-link/8-select-private-endpoint.png" alt-text="选择 "专用链接"。":::

3. 选择 " **+ 添加**" 按钮以创建专用终结点。 

    :::image type="content" source="media/cache-private-link/9-add-private-endpoint.png" alt-text="添加专用链接。":::

4. 在 "**创建专用终结点" 页**上，配置专用终结点的设置。

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 单击下拉箭头并选择你的订阅。 |
    | 资源组 | 下拉，然后选择一个资源组。 |
    | **实例详细信息** |  |
    | 名称 |输入专用终结点的名称。  |
    | 区域 |单击下拉箭头并选择一个位置。 |
    |||

5. 选择页面底部的 "**下一步：资源**" 按钮。

6. 在 "**资源**" 选项卡中，选择 "订阅"，选择 "Redis" 资源类型，然后选择在上一部分中创建的缓存。

    :::image type="content" source="media/cache-private-link/10-resource-private-endpoint.png" alt-text="专用链接的资源。":::

7. 选择页面底部的 "**下一步：配置**" 按钮。

8. 在 "**配置**" 选项卡中，选择在上一部分中创建的虚拟网络和子网。

    :::image type="content" source="media/cache-private-link/11-configuration-private-endpoint.png" alt-text="专用链接的配置。":::

9. 选择页面底部的 "**下一步：标记**" 按钮。

10. 如果要对资源进行分类，请在 "**标记**" 选项卡中输入名称和值。 此步骤是可选的。

    :::image type="content" source="media/cache-private-link/12-tags-private-endpoint.png" alt-text="专用链接的标记。":::

11. 选择“查看 + 创建”。 **** 你将转到 " **查看**" 和 "创建   " 选项卡，Azure 将在其中验证你的配置。

12. 出现绿色**验证通过**消息后，选择 "**创建**"。
