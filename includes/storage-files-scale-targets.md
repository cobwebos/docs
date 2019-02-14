---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 72b7bd722bc7634a707e10cbcd45768140ed527a
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2019
ms.locfileid: "55757625"
---
| 资源 | 标准文件共享 | 高级文件共享（预览版） |
|----------|---------------|------------------------------------------|
| 文件共享的最小大小 | （没有最小值；即用即付） | 100 GiB |
| 文件共享的最大大小 | 5 TiB | 5 TiB |
| 文件共享中文件的最大大小 | 1 TiB | 1 TiB |
| 文件共享中的文件数上限 | 无限制 | 无限制 |
| 每个共享的最大 IOPS | 1000 IOPS | 5120 IOPS 基线<br />突发时为 15,360 IOPS |
| 每个文件共享的存储的访问策略的最大数目 | 5 | 5 |
| 单个文件共享的目标吞吐量 | 最多 60 MiB/秒 | 最多 612 MiB/秒（已预配） |
| 每个文件打开图柄的最大数目 | 2,000 个打开句柄 | 2,000 个打开句柄 |
| 共享快照的最大数目 | 200 个共享快照 | 200 个共享快照 |
| 最大对象（目录和文件）名称长度 | 2048 个字符 | 2048 个字符 |
| 最大路径名组成部分（在路径 \A\B\C\D 中，每个字母是一个组成部分） | 255 个字符 | 255 个字符 |