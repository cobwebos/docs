---
title: 将 Windows Server/System Center VMM 2012 R2 升级到 Windows Server 2016-Azure Site Recovery
description: 了解如何使用 Azure Site Recovery 服务为 Azure Stack VM 设置到 Azure 的灾难恢复。
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: rajanaki
ms.openlocfilehash: 1d94935db542a0e64754ab8769996fe906f88b46
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954402"
---
# <a name="upgrade-windows-server-serversystem-center-2012-r2-vmm-to-windows-servervmm-2016"></a>将 Windows Server Server/System Center 2012 R2 VMM 升级到 Windows Server/VMM 2016 

本文介绍如何将使用 Azure Site Recovery 配置的 Windows Server 2012 R2 主机和 SCVMM 2012 R2 升级到 Windows Server 2016 和 SCVMM 2016

Site Recovery 有助于实现业务连续性和灾难恢复 (BCDR) 策略。 该服务可确保在出现预期内和意外中断时，VM 工作负载仍然可用。

> [!IMPORTANT]
> 升级已使用 Azure Site Recovery 进行复制配置的 Windows Server 2012 R2 主机时，必须遵循本文档中所述的步骤。 为升级选择的任何备用路径可能导致不受支持的状态，并可能导致复制中断或无法执行故障转移。


本文将会介绍如何升级环境中的以下配置：

> [!div class="checklist"]
> * **不受 SCVMM 管理的 Windows Server 2012 R2 主机** 
> * **由独立 SCVMM 2012 R2 服务器管理的 Windows Server 2012 R2 主机** 
> * **由高可用性 SCVMM 2012 R2 服务器管理的 Windows Server 2012 R2 主机**


## <a name="prerequisites--factors-to-consider"></a>先决条件和考虑因素

升级之前，请注意以下事项：-

- 如果 Windows Server 2012 R2 主机不受 SCVMM 的管理并采用独立环境设置，则尝试执行升级时，复制将会中断。
- 如果最初在安装 SCVMM 2012 R2 时选择了“不要在 Active Directory 中的分布式密钥管理下存储我的密钥”，则升级不会成功完成。

- 如果使用 System Center 2012 R2 VMM， 

    - 检查 VMM 上的数据库信息： **vmm 控制台** -> **设置** -> **常规** -> **数据库连接**
    - 检查 System Center Virtual Machine Manager 代理服务正在使用的服务帐户
    - 确保备份 VMM 数据库。
    - 记下相关 SCVMM 服务器的数据库名称。 为此，可以导航到“VMM 控制台” **“设置”** “常规” -> “数据库连接” ->  -> 
    - 记下 2012R2 主要 VMM 服务器和恢复 VMM 服务器的 VMM ID。 可以在注册表项“HKLM:\SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Setup”中找到 VMM ID。
    - 确保添加到群集的新 SCVMM 的名称与以前相同。 

- 如果在两端由 SCVMM 管理的两个站点之间复制，请确保先升级恢复端，然后再升级主要端。
  > [!WARNING]
  > 升级 SCVMM 2012 R2 时，请在分发式密钥管理下选择**在 Active Directory 中存储加密密钥**。 请慎重选择服务帐户和分布式密钥管理的设置。 根据所做的选择，加密的数据（例如模板中的密码）在升级后可能不可用，并可能会影响使用 Azure Site Recovery 进行的复制

> [!IMPORTANT]
> 请参阅[先决条件](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#requirements-and-limitations)的详细 SCVMM 文档

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>不受 SCVMM 管理的 Windows Server 2012 R2 主机 
下述步骤列表适用于遵循[此教程](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-architecture)执行的、[从 Hyper-V 主机到 Azure 的复制](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial)的用户配置

> [!WARNING]
> 如先决条件中所述，这些步骤仅适用于群集环境方案，而不适用于独立 Hyper-V 主机配置。

1. 按步骤执行[群集滚动升级](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process)。 执行群集滚动升级过程。
2. 对于群集中引入的每个新 Windows Server 2016 主机，请遵循 [此处] 所述的步骤，从 Azure Site Recovery 中删除 Windows Server 2012 R2 主机的引用。 这应该是你选择从群集中排出和逐出的主机。
3. 针对所有虚拟机执行 *Update-VMVersion* 命令后，升级即告完成。 
4. 使用[此处](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-the-source-environment)所述的步骤将新的 Windows Server 2016 主机注册到 Azure Site Recovery。 请注意，Hyper-V 站点已处于活动状态，你只需在群集中注册新主机。 
5.  转到 Azure 门户并在恢复服务中检查复制运行状态

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>升级由独立 SCVMM 2012 R2 服务器管理的 Windows Server 2012 R2 主机
升级 Windows Sever 2012 R2 主机之前，需将 SCVMM 2012 R2 升级到 SCVMM 2016。 遵循以下步骤：-

**将独立 SCVMM 2012 R2 升级到 SCVMM 2016**

1.  导航到“控制面板”->“程序”->“程序和功能”->“Microsoft Azure Site Recovery”并单击“卸载”，以卸载 ASR 提供程序
2. [保留 SCVMM 数据库并升级操作系统](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#back-up-and-upgrade-the-operating-system)
3. 在“添加/删除程序”中，选择“VMM” **“卸载”。**  >  b. 依次选择“删除功能”、“VMM 管理服务器和 VMM 控制台”。 c. 在“数据库选项”中，选择“保留数据库”。 d. 查看摘要，然后单击“卸载”。

4. [安装 VMM 2016](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#install-vmm-2016)
5. 在 "**构造**" 选项卡中启动 SCVMM 并检查每个主机的状态。单击 "**刷新**" 以获取最新状态。 应会看到状态为“需要注意”。 
17. 在 SCVMM 上安装最新的 [Microsoft Azure Site Recovery 提供程序](https://aka.ms/downloaddra)。
16. 在群集的每个主机上安装最新的 [Microsoft Azure 恢复服务 (MARS) 代理](https://aka.ms/latestmarsagent)。 刷新以确保 SCVMM 能够成功查询主机。

**将 Windows Server 2012 R2 主机升级到 Windows Server 2016**

1. 遵循[此处](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process)所述的步骤执行群集滚动升级过程。 
2. 将新主机添加到群集后，在 SCVMM 控制台中刷新主机，以便在此更新的主机上安装 VMM 代理。
3. 执行 *Update-VMVersion* 以更新虚拟机的 VM 版本。 
4.  转到 Azure 门户，检查恢复服务保管库中虚拟机的复制运行状态。 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>升级由高可用性 SCVMM 2012 R2 服务器管理的 Windows Server 2012 R2 主机
升级 Windows Sever 2012 R2 主机之前，需将 SCVMM 2012 R2 升级到 SCVMM 2016。 升级使用 Azure Site Recovery 配置的 SCVMM 2012 R2 服务器时支持以下升级模式 - 不包括附加 VMM 服务器的混合模式，以及包括附加 VMM 服务器的混合模式。

**将 SCVMM 2012 R2 升级到 SCVMM 2016**

1.  导航到“控制面板”->“程序”->“程序和功能”->“Microsoft Azure Site Recovery”并单击“卸载”，以卸载 ASR 提供程序
2. 根据想要执行的升级模式遵循[此处](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#upgrade-a-standalone-vmm-server)所述的步骤。
3. 启动 SCVMM 控制台并检查 "**构造**" 选项卡下的每个主机的状态。单击 "**刷新**" 以获取最新状态。 应会看到状态为“需要注意”。
4. 在 SCVMM 上安装最新的 [Microsoft Azure Site Recovery 提供程序](https://aka.ms/downloaddra)。
5. 在群集的每个主机上更新到最新的 [Microsoft Azure 恢复服务 (MARS) 代理](https://aka.ms/latestmarsagent)。 刷新以确保 SC VMM 能够成功查询主机。


**将 Windows Server 2012 R2 主机升级到 Windows Server 2016**

1. 遵循[此处](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process)所述的步骤执行群集滚动升级过程。
2. 将新主机添加到群集后，在 SCVMM 控制台中刷新主机，以便在此更新的主机上安装 VMM 代理。
3. 执行 *Update-VMVersion* 以更新虚拟机的 VM 版本。 
4.  转到 Azure 门户，检查恢复服务保管库中虚拟机的复制运行状态。 

## <a name="next-steps"></a>后续步骤
执行主机升级后，可以执行[测试故障转移](tutorial-dr-drill-azure.md)，以测试复制运行状况和灾难恢复状态。

