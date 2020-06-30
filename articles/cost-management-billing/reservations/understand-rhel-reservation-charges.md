---
title: Red Hat 预留计划折扣 - Azure
description: 了解如何将 Red Hat 计划折扣应用于虚拟机上的 Red Hat 软件。
author: yashesvi
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: banders
ms.openlocfilehash: 46a1fd9be35abb19d920e2a3bd34f88c557f40b1
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/12/2020
ms.locfileid: "84735109"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>了解如何将 Red Hat Linux Enterprise 软件预留计划折扣应用于 Azure

购买 Red Hat Linux 计划后，折扣将自动应用于与预留匹配的已部署 Red Hat 虚拟机 (VM)。 Red Hat Linux 计划涵盖了在 Azure VM 上运行 Red Hat 软件的成本。

若要购买正确的 Red Hat Linux 计划，需要了解所运行的 Red Hat VM 以及这些 VM 上的 vCPU 数量。 使用下列各节来帮助通过使用情况 CSV 文件来识别要购买的计划。

## <a name="discount-applies-to-different-vm-sizes"></a>折扣适用于不同的 VM 大小

与预留的 VM 实例一样，Red Hat 计划购买提供了实例大小灵活性。 这意味着即使在部署 vCPU 计数不同的 VM 时，折扣也适用。 折扣适用于软件计划内不同的 VM 大小。

折扣金额取决于下表中列出的比率。 比率将比较该组中每个 VM 大小的相对占用情况。 比率取决于 VM vCPU。 使用比率值计算可获得 Red Hat Linux 计划折扣的 VM 数量。

例如，如果为具有 1 至 4 个 vCPU 的 VM 购买 Red Hat Linux Enterprise Server 计划，则该预留的比率为 1。 折扣涵盖了以下项的 Red Hat 软件成本：

- 1 台具有 1 至 4 个 vCPU 的已部署 VM，
- 或具有 5 个或更多 vCPU 的一台 VM，只需 Red Hat Enterprise Linux 成本的 0.46 (46%)。

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Azure 门户商城名称：
- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 8.2

[检查应用计划的 Red Hat Enterprise Linux 计量](https://isfratio.blob.core.windows.net/isfratio/RHELRatios.csv)

## <a name="next-steps"></a>后续步骤

若要了解有关预留的详细信息，请参阅以下文章：

- [什么是适用于 Azure 的预留](save-compute-costs-reservations.md)
- [通过 Azure 预留为 Red Hat 软件计划预付费](../../virtual-machines/linux/prepay-rhel-software-charges.md)
- [通过 Azure 虚拟机预留实例为虚拟机预付费](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [管理 Azure 预留](manage-reserved-vm-instance.md)
- [了解即用即付订阅的预留使用情况](understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
