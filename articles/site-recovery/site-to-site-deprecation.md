---
title: 弃用使用 Azure Site Recovery 的客户托管站点（使用 VMM）之间的灾难恢复 |Microsoft Docs
description: 有关即将发生的客户所拥有的站点的灾难恢复的详细信息，请使用 Hyper-v 在 SCVMM 管理的站点之间以及备用选项
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: rajanaki
ms.openlocfilehash: 208177d10e9002fafe2495710da229541a11a43e
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77661664"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>使用 Azure Site Recovery 在客户管理的站点（使用 VMM）之间弃用灾难恢复

本文介绍了即将推出的弃用计划、相应的含意以及可供客户使用的替代选项：

使用 Site Recovery 通过 System Center Virtual Machine Manager （SCVMM）管理的客户拥有的站点之间的 DR

> [!IMPORTANT]
> 建议客户尽可能最早地执行修正步骤，以避免对其环境造成任何中断。 

## <a name="what-changes-should-you-expect"></a>你需要做哪些更改？

- 从2020年3月开始，你将收到 Azure 门户通知 & 电子邮件通信，其中包含即将弃用的 Hyper-v Vm 站点到站点复制。 弃用计划于2023年3月。

- 如果有现有配置，则不会对设置产生任何影响。

- 一旦这些方案被弃用，除非客户遵循备用方法，否则现有复制可能会中断。 客户无法通过 Azure 门户中的 Azure Site Recovery 体验来查看、管理或执行任何 DR 相关的操作。
 
## <a name="alternatives"></a>备选项 

下面是客户可以选择的替代方案，以确保在方案被弃用后，其 DR 策略不会受到影响。 

- 选项1（推荐）：选择[开始使用 Azure 作为 DR 目标](hyper-v-vmm-azure-tutorial.md)。


- 选项2：选择使用基础[Hyper-v 副本解决方案](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)继续进行站点到站点复制，但你将无法使用 Azure 门户中的 Azure Site Recovery 来管理 DR 配置。 


## <a name="remediation-steps"></a>修正步骤

如果选择选择 "选项 1"，请执行以下步骤：

1. [禁用与 VMMs 关联的所有虚拟机的保护](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario)。 使用 "**禁用复制并删除**" 选项或运行提到的脚本，确保清理本地的复制设置。 

2. 从站点到站点复制配置中[注销所有 VMM 服务器](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server)。

3. [准备 Azure 资源](tutorial-prepare-azure-for-hyperv.md)以启用 vm 复制。
4. [准备本地 Hyper-v 服务器](hyper-v-prepare-on-premises-tutorial.md)
5. [在 VMM 云中为 Vm 设置复制](hyper-v-vmm-azure-tutorial.md)
6. 可选，但建议使用：[运行 DR 演练](tutorial-dr-drill-azure.md)

如果选择使用 Hyper-v 副本的选项2，请执行以下步骤：

1. 依次转到“受保护的项” > “复制的项”，右键单击计算机，再单击“禁用复制”。
2. 在 "**禁用复制**" 中，选择 "**删除**"。

    此选项会从 Azure Site Recovery 中删除复制的项（停止计费）。 不过，并不会清理本地虚拟机上的复制配置。 

## <a name="next-steps"></a>后续步骤
规划弃用，并选择最适合你的基础结构和业务的替代选项。 如果你有关于此问题的任何查询，请联系 Microsoft 支持部门

