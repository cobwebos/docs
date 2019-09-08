---
title: 装载 Azure HPC 缓存
description: 如何将客户端连接到 Azure HPC 缓存服务
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 0ccd256ed399fff7c364883ada45d2d15c75a120
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775346"
---
# <a name="mount-the-azure-hpc-cache"></a>装载 Azure HPC 缓存

创建缓存后，NFS 客户端可以使用简单的装载命令来访问它。

使用在 "缓存概述" 页上列出的装入地址和创建存储目标时设置的虚拟命名空间路径。 

![Azure HPC 缓存实例的 "概述" 页的屏幕截图，右下方的 "装入地址" 列表周围有一个突出显示框](media/mount-addresses.png)

> [!NOTE] 
> 缓存装入地址对应于缓存的子网中的网络接口。 这些 nic 出现在名称以结尾`-cluster-nic-`的资源组中。 请勿更改或删除这些接口，否则缓存将不可用。

虚拟命名空间路径显示在 "**存储目标**" 页中。 
![缓存的 "存储目标" 面板的屏幕截图，表的 "路径" 列中的条目周围有一个突出显示框](media/storage-target-path.png)

## <a name="mount-command-syntax"></a>装载命令语法

使用如下所示的装载命令：

> sudo mount *cache_mount_address*：/*namespace_path* *local_path* {*options*}

例如： 

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard,intr
root@test-client:/tmp# 
```

此命令成功之后，存储导出的内容应显示在客户端上的``hpccache``目录中。 

> [!NOTE] 
> 你的客户端必须能够访问包含缓存的虚拟网络和子网。 例如，在同一虚拟网络中创建客户端 Vm，或者使用终结点、网关或虚拟网络中的其他解决方案从外部访问。 请记住，不能将其他内容托管在缓存的子网中。

### <a name="mount-command-options"></a>装载命令选项

对于可靠的客户端装载，请在 mount 命令中传递这些设置和参数： 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| 建议装载命令设置 | |
--- | --- 
``hard`` | 对 vFXT 群集的软装载与应用程序故障和可能的数据丢失相关联。 
``proto=netid`` | 此选项支持适当处理 NFS 网络错误。
``mountproto=netid`` | 此选项支持对装载操作的网络错误进行适当处理。
``retry=n`` | 设置 ``retry=30`` 以避免瞬时装载失败。 （建议在前景装载中使用不同的值。）

## <a name="next-steps"></a>后续步骤

* 若要将数据移到缓存的存储目标，请阅读[填充新的 Azure Blob 存储](hpc-cache-ingest.md)。
