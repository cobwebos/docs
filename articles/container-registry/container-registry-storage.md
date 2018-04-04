---
title: Azure 容器注册表中的映像存储
description: 详述如何在 Azure 容器注册表中存储 Docker 容器映像，包括安全性、冗余和容量。
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 03/21/2018
ms.author: marsma
ms.openlocfilehash: df46712889a3eba54f1a2288ba93c82b21b92deb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="container-image-storage-in-azure-container-registry"></a>Azure 容器注册表中的容器映像存储

每个[基本、标准和高级](container-registry-skus.md) Azure 容器注册表均受益于高级 Azure 存储功能，如静态加密（以确保映像数据安全）和地域冗余（以实现映像数据保护）。 以下部分介绍 Azure 容器注册表 (ACR) 中映射存储的功能和限制。

## <a name="encryption-at-rest"></a>静态加密

注册表中的所有容器映像均已进行静态加密。 Azure 在存储映像之前自动对其进行加密，当应用程序和服务请求映像时即时对其进行解密。

## <a name="geo-redundant-storage"></a>异地冗余存储

Azure 使用异地冗余存储方案来防止容器映像丢失。 Azure 容器注册表会自动将容器映像复制到多个地理位置相距遥远的数据中心，以防止其在区域存储失败时丢失。

## <a name="geo-replication"></a>异地复制

对于需要更高可用性保证的方案，请考虑使用高级注册表的[异地复制](container-registry-geo-replication.md)功能。 异地复制可帮助在全部区域失败（而不仅仅是一个存储失败）时，防止丢失对注册表的访问权限。 异地复制还提供了其他好处，如临近网络映像存储，以便在分布式开发或部署方案中实现更快地推送和拉取。

## <a name="image-limits"></a>映像限制

下表介绍了针对 Azure 容器注册表设置的容器映像和存储限制。

| 资源 | 限制 |
| -------- | :---- |
| 存储库 | 无限制 |
| 映像 | 无限制 |
| 层 | 无限制 |
| 标记 | 无限制|
| 存储 | 5 TB |

大量的存储库和标记可能会影响注册表的性能。 应将定期使用 [Azure CLI](/cli/azure/acr)、ACR [REST API](/rest/api/containerregistry/) 或 [Azure 门户][portal] 删除未使用的存储库、标记和映像作为注册表维护程序的一部分。 已删除的注册表资源（如存储库、映像和标记）在删除后*无法*恢复。

## <a name="storage-cost"></a>存储成本

有关定价的完整详细信息，请参阅 [Azure 容器注册表定价][pricing]。

## <a name="next-steps"></a>后续步骤

有关不同 Azure 容器注册表 SKU（基本、标准和高级）的详细信息，请参阅 [Azure 容器注册表 SKU](container-registry-skus.md)。

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: http://aka.ms/acr/pricing

<!-- LINKS - Internal -->
