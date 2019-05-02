---
title: include 文件
description: include 文件
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 04/29/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: d20e266d1331fc15e65b2d119468483ff53a4c06
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64951503"
---
| 资源 | 基本 | 标准 | 高级 |
|---|---|---|---|
| 存储<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| 图像最大层大小 | 200 GiB | 200 GiB | 200 GiB |
| 每分钟读取操作数<sup>2、3</sup> | 1,000 | 3,000 | 10,000 |
| 每分钟写入操作数<sup>2、4</sup> | 100 | 500 | 2,000 |
| 下载带宽 (MBps)<sup>2</sup> | 30 | 60 | 100 |
| 上传带宽 (MBps)<sup>2</sup> | 10 | 20 | 50 |
| Webhook | 2 | 10 | 100 |
| 异地复制 | 不适用 | 不适用 | [受支持][geo-replication] |
| 内容信任（预览版） | 不适用 | 不适用 | [受支持][content-trust] |

<sup>1</sup>指定的存储限制是量*包含*每个层的存储。 对于超出这些限制的图像存储，将每日针对每 GiB 进行额外收费。 汇率信息，请参阅[Azure 容器注册表定价][pricing]。

<sup>2</sup>*读取操作数*，*写入操作数*，并且*带宽*是最小估计值。 Azure 容器注册表致力于提高性能，使用情况。

<sup>3</sup>A [docker 拉取](https://docs.docker.com/registry/spec/api/#pulling-an-image)会转换为基于映像和清单检索中的层数的多个读取操作。

<sup>4</sup>A [docker 推送](https://docs.docker.com/registry/spec/api/#pushing-an-image)会转换为多个写入操作，根据必须推送的层数。 `docker push` 包含 ReadOps，用于检索现有映像的清单。

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
