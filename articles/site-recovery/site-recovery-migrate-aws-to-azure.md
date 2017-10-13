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
ms.workload: storage-backup-recovery
ms.date: 05/31/2017
ms.author: bsiva
ms.openlocfilehash: b3c0727a279649f4f7dae30d41027129ce5b04ee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>使用 Azure Site Recovery 将 Amazon Web Services (AWS) 中的虚拟机迁移到 Azure

本文介绍如何使用 [Azure Site Recovery](site-recovery-overview.md) 服务将 AWS Windows 实例迁移到 Azure 虚拟机。

迁移实际上是 AWS 到 Azure 的故障转移。 无法将计算机故障回复到 AWS，且没有任何正在进行的复制。 本文以[将物理计算机复制到 Azure](site-recovery-vmware-to-azure.md) 的说明为基础，介绍了在 Azure 门户进行迁移的步骤。

请将任何评论或问题发布到本文底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## <a name="supported-operating-systems"></a>支持的操作系统

Site Recovery 可用于迁移运行以下任意操作系统的 EC2 实例：

- Windows（仅 64 位）
    - Windows Server 2008 R2 SP1 及更高版本（仅限 Citrix PV 驱动程序或 AWS PV 驱动程序。 **不支持运行 RedHat PV 驱动程序的实例**）Windows Server 2012 Windows Server 2012 R2
- Linux（仅 64 位）
    - Red Hat Enterprise Linux 6.7（仅适用于 HVM 虚拟化实例）

## <a name="prerequisites"></a>先决条件

以下是执行此部署所需的组件：

* **配置服务器**：运行 Windows Server 2012 R2 的 Amazon EC2 VM 将部署为配置服务器。 默认情况下，部署配置服务器时会安装其他 Azure Site Recovery 组件（进程服务器和主目标服务器）。 本文以[了解更多](site-recovery-components.md)的说明为基础，介绍在 Azure 门户中进行迁移的步骤

* **EC2 实例**：要迁移的 Amazon EC2 虚拟机实例。

## <a name="deployment-steps"></a>部署步骤

1. 创建恢复服务保管库。
2. EC2 实例的安全组应配置相关规则，允许在想要迁移的 EC2 实例和计划在其中部署配置服务器的实例之间进行通信。

3. 在 EC2 实例所在的 Amazon 虚拟私有云上，部署 ASR 配置服务器。 请参阅将 VMware 虚拟机/物理计算机迁移到 Azure 的先决条件，了解配置服务器部署要求。

    ![DeployCS](./media/site-recovery-migrate-aws-to-azure/migration_pic2.png)

4.  将配置服务器部署到 AWS 中并注册到恢复服务保管库后，应在 Site Recovery 基础结构下看到配置服务器和进程服务器，如下所示：

    ![CSinVault](./media/site-recovery-migrate-aws-to-azure/migration_pic3.png)

5. 部署配置服务器（最长可能需要 15 分钟才会显示）后，验证其是否可以与想要迁移的 VM 通信。

6. [设置复制设置](site-recovery-setup-replication-settings-vmware.md)。

7. 启用复制：为要迁移的 VM 启用复制。 可以使用专用 IP 地址发现 EC2 实例，该地址可以在 EC2 控制台中获取。

    ![SelectVM](./media/site-recovery-migrate-aws-to-azure/migration_pic4.png)

8. EC2 实例受到保护且复制到 Azure 的操作已完成后，请[运行测试故障转移](site-recovery-test-failover-to-azure.md)，在 Azure 中验证应用程序的性能。

    ![TFI](./media/site-recovery-migrate-aws-to-azure/migration_pic5.png)

9. 针对每个 VM 运行从 AWS 到 Azure 的故障转移。 （可选）可以创建一个恢复计划并运行故障转移，从 AWS 向 Azure 迁移多个虚拟机。 [详细了解](site-recovery-create-recovery-plans.md)恢复计划。

10. 迁移时，不需要提交故障转移。 而是，对于要迁移的每台计算机，选择“完成迁移”选项。 “完成迁移”操作会完成迁移过程，删除计算机复制设置，使计算机不再产生 Site Recovery 费用。

    ![迁移](./media/site-recovery-migrate-aws-to-azure/migration_pic6.png)

## <a name="next-steps"></a>后续步骤

- [准备迁移的计算机以启用复制](site-recovery-azure-to-azure-after-migration.md)，根据灾难恢复需要复制到其他区域。
- [复制 Azure 虚拟机](site-recovery-azure-to-azure.md)，开始对工作负荷进行保护。
