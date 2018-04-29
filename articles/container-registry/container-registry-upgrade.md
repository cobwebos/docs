---
title: 升级经典 Azure 容器注册表
description: 通过升级非托管的经典容器注册表，利用基本、标准和高级托管容器注册表的扩展功能集。
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 03/15/2018
ms.author: marsma
ms.openlocfilehash: 084dfc8f87aaea4b5bbad7cb5fdb9d445d566206
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="upgrade-a-classic-container-registry"></a>升级经典容器注册表

Azure 容器注册表 (ACR) 分为多个服务层（[称为“SKU”](container-registry-skus.md)）。 ACR 的初始版本提供的单一 SKU（即经典 SKU）缺乏基本 SKU、标准 SKU 和高级 SKU（统称托管注册表）固有的多项功能。 本文详述了如何将非托管的经典注册表迁移到某个托管的 SKU，以便利用其增强的功能集。

## <a name="why-upgrade"></a>为什么升级？

由于经典的非托管注册表的功能有限，因此建议将所有经典注册表升级为基本、标准或高级托管注册表。 这些更高级 SKU 将注册表更深入地集成到 Azure 功能中。

托管的注册表提供：

* 用于[单次登录](container-registry-authentication.md#individual-login-with-azure-ad)的 Azure Active Directory 集成
* 映像和标记删除支持
* [异地复制](container-registry-geo-replication.md)
* [Webhook](container-registry-webhook.md)

最重要的是，经典注册表依赖 Azure 在用户创建注册表时在 Azure 订阅中自动预配的存储帐户。 相比之下，基本、标准和高级 SKU 通过以透明方式处理映像的存储来利用 Azure 的[高级存储功能](container-registry-storage.md)。 不在自己的订阅中创建单独的存储帐户。

托管注册表存储有以下优点：

* 容器映像[静态加密](container-registry-storage.md#encryption-at-rest)。
* 映像使用[异地冗余存储](container-registry-storage.md#geo-redundant-storage)进行存储，以确保通过多区域复制备份映像。
* 可以自由地[在 SKU 之间切换](container-registry-skus.md#changing-skus)，在选择更高级 SKU 时支持更大吞吐量。 对于每个 SKU，ACR 都可以用户需求增加时满足吞吐量需求。
* 注册表及其存储具有统一的安全模型，可以简化权限管理。 只需为容器注册表管理权限，不需为单独的存储帐户管理权限。

有关 ACR 中映像存储的其他详细信息，请参阅 [Azure 容器注册表中的容器映像存储](container-registry-storage.md)。

## <a name="migration-considerations"></a>迁移注意事项

将经典注册表更改为托管注册表时，Azure 必须将所有现有的容器映像从订阅中 ACR 创建的存储帐户复制到 Azure 托管的存储帐户中。 此过程可能需要数分钟到数小时，具体取决于注册表的大小。

在转换过程中，会阻止所有 `docker push` 操作，而 `docker pull` 则可继续运行。

在转换过程中，请勿删除或修改充当经典注册表后备的存储帐户的内容， 否则可能导致容器映像损坏。

迁移完成以后，ACR 不再使用订阅中最初充当经典注册表后备的存储帐户。 验证迁移成功以后，考虑删除存储帐户，尽量降低成本。

>[!IMPORTANT]
> 从经典注册表升级到某个托管的 SKU 属于**单向过程**。 将经典注册表转换为基本、标准或高级注册表以后，不能还原为经典注册表。 但是，可以在不同的托管 SKU 之间自由转换，只要有足够的容量用于注册表。

## <a name="how-to-upgrade"></a>如何升级

可以通过多种方式将非托管的经典注册表升级为某个托管的 SKU。 以下部分介绍 [Azure CLI][azure-cli] 和 [Azure 门户][azure-portal]的使用过程。

## <a name="upgrade-in-azure-cli"></a>在 Azure CLI 中升级

若要在 Azure CLI 中升级经典注册表，请执行 [az acr update][az-acr-update] 命令，并为注册表指定新的 SKU。 在以下示例中，名为 *myclassicregistry* 的经典注册表将升级为高级 SKU：

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

迁移完成后，会看到如下所示的输出。 请注意，`sku` 为“高级”，`storageAccount` 为“null”，表明 Azure 现在为此注册表管理映像存储。

```JSON
{
  "adminUserEnabled": false,
  "creationDate": "2017-12-12T21:23:29.300547+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "name": "Premium",
    "tier": "Premium"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

如果指定的托管注册表 SKU 的最大容量小于经典注册表的大小，则会收到如下所示的错误消息。

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

如果收到类似的错误，请再次运行 [az acr update][az-acr-update] 命令并指定建议的 SKU，即可以容纳映像的下一最高级别的 SKU。

## <a name="upgrade-in-azure-portal"></a>在 Azure 门户中升级

使用 Azure 门户升级经典注册表时，Azure 自动选择能够容量映像的最低级别的 SKU。 例如，如果注册表包含 12 GiB 的映像，Azure 会自动选择经典注册表并将其转换为标准注册表（最大容量为 100 GiB）。

若要使用 Azure 门户升级经典注册表，请导航到容器注册表的“概览”页，然后选择“升级到托管的注册表”。

![Azure 门户 UI 中的经典注册表升级按钮][update-classic-01-upgrade]

选择“确定”，确认要升级到托管的注册表。

![Azure 门户 UI 中的经典注册表升级确认][update-classic-02-confirm]

在迁移过程中，门户会指示注册表的**预配状态**为“正在更新”。 如前所述，`docker push` 操作在迁移期间是禁用的。在迁移正在进行时，不得删除或更新经典注册表使用的存储帐户，否则可能导致映像损坏。

![Azure 门户 UI 中的经典注册表升级进度][update-classic-03-updating]

迁移完成后，**预配状态**指示“成功”，此时即可再次 `docker push` 到注册表。

![Azure 门户 UI 中的经典注册表升级完成状态][update-classic-04-updated]

## <a name="next-steps"></a>后续步骤

将经典注册表升级为基本、标准或高级注册表以后，Azure 不再使用一开始充当经典注册表后备的存储帐户。 为了降低成本，可以考虑删除存储帐户或帐户中的 Blob 容器（其中包含旧的容器映像）。

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade\update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade\update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade\update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade\update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com