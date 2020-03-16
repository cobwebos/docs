---
title: Azure 上的 Red Hat 工作负荷概述 | Microsoft Docs
description: 了解 Azure 上提供的 Red Hat 产品。
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 718447e1dbf597af4349eab0be78a2bb544dec90
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970175"
---
# <a name="red-hat-workloads-on-azure"></a>Azure 上的 Red Hat 工作负荷

通过 Azure 上的各种产品/服务支持 Red Hat 工作负荷。 Red Hat Enterprise Linux (RHEL) 映像是 RHEL 工作负荷的核心，如 Red Hat 更新基础结构 (RHUI) 一样。

## <a name="red-hat-enterprise-linux-images"></a>Red Hat Enterprise Linux 映像

Azure 在 Azure 上提供了大量的 RHEL 映像。 这些映像通过两种不同的许可模式提供：即用即付和自带订阅 (BYOS)。 当新的 RHEL 版本在其生命周期中根据需要发布并更新时，将发布 Azure 上的新 RHEL 映像。

### <a name="pay-as-you-go-images"></a>即用即付映像

Azure 提供了各种 RHEL 即用即付映像。 这些映像适用于 RHEL，并附加到更新源（Red Hat 更新基础结构）。 这些映像将针对 RHEL 权限和更新收取高级费用。 RHEL 即用即付映像变种包括：

* 标准 RHEL。
* RHEL for SAP。
* 具有高可用性和更新服务的 RHEL for SAP。

如果不想为相应数量的订阅单独付费，则可能需要使用即用即付映像。

### <a name="red-hat-gold-images"></a>Red Hat 黄金映像

Azure 还提供 Red Hat 黄金映像 (`rhel-byos`)。 对于有现有的 Red Hat 订阅并想要在 Azure 中使用这些订阅的客户，这些映像可能会很有用。 必须先为现有 Red Hat 订阅启用 Red Hat 云访问，然后才能在 Azure 中使用它们。 当 Red Hat 订阅启用了云访问并且满足资格要求时，将自动授予对这些映像的访问权限。 使用这些映像，客户可以避免由于使用即用即付映像而导致的双重计费。
* 了解如何[使用 Azure 为 Red Hat 订阅启用云访问](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs)。
* 了解如何[在 Azure 门户、Azure CLI 或 PowerShell cmdlet 中查找 Red Hat 黄金映像](./byos.md)。

> [!NOTE]
> 用户为 RHEL 订阅付款两次时，即产生双重计费。 当客户使用 Red Hat 订阅管理器在 RHEL 即用即付 VM 上附加授权时，通常会发生这种情况。 例如，使用订阅管理器在 RHEL 即用即付映像上为 SAP 程序包附加权利的客户将被间接收取双倍费用，因为他们为 RHEL 支付两次费用。 他们通过即用即付高级费用支付一次，通过其 SAP 订阅支付一次。 BYOS 映像用户不会发生这种情况。

### <a name="generation-2-images"></a>第 2 代映像

与第 1 代虚拟机 (VM) 相比，第 2 代 VM 提供了许多较新的功能。 有关详细信息，请参阅[第 2 代文档](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2)。 从 RHEL 映像角度来看，主要区别在于第 2 代 VM 使用 UEFI 而非 BIOS 固件接口。 在启动时，它们还使用 GUID 分区表 (GPT) 而非主启动记录 (MBR)。 使用 GPT 将允许 OS 磁盘大小超过 2 TB，并允许更多事项。 此外，[Mv2 系列 VM](../../mv2-series.md) 仅在第 2 代映像上运行。

Azure 市场中提供了 RHEL 第 2 代映像。 在使用 Azure CLI 时显示的所有映像的列表中，在映像 SKU 中查找“gen2”。 在 VM 部署过程中，可以转到“高级”  选项卡来部署第 2 代 VM。

## <a name="red-hat-update-infrastructure"></a>Red Hat 更新基础结构

Azure 仅为即用即付 RHEL VM 提供了 Red Hat 更新基础结构。 RHUI 实际上是 Red Hat CDN 的镜像，但只能由 Azure 即用即付 RHEL VM 访问。 你有权访问相应的包，具体取决于已部署的 RHEL 映像。 例如，除了基础 RHEL 包之外，RHEL for SAP 映像还可以访问 SAP 包。

### <a name="rhui-update-behavior"></a>RHUI 更新行为

默认情况下，当 `yum update` 运行时，连接到 RHUI 的 RHEL 映像将更新为 RHEL 的最新次版本。 此行为意味着在 RHEL 7.4 VM 上运行 `yum update` 操作时，它可能会升级到 RHEL 7.7。 此行为是 RHUI 的设计使然。 若要缓解此升级行为，可以从常规 RHEL 存储库切换到[扩展更新支持存储库](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms)。

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure 上的 RHEL 映像](./redhat-images.md)。
* 详细了解 [Red Hat 更新基础结构](./redhat-rhui.md)。
* 详细了解 [Red Hat 黄金映像 (`rhel-byos`) 产品](./byos.md)。
