---
title: 注册表服务层级和功能
description: 了解 Azure 容器注册表的“基本”、“标准”和“高级”服务层级 (SKU) 中的功能和限制。
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: e2a5ad52775e9000aa0beb0a926d809da1c5a0e0
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048468"
---
# <a name="azure-container-registry-service-tiers"></a>Azure 容器注册表服务层级

Azure 容器注册表分为多个服务层级（也称为 SKU）。 这些层级提供可预测的定价和多个选项，用来适应你在 Azure 中的专用 Docker 注册表的容量和使用模式。

| 层 | 说明 |
| --- | ----------- |
| **基本** | 供开发者了解 Azure 容器注册表的入口点（已优化过成本）。 基本注册表的编程功能（如 Azure Active Directory [身份验证集成](container-registry-authentication.md#individual-login-with-azure-ad)、[映像删除][container-registry-delete]和 [Webhook][container-registry-webhook]）与标准注册表和高级注册表相同。 但其附带的存储和映像吞吐量最适合使用较少的场景。 |
| **Standard** | 标准注册表的功能与基本注册表相同。不同之处在于，前者附带更多的存储和映像吞吐量。 标准注册表应能够满足大部分生产方案的需求。 |
| **高级** | 高级注册表附带的存储和并发操作数最多，支持大容量方案。 除增加了映像吞吐容量之外，高级注册表还增添了其他功能，例如用于跨多个区域管理一个注册表的[异地复制][container-registry-geo-replication]、用于映像标记签名的[内容信任](container-registry-content-trust.md)、用于限制注册表访问的[具有专用终结点的专用链接](container-registry-private-link.md)。 |

“基本”、“标准”和“高级”层级全都提供相同的编程功能。 它们还全都受益于完全由 Azure 托管的[映像存储][container-registry-storage]。 选择的层级等级越高，性能和可缩放性就越高。 使用多个服务层级，你可以从“基本”层级开始，然后随着注册表使用量增长转换到“标准”和“高级”层级。

## <a name="service-tier-features-and-limits"></a>服务层级功能和限制

下表详细介绍了基本、标准和高级服务层的功能和注册表限制。

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-tiers"></a>更改层级

可以通过 Azure CLI 或在 Azure 门户中更改注册表的服务层级。 你可以自由地在各种层级之间切换，只要你要切换到的层级具有所需的最大存储容量即可。 

### <a name="azure-cli"></a>Azure CLI

若要使用 Azure CLI 在各种服务层级之间切换，请使用 [az acr update][az-acr-update] 命令。 例如，若要切换到高级 SKU，请使用以下命令：

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure 门户

在 Azure 门户中的容器注册表“概述”中，选择“更新”，然后从“SKU”下拉列表中选择一个新 SKU。

![在 Azure 门户中更新容器注册表 SKU][update-registry-sku]

## <a name="pricing"></a>定价

有关每个 Azure 容器注册表服务层级的定价信息，请参阅[容器注册表定价][container-registry-pricing]。

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