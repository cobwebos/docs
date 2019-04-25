---
title: 使用 Azure Site Recovery 管理进程服务器，以便将 VMware VM 和物理服务器灾难恢复到 Azure | Microsoft Docs
description: 本文介绍使用 Azure Site Recovery 管理为 VMware VM 和物理服务器灾难恢复到 Azure 而设置的进程服务器。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: ramamill
ms.openlocfilehash: ba80c8ce57495eaa46e915cb0c472eb4aabcee57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60318504"
---
# <a name="manage-process-servers"></a>管理进程服务器

默认情况下，将 VMware VM 或物理服务器复制到 Azure 时使用的进程服务器安装在本地配置服务器计算机上。 需要在几个实例中设置单独的进程服务器：

- 对于大型部署，可能需要使用额外的本地进程服务器来扩展容量。
- 对于故障回复，需要在 Azure 中设置临时进程服务器。 故障回复完成后，可以删除此 VM。 

本文汇总了这些额外的进程服务器的典型管理任务。

## <a name="upgrade-a-process-server"></a>升级进程服务器

升级在本地或 Azure 中运行的进程服务器（用于故障回复），如下所示：

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

> [!NOTE]
>   通常，使用 Azure 库映像在 Azure 中创建用于故障回复的进程服务器时，该进程服务器运行最新的可用版本。 Site Recovery 团队会定期发布修补程序和增强功能，因此我们建议你使进程服务器保持最新。

## <a name="balance-the-load-on-process-server"></a>均衡进程服务器上的负载

要均衡两个进程服务器之间的负载，

1. 导航至“恢复服务保管库” > “管理” > “站点恢复基础结构” > “对于 VMware 和物理机” > “配置服务器”。
2. 单击进程服务器注册到的配置服务器。
3. 页面上列出了注册到该配置服务器的进程服务器。
4. 单击要修改其工作负载的进程服务器。

    ![LoadBalance](media/vmware-azure-manage-process-server/LoadBalance.png)

5. 可根据需要使用“负载均衡”或“切换”选项，如下所述。

### <a name="load-balance"></a>负载均衡

通过此选项，可选择一个或多个虚拟机，并可将它们传输到另一个进程服务器。

1. 单击“负载均衡”，从下拉列表中选择目标进程服务器。 单击 **“确定”**

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. 单击“选择计算机”，选择要从当前进程服务器移动到目标进程服务器的虚拟机。 针对每个虚拟机显示平均数据更改的详细信息。
3. 单击“确定”。 在“恢复服务保管库” > “监控” > “站点恢复作业”下监控作业进度。
4. 此操作成功完成后，需要 15 分钟才能反映所做更改，或者可以[刷新配置服务器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)使更改立即生效。

### <a name="switch"></a>Switch

通过此选项，受进程服务器保护的整个工作负载将移动到另一个进程服务器。

1. 单击“切换”，选择目标进程服务器，然后单击“确认”。

    ![Switch](media/vmware-azure-manage-process-server/Switch.PNG)

2. 在“恢复服务保管库” > “监控” > “站点恢复作业”下监控作业进度。
3. 此操作成功完成后，需要 15 分钟才能反映所做更改，或者可以[刷新配置服务器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)使更改立即生效。

## <a name="reregister-a-process-server"></a>重新注册进程服务器

如果需要将在本地或 Azure 中运行的进程服务器重新注册到配置服务器中，请执行以下操作：

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

如果在进程服务器使用代理连接到 Azure 中的 Site Recovery 的情况下需要修改现有代理设置，请使用此过程。

1. 登录到进程服务器计算机。 
2. 打开管理员 PowerShell 命令窗口并运行以下命令：
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. 浏览到文件夹 **%PROGRAMDATA%\ASR\Agent**，并运行以下命令：
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```


## <a name="remove-a-process-server"></a>删除进程服务器

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="manage-anti-virus-software-on-process-servers"></a>管理进程服务器上的防病毒软件

如果防病毒软件在独立进程服务器或主目标服务器上处于活动状态，请从防病毒操作中排除以下文件夹：


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- 进程服务器安装目录，示例：C:\Program Files (x86)\Microsoft Azure Site Recovery

