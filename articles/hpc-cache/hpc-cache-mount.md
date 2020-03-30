---
title: 装载 Azure HPC 缓存
description: 如何将客户端连接到 Azure HPC 缓存服务
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: d906ed9a1a55e936c6374806a9037085c47e3b01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73582215"
---
# <a name="mount-the-azure-hpc-cache"></a>装载 Azure HPC 缓存

创建缓存后，NFS 客户端可以使用简单的装载命令访问它。

装载命令由两个元素组成：

* 缓存的装载地址之一（列在缓存概述页上）
* 创建存储目标时设置的虚拟命名空间路径

![Azure HPC 缓存实例的"概述"页面的屏幕截图，右下角的装载地址列表周围有一个突出显示框](media/hpc-cache-mount-addresses.png)

> [!NOTE] 
> 缓存装载地址对应于缓存子网中的网络接口。 在资源组中，这些 NIC 列出的名称以 结尾`-cluster-nic-`，以数字结尾。 不要更改或删除这些接口，否则缓存将不可用。

虚拟命名空间路径显示在 **"存储目标"** 页中。 单击单个存储目标名称以查看其详细信息，包括与其关联的聚合命名空间路径。

![缓存存储目标面板的屏幕截图，在表的 Path 列中条目周围有一个突出显示框](media/hpc-cache-view-namespace-paths.png)

## <a name="mount-command-syntax"></a>装载命令语法

使用安装命令，如下所示：

> 苏多安装*cache_mount_address*：/*namespace_pathlocal_path* *local_path* [*选项*]

示例：

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard
root@test-client:/tmp# 
```

此命令成功后，存储导出的内容应在客户端的``hpccache``目录中可见。

> [!NOTE] 
> 客户端必须能够访问存放缓存的虚拟网络和子网。 例如，在同一虚拟网络中创建客户端 VM，或使用虚拟网络中的终结点、网关或其他解决方案从外部访问。 请记住，在缓存的子网内无法托管任何其他内容。

### <a name="mount-command-options"></a>装载命令选项

对于可靠的客户端装载，在装载命令中传递这些设置和参数： 

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| 建议的装载命令设置 | |
--- | --- 
``hard`` | Azure HPC 缓存的软装载与应用程序故障和可能的数据丢失相关联。 
``proto=netid`` | 此选项支持适当处理 NFS 网络错误。
``mountproto=netid`` | 此选项支持对装载操作的网络错误进行适当处理。
``retry=n`` | 设置 ``retry=30`` 以避免瞬时装载失败。 （建议在前景装载中使用不同的值。）

## <a name="next-steps"></a>后续步骤

* 要将数据移动到缓存的存储目标，请阅读[填充新的 Azure Blob 存储](hpc-cache-ingest.md)。
