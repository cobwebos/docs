---
title: Azure HPC 缓存预览数据引入-手动复制
description: 如何使用 cp 命令将数据移动到 Azure HPC 缓存中的 Blob 存储目标
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: rohogue
ms.openlocfilehash: 7e29cbd202b32897026bed074743de543d3fd587
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254470"
---
# <a name="azure-hpc-cache-preview-data-ingest---manual-copy-method"></a>Azure HPC 缓存（预览）数据引入-手动复制方法

本文提供了有关将数据手动复制到 Blob 存储容器以用于 Azure HPC 缓存的详细说明。 它使用多线程并行操作来优化副本速度。

若要详细了解如何将数据移到 Azure HPC 缓存的 Blob 存储，请参阅[将数据移到 AZURE Hpc 缓存的 Azure blob 存储](hpc-cache-ingest.md)。

## <a name="simple-copy-example"></a>简单复制示例

可以通过针对预定义的文件或路径集在后台一次性运行多个复制命令，在客户端上手动创建多线程复制。

Linux/UNIX ``cp`` 命令包含用于保留所有权和 mtime 元数据的 ``-p`` 参数。 可以选择性地将此参数添加到以下命令。 （添加参数会将从客户端发送到目标文件系统的文件系统调用数增加到修改元数据。）

此简单示例将并行复制两个文件：

```bash
cp /mnt/source/file1 /mnt/destination1/ & cp /mnt/source/file2 /mnt/destination1/ &
```

发出此命令后，`jobs` 命令将显示有两个线程正在运行。

## <a name="copy-data-with-predictable-file-names"></a>复制具有可预测文件名的数据

如果文件名称是可预测的，则可以使用表达式来创建并行复制线程。 

例如，如果目录包含 1000 个按顺序从 `0001` 到 `1000` 编号的文件，则你可以使用以下表达式创建 10 个并行线程，其中每个线程可以复制 100 个文件：

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination1/ & \
cp /mnt/source/file2* /mnt/destination1/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination1/ & \
cp /mnt/source/file5* /mnt/destination1/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination1/ & \
cp /mnt/source/file8* /mnt/destination1/ & \
cp /mnt/source/file9* /mnt/destination1/
```

## <a name="copy-data-with-unstructured-file-names"></a>复制包含非结构化文件名称的数据

如果文件命名结构不可预测，则可以按目录名称对文件进行分组。 

此示例收集要发送到作为后台任务运行的 ``cp`` 命令的整个目录：

```bash
/root
|-/dir1
| |-/dir1a
| |-/dir1b
| |-/dir1c
   |-/dir1c1
|-/dir1d
```

收集文件后，可以运行并行复制命令，以递归方式复制子目录及其所有内容：

```bash
cp /mnt/source/* /mnt/destination/
mkdir -p /mnt/destination/dir1 && cp /mnt/source/dir1/* mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ & 
cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ & # this command copies dir1c1 via recursion
cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
```

## <a name="when-to-add-mount-points"></a>何时添加装入点

在针对单个目标文件系统装入点执行足够的并行线程后，添加更多线程并不会给出更多吞吐量。 （将会根据每秒文件数或每秒字节数来测量吞吐量，具体取决于数据类型。）更糟糕的是，过多的线程有时会导致吞吐量下降。  

发生这种情况时，可以使用相同的远程文件系统装载路径，将客户端装入点添加到其他 Azure HPC 缓存装载点：

```bash
10.1.0.100:/nfs on /mnt/sourcetype nfs (rw,vers=3,proto=tcp,addr=10.1.0.100)
10.1.1.101:/nfs on /mnt/destination1type nfs (rw,vers=3,proto=tcp,addr=10.1.1.101)
10.1.1.102:/nfs on /mnt/destination2type nfs (rw,vers=3,proto=tcp,addr=10.1.1.102)
10.1.1.103:/nfs on /mnt/destination3type nfs (rw,vers=3,proto=tcp,addr=10.1.1.103)
```

添加客户端装入点可将附加的复制命令分叉到其他 `/mnt/destination[1-3]` 装入点，从而实现更高的并行度。  

例如，如果文件极大，可将复制命令定义为使用不同的目标路径，并从客户端并行发出更多的命令来执行复制。

```bash
cp /mnt/source/file0* /mnt/destination1/ & \
cp /mnt/source/file1* /mnt/destination2/ & \
cp /mnt/source/file2* /mnt/destination3/ & \
cp /mnt/source/file3* /mnt/destination1/ & \
cp /mnt/source/file4* /mnt/destination2/ & \
cp /mnt/source/file5* /mnt/destination3/ & \
cp /mnt/source/file6* /mnt/destination1/ & \
cp /mnt/source/file7* /mnt/destination2/ & \
cp /mnt/source/file8* /mnt/destination3/ & \
```

在以上示例中，所有三个目标装入点都是客户端文件复制进程的目标。

## <a name="when-to-add-clients"></a>何时添加客户端

最后，当客户端的处理能力达到极限时，添加更多的复制线程或装入点不会进一步提高每秒文件数或每秒字节数。 在这种情况下，可以部署包含相同装入点集、运行自身文件复制进程集的另一个客户端。 

示例：

```bash
Client1: cp -R /mnt/source/dir1/dir1a /mnt/destination/dir1/ &
Client1: cp -R /mnt/source/dir2/dir2a /mnt/destination/dir2/ &
Client1: cp -R /mnt/source/dir3/dir3a /mnt/destination/dir3/ &

Client2: cp -R /mnt/source/dir1/dir1b /mnt/destination/dir1/ &
Client2: cp -R /mnt/source/dir2/dir2b /mnt/destination/dir2/ &
Client2: cp -R /mnt/source/dir3/dir3b /mnt/destination/dir3/ &

Client3: cp -R /mnt/source/dir1/dir1c /mnt/destination/dir1/ &
Client3: cp -R /mnt/source/dir2/dir2c /mnt/destination/dir2/ &
Client3: cp -R /mnt/source/dir3/dir3c /mnt/destination/dir3/ &

Client4: cp -R /mnt/source/dir1/dir1d /mnt/destination/dir1/ &
Client4: cp -R /mnt/source/dir2/dir2d /mnt/destination/dir2/ &
Client4: cp -R /mnt/source/dir3/dir3d /mnt/destination/dir3/ &
```

## <a name="create-file-manifests"></a>创建文件清单

了解以上方法之后（每个目标多个复制线程，每个客户端多个目标，每个网络可访问的源文件系统多个客户端），请考虑以下建议：生成文件清单，然后在多个客户端中将这些清单用于复制命令。

此方案使用 UNIX ``find`` 命令创建文件或目录的清单：

```bash
user@build:/mnt/source > find . -mindepth 4 -maxdepth 4 -type d
./atj5b55c53be6-01/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-01/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-01/support/trace/rolling
./atj5b55c53be6-03/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-03/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-03/support/trace/rolling
./atj5b55c53be6-02/support/gsi/2018-07-22T21:12:06EDT
./atj5b55c53be6-02/support/pcap/2018-07-23T01:34:57UTC
./atj5b55c53be6-02/support/trace/rolling
```

将此结果重定向到文件 `find . -mindepth 4 -maxdepth 4 -type d > /tmp/foo`

然后，可以循环访问清单，并使用 BASH 命令来统计文件数目和确定子目录的大小：

```bash
ben@xlcycl1:/sps/internal/atj5b5ab44b7f > for i in $(cat /tmp/foo); do echo " `find ${i} |wc -l`    `du -sh ${i}`"; done
244    3.5M    ./atj5b5ab44b7f-02/support/gsi/2018-07-18T00:07:03EDT
9      172K    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.8M    ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T01:01:00UTC
131    13M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    6.2M    ./atj5b5ab44b7f-02/support/gsi/2018-07-20T21:59:41UTC
134    12M     ./atj5b5ab44b7f-02/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
7      16K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:12:19UTC
8      83K     ./atj5b5ab44b7f-02/support/pcap/2018-07-18T17:17:17UTC
575    7.7M    ./atj5b5ab44b7f-02/support/cores/armada_main.2000.1531980253.gsi
33     4.4G    ./atj5b5ab44b7f-02/support/trace/rolling
281    6.6M    ./atj5b5ab44b7f-01/support/gsi/2018-07-18T00:07:03EDT
15     182K    ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-18T05:01:00UTC
244    17M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-19T01:01:01UTC
299    31M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T01:01:00UTC
256    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T21:59:41UTC_partial
889    7.7M    ./atj5b5ab44b7f-01/support/gsi/2018-07-20T21:59:41UTC
262    29M     ./atj5b5ab44b7f-01/support/gsi/stats_2018-07-20T22:22:55UTC_hpccache_catchup
11     248K    ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:12:19UTC
11     88K     ./atj5b5ab44b7f-01/support/pcap/2018-07-18T17:17:17UTC
645    11M     ./atj5b5ab44b7f-01/support/cores/armada_main.2019.1531980253.gsi
33     4.0G    ./atj5b5ab44b7f-01/support/trace/rolling
244    2.1M    ./atj5b5ab44b7f-03/support/gsi/2018-07-18T00:07:03EDT
9      158K    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-18T05:01:00UTC
124    5.3M    ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-19T01:01:01UTC
152    15M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T01:01:00UTC
131    12M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T21:59:41UTC_partial
789    8.4M    ./atj5b5ab44b7f-03/support/gsi/2018-07-20T21:59:41UTC
134    14M     ./atj5b5ab44b7f-03/support/gsi/stats_2018-07-20T22:25:58UTC_hpccache_catchup
7      159K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:12:19UTC
7      157K    ./atj5b5ab44b7f-03/support/pcap/2018-07-18T17:17:17UTC
576    12M     ./atj5b5ab44b7f-03/support/cores/armada_main.2013.1531980253.gsi
33     2.8G    ./atj5b5ab44b7f-03/support/trace/rolling
```

最后，必须在客户端中编写实际的文件复制命令。  

如果有四个客户端，请使用以下命令：

```bash
for i in 1 2 3 4 ; do sed -n ${i}~4p /tmp/foo > /tmp/client${i}; done
```

如果有五个客户端，请使用如下所示的命令：

```bash
for i in 1 2 3 4 5; do sed -n ${i}~5p /tmp/foo > /tmp/client${i}; done
```

如果有六个...根据需要进行推断。

```bash
for i in 1 2 3 4 5 6; do sed -n ${i}~6p /tmp/foo > /tmp/client${i}; done
```

你将获得 *N* 个生成的文件，包含 `find` 命令输出中获取的四级目录的路径名称的每个（共 *N* 个）客户端各有一个生成的文件。 

使用每个文件生成复制命令：

```bash
for i in 1 2 3 4 5 6; do for j in $(cat /tmp/client${i}); do echo "cp -p -R /mnt/source/${j} /mnt/destination/${j}" >> /tmp/client${i}_copy_commands ; done; done
```

上述命令会返回 *N* 个文件，每个文件的每行包含一个可在客户端上作为 BASH 脚本运行的复制命令。 

目标是在多个客户端的每个客户端上并行运行这些脚本的多个线程。
