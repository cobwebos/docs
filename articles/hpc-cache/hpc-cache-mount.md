---
title: 装载 Azure HPC 缓存
description: 如何将客户端连接到 Azure HPC 缓存服务
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 7f1d8d34d6351fc344fdb101ac8e9a96678df9d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91651422"
---
# <a name="mount-the-azure-hpc-cache"></a>装载 Azure HPC 缓存

创建缓存后，NFS 客户端可以使用简单的命令来访问它 `mount` 。 命令将 Azure HPC 缓存上的特定存储目标路径连接到客户端计算机上的本地目录。

装载命令由以下元素组成：

* 缓存的装入地址之一 (列在缓存概述页上) 
* 为 "缓存命名空间" 页上列出的存储目标 (设置的虚拟命名空间路径) 
* 要在客户端上使用的本地路径
* 优化此类 NFS 安装的成功的命令参数

缓存的 " **装入说明** " 页将收集信息和建议的选项，并创建可复制的原型装载命令。 有关详细信息， [请参阅使用装载说明实用程序](#use-the-mount-instructions-utility) 。

## <a name="prepare-clients"></a>准备客户端

请确保客户端能够按照此部分中的指导来装载 Azure HPC 缓存。

### <a name="provide-network-access"></a>提供网络访问

客户端计算机必须能够访问缓存的虚拟网络和专用子网。

例如，在同一虚拟网络中创建客户端 Vm，或者使用终结点、网关或虚拟网络中的其他解决方案从外部访问。  (请注意，除缓存本身以外的任何内容都应托管在缓存的子网中。 ) 

### <a name="install-utilities"></a>安装实用工具

安装适当的 Linux 实用程序软件以支持 NFS 装载命令：

* 对于 Red Hat Enterprise Linux 或 SuSE： `sudo yum install -y nfs-utils`
* 对于 Ubuntu 或 Debian： `sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>创建本地路径

在每个客户端上创建本地目录路径以连接到缓存。 为要装入的每个命名空间路径创建一个路径。

示例：`sudo mkdir -p /mnt/hpc-cache-1/target3`

Azure 门户中的 " [装入说明](#use-the-mount-instructions-utility) " 页包含一个可供复制的原型命令。

将客户端计算机连接到缓存时，会将此路径与表示存储目标导出的虚拟命名空间路径相关联。 为客户端将使用的每个虚拟命名空间路径创建目录。

## <a name="use-the-mount-instructions-utility"></a>使用装入说明实用工具

你可以使用 Azure 门户中的 " **装载说明** " 页来创建复制 Mount 命令。 在门户中的缓存视图的 " **配置** " 部分打开页面。

在客户端上使用该命令之前，请确保客户端满足先决条件，并且具有使用 NFS 命令所需的软件 `mount` （如上文在 [准备客户端](#prepare-clients)中所述）。

![门户中 Azure HPC 缓存实例的屏幕截图，其中加载了 "配置 > 装载说明" 页面](media/mount-instructions.png)

按照此过程创建装载命令。

1. 自定义 **客户端路径** 字段。 此字段提供一个示例命令，可用于在客户端上创建本地路径。 客户端在此目录中本地访问 Azure HPC 缓存中的内容。

   单击该字段，然后编辑该命令以包含所需的目录名称。 名称出现在字符串的末尾。 `sudo mkdir -p`

   ![位于结束位置的客户端路径字段的屏幕截图](media/mount-edit-client.png)

   完成字段编辑后，页面底部的 "装载" 命令将更新为新的客户端路径。

1. 从列表中选择 **缓存装载地址** 。 此菜单将列出缓存的所有 [客户端装入点](#find-mount-command-components)。

   平衡所有可用装载地址的客户端负载，以获得更好的缓存性能。

   ![缓存装入地址字段的屏幕截图，其中显示了用于选择的三个 IP 地址的选择器](media/mount-select-ip.png)

1. 选择要用于客户端的 **虚拟命名空间路径** 。 这些路径链接到后端存储系统上的导出。

   ![显示 "虚拟命名空间路径" 字段并打开选择器的屏幕截图。](media/mount-select-target.png)

   可以在 **命名空间** 门户页上查看和更改虚拟命名空间路径。 请参阅 [设置聚合命名空间](add-namespace-paths.md) 以了解如何操作。

   若要了解有关 Azure HPC 缓存的聚合命名空间功能的详细信息，请阅读 [规划聚合命名空间](hpc-cache-namespace.md)。

1. 步骤3中的 " **装载命令** " 字段自动使用自定义装载命令填充，该命令使用在前面的字段中设置的装入地址、虚拟命名空间路径和客户端路径。

   单击字段右侧的 "复制符号"，将其自动复制到剪贴板。

   ![原型装载命令字段的屏幕截图，显示 "复制到剪贴板" 按钮的悬停文本](media/mount-command-copy.png)

1. 使用客户端计算机上复制的装载命令将其连接到 Azure HPC 缓存。 你可以从客户端命令行直接发出命令，或在客户端安装脚本或模板中包含装载命令。

## <a name="understand-mount-command-syntax"></a>了解装载命令语法

装载命令的格式如下：

> sudo 装载 {*options*} *cache_mount_address*：/*namespace_path* *local_path*

例如：

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
root@test-client:/tmp#
```

此命令成功之后，存储导出的内容将显示在 ``hpccache`` 客户端上的目录中。

### <a name="mount-command-options"></a>装载命令选项

对于可靠的客户端装载，请在 mount 命令中传递这些设置和参数：

> 装载-o hard，proto = tcp，mountproto = tcp，retry = 30 $ {CACHE_IP_ADDRESS}:/$ {NAMESPACE_PATH} $ {LOCAL_FILESYSTEM_MOUNT_POINT}

| 建议装载命令设置 | 说明 |
--- | ---
``hard`` | 软装载到 Azure HPC 缓存与应用程序故障和可能的数据丢失相关联。
``proto=tcp`` | 此选项支持适当处理 NFS 网络错误。
``mountproto=tcp`` | 此选项支持对装载操作的网络错误进行适当处理。
``retry=<value>`` | 设置 ``retry=30`` 以避免瞬时装载失败。 （建议在前景装载中使用不同的值。）

### <a name="find-mount-command-components"></a>查找装入命令组件

如果要在不使用 " **装载说明** " 页的情况下创建装载命令，可以在 "缓存 **概述** " 页和 **命名空间** 页上的虚拟命名空间路径上找到装入地址。

![Azure HPC 缓存实例的 "概述" 页的屏幕截图，右下方的 "装入地址" 列表周围有一个突出显示框](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> 缓存装入地址对应于缓存的子网中的网络接口。 在资源组中，将列出这些 Nic，名称以结尾 `-cluster-nic-` 并使用数字。 请勿更改或删除这些接口，否则缓存将不可用。

虚拟命名空间路径显示在缓存的 **命名空间** 设置页上。

![在表的第一列周围显示突出显示框的门户设置 > "命名空间" 页的屏幕截图： "命名空间路径"](media/view-namespace-paths.png)

## <a name="next-steps"></a>后续步骤

* 若要将数据移到缓存的存储目标，请阅读 [填充新的 Azure Blob 存储](hpc-cache-ingest.md)。
