---
title: 将数据移动到 Azure HPC 缓存云容器
description: 如何填充用于 Azure HPC 缓存的 Azure Blob 存储
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 07a97b1afa8049ace97f1589393cd76c24f21368
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775645"
---
# <a name="move-data-to-azure-blob-storage-for-azure-hpc-cache"></a>将数据移到 azure HPC 缓存的 Azure Blob 存储

如果工作流包括将数据移动到 Azure Blob 存储，请确保使用有效的策略通过 Azure HPC 缓存复制数据。

本文介绍将数据移动到 Blob 存储以用于 Azure HPC 缓存的最佳方式。

请记住以下事实：

* Azure HPC 缓存使用专用存储格式在 Blob 存储中组织数据。 这就是 Blob 存储目标必须是新的空容器或以前用于 Azure HPC 缓存数据的 Blob 容器的原因。 （[适用于 Azure 的 Avere vFXT](https://azure.microsoft.com/services/storage/avere-vfxt/)也使用此云文件系统。）

* 如果使用多个客户端和并行操作，则可以通过 Azure HPC 缓存复制数据。 一台客户端的简单复制命令会慢慢地移动数据。

基于 Python 的实用工具可用于将内容加载到 Blob 存储容器中。 若要了解详细信息，请参阅[Blob 存储中的预加载数据](#pre-load-data-in-blob-storage-with-clfsload)。

如果不想使用加载实用程序，或者如果要将内容添加到现有存储目标，请遵循[通过 AZURE HPC 缓存复制数据](#copy-data-through-the-azure-hpc-cache)中的并行数据引入提示。 

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>在 Blob 存储中通过 CLFSLoad 预加载数据

在将数据添加为存储目标之前，可以使用[Avere CLFSLoad](https://aka.ms/avere-clfsload)实用工具将数据复制到新的 Blob 存储容器。 此实用程序在 Linux VM 上运行，并以 Azure HPC 缓存所需的专用格式写入数据。 这是用来填充用于缓存的 Blob 存储容器的最有效方法。

此选项仅适用于新的空容器。 请先创建容器，然后再使用 Avere CLFSLoad。

[Avere CLFSLoad 自述文件](https://github.com/microsoft/Avere-CLFSLoad/blob/master/README.md)中提供了详细信息。 <!-- caution literal link -->

过程的一般概述：

1. 使用 Python 版本3.6 或更高版本准备 Linux 系统（物理或虚拟机）。 （建议使用 Python 3.7 以获得更好的性能。）
1. 在 Linux 系统上安装 Avere-CLFSLoad 软件。
1. 从 Linux 命令行执行传输。

Avere CLFSLoad 实用工具需要以下信息：

* 包含 Blob 存储容器的存储帐户 ID
* 空 Blob 存储容器的名称
* 允许实用工具写入容器的共享访问签名（SAS）令牌
* 数据源的本地路径-一个本地目录，其中包含要复制的数据，或包含数据的已装载远程系统的本地路径。

[Avere CLFSLoad 自述文件](https://aka.ms/avere-clfsload)中详细说明了这些要求。

## <a name="copy-data-through-the-azure-hpc-cache"></a>通过 Azure HPC 缓存复制数据

如果不想要使用 Avere CLFSLoad 实用程序，或者想要将大量数据添加到现有 Blob 存储目标，可以通过缓存复制它。 Azure HPC 缓存旨在同时为多个客户端提供服务，因此，若要通过缓存复制数据，应使用来自多个客户端的并行写入。

![显示多客户端、多线程数据移动的示意图：在左上方，本地硬件存储对应的图标引出了多个箭头。 箭头指向四台客户端计算机。 每个客户端计算机的三个箭头指向 Azure HPC 缓存。 通过 Azure HPC 缓存，多个箭头指向 Blob 存储。](media/hpc-cache-parallel-ingest.png) 

通常用于``copy``将数据从一个存储系统传输到另一个存储系统的或命令是单线程进程，该进程一次只复制一个文件。``cp`` 这意味着，文件服务器每次只会引入一个文件 - 这给群集资源造成了浪费。

本部分介绍了使用 Azure HPC 缓存创建多客户端多线程文件复制系统以将数据移到 Blob 存储的策略。 其中解释了文件传输的概念，以及可用于有效通过多个客户端和简单复制命令来复制数据的决策点。

此外，本文还介绍了一些有用的实用工具。 ``msrsync`` 实用工具可用于部分自动化将数据集分割成桶和使用 rsync 命令的过程。 ``parallelcp`` 脚本是可以读取源目录和自动发出复制命令的另一个实用工具。

### <a name="strategic-planning"></a>策略规划

在制定并行复制数据的策略时，应该知道如何在文件大小、文件计数和目录深度方面做出取舍。

* 如果文件较小，应该关注的指标是每秒文件数。
* 如果文件较大（10MiBi 或更大），则应该关注的指标是每秒字节数。

每个复制进程都有相关的吞吐率和文件传输速率，可以通过计量复制命令的时长和分解文件大小与文件计数来测量这些参数。 有关如何测量速率超出了本文档的范畴，但必须了解要处理的是小型还是大型文件。

通过 Azure HPC 缓存引入并行数据的策略包括：

* 手动复制-可以通过在后台针对预定义的文件或路径集运行多个 copy 命令，在客户端上手动创建多线程副本。 有关详细信息，请参阅[AZURE HPC 云数据引入-手动复制方法](hpc-cache-ingest-manual.md)。

* 的``msrsync``  -  ``rsync``部分自动复制是一个运行多个并行进程的包装实用工具。 ``msrsync`` 有关详细信息，请参阅[AZURE HPC 缓存数据引入-msrsync 方法](hpc-cache-ingest-msrsync.md)。

* 使用``parallelcp``脚本复制-了解如何在[Azure HPC 缓存数据引入-并行复制脚本方法](hpc-cache-ingest-parallelcp.md)中创建和运行并行复制脚本。

## <a name="next-steps"></a>后续步骤

设置存储后，了解客户端如何装载缓存。

* [访问 Azure HPC 缓存系统](hpc-cache-mount.md)
