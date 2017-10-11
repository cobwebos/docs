---
title: "映射网络以便使用 Azure Site Recovery 将 Hyper-V VM 复制到辅助站点 | Microsoft Docs"
description: "介绍使用 Azure Site Recovery 将 Hyper-V VM 复制到辅助 VMM 站点时如何映射网络。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 461b7c1c-ef61-4005-aeec-2324e727a3d0
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 606e2d3d0e31b9d80200105e812f9d7bbbcf939c
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="step-7-map-networks-for-hyper-v-vm-replication-to-a-secondary-site"></a>步骤 7：映射网络以便将 Hyper-V VM 复制到辅助站点


设置完用于将 Hyper-V 虚拟机 (VM) 复制到辅助 System Center Virtual Machine Manager (VMM) 站点的[源和目标设置](vmm-to-vmm-walkthrough-source-target.md)后，使用本文通过 [Azure Site Recovery](site-recovery-overview.md) 配置网络映射以便将 Hyper-V 虚拟机 (VM) 复制到辅助站点。

阅读本文后，请在底部发布评论，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。


## <a name="before-you-start"></a>开始之前

- 开始前，请先了解[网络映射](vmm-to-vmm-walkthrough-network.md#network-mapping-overview)的相关信息。
- 验证 VMM 服务器上的虚拟机是否已连接到 VM 网络。

## <a name="configure-network-mapping"></a>配置网络映射

1. 在“网络映射” > “网络映射”中，单击“+网络映射”。
2. 在“添加网络映射”选项卡中，选择源和目标 VMM 服务器。 系统将检索与 VMM 服务器关联的 VM 网络。
3. 在“源网络”中，从与主 VMM 服务器关联的 VM 网络列表中选择要使用的网络。
4. 在“目标网络”中，选择要在辅助 VMM 服务器上使用的网络。 。

    ![网络映射](./media/vmm-to-vmm-walkthrough-network-mapping/network-mapping2.png)

下面是网络映射开始时发生的事情：

* 与源 VM 网络对应的任何现有副本虚拟机都将连接到目标 VM 网络。
* 在复制后，连接到源 VM 网络的新虚拟机将连接到目标映射网络。
* 如果修改了与新网络之间的映射，则会使用新设置来连接副本虚拟机。
* 如果目标网络具有多个子网，并且其中一个子网与源虚拟机所在的子网同名，则在故障转移后副本虚拟机将连接到该目标子网。 如果没有具有匹配名称的目标子网，则虚拟机将连接到网络中的第一个子网。



## <a name="next-steps"></a>后续步骤

转到[步骤 8：配置复制策略](vmm-to-vmm-walkthrough-replication.md)。