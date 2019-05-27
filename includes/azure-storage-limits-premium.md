---
title: include 文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7b75e24e5d59206ee8330c3ca9eaf86eacbad13d
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114812"
---
### <a name="premium-performance-block-blob-storage"></a>高级性能数据块 blob 存储

高级性能块 blob 存储帐户是使用较小，千字节范围，对象的应用程序进行了优化。 它非常适合需要高事务率或一致的低延迟存储的应用程序。 高级性能数据块 blob 存储被设计为与你的应用程序扩展。 如果你打算部署需要数以百计的千位每秒的请求或千万亿字节的存储容量的应用程序，请与我们联系通过提交支持请求[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="premium-performance-filestorage-preview"></a>文件存储 （预览版） 的高级性能

高级文件使用一个名为唯一的存储帐户**文件存储 （预览版）**。 此帐户类型专为 IOPS 较高的工作负荷、 高吞吐量且一致的低滞后时间。 高级文件存储的可伸缩性与预配的共享大小。

|区域  |确定目标  |
|---------|---------|
|最大预配大小     |100 TiB     |
|共享   |无限制  |
|IOPS     |100,000    |
|流入量|4,136 MiB/s     |
|流出量|6,204 MiB/s |

 对于高级文件共享规模目标，请参阅[高级文件缩放目标](../articles/storage/common/storage-scalability-targets.md#premium-files-scale-targets)部分。

### <a name="premium-performance-page-blob-storage"></a>高级性能页 blob 存储

高级性能、常规用途 v1 或 v2 存储帐户有以下可伸缩性目标：

| 总帐户容量                            | 本地冗余存储帐户的总带宽                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| 磁盘容量：35 TB <br>快照容量：10 TB | 为入站<sup>1</sup> 和出站<sup>2</sup> 流量提供最高 50 Gbps 的带宽 |

<sup>1</sup> 发送到存储帐户的所有数据（请求）

<sup>2</sup> 从存储帐户接收的所有数据（响应）

如果要对非托管磁盘使用高级性能存储帐户并且应用程序超过了单个存储帐户的可伸缩性目标，可以考虑迁移到托管磁盘。 如果不想要迁移到托管磁盘，请将应用程序构建为使用多个存储帐户。 然后，将数据分布到这些存储帐户中。 例如，如果要将 51-TB 的磁盘附加到多个 VM，请将这些磁盘分散在两个存储帐户中。 35 TB 是单个高级存储帐户的限制。 请确保单个高级性能存储帐户永远不会超过 35 TB 的预配磁盘。