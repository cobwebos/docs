---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/11/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 9c59b98fb615266c193f997c01c83922c18d4408
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66147912"
---
预热缓存  
启用 ReadOnly 主机缓存的磁盘能够提供比磁盘限制更高的 IOPS。 若要通过主机缓存来实现此最大读取性能，首先必须对此磁盘的缓存进行预热。 这样可确保需要通过基准测试工具在 CacheReads 卷上实现的读取 IO 实际上可以直接命中缓存而不是磁盘。 命中缓存导致单个启用缓存的磁盘可以实现额外的 IOPS。

> [!IMPORTANT]
> 每次重启 VM 后，运行基准测试之前必须对缓存进行预热。

## <a name="tools"></a>工具

### <a name="iometer"></a>Iometer

在 VM 上[下载 Iometer 工具](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download)。

#### <a name="test-file"></a>测试文件

Iometer 使用一个测试文件，该文件存储在运行基准测试的卷上。 Iometer 会尝试完成此测试文件中的读取和写入，以便测量磁盘 IOPS 和吞吐量。 如果没有提供此测试文件，Iometer 会创建一个。 在 CacheReads 和 NoCacheWrites 卷上创建名为 iobw.tst 的 200 GB 的测试文件。

#### <a name="access-specifications"></a>访问规范

规范、请求 IO 大小、读/写百分比、随机/顺序百分比都在 Iometer 中使用“访问规范”选项卡进行配置。 为下述每个方案创建一个访问规范。 创建访问规范，并使用合适的名称（例如 RandomWrites\_8K、RandomReads\_8K）进行“保存”。 在运行测试方案时，请选择相应的规范。

最大写入 IOPS 方案的访问规范示例如下所示：  
    ![最大写入 IOPS 方案的访问规范示例](../articles/virtual-machines/linux/media/premium-storage-performance/image8.png)

#### <a name="maximum-iops-test-specifications"></a>最大 IOPS 测试规范

若要演示最大 IOPS，请使用较小的请求大小。 使用 8k 请求大小，创建随机读写的规范。

| 访问规范 | 请求大小 | 随机百分比 | 读取百分比 |
| --- | --- | --- | --- |
| RandomWrites\_8K |8K |100 |0 |
| RandomReads\_8K |8K |100 |100 |

#### <a name="maximum-throughput-test-specifications"></a>最大吞吐量测试规范

若要演示最大吞吐量，请使用较大的请求大小。 使用 64k 请求大小，创建随机读写的规范。

| 访问规范 | 请求大小 | 随机百分比 | 读取百分比 |
| --- | --- | --- | --- |
| RandomWrites\_64K |64 K |100 |0 |
| RandomReads\_64K |64 K |100 |100 |

#### <a name="run-the-iometer-test"></a>运行 Iometer 测试

执行以下步骤来预热缓存

1. 使用显示在下面的值创建两个访问规范：

   | 名称 | 请求大小 | 随机百分比 | 读取百分比 |
   | --- | --- | --- | --- |
   | RandomWrites\_1MB |1 MB |100 |0 |
   | RandomReads\_1MB |1 MB |100 |100 |
1. 运行 Iometer 测试，以便使用以下参数初始化缓存磁盘。 针对目标卷使用三个工作线程，队列深度为 128。 在“测试设置”选项卡上将测试的“运行时间”持续时间设置为 2 小时。

   | 场景 | 目标卷 | 名称 | 持续时间 |
   | --- | --- | --- | --- |
   | 初始化缓存磁盘 |CacheReads |RandomWrites\_1MB |2 小时 |
1. 运行 Iometer 测试，以便使用以下参数预热缓存磁盘。 针对目标卷使用三个工作线程，队列深度为 128。 在“测试设置”选项卡上将测试的“运行时间”持续时间设置为 2 小时。

   | 场景 | 目标卷 | 名称 | 持续时间 |
   | --- | --- | --- | --- |
   | 预热缓存磁盘 |CacheReads |RandomReads\_1MB |2 小时 |

预热缓存磁盘后，继续执行下面列出的测试方案。 若要运行 Iometer 测试，请为**每个**目标卷使用至少三个工作线程。 对于每个工作线程，请选择目标卷并设置队列深度，并选择一个保存的测试规范（如下表所示），以便运行相应的测试方案。 该表还显示了运行这些测试时 IOPS 和吞吐量的预期结果。 所有方案都使用 8 KB 的较小 IO 大小，而队列深度则较高，为 128。

| 测试方案 | 目标卷 | 名称 | 结果 |
| --- | --- | --- | --- |
| 最大 读取 IOPS |CacheReads |RandomWrites\_8K |50,000 IOPS |
| 最大 写入 IOPS |NoCacheWrites |RandomReads\_8K |64,000 IOPS |
| 最大 组合 IOPS |CacheReads |RandomWrites\_8K |100,000 IOPS |
| NoCacheWrites |RandomReads\_8K | &nbsp; | &nbsp; |
| 最大 读取 MB/秒 |CacheReads |RandomWrites\_64K |524 MB/秒 |
| 最大 写入 MB/秒 |NoCacheWrites |RandomReads\_64K |524 MB/秒 |
| 组合 MB/秒 |CacheReads |RandomWrites\_64K |1000 MB/秒 |
| NoCacheWrites |RandomReads\_64K | &nbsp; | &nbsp; |

以下是组合型 IOPS 和吞吐量方案的 Iometer 测试结果的屏幕快照。

#### <a name="combined-reads-and-writes-maximum-iops"></a>读写组合最大 IOPS

![读写组合最大 IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image9.png)

#### <a name="combined-reads-and-writes-maximum-throughput"></a>读写组合最大吞吐量

![读写组合最大吞吐量](../articles/virtual-machines/linux/media/premium-storage-performance/image10.png)

### <a name="fio"></a>FIO

FIO 是一种常用工具，可以在 Linux VM 上对存储进行基准测试。 它可以灵活地选择不同的 IO 大小、顺序或随机读取和写入。 它生成的工作线程或进程可以执行指定的 I/O 操作。 可以使用作业文件指定每个工作线程必须执行的 I/O 操作类型。 我们根据以下示例所描述的方案创建了一个作业文件。 可以更改这些作业文件中的规范，以便对在高级存储上运行的不同工作负荷进行基准测试。 在这些示例中，我们将使用运行 **Ubuntu** 的标准 DS 14 VM。 运行基准测试之前，请使用基准测试部分开头所述的相同设置来预热缓存。

开始之前，请[下载 FIO](https://github.com/axboe/fio) 并将其安装在虚拟机上。

针对 Ubuntu 运行以下命令：

```
apt-get install fio
```

我们在磁盘上使用 4 个工作线程来执行写入操作，4 个工作线程来执行读取操作。 写入工作线程推动“nocache”卷上的流量，该卷有 10 个磁盘的缓存设置为“无”。 读取工作线程推动“readcache”卷上的流量，该卷有 1 个磁盘的缓存设置为“ReadOnly”。

#### <a name="maximum-write-iops"></a>最大写入 IOPS

使用以下规范创建作业文件，以便获得最大写入 IOPS。 将其命名为“fiowrite.ini”。

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

请注意以下重要事项，这些事项必须符合前面部分讨论的设计准则。 这些规范是实现最大 IOPS 所必需的。  

* 较高的队列深度：256。  
* 较小的块大小：8 KB。  
* 多个执行随机写入的线程。

运行以下命令，开始进行 30 秒的 FIO 测试：  

```
sudo fio --runtime 30 fiowrite.ini
```

进行测试时，就能够看到 VM 和高级磁盘传送的写入 IOPS 数。 如以下示例所示，DS14 VM 传送的写入 IOPS 达到了最大限制：50,000 IOPS。  
    ![正在传送的写入 IOPS VM 和高级磁盘的数量](../articles/virtual-machines/linux/media/premium-storage-performance/image11.png)

#### <a name="maximum-read-iops"></a>最大读取 IOPS

使用以下规范创建作业文件，以便获得最大读取 IOPS。 将其命名为“fioread.ini”。

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

请注意以下重要事项，这些事项必须符合前面部分讨论的设计准则。 这些规范是实现最大 IOPS 所必需的。

* 较高的队列深度：256。  
* 较小的块大小：8 KB。  
* 多个执行随机写入的线程。

运行以下命令，开始进行 30 秒的 FIO 测试：

```
sudo fio --runtime 30 fioread.ini
```

进行测试时，就能够看到 VM 和高级磁盘传送的读取 IOPS 数。 如以下示例所示，DS14 VM 传送了 64,000 个以上的读取 IOPS。 这是磁盘和缓存性能的组合。  
    ![](../articles/virtual-machines/linux/media/premium-storage-performance/image12.png)

#### <a name="maximum-read-and-write-iops"></a>最大读取和写入 IOPS

使用以下规范创建作业文件，以便获得最大读写组合 IOPS。 将其命名为“fioreadwrite.ini”。

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

请注意以下重要事项，这些事项必须符合前面部分讨论的设计准则。 这些规范是实现最大 IOPS 所必需的。

* 较高的队列深度：128。  
* 较小的块大小：4 KB。  
* 多个执行随机读取和写入的线程。

运行以下命令，开始进行 30 秒的 FIO 测试：

```
sudo fio --runtime 30 fioreadwrite.ini
```

进行测试时，就能够看到 VM 和高级磁盘传送的组合型读取和写入 IOPS 数。 如以下示例所示，DS14 VM 传送了 100,000 个以上的组合型读取和写入 IOPS。 这是磁盘和缓存性能的组合。  
    ![组合型读取和写入 IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image13.png)

#### <a name="maximum-combined-throughput"></a>最大组合吞吐量

若要获得最大读写组合吞吐量，请使用较大的块大小和大的队列深度，并通过多个线程执行读取和写入操作。 可以使用 64 KB 的块大小，128 的队列深度。
