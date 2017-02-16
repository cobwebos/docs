---
title: "在 Azure 区域之间迁移 Azure IaaS VM | Microsoft 文档"
description: "使用 Azure Site Recovery 将 Azure IaaS 虚拟机从一个 Azure 区域迁移到另一个 Azure 区域。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8a29e0d9-0010-4739-972f-02b8bdf360f6
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 3b606aa6dc3b84ed80cd3cc5452bbe1da6c79a8b
ms.openlocfilehash: 3db1ec50b357c5bf6da51aaaf0f04ccb65d1bb05


---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>使用 Azure Site Recovery 在 Azure 区域之间迁移 Azure IaaS 虚拟机
## <a name="overview"></a>概述
欢迎使用 Azure Site Recovery！ 如果想要在 Azure 区域之间迁移 Azure VM，请参阅此文章。 开始之前，请注意：

* Azure 提供两个不同的部署模型来创建和处理资源：Azure Resource Manager 和经典。 Azure 还有两个门户 – 支持经典部署模型的 Azure 经典门户，以及支持两种部署模型的 Azure 门户。 无论是在 Resource Manager 中还是在经典中配置 Site Recovery，迁移的基本步骤都是相同的。 但是，本文中的 UI 说明和屏幕截图与 Azure 门户相关。
* **目前仅能从一个区域迁移到另一个区域。可以将 VM 从一个 Azure 区域故障转移到另一个 Azure 区域，但不能重新对其进行故障回复。**
* 本文中的迁移说明以将物理计算机复制到 Azure 的说明为基础。 其中包括指向[将 VMware VM 或物理服务器复制到 Azure](site-recovery-vmware-to-azure.md) 的步骤链接，链接文章介绍了如何在 Azure 门户中复制物理服务器。
* 如果要在经典门户中设置 Site Recovery，请按照[本文](site-recovery-vmware-to-azure-classic.md)中的详细说明进行操作。 **不应再使用**此[旧版文章](site-recovery-vmware-to-azure-classic-legacy.md)中的说明。

请将任何评论或问题发布到本文底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## <a name="prerequisites"></a>先决条件
以下是执行此部署所需的组件：

* **配置服务器**：运行 Windows Server 2012 R2 作为配置服务器的本地 VM。 在此 VM 上也安装其他 Site Recovery 组件（包括进程服务器和主目标服务器）。 有关详细信息，请参阅[方案体系结构](site-recovery-vmware-to-azure.md#scenario-architecture)和[配置服务器先决条件](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites)。
* **IaaS 虚拟机**：想要迁移的 VM。 将 VM 视为物理计算机进行迁移。

## <a name="deployment-steps"></a>部署步骤
本节介绍新 Azure 门户中的部署步骤。 如果需要经典门户中 Site Recovery 的这些部署步骤，请参阅[本文](site-recovery-vmware-to-azure-classic.md)。

1. [创建保管库](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault)。
2. [部署配置服务器](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment)。
3. 部署配置服务器之后，检查其是否能与要迁移的 VM 通信。
4. [设置复制设置](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings)。 创建复制策略并分配到配置服务器。
5. [安装移动服务](site-recovery-vmware-to-azure.md#step-6-replicate-applications)。 你要保护的每个虚拟机需要安装移动服务。 此服务将数据发送到进程服务器。 可以手动安装移动服务，也可以在启用了虚拟机保护后由进程服务器自动推送并安装。 要迁移的 VM 上的防火墙规则应配置为允许此服务的推送安装。
6. [启用复制](site-recovery-vmware-to-azure.md#enable-replication)。 为需要迁移的虚拟机启用复制。 你可以使用虚拟机的 IP 地址发现要迁移到 Azure 的 IaaS 虚拟机。 在 Azure 中的虚拟机仪表板上找到此地址。 启用复制时，将 VM 的计算机类型设置为物理计算机。
7. [运行非计划的故障转移](site-recovery-failover.md#run-an-unplanned-failover)。 初始复制完成之后，可以运行从一个 Azure 区域到另一个 Azure 区域的非计划的故障转移。 （可选）你可以创建一个恢复计划并运行非计划的故障转移，在区域之间迁移多个虚拟机。 [详细了解](site-recovery-create-recovery-plans.md)恢复计划。

## <a name="next-steps"></a>后续步骤
若要详细了解其他复制方案，请参阅[什么是 Azure Site Recovery？](site-recovery-overview.md)



<!--HONumber=Jan17_HO5-->


