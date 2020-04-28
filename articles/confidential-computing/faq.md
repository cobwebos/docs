---
title: Azure 机密计算常见问题解答
description: 有关 Azure 机密计算的常见问题的解答。
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: workloads
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: e9cb691ef60f612672078a9ef84db904c79cbc87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82189439"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Azure 机密计算常见问题

本文提供有关[在 Azure 上运行机密计算工作负荷](overview.md)的一些最常见问题的解答。

如果本文未解决 Azure 问题，请访问 [MSDN 和 Stack Overflow](https://azure.microsoft.com/support/forums/) 上的 Azure 论坛。 你可以在这些论坛中发布问题，或在[ @AzureSupport Twitter 上](https://twitter.com/AzureSupport)发布到。 你还可以提交 Azure 支持请求。 若要提交支持请求，请在[Azure 支持页](https://azure.microsoft.com/support/options/)上，选择 "获取支持"。

## <a name="confidential-computing-virtual-machines"></a>机密计算虚拟机<a id="vm-faq"></a>

1. **如何开始部署 DCsv2 系列 Vm？**

   可以通过以下方式部署 DCsv2 VM：
   - 使用[Azure 资源管理器模板](../virtual-machines/windows/template-description.md)
   - 从[Azure 门户](https://portal.azure.com/#create/hub)
   - 在[Azure 保密计算（虚拟机）](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) marketplace 解决方案模板中。 Marketplace 解决方案模板将帮助客户提供受支持的方案（区域、图像、可用性、磁盘加密）。 

1. **所有 OS 映像是否适用于 Azure 机密计算？**

   否。 只能在第2代虚拟机上部署虚拟机。 我们为 Ubuntu Server 18.04、Ubuntu Server 16.04 和 Windows Server 2016 Datacenter 提供第2代支持。 阅读有关[Linux](../virtual-machines/linux/generation-2.md)和[Windows](../virtual-machines/windows/generation-2.md)上的第2代 vm 的详细信息

1. **DCsv2 虚拟机在门户中灰显，无法选择其中的虚拟机**

   根据 VM 旁边的信息气泡，需要执行不同的操作：
    -   **UnsupportedGeneration**：将虚拟机映像的生成更改为 "Gen2"。
    -   **NotAvailableForSubscription** ：该区域尚不适用于你的订阅。 选择可用区域。
    -   **InsufficientQuota**：[创建支持请求以增加你的配额](../azure-portal/supportability/per-vm-quota-requests.md)。 免费试用订阅没有适用于机密计算 Vm 的配额。 

1. **当我尝试在门户大小选择器中搜索时，DCsv2 虚拟机不会显示**

   请确保已选择可用区域。 此外，请确保在大小选择器中选择 "清除所有筛选器"。 

1. **DCsv2 系列和 DC 系列 Vm 之间有何区别？**

   在采用 Intel SGX 的老式6核 Intel 处理器上运行 DC 系列 Vm。 它们的内存总量更少，小于小于（Enclave 页框架）内存，并且在更少的区域中可用。 这些 Vm 仅适用于美国东部，欧洲西部提供两种大小： Standard_DC2s 和 Standard_DC4s。 它们不会成为 GA，只能部署到[机密计算 DC 系列 VM [预览版]](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview) Marketplace 实例

1. **DCsv2 虚拟机是否全局可用？**

   不能，这些虚拟机仅在选择区域中可用。 查看最新可用区域的 "[产品（按区域](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)）" 页。 

1. **如何实现安装开放的 Enclave SDK？**
   
   有关如何在计算机上安装 OE SDK 的说明，无论是在 Azure 中还是在本地，请按照[Open ENCLAVE SDK GitHub](https://github.com/openenclave/openenclave)上的说明进行操作。
     
   你还可以转到 Open Enclave SDK GitHub 来了解特定于操作系统的安装说明：
     - [在 Windows 上安装 OE SDK](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
     - [在 Ubuntu 18.04 上安装 OE SDK](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
     - [在 Ubuntu 16.04 上安装 OE SDK](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)
