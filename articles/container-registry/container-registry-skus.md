---
title: Azure 容器注册表 SKU
description: 比较 Azure 容器注册表中的不同服务层。
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 08/30/2018
ms.author: marsma
ms.openlocfilehash: eb3a1745677871211df05d18e28d32061f360bac
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301810"
---
# <a name="azure-container-registry-skus"></a>Azure 容器注册表 SKU

Azure 容器注册表 (ACR) 分为多个服务层（称为“SKU”）。 这些 SKU 提供可预测的定价和多个选项，用来适应你在 Azure 中的专用 Docker 注册表的容量和使用模式。

| SKU | 托管 | Description |
| --- | :-------: | ----------- |
| **基本** | 是 | 供开发者了解 Azure 容器注册表的入口点（已优化过成本）。 基本注册表的编程功能（Azure Active Directory 身份验证集成、映像删除和 Webhook）与标准注册表和高级注册表相同。 但其附带的存储和映像吞吐量最适合使用较少的场景。 |
| **标准** | 是 | 标准注册表的功能与基本注册表相同。不同之处在于，前者附带更多的存储和映像吞吐量。 标准注册表应能够满足大部分生产方案的需求。 |
| **高级** | 是 | 高级注册表附带的存储和并发操作数最多，支持大容量方案。 除增加了映像吞吐容量之外，高级注册表还增添了其他功能，例如[异地复制][container-registry-geo-replication]可用于跨多个区域管理一个注册表，以及[内容信任（预览版）](container-registry-content-trust.md)可用于映像标记签名。 |
| 经典<sup>1</sup> | 否 | 此 SKU 在 Azure 中启用了初始版 Azure 容器注册表服务。 经典注册表由 Azure 在订阅中创建的存储帐户提供支持，这会限制 ACR 提供更高级功能，如增加吞吐量和异地复制。 |

<sup>1</sup> 经典 SKU 将在 **2019 年 3 月****弃用**。 请对所有新的容器注册表使用基本、标准或高级 SKU。

选择更高级别的 SKU 可以提供更好的性能和缩放，但是，所有托管的 SKU 都提供相同的编程功能。 使用多个服务层，你可以从基本层开始，然后随着注册表使用量增长转换到标准和高级层。

## <a name="managed-vs-unmanaged"></a>托管的与非托管的

基本、标准和高级 SKU 统称为“托管的”注册表，而经典注册表是“非托管的”。 两者之间的主要区别是容器映像的存储方式。

### <a name="managed-basic-standard-premium"></a>托管的（基本、标准、高级）

托管的注册表受益于完全由 Azure 托管的映像存储。 也就是说，存储映像的存储帐户不会显示在 Azure 订阅中。 使用托管的注册表 SKU 可以获得几个优势，[Azure 容器注册表中的容器映像存储][container-registry-storage]中对此进行了深入讨论。 本文重点介绍托管的注册表 SKU 及其功能。

### <a name="unmanaged-classic"></a>非托管的（经典）

> [!IMPORTANT]
> 经典 SKU 已弃用，2019 年 3 月后将无法使用。 请对所有新的注册表使用基本、标准或高级 SKU。

经典注册表是“非托管的”，这是因为，为经典注册表提供支持的存储帐户位于“你的” Azure 订阅中。 因此，将由你负责管理存储着你的容器映像的存储帐户。 使用非托管的注册表时，无法在需求发生变化时在各种 SKU 之间切换（除非[升级][container-registry-upgrade]到托管的注册表），并且无法使用托管的注册表的多项功能（例如，容器映像删除、[异地复制][container-registry-geo-replication]和 [Webhook][container-registry-webhook]）。

有关将经典注册表升级到托管的 SKU 之一的详细信息，请参阅[升级经典注册表][container-registry-upgrade]。

## <a name="sku-feature-matrix"></a>SKU 功能矩阵

下表详细介绍了基本、标准和高级服务层的功能和限制。

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>更改 SKU

可以通过 Azure CLI 或在 Azure 门户中更改注册表的 SKU。 你可以自由地在各种托管的 SKU 之间切换，只要你要切换到的 SKU 具有所需的最大存储容量即可。 如果从经典 SKU 切换到托管的 SKU 之一，则无法切换回经典 SKU，因为这是一个单向转换。

### <a name="azure-cli"></a>Azure CLI

若要使用 Azure CLI 在各种 SKU 之间切换，请使用 [az acr update][az-acr-update] 命令。 例如，若要切换到高级 SKU，请使用以下命令：

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure 门户

在 Azure 门户中的容器注册表“概述”中，选择“更新”，然后从“SKU”下拉列表中选择一个新 SKU。

![在 Azure 门户中更新容器注册表 SKU][update-registry-sku]

如果你有经典注册表，则无法在 Azure 门户中选择托管的 SKU。 而是必须先[升级][container-registry-upgrade]到托管的注册表（请参阅[转换经典注册表](#changing-from-classic)）。

## <a name="changing-from-classic"></a>转换经典注册表

将非托管的经典注册表迁移到托管的基本、标准或高级 SKU 之一时，需要考虑一些其他注意事项。 如果你的经典注册表包含大量映像，并且其大小为许多 GB，则迁移过程可能要花费一些时间。 此外，在迁移完成之前，`docker push` 操作会被禁用。

有关将经典注册表升级到托管的 SKU 之一的详细信息，请参阅[升级经典容器注册表][container-registry-upgrade]。

## <a name="pricing"></a>定价

有关每个 Azure 容器注册表 SKU 的定价信息，请参阅[容器注册表定价][container-registry-pricing]。

## <a name="next-steps"></a>后续步骤

**Azure 容器注册表路线图**

访问 GitHub 上的 [ACR 路线图][acr-roadmap]，了解服务即将推出的新功能。

**Azure 容器注册表 UserVoice**

在 [ACR UserVoice][container-registry-uservoice] 中提交新功能建议，以及对新功能建议进行投票。

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-storage]: container-registry-storage.md
[container-registry-webhook]: container-registry-webhook.md
