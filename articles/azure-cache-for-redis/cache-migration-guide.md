---
title: 迁移到 Azure Cache for Redis
description: 了解如何将现有缓存迁移到 Azure Cache for Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: yegu
ms.openlocfilehash: 2a95aa9e9fccdb7047c2c0901f4349fecfbab672
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009573"
---
# <a name="migrate-to-azure-cache-for-redis"></a>迁移到 Azure Cache for Redis
本文介绍了一些方法，用于将在本地或其他云服务中运行的现有 Redis 缓存迁移到 Azure Cache for Redis。

## <a name="migration-scenarios"></a>迁移方案
开源 Redis 可以在许多计算环境中运行。 常见示例包括：

- **在**专用数据中心运行的本地 Redis 缓存。
- **基于云的 vm** -在 Azure vm 上运行的 Redis 缓存、AWS EC2 等。
- 托管**服务**托管的 Redis 服务，例如 AWS ElastiCache。
- **不同区域**-位于另一个 Azure 区域中的 Redis 缓存。

如果有这样的缓存，则可以将其移动到 Redis 的 Azure 缓存中，从而减少中断或停机时间。

## <a name="migration-options"></a>迁移选项

可以通过不同的方式从一个缓存切换到另一个缓存。 根据缓存的位置和应用程序与之交互的方式，其中一种方法比其他方法更有用。 下面详细介绍了一些常用的迁移策略。

   | 选项       | 优点 | 缺点 |
   | ------------ | ---------- | ------------- |
   | 创建新缓存 | 最简单实现。 | 需要将数据重新填充到新缓存，这可能不适用于许多应用程序。 |
   | 通过 RDB 文件导出和导入数据 | 通常与任何 Redis 缓存兼容。 | 如果在生成 RDB 文件后将数据写入现有缓存，则可能会丢失某些数据。 | 
   | 双写入两个缓存的数据 | 无数据丢失或市区。 现有缓存的不间断操作。 更轻松地测试新的缓存。 | 长时间需要两个缓存。 | 
   | 以编程方式迁移数据 | 完全控制数据移动方式。 | 需要自定义代码。 | 

### <a name="create-a-new-azure-cache-for-redis"></a>为 Redis 创建新的 Azure 缓存

此方法在技术上不是迁移。 如果数据丢失，最简单的方法是创建缓存实例，并将应用程序连接到 Redis。 例如，如果使用 Redis 作为数据库记录的外观缓存，则可以轻松地从头开始重新生成缓存。

实现此选项的一般步骤如下：

1. 为 Redis 实例创建新的 Azure Cache。

2. 更新应用程序以使用新的实例。

3. 删除旧的 Redis 实例。

### <a name="export-data-to-an-rdb-file-and-import-it-into-azure-cache-for-redis"></a>将数据导出到 RDB 文件并将其导入到 Azure Cache for Redis

开源 Redis 定义了一种标准机制，用于获取缓存内存中数据集的快照，并将其保存到文件中。 此文件称为 RDB，可由另一个 Redis 缓存读取。 [适用于 Redis 高级层的 Azure 缓存](cache-overview.md#service-tiers)支持通过 RDB 文件将数据导入缓存实例。 可以使用 RDB 文件将现有缓存中的数据传输到 Azure Cache for Redis。

> [!IMPORTANT]
> RDB 文件格式在 Redis 版本之间可能会更改，并且可能不会保留向后兼容性。 要从中导出的缓存的 Redis 版本应等于或小于 Azure Cache for Redis 提供的版本。
>

实现此选项的一般步骤如下：

1. 在高级层中创建新的 Azure Cache for Redis 实例，该实例大小与现有缓存)  (或更大。

2. 保存现有 Redis 缓存的快照。 可以[将 Redis 配置为定期保存快照](https://redis.io/topics/persistence)，或使用[save](https://redis.io/commands/save)或[BGSAVE](https://redis.io/commands/bgsave)命令手动运行进程。 默认情况下，RDB 文件命名为 "dump. RDB"，并将位于*redis*配置文件中指定的路径。

    > [!NOTE]
    > 如果要在 Azure Cache for Redis 中迁移数据，请参阅有关[如何导出 RDB 文件](cache-how-to-import-export-data.md)或改用[PowerShell export cmdlet](https://docs.microsoft.com/powershell/module/azurerm.rediscache/export-azurermrediscache?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.4.0)的这些说明。
    >

3. 将 RDB 文件复制到新缓存所在区域中的 Azure 存储帐户。 你可以使用 AzCopy 来执行此任务。

4. 使用这些[导入说明](cache-how-to-import-export-data.md)或[PowerShell IMPORT cmdlet](https://docs.microsoft.com/powershell/module/azurerm.rediscache/import-azurermrediscache?view=azurermps-6.13.0&viewFallbackFrom=azurermps-6.4.0)将 RDB 文件导入新缓存。

5. 更新应用程序以使用新的缓存实例。

### <a name="write-to-two-redis-caches-simultaneously-during-migration-period"></a>在迁移期间同时写入两个 Redis 缓存

您可以使用您的应用程序将数据写入到现有缓存和所设置的新缓存中，而不是直接在缓存之间移动数据。 应用程序将首先从现有的缓存中读取数据。 如果新缓存具有所需的数据，请将应用程序切换到该缓存，并停用旧的缓存。 例如，例如，使用 Redis 作为会话存储，应用程序会话有效时间为七天。 每周写入两次缓存后，将确定新缓存是否包含所有未过期的会话信息。 你可以从该点开始安全地依赖它，而无需担心数据丢失。

实现此选项的一般步骤如下：

1. 在高级层中创建新的 Azure Cache for Redis 实例，该实例大小与现有缓存)  (或更大。

2. 修改应用程序代码以写入新实例和原始实例。

3. 继续读取原始实例中的数据，直到新实例填满数据。

4. 更新应用程序代码以仅从新实例中进行读取和写入。

5. 删除原始实例。

### <a name="migrate-programmatically"></a>以编程方式迁移

可以通过编程方式读取现有缓存中的数据并将其写入 Azure Cache for Redis，来创建自定义迁移过程。 此[开源工具](https://github.com/deepakverma/redis-copy)可用于将数据从一个 Azure Cache for Redis 实例复制到另一个实例。 此工具适用于在不同 Azure 缓存区域中的缓存实例之间移动数据。 还提供了一个[已编译版本](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip)。 你还可能会发现源代码是编写你自己的迁移工具的有用指南。

> [!NOTE]
> Microsoft 不支持此工具。 
>

实现此选项的一般步骤如下：

1. 在现有缓存所在的区域中创建 VM。 如果数据集较大，请选择一个相对较强大的 VM 来减少复制时间。

2. 为 Redis 实例创建新的 Azure Cache。

3. 刷新新缓存中的数据，以确保它是空的。 此步骤是必需的，因为复制工具本身不会覆盖目标缓存中的任何现有密钥。

    > [!IMPORTANT]
    > 请确保不要从源缓存中刷新。
    >

4. 使用上述开源工具之类的应用程序自动将数据从源缓存复制到目标。 请记住，复制过程可能需要一段时间才能完成，具体取决于数据集的大小。

## <a name="next-steps"></a>后续步骤
详细了解 Azure Cache for Redis 功能。

* [适用于 Redis 服务层的 Azure 缓存](cache-overview.md#service-tiers)
* [导入数据](cache-how-to-import-export-data.md#import)
