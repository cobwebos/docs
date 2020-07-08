---
title: include 文件
description: include 文件
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 06/18/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 643cf8fd400adf06bf61f070947bd78ba7be50eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85242231"
---
| 资源 | 基本 | Standard | Premium |
|---|---|---|---|
| 包含的存储<sup>1</sup> （GiB） | 10 | 100 | 500 |
| 存储限制（TiB） | 20| 20 | 20 |
| 最大图像层大小（GiB） | 200 | 200 | 200 |
| 每分钟读取操作数<sup>2、3</sup> | 1,000 | 3,000 | 10,000 |
| 每分钟写入操作数<sup>2、4</sup> | 100 | 500 | 2,000 |
| 下载带宽 (MBps)<sup>2</sup> | 30 | 60 | 100 |
| 上传带宽 (MBps)<sup>2</sup> | 10 | 20 | 50 |
| Webhook | 2 | 10 | 500 |
| 异地复制 | 空值 | 空值 | [支持][geo-replication] |
| 内容信任 | 空值 | 空值 | [支持][content-trust] |
| 具有专用终结点的专用链接 | 空值 | 空值 | [支持][plink] |
| 服务终结点 VNet 访问 | 空值 | 空值 | [预览][vnet] |
| 客户管理的密钥 | 空值 | 空值 | [支持][cmk] |
| 存储库范围内的权限 | 空值 | 空值 | [预览][token]|
| &bull; 令牌 | 空值 | 空值 | 20,000 |
| &bull; 范围映射 | 空值 | 空值 | 20,000 |
| &bull; 每个范围映射的存储库 | 不适用 | 不适用 | 500 |


每个层的每日费率中包含<sup>1</sup>个存储。 对于其他存储，将按每个 GiB 支付额外的每日费率，最高可达存储限制。 有关费率的信息，请参阅 [Azure 容器注册表定价][pricing]。

<sup>2</sup>读取操作数、写入操作数和带宽是最小估计值。 Azure 容器注册表致力于根据使用情况来提高性能。

<sup>3</sup>[docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) 将根据映像中的层数和清单检索行为转换为多个读取操作。

<sup>4</sup>[docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) 将根据必须推送的层数转换为多个写入操作。 `docker push` 包含 ReadOps，用于检索现有映像的清单。

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md