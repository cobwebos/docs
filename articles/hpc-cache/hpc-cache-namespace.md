---
title: 创建 Azure HPC 缓存
description: 如何创建 Azure HPC 缓存实例
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: aaa939051a1aeafdb0650119772fc7214506aa8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73582177"
---
# <a name="plan-the-aggregated-namespace"></a>规划聚合命名空间

Azure HPC 缓存允许客户端通过隐藏后端存储系统详细信息的虚拟命名空间访问各种存储系统。

添加存储目标时，将设置面向客户端的文件路径。 客户端计算机装载此文件路径，可以向缓存发出文件读取请求，而不是直接安装存储系统。

由于 Azure HPC 缓存管理此虚拟文件系统，因此无需更改面向客户端的路径即可更改存储目标。 例如，您可以将硬件存储系统替换为云存储，而无需重写面向客户端的过程。

## <a name="aggregated-namespace-example"></a>聚合命名空间示例

规划聚合命名空间，以便客户端计算机能够方便地到达所需的信息，以便管理员和工作流工程师可以轻松地区分路径。

例如，考虑使用 Azure HPC 缓存实例来处理存储在 Azure Blob 中的数据的系统。 分析需要存储在本地数据中心中的模板文件。

模板数据存储在数据中心中，此作业所需的信息存储在以下子目录中：

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

数据中心存储系统公开这些导出：

    /
    /goldline
    /goldline/templates

要分析的数据已通过使用[CLFSLoad 实用程序](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload)复制到名为"源集合"的 Azure Blob 存储容器。

为了便于通过缓存访问，请考虑使用这些虚拟命名空间路径创建存储目标：

| 后端存储系统 <br/> （NFS 文件路径或 Blob 容器） | 虚拟命名空间路径 |
|-----------------------------------------|------------------------|
| /黄金线/模板/acme2017/sku798     | /模板/sku798      |
| /黄金线/模板/acme2017/sku980     | /模板/sku980      |
| 源集合                        | /来源/               |

NFS 存储目标可以有多个虚拟命名空间路径，只要每个路径引用唯一导出路径。

由于 NFS 源路径是同一导出的子目录，因此需要从同一存储目标定义多个命名空间路径。

| 存储目标主机名  | NFS 导出路径      | 子目录路径 | 命名空间路径    |
|--------------------------|----------------------|-------------------|-------------------|
| *IP 地址或主机名* | /黄金线/模板  | acme2017/sku798   | /模板/sku798 |
| *IP 地址或主机名* | /黄金线/模板  | acme2017/sku980   | /模板/sku980 |

客户端应用程序可以装载缓存并轻松访问聚合的命名空间文件路径``/source````/templates/sku798``和 。 ``/templates/sku980``

## <a name="next-steps"></a>后续步骤

在决定如何设置虚拟文件系统后，[创建存储目标](hpc-cache-add-storage.md)以将后端存储映射到面向客户端的虚拟文件路径。
