---
title: 利用 Azure Site Recovery 缩放 VMware/物理灾难恢复
description: 了解如何使用 Azure Site Recovery 将大量本地 VMware VM 或物理服务器设置为灾难恢复到 Azure。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: e08c7d5f794611a92688e931f35da7482c04407f
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082225"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>为 VMware VM/物理服务器设置大规模灾难恢复

本文介绍如何使用 [Azure Site Recovery](site-recovery-overview.md) 服务将生产环境中的大量（1000 台以上）本地 VMware VM 或物理服务器设置为灾难恢复到 Azure。


## <a name="define-your-bcdr-strategy"></a>定义 BCDR 策略

在业务连续性和灾难恢复 (BCDR) 策略中，定义业务应用和工作负荷的恢复点目标 (RPO) 与恢复时间目标 (RTO)。 RTO 衡量业务应用或流程为了避免出现连续性问题，必须能够在多长时间内还原并保持可用性服务级别。
- Site Recovery 为 VMware VM 和物理服务器提供连续复制，并提供 RTO 方面的 [SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)。
- 在规划 VMware VM 的大规模灾难恢复以及测算所需的 Azure 资源时，可以指定用于容量计算的 RTO 值。


## <a name="best-practices"></a>最佳实践

适用于大规模灾难恢复的一些常规最佳做法。 本文档的后续几个部分将更详细地讨论这些最佳做法。

- **确定目标要求**：在设置灾难恢复之前，估算 Azure 中的容量和资源需求。
- **规划 Site Recovery 组件**：确定需要满足估计容量的 Site Recovery 组件（配置服务器、进程服务器）。
- **设置一个或多个横向扩展进程服务器**：不要使用配置服务器上默认运行的进程服务器。 
- **运行最新更新**： Site Recovery 团队定期发布 Site Recovery 组件的新版本，你应确保运行最新版本。 为帮助做到这一点，请在[新增功能](site-recovery-whats-new.md)中跟踪更新，在发布后[启用并安装更新](service-updates-how-to.md)。
- **主动监视**：在恢复并运行灾难恢复时，应主动监视复制的计算机的状态和运行状况以及基础结构资源。
- **灾难恢复演练**：应定期运行灾难恢复演练。 这些演练不会影响生产环境，但有助于确保在必要时可按预期故障转移到 Azure。



## <a name="gather-capacity-planning-information"></a>收集容量规划信息

收集有关本地环境的信息，以帮助评估目标 (Azure) 容量需求。
- 对于 VMware，可运行适用于 VMware VM 的部署规划器来实现此目的。
- 对于物理服务器，可手动收集信息。

### <a name="run-the-deployment-planner-for-vmware-vms"></a>运行适用于 VMware VM 的部署规划器

部署规划器可帮助你收集有关 VMware 本地环境的信息。

- 在 VM 出现典型变动情况的时间段运行部署规划器。 这会生成更准确的估算值和建议。
- 我们建议在配置服务器计算机上运行部署规划器，因为规划器将计算其运行所在的服务器的吞吐量。 [详细了解](site-recovery-vmware-deployment-planner-run.md#get-throughput)如何测量吞吐量。
- 如果尚未设置配置服务器：
    - [获取 Site Recovery 组件的概述](vmware-physical-azure-config-process-server-overview.md)。
    - [设置配置服务器](vmware-azure-deploy-configuration-server.md)，以在其上运行部署规划器。

然后按如下所示运行规划器：

1. [了解](site-recovery-deployment-planner.md)部署规划器。 可以从门户下载最新版本，或者[直接下载](https://aka.ms/asr-deployment-planner)。
2. 请查看部署规划器的[先决条件](site-recovery-deployment-planner.md#prerequisites)和[最新更新](site-recovery-deployment-planner-history.md)，然后[下载并提取](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool)该工具。
3. 在配置服务器上[运行部署规划器](site-recovery-vmware-deployment-planner-run.md)。
4. [生成报告](site-recovery-vmware-deployment-planner-run.md#generate-report)以汇总估算值和建议。
5. 分析[报告建议](site-recovery-vmware-deployment-planner-analyze-report.md)和[成本估算](site-recovery-vmware-deployment-planner-cost-estimation.md)。

>[!NOTE]
> 默认情况下，此工具配置为在分析后为最多 1000 个 VM 生成报告。 若要更改此限制，可以增大 ASRDeploymentPlanner.exe.config 文件中的 MaxVMsSupported 键值。

## <a name="plan-target-azure-requirements-and-capacity"></a>规划目标 (Azure) 要求和容量

使用收集的估算值和建议，可以规划目标资源和容量。 如果已运行适用于 VMware VM 的部署规划器，可以借助许多的[报告建议](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations)。

- **兼容的 vm**：使用此数字来确定已准备好进行灾难恢复到 Azure 的 vm 的数量。 有关网络带宽和 Azure 核心数的建议基于此数字。
- **所需的网络带宽**：记下兼容 vm 的增量复制所需的带宽。 
    - 运行规划器时，应指定所需的 RPO（以分钟为单位）。 建议中会显示符合该 RPO 时间的 100% 和 90% 所需的带宽。 
    - 网络带宽建议考虑到了规划器中建议的所有配置服务器和进程服务器所需的带宽。
- **必需的 azure 内核**：根据兼容 vm 的数量，记录目标 Azure 区域中需要的核心数。 如果没有足够的核心，在故障转移时，Site Recovery 将无法创建所需的 Azure VM。
- **建议的 VM 批大小**：建议的批大小基于默认情况下在72小时内完成批处理的初始复制的能力，同时满足100% 的 RPO 要求。 可以修改小时值。

使用这些建议可以规划 Azure 资源、网络带宽和 VM 批处理。

## <a name="plan-azure-subscriptions-and-quotas"></a>规划 Azure 订阅和配额

我们希望确保目标订阅中的可用配额足以应对故障转移。

**任务** | **详细信息** | **操作**
--- | --- | ---
**检查核心数** | 如果可用配额中的核心数少于故障转移时的目标总数，故障转移将会失败。 | 对于 VMware VM，请检查目标订阅中是否有足够的核心，与部署规划器的核心建议相符。<br/><br/> 对于物理服务器，请检查 Azure 核心数是否符合人工估算结果。<br/><br/> 若要检查配额，请在 Azure 门户中依次单击“订阅”、“用量 + 配额”。<br/><br/> [详细了解](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)如何提高配额。
**检查故障转移限制** | 故障转移次数不得超过 Site Recovery 的故障转移限制。 |  如果故障转移次数超过限制，你可以添加订阅并故障转移到多个订阅，或者提高订阅的配额。 


### <a name="failover-limits"></a>故障转移限制

限制是指 Site Recovery 在一小时内支持的故障转移次数（假设每台计算机包含三个磁盘）。

“符合条件”是什么意思？ 若要启动 Azure VM，Azure 要求某些驱动程序处于引导/启动状态，并将 DHCP 等服务设置为自动启动。
- 符合条件的计算机已采用这些设置。
- 对于运行 Windows 的计算机，可以主动检查符合性，并根据需要使其符合条件。 [了解详细信息](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010)。
- 对于 Linux 计算机，只能在故障转移时使其符合条件。

**计算机是否符合 Azure 条件？** | **Azure VM 限制（托管磁盘故障转移）**
--- | --- 
是 | 2000
否 | 1000

- 限制假设订阅的目标区域中只有其他极少量的作业正在进行。
- 某些 Azure 区域的规模较小，因此限制可能略低一些。

## <a name="plan-infrastructure-and-vm-connectivity"></a>规划基础结构和 VM 连接

故障转移到 Azure 后，需要让工作负荷像在本地一样正常运行，并使用户能够访问 Azure VM 上运行的工作负荷。

- [详细了解](site-recovery-active-directory.md#test-failover-considerations)如何将 Active Directory 或 DNS 本地基础结构故障转移到 Azure。
- [详细了解](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)如何准备好在故障转移后连接到 Azure VM。



## <a name="plan-for-source-capacity-and-requirements"></a>规划源容量和要求

重要的一点是，必须提供足够的配置服务器和横向扩展进程服务器来满足容量要求。 开始大规模部署时，请先从一台配置服务器和一台横向扩展进程服务器着手。 达到规定的限制后，添加更多的服务器。

>[!NOTE]
> 对于 VMware VM，部署规划器将在所需的配置服务器和进程服务器方面提供一些建议。 我们建议使用以下过程中包含的表格，而不要遵循部署规划器的建议。 


## <a name="set-up-a-configuration-server"></a>设置配置服务器
 
配置服务器容量受启用复制的计算机数目的影响，而不受数据变动率的影响。 若要确定是否需要更多的配置服务器，请参考定义的这些 VM 限制。

**CPU** | **内存** | **缓存磁盘** | **复制的计算机限制**
 --- | --- | --- | ---
8 个 vCPU<br> 2 个插槽 * 4 个核心 @ 2.5 GHz | 16 GB | 600 GB | 最多 550 台计算机<br> 假设每台计算机有 3 个 100 GB 的磁盘。

- 这些限制基于使用 OVF 模板设置的配置服务器。
- 这些限制假设不是使用配置服务器上默认运行的进程服务器。

如果需要添加新的配置服务器，请遵照以下说明操作：

- 使用 OVF 模板为 VMware VM 灾难恢复[设置配置服务器](vmware-azure-deploy-configuration-server.md)。
- 为物理服务器或者无法使用 OVF 模板的 VMware 部署手动[设置配置服务器](physical-azure-set-up-source.md)。

设置配置服务器时，请注意：

- 设置配置服务器时，必须考虑到该服务器所在的订阅和保管库，因为设置后不能更改订阅和保管库。 如果确实需要更改保管库，必须从保管库取消关联配置服务器，然后重新注册该服务器。 这会停止保管库中的 VM 复制。
- 若要设置包含多个网络适配器的配置服务器，应在设置期间执行此操作。 将配置服务器注册到保管库中后，无法执行此操作。

## <a name="set-up-a-process-server"></a>设置进程服务器

进程服务器容量受数据变动率的影响，而不受启用复制的计算机数目的影响。

- 对于大型部署，始终应该至少提供一台横向扩展进程服务器。
- 若要确定是否需要更多的服务器，请参考下表。
- 我们建议添加最高规格的服务器。 


**CPU** | **内存** | **缓存磁盘** | **变动率**
 --- | --- | --- | --- 
12 个 vCPU<br> 2 个插槽 * 6 个核心 @ 2.5 GHz | 24 GB | 1 GB | 每天最大 2 TB

按如下所述设置进程服务器：

1. 请查看[先决条件](vmware-azure-set-up-process-server-scale.md#prerequisites)。
2. 通过[门户](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)或[命令行](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line)安装该服务器。
3. 将复制的计算机配置为使用新服务器。 如果已启用计算机复制：
    - 可将整个进程服务器工作负荷[移动](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server)到新的进程服务器。
    - 或者，可将特定的 VM [移动](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load)到新的进程服务器。



## <a name="enable-large-scale-replication"></a>启用大规模复制

规划容量并部署所需的组件和基础结构之后，为大量的 VM 启用复制。

1. 将计算机排序成批。 为一个批中的 VM 启用复制，然后转到下一批。

    - 对于 VMware VM，可以使用部署规划器报告中[建议的 VM 批大小](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication)。
    - 对于物理计算机，我们建议根据大小和数据量类似的计算机以及可用的网络吞吐量来标识批。 目的是将有可能在大致相同的时间内完成初始复制的计算机分批。
    
2. 如果某台计算机的磁盘变动率较高或超过部署规划器中的限制，则你可以将不需要复制的非关键文件（例如日志转储或临时文件）移出该计算机。 对于 VMware VM，可将这些文件移到单独的磁盘，然后从复制项中[排除该磁盘](vmware-azure-exclude-disk.md)。
3. 在启用复制之前，请检查计算机是否满足[复制要求](vmware-physical-azure-support-matrix.md#replicated-machines)。
4. 为 [VMware VM](vmware-azure-set-up-replication.md#create-a-policy) 或[物理服务器](physical-azure-disaster-recovery.md#create-a-replication-policy)配置复制策略。
5. 为 [VMware VM](vmware-azure-enable-replication.md) 或[物理服务器](physical-azure-disaster-recovery.md#enable-replication)启用复制。 这会启动所选计算机的初始复制。

## <a name="monitor-your-deployment"></a>监视部署

启动第一批 VM 的复制后，按如下所述开始监视部署：  

1. 分配一名灾难恢复管理员来监视复制的计算机的运行状态。
2. 监视复制项和基础结构的[事件](site-recovery-monitor-and-troubleshoot.md)。
3. 监视横向扩展进程服务器的[运行状况](vmware-physical-azure-monitor-process-server.md)。
4. 注册接收事件的[电子邮件通知](https://docs.microsoft.com/azure/site-recovery/site-recovery-monitor-and-troubleshoot#subscribe-to-email-notifications)，以便于监视。
5. 定期开展[灾难恢复演练](site-recovery-test-failover-to-azure.md)，以确保一切按预期方式进行。


## <a name="plan-for-large-scale-failovers"></a>规划大规模故障转移

发生灾难时，你可能需要将大量计算机/工作负荷故障转移到 Azure。 按如下所述准备应对此类事件。

可按如下所述提前准备好故障转移：

- [准备基础结构和 VM](#plan-infrastructure-and-vm-connectivity)，以便在故障转移后工作负荷可用，并且用户可以访问 Azure VM。
- 请注意本文档前面所述的[故障转移限制](#failover-limits)。 确保故障转移在这些限制范围内进行。
- 定期运行[灾难恢复演练](site-recovery-test-failover-to-azure.md)。 演练可以帮助：
    - 在故障转移之前发现部署中的不足。
    - 估算应用的端到端 RTO。
    - 估算工作负荷的端到端 RPO。
    - 识别 IP 地址范围冲突。
    - 运行演练时，我们建议不要使用生产网络，避免在生产和测试网络中使用相同的子网名称，并在每次演练后清理测试故障转移。

若要运行大规模故障转移，我们建议：

1. 为工作负荷故障转移创建恢复计划。
    - 每个恢复计划最多可以触发 50 台计算机的故障转移。
    - [详细了解](recovery-plan-overview.md)恢复计划。
2. 将 Azure 自动化 Runbook 脚本添加到恢复计划，以将 Azure 上的任何手动任务自动化。 典型的任务包括配置负载均衡器、更新 DNS，等等。 [了解详细信息](site-recovery-runbook-automation.md)
2. 在故障转移之前，请准备好 Windows 计算机，使之符合 Azure 环境的条件。 符合条件的计算机的[故障转移限制](#plan-azure-subscriptions-and-quotas)更高。 [详细了解](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) Runbook。
4.  结合恢复计划使用 [Start-AzRecoveryServicesAsrPlannedFailoverJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) PowerShell cmdlet 触发故障转移。



## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [监视 Site Recovery](site-recovery-monitor-and-troubleshoot.md)
