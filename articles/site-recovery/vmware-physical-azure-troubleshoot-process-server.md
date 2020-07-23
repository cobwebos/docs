---
title: Azure Site Recovery 进程服务器故障排除
description: 本文介绍如何对 Azure Site Recovery 进程服务器的问题进行故障排除
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 7679148e195bd67ab5da58636552a684c25c31b0
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86131981"
---
# <a name="troubleshoot-the-process-server"></a>进程服务器故障排除

在将本地 VMware VM 和物理服务器的灾难恢复设置到 Azure 时，会使用 [Site Recovery](site-recovery-overview.md) 进程服务器。 本文介绍如何对进程服务器的问题进行故障排除，包括复制和连接问题。

[详细了解](vmware-physical-azure-config-process-server-overview.md)进程服务器。

## <a name="before-you-start"></a>开始之前

开始故障排除之前：

1. 确保了解如何[监视进程服务器](vmware-physical-azure-monitor-process-server.md)。
2. 查看下面的最佳做法。
3. 确保遵循[容量注意事项](site-recovery-plan-capacity-vmware.md#capacity-considerations)，并使用适用于[配置服务器](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server)或[独立进程服务器](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)的大小调整指南。

## <a name="best-practices-for-process-server-deployment"></a>进程服务器部署的最佳做法

为了获得进程服务器的最佳性能，我们总结了一些常规最佳做法。

最佳做法 | **详细信息**
--- |---
**使用情况** | 确保配置服务器/独立进程服务器仅用于预期目的。 不要在计算机上运行任何其他操作。
IP 地址 | 确保进程服务器具有静态 IPv4 地址，而未配置 NAT。
控制内存/CPU 使用率 |使 CPU 和内存使用率保持低于 70%。
确保可用空间 | 可用空间指进程服务器上的缓存磁盘空间。 复制数据在上传到 Azure 之前存储在缓存中。<br/><br/> 使可用空间保持高于 25%。 如果低于 20%，则会对与进程服务器关联的复制计算机限制复制。

## <a name="check-process-server-health"></a>检查进程服务器运行状况

故障排除的第一步是检查进程服务器的运行状况和状态。 为此，请查看所有警报，检查所需服务是否正在运行，并验证是否有来自进程服务器的检测信号。 下图汇总了这些步骤，后面是可帮助你执行这些步骤的过程。

![进程服务器运行状况故障排除](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>步骤 1：对进程服务器运行状况警报进行故障排除

进程服务器会生成一些运行状况警报。 下表汇总了这些警报和建议操作。

警报类型 | **错误** | **故障排除**
--- | --- | --- 
![正常][green] | 无  | 进程服务器已连接并且状态正常。
![警告][yellow] | 指定服务未在运行。 | 1.检查服务是否正在运行。<br/> 2.如果服务按预期运行，请按照下面的说明[对连接和复制问题进行故障排除](#check-connectivity-and-replication)。
![警告][yellow]  | 过去 15 分钟内的 CPU 使用率 > 80%。 | 1.不要添加新计算机。<br/>2.检查使用进程服务器的 VM 数是否符合[定义的限制](site-recovery-plan-capacity-vmware.md#capacity-considerations)，并考虑设置[其他进程服务器](vmware-azure-set-up-process-server-scale.md)。<br/>3.按照下面的说明[对连接和复制问题进行故障排除](#check-connectivity-and-replication)。
![严重][red] |  过去 15 分钟内的 CPU 使用率 > 95%。 | 1.不要添加新计算机。<br/>2.检查使用进程服务器的 VM 数是否符合[定义的限制](site-recovery-plan-capacity-vmware.md#capacity-considerations)，并考虑设置[其他进程服务器](vmware-azure-set-up-process-server-scale.md)。<br/>3.按照下面的说明[对连接和复制问题进行故障排除](#check-connectivity-and-replication)。<br/> 4.如果问题仍然存在，请运行[部署规划器](https://aka.ms/asr-v2a-deployment-planner)以用于 VMware/物理服务器复制。
![警告][yellow] | 过去 15 分钟内的内存使用率 > 80%。 |  1.不要添加新计算机。<br/>2.检查使用进程服务器的 VM 数是否符合[定义的限制](site-recovery-plan-capacity-vmware.md#capacity-considerations)，并考虑设置[其他进程服务器](vmware-azure-set-up-process-server-scale.md)。<br/>3.按照与警告关联的任何说明进行操作。<br/> 4.如果问题仍然存在，请按照下面的说明[对连接和复制问题进行故障排除](#check-connectivity-and-replication)。
![严重][red] | 过去 15 分钟内的内存使用率 > 95%。 | 1.不要添加新计算机，并考虑设置[附加进程服务器](vmware-azure-set-up-process-server-scale.md)。<br/> 2.按照与警告关联的任何说明进行操作。<br/> 3. 4. 如果问题任然存在，请按照下面的说明[对连接和复制问题进行故障排除](#check-connectivity-and-replication)。<br/> 4.如果问题仍然存在，请运行[部署规划器](https://aka.ms/asr-v2a-deployment-planner)以解决 VMware/物理服务器复制问题。
![警告][yellow] | 过去 15 分钟内的缓存文件夹可用空间 < 30%。 | 1.不要添加新计算机，并考虑设置[附加进程服务器](vmware-azure-set-up-process-server-scale.md)。<br/>2.检查使用进程服务器的 VM 数是否符合[指导原则](site-recovery-plan-capacity-vmware.md#capacity-considerations)。<br/> 3.按照下面的说明[对连接和复制问题进行故障排除](#check-connectivity-and-replication)。
![严重][red] |  过去 15 分钟内的可用空间 < 25% | 1.按照与警告关联的说明来解决此问题。<br/> 2. 3. 按照下面的说明[对连接和复制问题进行故障排除](#check-connectivity-and-replication)。<br/> 3.如果问题仍然存在，请运行[部署规划器](https://aka.ms/asr-v2a-deployment-planner)以用于 VMware/物理服务器复制。
![严重][red] | 在 15 分钟或更长时间内没有来自进程服务器的检测信号。 tmansvs 服务未与配置服务器通信。 | 1) 检查进程服务器是否已启动并正在运行。<br/> 2.检查 tmassvc 是否在进程服务器上运行。<br/> 3.按照下面的说明[对连接和复制问题进行故障排除](#check-connectivity-and-replication)。


![表键](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>步骤 2：检查进程服务器服务

下表中汇总了应在进程服务器上运行的服务。 根据进程服务器的部署方式，服务会略有不同。 

对于除 Microsoft Azure 恢复服务代理 (obengine) 以外的所有服务，请检查“启动类型”是否设置为“自动”或“自动(延迟启动)”。
 
**部署** | 正在运行的服务
--- | ---
配置服务器上的进程服务器 | ProcessServer；ProcessServerMonitor；cxprocessserver；InMage PushInstall；日志上传服务 (LogUpload)；InMage Scout 应用程序服务；Microsoft Azure 恢复服务代理 (obengine)；InMage Scout VX Agent-Sentinel/Outpost (svagents)；tmansvc；World Wide Web 发布服务 (W3SVC)；MySQL；Microsoft Azure Site Recovery 服务 (dra)
作为独立服务器运行的进程服务器 | ProcessServer；ProcessServerMonitor；cxprocessserver；InMage PushInstall；日志上传服务 (LogUpload)；InMage Scout 应用程序服务；Microsoft Azure 恢复服务代理 (obengine)；InMage Scout VX Agent-Sentinel/Outpost (svagents)；tmansvc。
部署在 Azure 中用于故障回复的进程服务器 | ProcessServer；ProcessServerMonitor；cxprocessserver；InMage PushInstall；日志上传服务 (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>步骤 3：检查进程服务器检测信号

如果没有来自进程服务器的检测信号（错误代码 806），请执行以下操作：

1. 验证进程服务器 VM 是否已启动并正在运行。
2. 检查这些日志中是否有错误。

    C:\ProgramData\ASR\home\svsystems\eventmanager.log  C\ProgramData\ASR\home\svsystems\monitor_protection.log

## <a name="check-connectivity-and-replication"></a>检查连接和复制

 初始和持续的复制失败通常是源计算机与进程服务器或者进程服务器与 Azure 之间的连接问题造成的。 下图汇总了这些步骤，后面是可帮助你执行这些步骤的过程。

![对连接和复制进行故障排除](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>步骤 4：验证源计算机上的时间同步

确保复制计算机的系统日期/时间处于同步状态。[了解详细信息](/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>步骤 5：检查源计算机上的防病毒软件

检查复制计算机上没有防病毒软件在阻止 Site Recovery。 如果需要从防病毒程序中排除 Site Recovery，请参阅[本文](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)。

## <a name="step-6-check-connectivity-from-source-machine"></a>步骤 6：检查来自源计算机的连接


1. 如果需要，在源计算机上安装 [Telnet 客户端](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771275(v=ws.10))。 请勿使用 Ping。
2. 在源计算机上，使用 Telnet 在 HTTPS 端口上对进程服务器执行 ping 操作。 默认情况下，9443 是用于复制流量的 HTTPS 端口。

    `telnet <process server IP address> <port>`

3. 验证连接是否成功。


**连接** | **详细信息** | **Action**
--- | --- | ---
成功 | Telnet 显示空白屏幕，进程服务器可以访问。 | 无需进一步操作。
不成功 | 无法连接 | 确保进程服务器上允许使用入站端口 9443。 例如，如果有外围网络或外围子网。 再次检查连接。
部分成功 | 可以连接，但源计算机报告无法访问进程服务器。 | 继续执行下一个故障排除过程。

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>步骤 7：对无法访问的进程服务器进行故障排除

如果无法从源计算机访问进程服务器，则会显示错误 78186。 如果未得到解决，则此问题会导致应用一致性恢复点和崩溃一致性恢复点都不会按预期方式生成。

故障排除方法是检查源计算机是否可以访问进程服务器的 IP 地址，并在源计算机上运行 cxpsclient 工具以检查端到端连接。


### <a name="check-the-ip-connection-on-the-process-server"></a>检查进程服务器上的 IP 连接

如果 telnet 成功但源计算机报告无法访问进程服务器，请检查是否可以访问进程服务器的 IP 地址。

1. 在 Web 浏览器中，尝试访问 IP 地址 https://<PS_IP>:<PS_Data_Port>/。
2. 如果此检查显示 HTTPS 证书错误，则这是正常情况。 如果忽略该错误，则应看到“400 - 错误请求”。 这意味着服务器无法处理浏览器请求，并且标准 HTTPS 连接状态正常。
3. 如果此检查不起作用，请记下浏览器错误消息。 例如，407 错误指示代理身份验证存在问题。

### <a name="check-the-connection-with-cxpsclient"></a>使用 cxpsclient 检查连接

此外，可以运行 cxpsclient 工具以检查端到端连接。

1. 按以下所示运行此工具：

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. 在进程服务器上，检查以下文件夹中生成的日志：

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err  C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>检查源 VM 日志中是否有上传失败（错误 78028）

阻止从源计算机将数据上传到进程服务的问题可能会导致不生成崩溃一致性和应用一致性恢复点。 

1. 若要对网络上传失败进行故障排除，可以在此日志中查找错误：

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

2. 使用本文中的其余过程可帮助你解决数据上传问题。



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>步骤 8：检查进程服务器是否在推送数据

检查进程服务器是否主动将数据推送到 Azure。

  1. 在进程服务器上打开任务管理器（按 Ctrl+Shift+Esc）。
  2. 选择“性能”选项卡 >“打开资源监视器”。
  3. 在“资源监视器”页中，选择“网络”选项卡。 在“网络活动的进程”下，检查 cbengine.exe 是否正在主动发送大量数据。

       ![“网络活动的进程”下的卷](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  如果 cbengine.exe 未发送大量数据，请完成以下部分中的步骤。

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>步骤 9：检查进程服务器与 Azure Blob 存储的连接

1. 在资源监视器中，选择 cbengine.exe。
2. 在“TCP 连接”下，检查进程服务器与 Azure 存储之间是否建立了连接。

  ![cbengine.exe 与 Azure Blob 存储 URL 之间的连接](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>检查服务

如果进程服务器与 Azure Blob 存储 URL 之间未建立连接，请检查服务是否在运行。

1. 在“控制面板”中，选择“服务”。
2. 验证下列服务是否正在运行：

    - cxprocessserver
    - InMage Scout VX Agent – Sentinel/Outpost
    - Microsoft Azure 恢复服务代理
    - Microsoft Azure Site Recovery 服务
    - tmansvc

3. 启动或重启未运行的任何服务。
4. 验证进程服务器是否已连接并且可访问。 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>步骤 10：检查进程服务器与 Azure 公共 IP 地址的连接

1. 在进程服务器上的 %programfiles%\Microsoft Azure Recovery Services Agent\Temp 中，打开最新的 CBEngineCurr.errlog 文件。
2. 在该文件中，搜索 443 或字符串 connection attempt failed。

  ![Temp 文件夹中的错误日志](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. 如果看到问题，请使用端口 443 在 CBEngineCurr.currLog 文件中查找 Azure 公共 IP 地址：

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. 在进程服务器上的命令行中，使用 Telnet 来 ping Azure 公共 IP 地址。
6. 如果无法连接，则执行下一个过程。

## <a name="step-11-check-process-server-firewall-settings"></a>步骤 11：检查进程服务器防火墙设置。 

检查进程服务器上的基于 IP 地址的防火墙是否阻止访问。

1. 对于基于 IP 地址的防火墙规则：

    a) 下载 [Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。

    b) 将 IP 地址范围添加到防火墙配置，以确保防火墙允许与 Azure（以及默认的 HTTPS 端口 443）通信。

    c) 允许订阅的 Azure 区域的 IP 地址范围以及 Azure 美国西部区域的 IP 地址范围（用于访问控制和标识管理）。

2. 对于基于 URL 的防火墙，将下表中列出的 URL 添加到防火墙配置。

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>步骤 12：验证进程服务器代理设置 

1. 如果使用代理服务器，请确保代理服务器名称由 DNS 服务器解析。 在注册表项 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings 中检查设置配置服务器时提供的值。
2. 确保 Azure Site Recovery 代理使用相同的设置发送数据。

    a) 搜索“Microsoft Azure 备份”。

    b) 打开“Microsoft Azure 备份”，然后选择“操作” > “更改属性”。  

    c) 在“代理配置”选项卡上，代理地址应与注册表设置中显示的代理地址相同。 如果不同，请将其更改为相同的地址。

## <a name="step-13-check-bandwidth"></a>步骤 13：检查带宽

增加进程服务器与 Azure 之间的带宽，然后检查问题是否仍然出现。


## <a name="next-steps"></a>后续步骤

如需更多帮助，请在[有关 Azure Site Recovery 的 Microsoft Q&A 问题页面](/answers/topics/azure-site-recovery.html)中提问。 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png
