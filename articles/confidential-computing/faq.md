---
title: Azure 机密计算常见问题解答
description: 有关 Azure 机密计算的常见问题解答。
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: workloads
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: bb821d00a168e3b8f0636b93696376dc8b5d492e
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772892"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Azure 机密计算常见问题解答

本文提供了有关运行 [Azure 上的机密计算工作负荷](overview.md)时出现的一些最常见问题的解答。

如果本文未解决 Azure 问题，请访问 [MSDN 和 Stack Overflow](https://azure.microsoft.com/support/forums/) 上的 Azure 论坛。 可将问题发布到这些论坛上，或发布到 [Twitter 上的 @AzureSupport](https://twitter.com/AzureSupport)。 还可提交 Azure 支持请求。 若要提交支持请求，请在 [Azure 支持](https://azure.microsoft.com/support/options/)页上，选择“获取支持”。

## <a name="confidential-computing-virtual-machines"></a>机密计算虚拟机 <a id="vm-faq"></a>

**如何在 Azure 上部署 DCsv2 系列 VM？**

可通过以下方式部署 DCsv2 VM：
   - 使用 [Azure 资源管理器模板](../virtual-machines/windows/template-description.md)
   - 通过 [Azure 门户](https://portal.azure.com/#create/hub)
   - 通过 [Azure 机密计算（虚拟机）](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview)市场解决方案模板。 该市场解决方案模板有助于将客户约束为使用受支持的方案（区域、映像、可用性、磁盘加密）。 

**是否所有 OS 映像均可与 Azure 机密计算配合工作？**

不是。 只能在装有 Ubuntu Server 18.04、Ubuntu Server 16.04、Windows Server 2019 Datacenter 或 Windows Server 2016 Datacenter 的第 2 代操作计算机上部署虚拟机。 详细了解 [Linux](../virtual-machines/linux/generation-2.md) 和 [Windows](../virtual-machines/windows/generation-2.md) 上的第 2 代 VM

**DCsv2 虚拟机在门户中灰显，我无法选择此类虚拟机**

根据 VM 旁边的信息气泡执行不同的操作：
   -    **UnsupportedGeneration**：将虚拟机映像的代系更改为“Gen2”。
   -    **NotAvailableForSubscription**：此区域尚不可用于你的订阅。 选择可用区域。
   -    **InsufficientQuota**：[创建支持请求以提高配额](../azure-portal/supportability/per-vm-quota-requests.md)。 免费试用订阅没有提供机密计算 VM 配额。 

**当我尝试在门户上的大小选择器中搜索 DCsv2 虚拟机时，未显示此类虚拟机**

请确保已选择一个[可用区域](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)。 另外，请确保在大小选择器中选择“清除所有筛选器”。 

**我收到 Azure 资源管理器模板部署失败错误：“操作无法完成，因为此操作导致超出批准的标准 DcsV2 系列核心配额”**

[创建支持请求以提高配额](../azure-portal/supportability/per-vm-quota-requests.md)。 免费试用订阅没有提供机密计算 VM 配额。 

**DCsv2 系列与 DC 系列 VM 之间有何差别？**

DC 系列 VM 在较旧的 6 核 Intel 处理器上运行，使用 Intel SGX 并且总内存和 Enclave 页面缓存 (EPC) 内存较少，仅可在两个区域（“美国东部”和“西欧”，Standard_DC2s 和 Standard_DC4s 大小）中使用。 我们不打算正式发布这些 VM，且不建议将其用于生产。 若要部署这些 VM，请使用[机密计算 DC 系列 VM [预览版]](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview) 市场实例。

**DCsv2 虚拟机是否可在全球使用？**

不是。 目前，这些虚拟机只能在特定的区域中使用。 有关最新的可用区域，请查看[产品上市区域页](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)。 

**如何在 DCsv2 虚拟机上安装 Open Enclave SDK？**
   
有关如何在 Azure 或本地计算机上安装 OE SDK 的说明，请参阅 [Open Enclave SDK GitHub](https://github.com/openenclave/openenclave)。
     
还可以在 Open Enclave SDK GitHub 中详细了解特定于 OS 的安装说明：
   - [在 Windows 上安装 OE SDK](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
   - [在 Ubuntu 18.04 上安装 OE SDK](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
   - [在 Ubuntu 16.04 上安装 OE SDK](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)
