---
title: include 文件
description: include 文件
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 03/11/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0090f02382e024e5539383328b55d58798002d63
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79117141"
---
| 资源 | 基本 | Standard | Premium |
|---|---|---|---|
| 存储<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| 最大映像层大小 | 200 GiB | 200 GiB | 200 GiB |
| 每分钟读取操作数<sup>2、3</sup> | 1,000 | 3,000 | 10,000 |
| 每分钟写入操作数<sup>2、4</sup> | 100 | 500 | 2,000 |
| 下载带宽 (MBps)<sup>2</sup> | 30 | 60 | 100 |
| 上传带宽 (MBps)<sup>2</sup> | 10 | 20 | 50 |
| Webhook | 2 | 10 | 500 |
| 异地复制 | 空值 | 空值 | [支持][geo-replication] |
| 内容信任 | 不可用 | 不可用 | [支持][content-trust] |
| 虚拟网络访问 | 不可用 | 不可用 | [预览][vnet] |
| 私有链接集成 | 不可用 | 不可用 | [预览][plink] |
| 客户管理的密钥 | 不可用 | 不可用 | [预览][cmk] |
| 存储库范围内的权限 | 不可用 | 不可用 | [预览][token]|
| &bull;令牌 | 不可用 | 不可用 | 20,000 |
| &bull;范围映射 | 不可用 | 不可用 | 20,000 |
| &bull;按范围映射的存储库 | 不可用 | 不可用 | 500 |


<sup>1</sup> 指定的存储上限是每层包含** 的存储量。 对于超出这些限制的图像存储，将每日针对每 GiB 进行额外收费。 有关费率的信息，请参阅 [Azure 容器注册表定价][pricing]。

<sup>2</sup>读取操作数**、写入操作数** 和带宽** 是最小估计值。 Azure 容器注册表根据使用需求努力提高性能。

<sup>3</sup>[docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) 根据映像中的层数和清单检索行为转换为多个读取操作。

<sup>4</sup>[docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) 根据必须推送的层数转换为多个写入操作。 `docker push` 包含 ReadOps**，用于检索现有映像的清单。

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md