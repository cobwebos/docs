---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 6cf9be653da2dd587b93724b6e319dc4d20686c2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536417"
---
| 资源 | 标准文件共享 | 高级文件共享 |
|----------|---------------|------------------------------------------|
| 文件共享的最小大小 | 无最小值;即付即用 | 100 GiB；未预配 |
| 文件共享的最大大小 | 100 TiB *，5 TiB | 100 TiB |
| 文件共享中文件的最大大小 | 1 TiB | 1 TiB |
| 文件共享中的文件数上限 | 无限制 | 无限制 |
| 每个共享的最大 IOPS | 10000 IOPS *，1000 IOPS | 100,000 IOPS |
| 每个文件共享的存储的访问策略的最大数目 | 5 | 5 |
| 单个文件共享的目标吞吐量 | 最高 300 MiB/秒 *，最高 60 MiB/秒  | 查看高级文件共享流入量和流出量值|
| 单个文件共享的最大流出量 | 查看标准文件共享目标吞吐量 | 最多 6,204 MiB/秒 |
| 单个文件共享的最大流入量 | 查看标准文件共享目标吞吐量 | 最多 4,136 MiB/秒 |
| 每个文件打开图柄的最大数目 | 2,000 个打开句柄 | 2,000 个打开句柄 |
| 共享快照的最大数目 | 200 个共享快照 | 200 个共享快照 |
| 最大对象（目录和文件）名称长度 | 2,048 个字符 | 2,048 个字符 |
| 最大路径名组成部分（在路径 \A\B\C\D 中，每个字母是一个组成部分） | 255 个字符 | 255 个字符 |

\*标准文件共享上的默认值为 5 TiB，有关如何增加标准文件100共享的详细信息，请参阅[启用和创建大型文件](../articles/storage/files/storage-files-how-to-create-large-file-share.md)共享。
