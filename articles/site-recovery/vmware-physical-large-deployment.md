---
title: 设置到 Azure 的 VMware Vm 或物理服务器的较大数字为使用 Azure Site Recovery 的灾难恢复 |Microsoft Docs
description: 了解如何设置到 Azure 的本地 VMware Vm 或物理服务器的较大数字为使用 Azure Site Recovery 的灾难恢复。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: raynew
ms.openlocfilehash: e96aafe61c0d8547ffca9e97bfd9e90c9529155f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66237265"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>设置 VMware Vm/物理服务器对大规模的灾难恢复

本文介绍如何设置灾难恢复到 Azure 的大量 (> 1000) 的本地 VMware Vm 或物理服务器在生产环境中，使用[Azure Site Recovery](site-recovery-overview.md)服务。


## <a name="define-your-bcdr-strategy"></a>定义 BCDR 策略

作为你的业务连续性和灾难恢复 (BCDR) 策略的一部分，您可以定义恢复点目标 (Rpo) 和业务应用和工作负荷的恢复时间目标 (Rto)。 RTO 用于度量的持续时间的时间和服务级别在其中将企业应用程序或进程必须是还原并可用，以避免连续性问题。
- Site Recovery 提供连续复制 VMware Vm 和物理服务器和一个[SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)的 RTO。
- 在进行大规模的灾难恢复规划的 VMware Vm 和找出所需的 Azure 资源时，可以指定将用于容量计算的 RTO 值。


## <a name="best-practices"></a>最佳做法

大范围灾难中恢复一些常规最佳做法。 文档的以下各节中更详细地讨论了这些最佳实践。

- **确定目标要求**:设置灾难恢复之前，估计出在 Azure 中的容量和资源需求。
- **Site Recovery 组件计划**:找出哪些 Site Recovery 组件 （配置服务器、 进程服务器） 必须满足你的估计的容量。
- **设置一个或多个横向扩展进程服务器**:不要使用默认运行配置服务器的进程服务器。 
- **运行最新的更新**:Site Recovery 团队发布新版本的 Site Recovery 组件定期进行，并应确保正在运行的最新版本。 若要帮助解决此问题，跟踪[新增功能](site-recovery-whats-new.md)进行更新，并[启用并安装更新](service-updates-how-to.md)发布。
- **主动监视**:与启动和运行获取灾难恢复，你应主动监视状态和复制的机和基础结构资源的运行状况。
- **灾难恢复演练**:你应定期运行灾难恢复演练。 这些不会影响在生产环境中，但将有助于确保该故障转移到 Azure 将正常工作，在需要时。



## <a name="gather-capacity-planning-information"></a>收集容量规划信息

收集有关你的本地环境，以帮助评估并评估您目标 (Azure) 的容量需求的信息。
- 对于 VMware，运行 Deployment Planner 将 VMware Vm，若要执行此操作。
- 对于物理服务器，手动收集的信息。

### <a name="run-the-deployment-planner-for-vmware-vms"></a>运行 Deployment Planner 将 VMware Vm

部署规划器可帮助你收集有关您的 VMware 的本地环境的信息。

- 在表示为您的 Vm 的典型改动期间运行部署规划器。 这将生成更准确的估计值和建议。
- 我们建议你运行配置服务器计算机上部署规划器因为 Planner 计算在其运行的服务器的吞吐量。 [了解详细信息](site-recovery-vmware-deployment-planner-run.md#get-throughput)有关测量吞吐量。
- 如果尚不具有配置服务器设置：
    - [大致了解](vmware-physical-azure-config-process-server-overview.md)Site Recovery 组件。
    - [设置配置服务器](vmware-azure-deploy-configuration-server.md)，以便在其上运行部署规划器。

然后运行 Planner，如下所示：

1. [了解有关](site-recovery-deployment-planner.md)部署规划器。 可以在门户中，下载最新版本或[直接下载](https://aka.ms/asr-deployment-planner)。
2. 评审[先决条件](site-recovery-deployment-planner.md#prerequisites)并[最新的更新](site-recovery-deployment-planner-history.md)对于部署规划器，并[下载并提取](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool)该工具。
3. [运行部署规划器](site-recovery-vmware-deployment-planner-run.md)配置服务器上。
4. [生成报告](site-recovery-vmware-deployment-planner-run.md#generate-report)汇总估计和建议。
5. 分析[报告建议](site-recovery-vmware-deployment-planner-analyze-report.md)并[成本估计](site-recovery-vmware-deployment-planner-cost-estimation.md)。

>[!NOTE]
> 默认情况下，该工具配置为进行配置，并为最多 1000 个 Vm 生成报表。 可以通过增加 ASRDeploymentPlanner.exe.config 文件中的 MaxVMsSupported 项值来更改此限制。

## <a name="plan-target-azure-requirements-and-capacity"></a>计划目标 (Azure) 的要求和容量

使用收集到的估算和建议，您可以计划的目标资源和容量。 如果将 VMware vm 运行部署规划器，可以使用的许多[报告建议](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations)以帮助您。

- **兼容的 Vm**:使用此号码来标识已准备好进行灾难恢复到 Azure Vm 的数目。 有关网络带宽和 Azure 核心数的建议基于此数字。
- **所需网络带宽**:请注意有关的兼容 Vm 的增量复制所需的带宽。 
    - 运行规划器时在几分钟内将指定的所需的 RPO。 建议向您展示了所需带宽来满足 100%和 90%的时间的 RPO。 
    - 网络带宽建议考虑到所需的配置服务器和进程服务器在 Planner 中建议的总数的带宽。
- **所需 Azure 核心**:请注意在目标 Azure 区域中所需的内核数基于兼容的 Vm 数。 如果你没有足够的内核，在故障转移时 Site Recovery 将无法创建所需的 Azure Vm。
- **建议的 VM 批大小**:推荐的批次大小取决于完成初始复制批处理在 72 小时内的功能默认情况下，同时可以满足 100%的 RPO。 可以修改的小时值。

这些建议可用于 Azure 资源、 网络带宽和 VM 批处理的计划。

## <a name="plan-azure-subscriptions-and-quotas"></a>规划 Azure 订阅和配额

我们想要确保在目标订阅中的可用配额不足，无法处理故障转移。

**任务** | **详细信息** | **Action**
--- | --- | ---
**检查内核** | 如果中的可用配额的内核不等于或超过总目标计数在故障转移时，故障转移将失败。 | 对于 VMware Vm，请检查以满足部署规划器核心建议在目标订阅中有足够的核心。<br/><br/> 对于物理服务器，检查 Azure 核心满足您手动的估计数据。<br/><br/> 若要检查配额，请在 Azure 门户 >**订阅**，单击**使用情况 + 配额**。<br/><br/> [了解详细信息](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)有关提高配额。
**检查故障转移的限制** | 故障转移数不得超过 Site Recovery 故障转移的限制。 |  如果故障转移会超出其限制，可以添加订阅，并故障转移到多个订阅，或针对订阅的配额。 


### <a name="failover-limits"></a>故障转移的限制

限制指示故障转移支持的 Site Recovery 在 1 小时内假定每台计算机的三个磁盘的数量。

什么没有是否符合平均值？ 若要启动 Azure VM，Azure 需要处于启动启动状态，某些驱动程序和类似于 DHCP 设置为自动启动服务。
- 符合的计算机已经具有这些设置中的位置。
- 对于运行 Windows 的计算机，可以主动检查符合性，并使它们符合必要。 [了解详细信息](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010)。
- Linux 计算机只能进入符合性的故障转移的时间。

**计算机是否符合 Azure？** | **Azure VM 限制 （托管的磁盘故障转移）**
--- | --- 
是 | 2000
否 | 1000

- 假定其他作业正在进行中的订阅在目标区域中的最小限制。
- 某些 Azure 区域更小，并且可能略有较低的限制。

## <a name="plan-infrastructure-and-vm-connectivity"></a>规划基础结构和 VM 连接

故障转移到 Azure 后所需工作负荷运行，像在本地，并使用户能够访问 Azure Vm 上运行的工作负荷。

- [了解详细信息](site-recovery-active-directory.md#test-failover-considerations)大约故障转移到 Azure 在 Active Directory 或 DNS 的本地基础结构。
- [了解详细信息](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)有关准备故障转移后连接到 Azure Vm。



## <a name="plan-for-source-capacity-and-requirements"></a>规划源容量和要求

非常重要，您有足够的配置服务器和横向扩展进程服务器以满足容量需求。 当您开始在大规模部署，开始使用一个配置服务器和一个横向扩展进程服务器。 在达到规定的限制，添加其他服务器。

>[!NOTE]
> VMware Vm 的部署规划器可以配置服务器和进程服务器所需的建议。 我们建议使用以下过程中，而不是按照部署规划器建议中包含的表。 


## <a name="set-up-a-configuration-server"></a>设置配置服务器
 
配置服务器容量的复制的计算机数会影响并不是由数据变动率。 若要确定是否需要额外的配置服务器，使用这些定义的 VM 的限制。

CPU  | 内存  | 缓存磁盘  | **复制计算机限制**
 --- | --- | --- | ---
8 个 vCPU<br> 2 套接字 * 4 个核心 @ 2.5 Ghz | 16 GB | 600 TB | 最多 550 计算机<br> 假定每台计算机具有三个磁盘 100 gb。

- 这些限制基于使用 OVF 模板设置的配置服务器。
- 限制假定你未使用的配置服务器默认情况下运行的进程服务器。

如果需要添加新的配置服务器，请按照这些说明操作：

- [设置配置服务器](vmware-azure-deploy-configuration-server.md)以进行 VMware VM 灾难恢复，使用 OVF 模板。
- [设置配置服务器](physical-azure-set-up-source.md)手动为物理服务器，或不能使用 OVF 模板的 VMware 部署。

如您将配置服务器设置，请注意：

- 如果设置了配置服务器时，务必考虑订阅和保管库驻留在其中它，因为不应在安装完成后更改这些。 如果需要更改在保管库，你必须取消关联配置服务器从保管库，然后重新注册它。 这会停止 Vm 的保管库中的复制。
- 如果你想要设置具有多个网络适配器的配置服务器，您应期间执行此操作组。 无法执行此操作后将配置服务器注册到保管库中。

## <a name="set-up-a-process-server"></a>设置进程服务器

进程服务器容量会影响由数据改动率，而不是为复制启用的计算机的数目。

- 对于大型部署应始终具有至少一个横向扩展进程服务器。
- 若要找出是否需要更多服务器，请使用下表。
- 我们建议您添加具有最高规格的服务器。 


CPU  | 内存  | 缓存磁盘  | **变动率**
 --- | --- | --- | --- 
12 个 vCPU<br> 2 套接字 * 6 个核心 @ 2.5 Ghz | 24 GB | 1 GB | 一天最多 2 TB

设置进程服务器，如下所示：

1. 请查看[先决条件](vmware-azure-set-up-process-server-scale.md#prerequisites)。
2. 安装中的服务器[门户](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)，或从[命令行](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line)。
3. 将使用新的服务器的复制的计算机的配置。 如果你已复制的计算机：
    - 你可以[移动](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server)到新的进程服务器的整个过程服务器工作负荷。
    - 此外，也可以[移动](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load)特定的 vm 转移到新的进程服务器。



## <a name="enable-large-scale-replication"></a>启用大规模复制

规划容量和部署所需的组件和基础结构之后, 启用大量的虚拟机的复制。

1. 对计算机进行排序为多个批。 为 Vm 启用复制的批处理中，然后转到下一批。

    - 对于 VMware Vm，可以使用[建议的 VM 批大小](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication)部署规划器报表中。
    - 对于物理计算机，建议你确定基于具有相似的大小和数据量的计算机和可用的网络吞吐量的批处理。 目标是时间的为批处理可能完成量相同中的其初始复制的计算机。
    
2. 如果为机的磁盘变动量很高，或超过中部署 thePlanner 的限制，可以移动无需关闭计算机 （如日志转储或临时文件） 复制非关键文件。 对于 VMware Vm，你可以将这些文件移到一个单独的磁盘，然后[排除该磁盘](vmware-azure-exclude-disk.md)从复制。
3. 启用复制之前，请检查计算机满足[复制要求](vmware-physical-azure-support-matrix.md#replicated-machines)。
4. 配置复制策略以便[VMware Vm](vmware-azure-set-up-replication.md#create-a-policy)或[物理服务器](physical-azure-disaster-recovery.md#create-a-replication-policy)。
5. 为启用复制[VMware Vm](vmware-azure-enable-replication.md)或[物理服务器](physical-azure-disaster-recovery.md#enable-replication)。 这将启动所选计算机的初始复制。

## <a name="monitor-your-deployment"></a>监视部署

启动第一批 Vm 的复制后，开始监视你的部署，如下所示：  

1. 分配的灾难恢复管理员，来监视复制的计算机的运行状况状态。
2. [监视事件](site-recovery-monitor-and-troubleshoot.md)复制的项和基础结构。
3. [监视运行状况](vmware-physical-azure-monitor-process-server.md)的横向扩展进程服务器。
4. 注册以获取[电子邮件通知](https://docs.microsoft.com/azure/site-recovery/site-recovery-monitor-and-troubleshoot#subscribe-to-email-notifications)的事件，用于更轻松地监视。
5. 执行常规[灾难恢复演练](site-recovery-test-failover-to-azure.md)，以确保一切按预期方式工作。


## <a name="plan-for-large-scale-failovers"></a>对于大规模的故障转移的计划

在灾难的情况下，您可能需要进行故障转移大量的计算机/工作负荷到 Azure。 按如下所示准备此类型的事件。

你可以提前准备进行故障转移，如下所示：

- [准备你的基础结构和 Vm](#plan-infrastructure-and-vm-connectivity) ，以便将故障转移后，提供你的工作负载和，以便用户可以访问 Azure Vm。
- 请注意[故障转移限制](#failover-limits)本文档前面。 请确保在故障转移将位于这些限制内。
- 常规运行[灾难恢复演练](site-recovery-test-failover-to-azure.md)。 钻取到帮助：
    - 查找到的故障转移之前在部署中的部分。
    - 估计您的应用程序的端到端 RTO。
    - 估计工作负荷的端到端 RPO。
    - 标识 IP 地址范围冲突。
    - 在运行时演练，我们建议不用于钻取的生产网络，避免在生产和测试网络中使用的相同子网名称，在每个钻取后清理测试故障转移。

若要运行的大规模故障转移，我们建议：

1. 创建工作负荷故障转移的恢复计划。
    - 每个恢复计划可以触发最多 50 个计算机的故障转移。
    - [详细了解](recovery-plan-overview.md)恢复计划。
2. 将 Azure 自动化 runbook 脚本添加到恢复计划来自动执行 Azure 上的任何手动任务。 典型任务包括配置负载均衡器，更新 DNS 等。 [了解详细信息](site-recovery-runbook-automation.md)
2. 故障转移前，准备 Windows 计算机，以便它们都遵循 Azure 环境。 [故障转移限制](#plan-azure-subscriptions-and-quotas)是符合的计算机的更高版本。 [了解详细信息](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010)有关 runbook。
4.  触发故障转移[开始 AzRecoveryServicesAsrPlannedFailoverJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) PowerShell cmdlet，同时使用恢复计划。



## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [监视 Site Recovery](site-recovery-monitor-and-troubleshoot.md)
