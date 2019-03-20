---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: ec75cf35bb503e49885664a6682e3be319cc45f6
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553316"
---
| 资源 | 标准文件共享 | 高级文件共享（预览版） |
|----------|---------------|------------------------------------------|
| 文件共享的最小大小 | 没有最小值;现用现付 | 100 GiB |
| 文件共享的最大大小 | 5 TiB | 5 TiB |
| 文件共享中文件的最大大小 | 1 TiB | 1 TiB |
| 文件共享中的文件的最大数量 | 无限制 | 无限制 |
| 每个共享的最大 IOPS | 1,000 IOPS | 5,120 IOPS 比较基准<br />突发时为 15,360 IOPS |
| 每个文件的存储的访问策略的最大数目共享 | 5 | 5 |
| 单个文件共享的目标吞吐量 | 最多 60 MiB/秒 | 最多 612 MiB/秒（已预配） |
| 每个文件打开图柄的最大数目 | 2,000 个打开句柄 | 2,000 个打开句柄 |
| 共享快照的最大数目 | 200 个共享快照 | 200 个共享快照 |
| 最大对象（目录和文件）名称长度 | 有 2048 个字符 | 有 2048 个字符 |
| 最大路径名组成部分（在路径 \A\B\C\D 中，每个字母是一个组成部分） | 255 个字符 | 255 个字符 |