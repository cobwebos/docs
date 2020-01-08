---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 2ed88d8abb7cbe96093b68d89030e6e464a35541
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392278"
---
| 资源 | 确定目标        |
|----------|---------------|
| 单个 blob 容器的最大大小 | 与最大存储帐户容量相同 |
| 块 blob 或附加 blob 中块的最大数目 | 50,000 块 |
| 块 blob 中块的最大大小 | 100 MiB |
| 块 blob 的最大大小 | 50000 X 100 MiB （约 4.75 TiB） |
| 追加 blob 中块的最大大小 | 4 MiB |
| 追加 blob 的最大大小 | 50000 x 4 MiB （约 195 GiB） |
| 页 blob 的最大大小 | 8 TiB |
| 每个 blob 容器的最大存储访问策略数 | 5 |
|单个 blob 的目标请求速率 | 每秒最多500个请求 |
|单页 blob 的目标吞吐量 | 最高每秒 60 MiB |
|单个块 blob 的目标吞吐量 |最多存储帐户入口/出口限制<sup>1</sup> |

<sup>1</sup>单个 blob 的吞吐量取决于多个因素，包括但不限于：并发、请求大小、性能层、上传源的速度，以及下载的目标。 若要利用[高吞吐量块 blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)的性能增强功能，请上传更大的 blob 或块。 具体而言，为标准存储帐户使用大于 4 MiB 的 blob 或块大小调用[Put blob](/rest/api/storageservices/put-blob)或[put 块](/rest/api/storageservices/put-block)操作。 对于高级块 blob 或 Data Lake Storage Gen2 存储帐户，请使用大于 256 KiB 的块或 blob 大小。
