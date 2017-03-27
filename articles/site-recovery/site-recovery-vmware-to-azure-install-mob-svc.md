---
title: "安装移动服务（VMware/物理到 Azure）| Microsoft 文档"
description: "本文介绍如何在本地计算机上安装移动服务代理以开始保护计算机。"
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 2/20/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 7e82ac74a8aef4e3cc8aff4dea3c572dcb9d9c40
ms.lasthandoff: 03/03/2017

---

# <a name="installing-mobility-service-vmwarephysical-to-azure"></a>安装移动服务（VMware/物理到 Azure）
需要将移动服务部署到要复制到 Azure 的每个计算机（VMware VM 或物理服务器）上。 它可以捕获计算机上的数据写入，并将其转发到进程服务器。 可以使用以下方法将移动服务部署到需要保护的服务器上


1. [使用 System Center Configuration Manager 等软件部署工具安装移动服务](site-recovery-install-mobility-service-using-sccm.md)
2. [使用 Azure 自动化和 Desired State Configuration (DSC) 安装移动服务](site-recovery-automate-mobility-service-install.md)
3. [使用图形用户界面 (GUI) 手动安装移动服务](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-using-the-graphical-user-interface)
4. [使用命令行手动安装移动服务](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-using-command-line)
5. [使用推送安装从 Azure Site Recovery 安装移动服务](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-using-push-install-from-azure-site-recovery)


>[!IMPORTANT]
> 从版本 9.7.0.0 开始，在 Windows 虚拟机上，移动服务安装程序还将安装最新可用的 [Azure VM 代理](../virtual-machines/virtual-machines-windows-extensions-features.md#azure-vm-agent)。 这可确保当计算机故障转移到 Azure 时，它满足使用任何 VM 扩展所需的此先决条件。

## <a name="prerequisites"></a>先决条件
开始在服务器上手动安装移动服务之前，请执行这些先决条件。
1. 登录到配置服务器，并使用管理权限打开命令提示符。
2. 将目录更改到 bin 文件夹并创建一个密码文件

  ```
  cd %ProgramData%\ASR\home\svsystems\bin
  genpassphrase.exe -v > MobSvc.passphrase
  ```
3. 将该文件存储在安全位置中，因为在移动服务安装过程中将需要使用它。
4. 支持的所有操作系统的移动服务安装程序均可在该目录下找到     

  `%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

#### <a name="mobility-service-installer-to-operating-system-mapping"></a>移动服务安装程序到操作系统的映射

| 安装程序文件模板名称| 操作系统 |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2008 R2（64 位）SP1</br> Windows Server 2012（64 位） </br> Windows Server 2012 R2（64 位） |
|Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz| RHEL 6.4、6.5、6.6、6.7、6.8（仅 64 位） </br> CentOS 6.4、6.5、6.6、6.7。 6.8（仅&64; 位） |
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3（仅限 64 位）|
|Microsoft-ASR_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4、6.5（仅限 64 位）|


## <a name="install-mobility-service-manually-using-the-graphical-user-interface"></a>使用图形用户界面手动安装移动服务

>[!NOTE]
> 仅 Microsoft Windows 操作系统支持基于图形用户界面的安装。

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-using-command-line"></a>使用命令行手动安装移动服务
### <a name="command-line-based-install-on-windows-computers"></a>Windows 计算机上基于命令行的安装
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-based-install-on-linux-computers"></a>Linux 计算机上基于命令行的安装
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-using-push-install-from-azure-site-recovery"></a>使用推送安装从 Azure Site Recovery 安装移动服务
若要能够使用 Azure Site Recovery 执行移动服务的推送安装，需要确保所有目标计算机都满足以下先决条件。

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


> [!NOTE]
安装移动服务后，可以使用 Azure 门户中的“+复制”按钮开始为这些 VM 启用保护。

## <a name="uninstall-mobility-service-on-windows-servers"></a>卸载 Windows Server 上的移动服务
可通过两种方法卸载 Windows Server 上的移动服务

### <a name="uninstall-using-graphical-user-interface"></a>使用图形用户界面卸载
1. 打开“控制面板”>“程序”
2. 选择“Microsoft Azure Site Recovery 移动服务/主目标服务器”并单击“卸装”。

### <a name="uninstall-using-command-line"></a>使用命令行卸载
1. 打开管理员命令提示符
2. 运行以下命令以卸载移动服务。

```
MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
```

## <a name="uninstall-mobility-service-on-linux-computers"></a>卸载 Linux 计算机上的移动服务
1. 以 **ROOT** 身份在 Linux 服务器上登录
2. 在**终端**中浏览到 /user/local/ASR
3. 运行以下命令以卸载移动服务

```
uninstall.sh -Y
```

