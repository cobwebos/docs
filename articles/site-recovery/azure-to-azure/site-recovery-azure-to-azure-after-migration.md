---
title: "准备好计算机，以便在迁移到 Azure 后使用 Site Recovery 设置 Azure 区域之间的灾难恢复 | Microsoft Docs"
description: "本文介绍如何准备好计算机，以便在迁移到 Azure 后使用 Azure Site Recovery 设置 Azure 区域之间的灾难恢复。"
services: site-recovery
documentationcenter: 
author: ponatara
manager: abhemraj
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: ponatara
ms.openlocfilehash: 7658bedc0bd5c4a289f3271504a006ba54c783b6
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2018
---
# <a name="replicate-azure-vms-to-another-region-after-migration-to-azure-by-using-azure-site-recovery"></a>在迁移到 Azure 后，使用 Azure Site Recovery 将 Azure VM 复制到另一个区域

>[!NOTE]
> Azure 虚拟机 (VM) 的 Azure Site Recovery 复制当前处于预览阶段。

## <a name="overview"></a>概述

本文帮助准备 Azure 虚拟机，以便在这些虚拟机从本地环境迁移到 Azure 后，使用 Azure Site Recovery 在两个 Azure 区域之间进行复制。

## <a name="disaster-recovery-and-compliance"></a>灾难恢复和合规性
现在，越来越多的企业将其工作负荷移动到 Azure。 随着企业将任务关键型本地生产工作负荷移动到 Azure，为这些工作负荷设置灾难恢复既是合规性的强制要求，也是防止 Azure 区域中出现中断的需要。

## <a name="steps-for-preparing-migrated-machines-for-replication"></a>让迁移的计算机做好复制准备的步骤
若要让迁移的计算机为设置到另一个 Azure 区域的复制做好准备，请执行以下操作：

1. 完成迁移。
2. 根据需要安装 Azure 代理。
3. 删除移动服务。  
4. 重启 VM。

以下部分会更详细地介绍这些步骤。

### <a name="step-1-migrate-workloads-running-on-hyper-v-vms-vmware-vms-and-physical-servers-to-run-on-azure-vms"></a>步骤 1：迁移在 Hyper-V VM、VMware VM 和物理服务器上运行的工作负荷，使其在 Azure VM 上运行

若要设置复制并将本地 Hyper-V、VMware 和物理工作负荷迁移到 Azure，请按照[使用 Azure Site Recovery 在 Azure 区域之间迁移 Azure IaaS 虚拟机](site-recovery-migrate-azure-to-azure.md)一文中的步骤执行操作。 

迁移后，无需提交或删除故障转移。 相反，对于想要迁移的每台计算机，可选择“完成迁移”选项：
1. 在“复制的项”中，右键单击 VM，并单击“完成迁移”。 单击“确定”完成本步骤。 若要在 VM 属性中跟踪进度，可在“Site Recovery 作业”中监视“完成迁移”作业。
2. “完成迁移”操作会完成迁移过程、删除计算机复制设置及让计算机不再产生 Site Recovery 费用。

### <a name="step-2-install-the-azure-vm-agent-on-the-virtual-machine"></a>步骤 2：在虚拟机上安装 Azure VM 代理
必须在虚拟机上安装 Azure [VM 代理](../../virtual-machines/windows/classic/agents-and-extensions.md#azure-vm-agents-for-windows-and-linux)，Site Recovery 扩展才能运行及帮助保护 VM。

>[!IMPORTANT]
>从版本 9.7.0.0 开始，在 Windows 虚拟机上，移动服务安装程序还会安装最新可用的 Azure VM 代理。 迁移时，虚拟机需要满足使用所有 VM 扩展（包括 Site Recovery 扩展）的代理安装先决条件。 仅当迁移的计算机上安装的移动服务为版本 9.6 或更早版本时，才需要手动安装 Azure VM 代理。

下表提供有关安装 VM 代理及验证其是否已安装的其他信息：

| **操作** | **Windows** | **Linux** |
| --- | --- | --- |
| 安装 VM 代理 |下载并安装 [代理 MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 需要管理员权限才能完成安装。 |安装最新的 [Linux 代理](../../virtual-machines/linux/agent-user-guide.md)。 需要管理员权限才能完成安装。 我们建议从分发存储库安装代理。 我们*不建议*直接从 GitHub 安装 Linux VM 代理。  |
| 验证 VM 代理安装 |1.浏览到 Azure VM 中的 C:\WindowsAzure\Packages 文件夹。 此时应看到 WaAppAgent.exe 文件。 <br>2.右键单击该文件，转到“**属性**”，并选择“**详细信息**”选项卡。“产品版本”字段应为 2.6.1198.718 或更高版本。 |不适用 |


### <a name="step-3-remove-the-mobility-service-from-the-migrated-virtual-machine"></a>步骤 3：从迁移的虚拟机中删除移动服务

如果已迁移本地 VMware 虚拟机或 Windows/Linux 上的物理服务器，则需要从迁移的虚拟机中手动删除/卸载移动服务。

>[!IMPORTANT]
>对于迁移到 Azure 的 Hyper-V VM，无需执行此步骤。

#### <a name="uninstall-the-mobility-service-on-a-windows-server-vm"></a>卸载 Windows Server VM 上的移动服务
使用以下方法之一卸载 Windows Server 计算机上的移动服务。

##### <a name="uninstall-by-using-the-windows-ui"></a>使用 Windows UI 卸载
1. 在“控制面板”中，选择“程序”。
2. 选择“Microsoft Azure Site Recovery 移动服务/主目标服务器”，并单击“卸装”。

##### <a name="uninstall-at-a-command-prompt"></a>在命令提示符下卸载
1. 以管理员身份打开“命令提示符”窗口。
2. 若要卸载移动服务，请运行以下命令：

   ```
   MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
   ```

#### <a name="uninstall-the-mobility-service-on-a-linux-computer"></a>卸载 Linux 计算机上的移动服务
1. 在 Linux 服务器上，以**根**用户身份登录。
2. 在终端中，转到 /user/local/ASR。
3. 若要卸载移动服务，请运行以下命令：

   ```
   uninstall.sh -Y
   ```

### <a name="step-4-restart-the-vm"></a>步骤 4：重启 VM

卸载移动服务后，在设置到另一个 Azure 区域的复制前，重启 VM。


## <a name="next-steps"></a>后续步骤
- [复制 Azure 虚拟机](azure-to-azure-quickstart.md)，开始对工作负荷进行保护。
- 详细了解 [Azure 虚拟机复制网络指南](site-recovery-azure-to-azure-networking-guidance.md)。
