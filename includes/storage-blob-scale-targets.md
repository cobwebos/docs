---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/20/2019
ms.author: tamram
ms.openlocfilehash: aab17966862c57a52f252b3c4e9b757673078b0a
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "67173162"
---
| Resource | 目标        |
|----------|---------------|
| 单个 Blob 容器的最大大小 | 与最大存储帐户容量相同 |
| 块 Blob 或附加 Blob 中的块数上限 | 50,000 块 |
| 块 Blob 中块的最大大小 | 100 MiB |
| 块 Blob 的最大大小 | 50,000 X 100 MiB（大约 4.75 TiB） |
| 附加 Blob 中块的最大大小 | 4 MiB |
| 附加 Blob 的最大大小 | 50,000 x 4 MiB（大约 195 GiB） |
| 页 Blob 的最大大小 | 8 TiB |
| 每个 Blob 容器存储的访问策略的最大数目 | 5 |
|单个 Blob 的目标吞吐量 |上限为存储帐户的传入/传出限制<sup>1</sup> |

<sup>1</sup> 单一对象吞吐量取决于多个因素，包括但不限于：并发性、请求大小、性能层、源上传速度和目标下载速度。 若要利用[高吞吐量块 blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)性能增强功能，请使用 > 4 MiB （> 256 KiB 的 put Blob 或 put 块请求大小，以实现高级性能块 blob 存储或 Data Lake Storage Gen2）。
