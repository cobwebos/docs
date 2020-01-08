---
title: 注册最佳做法
description: 通过遵循这些最佳做法，了解如何有效使用 Azure 容器注册表。
ms.topic: article
ms.date: 09/27/2018
ms.openlocfilehash: 7efea468a6c5c042f709d8a5bb493516458ce52b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445790"
---
# <a name="best-practices-for-azure-container-registry"></a>Azure 容器注册表的最佳做法

通过遵循这些最佳做法，可帮助最大化性能并在 Azure 中经济、高效地利用私有 Docker 注册表。

另请参阅[用于标记和版本管理容器映像的建议](container-registry-image-tag-version.md)，了解如何在注册表中标记和版本映像。 

## <a name="network-close-deployment"></a>临近网络部署

在部署容器的 Azure 区域中创建容器注册表。 将注册表置于容器主机临近网络的区域中可帮助降低延迟和成本。

临近网络部署是使用私有容器注册表的主要原因之一。 Docker 映像具有有效的[分层构造](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/)，可实现增量部署。 但是，新节点需要拉取给定映像所需的全部构造层。 此初始 `docker pull` 可以快速增加多个千兆字节。 将私有注册表置于临近部署的位置可最小化网络延迟。
此外，所有公有云（包括 Azure）都实施了网络出口费用。 除了延迟之外，将映像从一个数据中心拉取到另一个数据中心还会增加网络出口费用。

## <a name="geo-replicate-multi-region-deployments"></a>异地复制多区域部署

如果将容器部署到多个区域，请使用 Azure 容器注册表的[异地复制](container-registry-geo-replication.md)功能。 无论是为本地数据中心的全局客户提供服务还是开发团队处于不同位置，都可以通过异地复制注册表来简化注册表管理并最小化延迟。 异地复制仅适用于[高级](container-registry-skus.md)注册表。

若要了解如何使用异地复制，请参阅 [Azure 容器注册表中的异地复制](container-registry-tutorial-prepare-registry.md)教程，该教程分为三部分。

## <a name="repository-namespaces"></a>存储库命名空间

通过利用存储库命名空间，可以在组织中的多个组之间共享单个注册表。 可在部署和团队之间共享注册表。 Azure 容器注册表支持嵌套的命名空间，可实现组隔离。

例如，考虑以下容器映像标记。 企业范围内使用的图像（如 `aspnetcore`）位于根命名空间中，而产品和市场营销组拥有的容器映像均使用自己的命名空间。

```
contoso.azurecr.io/aspnetcore:2.0
contoso.azurecr.io/products/widget/web:1
contoso.azurecr.io/products/bettermousetrap/refundapi:12.3
contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42
```

## <a name="dedicated-resource-group"></a>专用资源组

由于容器注册表是跨多个容器主机使用的资源，注册表应位于其自己的资源组中。

虽然可以试用特定的主机类型（如 Azure 容器实例），但完成操作后可能会删除容器实例。 但是，你可能还想保留推送到 Azure 容器注册表的映像集合。 通过将注册表置于其自己的资源组中，可以最小化删除容器实例资源组时在注册表中意外删除映像集合的风险。

## <a name="authentication"></a>身份验证

Azure 容器注册表的身份验证有两种主要方案：单个身份验证和服务（或“无外设”）身份验证。 下表提供了这两个方案的简要概述，以及每个方案的推荐身份验证方法。

| 类型 | 示例方案 | 推荐的方法 |
|---|---|---|
| 单个标识 | 开发者从/向其开发计算机推送映像。 | [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) |
| 无外设/服务标识 | 用户未直接参与的生成和部署管道。 | [服务主体](container-registry-authentication.md#service-principal) |

有关 Azure 容器注册表身份验证的详细信息，请参阅 [Azure 容器注册表的身份验证](container-registry-authentication.md)。

## <a name="manage-registry-size"></a>管理注册表大小

每个[容器注册表 SKU][container-registry-skus]的存储约束旨在与典型方案保持一致：**基本**为入门，适用于大多数生产应用程序的**标准**，以及超大规模性能和[异地复制][container-registry-geo-replication]的**高级**。 在注册表的整个生命周期中，应定期删除未使用的内容，管理注册表大小。

使用 Azure CLI 命令[az acr show][az-acr-show-usage] ：显示注册表的当前大小：

```console
$ az acr show-usage --resource-group myResourceGroup --name myregistry --output table
NAME      LIMIT         CURRENT VALUE    UNIT
--------  ------------  ---------------  ------
Size      536870912000  185444288        Bytes
Webhooks  100                            Count
```

此外，在 Azure 门户的注册表“概述”中，还可以找到当前已用存储：

![Azure 门户中的注册表使用情况信息][registry-overview-quotas]

### <a name="delete-image-data"></a>删除映像数据

Azure 容器注册表支持多种从容器注册表中删除映像数据的方法。 可以按标记或程序清单摘要删除映像，也可以删除整个存储库。

有关从注册表中删除映像数据（包括无标记映像，有时称为“无关联”映像或“孤立”映像）的详细信息，请参阅[删除 Azure 容器注册表中的容器映像](container-registry-delete.md)。

## <a name="next-steps"></a>后续步骤

Azure 容器注册表可用于多层（称为 SKU），每层提供不同功能。 有关可用 SKU 的详细信息，请参阅 [Azure 容器注册表 SKU](container-registry-skus.md)。

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-show-usage]: /cli/azure/acr#az-acr-show-usage
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md
