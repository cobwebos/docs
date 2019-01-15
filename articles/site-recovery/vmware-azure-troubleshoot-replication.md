---
title: 使用 Azure Site Recovery 排查将 VMware VM 和物理服务器灾难恢复到 Azure 时的复制问题 | Microsoft Docs
description: 本文针对使用 Azure Site Recovery 将 VMware VM 和物理服务器灾难恢复到 Azure 期间遇到的常见复制问题提供故障排除信息。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: c53dc81da9469c0628adbd3751dc818997fa4d05
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063672"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>解决 VMware VM 和物理服务器的复制问题

使用 Azure Site Recovery 保护 VMware 虚拟机或物理服务器时，可能会收到特定的错误消息。 本文介绍在使用 [Site Recovery](site-recovery-overview.md) 将本地 VMware VM 和物理服务器复制到 Azure 时可能遇到的一些常见问题。

## <a name="initial-replication-issues"></a>初始复制问题

初始复制失败往往是源服务器与进程服务器或者进程服务器与 Azure 之间的连接问题造成的。 大多数情况下，完成以下部分中的步骤可以排查这些问题。

### <a name="check-the-source-machine"></a>检查源计算机

以下列表显示了检查源计算机的方式：

*  在源服务器上的命令行中运行以下命令，使用 Telnet 通过 HTTPS 端口（默认 HTTPS 端口为 9443）对进程服务器执行 ping 操作。 该命令将检查网络连接问题，或者阻止防火墙端口的问题。


   `telnet <process server IP address> <port>`


   > [!NOTE]
   > 使用 Telnet 测试连接。 请不要使用 `ping`。 如果未安装 Telnet，请完成[安装 Telnet 客户端](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)中列出的步骤。

   如果无法连接到进程服务器，请在进程服务器上允许入站端口 9443。 例如，如果网络包含外围网络或屏蔽子网，可能需要在进程服务器上允许入站端口 9443。 然后，检查是否仍出现此问题。

*  检查 **InMage Scout VX Agent – Sentinel/OutpostStart** 服务的状态。 如果该服务未运行，请启动该服务，然后检查是否仍出现此问题。   

### <a name="check-the-process-server"></a>检查进程服务器

以下列表显示了检查进程服务器的方式：

*  **检查进程服务器是否主动将数据推送到 Azure**。

   1. 在进程服务器上打开任务管理器（按 Ctrl+Shift+Esc）。
   2. 选择“性能”选项卡，然后选择“打开资源监视器”链接。 
   3. 在“资源监视器”页上，选择“网络”选项卡。在“网络活动的进程”下，检查 **cbengine.exe** 是否正在主动发送大量数据。

        ![显示“网络活动的进程”下的卷的屏幕截图](./media/vmware-azure-troubleshoot-replication/cbengine.png)

   如果 cbengine.exe 未发送大量数据，请完成以下部分中的步骤。

*  **检查进程服务器可以连接到 Azure Blob 存储**。

   选择“cbengine.exe”。 在“TCP 连接”下，检查进程服务器与 Azure Blob 存储 URL 之间是否建立了连接。

   ![显示 cbengine.exe 与 Azure Blob 存储 URL 之间的连接的屏幕截图](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

   进程服务器与 Azure Blob 存储 URL 之间未建立连接，请在控制面板中选择“服务”。 检查以下服务是否正在运行：

   *  cxprocessserver
   *  InMage Scout VX Agent – Sentinel/Outpost
   *  Microsoft Azure 恢复服务代理
   *  Microsoft Azure Site Recovery 服务
   *  tmansvc

   启动或重启未运行的任何服务。 检查是否仍出现此问题。

*  **检查进程服务器是否可以使用端口 443 连接到 Azure 公共 IP 地址**。

   在 %programfiles%\Microsoft Azure Recovery Services Agent\Temp 中，打开最新的 CBEngineCurr.errlog 文件。 在该文件中，搜索 **443** 或字符串 **connection attempt failed**。

   ![显示 Temp 文件夹中错误日志的屏幕截图](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

   如果找到问题，请在进程服务器上的命令行中，使用 Telnet 来 ping Azure 公共 IP 地址（在上图中，IP 地址已掩码）。 可以使用端口 443 在 CBEngineCurr.currLog 文件查找 Azure 公共 IP 地址：

   `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

   如果无法连接，请检查访问问题是否由防火墙或代理设置所导致，如下一步骤中所述。

*  **检查进程服务器上的基于 IP 地址的防火墙是否阻止了访问**。

   如果在服务器上使用基于 IP 地址的防火墙规则，请下载 [Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)的完整列表。 将 IP 地址范围添加到防火墙配置，以确保防火墙允许与 Azure（以及默认的 HTTPS 端口 443）通信。 允许订阅的 Azure 区域的 IP 地址范围以及 Azure 美国西部区域的 IP 地址范围（用于访问控制和标识管理）。

*  **检查进程服务器上的基于 URL 的防火墙是否阻止了访问**。

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

*  如果 vCenter 中存在具有相同实例 UUID 的两个虚拟机，则配置服务器发现的第一个虚拟机将显示在 Azure 门户中。 若要解决此问题，请确保没有两个虚拟机具有相同的实例 UUID。
*  确保在使用 OVF 模板或统一安装程序安装配置服务器时添加正确的 vCenter 凭据。 若要验证安装期间添加的凭据是否正确，请参阅[修改用于自动发现的凭据](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery)。
*  如果未提供所需的权限来访问 vCenter，则发现虚拟机时可能会失败。 确保将[为自动发现准备帐户](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)中所述的权限添加到 vCenter 用户帐户。
*  如果虚拟机已通过 Site Recovery 进行保护，则无法在门户中选择保护该虚拟机。 确保要在门户中查找的虚拟机尚未由其他任何用户进行保护，或者位于不同的订阅下。

## <a name="protected-virtual-machines-arent-available-in-the-portal"></a>门户中不显示受保护的虚拟机

如果系统中存在重复的条目，则在 Site Recovery 下复制的虚拟机将不会显示在 Azure 门户中。 若要了解如何删除过时的条目和解决此问题，请参阅[使用 Azure Site Recovery 进行 VMware 到 Azure 的复制：如何清除重复或过时的条目](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)。

## <a name="next-steps"></a>后续步骤

如需更多帮助，请在 [Azure Site Recovery 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)中提问。 我们的社区非常活跃，将有一位工程师为你提供帮助。
