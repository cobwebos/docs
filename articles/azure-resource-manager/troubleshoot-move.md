---
title: 移动错误疑难解答
description: 使用 Azure 资源管理器将资源移到新的资源组或订阅。
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 46fedeb8b50b19a0adc34b2099e7e76b888821f6
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149108"
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

如果收到一条错误消息，指出由于资源未处于成功状态而无法移动资源，则它可能实际上是阻止移动的依赖资源。 通常，错误代码为 **MoveCannotProceedWithResourcesNotInSucceededState**。

如果源或目标资源组包含虚拟网络，则会在移动过程中检查虚拟网络的所有依赖资源的状态。 该检查包括直接和间接依赖于虚拟网络的这些资源。 如果这些资源中有任何资源处于故障状态，则会阻止移动。 例如，如果某个使用虚拟网络的虚拟机故障，则会阻止移动。 即使该虚拟机不是要移动的资源之一，也不在要移动的资源组之一中，系统也会阻止移动。

收到此错误时，你有两个选择。 将资源移到没有虚拟网络的资源组，或[联系支持人员](../azure-supportability/how-to-create-azure-support-request.md)。

## <a name="next-steps"></a>后续步骤

有关移动资源的命令，请参阅[将资源移动至新资源组或订阅](resource-group-move-resources.md)。
