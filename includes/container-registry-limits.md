---
title: include 文件
description: include 文件
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 11/05/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6707e844948ac76d4cec29faf69d80b3c9cb3c0f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392404"
---
| 资源 | 基本 | 标准 | 高级 |
|---|---|---|---|
| 存储<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| 最大图像层大小 | 200 GiB | 200 GiB | 200 GiB |
| 每分钟读取操作数<sup>2、3</sup> | 1,000 | 3,000 | 10,000 |
| 每分钟写入操作数<sup>2、4</sup> | 100 | 500 | 2,000 |
| 下载带宽 (MBps)<sup>2</sup> | 30 | 60 | 100 |
| 上传带宽 (MBps)<sup>2</sup> | 10 | 20 | 50 |
| Webhook | 2 | 10 | 500 |
| 异地复制 | N/A | N/A | [支持][geo-replication] |
| 内容信任 | N/A | N/A | [支持][content-trust] |
| 虚拟网络访问 | N/A | N/A | [预览][vnet] |
| 存储库范围内的权限 | N/A | N/A | [预览][token]|
| &bull; 标记 | N/A | N/A | 20,000 |
| &bull; 作用域映射 | N/A | N/A | 20,000 |
| 每个作用域映射的 &bull; 存储库 | N/A | N/A | 500 |


<sup>1</sup>指定的存储限制是每个层的*包含*存储量。 对于超出这些限制的图像存储，将每日针对每 GiB 进行额外收费。 有关速率信息，请参阅[Azure 容器注册表定价][pricing]。

<sup>2</sup>读取操作数、写入操作数和带宽是最小估计值。 Azure 容器注册表致力于根据使用情况来提高性能。

<sup>3</sup>[Docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image)转换为多个基于图像中的层数的读取操作，以及清单检索。

<sup>4</sup>[Docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image)根据必须推送的层数，转换为多个写入操作。 `docker push` 包含 ReadOps，用于检索现有映像的清单。

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md