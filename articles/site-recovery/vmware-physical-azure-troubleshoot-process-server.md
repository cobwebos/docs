---
title: 排查 Azure Site Recovery 进程服务器问题
description: 本文介绍如何解决 Azure Site Recovery 进程服务器的问题
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 812cd0293f9627b7438e9870d8985e71dae1d147
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256856"
---
# <a name="troubleshoot-the-process-server"></a>进程服务器故障排除

为本地 VMware Vm 和物理服务器设置到 Azure 的灾难恢复时，将使用[Site Recovery](site-recovery-overview.md)进程服务器。 本文介绍如何排查进程服务器的问题，包括复制和连接问题。

[了解](vmware-physical-azure-config-process-server-overview.md)有关进程服务器的详细信息。

## <a name="before-you-start"></a>开始之前

开始故障排除之前：

1. 请确保了解如何[监视进程服务器](vmware-physical-azure-monitor-process-server.md)。
2. 请查看下面的最佳实践。
3. 请确保遵循[容量注意事项](site-recovery-plan-capacity-vmware.md#capacity-considerations)，并使用[配置服务器](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server)或[独立进程服务器](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)的大小调整指南。

## <a name="best-practices-for-process-server-deployment"></a>进程服务器部署的最佳做法

为了获得进程服务器的最佳性能，我们总结了一些常规的最佳实践。

**最佳做法** | **详细信息**
--- |---
**使用情况** | 请确保配置服务器/独立进程服务器仅用于预期目的。 不要在计算机上运行任何其他操作。
IP 地址 | 请确保进程服务器具有静态 IPv4 地址，但未配置 NAT。
**控制内存/CPU 使用情况** |将 CPU 和内存使用率保持在70% 下。
**确保可用空间** | 可用空间指的是进程服务器上的缓存磁盘空间。 复制数据在上载到 Azure 之前存储在缓存中。<br/><br/> 将可用空间保留在25% 以上。 如果低于20%，则会限制与进程服务器关联的复制计算机的复制。

## <a name="check-process-server-health"></a>检查进程服务器运行状况

故障排除的第一步是检查进程服务器的运行状况和状态。 为此，请查看所有警报，检查所需服务是否正在运行，并验证是否有来自进程服务器的检测信号。 下图对这些步骤进行了概述，后面是可帮助你执行这些步骤的过程。

![解决进程服务器运行状况问题](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>步骤1：解决进程服务器运行状况警报问题

进程服务器生成多个运行状况警报。 下表总结了这些警报和建议的操作。

**警报类型** | **错误** | **故障排除**
--- | --- | --- 
![正常][green] | 无  | 进程服务器已连接并且运行正常。
![警告][yellow] | 指定的服务未运行。 | 1. 检查服务是否正在运行。<br/> 2. 如果服务按预期运行，请按照下面的说明进行操作，以[解决连接和复制问题](#check-connectivity-and-replication)。
![警告][yellow]  | 过去15分钟 > 80% 的 CPU 使用率。 | 1. 不要添加新计算机。<br/>2. 检查使用进程服务器的 Vm 数目是否符合[定义的限制](site-recovery-plan-capacity-vmware.md#capacity-considerations)，并考虑设置[其他进程服务器](vmware-azure-set-up-process-server-scale.md)。<br/>3. 请按照下面的说明进行操作[，以解决连接和复制问题](#check-connectivity-and-replication)。
![严重][red] |  过去15分钟 > 95% 的 CPU 使用率。 | 1. 不要添加新计算机。<br/>2. 检查使用进程服务器的 Vm 数目是否符合[定义的限制](site-recovery-plan-capacity-vmware.md#capacity-considerations)，并考虑设置[其他进程服务器](vmware-azure-set-up-process-server-scale.md)。<br/>3. 请按照下面的说明进行操作[，以解决连接和复制问题](#check-connectivity-and-replication)。<br/> 4. 如果此问题仍然存在，请运行用于 VMware/物理服务器复制的[部署规划器](https://aka.ms/asr-v2a-deployment-planner)。
![警告][yellow] | 过去15分钟 > 80% 的内存使用率。 |  1. 不要添加新计算机。<br/>2. 检查使用进程服务器的 Vm 数目是否符合[定义的限制](site-recovery-plan-capacity-vmware.md#capacity-considerations)，并考虑设置[其他进程服务器](vmware-azure-set-up-process-server-scale.md)。<br/>3. 按照与该警告关联的任何说明进行操作。<br/> 4. 如果问题仍然存在，请按照下面的说明进行操作，以[解决连接和复制问题](#check-connectivity-and-replication)。
![严重][red] | 过去15分钟 > 95% 的内存使用率。 | 1. 不要添加新计算机，并考虑设置[其他进程服务器](vmware-azure-set-up-process-server-scale.md)。<br/> 2. 按照与该警告关联的任何说明进行操作。<br/> 3. 4. 如果此问题仍然存在，请按照下面的说明进行操作，以[解决连接和复制问题](#check-connectivity-and-replication)。<br/> 4. 如果此问题仍然存在，请运行用于 VMware/物理服务器复制问题的[部署规划器](https://aka.ms/asr-v2a-deployment-planner)。
![警告][yellow] | 缓存文件夹可用空间 < 30% 过去15分钟。 | 1. 不要添加新计算机，并考虑设置附加的[进程服务器](vmware-azure-set-up-process-server-scale.md)。<br/>2. 检查使用进程服务器的 Vm 数目是否与[准则](site-recovery-plan-capacity-vmware.md#capacity-considerations)相符。<br/> 3. 请按照下面的说明进行操作[，以解决连接和复制问题](#check-connectivity-and-replication)。
![严重][red] |  过去15分钟 < 25% 的可用空间 | 1. 按照与此问题相关的警告的说明进行操作。<br/> 2. 3。 请按照下面的说明进行操作[，以解决连接和复制问题](#check-connectivity-and-replication)。<br/> 3. 如果此问题仍然存在，请运行用于 VMware/物理服务器复制的[部署规划器](https://aka.ms/asr-v2a-deployment-planner)。
![严重][red] | 在15分钟或更长时间内没有来自进程服务器的检测信号。 Tmansvs 服务未与配置服务器通信。 | 1）检查进程服务器是否已启动并正在运行。<br/> 2. 检查 tmassvc 是否正在进程服务器上运行。<br/> 3. 请按照下面的说明进行操作[，以解决连接和复制问题](#check-connectivity-and-replication)。


![表键](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>步骤2：检查进程服务器服务

下表总结了应在进程服务器上运行的服务。 服务略有不同，具体取决于进程服务器的部署方式。 

对于除 Microsoft Azure 恢复服务代理（obengine）以外的所有服务，请检查 StartType 是否设置为 "**自动**" 或 "**自动（延迟启动）** "。
 
**部署** | **正在运行服务**
--- | ---
**配置服务器上的进程服务器** | Scaleout-processserverProcessServerMonitor;cxprocessserverInMage PushInstall;日志上载服务（LogUpload）;InMage Scout 应用程序服务;Microsoft Azure 恢复服务代理（obengine）;InMage Scout VX Agent-Sentinel/Outpost （svagents）;tmansvcWorld Wide Web 发布服务（W3SVC）;MySQLMicrosoft Azure Site Recovery 服务（dra）
**作为独立服务器运行的进程服务器** | Scaleout-processserverProcessServerMonitor;cxprocessserverInMage PushInstall;日志上载服务（LogUpload）;InMage Scout 应用程序服务;Microsoft Azure 恢复服务代理（obengine）;InMage Scout VX Agent-Sentinel/Outpost （svagents）;tmansvc.
**部署在 Azure 中进行故障回复的进程服务器** | Scaleout-processserverProcessServerMonitor;cxprocessserverInMage PushInstall;日志上载服务（LogUpload）


## <a name="step-3-check-the-process-server-heartbeat"></a>步骤3：检查进程服务器检测信号

如果没有来自进程服务器的检测信号（错误代码806），请执行以下操作：

1. 验证进程服务器 VM 是否已启动并正在运行。
2. 检查这些日志中是否有错误。

    C:\ProgramData\ASR\home\svsystems\eventmanager*c\programdata\asr\home\svsystems\ monitor_protection*

## <a name="check-connectivity-and-replication"></a>检查连接和复制

 初始复制和当前复制失败通常由源计算机和进程服务器之间的连接问题或进程服务器和 Azure 之间的连接问题引起。 下图对这些步骤进行了概述，后面是可帮助你执行这些步骤的过程。

![排查连接和复制问题](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>步骤4：验证源计算机上的时间同步

请确保复制的计算机的系统日期/时间处于同步状态。[了解更多](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>步骤5：检查源计算机上的防病毒软件

检查复制的计算机上的防病毒软件是否正在阻止 Site Recovery。 如果需要从防病毒程序中排除 Site Recovery，请参阅[此文](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)。

## <a name="step-6-check-connectivity-from-source-machine"></a>步骤6：检查源计算机的连接


1. 如果需要，请在源计算机上安装[Telnet 客户端](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)。 不要使用 Ping 命令。
2. 在源计算机上，通过 Telnet 对 HTTPS 端口上的进程服务器执行 ping 操作。 默认情况下，9443是用于复制通信的 HTTPS 端口。

    `telnet <process server IP address> <port>`

3. 验证连接是否成功。


**连接** | **详细信息** | **Action**
--- | --- | ---
**Rp** | Telnet 显示空白屏幕，进程服务器可以访问。 | 无需进一步操作。
**...** | 无法连接 | 请确保进程服务器上允许入站端口9443。 例如，如果有外围网络或屏蔽式子网。 再次检查连接。
**部分成功** | 你可以连接，但源计算机将报告无法访问进程服务器。 | 继续下一次故障排除过程。

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>步骤7：排除无法访问的进程服务器的故障

如果无法从源计算机访问进程服务器，则会显示错误78186。 如果未解决此问题，此问题会导致应用一致性恢复点和崩溃一致恢复点不按预期方式生成。

解决方法：检查源计算机是否可以访问进程服务器的 IP 地址，并在源计算机上运行 cxpsclient 工具以检查端到端连接。


### <a name="check-the-ip-connection-on-the-process-server"></a>检查进程服务器上的 IP 连接

如果 telnet 成功但源计算机报告无法访问进程服务器，请检查是否可以访问进程服务器的 IP 地址。

1. 在 web 浏览器中，尝试访问 IP 地址 https：//< PS_IP >： < PS_Data_Port >/。
2. 如果此检查显示 HTTPS 证书错误，则这是正常的。 如果忽略此错误，则会看到 "400-错误的请求"。 这意味着服务器无法为浏览器请求提供服务，并且标准 HTTPS 连接正常。
3. 如果此检查不起作用，请记下浏览器错误消息。 例如，407错误将指示代理身份验证存在问题。

### <a name="check-the-connection-with-cxpsclient"></a>检查与 cxpsclient 的连接

此外，可以运行 cxpsclient 工具来检查端到端连接。

1. 按以下所示运行此工具：

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. 在进程服务器上，检查以下文件夹中生成的日志：

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err  C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>检查源 VM 日志中的上载失败（错误78028）

将源计算机中阻止的数据上传到进程服务的问题可能导致不生成崩溃一致且应用一致的恢复点。 

1. 若要解决网络上传失败问题，可以在此日志中查找错误：

    C:\Program Files （x86） \Microsoft Azure Site Recovery\agent\svagents * .log 

2. 使用本文中的其余过程可帮助你解决数据上传问题。



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>步骤8：检查进程服务器是否正在推送数据

检查进程服务器是否主动将数据推送到 Azure。

  1. 在进程服务器上打开任务管理器（按 Ctrl+Shift+Esc）。
  2. 选择 "**性能**" 选项卡 >**打开资源监视器**。
  3. 在**资源监视器**"页中，选择"**网络**"选项卡。在 "**具有网络活动的进程**" 下，检查 cbengine.exe 是否正在主动发送大量数据。

       ![具有网络活动的进程下的卷](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  如果 cbengine.exe 未发送大量数据，请完成以下部分中的步骤。

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>步骤9：检查进程服务器与 Azure blob 存储的连接

1. 在资源监视器中，选择 " **cbengine.exe**"。
2. 在 " **TCP 连接**" 下，检查是否存在从进程服务器到 Azure 存储的连接。

  ![Cbengine.exe 与 Azure Blob 存储 URL 之间的连接](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>检查服务

如果没有从进程服务器到 Azure blob 存储 URL 的连接，请检查服务是否正在运行。

1. 在控制面板中，选择 "**服务**"。
2. 验证以下服务是否正在运行：

    - cxprocessserver
    - InMage Scout VX Agent – Sentinel/Outpost
    - Microsoft Azure 恢复服务代理
    - Microsoft Azure Site Recovery 服务
    - tmansvc

3. 启动或重启未运行的任何服务。
4. 验证进程服务器是否已连接并且可访问。 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>步骤10：检查进程服务器与 Azure 公共 IP 地址的连接

1. 在进程服务器上的 **%Programfiles%\Microsoft Azure Recovery Services Agent\Temp**中，打开最新的 cbenginecurr.errlog .errlog 文件。
2. 在文件中，搜索**443**，或搜索字符串**连接尝试失败**。

  ![临时文件夹中的错误日志](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. 如果遇到问题，请使用端口443在 Cbenginecurr.errlog 文件中找到 Azure 公共 IP 地址：

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. 在进程服务器上的命令行中，使用 Telnet 对 Azure 公共 IP 地址进行 ping 操作。
6. 如果无法连接，请执行下一个过程。

## <a name="step-11-check-process-server-firewall-settings"></a>步骤11：检查进程服务器防火墙设置。 

检查进程服务器上基于 IP 地址的防火墙是否正在阻止访问。

1. 对于基于 IP 地址的防火墙规则：

    a）下载[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)的完整列表。

    b）将 IP 地址范围添加到防火墙配置，以确保防火墙允许与 Azure （和默认 HTTPS 端口443）进行通信。

    c）允许订阅的 Azure 区域的 IP 地址范围，以及 Azure 美国西部区域的 IP 地址范围（用于访问控制和标识管理）。

2. 对于基于 URL 的防火墙，请将下表中列出的 Url 添加到防火墙配置。

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>步骤12：验证进程服务器代理设置 

1. 如果使用代理服务器，请确保代理服务器名称由 DNS 服务器解析。 检查在注册表项**HKEY_LOCAL_MACHINE \Software\microsoft\azure Site Recovery\ProxySettings**中设置配置服务器时提供的值。
2. 确保 Azure Site Recovery 代理使用相同的设置发送数据。

    a）搜索**Microsoft Azure 备份**。

    b）打开**Microsoft Azure 备份**，然后选择 "**操作**" > **更改属性**。

    c）在 "**代理配置**" 选项卡上，代理地址应与注册表设置中显示的代理地址相同。 如果不同，请将其更改为相同的地址。

## <a name="step-13-check-bandwidth"></a>步骤13：检查带宽

增加进程服务器和 Azure 之间的带宽，然后检查问题是否仍然存在。


## <a name="next-steps"></a>后续步骤

如需更多帮助，请在 [Azure Site Recovery 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)中提问。 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png
