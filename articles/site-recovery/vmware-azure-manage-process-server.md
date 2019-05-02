---
title: 管理用于 VMware Vm 和物理服务器到 Azure 中使用 Azure Site Recovery 的灾难恢复的进程服务器 |Microsoft Docs
description: 本指南介绍了管理为 VMware Vm 和物理服务器到 Azure 中使用 Azure Site Recovery 的灾难恢复设置的进程服务器。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 2c27779719c73adf4d7fc1a61a0c77d03df71815
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925613"
---
# <a name="manage-process-servers"></a>管理进程服务器

本文介绍管理 Site Recovery 进程服务器的常见任务。

进程服务器用于接收、 优化和复制数据发送到 Azure。 它还执行推送安装移动服务的 VMware Vm 和物理服务器，你想要复制，并执行自动发现本地计算机上。 将本地 VMware Vm 或物理服务器复制到 Azure，默认情况下，在配置服务器计算机上安装进程服务器。 

- 对于大型部署，可能需要使用额外的本地进程服务器来扩展容量。
- 从 Azure 到本地的故障回复，必须设置 Azure 中的临时进程服务器。 故障回复完成后，可以删除此 VM。 

了解有关进程服务器的详细信息。


## <a name="upgrade-a-process-server"></a>升级进程服务器

当部署进程服务器在本地，或作为故障回复 Azure VM，安装进程服务器的最新版本。 Site Recovery 团队会定期发布修补程序和增强功能，因此我们建议你使进程服务器保持最新。 您可以将进程服务器升级，如下所示：

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>移动虚拟机以平衡进程服务器负载

按如下所示两个进程服务器之间移动虚拟机以平衡负载：

1. 在保管库下**管理**单击**Site Recovery 基础结构**。 下**适用于 VMware 和物理计算机**，单击**配置服务器**。
2. 单击与该注册进程服务器在配置服务器上。
3. 单击你想要流量进行负载平衡的进程服务器。

    ![LoadBalance](media/vmware-azure-manage-process-server/LoadBalance.png)

4. 单击**负载平衡**，选择你想要移动计算机的目标进程服务器。 然后单击**确定**

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. 单击**选择计算机**，然后选择想要将从当前移动到目标进程服务器的计算机。 针对每个虚拟机显示平均数据更改的详细信息。 然后单击“确定”。 
3. 在保管库中，监视下的作业的进度**监视** > **Site Recovery 作业**。

它将需要大约 15 分钟才会反映在门户中的更改。 要更快的效果，请[刷新配置服务器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。

## <a name="switch-an-entire-workload-to-another-process-server"></a>切换到另一个进程服务器的整个工作负荷

移动整个工作负荷处理不同的进程服务器，到了进程服务器，如下所示：

1. 在保管库下**管理**单击**Site Recovery 基础结构**。 下**适用于 VMware 和物理计算机**，单击**配置服务器**。
2. 单击与该注册进程服务器在配置服务器上。
3. 单击你想要将工作负荷切换的进程服务器。
4. 单击**交换机**，选择你想要移动工作负荷的目标进程服务器。 然后单击**确定**

    ![Switch](media/vmware-azure-manage-process-server/Switch.PNG)

5. 在保管库中，监视下的作业的进度**监视** > **Site Recovery 作业**。

它将需要大约 15 分钟才会反映在门户中的更改。 要更快的效果，请[刷新配置服务器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。



## <a name="reregister-a-process-server"></a>重新注册进程服务器

重新注册进程服务器在本地运行或与配置服务器，如下所示的 Azure VM 上：

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

保存设置后，请执行以下操作：

1. 在进程服务器上，打开管理员命令提示符。
2. 浏览到文件夹 **%PROGRAMDATA%\ASR\Agent**，并运行以下命令：

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>修改本地进程服务器的代理设置

如果在本地进程服务器使用代理连接到 Azure，你可以修改代理设置，如下所示：

1. 登录到进程服务器计算机。 
2. 打开管理员 PowerShell 命令窗口并运行以下命令：
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. 浏览到文件夹 **%PROGRAMDATA%\ASR\Agent**，然后运行以下命令：
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>删除进程服务器

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>从防病毒软件中排除文件夹

如果横向扩展进程服务器 （或主目标服务器） 上运行防病毒软件，请从防病毒软件操作中排除以下文件夹：


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- 进程服务器安装目录。 例如：C:\Program Files (x86)\Microsoft Azure Site Recovery