---
title: 对错误进行故障排除时将 Azure 资源移到新订阅或资源组
description: 使用 Azure 资源管理器将资源移到新的资源组或订阅。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: e23d7c571a010e5bfb42e5f15368e0194272ed53
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723462"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>排查移动到新的资源组或订阅的 Azure 资源

本文提供了建议，可帮助移动资源时解决问题。

## <a name="upgrade-a-subscription"></a>升级订阅

如果确实想要升级 Azure 订阅（例如从免费切换到即用即付），则需要转换订阅。

* 若要升级免费试用版，请参阅[将免费试用版或 Microsoft Imagine Azure 订阅升级到即用即付](../billing/billing-upgrade-azure-subscription.md)。
* 若要更改即用即付帐户，请参阅[将 Azure 即用即付订阅更改为其他套餐](../billing/billing-how-to-switch-azure-offer.md)。

如果无法转换订阅，请[创建 Azure 支持请求](../azure-supportability/how-to-create-azure-support-request.md)。 选择“订阅管理”  作为问题类型。

## <a name="service-limitations"></a>服务限制

移动资源时，某些服务需要的其他注意事项。 如果你正在迁移以下服务，请确保检查的指南和限制。

* [应用服务](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [经典部署模型](./move-limitations/classic-model-move-limitations.md)
* [恢复服务](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [虚拟机](./move-limitations/virtual-machines-move-limitations.md)
* [虚拟网络](./move-limitations/virtual-network-move-limitations.md)

## <a name="large-requests"></a>大型请求

在可能的情况下，将大型移动分为单独的移动操作。 在一次操作中有 800 多项资源时，资源管理器会立即返回错误。 但是，移动 800 项以下的资源也可能因超时而失败。

## <a name="next-steps"></a>后续步骤

有关移动资源的命令，请参阅[将资源移动至新资源组或订阅](resource-group-move-resources.md)。
