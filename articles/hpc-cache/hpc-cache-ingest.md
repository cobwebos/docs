---
title: 将数据移动到 Azure HPC 缓存云容器
description: 如何填充 Azure Blob 存储，以便与 Azure HPC 缓存一起使用
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: fd21a78d0271f91d334bba5aba748f3770ad38cf
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537927"
---
# <a name="move-data-to-azure-blob-storage"></a>将数据移动到 Azure Blob 存储

如果工作流包括将数据移动到 Azure Blob 存储，请确保使用有效的策略。 您可以将数据预加载到新的 Blob 容器中，然后再将其定义为存储目标，也可以添加容器，然后使用 Azure HPC 缓存复制数据。

本文介绍了将数据移动到 Blob 存储以便与 Azure HPC 缓存一起使用的最佳方式。

请记住以下事实：

* Azure HPC 缓存使用专用存储格式来组织 Blob 存储中的数据。 因此，Blob 存储目标必须是新的空容器，或者以前用于 Azure HPC 缓存数据的 Blob 容器。

* 当您使用多个客户端和并行操作时，通过 Azure HPC 缓存将数据复制到后端存储目标的效率更高。 来自一个客户端的简单复制命令将缓慢移动数据。

基于 Python 的实用程序可用于将内容加载到 Blob 存储容器中。 阅读[Blob 存储中的预加载数据](#pre-load-data-in-blob-storage-with-clfsload)以了解更多信息。

如果不想使用加载实用程序，或者要将内容添加到现有存储目标，请[按照通过 Azure HPC 缓存复制数据](#copy-data-through-the-azure-hpc-cache)中的并行数据引入提示。

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>使用 CLFSLoad 在 Blob 存储中预加载数据

在将数据添加为存储目标之前，可以使用 Avere CLFSLoad 实用程序将数据复制到新的 Blob 存储容器。 此实用程序在单个 Linux 系统上运行，以 Azure HPC 缓存所需的专有格式写入数据。 CLFSLoad 是填充 Blob 存储容器以用于缓存的最有效方法。

Avere CLFSLoad 实用程序可通过 Azure HPC 缓存团队的请求提供。 请向您的团队联系人咨询，或打开[支持票证](hpc-cache-support-ticket.md)以请求帮助。

此选项仅适用于新的空容器。 在使用 Avere CLFSLoad 之前创建容器。

详细信息包含在 Avere CLFSLoad 分布中，该分布应 Azure HPC 缓存团队的请求提供。

流程概述：

1. 使用 Python 版本 3.6 或更高版本准备 Linux 系统（VM 或物理系统）。 为了更好的性能，建议使用 Python 3.7。
1. 在 Linux 系统上安装 Avere-CLFSLoad 软件。
1. 从 Linux 命令行执行传输。

Avere CLFSLoad 实用程序需要以下信息：

* 包含 Blob 存储容器的存储帐户 ID
* 空 Blob 存储容器的名称
* 允许实用程序写入容器的共享访问签名 （SAS） 令牌
* 数据源的本地路径 - 包含要复制的数据的本地目录，或到具有数据的装载远程系统的本地路径

## <a name="copy-data-through-the-azure-hpc-cache"></a>通过 Azure HPC 缓存复制数据

如果不想使用 Avere CLFSLoad 实用程序，或者要向现有 Blob 存储目标添加大量数据，则可以通过缓存复制它。 Azure HPC 缓存旨在同时为多个客户端提供服务，因此，要通过缓存复制数据，应使用来自多个客户端的并行写入。

![显示多客户端、多线程数据移动的示意图：在左上方，本地硬件存储对应的图标引出了多个箭头。 箭头指向四台客户端计算机。 从每台客户端计算机三个箭头指向 Azure HPC 缓存。 从 Azure HPC 缓存中，多个箭头指向 Blob 存储。](media/hpc-cache-parallel-ingest.png)

通常用于``cp``将数据``copy``从一个存储系统传输到另一个存储系统的 或 命令是单线程进程，一次只复制一个文件。 这意味着文件服务器一次只引入一个文件，这是对缓存资源的浪费。

本节介绍创建多客户端多线程文件复制系统以使用 Azure HPC 缓存将数据移动到 Blob 存储的策略。 其中解释了文件传输的概念，以及可用于有效通过多个客户端和简单复制命令来复制数据的决策点。

此外，本文还介绍了一些有用的实用工具。 ``msrsync`` 实用工具可用于部分自动化将数据集分割成桶和使用 rsync 命令的过程。 ``parallelcp`` 脚本是可以读取源目录和自动发出复制命令的另一个实用工具。

### <a name="strategic-planning"></a>策略规划

在制定并行复制数据的策略时，应该知道如何在文件大小、文件计数和目录深度方面做出取舍。

* 如果文件较小，应该关注的指标是每秒文件数。
* 如果文件较大（10MiBi 或更大），则应该关注的指标是每秒字节数。

每个复制进程都有相关的吞吐率和文件传输速率，可以通过计量复制命令的时长和分解文件大小与文件计数来测量这些参数。 有关如何测量速率超出了本文档的范畴，但必须了解要处理的是小型还是大型文件。

使用 Azure HPC 缓存并行数据引入的策略包括：

* 手动复制 - 您可以根据预定义的文件或路径集在后台同时运行多个复制命令，从而在客户端上手动创建多线程副本。 阅读[Azure HPC 缓存数据引入 - 手动复制方法](hpc-cache-ingest-manual.md)了解详细信息。

* 部分自动``msrsync`` - ``msrsync``复制是运行多个并行``rsync``进程的包装实用程序。 有关详细信息，请阅读[Azure HPC 缓存数据引入 - msrsync 方法](hpc-cache-ingest-msrsync.md)。

* 脚本复制与``parallelcp``- 了解如何在[Azure HPC 缓存数据引入 - 并行复制脚本方法](hpc-cache-ingest-parallelcp.md)中创建和运行并行复制脚本。

## <a name="next-steps"></a>后续步骤

设置存储后，了解客户端如何装载缓存。

* [访问 Azure HPC 缓存系统](hpc-cache-mount.md)
