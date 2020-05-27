---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 2ed88d8abb7cbe96093b68d89030e6e464a35541
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2020
ms.locfileid: "75392278"
---
| 资源 | 目标        |
|----------|---------------|
| 单个 Blob 容器的最大大小 | 与最大存储帐户容量相同 |
| 块 Blob 或附加 Blob 中的块数上限 | 50,000 块 |
| 块 Blob 中块的最大大小 | 100 MiB |
| 块 Blob 的最大大小 | 50,000 X 100 MiB（大约 4.75 TiB） |
| 附加 Blob 中块的最大大小 | 4 MiB |
| 附加 Blob 的最大大小 | 50,000 x 4 MiB（大约 195 GiB） |
| 页 Blob 的最大大小 | 8 TiB |
| 每个 Blob 容器存储的访问策略的最大数目 | 5 |
|单个 blob 的目标请求速率 | 每秒最多 500 个请求 |
|单个页 blob 的目标吞吐量 | 最高每秒 60 MiB |
|单个块 blob 的目标吞吐量 |上限为存储帐户的传入/传出限制<sup>1</sup> |

<sup>1</sup> 单个 blob 的吞吐量取决于多个因素，包括但不限于：并发性、请求大小、性能层、源上传速度和目标下载速度。 若要利用[高吞吐量块 blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)的性能增强功能，请上传更大的 blob 或块。 具体地说，对于标准存储帐户，请使用大于 4 MiB 的 Blob 或块大小调用 [Put Blob](/rest/api/storageservices/put-blob) 或 [Put Block](/rest/api/storageservices/put-block) 操作。 对于高级块 blob 或 Data Lake Storage Gen2 存储帐户，请使用大于 256 KiB 的块或 blob 大小。
