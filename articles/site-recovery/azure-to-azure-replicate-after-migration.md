---
title: 使用 Azure Site Recovery 设置 Azure VM 迁移到 Azure 后的灾难恢复 | Microsoft Docs
description: 本文介绍如何准备好计算机，以便在迁移到 Azure 后使用 Azure Site Recovery 设置 Azure 区域之间的灾难恢复。
services: site-recovery
author: ponatara
ms.service: site-recovery
ms.topic: article
ms.date: 01/07/2018
ms.author: ponatara
ms.openlocfilehash: 034052c0406ee51f33c598634f92d27867c7cbe7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>设置 Azure VM 迁移到 Azure 后的灾难恢复 

>[!NOTE]
> 使用 Azure Site Recovery 的 Azure VM 灾难恢复当前处于预览状态。

使用 [Site Recovery](site-recovery-overview.md) 服务[将本地计算机迁移到 Azure VM](tutorial-migrate-on-premises-to-azure.md) 之后使用本文。 本文可帮助你准备 Azure VM，以便使用 Site Recovery 设置到 Azure 次要区域的灾难恢复。



## <a name="before-you-start"></a>开始之前

设置灾难恢复之前，请确保已按预期完成迁移。 若要成功完成迁移，在故障转移后，应为要迁移的每台计算机选择“完成迁移”选项。 



## <a name="install-the-azure-vm-agent"></a>安装 Azure VM 代理

Azure [VM 代理](../virtual-machines/extensions/agent-windows.md)必须安装在 VM 上,，以便 Site Recovery 可复制它。


1. 若要在运行 Windows 的 VM 上安装 VM 代理，请下载并运行[代理安装程序](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 需要在 VM 上有管理员权限才能完成安装。
2. 若要在运行 Linux 的 VM 上安装 VM 代理，请安装最新 [Linux 代理](../virtual-machines/extensions/agent-linux.md)。 需要管理员权限才能完成安装。 我们建议从分发存储库安装。 我们不建议直接从 GitHub 安装 Linux VM 代理。 


## <a name="validate-the-installation-on-windows-vms"></a>在 Windows VM 上验证安装

1. 在 Azure VM 上的 C:\WindowsAzure\Packages 文件夹中，应看到 WaAppAgent.exe 文件。
2. 右键单击该文件，在“属性”中选择“详细信息”选项卡。
3. 验证“产品版本”字段是否显示 2.6.1198.718 或更高版本。



## <a name="migration-from-vmware-vms-or-physical-servers"></a>从 VMware VM 或物理服务器迁移

如果将本地 VMware VM（或物理服务器）迁移到 Azure，请注意：

- 仅当迁移计算机上安装的移动服务为 9.6 版或更早版本时，才需要安装 Azure VM 代理。
- 在运行移动服务 9.7.0.0 版或更高版本的 Windows VM 上，服务安装程序将安装最新可用的 Azure VM 代理。 迁移时，这些 VM 已满足所有 VM 扩展（包括 Site Recovery 扩展）的代理安装先决条件。
- 需要使用以下方法之一从 Azure VM 上手动卸载移动服务。 在配置复制之前，请重新启动 VM。
    - 对于 Windows，在控制面板中 >“添加/删除程序”，卸载“Microsoft Azure Site Recovery 移动服务/主目标服务器”。 在提升的命令提示符下，运行：
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - 对于 Linux，以根用户登录。 在终端中，转到 **/user/local/ASR**，运行以下命令：
        ```
        uninstall.sh -Y
        ```


## <a name="next-steps"></a>后续步骤

将 Azure VM [快速复制](azure-to-azure-quickstart.md)到次要区域。
