---
title: Azure HPC 缓存预览版数据摄取-msrsync
description: 如何使用 msrsync 将数据移动到 Azure HPC 缓存中的 Blob 存储目标
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: rohogue
ms.openlocfilehash: 6eac6c367be42021a4654f85c8f4ec980c9f6925
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255288"
---
# <a name="azure-hpc-cache-preview-data-ingest---msrsync-method"></a>Azure HPC 缓存（预览）数据引入-msrsync 方法

本文详细说明了如何使用 ``msrsync`` 实用工具将数据复制到 Azure Blob 存储容器，以便与 Azure HPC 缓存一起使用。

若要详细了解如何将数据移到 Azure HPC 缓存的 Blob 存储，请参阅[将数据移到 AZURE Hpc 缓存的 Azure blob 存储](hpc-cache-ingest.md)。

@No__t-0 工具可用于将数据移到 Azure HPC 缓存的后端存储目标。 此工具旨在通过运行多个并行 ``rsync`` 进程来优化带宽的使用。 可从 GitHub 获取此工具： https://github.com/jbd/msrsync 。

``msrsync`` 将源目录分解成独立的“桶”，然后针对每个桶运行单个 ``rsync`` 进程。

使用四核 VM 进行的初步测试表明，使用 64 个进程时效率最高。 使用 ``msrsync`` 选项 ``-p`` 将进程数设置为 64。

请注意，``msrsync`` 只能与本地卷相互写入。 源和目标必须可作为用于发出命令的工作站上的本地装载。

按照以下说明使用 ``msrsync`` 来使用 Azure HPC 缓存填充 Azure Blob 存储：

1. 安装 ``msrsync`` 及其必备组件（@no__t 1 和 Python 2.6 或更高版本）
1. 确定要复制的文件和目录总数。

   例如，使用实用程序 ``prime.py``，参数 ```prime.py --directory /path/to/some/directory``` （可通过下载 <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>）使用。

   如果不使用 ``prime.py``，则可按如下所示计算 GNU ``find`` 工具的项的数目：

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. 将项数除以 64 即可得出每个进程的项数。 运行该命令时，在 ``-f`` 选项中使用此数字可以设置桶的大小。

1. 发出 ``msrsync`` 命令以复制文件：

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   例如，此命令旨在将64进程中的11000文件从/test/source-repository 移动到/mnt/hpccache/repository：

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
