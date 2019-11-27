---
title: 服务层和 Sku
description: 了解 Azure 容器注册表的基本、标准和高级服务层（Sku）中的功能和限制。
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 1ebe5339b7523a4463dee45b126244d7ec5b2e4b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456266"
---
# <a name="azure-container-registry-skus"></a>Azure 容器注册表 SKU

Azure 容器注册表 (ACR) 分为多个服务层级（称为“SKU”）。 这些 SKU 提供可预测的定价和多个选项，用来适应你在 Azure 中的专用 Docker 注册表的容量和使用模式。

| SKU | 说明 |
| --- | ----------- |
| **基本** | 供开发者了解 Azure 容器注册表的入口点（已优化过成本）。 基本注册表的编程功能（例如 Azure Active Directory [身份验证集成](container-registry-authentication.md#individual-login-with-azure-ad)、[映像删除][container-registry-delete]和 [Webhook][container-registry-webhook]）与标准注册表和高级注册表相同。 但其附带的存储和映像吞吐量最适合使用较少的场景。 |
| **标准** | 标准注册表的功能与基本注册表相同。不同之处在于，前者附带更多的存储和映像吞吐量。 标准注册表应能够满足大部分生产方案的需求。 |
| **高级** | 高级注册表附带的存储和并发操作数最多，支持大容量方案。 除了更高的映像吞吐量之外，高级版还添加了[异地复制][container-registry-geo-replication]功能，用于跨多个区域管理单个注册表，为映像标记签名、[防火墙和虚拟网络（预览版）](container-registry-vnet.md)提供[内容信任](container-registry-content-trust.md)限制对注册表的访问。 |

基本、标准和高级 Sku 均提供相同的编程能力。 它们也全都从完全由 Azure 托管的[映像存储][container-registry-storage]中受益。 选择的 SKU 级别越高，性能和可缩放性就越高。 使用多个服务层级，你可以从“基本”层级开始，然后随着注册表使用量增长转换到“标准”和“高级”层级。

## <a name="sku-features-and-limits"></a>SKU 功能和限制

下表详细介绍了“基本”、“标准”和“高级”服务层级的功能和限制。

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>更改 SKU

可以通过 Azure CLI 或在 Azure 门户中更改注册表的 SKU。 你可以在 Sku 之间自由移动，只要你要切换到的 SKU 具有所需的最大存储容量。 

### <a name="azure-cli"></a>Azure CLI

若要使用 Azure CLI 在各种 SKU 之间切换，请使用 [az acr update][az-acr-update] 命令。 例如，若要切换到高级 SKU，请使用以下命令：

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure 门户

在 Azure 门户中的容器注册表“概述”中，选择“更新”，然后从“SKU”下拉列表中选择一个新 SKU。

![在 Azure 门户中更新容器注册表 SKU][update-registry-sku]

## <a name="pricing"></a>定价

有关每个 Azure 容器注册表 SKU 的定价信息，请参阅[容器注册表定价][container-registry-pricing]。

有关数据传输定价的详细信息，请参阅[带宽定价详细信息](https://azure.microsoft.com/pricing/details/bandwidth/)。 

## <a name="next-steps"></a>后续步骤

**Azure 容器注册表路线图**

请访问 GitHub 上的 [ACR 路线图][acr-roadmap]，了解服务即将推出的新功能。

**Azure 容器注册表 UserVoice**

请在 [ACR UserVoice][container-registry-uservoice] 中提交新功能建议，并对这些功能建议投票。

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
