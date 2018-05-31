---
title: 使用 Site Recovery 对复制到辅助数据中心的 Hyper-V VM 进行故障转移和故障回复 | Microsoft Docs
description: 了解如何使用 Azure Site Recovery 将 Hyper-V VM 故障转移到辅助本地站点以及故障回复到主站点
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/02/2018
ms.author: raynew
ms.openlocfilehash: ecb0b9395ce7071442ddf0dd976e1ca57b8be906
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161135"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>对复制到辅助本地站点的 Hyper-V VM 进行故障转移和故障回复

[Azure Site Recovery](site-recovery-overview.md) 服务可管理和协调本地计算机和 Azure 虚拟机 (VM) 的复制、故障转移和故障回复。

本文介绍如何将 System Center Virtual Machine Manager (VMM) 云中托管的 Hyper-V VM 故障转移到辅助 VMM 站点。 故障转移后，可故障回复到本地站点（若可行）。 在本文中，学习如何：

> [!div class="checklist"]
> * 将 Hyper-V VM 从主 VMM 云故障转移到辅助 VMM 云
> * 从辅助站点重新保护到主站点，并进行故障回复
> * （可选）再次开始从主站点复制到辅助站点

## <a name="failover-and-failback"></a>故障转移和故障回复

故障转移和故障回复具有三个阶段：

1. **故障转移到辅助站点**：将计算机从主站点故障转移到辅助站点。
2. **从辅助站点故障回复**：将 VM 从辅助站点复制到主站点，并运行计划的故障转移以进行故障回复。
3. 在计划的故障转移之后，可以选择再次开始从主站点复制到辅助站点。


## <a name="prerequisites"></a>先决条件

- 确保已完成[灾难恢复演练](hyper-v-vmm-test-failover.md)，检查所有内容是否都按预期工作。
- 若要完成故障回复，请确保主和辅助 VMM 服务器已连接到 Site Recovery。



## <a name="run-a-failover-from-primary-to-secondary"></a>运行从主站点到辅助站点的故障转移

可以为 Hyper-V VM 运行常规或计划的故障转移。

- 使用常规故障转移处理意外中断。 当运行此故障转移时，Site Recovery 会在辅助站点中创建一个 VM，并将其启动。 根据未同步的挂起数据，可能会丢失数据。
- 可在维护或预期中断过程中使用计划内故障转移。 此选项保证无数据丢失。 触发计划内故障转移时，关闭源 VM。 同步未同步的数据，并触发故障转移。 
- 
本过程介绍如何运行常规故障转移。


1. 在“设置” > “复制的项”中，单击“VM”>“故障转移”。
1. 如果希望 Site Recovery 在触发故障转移之前尝试关闭源 VM，请选择“在开始故障转移前关闭计算机”。 在触发故障转移前，Site Recovery 还会尝试同步尚未发送到辅助站点的本地数据。 请注意：即使关机失败，故障转移也仍会继续。 可以在“作业”页上跟踪故障转移进度。
2. 你现在应能够在辅助 VMM 云中看到 VM。
3. 验证 VM 后，“提交”故障转移。 这会删除所有可用的恢复点。

> [!WARNING]
> **请勿取消正在进行的故障转移**：在故障转移开始前，停止 VM 复制。 如果取消正在进行的故障转移，故障转移会停止，但 VM 将不再进行复制。  


## <a name="reverse-replicate-and-failover"></a>反向复制和故障转移

开始从辅助站点复制到主站点，并故障回复到主站点。 VM 在主站点中再次运行之后，可以将它们复制到辅助站点。  

 
1. 单击“VM”> 单击“反向复制”。
2. 在完成作业后，单击“VM”，在“故障转移”中确认故障转移方向（从辅助 VMM 云），并选择源和目标位置。 
4. 启动故障转移。 可以在“**作业**”选项卡上跟踪故障转移进度。
5. 在主 VMM 云中，检查 VM 是否可用。
6. 如果要再次开始将主 VM 复制回辅助站点，请单击“反向复制”。

## <a name="next-steps"></a>后续步骤
[查看将 Hyper-V VM 复制到辅助站点的步骤](hyper-v-vmm-disaster-recovery.md)。
