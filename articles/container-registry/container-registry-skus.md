---
title: "Azure 容器注册表 SKU"
description: "比较 Azure 容器注册表的不同服务层"
services: container-registry
author: stevelas
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 10/16/2017
ms.author: stevelas
ms.openlocfilehash: cf7724dd9e3e870cfd7ec0b5d2e4ea1d0694e9de
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2017
---
# <a name="azure-container-registry-skus"></a>Azure 容器注册表 SKU

Azure 容器注册表 (ACR) 分为多个服务层（称为“SKU”）。 这些 SKU 提供可预测的定价和多个选项，用于指定如何在 Azure 中使用专用 Docker 注册表。 选择的 SKU 级别越高，性能和可缩放性就越高。 不过，所有 SKU 提供的编程功能都相同，以便于开发者可以从基本注册表入手，然后随着注册表使用情况增多改用标准注册表和高级注册表。

## <a name="basic"></a>基本
供开发者了解 Azure 容器注册表的入口点（已优化过成本）。 基本注册表的编程功能（Azure Active Directory 身份验证集成、映像删除和 Webhook）与标准注册表和高级注册表相同。不同之处在于大小和使用情况约束。

## <a name="standard"></a>标准
标准注册表的功能与基本注册表相同。不同之处在于，前者增加了存储空间上限和映像吞吐量。 标准注册表应能够满足大部分生产方案的需求。

## <a name="premium"></a>高级
高级注册表对存储和并发操作等功能的约束限制更高，支持大容量方案。 除了增加映像吞吐量之外，高级注册表还增添了其他功能（如[异地复制](container-registry-geo-replication.md)，用于跨多个区域管理一个注册表，并在每个部署中维护网络封闭注册表）。

## <a name="classic"></a>经典
经典注册表 SKU 在 Azure 中启用了首版 Azure 容器注册表。 经典注册表由 Azure 在订阅中创建的存储帐户提供支持，这会限制 ACR 提供更高级功能，如增加吞吐量和异地复制。 由于功能有限，我们计划在未来弃用经典 SKU。

> [!NOTE]
> 由于我们已计划弃用经典注册表 SKU，因此建议对所有新注册表使用基本、标准或高级 SKU。 若要了解如何转换现有经典注册表，请参阅[更改 SKU](#changing-skus)。
>

## <a name="registry-sku-feature-matrix"></a>注册表 SKU 功能矩阵

下表详细介绍了基本、标准和高级服务层的功能和限制。

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="manage-registry-size"></a>管理注册表大小
每个 SKU 的存储约束旨在与典型方案保持一致，即基本 SKU 适用于入门，标准 SKU 适用于大部分生产应用程序，高级 SKU 适用于超大规模提升性能和[异地复制](container-registry-geo-replication.md)。 在注册表的整个生命周期中，应定期删除未使用的内容，管理注册表大小。

在 Azure 门户的容器注册表“概述”中，可以查看注册表的当前使用情况：

![Azure 门户中的注册表使用情况信息](media/container-registry-skus/registry-overview-quotas.png)

可以在 Azure 门户中删除存储库，管理注册表大小。

在“服务”下，选择“存储库”，右键单击要删除的存储库，再选择“删除”。

![在 Azure 门户中删除存储库](media/container-registry-skus/delete-repository-portal.png)

## <a name="changing-skus"></a>更改 SKU

可以在 Azure 门户中更改注册表 SKU。

在 Azure 门户的注册表“概述”中，选择“更新”，再从“SKU”下拉列表中选择新 SKU。

![在 Azure 门户中更新容器注册表 SKU](media/container-registry-skus/update-registry-sku.png)

## <a name="changing-from-classic"></a>转换经典注册表
将经典注册表转换为基本、标准或高级注册表时，Azure 将现有容器映像从订阅中的关联存储帐户复制到 Azure 托管的存储帐户中。 此过程可能需要一段时间才能完成。

在转换期间，`docker pull` 可以继续正常运行，而 `docker push` 则在转换完成前遭屏蔽。

完成后，ACR 便不再使用订阅存储帐户。

### <a name="why-change-from-classic-to-basic-standard-or-premium"></a>为什么要将经典注册表转换为基本、标准或高级注册表？

由于经典注册表的功能有限，因此建议将经典注册表转换为基本、标准或高级服务层。 这些更高级 SKU 将注册表更深入地集成到 Azure 功能中。 其中部分功能包括：

* 用于个人身份验证的 Azure Active Directory 集成（请参阅 [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login)）
* 映像和标记删除支持
* [异地复制](container-registry-geo-replication.md)
* [Webhook](container-registry-webhook.md)

最重要的是，经典注册表依赖 Azure 在用户创建注册表时在 Azure 订阅中自动预配的存储帐户。 相比之下，基本、标准和高级 SKU 利用的是托管存储。 也就是说，Azure 在透明的状态下为用户管理映像存储，即利用不是在自己的订阅中创建的单独存储帐户。

基本、标准和高级注册表利用管理存储带来了下面一些优势：

* 容器映像[静态加密](../storage/common/storage-service-encryption.md)。
* 映像使用[异地冗余存储](../storage/common/storage-redundancy.md#geo-redundant-storage)进行存储，以确保通过多区域复制备份映像。
* 可以[切换 SKU](#changing-skus)，在选择更高级 SKU 时支持更大吞吐量。 对于每个 SKU，ACR 都可以用户需求增加时满足吞吐量需求。 有关 ACR 如何达到相应吞吐量目标的基础实现表示成意向（通过选择更高级 SKU），用户无需管理实现的详细信息。

## <a name="pricing"></a>定价

有关每个 Azure 容器注册表 SKU 的定价信息，请参阅[容器注册表定价](https://azure.microsoft.com/pricing/details/container-registry/)。

## <a name="next-steps"></a>后续步骤

**Azure 容器注册表路线图**

请访问 GitHub 上的 [ACR 路线图](https://aka.ms/acr/roadmap)，了解服务即将推出的新功能。

**Azure 容器注册表 UserVoice**

请在 [ACR UserVoice](https://feedback.azure.com/forums/903958-azure-container-registry) 中提交新功能建议，并对这些功能建议投票。