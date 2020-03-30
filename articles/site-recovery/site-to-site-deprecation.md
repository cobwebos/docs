---
title: 使用 Azure 站点恢复在客户托管站点（使用 VMM）之间弃用灾难恢复 |微软文档
description: 有关使用 Hyper-V 的客户拥有的站点之间以及 SCVMM 管理的站点与 Azure 和备用选项之间即将弃用 DR 的详细信息
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: rajanaki
ms.openlocfilehash: 208177d10e9002fafe2495710da229541a11a43e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661664"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>使用 Azure 站点恢复在客户托管站点（使用 VMM）之间弃用灾难恢复

本文介绍了即将推出的弃用计划、相应的影响以及适用于以下方案的客户可用的替代选项：

使用站点恢复的系统中心虚拟机管理器 （SCVMM） 管理的客户拥有的站点之间的 DR

> [!IMPORTANT]
> 建议客户尽早采取补救措施，以避免对其环境造成任何破坏。 

## <a name="what-changes-should-you-expect"></a>您应该期待哪些更改？

- 从 2020 年 3 月开始，您将收到 Azure 门户通知&电子邮件通信，以及即将弃用的 Hyper-V VM 站点复制到。 弃用计划于 2023 年 3 月进行。

- 如果您有现有配置，则对设置没有影响。

- 一旦方案被弃用，除非客户遵循替代方法，否则现有复制可能会中断。 客户将无法通过 Azure 门户中的 Azure 站点恢复体验查看、管理或执行任何与 DR 相关的操作。
 
## <a name="alternatives"></a>备选项 

以下是客户可以选择的备选方案，以确保一旦方案被弃用，他们的 DR 策略不会受到影响。 

- 选项 1（建议）：选择[开始使用 Azure 作为 DR 目标](hyper-v-vmm-azure-tutorial.md)。


- 选项 2：选择使用基础[Hyper-V 副本解决方案](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)继续进行站点到站点复制，但您将无法使用 Azure 门户中的 Azure 站点恢复管理 DR 配置。 


## <a name="remediation-steps"></a>补救步骤

如果您选择使用选项 1，请执行以下步骤：

1. [禁用与 VM 关联的所有虚拟机的保护](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario)。 使用**禁用复制和删除**选项或运行上述脚本，以确保清理本地复制设置。 

2. 从站点到站点复制配置[中取消注册所有 VMM 服务器](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server)。

3. [准备 Azure 资源](tutorial-prepare-azure-for-hyperv.md)以启用 VM 的复制。
4. [准备本地 Hyper-V 服务器](hyper-v-prepare-on-premises-tutorial.md)
5. [为 VMM 云中的 VM 设置复制](hyper-v-vmm-azure-tutorial.md)
6. 可选但建议：[运行 DR 钻](tutorial-dr-drill-azure.md)

如果您选择使用使用 Hyper-V 副本的选项 2，请执行以下步骤：

1. 在**受保护项目** > **复制项**中，右键单击计算机>**禁用复制**。
2. 在**禁用复制**中，选择 **"删除**"。

    此选项会从 Azure Site Recovery 中删除复制的项（停止计费）。 不过，并不会**** 清理本地虚拟机上的复制配置。 

## <a name="next-steps"></a>后续步骤
规划弃用，并选择最适合您的基础架构和业务的备用选项。 如果您对此有任何疑问，则联系 Microsoft 支持部门

