---
title: 管理服务器的 VMware Vm 灾难恢复和 Azure Site Recovery 使用的物理服务器上的移动代理 |Microsoft Docs
description: 管理的 VMware Vm 和物理服务器到 Azure 中使用 Azure Site Recovery 服务灾难恢复的移动服务代理。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 69b8e1c533747d1bade69949911ea43f299f49e9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59794229"
---
# <a name="manage-mobility-agent-on-protected-machines"></a>管理受保护计算机上的移动代理

你设置移动代理在服务器上使用 Azure Site Recovery 进行 VMware Vm 和物理服务器到 Azure 的灾难恢复时。 移动代理在受保护的计算机，配置服务器/扩展进程服务器之间协调通信并管理数据复制。 本文汇总了部署后管理移动代理的常见任务。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>从 Azure 门户更新移动服务

1. 开始在受保护的计算机上更新移动服务之前，请确保部署中的配置服务器、横向扩展进程服务器及所有主目标服务器均已更新。
2. 在门户中打开保管库 >“复制的项”。
3. 如果配置服务器是最新版本，则会看到一条通知，指出“新的 Site Recovery 复制代理更新已可用。 单击可安装。”

     ![“复制的项”窗口](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. 单击该通知，并在“代理更新”中选择要在其上升级移动服务的计算机。 然后单击“确定”。

     ![“复制的项”VM 列表](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. 将为所选的每台计算机启动“更新移动服务”作业。

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>在 Windows 服务器上通过 powershell 脚本更新移动服务

使用以下脚本通过 power shell cmdlet 更新服务器上的移动服务

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>更新用于推送安装移动服务的帐户

在部署 Site Recovery 时，为了启用移动服务的推送安装，你已指定一个帐户，供 Site Recovery 进程服务器在为计算机启用了复制时，用来访问计算机和安装服务。 若要更新此帐户的凭据，请遵照[这些说明](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)操作。

## <a name="uninstall-mobility-service"></a>卸载移动服务

### <a name="on-a-windows-machine"></a>在 Windows 计算机上

从 UI 或命令提示符卸载。

- **通过 UI**：在计算机的控制面板中，选择“程序”。 选择“Microsoft Azure Site Recovery 移动服务/主目标服务器” > “卸载”。
- **通过命令提示符**：在计算机上以管理员身份打开命令提示符窗口。 运行以下命令： 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>在 Linux 计算机上
1. 在 Linux 计算机上以 **root** 用户身份登录。
2. 在终端中，转到 /user/local/ASR。
3. 运行以下命令：
    ```
    uninstall.sh -Y

## Install Site Recovery VSS provider on source machine

Azure Site Recovery VSS provider is required on the source machine to generate application consistency points. If the installation of the provider didn't succeed through push installation, follow the below given guidelines to install it manually.

1. Open admin cmd window.
2. Navigate to the mobility service installation location. (Eg - C:\Program Files (x86)\Microsoft Azure Site Recovery\agent)
3. Run the script InMageVSSProvider_Uninstall.cmd . This will uninstall the service if it already exists.
4. Run the script InMageVSSProvider_Install.cmd to install the VSS provider manually.

## Next steps

- [Set up disaster recovery for VMware VMs](vmware-azure-tutorial.md)
- [Set up disaster recovery for physical servers](physical-azure-disaster-recovery.md)
