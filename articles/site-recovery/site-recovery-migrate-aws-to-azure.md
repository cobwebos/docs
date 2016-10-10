<properties
	pageTitle="使用 Site Recovery 将 Windows 虚拟机从 Amazon Web Services 迁移到 Azure | Azure"
	description="本文介绍如何使用 Azure Site Recovery 将 Amazon Web Services (AWA) 中运行的 Windows 虚拟机迁移到 Azure。"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="08/22/2016"
	wacn.date=""
	ms.author="raynew"/>

#  使用 Azure Site Recovery 将 Amazon Web Services (AWS) 中的 Windows 虚拟机迁移到 Azure

## 概述

欢迎使用 Azure Site Recovery。根据本文所述的方法，使用 Site Recovery 将 AWS 中运行的 Windows 实例迁移到 Azure。开始之前，请注意：

- Azure 提供两个不同的部署模型来创建和处理资源：Azure Resource Manager 和经典。Azure 还有两个门户 – 支持经典部署模型的 Azure 经典门户，以及支持两种部署模型的 Azure 门户。无论是在 Resource Manager 中还是在经典中配置 Site Recovery，迁移的基本步骤都是相同的。但是，本文中的 UI 说明和屏幕截图与 Azure 门户相关。
- **目前仅能从 AWS 迁移到 Azure。可以从 AWS 将 VM 故障转移到 Azure，但不能重新对其进行故障回复。没有任何正在进行的复制。**
- 本文中的迁移说明以将物理计算机复制到 Azure 的说明为基础。其中包括指向[将 VMware VM 或物理服务器复制到 Azure](/documentation/articles/site-recovery-vmware-to-azure/) 的步骤的链接，链接文章介绍了如何在 Azure 门户中复制物理服务器。
- 如果要在经典门户中设置 Site Recovery，请按照[本文](/documentation/articles/site-recovery-vmware-to-azure-classic/)中的详细说明进行操作。**不应该再使用**此[旧版文章](/documentation/articles/site-recovery-vmware-to-azure-classic-legacy/)中的说明。

请将任何评论或问题发布到本文底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/Forums/zh-cn/home?forum=hypervrecovmgr)


## 先决条件

以下是执行此部署所需的组件

- **配置服务器**：运行 Windows Server 2012 R2 作为配置服务器的本地 VM。在此 VM 上也安装其他 Site Recovery 组件（包括进程服务器和主目标服务器）。有关详细信息，请参阅[方案体系结构](/documentation/articles/site-recovery-vmware-to-azure/#scenario-architecture)和[配置服务器先决条件](/documentation/articles/site-recovery-vmware-to-azure/#configuration-server-prerequisites)。
- **EC2 VM 实例**：想要迁移的运行 Windows 的实例。

## 部署步骤

本节介绍新 Azure 门户中的部署步骤。如果需要经典门户中 Site Recovery 的这些部署步骤，请参阅 [本文](/documentation/articles/site-recovery-vmware-to-azure-classic/)。

1. [创建保管库](/documentation/articles/site-recovery-vmware-to-azure/#create-a-recovery-services-vault)
2. [部署配置服务器](/documentation/articles/site-recovery-vmware-to-azure/#step-2-set-up-the-source-environment)。
3. 部署配置服务器之后，验证其是否能与要迁移的 VM 通信。
4. [设置复制设置](/documentation/articles/site-recovery-vmware-to-azure/#step-4-set-up-replication-settings)。创建复制策略并分配到配置服务器。
5. [安装移动服务](/documentation/articles/site-recovery-vmware-to-azure/#step-6-replication-application)。你要保护的每个虚拟机需要安装移动服务。此服务将数据发送到进程服务器。可以手动安装移动服务，也可以在启用了虚拟机保护后由进程服务器自动推送并安装。要迁移的 EC2 实例上的防火墙规则应配置为允许此服务的推送安装。EC2 实例的安全组应具有以下规则：

	![防火墙规则](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)

6. [启用复制](/documentation/articles/site-recovery-vmware-to-azure/#enable-replication)。为需要迁移的虚拟机启用复制。可以使用专用 IP 地址发现 EC2 实例，该地址可以在 EC2 控制台中获取。
9. [运行非计划的故障转移](/documentation/articles/site-recovery-failover/#run-an-unplanned-failover)。初始复制完成后，可以为每个 VM 运行从 AWS 到 Azure 的非计划故障转移。（可选）你可以创建一个恢复计划并运行非计划的故障转移，从 AWS 向 Azure 迁移多个虚拟机。[详细了解](/documentation/articles/site-recovery-create-recovery-plans/)恢复计划。

## 后续步骤

若要详细了解其他复制方案，请参阅[什么是 Azure Site Recovery？](/documentation/articles/site-recovery-overview/)

<!---HONumber=Mooncake_0926_2016-->