---
title: 关于使用 Azure Site Recovery 对 VMware VM 和物理服务器进行灾难恢复的移动服务 | Microsoft Docs
description: 了解可使用 Azure Site Recovery 将 VMware VM 和物理服务器灾难恢复到 Azure 的移动服务代理。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: e3967319cd41399209bd50886bce88efc8ba6ba6
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956509"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>关于适用于 VMware VM 和物理服务器的移动服务

在使用 [Azure Site Recovery](site-recovery-overview.md) 设置 VMware VM 和物理服务器的灾难恢复时，可在每个本地 VMware VM 和物理服务器上安装 Site Recovery 移动服务。  移动服务可以捕获计算机上的数据写入，并将其转发到 Site Recovery 进程服务器。 可以使用下述方法部署移动服务：

[推送安装](vmware-azure-install-mobility-service.md)：将 Site Recovery 配置为执行移动服务的推送安装：为此，请在设置灾难恢复的同时设置一个帐户，供 Site Recovery 进程服务器用来访问 VM 或物理服务器以安装该服务。
[手动安装](vmware-physical-mobility-service-install-manual.md)：可以使用 UI 或命令提示符在每台计算机上手动安装移动服务。
[自动部署](vmware-azure-mobility-install-configuration-mgr.md)：可以使用 System Center Configuration Manager 等软件部署工具自动完成安装。

## <a name="azure-virtual-machine-agent"></a>Azure 虚拟机代理

- **Windows VM**：从移动服务版本 9.7.0.0 开始，移动服务安装程序会安装 [Azure VM 代理](../virtual-machines/extensions/features-windows.md#azure-vm-agent)。 这可以确保当计算机故障转移到 Azure 时，Azure VM 满足使用任何 VM 扩展相关的代理安装先决条件。
- **Linux VM**：故障转移后，必须在 Azure VM 上手动安装 [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent)。

## <a name="installer-files"></a>安装程序文件

下表汇总了每个 VMware VM 和物理服务器操作系统的安装程序文件。 在开始之前，可以查看[支持的操作系统](vmware-physical-azure-support-matrix.md#replicated-machines)。


**安装程序文件** | **操作系统（仅限 64 位）** 
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016；Windows Server 2012 R2；Windows Server 2012；Windows Server 2008 R2 SP1 
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6。* </br> CentOS 6。*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7。* </br> CentOS 7。* 
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1、SP2、SP3 
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4、6.5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16.04 LTS 服务器
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7 
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="anti-virus-on-replicated-machines"></a>复制计算机上的防病毒

如果要复制的计算机上正在运行活动的防病毒软件，请确保从防病毒操作中排除移动服务安装文件夹 (*C:\ProgramData\ASR\agent*)。 这可确保复制正常工作。

## <a name="update-the-mobility-service"></a>更新移动服务

1. 开始在受保护的计算机上更新移动服务之前，请确保部署中的配置服务器、横向扩展进程服务器及所有主目标服务器均已更新。
2. 在门户中打开保管库 >“复制的项”。
3. 如果配置服务器是最新版本，则会看到一条通知，指出“新的 Site Recovery 复制代理更新已可用。 单击可安装。”

     ![“复制的项”窗口](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. 单击该通知，并在“代理更新”中选择要在其上升级移动服务的计算机。 然后单击“确定”。

     ![“复制的项”VM 列表](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. 将为所选的每台计算机启动“更新移动服务”作业。

## <a name="update-the-acount-used-for-push-installation-of-the-mobility-service"></a>更新用于 Mobility Service 推送安装的帐户

在部署 Site Recovery 时，为了启用移动服务的推送安装，你已指定一个帐户，供 Site Recovery 进程服务器在为计算机启用了复制时，用来访问计算机和安装服务。 若要更新此帐户的凭据，请遵照[这些说明](vmware-azure-manage-configuration-server.md)操作。

## <a name="uninstall-the-mobility-service"></a>卸载移动服务

### <a name="on-a-windows-machine"></a>在 Windows 计算机上

从 UI 或命令提示符卸载。

- **通过 UI**：在计算机的控制面板中，选择“程序”。 选择“Microsoft Azure Site Recovery 移动服务/主目标服务器” > “卸载”。
- **通过命令提示符**：在计算机上以管理员身份打开命令提示符窗口。 运行以下命令： 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

## <a name="on-a-linux-machine"></a>在 Linux 计算机上
1. 在 Linux 计算机上以 **root** 用户身份登录。
2. 在终端中，转到 /user/local/ASR。
3. 运行以下命令：

    ```
    uninstall.sh -Y
    ```

## <a name="next-steps"></a>后续步骤

[设置移动服务的推送安装](vmware-azure-install-mobility-service.md)。
