---
title: Azure HPC 缓存数据引入-msrsync
description: 如何使用 msrsync 将数据移动到 Azure HPC 缓存中的 Blob 存储目标
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: 323ecd6a2dd001c3c8df1b2ec15f0ae8402ec70c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87092399"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Azure HPC 缓存数据引入-msrsync 方法

本文提供了有关使用 ``msrsync`` 实用程序将数据复制到 Azure Blob 存储容器以用于 AZURE HPC 缓存的详细说明。

若要详细了解如何将数据移到 Azure HPC 缓存的 Blob 存储，请参阅 [将数据移到 Azure blob 存储](hpc-cache-ingest.md)。

该 ``msrsync`` 工具可用于将数据移到 AZURE HPC 缓存的后端存储目标。 此工具旨在通过运行多个并行 ``rsync`` 进程来优化带宽的使用。 可从 GitHub 获取此工具：https://github.com/jbd/msrsync。

``msrsync`` 将源目录分解成独立的“桶”，然后针对每个桶运行单个 ``rsync`` 进程。

使用四核 VM 进行的初步测试表明，使用 64 个进程时效率最高。 使用 ``msrsync`` 选项 ``-p`` 将进程数设置为 64。

请注意，``msrsync`` 只能与本地卷相互写入。 源和目标必须可作为用于发出命令的工作站上的本地装载。

按照这些说明操作，使用 ``msrsync`` AZURE HPC 缓存填充 Azure Blob 存储：

1. 安装及其 ``msrsync`` 必备 (``rsync`` 和 Python 2.6 或更高版本) 
1. 确定要复制的文件和目录总数。

   例如， ``prime.py`` 通过下载) 使用带参数的实用程序 ```prime.py --directory /path/to/some/directory``` (可用 <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py> 。

   如果不使用 ``prime.py`` ，则可以使用 GNU 工具计算项的数目， ``find`` 如下所示：

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
