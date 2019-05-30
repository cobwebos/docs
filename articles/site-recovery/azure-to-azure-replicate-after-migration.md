---
title: 使用 Azure Site Recovery 设置 Azure VM 迁移到 Azure 后的灾难恢复 | Microsoft Docs
description: 本文介绍如何准备好计算机，以便在迁移到 Azure 后使用 Azure Site Recovery 设置 Azure 区域之间的灾难恢复。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 4b764c8e7c3d97ff521add05033265f705c4136f
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399519"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>设置 Azure VM 迁移到 Azure 后的灾难恢复 


如果你已将遵循此篇文章[迁移到 Azure Vm 的本地计算机](tutorial-migrate-on-premises-to-azure.md)使用[Site Recovery](site-recovery-overview.md)服务，并且现在想要获取 Vm 设置灾难恢复到辅助 Azure 区域。 本文介绍如何确保已迁移的 Vm 上安装了 Azure VM 代理以及如何删除已迁移后不再需要 Site Recovery 移动服务。



## <a name="verify-migration"></a>验证迁移

设置灾难恢复之前，请确保已按预期完成迁移。 若要成功完成迁移，在故障转移后，应为要迁移的每台计算机选择“完成迁移”  选项。 

## <a name="verify-the-azure-vm-agent"></a>验证 Azure VM 代理

每个 Azure VM 必须具有[Azure VM 代理](../virtual-machines/extensions/agent-windows.md)安装。 若要将 Azure Vm 复制，Site Recovery 在代理上安装扩展。

- 如果计算机正在运行版本 9.7.0.0 开始或更高版本的 Site Recovery 移动服务，Azure VM 代理将自动安装的 Windows Vm 上的移动服务。 在早期版本的移动服务，需要自动安装代理。
- 对于 Linux Vm，必须手动安装 Azure VM 代理。只需安装 Azure VM 代理，如果迁移的计算机上安装移动服务为 9.6 版或更早版本。


### <a name="install-the-agent-on-windows-vms"></a>Windows Vm 上安装代理

如果您运行 Site Recovery 移动服务 9.7.0.0 开始，之前的版本或其他需要手动安装代理，请执行以下操作：  

1. 请确保在 VM 上具有管理员权限。
2. 下载[VM 代理安装程序](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。
3. 运行安装程序文件。

#### <a name="validate-the-installation"></a>验证安装
若要检查已安装代理：

1. 在 Azure VM 上的 C:\WindowsAzure\Packages 文件夹中，应看到 WaAppAgent.exe 文件。
2. 右键单击该文件，在“属性”  中选择“详细信息”  选项卡。
3. 验证“产品版本”  字段是否显示 2.6.1198.718 或更高版本。

[了解详细信息](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)有关 Windows 的代理安装。

### <a name="install-the-agent-on-linux-vms"></a>在 Linux Vm 上安装代理

安装[Azure Linux VM](../virtual-machines/extensions/agent-linux.md)代理手动，如下所示：

1. 请确保在计算机上具有管理员权限。
2. 我们强烈建议您安装 Linux VM 代理从分发的包存储库使用 RPM 或 DEB 包。 所有[认可的分发版提供商](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)会将 Azure Linux 代理包集成到其映像和存储库。
    - 我们强烈建议更新只通过分发存储库代理。
    - 我们不建议直接从 GitHub 安装 Linux VM 代理和更新它。
    -  如果分发没有可用的最新代理，请联系分发支持部门，了解如何安装最新代理。 

#### <a name="validate-the-installation"></a>验证安装 

1. 运行以下命令： **ps-e**以确保 Azure 代理在 Linux VM 上运行。
2. 如果该进程未运行，请使用以下命令进行重启：
    - 对于 Ubuntu:**服务 walinuxagent 开始**
    - 对于其他分发版： **service waagent start**


## <a name="uninstall-the-mobility-service"></a>卸载移动服务

1. 从 Azure VM，使用以下方法之一手动卸载移动服务。 
    - 对于 Windows，在控制面板中 >“添加/删除程序”  ，卸载“Microsoft Azure Site Recovery 移动服务/主目标服务器”  。 在提升的命令提示符下，运行：
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - 对于 Linux，以根用户身份登录。 在终端中，转到 **/user/local/ASR**，运行以下命令：
        ```
        ./uninstall.sh -Y
        ```
2. 在配置复制之前，请重新启动 VM。

## <a name="next-steps"></a>后续步骤

[查看故障排除](site-recovery-extension-troubleshoot.md)为 Azure VM 代理上的 Site Recovery 扩展。
将 Azure VM [快速复制](azure-to-azure-quickstart.md)到次要区域。
