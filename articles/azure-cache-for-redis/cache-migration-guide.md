---
title: 迁移到 Azure Cache for Redis
description: 了解如何将现有缓存迁移到 Azure Cache for Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: yegu
ms.openlocfilehash: 4b196818ade1e703e24ed1ced6ebac1b44d0b083
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91372062"
---
# <a name="migrate-to-azure-cache-for-redis"></a>迁移到 Azure Cache for Redis
本文介绍将在本地运行或在其他云服务中运行的现有 Redis 缓存迁移到 Azure Cache for Redis 的多种方法。

## <a name="migration-scenarios"></a>迁移方案
开源 Redis 可以在许多计算环境中运行。 常见示例包括：

- **本地** - 在专用数据中心中运行的 Redis 缓存。
- **基于云的 VM** - 在 Azure VM、AWS EC2 等上运行的 Redis 缓存。
- **托管服务** - 托管 Redis 服务，例如 AWS ElastiCache。
- **不同区域** - 位于另一个 Azure 区域的 Redis 缓存。

如果拥有此类缓存，则可以以最小中断或停机时间将其移至 Azure Cache for Redis。

## <a name="migration-options"></a>迁移选项

可以通过多种方式从一个缓存切换到另一个缓存。 根据缓存所在的位置以及应用程序与缓存的交互方式，其中一种方法将比其他方法更有用。 下面详细介绍一些常用的迁移策略。

   | 选项       | 优点 | 缺点 |
   | ------------ | ---------- | ------------- |
   | 创建新缓存 | 最易于实现。 | 需要将数据重新填充到新的缓存中，这可能不适用于许多应用程序。 |
   | 通过 RDB 文件导出和导入数据 | 通常与任何 Redis 缓存兼容。 | 如果某些数据在生成 RDB 文件后写入现有缓存，这些数据可能会丢失。 | 
   | 将数据双重写入两个缓存 | 无数据丢失或停机时间。 现有缓存不间断操作。 更轻松地测试新缓存。 | 需要两个缓存以延长时间。 | 
   | 以编程方式迁移数据 | 对数据的移动方式具有完全控制。 | 需要自定义代码。 | 

### <a name="create-a-new-azure-cache-for-redis"></a>创建新的 Azure Cache for Redis

从技术上讲，这种方法不是迁移。 如果无需担心丢失数据，则迁移 Azure Cache for Redis 的最简单方法是创建缓存实例并将应用程序连接到该实例。 例如，如果将 Redis 用作数据库记录的后备缓存，则可以轻松地从头开始重新生成缓存。

实现此选项的一般步骤如下：

1. 创建新的 Azure Cache for Redis 实例。

2. 更新应用程序以使用新实例。

3. 删除旧的 Redis 实例。

### <a name="export-data-to-an-rdb-file-and-import-it-into-azure-cache-for-redis"></a>将数据导出到 RDB 文件并将该文件导入 Azure Cache for Redis

开源 Redis 定义了一种标准机制，用于获取缓存的内存中数据集的快照并将其保存到文件中。 此文件名为 RDB，可由另一个 Redis 缓存读取。 [Azure Cache for Redis 高级层](cache-overview.md#service-tiers)支持通过 RDB 文件将数据导入缓存实例。 可以使用 RDB 文件将数据从现有缓存传输到 Azure Cache for Redis。

> [!IMPORTANT]
> RDB 文件格式可以在 Redis 版本之间更改，并且可能无法保持向后兼容。 要从中导出的缓存的 Redis 版本应等于或低于 Azure Cache for Redis 提供的版本。
>

实现此选项的一般步骤如下：

1. 在高级层中创建与现有缓存大小相同（或大于现有缓存）的新 Azure Cache for Redis 实例。

2. 保存现有 Redis 缓存的快照。 可以[配置 Redis 以定期保存快照](https://redis.io/topics/persistence)，或者使用 [SAVE](https://redis.io/commands/save) 或 [BGSAVE](https://redis.io/commands/bgsave) 命令手动运行该过程。 RDB 文件默认命名为“dump.rdb”，并将位于 redis.conf 配置文件中指定的路径下。

    > [!NOTE]
    > 如果要在 Azure Cache for Redis 中迁移数据，请参阅[这些有关如何导出 RDB 文件的说明](cache-how-to-import-export-data.md)，或改为使用 [PowerShell 导出 cmdlet](https://docs.microsoft.com/powershell/module/azurerm.rediscache/export-azurermrediscache?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.4.0)。
    >

3. 将 RDB 文件复制到新缓存所在区域中的 Azure 存储帐户。 可以将 AzCopy 用于此任务。

4. 使用这些[导入说明](cache-how-to-import-export-data.md)或 [PowerShell 导入 cmdlet](https://docs.microsoft.com/powershell/module/azurerm.rediscache/import-azurermrediscache?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.4.0) 将 RDB 文件导入新缓存。

5. 更新应用程序以使用新缓存实例。

### <a name="write-to-two-redis-caches-simultaneously-during-migration-period"></a>在迁移期间同时写入两个 Redis 缓存

可以使用应用程序将数据写入现有缓存和要设置的新缓存，而不是直接在缓存之间移动数据。 应用程序一开始仍将从现有缓存中读取数据。 当新缓存拥有必要的数据后，就可以将应用程序切换到该缓存，然后停用旧缓存。 举例来说，假设使用 Redis 作为会话存储，且应用程序会话的有效期为 7 天。 写入两个缓存一周后，即可确定新缓存包含所有未过期会话信息。 此后，可放心地依靠它，而不必担心数据丢失。

实现此选项的一般步骤如下：

1. 在高级层中创建与现有缓存大小相同（或大于现有缓存）的新 Azure Cache for Redis 实例。

2. 修改应用程序代码以写入新实例和原始实例。

3. 继续从原始实例读取数据，直到新实例填充足够的数据为止。

4. 更新应用程序代码以仅从新实例读取和写入。

5. 删除原始实例。

### <a name="migrate-programmatically"></a>以编程方式迁移

可以通过编程方式从现有缓存中读取数据并将其写入 Azure Cache for Redis，从而创建自定义迁移过程。 此[开源工具](https://github.com/deepakverma/redis-copy)可用于将数据从一个 Azure Cache for Redis 实例复制到另一个 Azure Cache for Redis 实例。 此工具对于在不同 Azure 缓存区域中的缓存实例之间移动数据很有用。 还提供[编译版本](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip)。 你可能还会发现，源代码可为编写自己的迁移工具提供有用指导。

> [!NOTE]
> Microsoft 尚未正式支持此工具。 
>

实现此选项的一般步骤如下：

1. 在现有缓存​​所在区域中创建 VM。 如果数据集很大，请选择功能相对强大的 VM 以减少复制时间。

2. 创建新的 Azure Cache for Redis 实例。

3. 刷新新缓存中的数据以确保其为空。 由于复制工具本身不会覆盖目标缓存中的任何现有密钥，因此需要执行此步骤。

    > [!IMPORTANT]
    > 确保不要从源缓存中刷新。
    >

4. 使用应用程序（例如上述开源工具）自动将数据从源缓存复制到目标缓存。 请记住，复制过程可能需要一段时间才能完成，具体取决于数据集的大小。

## <a name="next-steps"></a>后续步骤
了解有关 Azure Cache for Redis 功能的详细信息。

* [Azure Cache for Redis 服务层](cache-overview.md#service-tiers)
* [导入数据](cache-how-to-import-export-data.md#import)
