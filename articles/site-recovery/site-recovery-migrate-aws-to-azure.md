---
title: "将 VM 从 AWS 迁移到 Azure | Microsoft 文档"
description: "本文介绍如何使用 Azure Site Recovery 将 Amazon Web Services (AWS) 中运行的虚拟机迁移到 Azure。"
services: site-recovery
documentationcenter: 
author: bsiva
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 02/12/2017
ms.author: bsiva
translationtype: Human Translation
ms.sourcegitcommit: 5031f64ffcd34b6287a3ecd87dd027c2bc7c716f
ms.openlocfilehash: 9d0d0ba4ca5966b39ce62ea8296d48e5930c9782


---
# <a name="migrate-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>使用 Azure Site Recovery 将 Amazon Web Services (AWS) 中的虚拟机迁移到 Azure

本文介绍如何使用 [Azure Site Recovery](site-recovery-overview.md) 服务将 AWS Windows 实例迁移到 Azure 虚拟机。

迁移实际上是 AWS 到 Azure 的故障转移。 无法故障回复计算机，且没有任何正在进行的复制。 本文以[将物理计算机复制到 Azure](site-recovery-vmware-to-azure.md) 的说明为基础，介绍了在 Azure 门户进行迁移的步骤。

请将任何评论或问题发布到本文底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## <a name="supported-operating-systems"></a>支持的操作系统

Site Recovery 可用于迁移运行以下任意操作系统的 EC2 实例：

- Windows（仅&64; 位）
    - Windows Server 2008 R2 SP1 及更高版本（仅限 Citrix PV 驱动程序或 AWS PV 驱动程序。 **不支持运行 RedHat PV 驱动程序的实例**）Windows Server 2012 Windows Server 2012 R2
- Linux（仅&64; 位）
    - Red Hat Enterprise Linux 6.7（仅适用于 HVM 虚拟化实例）

## <a name="prerequisites"></a>先决条件

以下是执行此部署所需的组件

* **配置服务器**：运行 Windows Server 2012 R2 的本地 VM 将部署为配置服务器。 默认情况下，部署配置服务器时将再安装一个 Site Recovery 组件（进程服务器和主目标服务器）。 [了解详细信息](site-recovery-components.md#replicate-vmware-vmsphysical-servers-to-azure)
* **EC2 实例**：要迁移的 EC2 虚拟机实例。

## <a name="deployment-steps"></a>部署步骤

1. 创建保管库
2. 部署配置服务器
3. 部署配置服务器之后，验证其是否能与要迁移的 VM 通信。
4. 设置复制设置。
5. 安装移动服务。 你要保护的每个虚拟机需要安装移动服务。 此服务将数据发送到进程服务器。 可以手动安装移动服务，也可以在启用了虚拟机保护后由进程服务器自动推送并安装。 要迁移的 EC2 实例上的防火墙规则应配置为允许此服务的推送安装。 EC2 实例的安全组应具有以下规则：

    ![防火墙规则](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)
6. 启用复制。 为需要迁移的虚拟机启用复制。 可以使用专用 IP 地址发现 EC2 实例，该地址可以在 EC2 控制台中获取。
7. 运行非计划的故障转移）。 初始复制完成后，可以为每个 VM 运行一个从 AWS 到 Azure 的非计划故障转移。 （可选）你可以创建一个恢复计划并运行非计划的故障转移，从 AWS 向 Azure 迁移多个虚拟机。 [详细了解](site-recovery-create-recovery-plans.md)恢复计划。

获取有关[部署步骤](site-recovery-vmware-to-azure.md)和运行[非计划的故障转移](site-recovery-failover.md#run-an-unplanned-failover)的详细说明。



<!--HONumber=Feb17_HO2-->


