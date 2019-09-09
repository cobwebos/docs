---
title: 排查 Azure Site Recovery 进程服务器问题
description: 本文介绍如何排查 Azure Site Recovery 进程服务器的问题
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 812cd0293f9627b7438e9870d8985e71dae1d147
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813409"
---
# <a name="troubleshoot-the-process-server"></a>排查进程服务器问题

将本地 VMware VM 和物理服务器设置为灾难恢复到 Azure 时，将使用 [Site Recovery](site-recovery-overview.md) 进程服务器。 本文介绍如何排查进程服务器问题，包括复制和连接问题。

[详细了解](vmware-physical-azure-config-process-server-overview.md)进程服务器。

## <a name="before-you-start"></a>开始之前

在开始故障排除之前：

1. 请确保了解如何[监视进程服务器](vmware-physical-azure-monitor-process-server.md)。
2. 查看下面的最佳做法。
3. 确保遵循[容量注意事项](site-recovery-plan-capacity-vmware.md#capacity-considerations)，并使用[配置服务器](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server)或[独立进程服务器](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)的大小调整指导。

## <a name="best-practices-for-process-server-deployment"></a>进程服务器部署最佳做法

为使进程服务器提供最佳性能，我们汇总了一些常规最佳做法。

**最佳做法** | **详细信息**
--- |---
**使用情况** | 确保只将配置服务器/独立进程服务器用于目标用途。 不要在该计算机上运行任何其他组件。
IP 地址 | 确保进程服务器使用静态 IPv4 地址，且未配置 NAT。
**控制内存/CPU 使用率** |将 CPU 和内存使用率保持在 70% 以下。
**确保有足够的可用空间** | 可用空间是指进程服务器上的缓存磁盘空间。 复制数据在上传到 Azure 之前会存储在缓存中。<br/><br/> 请将可用空间保持在 25% 以上。 如果低于 20%，与进程服务器关联的复制计算机的复制会受到限制。

## <a name="check-process-server-health"></a>检查进程服务器的运行状况

故障排除的第一个步骤是检查进程服务器的运行状况和状态。 为此，请查看所有警报，检查所需的服务是否正在运行，并验证进程服务器是否发出了检测信号。 下图汇总了这些步骤，其后的过程可帮助你执行这些步骤。

![排查进程服务器运行状况问题](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>步骤 1：排查进程服务器运行状况警报

进程服务器会生成一些运行状况警报。 下表汇总了这些警报和建议的操作。

**警报类型** | **错误** | **故障排除**
--- | --- | --- 
![状态良好][green] | 无  | 进程服务器已连接且正常运行。
![警告][yellow] | 指定的服务未运行。 | 1.检查服务是否正在运行。<br/> 2.如果服务已按预期方式运行，请遵照以下说明[排查连接和复制问题](#check-connectivity-and-replication)。
![警告][yellow]  | 过去 15 分钟的 CPU 利用率超过 80%。 | 1.不要添加新计算机。<br/>2.检查使用进程服务器的 VM 数目是否符合[定义的限制](site-recovery-plan-capacity-vmware.md#capacity-considerations)，并考虑设置[额外的进程服务器](vmware-azure-set-up-process-server-scale.md)。<br/>3.遵照以下说明[排查连接和复制问题](#check-connectivity-and-replication)。
![关键][red] |  过去 15 分钟的 CPU 利用率超过 95%。 | 1.不要添加新计算机。<br/>2.检查使用进程服务器的 VM 数目是否符合[定义的限制](site-recovery-plan-capacity-vmware.md#capacity-considerations)，并考虑设置[额外的进程服务器](vmware-azure-set-up-process-server-scale.md)。<br/>3.遵照以下说明[排查连接和复制问题](#check-connectivity-and-replication)。<br/> 4.如果问题持续出现，请针对 VMware/物理服务器复制运行[部署规划器](https://aka.ms/asr-v2a-deployment-planner)。
![警告][yellow] | 过去 15 分钟的内存使用率超过 80%。 |  1.不要添加新计算机。<br/>2.检查使用进程服务器的 VM 数目是否符合[定义的限制](site-recovery-plan-capacity-vmware.md#capacity-considerations)，并考虑设置[额外的进程服务器](vmware-azure-set-up-process-server-scale.md)。<br/>3.遵照警告相关的任何说明操作。<br/> 4.如果问题持续出现，请遵照以下说明[排查连接和复制问题](#check-connectivity-and-replication)。
![关键][red] | 过去 15 分钟的内存使用率超过 95%。 | 1.不要添加新计算机，并考虑设置[额外的进程服务器](vmware-azure-set-up-process-server-scale.md)。<br/> 2.遵照警告相关的任何说明操作。<br/> 3. 4. 如果问题持续出现，请遵照以下说明[排查连接和复制问题](#check-connectivity-and-replication)。<br/> 4.如果问题持续出现，请针对 VMware/物理服务器复制问题运行[部署规划器](https://aka.ms/asr-v2a-deployment-planner)。
![警告][yellow] | 过去 15 分钟的缓存文件夹可用空间小于 30%。 | 1.不要添加新计算机，并考虑设置[额外的进程服务器](vmware-azure-set-up-process-server-scale.md)。<br/>2.检查使用进程服务器的 VM 数量是否符合[指导原则](site-recovery-plan-capacity-vmware.md#capacity-considerations)。<br/> 3.遵照以下说明[排查连接和复制问题](#check-connectivity-and-replication)。
![关键][red] |  过去 15 分钟的可用空间小于 25%。 | 1.遵照警告相关的说明来解决问题。<br/> 2. 3. 遵照以下说明[排查连接和复制问题](#check-connectivity-and-replication)。<br/> 3.如果问题持续出现，请针对 VMware/物理服务器复制运行[部署规划器](https://aka.ms/asr-v2a-deployment-planner)。
![关键][red] | 进程服务器有 15 分钟或更长时间未发出检测信号。 Tmansvs 服务未与配置服务器通信。 | 1) 检查进程服务器是否已启动并运行。<br/> 2.检查 tmassvc 是否在进程服务器上运行。<br/> 3.遵照以下说明[排查连接和复制问题](#check-connectivity-and-replication)。


![表键](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>步骤 2：检查进程服务器服务

下表汇总了应在进程服务器上运行的服务。 根据进程服务器的部署方式，这些服务存在细微的差别。 

对于除 Microsoft Azure 恢复服务代理 (obengine) 以外的所有服务，请检查“启动类型”是否设置为“自动”或“自动(延迟启动)”。
 
**部署** | **正在运行的服务**
--- | ---
**配置服务器上的进程服务器** | Scaleout-processserverProcessServerMonitor;cxprocessserverInMage PushInstall;日志上载服务（LogUpload）;InMage Scout 应用程序服务;Microsoft Azure 恢复服务代理（obengine）;InMage Scout VX Agent-Sentinel/Outpost （svagents）;tmansvcWorld Wide Web 发布服务（W3SVC）;MySQLMicrosoft Azure Site Recovery 服务（dra）
**作为独立服务器运行的进程服务器** | ProcessServer；ProcessServerMonitor；cxprocessserver；InMage PushInstall；Log Upload Service (LogUpload)；InMage Scout Application Service；Microsoft Azure 恢复服务代理 (obengine)；InMage Scout VX Agent-Sentinel/Outpost (svagents)；tmansvc。
**Azure 中部署的用于故障回复的进程服务器** | ProcessServer；ProcessServerMonitor；cxprocessserver；InMage PushInstall；Log Upload Service (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>步骤 3：检查进程服务器的检测信号

如果进程服务器未发出检测信号（错误代码 806），请执行以下操作：

1. 验证进程服务器 VM 是否已启动并运行。
2. 检查以下日志中的错误。

    C:\ProgramData\ASR\home\svsystems\eventmanager *.log  C\ProgramData\ASR\home\svsystems\monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>检查连接和复制

 初始和进行中的复制失败往往是源计算机与进程服务器或者进程服务器与 Azure 之间的连接问题造成的。 下图汇总了这些步骤，其后的过程可帮助你执行这些步骤。

![排查连接和复制问题](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>步骤 4：检查源计算机上的时间同步

确保复制的计算机的系统日期/时间已同步。[了解详细信息](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>步骤 5：检查源计算机上的防病毒软件

检查复制的计算机上是否没有任何防病毒软件正在阻止 Site Recovery。 如果需要从防病毒程序中排除 Site Recovery，请查看[此文](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)。

## <a name="step-6-check-connectivity-from-source-machine"></a>步骤 6：检查从源计算机建立的连接


1. 如果需要，请在源计算机上安装 [Telnet 客户端](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)。 不要使用 Ping。
2. 在源计算机上，使用 Telnet 对 HTTPS 端口上的进程服务器运行 ping。 默认情况下，9443 是复制流量使用的 HTTPS 端口。

    `telnet <process server IP address> <port>`

3. 验证连接是否成功。


**连接** | **详细信息** | **Action**
--- | --- | ---
**成功** | Telnet 会显示空白屏幕，并且可以访问进程服务器。 | 无需进一步的操作。
**不成功** | 无法连接 | 请确保进程服务器上允许入站端口 9443。 例如，你使用了外围网络或屏蔽子网。 再次检查连接。
**部分成功** | 可以连接，但源计算机报告无法访问进程服务器。 | 继续执行下一个故障排除过程。

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>步骤 7：排查无法访问进程服务器的问题

如果无法从源计算机访问进程服务器，会显示错误 78186。 如果不解决此问题，它会导致无法按预期方式生成应用一致性和崩溃一致性恢复点。

故障排除方法是检查源计算机是否可以访问进程服务器的 IP 地址，并在源计算机上运行 cxpsclient 工具来检查端到端的连接。


### <a name="check-the-ip-connection-on-the-process-server"></a>检查进程服务器上的 IP 连接

如果 telnet 成功，但源计算机报告无法访问进程服务器，请检查你是否可以访问进程服务器的 IP 地址。

1. 在 Web 浏览器中，尝试访问 IP 地址 https://<进程服务器 IP>:<进程服务器数据端口>/。
2. 如果检查结果显示 HTTPS 证书错误，这是正常情况。 如果忽略该错误，应会看到“400 - 错误的请求”。 这表示该服务器无法为浏览器请求提供服务，但标准的 HTTPS 连接正常。
3. 如果无法运行此检查，请记下浏览器错误消息。 例如，407 错误表示代理身份验证出现问题。

### <a name="check-the-connection-with-cxpsclient"></a>使用 cxpsclient 检查连接

此外，可以运行 cxpsclient 工具来检查端到端的连接。

1. 按如下所示运行该工具：

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. 在进程服务器上，检查以下文件夹中生成的日志：

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err  C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>检查源 VM 日志中的上传错误（错误 78028）

阻止将数据从源计算机上传到进程服务的问题可能会导致无法生成崩溃一致性和应用一致性恢复点。 

1. 若要排查网络上传错误，可以查看以下日志中的错误：

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

2. 使用本文中的余下过程可帮助解决数据上传问题。



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>步骤 8：检查进程服务器是否正在推送数据

检查进程服务器是否主动将数据推送到 Azure。

  1. 在进程服务器上打开任务管理器（按 Ctrl+Shift+Esc）。
  2. 选择“性能”选项卡> “打开资源监视器”。
  3. 在“资源监视器”页中，选择“网络”选项卡。在 "**具有网络活动的进程**" 下，检查 cbengine.exe 是否正在主动发送大量数据。

       ![存在网络活动的进程下的数量](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  如果 cbengine.exe 未发送大量数据，请完成以下部分中的步骤。

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>步骤 9：检查进程服务器可以连接到 Azure Blob 存储

1. 在资源监视器中选择“cbengine.exe”。
2. 在“TCP 连接”下，检查进程服务器与 Azure 存储之间是否建立了连接。

  ![cbengine.exe 与 Azure Blob 存储 URL 之间的连接](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>检查服务

如果进程服务器与 Azure Blob 存储 URL 之间未建立连接，请检查服务是否正在运行。

1. 在控制面板中选择“服务”。
2. 检查以下服务是否正在运行：

    - cxprocessserver
    - InMage Scout VX Agent – Sentinel/Outpost
    - Microsoft Azure 恢复服务代理
    - Microsoft Azure Site Recovery 服务
    - tmansvc

3. 启动或重启未运行的任何服务。
4. 检查进程服务器是否已连接且可访问。 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>步骤 10：检查进程服务器是否已连接到 Azure 公共 IP 地址

1. 在进程服务器上的 **%programfiles%\Microsoft Azure Recovery Services Agent\Temp** 中，打开最新的 CBEngineCurr.errlog 文件。
2. 在该文件中，搜索 **443** 或字符串 **connection attempt failed**。

  ![Temp 文件夹中的错误日志](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. 如果看到了问题，请使用端口 443 在 CBEngineCurr.currLog 文件查找 Azure 公共 IP 地址：

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. 在进程服务器上的命令行下，使用 Telnet 来 ping Azure 公共 IP 地址。
6. 如果无法连接，请遵循下一过程。

## <a name="step-11-check-process-server-firewall-settings"></a>步骤 11：检查进程服务器防火墙设置。 

检查进程服务器上的基于 IP 地址的防火墙是否阻止了访问。

1. 对于基于 IP 地址的防火墙规则：

    a）下载[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)的完整列表。

    b) 将 IP 地址范围添加到防火墙配置，以确保防火墙允许与 Azure（以及默认的 HTTPS 端口 443）通信。

    c）允许订阅的 Azure 区域的 IP 地址范围，以及 Azure 美国西部区域的 IP 地址范围（用于访问控制和标识管理）。

2. 对于基于 URL 的防火墙，请将下表中列出的 URL 添加到防火墙配置。

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>步骤 12：检查进程服务器代理设置 

1. 如果使用代理服务器，请确保代理服务器名称由 DNS 服务器解析。 在注册表项 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings** 中，检查设置配置服务器时提供的值。
2. 确保 Azure Site Recovery 代理使用相同的设置发送数据。

    a）搜索**Microsoft Azure 备份**。

    b）打开**Microsoft Azure 备份**，然后选择 "**操作** > " "**更改属性**"。

    c) 在“代理配置”选项卡上，代理地址应与注册表设置中显示的代理地址相同。 如果不同，请将其更改为相同的地址。

## <a name="step-13-check-bandwidth"></a>步骤 13：检查带宽

提高进程服务器与 Azure 之间的带宽，然后检查问题是否仍旧出现。


## <a name="next-steps"></a>后续步骤

如需更多帮助，请在 [Azure Site Recovery 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)中提问。 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png
