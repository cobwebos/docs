---
title: include 文件
description: include 文件
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 08/30/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 4021d1ac5a0b3d9d9bf19e9fb7f74952f2471c30
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2018
ms.locfileid: "49458814"
---
| 资源 | 基本 | 标准 | 高级 |
|---|---|---|---|---|
| 存储<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| 最大映像层大小 | 20 GiB | 20 GiB | 50 GiB |
| 每分钟读取操作数<sup>2、3</sup> | 1,000 | 3,000 | 10,000 |
| 每分钟写入操作数<sup>2、4</sup> | 100 | 500 | 2,000 |
| 下载带宽 (MBps)<sup>2</sup> | 30 | 60 | 100 |
| 上传带宽 (MBps)<sup>2</sup> | 10 | 20 | 50 |
| Webhook | 2 | 10 | 100 |
| 异地复制 | 不适用 | 不适用 | [受支持][geo-replication] |
| 内容信任（预览版） | 不适用 | 不适用 | [受支持][content-trust] |

<sup>1</sup>指定的存储空间上限是每层的包含的存储空间量。 对于超出这些限制的图像存储，将每日针对每 GiB 进行额外收费。 有关费率的信息，请参阅[容器注册表定价][pricing]。

<sup>2</sup>读取操作数、写入操作数和带宽是最小估计值。 ACR 旨在随使用情况增多提升性能。

<sup>3</sup>[docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) 根据映像中的层数和清单检索行为转换为多个读取操作。

<sup>4</sup>[docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) 根据必须推送的层数转换为多个写入操作。 `docker push` 包含 ReadOps，用于检索现有映像的清单。

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md