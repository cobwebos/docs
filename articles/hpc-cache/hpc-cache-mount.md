---
title: 装载 Azure HPC 缓存
description: 如何将客户端连接到 Azure HPC 缓存服务
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: f176e30cfaf9a52e4f58091b7fc76098a4c88a48
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657357"
---
# <a name="mount-the-azure-hpc-cache"></a>装载 Azure HPC 缓存

创建缓存后，NFS 客户端可以使用简单`mount`命令访问它。 该命令将 Azure HPC 缓存上的特定存储目标路径连接到客户端计算机上的本地目录。

装载命令由以下元素组成：

* 缓存的装载地址之一（列在缓存概述页上）
* 创建存储目标时设置的虚拟命名空间路径
* 要在客户端上使用的本地路径
* 优化此类 NFS 装载成功的命令参数

缓存的 **"装载指令"** 页会收集信息和推荐的选项，并创建可以复制的原型装载命令。 阅读[使用装载说明实用程序](#use-the-mount-instructions-utility)了解详细信息。

## <a name="prepare-clients"></a>准备客户

按照本节中的指南，确保客户端能够装载 Azure HPC 缓存。

### <a name="provide-network-access"></a>提供网络访问

客户端计算机必须具有对缓存的虚拟网络和专用子网的网络访问权限。

例如，在同一虚拟网络中创建客户端 VM，或使用虚拟网络中的终结点、网关或其他解决方案从外部访问。 （请记住，除了缓存本身之外，其他内容都不应托管在缓存的子网内。

### <a name="install-utilities"></a>安装实用程序

安装适当的 Linux 实用程序软件以支持 NFS 装载命令：

* 对于红帽企业 Linux 或 SuSE：`sudo yum install -y nfs-utils`
* 对于乌本图或德比安：`sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>创建本地路径

在每个客户端上创建本地目录路径以连接到缓存。 为要装载的每个存储目标创建路径。

示例： `sudo mkdir -p /mnt/hpc-cache-1/target3`

## <a name="use-the-mount-instructions-utility"></a>使用装载说明实用程序

从 Azure 门户中的缓存视图的 **"配置**"部分打开 **"装载说明"** 页。

![门户中的 Azure HPC 缓存实例的屏幕截图，加载了"配置>装载指令页](media/mount-instructions.png)

装载命令页包括有关客户端装载过程和先决条件的信息，以及可用于创建可复制装载命令的字段。

要使用此页面，请按照以下步骤操作：

<!--1.  In step one of **Mounting your file system**, enter the path that the client will use to access the Azure HPC Cache storage target.

   * This path is local to the client.
   * After you provide the directory name, the field populates with a command you can copy. Use this command on the client directly or in a setup script to create the directory path on the client VM. -->

1. 查看客户端先决条件并安装使用 NFS`mount`命令所需的实用程序，如上述[在"准备客户端](#prepare-clients)"中所述。

1. **安装文件系统**的步骤一<!-- label will change --> 给出了在客户端上创建本地路径的示例命令。 这是客户端将用于从 Azure HPC 缓存访问内容的路径。

   请注意路径名称，以便在需要时可以在命令中对其进行修改。

1. 在步骤 2 中，选择其中一个可用的 IP 地址。 此处列出了缓存的所有[客户端装载点](#find-mount-command-components)。 确保您有一个系统来平衡所有 IP 地址之间的负载。

1. 步骤三中的字段自动填充原型装载命令。 单击字段右侧的复制符号，自动将其复制到剪贴板。

   > [!NOTE]
   > 在使用复制命令之前，请检查该命令。 您可能需要自定义客户端装载路径和存储目标虚拟命名空间路径，这些路径在此接口中尚未选择。 您还应更新装载命令选项以反映以下[建议的选项](#mount-command-options)。 阅读["了解装载"命令语法](#understand-mount-command-syntax)以寻求帮助。

1. 在客户端计算机上使用复制的装载命令（如果需要进行编辑）将其连接到 Azure HPC 缓存上的存储目标。 可以直接从客户端命令行发出该命令，或在客户端设置脚本或模板中包括装载命令。

## <a name="understand-mount-command-syntax"></a>了解装载命令语法

装载命令具有以下形式：

> 苏多安装*cache_mount_address*：/*namespace_pathlocal_path* *local_path* [*选项*]

示例：

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -o hard,proto=tcp,mountproto=tcp,retry=30
root@test-client:/tmp#
```

此命令成功后，存储导出的内容将在客户端的``hpccache``目录中可见。

### <a name="mount-command-options"></a>装载命令选项

对于可靠的客户端装载，在装载命令中传递这些设置和参数：

> 安装 -o 硬，proto_tcp，mountproto_tcp，retry_30 {CACHE_IP_ADDRESS}：/$NAMESPACE_PATH} {LOCAL_FILESYSTEM_MOUNT_POINT}

| 建议的装载命令设置 | |
--- | ---
``hard`` | Azure HPC 缓存的软装载与应用程序故障和可能的数据丢失相关联。
``proto=tcp`` | 此选项支持适当处理 NFS 网络错误。
``mountproto=tcp`` | 此选项支持对装载操作的网络错误进行适当处理。
``retry=<value>`` | 设置 ``retry=30`` 以避免瞬时装载失败。 （建议在前景装载中使用不同的值。）

### <a name="find-mount-command-components"></a>查找装载命令组件

如果要创建装载命令而不使用 **"装载指令"** 页，可以在缓存**概述**页上找到装载地址和**存储目标**页上的虚拟命名空间路径。

![Azure HPC 缓存实例的"概述"页面的屏幕截图，右下角的装载地址列表周围有一个突出显示框](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> 缓存装载地址对应于缓存子网中的网络接口。 在资源组中，这些 NIC 列出的名称以 结尾`-cluster-nic-`，以数字结尾。 不要更改或删除这些接口，否则缓存将不可用。

虚拟命名空间路径显示在 **"存储目标"** 页中。 单击单个存储目标名称以查看其详细信息，包括与其关联的聚合命名空间路径。

![缓存存储目标面板的屏幕截图，在表的 Path 列中条目周围有一个突出显示框](media/hpc-cache-view-namespace-paths.png)

## <a name="next-steps"></a>后续步骤

* 要将数据移动到缓存的存储目标，请阅读[填充新的 Azure Blob 存储](hpc-cache-ingest.md)。
