---
title: Azure 上的 Red Hat 工作负荷概述 | Microsoft Docs
description: 了解 Azure 上提供的 Red Hat 产品
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: 8ca249a5f6c300a39548e4e16927d7a20acae1a8
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942615"
---
# <a name="red-hat-workloads-on-azure"></a>Azure 上的 Red Hat 工作负荷
通过 Azure 上的各种产品/服务支持 Red Hat 工作负荷。 Red Hat Enterprise Linux (RHEL) 映像是 RHEL 工作负荷的核心，如 Red Hat 更新基础结构 (RHUI) 一样。

## <a name="red-hat-enterprise-linux-rhel-images"></a>Red Hat Enterprise Linux (RHEL) 映像
Azure 在 Azure 上提供了大量的 RHEL 映像。 这些映像通过两种不同的许可模式提供：即用即付 (PAYG) 和自带订阅 (BYOS)。 当新的 RHEL 版本在其生命周期中根据需要发布并更新时，将发布 Azure 上的新 RHEL 映像。

### <a name="pay-as-you-go-payg-images"></a>即用即付 (PAYG) 映像
Azure 提供各种 RHEL PAYG 映像。 这些映像适用于 RHEL，并附加到更新源（Red Hat 更新基础结构）。 这些图像将收取 RHEL 权限和更新的高级费用。 RHEL PAYG 映像变种包括：
* 标准 RHEL
* RHEL for SAP
* 具有高可用性和更新服务的 RHEL for SAP。

如果不想为相应数量的订阅单独付费，则可能需要使用 PAYG 映像。

### <a name="red-hat-gold-images-rhel-byos"></a>Red Hat 黄金映像 (`rhel-byos`)
Azure 还提供 Red Hat 黄金映像。 对于有现有的 Red Hat 订阅并想要在 Azure 中使用这些订阅的客户，这些映像可能会很有用。 必须先为现有 Red Hat 订阅启用 Red Hat 云访问，然后才能在 Azure 中使用它们。 当 Red Hat 订阅启用了云访问并且满足资格要求时，将自动授予对这些映像的访问权限。 使用这些映像，客户可以避免由于使用 PAYG 映像而导致的双重计费。
* [了解如何使用 Azure 为 Red Hat 订阅启用云访问](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs)
* [了解如何在 Azure 门户、CLI 或 PowerShell Cmdlet 中查找 Red Hat 黄金映像](./byos.md)

> [!NOTE]
> 双重计费说明：用户为 RHEL 订阅付款两次时，即产生双重计费。 当客户使用订阅管理器在 RHEL PAYG VM 上附加授权时，通常会发生这种情况。 例如，使用订阅管理器将 SAP 包的授权附加到 RHEL PAYG 映像上的客户会被间接双重计费，因为他们将为 RHEL 付款两次，一次是 PAYG 高级费用，一次是 SAP 订阅费用。 这并不是 BYOS 映像用户的情况。

## <a name="red-hat-update-infrastructure-rhui"></a>Red Hat 更新基础结构 (RHUI)
Azure 仅提供适用于 PAYG RHEL 虚拟机 (VM) 的 Red Hat 更新基础结构。 RHUI 实际上是 Red Hat CDN 的镜像，但只能由 Azure PAYG RHEL VM 访问。 你将有权访问相应的包，具体取决于已部署的 RHEL 映像。 例如，RHEL for SAP 映像除了基础 RHEL 包外，还可以访问 SAP 包。

### <a name="rhui-update-behavior"></a>RHUI 更新行为
默认情况下，连接到 RHUI 的 RHEL 映像会在 `yum update` 运行时更新到 RHEL 的最新次要版本。 此行为意味着 RHEL 7.4 VM 在运行 `yum update` 操作时，可能会升级到 RHEL 7.7。 这是 RHUI 的设计结果。 但是，可以通过从常规 RHEL 存储库切换到[扩展更新支持 (EUS) 存储库](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms)来缓解这种升级行为。

## <a name="next-steps"></a>后续步骤
* 详细了解 [Azure 上的 RHEL 映像](./redhat-images.md)
* 详细了解 [Red Hat 更新基础结构](./redhat-rhui.md)
* 详细了解 [Red Hat 黄金映像 (`rhel-byos`) 产品](./byos.md)
