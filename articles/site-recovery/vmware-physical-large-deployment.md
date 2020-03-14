---
title: 利用 Azure Site Recovery 缩放 VMware/物理灾难恢复
description: 了解如何通过 Azure Site Recovery 为大量本地 VMware Vm 或物理服务器设置到 Azure 的灾难恢复。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 36cc63721fe003934aabfb3ae2a03a4113937ca4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256934"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>为 VMware Vm/物理服务器设置大规模灾难恢复

本文介绍如何使用[Azure Site Recovery](site-recovery-overview.md)服务设置生产环境中的大量（> 1000）的本地 VMware vm 或物理服务器的灾难恢复。


## <a name="define-your-bcdr-strategy"></a>定义 BCDR 策略

作为业务连续性和灾难恢复（BCDR）策略的一部分，可为业务应用和工作负荷定义恢复点目标（Rpo）和恢复时间目标（Rto）。 RTO 度量时间和服务级别的持续时间，在此期间，业务应用或流程必须还原并可用，以避免出现连续性问题。
- Site Recovery 为 VMware Vm 和物理服务器提供连续复制，并提供 RTO 的[SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) 。
- 当你为 VMware Vm 规划大规模灾难恢复并找出所需的 Azure 资源时，你可以指定将用于容量计算的 RTO 值。


## <a name="best-practices"></a>最佳做法

大规模灾难恢复的一些常规最佳做法。 本文的后续部分将更详细地讨论这些最佳做法。

- **确定目标要求**：在设置灾难恢复之前，估算 Azure 中的容量和资源需求。
- **规划 Site Recovery 组件**：确定需要满足估计容量的 Site Recovery 组件（配置服务器、进程服务器）。
- **设置一个或多个横向扩展进程服务器**：不要使用配置服务器上默认运行的进程服务器。 
- **运行最新更新**： Site Recovery 团队定期发布 Site Recovery 组件的新版本，你应确保运行最新版本。 为此，请跟踪更新的[新增功能](site-recovery-whats-new.md)，并在更新发布时[启用和安装更新](service-updates-how-to.md)。
- **主动监视**：在恢复并运行灾难恢复时，应主动监视复制的计算机的状态和运行状况以及基础结构资源。
- **灾难恢复演练**：应定期运行灾难恢复演练。 这不会影响你的生产环境，但请务必确保故障转移到 Azure 会在需要时按预期方式工作。



## <a name="gather-capacity-planning-information"></a>收集容量规划信息

收集有关本地环境的信息，帮助评估和估计目标（Azure）容量需求。
- 对于 VMware，请运行 VMware Vm 的部署规划器执行此操作。
- 对于物理服务器，请手动收集该信息。

### <a name="run-the-deployment-planner-for-vmware-vms"></a>为 VMware Vm 运行部署规划器

部署规划器帮助收集有关 VMware 本地环境的信息。

- 在表示 Vm 的典型变动的时间段内运行部署规划器。 这将生成更准确的估计和建议。
- 建议你在配置服务器计算机上运行部署规划器，因为 Planner 会从运行它的服务器计算吞吐量。 [详细了解](site-recovery-vmware-deployment-planner-run.md#get-throughput)如何测量吞吐量。
- 如果尚未设置配置服务器：
    - [大致了解](vmware-physical-azure-config-process-server-overview.md)Site Recovery 组件。
    - [设置配置服务器](vmware-azure-deploy-configuration-server.md)，以便在其上运行部署规划器。

然后，按如下所示运行 Planner：

1. [了解](site-recovery-deployment-planner.md)部署规划器。 可以从门户下载最新版本，也可以[直接下载](https://aka.ms/asr-deployment-planner)。
2. 查看部署规划器的[先决条件](site-recovery-deployment-planner.md#prerequisites)和[最新更新](site-recovery-deployment-planner-history.md)，并[下载并提取](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool)该工具。
3. 在配置服务器上[运行部署规划器](site-recovery-vmware-deployment-planner-run.md)。
4. [生成报告](site-recovery-vmware-deployment-planner-run.md#generate-report)以汇总估计和建议。
5. 分析[报告建议](site-recovery-vmware-deployment-planner-analyze-report.md)和[成本](site-recovery-vmware-deployment-planner-cost-estimation.md)估算。

>[!NOTE]
> 默认情况下，该工具配置为分析并生成最多1000个 Vm 的报告。 可以通过在 Asrdeploymentplanner.exe 文件中增加 MaxVMsSupported 键值来更改此限制。

## <a name="plan-target-azure-requirements-and-capacity"></a>规划目标（Azure）要求和容量

使用收集的评估和建议，可以规划目标资源和容量。 如果你运行的是 VMware Vm 的部署规划器，则可以使用多个[报告建议](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations)来帮助你。

- **兼容的 vm**：使用此数字来确定已准备好进行灾难恢复到 Azure 的 vm 的数量。 有关网络带宽和 Azure 内核的建议基于此数字。
- **所需的网络带宽**：记下兼容 vm 的增量复制所需的带宽。 
    - 当你运行 Planner 时，你可以指定所需的 RPO （分钟）。 建议会显示达到 RPO 100% 和90% 所需的带宽。 
    - 网络带宽建议考虑到规划器中建议的配置服务器和进程服务器总数所需的带宽。
- **必需的 azure 内核**：根据兼容 vm 的数量，记录目标 Azure 区域中需要的核心数。 如果没有足够的内核，故障转移 Site Recovery 无法创建所需的 Azure Vm。
- **建议的 VM 批大小**：建议的批大小基于默认情况下在72小时内完成批处理的初始复制的能力，同时满足100% 的 RPO 要求。 可以修改小时值。

可以使用这些建议来规划 Azure 资源、网络带宽和 VM 批处理。

## <a name="plan-azure-subscriptions-and-quotas"></a>规划 Azure 订阅和配额

我们想要确保目标订阅中的可用配额足以处理故障转移。

**任务** | **详细信息** | **Action**
--- | --- | ---
**检查核心** | 如果在发生故障转移时，可用配额中的内核不等于或超出目标总数，则故障转移会失败。 | 对于 VMware Vm，请检查目标订阅中是否有足够的内核，以满足部署规划器核心建议。<br/><br/> 对于物理服务器，请检查 Azure 核心是否满足手动估算。<br/><br/> 若要检查配额，请在 Azure 门户 >**订阅**中，单击 "**使用情况 + 配额**"。<br/><br/> [了解](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)有关增加配额的详细信息。
**检查故障转移限制** | 故障转移不得的数目超出了故障转移限制 Site Recovery。 |  如果故障转移超出限制，则可以添加订阅，并故障转移到多个订阅或增加订阅配额。 


### <a name="failover-limits"></a>故障转移限制

此限制表示在一小时内 Site Recovery 支持的故障转移次数，假设每台计算机有三个磁盘。

符合的含义是什么？ 若要启动 Azure VM，Azure 需要某些驱动程序处于启动开始状态，并将 DHCP 等服务设置为自动启动。
- 符合的计算机已准备好这些设置。
- 对于运行 Windows 的计算机，你可以主动检查符合性，并根据需要使它们符合要求。 [了解详细信息](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010)。
- 在故障转移时，Linux 计算机只会处于符合性状态。

**计算机符合 Azure？** | **Azure VM 限制（托管磁盘故障转移）**
--- | --- 
是 | 2000
否 | 1000

- 限制假设订阅的目标区域中正在进行的其他作业数最少。
- 某些 Azure 区域较小，可能会稍微降低限制。

## <a name="plan-infrastructure-and-vm-connectivity"></a>规划基础结构和 VM 连接

故障转移到 Azure 后，需要你的工作负荷在本地运行，并使用户能够访问在 Azure Vm 上运行的工作负荷。

- [详细了解](site-recovery-active-directory.md#test-failover-considerations)如何将 ACTIVE DIRECTORY 或 DNS 本地基础结构故障转移到 Azure。
- [详细了解](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)如何准备在故障转移后连接到 Azure vm。



## <a name="plan-for-source-capacity-and-requirements"></a>规划源容量和需求

必须拥有足够的配置服务器和横向扩展进程服务器，才能满足容量需求。 开始进行大规模部署时，请从单个配置服务器开始，并使用单个横向扩展进程服务器。 达到规定的限制时，请添加其他服务器。

>[!NOTE]
> 对于 VMware Vm，部署规划器对所需的配置和进程服务器做出一些建议。 建议使用以下过程中包含的表，而不是按照部署规划器建议操作。 


## <a name="set-up-a-configuration-server"></a>设置配置服务器
 
配置服务器容量受复制的计算机数和数据更改率的影响。 若要确定是否需要其他配置服务器，请使用这些定义的 VM 限制。

CPU | **内存** | 缓存磁盘 | **复制的计算机限制**
 --- | --- | --- | ---
8 个 vCPU<br> 2个插槽 * 4 个核心 @ 2.5 Ghz | 16 GB | 600 GB | 最多550台计算机<br> 假定每台计算机都有3个磁盘，每个磁盘 100 GB。

- 这些限制基于使用 OVF 模板设置的配置服务器。
- 此限制假设你使用的是配置服务器上默认运行的进程服务器。

如果需要添加新的配置服务器，请按照以下说明进行操作：

- 使用 OVF 模板为 VMware VM 灾难恢复[设置配置服务器](vmware-azure-deploy-configuration-server.md)。
- 为物理服务器或不能使用 OVF 模板的 VMware 部署手动[设置配置服务器](physical-azure-set-up-source.md)。

设置配置服务器时，请注意：

- 设置配置服务器时，请务必考虑该服务器所驻留的订阅和保管库，因为在安装后不应更改这些设置。 如果确实需要更改保管库，则必须将配置服务器从保管库取消关联，然后重新注册。 这会停止复制保管库中的 Vm。
- 如果要设置具有多个网络适配器的配置服务器，则应在安装过程中执行此操作。 在保管库中注册配置服务器后，不能执行此操作。

## <a name="set-up-a-process-server"></a>设置进程服务器

进程服务器容量受数据改动率的影响，而不受为复制启用的计算机数的影响。

- 对于大型部署，应始终至少具有一个横向扩展进程服务器。
- 若要确定是否需要其他服务器，请使用下表。
- 建议添加最高规范的服务器。 


CPU | **内存** | 缓存磁盘 | **变动率**
 --- | --- | --- | --- 
12 个 vCPU<br> 2个插槽 * 6 个核心 @ 2.5 Ghz | 24 GB | 1 GB | 每日最大 2 TB

设置进程服务器，如下所示：

1. 请查看[先决条件](vmware-azure-set-up-process-server-scale.md#prerequisites)。
2. 在[门户](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)中或从[命令行](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line)安装服务器。
3. 将复制的计算机配置为使用新服务器。 如果你已有复制的计算机：
    - 您可以[将](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server)整个进程服务器工作负荷移动到新的进程服务器。
    - 或者，你可以[将](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load)特定的 vm 移动到新的进程服务器。



## <a name="enable-large-scale-replication"></a>启用大规模复制

规划容量和部署所需的组件和基础结构后，为大量 Vm 启用复制。

1. 将计算机分类为批处理。 在批处理中为 Vm 启用复制，然后转到下一批。

    - 对于 VMware Vm，可以在部署规划器报表中使用[建议的 VM 批大小](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication)。
    - 对于物理计算机，我们建议你根据具有相似大小和数量的数据以及可用网络吞吐量来确定批处理。 其目标是将可能在相同时间内完成初始复制的计算机的批处理。
    
2. 如果计算机的磁盘改动较高，或超过了 Deployment thePlanner 中的限制，则可以将不需要复制的非关键文件（如日志转储或临时文件）移出计算机。 对于 VMware Vm，可以将这些文件移到单独的磁盘，然后从复制中[排除该磁盘](vmware-azure-exclude-disk.md)。
3. 启用复制之前，请检查计算机是否满足[复制要求](vmware-physical-azure-support-matrix.md#replicated-machines)。
4. 为[VMware vm](vmware-azure-set-up-replication.md#create-a-policy)或[物理服务器](physical-azure-disaster-recovery.md#create-a-replication-policy)配置复制策略。
5. 为[VMware vm](vmware-azure-enable-replication.md)或[物理服务器](physical-azure-disaster-recovery.md#enable-replication)启用复制。 这将启动所选计算机的初始复制。

## <a name="monitor-your-deployment"></a>监视部署

启动第一批 Vm 的复制后，开始监视部署，如下所示：  

1. 分配一个灾难恢复管理员来监视复制的计算机的运行状况状态。
2. [监视](site-recovery-monitor-and-troubleshoot.md)复制的项目和基础结构的事件。
3. 监视横向扩展进程服务器的[运行状况](vmware-physical-azure-monitor-process-server.md)。
4. 注册以获取事件的[电子邮件通知](https://docs.microsoft.com/azure/site-recovery/site-recovery-monitor-and-troubleshoot#subscribe-to-email-notifications)，以便更轻松地进行监视。
5. 定期执行[灾难恢复演练](site-recovery-test-failover-to-azure.md)，以确保一切按预期运行。


## <a name="plan-for-large-scale-failovers"></a>规划大规模故障转移

发生灾难时，可能需要将大量计算机/工作负荷故障转移到 Azure。 为此类型的事件做好准备，如下所示。

你可以提前准备进行故障转移，如下所示：

- [准备你的基础结构和 vm](#plan-infrastructure-and-vm-connectivity) ，以便在故障转移后能够使用工作负荷，使用户能够访问 Azure vm。
- 请注意本文档前面部分的[故障转移限制](#failover-limits)。 请确保故障转移将处于这些限制范围内。
- 运行定期[灾难恢复演练](site-recovery-test-failover-to-azure.md)。 钻取帮助：
    - 在故障转移之前查找部署中的间隙。
    - 估计应用程序的端到端 RTO。
    - 估计工作负荷的端到端 RPO。
    - 确定 IP 地址范围冲突。
    - 运行钻取时，建议不要使用生产网络进行深化，避免在生产和测试网络中使用相同的子网名称，并在每次钻取之后清理测试故障转移。

若要运行大规模故障转移，建议执行以下操作：

1. 为工作负荷故障转移创建恢复计划。
    - 每个恢复计划最多可触发50台计算机的故障转移。
    - [详细了解](recovery-plan-overview.md)恢复计划。
2. 将 Azure 自动化 runbook 脚本添加到恢复计划，以自动执行 Azure 上的任何手动任务。 典型的任务包括配置负载平衡器、更新 DNS 等。 [了解详细信息](site-recovery-runbook-automation.md)
2. 在故障转移之前，请准备 Windows 计算机以使其符合 Azure 环境。 对于符合的计算机，[故障转移限制](#plan-azure-subscriptions-and-quotas)会更高。 [了解](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010)有关 runbook 的详细信息。
4.  使用[AzRecoveryServicesAsrPlannedFailoverJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) PowerShell cmdlet 以及恢复计划触发故障转移。



## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [监视 Site Recovery](site-recovery-monitor-and-troubleshoot.md)
