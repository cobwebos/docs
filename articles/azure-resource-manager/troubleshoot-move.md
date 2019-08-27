---
title: 排查将 Azure 资源移到新订阅或资源组时出现的错误
description: 使用 Azure 资源管理器将资源移到新的资源组或订阅。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: b688218b871a5f652e7f4de172d23f1b1fb0aa5c
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035522"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>排查将 Azure 资源移到新的资源组或订阅时遇到的问题

本文提供了一些建议来帮助解决在移动资源时遇到的问题。

## <a name="upgrade-a-subscription"></a>升级订阅

如果确实想要升级 Azure 订阅（例如从免费切换到即用即付），则需要转换订阅。

* 若要升级免费试用版，请参阅[将免费试用版或 Microsoft Imagine Azure 订阅升级到即用即付](../billing/billing-upgrade-azure-subscription.md)。
* 若要更改即用即付帐户，请参阅[将 Azure 即用即付订阅更改为其他套餐](../billing/billing-how-to-switch-azure-offer.md)。

如果无法转换订阅，请[创建 Azure 支持请求](../azure-supportability/how-to-create-azure-support-request.md)。 选择“订阅管理”作为问题类型。

## <a name="service-limitations"></a>服务限制

有些服务在移动资源时需要注意额外的事项。 如果要移动以下服务，请务必查看指南和限制。

* [应用服务](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [经典部署模型](./move-limitations/classic-model-move-limitations.md)
* [网络](./move-limitations/networking-move-limitations.md)
* [恢复服务](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [虚拟机](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>大型请求

在可能的情况下，将大型移动分为单独的移动操作。 在一次操作中有 800 多项资源时，资源管理器会立即返回错误。 但是，移动 800 项以下的资源也可能因超时而失败。

## <a name="resource-not-in-succeeded-state"></a>资源未处于成功状态

如果收到一条错误消息, 指出由于某个资源未处于 "成功" 状态而无法移动, 则它实际上可能是阻止移动的依赖资源。

如果源或目标资源组包含虚拟网络, 则在移动过程中将检查虚拟网络的所有从属资源的状态。 如果其中的任何资源处于失败状态, 则会阻止移动。 例如, 如果使用虚拟网络的虚拟机出现故障, 则会阻止移动。 即使虚拟机不是要移动的资源之一, 而且不在移动的资源组中, 也会阻止移动。 若要避免此问题, 请将资源移到没有虚拟网络的资源组。

## <a name="next-steps"></a>后续步骤

有关移动资源的命令，请参阅[将资源移动至新资源组或订阅](resource-group-move-resources.md)。
