---
title: 使用 Azure Site Recovery (与 VMM) 的客户托管网站之间的灾难恢复已弃用 |Microsoft Docs
description: 详细介绍了使用 Hyper-V 在客户拥有的站点之间以及在由 SCVMM 管理的站点之间通过 Azure Site Discovery 进行灾难恢复这一功能即将弃用，并详细介绍了替代选项
services: site-recovery
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: sharrai
ms.openlocfilehash: 9ffe7a3158b1de6828350947dcf81ef41d08708d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87421835"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>使用 Azure Site Recovery 在客户管理的站点（包含 VMM）之间进行灾难恢复功能的弃用

本文介绍了即将推出的弃用计划、相应的影响以及以下方案中可供客户使用的替代选项：

使用 Site Recovery 在客户拥有的由 System Center Virtual Machine Manager (SCVMM) 管理的站点之间进行灾难恢复

> [!IMPORTANT]
> 建议客户尽早采取修正步骤，以避免对其环境造成任何中断。 

## <a name="what-changes-should-you-expect"></a>会发生哪些更改？

- 从 2020 年 3 月起，你将收到 Azure 门户通知和电子邮件通信，其中将介绍 Hyper-V VM 的站点到站点复制即将弃用。 此弃用计划在 2023 年 3 月之前完成。

- 对于现有的配置，对设置没有任何影响。

- 一旦这些方案被弃用，除非客户按照替代方法进行操作，否则现有复制可能会中断。 客户将无法在 Azure 门户中通过 Azure Site Recovery 体验来查看、管理或执行任何 DR 相关操作。
 
## <a name="alternatives"></a>备选方法 

下面是客户可以选择的替代方法，可以确保在方案被弃用后，其 DR 策略不会受到影响。 

- 选项 1（推荐使用）：选择[开始使用 Azure 作为 DR 目标](hyper-v-vmm-azure-tutorial.md)。


- 选项 2：选择继续使用站点到站点复制并利用基础的 [Hyper-V 副本解决方案](/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)，但你将无法在 Azure 门户中使用 Azure Site Recovery 来管理 DR 配置。 


## <a name="remediation-steps"></a>修正步骤

如果选择使用“选项 1”，请执行以下步骤：

1. [禁用与 VMM 关联的所有虚拟机的保护](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario)。 使用“禁用复制并删除”  选项，或运行所提到的脚本以确保清理本地的复制设置。 

2. 从站点到站点复制配置中[取消注册所有 VMM 服务器](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server)。

3. 为启用 VM 复制[准备 Azure 资源](tutorial-prepare-azure-for-hyperv.md)。
4. [准备本地 Hyper-V 服务器](hyper-v-prepare-on-premises-tutorial.md)
5. [为 VMM 云中的 VM 设置复制](hyper-v-vmm-azure-tutorial.md)
6. 可选但建议使用：[运行 DR 演练](tutorial-dr-drill-azure.md)

如果选择使用“选项 2”，即使用 Hyper-V 副本，请执行以下步骤：

1. 依次转到“受保护的项”   > “复制的项”  ，右键单击计算机，再单击“禁用复制”  。
2. 在“禁用复制”  中，选择“删除”  。

    此选项会从 Azure Site Recovery 中删除复制的项（停止计费）。 不过，并不会  清理本地虚拟机上的复制配置。 

## <a name="next-steps"></a>后续步骤
规划弃用并选择最适合你的基础设施和业务的替代选项。 如果你有关于此问题的任何查询，请联系 Microsoft 支持部门

