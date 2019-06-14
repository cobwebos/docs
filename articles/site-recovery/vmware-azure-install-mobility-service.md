---
title: 准备源计算机来安装移动服务通过推送安装的 VMware Vm 灾难恢复和物理服务器到 Azure |Microsoft Docs
description: 了解如何准备你的服务器安装移动代理通过推送安装的 VMware Vm 灾难恢复和物理服务器到 Azure 中使用 Azure Site Recovery 服务。
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 628be573d03d42ec62a358071074facfe228852d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60318153"
---
# <a name="prepare-source-machine-for-push-installation-of-mobility-agent"></a>准备推送安装移动代理的源计算机

在使用 [Azure Site Recovery](site-recovery-overview.md) 设置 VMware VM 和物理服务器的灾难恢复时，可在每个本地 VMware VM 和物理服务器上安装 [Site Recovery 移动服务](vmware-physical-mobility-service-overview.md)。  移动服务可以捕获计算机上的数据写入，并将其转发到 Site Recovery 进程服务器。

## <a name="install-on-windows-machine"></a>在 Windows 计算机上安装

在要保护的每台 Windows 计算机上，执行以下操作：

1. 确保计算机与进程服务器之间已建立网络连接。 如果尚未设置单独的进程服务器，则默认情况下它在配置服务器上运行。
1. 创建可供进程服务器用来访问计算机的帐户。 该帐户应具有管理员权限（本地或域）。 只能将此帐户用于推送安装和代理更新。
2. 如果未使用域帐户，请在本地计算机上禁用远程用户访问控制，如下所述：
    - 在 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 注册表项下，添加一个新的 DWORD：LocalAccountTokenFilterPolicy。  将值设置为 **1**。
    -  为此，请在命令提示符下运行以下命令：  
   `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d
3. 在要保护的计算机上的 Windows 防火墙中，选择“允许应用或功能通过防火墙”。  启用“文件和打印机共享”和“Windows Management Instrumentation (WMI)”。   对于属于某个域的计算机，可以使用组策略对象 (GPO) 配置防火墙设置。

   ![防火墙设置](./media/vmware-azure-install-mobility-service/mobility1.png)

4. 添加在 CSPSConfigtool 中创建的帐户。 若要执行此操作，请登录到你的配置服务器。
5. 打开 **cspsconfigtool.exe**。 桌面上有该工具的快捷方式，也可以在 %ProgramData%\home\svsystems\bin 文件夹中找到它。
6. 在“管理帐户”选项卡中，选择“添加帐户”。  
7. 添加已创建的帐户。
8. 输入为计算机启用复制时使用的凭据。

## <a name="install-on-linux-machine"></a>在 Linux 计算机上安装

在要保护的每台 Linux 计算机上，执行以下操作：

1. 确保 Linux 计算机与进程服务器之间已建立网络连接。
2. 创建可供进程服务器用来访问计算机的帐户。 帐户应该是源 Linux 服务器上的 **root** 用户。 只能将此帐户用于推送安装和更新。
3. 确保源 Linux 服务器上的 /etc/hosts 文件包含用于将本地主机名映射到所有网络适配器关联的 IP 地址的条目。
4. 在要复制的计算机上安装最新的 openssh、openssh-server 和 openssl 包。
5. 确保安全外科 (SSH) 已启用且正在端口 22 上运行。
4. 在 sshd_config 文件中启用 SFTP 子系统和密码身份验证。 为此，请以 **root** 身份登录。
5. 在 **/etc/ssh/sshd_config** 文件中，找到以“PasswordAuthentication”  开头的行。
6. 取消注释该行，并将值更改为 **yes**。
7. 找到以“Subsystem”  开头的行，并取消注释该行。

      ![Linux](./media/vmware-azure-install-mobility-service/mobility2.png)

8. 重启 sshd 服务。 
9. 添加在 CSPSConfigtool 中创建的帐户。 若要执行此操作，请登录到你的配置服务器。
10. 打开 **cspsconfigtool.exe**。 桌面上有该工具的快捷方式，也可以在 %ProgramData%\home\svsystems\bin 文件夹中找到它。
11. 在“管理帐户”选项卡中，选择“添加帐户”。  
12. 添加已创建的帐户。
13. 输入为计算机启用复制时使用的凭据。

## <a name="anti-virus-on-replicated-machines"></a>复制计算机上的防病毒

如果要复制的计算机上正在运行活动的防病毒软件，请确保从防病毒操作中排除移动服务安装文件夹 (*C:\ProgramData\ASR\agent*)。 这可确保复制正常工作。

## <a name="next-steps"></a>后续步骤

安装移动服务后，在 Azure 门户中选择“+复制”以开始保护这些 VM。  详细了解如何为 [VMware VM(vmware-azure-enable-replication.md) 和[物理服务器](physical-azure-disaster-recovery.md#enable-replication)启用复制。


