---
title: 使用 Azure Site Recovery 排查将 VMware VM 和物理服务器灾难恢复到 Azure 时的复制问题 | Microsoft Docs
description: 本文针对使用 Azure Site Recovery 将 VMware VM 和物理服务器灾难恢复到 Azure 期间遇到的常见复制问题提供故障排除信息。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/14/2019
ms.author: mayg
ms.openlocfilehash: 1aaf13f01c7e7197001f3099fabd4b8be8545f0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565023"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>解决 VMware VM 和物理服务器的复制问题

使用 Azure Site Recovery 保护 VMware 虚拟机或物理服务器时，可能会收到特定的错误消息。 本文介绍在使用 [Site Recovery](site-recovery-overview.md) 将本地 VMware VM 和物理服务器复制到 Azure 时可能遇到的一些常见问题。

## <a name="monitor-process-server-health-to-avoid-replication-issues"></a>监视进程服务器运行状况以避免出现复制问题

建议在门户中监视进程服务器 (PS) 运行状况，以确保关联的源计算机的复制正常进行。 在保管库中，转到“管理”>“Site Recovery 基础结构”>“配置服务器”。 在“配置服务器”边栏选项卡上，单击“关联的服务器”下的“进程服务器”。 此时会打开“进程服务器”边栏选项卡，其中显示了进程服务器的运行状况统计信息。 可以跟踪 CPU 利用率、内存使用率、用于复制的 PS 服务的状态、证书过期日期和可用空间。 所有统计信息的状态应显示为绿色。 

**建议将内存和 CPU 使用率保持在 70% 以下，将可用空间保持在 25% 以上**。 可用空间是指进程服务器中的缓存磁盘空间，在将源计算机中的复制数据上传到 Azure 之前，这些空间用于存储这些数据。 如果可用空间降到 20% 以下，所有关联源计算机的复制将受到限制。 遵循[容量指南](./site-recovery-plan-capacity-vmware.md#capacity-considerations)了解复制源计算机所需的配置。

确保以下服务正在 PS 计算机上运行。 启动或重启未运行的任何服务。

**内置进程服务器**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* InMage PushInstall
* 日志上传服务 (LogUpload)
* InMage Scout 应用程序服务
* Microsoft Azure 恢复服务代理 (obengine)
* InMage Scout VX 代理 – Sentinel/Outpost (svagents)
* tmansvc
* 万维网发布服务 (W3SVC)
* MySQL
* Microsoft Azure Site Recovery 服务 (dra)

**横向扩展进程服务器**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* InMage PushInstall
* 日志上传服务 (LogUpload)
* InMage Scout 应用程序服务
* Microsoft Azure 恢复服务代理 (obengine)
* InMage Scout VX 代理 – Sentinel/Outpost (svagents)
* tmansvc

**Azure 中用于故障回复的进程服务器**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* InMage PushInstall
* 日志上传服务 (LogUpload)

确保所有服务的启动类型设置为“自动”或“自动(延迟启动)”。 不需要按上面所述为 Microsoft Azure 恢复服务代理 (obengine) 服务设置启动类型。

## <a name="replication-issues"></a>复制问题

初始和正在进行的复制故障通常是由源服务器和进程服务器之间或进程服务器与 Azure 之间的连接问题引起。 大多数情况下，完成以下部分中的步骤可以排查这些问题。

>[!Note]
>请确保：
>1. 系统保持同步是受保护的项的日期时间。
>2. 没有防病毒软件正在阻止 Azure Site Recovery。 了解[详细](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)上所需的 Azure Site Recovery 的文件夹排除项。

### <a name="check-the-source-machine-for-connectivity-issues"></a>检查源计算机的连接问题

你可以检查源计算机以下列表显示方式。

*  在源服务器上的命令行中运行以下命令，使用 Telnet 通过 HTTPS 端口对进程服务器执行 ping 操作。 进程服务器默认使用 HTTPS 端口 9443 发送和接收复制流量。 在注册时可以修改此端口。 以下命令检查网络连接问题或者阻止防火墙端口的问题。


   `telnet <process server IP address> <port>`


   > [!NOTE]
   > 使用 Telnet 测试连接。 请不要使用 `ping`。 如果未安装 Telnet，请完成[安装 Telnet 客户端](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)中列出的步骤。

   如果 telnet 能够成功连接到 PS 端口，则会显示一个空白屏幕。

   如果无法连接到进程服务器，请在进程服务器上允许入站端口 9443。 例如，如果网络包含外围网络或屏蔽子网，可能需要在进程服务器上允许入站端口 9443。 然后，检查是否仍出现此问题。

*  如果 telnet 成功但源计算机报告 PS 不可访问，请在源计算机上打开 Web 浏览器，并检查是否可以访问地址 https://<PS IP>:<PS 数据端口>/。

    接入此地址时，预期会发生 HTTPS 证书错误。 忽略证书错误并继续操作会出现“400 – 错误的请求”，表示服务器无法为浏览器的请求提供服务，但与服务器建立的标准 HTTPS 连接可正常工作。

    如果连接不成功，浏览器上有关错误消息的详细信息会提供指导。 例如，如果代理身份验证不正确，则代理服务器会返回“407 – 需要代理身份验证”，并在错误消息中指明所需的操作。 

*  发生与网络上传相关的错误时，请检查源 VM 上的以下日志：

       C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

### <a name="check-the-process-server-for-connectivity-issues"></a>检查进程服务器上的连接问题

以下列表显示了检查进程服务器的方式：

> [!NOTE]
> 进程服务器必须有一个静态 IPv4 地址，不应在其上配置 NAT IP。

* **检查源计算机与进程服务器之间的连接**
* 如果可以从源计算机执行 telnet 但无法从源访问 PS，请在源 VM 上运行 cxpsclient 工具，使用 cxprocessserver 检查端到端连接：

      <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>

   有关相应错误的详细信息，请在 PS 上检查以下目录中生成的日志：

      C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err
      and
      C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer
* 检查 PS 上的以下日志，如果没有检测信号从 ps。 这由**错误代码 806**门户上。

      C:\ProgramData\ASR\home\svsystems\eventmanager*.log
      and
      C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

* **检查进程服务器是否主动将数据推送到 Azure**。

  1. 在进程服务器上打开任务管理器（按 Ctrl+Shift+Esc）。
  2. 选择“性能”选项卡，然后选择“打开资源监视器”链接。 
  3. 在“资源监视器”页上，选择“网络”选项卡。在“网络活动的进程”下，检查 **cbengine.exe** 是否正在主动发送大量数据。

       ![显示“网络活动的进程”下的卷的屏幕截图](./media/vmware-azure-troubleshoot-replication/cbengine.png)

  如果 cbengine.exe 未发送大量数据，请完成以下部分中的步骤。

* **检查进程服务器可以连接到 Azure Blob 存储**。

  选择“cbengine.exe”。 在“TCP 连接”下，检查进程服务器与 Azure Blob 存储 URL 之间是否建立了连接。

  ![显示 cbengine.exe 与 Azure Blob 存储 URL 之间的连接的屏幕截图](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

  进程服务器与 Azure Blob 存储 URL 之间未建立连接，请在控制面板中选择“服务”。 检查以下服务是否正在运行：

  *  cxprocessserver
  *  InMage Scout VX Agent – Sentinel/Outpost
  *  Microsoft Azure 恢复服务代理
  *  Microsoft Azure Site Recovery 服务
  *  tmansvc

  启动或重启未运行的任何服务。 检查是否仍出现此问题。

* **检查进程服务器是否可以使用端口 443 连接到 Azure 公共 IP 地址**。

  在 %programfiles%\Microsoft Azure Recovery Services Agent\Temp 中，打开最新的 CBEngineCurr.errlog 文件。 在该文件中，搜索 **443** 或字符串 **connection attempt failed**。

  ![显示 Temp 文件夹中错误日志的屏幕截图](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

  如果找到问题，请在进程服务器上的命令行中，使用 Telnet 来 ping Azure 公共 IP 地址（在上图中，IP 地址已掩码）。 可以使用端口 443 在 CBEngineCurr.currLog 文件查找 Azure 公共 IP 地址：

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

  如果无法连接，请检查访问问题是否由防火墙或代理设置所导致，如下一步骤中所述。

* **检查进程服务器上的基于 IP 地址的防火墙是否阻止了访问**。

  如果在服务器上使用基于 IP 地址的防火墙规则，请下载 [Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)的完整列表。 将 IP 地址范围添加到防火墙配置，以确保防火墙允许与 Azure（以及默认的 HTTPS 端口 443）通信。 允许订阅的 Azure 区域的 IP 地址范围以及 Azure 美国西部区域的 IP 地址范围（用于访问控制和标识管理）。

* **检查进程服务器上的基于 URL 的防火墙是否阻止了访问**。

  如果在服务器上使用基于 URL 的防火墙规则，请将下表中列出的 URL 添加到防火墙配置：

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

*  **检查进程服务器上的代理设置是否阻止了访问**。

   如果使用代理服务器，请确保代理服务器名称由 DNS 服务器解析。 若要检查设置配置服务器时提供的值，请转到注册表项 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**。

   接下来，确保 Azure Site Recovery 代理使用相同的设置发送数据： 
      
   1. 搜索“Microsoft Azure 备份”。 
   2. 打开“Microsoft Azure 备份”，然后选择“操作” > “更改属性”。 
   3. 在“代理配置”选项卡上，应会看到代理地址。 代理地址应与注册表设置中显示的代理地址相同。 如果不同，请将其更改为相同的地址。

*  **检查进程服务器上的限制带宽是否受约束**。

   增加带宽，然后检查问题是否仍然出现。


## <a name="source-machine-isnt-listed-in-the-azure-portal"></a>源计算机未在 Azure 门户中列出。

尝试选择源计算机来通过 Site Recovery 启用复制时，计算机可能由于以下原因之一而不可用：

* **具有相同实例 UUID 的两个虚拟机**：如果 vCenter 中存在具有相同实例 UUID 的两个虚拟机，则配置服务器发现的第一个虚拟机将显示在 Azure 门户中。 若要解决此问题，请确保没有两个虚拟机具有相同的实例 UUID。 如果备份 VM 处于活动状态，并且已记录到发现记录，则这种情况会很常见。 请参阅[使用 Azure Site Recovery 进行 VMware 到 Azure 的复制：如何清理重复或过时的条目](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)来解决问题。
* **vCenter 用户凭据不正确**：确保在使用 OVF 模板或统一安装程序安装配置服务器时添加正确的 vCenter 凭据。 若要验证安装期间添加的凭据是否正确，请参阅[修改用于自动发现的凭据](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery)。
* **vCenter 特权不足**：如果未提供所需的权限来访问 vCenter，则发现虚拟机时可能会失败。 确保将[为自动发现准备帐户](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)中所述的权限添加到 vCenter 用户帐户。
* **Azure Site Recovery 管理服务器**：如果虚拟机用作管理服务器并充当以下一个或多个角色 - 配置服务器/横向扩展进程服务器/主目标服务器，则无法在门户中选择该虚拟机。 无法复制管理服务器。
* **已通过 Azure Site Recovery 服务进行保护/故障转移**：如果虚拟机已通过 Site Recovery 进行保护或故障转移，则无法在门户中选择保护该虚拟机。 确保要在门户中查找的虚拟机尚未由其他任何用户进行保护，或者位于不同的订阅下。
* **vCenter 未连接**：检查 vCenter 是否处于连接状态。 若要验证，请转到“恢复服务保管库”>“Site Recovery 基础结构”>“配置服务器”，单击相应的配置服务器，此时，右侧会打开一个边栏选项卡，其中显示了关联服务器的详细信息。 检查 vCenter 是否已连接。 如果处于“未连接”状态，请解决问题，并在门户中[刷新配置服务器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 然后，虚拟机将列在门户中。
* **ESXi 已关机**：如果虚拟机所在的 ESXi 主机处于关机状态，则虚拟机将不会列出，或者在 Azure 门户中不可选择。 打开 ESXi 主机，并在门户中[刷新配置服务器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 然后，虚拟机将列在门户中。
* **正在等待重新启动**：如果虚拟机正在等待重新启动，则你无法在 Azure 门户中选择该虚拟机。 请务必完成等待中的重新启动活动，并[刷新配置服务器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 然后，虚拟机将列在门户中。
* **找不到 IP**：如果虚拟机没有关联有效的 IP 地址，则你无法在 Azure 门户中选择该虚拟机。 请务必将有效的 IP 地址分配到虚拟机，并[刷新配置服务器](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。 然后，虚拟机将列在门户中。

## <a name="protected-virtual-machines-are-greyed-out-in-the-portal"></a>受保护的虚拟机在门户中处于灰显状态

如果系统中存在重复的条目，则在 Site Recovery 下复制的虚拟机将不会显示在 Azure 门户中。 若要了解如何删除过时的条目和解决此问题，请参阅[使用 Azure Site Recovery 进行 VMware 到 Azure 的复制：如何清除重复或过时的条目](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)。

## <a name="common-errors-and-recommended-steps-for-resolution"></a>解决常见错误和建议的步骤

### <a name="initial-replication-issues-error-78169"></a>初始复制问题 [Error 78169]

通过更高版本，确保有无连接、 带宽或时间同步相关的问题，请确保：

- 没有防病毒软件正在阻止 Azure Site Recovery。 了解[详细](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)上所需的 Azure Site Recovery 的文件夹排除项。

### <a name="application-consistency-recovery-point-missing-error-78144"></a>缺少 [Error 78144] 应用程序一致性恢复点

 由于卷影复制服务 (VSS) 问题而发生这种情况。 若要解决问题，请执行以下操作： 
 
- 验证安装的 Azure Site Recovery 代理版本至少 9.22.2。 
- 验证为 Windows 服务中的服务安装 VSS 提供程序，同时还要验证组件服务 MMC 来检查列出了 Azure Site Recovery VSS 提供程序。
- 如果未安装 VSS 提供程序，请参阅[安装故障故障排除文章](vmware-azure-troubleshoot-push-install.md#vss-installation-failures)。

- 如果禁用了 VSS，
    - 验证 VSS 提供程序服务的启动类型设置为**自动**。
    - 重启以下服务：
        - VSS 服务
        - Azure Site Recovery VSS 提供程序
        - VDS 服务

### <a name="high-churn-on-source-machine-error-78188"></a>源计算机 [Error 78188] 上的高变动量

可能的原因：
- 列出的磁盘的虚拟机上的数据更改率 （写入字节数/秒） 是多个[Azure Site Recovery 支持的限制](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits)复制目标存储帐户类型。
- 变动率中没有突增的高数据量因处于挂起状态进行上传。

若要解决问题，请执行以下操作：
- 请确保目标存储帐户类型 （标准或高级） 根据流失率要求在源设置。
- 如果观察到的变动量是暂时性的等待挂起的数据上传来保持同步，并创建恢复点的几个小时。
- 如果问题仍然存在，使用 ASR[部署规划器](site-recovery-deployment-planner.md#overview)来帮助规划复制。

### <a name="no-heartbeat-from-source-machine-error-78174"></a>没有来自源计算机 [Error 78174] 检测信号

发生这种情况是当源计算机上的 Azure Site Recovery 移动代理通信不时与配置服务器 (CS)。

若要解决此问题，使用以下步骤来验证来自源 VM 网络连接到配置服务器：

1. 验证源计算机正在运行。
2. 登录到源计算机使用具有管理员权限的帐户。
3. 验证以下服务是否正在运行，如果不重新启动服务：
   - Svagents （InMage Scout VX 代理）
   - InMage Scout 应用程序服务
4. 源计算机上检查错误详细信息的位置的日志：

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="no-heartbeat-from-process-server-error-806"></a>从进程服务器 [错误 806] 无检测信号
如果没有任何检测信号从进程服务器 (PS)，请检查：
1. PS VM 已启动并运行
2. 检查错误详细信息的 PS 上的以下日志：

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="no-heartbeat-from-master-target-error-78022"></a>从主目标 [Error 78022] 无检测信号

在主目标上的 Azure Site Recovery 移动代理未与配置服务器通信时，将发生这种情况。

若要解决此问题，使用以下步骤来验证服务状态：

1. 验证主目标 VM 正在运行。
2. 登录到主目标 VM，使用具有管理员权限的帐户。
    - 验证 svagents 服务正在运行。 如果正在运行，重新启动服务
    - 检查错误详细信息的位置的日志：
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log

### <a name="process-server-is-not-reachable-from-the-source-machine-error-78186"></a>进程服务器不可访问源计算机 [Error 78186]

如果未解决未生成的应用程序和崩溃一致点会导致此错误。 若要解决此问题，请执行以下故障排除的链接：
1. 确保[PS 服务是否正在运行](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues)
2. [检查源计算机的连接问题](vmware-azure-troubleshoot-replication.md#check-the-source-machine-for-connectivity-issues)
3. [检查进程服务器的连接问题](vmware-azure-troubleshoot-replication.md#check-the-process-server-for-connectivity-issues)并遵循为提供的指南：
    - 检查与源的连接
    - 防火墙和代理问题

### <a name="data-upload-blocked-from-source-machine-to-process-server-error-78028"></a>数据上传到进程服务器 [Error 78028] 被阻止的源计算机

如果未解决未生成的应用程序和崩溃一致点会导致此错误。 若要解决此问题，请执行以下故障排除的链接：

1. 确保[PS 服务是否正在运行](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues)
2. [检查源计算机的连接问题](vmware-azure-troubleshoot-replication.md#check-the-source-machine-for-connectivity-issues)
3. [检查进程服务器的连接问题](vmware-azure-troubleshoot-replication.md#check-the-process-server-for-connectivity-issues)并遵循为提供的指南：
    - 检查与源的连接
    - 防火墙和代理问题

## <a name="next-steps"></a>后续步骤

如需更多帮助，请在 [Azure Site Recovery 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)中提问。 我们的社区非常活跃，将有一位工程师为你提供帮助。
