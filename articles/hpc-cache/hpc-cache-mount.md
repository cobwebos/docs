---
title: 装载 Azure HPC 缓存
description: 如何将客户端连接到 Azure HPC 缓存服务
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: a44232f06b455e20530271723e816c2117b339a0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458333"
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

在每个客户端上创建本地目录路径以连接到缓存。 为要装载的每个命名空间路径创建路径。

示例：`sudo mkdir -p /mnt/hpc-cache-1/target3`

Azure 门户中的["装载说明"](#use-the-mount-instructions-utility)页包含可以复制的原型命令。

将客户端计算机连接到缓存时，将此路径与表示存储目标导出的虚拟命名空间路径相关联。 为客户端将使用的每个虚拟命名空间路径创建目录。

## <a name="use-the-mount-instructions-utility"></a>使用装载说明实用程序

可以使用 Azure 门户中的 **"装载指令"** 页创建可复制装载命令。 从门户中缓存视图的 **"配置**"部分打开页面。

在客户端上使用 命令之前，请确保客户端满足先决条件，并具有使用 NFS`mount`命令所需的软件，如上述在["准备客户端](#prepare-clients)"中所述。

![门户中的 Azure HPC 缓存实例的屏幕截图，加载了"配置>装载指令页](media/mount-instructions.png)

按照此过程创建装载命令。

1. 自定义**客户端路径**字段。 此字段提供一个示例命令，可用于在客户端上创建本地路径。 客户端在此目录中本地访问 Azure HPC 缓存中的内容。

   单击该字段并编辑该命令以包含所需的目录名称。 该名称显示在字符串的末尾`sudo mkdir -p`

   ![客户端路径字段的屏幕截图，光标位于末尾](media/mount-edit-client.png)

   完成编辑字段后，页面底部的装载命令将更新新客户端路径。

1. 从列表中选择**缓存装载地址**。 此菜单列出了缓存的所有[客户端装载点](#find-mount-command-components)。

   平衡所有可用装载地址的客户端负载，以更好的缓存性能。

   ![缓存装载地址字段的屏幕截图，选择器显示三个 IP 地址可供选择](media/mount-select-ip.png)

1. 选择要用于客户端的**虚拟命名空间路径**。 这些路径链接到后端存储系统上的导出。

   ![命名空间路径字段的屏幕截图，选择器打开](media/mount-select-target.png)

   您可以在"存储目标"门户页上查看和更改虚拟命名空间路径。 阅读[添加存储目标](hpc-cache-add-storage.md)以查看如何。

   要了解有关 Azure HPC 缓存聚合命名空间功能的详细信息，请阅读[规划聚合命名空间](hpc-cache-namespace.md)。

1. 步骤三中的 **"装载"命令**字段使用自定义装载命令自动填充，该命令使用在前面的字段中设置的装载地址、虚拟命名空间路径和客户端路径。

   单击字段右侧的复制符号，自动将其复制到剪贴板。

   ![命名空间路径字段的屏幕截图，选择器打开](media/mount-command-copy.png)

1. 使用客户端计算机上的复制装载命令将其连接到 Azure HPC 缓存。 可以直接从客户端命令行发出该命令，或在客户端设置脚本或模板中包括装载命令。

## <a name="understand-mount-command-syntax"></a>了解装载命令语法

装载命令具有以下形式：

> sudo 安装 [*选项*] *cache_mount_address*：/*namespace_pathlocal_path* *local_path*

示例：

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
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

虚拟命名空间路径显示在每个存储目标的详细信息页上。 单击单个存储目标名称以查看其详细信息，包括与其关联的聚合命名空间路径。

![存储目标详细信息页面的屏幕截图（标题"更新存储目标"）。 表的虚拟命名空间路径列中条目周围有一个突出显示框](media/hpc-cache-view-namespace-paths.png)

## <a name="next-steps"></a>后续步骤

* 要将数据移动到缓存的存储目标，请阅读[填充新的 Azure Blob 存储](hpc-cache-ingest.md)。
