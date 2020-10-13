---
title: 使用 Azure Site Recovery 管理 VMware/物理服务器的移动代理
description: 管理可使用 Azure Site Recovery 将 VMware VM 和物理服务器灾难恢复到 Azure 的移动服务代理。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: d921bddf90c415cb244e2cc9ad98354392a537ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530142"
---
# <a name="manage-the-mobility-agent"></a>管理移动代理 

使用 Azure Site Recovery 进行从 VMware VM 和物理服务器到 Azure 的灾难恢复时，请在服务器上设置移动代理。 移动代理协调受保护计算机、配置服务器/横向扩展进程服务器之间的通信，并管理数据复制。 本文概述了在部署移动代理后对其进行管理时要执行的常见任务。

>[!TIP]
>若要下载特定 OS/Linux 发行版的安装程序，请参阅[此处](vmware-physical-mobility-service-overview.md#locate-installer-files)的指南。 若要从门户自动更新，则无需下载安装程序。 [ASR 会从配置服务器自动提取安装程序，并更新代理](#update-mobility-service-from-azure-portal)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>从 Azure 门户更新移动服务

1. 开始在受保护的计算机上更新移动服务之前，请确保部署中的配置服务器、横向扩展进程服务器及所有主目标服务器均已更新。
    1. 从9.36 版开始，对于 SUSE Linux Enterprise Server 11 SP3，RHEL 5，CentOS 5，Debian 7 确保 [配置服务器和横向扩展进程服务器上提供](vmware-physical-mobility-service-overview.md#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server)最新的安装程序。
1. 在门户中打开保管库 >“复制的项”。 
1. 如果配置服务器是最新版本，则会看到一条通知，指出“新的 Site Recovery 复制代理更新已可用。 单击可安装。”

     ![“复制的项”窗口](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. 单击该通知，并在“代理更新”中选择要在其上升级移动服务的计算机。   。

     ![“复制的项”VM 列表](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. 将为所选的每台计算机启动“更新移动服务”作业。 移动代理已更新到配置服务器的版本。 例如，如果配置服务器为版本 9.33，则受保护的 VM 上的移动代理也会更新为 9.33 版本。

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>在 Windows 服务器上通过 powershell 脚本更新移动服务

开始在受保护的计算机上更新移动服务之前，请确保部署中的配置服务器、横向扩展进程服务器及所有主目标服务器均已更新。

使用以下脚本通过 power shell cmdlet 更新服务器上的移动服务

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-mobility-service-manually-on-each-protected-server"></a>在每个受保护的服务器上手动更新移动服务

1. 开始在受保护的计算机上更新移动服务之前，请确保部署中的配置服务器、横向扩展进程服务器及所有主目标服务器均已更新。

2. 根据服务器的操作系统[找到代理安装程序](vmware-physical-mobility-service-overview.md#locate-installer-files)。

>[!IMPORTANT]
> 如果要在不同的 Azure 区域之间复制 Azure IaaS VM，请不要使用此方法。 有关所有可用选项的信息，请参阅 [我们的指南](azure-to-azure-autoupdate.md) 。

3. 将安装文件复制到受保护的计算机上，并运行该文件以更新移动代理。

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>更新用于推送安装移动服务的帐户

在部署 Site Recovery 时，为了启用移动服务的推送安装，你已指定一个帐户，供 Site Recovery 进程服务器在为计算机启用了复制时，用来访问计算机和安装服务。 若要更新此帐户的凭据，请遵照[这些说明](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)操作。

## <a name="uninstall-mobility-service"></a>卸载移动服务

### <a name="on-a-windows-machine"></a>在 Windows 计算机上

从 UI 或命令提示符卸载。

- **通过 UI**：在计算机的控制面板中，选择“程序”。  选择**Microsoft Azure Site Recovery 移动服务/主目标服务器**  >  **卸载**"。
- **通过命令提示符**：在计算机上以管理员身份打开命令提示符窗口。 运行以下命令： 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>在 Linux 计算机上
1. 在 Linux 计算机上以 **root** 用户身份登录。
2. 在终端中转到 /usr/local/ASR。
3. 运行以下命令：
    ```
    uninstall.sh -Y
   ```
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>在源计算机上安装 Site Recovery VSS 提供程序

源计算机上必须存在 Azure Site Recovery VSS 提供程序，否则无法生成应用程序一致性点。 如果通过推送安装来安装提供程序没有成功，请按下面的给定指南手动安装它。

1. 打开管理员 cmd 窗口。
2. 导航到移动服务安装位置。 （例如 C:\Program Files (x86)\Microsoft Azure Site Recovery\agent）
3. 运行 InMageVSSProvider_Uninstall.cmd 脚本。 这样会卸载该服务（如果已存在）。
4. 运行 InMageVSSProvider_Install.cmd 脚本，手动安装 VSS 提供程序。

## <a name="next-steps"></a>后续步骤

- [为 VMware VM 设置灾难恢复](vmware-azure-tutorial.md)
- [为物理服务器设置灾难恢复](physical-azure-disaster-recovery.md)
