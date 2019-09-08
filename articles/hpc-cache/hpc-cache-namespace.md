---
title: 创建 Azure HPC 缓存
description: 如何创建 Azure HPC 缓存实例
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 41361a3513c052d960726498d55745bf09afdfbb
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775190"
---
# <a name="configure-aggregated-namespace"></a>配置聚合命名空间
<!-- change link in GUI -->

Azure HPC 缓存允许客户端通过虚拟命名空间访问各种存储系统，该命名空间隐藏后端存储系统的详细信息。

添加存储目标时，将设置面向客户端的 filepath。 客户端计算机安装此 filepath。 你可以更改与该路径关联的存储目标。 例如，你可以将硬件存储系统替换为云存储，而无需重写面向客户端的过程。

## <a name="aggregated-namespace-example"></a>聚合命名空间示例

规划聚合命名空间，以便客户端计算机可以轻松地访问所需的信息，管理员和工作流工程师可以轻松区分路径。

例如，假设有一个系统使用 Azure HPC 缓存实例来处理存储在 Azure Blob 中的数据。 此分析需要存储在本地数据中心的模板文件。

模板数据存储在数据中心中，此作业所需的信息存储在这些子目录中：

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

数据中心存储系统将公开以下导出： 

    /
    /goldline
    /goldline/templates

使用[CLFSLoad 实用程序](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload)将要分析的数据复制到名为 "sourcecollection 进行" 的 Azure Blob 存储容器。

若要允许通过缓存轻松访问，请考虑使用以下虚拟命名空间路径创建存储目标：

| 后端 NFS filepath 或 Blob 容器 | 虚拟命名空间路径 |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| sourcecollection 进行                        | /source               |

由于 NFS 源路径是相同导出的子目录，因此需要从同一存储目标定义多个命名空间路径。 

| 存储目标主机名  | NFS 导出路径      | 子目录路径 | 命名空间路径    |
|--------------------------|----------------------|-------------------|-------------------|
| *IP 地址或主机名* | /goldline/templates  | acme2017/sku798   | /templates/sku798 |
| *IP 地址或主机名* | /goldline/templates  | acme2017/sku980   | /templates/sku980 |

客户端应用程序可以装载缓存并轻松访问聚合命名空间 filepaths/source、/templates/sku798 和/templates/sku980。

## <a name="next-steps"></a>后续步骤

确定如何设置虚拟文件系统后，请[创建存储目标](hpc-cache-add-storage.md)，将后端存储映射到面向客户端的虚拟 filepaths。
