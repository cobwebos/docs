---
title: Azure Migrate 中的依赖项可视化
description: 概述中的服务器评估服务中的评估计算 Azure Migrate
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: fd069ed98fa34fd6f281c98a061925f96c7bb2cd
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269700"
---
# <a name="dependency-visualization"></a>依赖项可视化

本文介绍 Azure Migrate：服务器评估中的依赖项可视化。

## <a name="what-is-dependency-visualization"></a>什么是依赖项可视化？

依赖关系可视化可帮助确定要评估并迁移到 Azure 的本地计算机之间的依赖关系。 

- 在 Azure Migrate：服务器评估中，将计算机收集到一个组中，然后对该组进行评估。 依赖关系可视化可以更准确地对计算机分组，并为评估提供较高的置信度。
- 通过依赖项可视化，你可以确定必须一起迁移的计算机。 你可以确定计算机是否正在使用，或者是否可以取消使用（而不是迁移）。
- 可视化依赖项有助于确保不会遗留任何内容，并避免在迁移过程中出现意外中断。
- 如果不确定计算机是否是要迁移到 Azure 的应用部署的一部分，则可视化效果特别有用。


> [!NOTE]
> 依赖关系可视化在 Azure 政府版中不可用。

## <a name="agent-basedagentless-visualization"></a>基于代理/无代理可视化

可通过两个选项来部署依赖关系可视化：

- **基于代理**的依赖项可视化要求在要分析的每台本地计算机上安装代理。
- **无代理**：利用此选项，无需在要交叉检查的计算机上安装代理。 此选项目前为预览版，仅适用于 VMware Vm。


## <a name="agent-based-visualization"></a>基于代理的可视化

**要求** | **详细信息** | **了解详细信息**
--- | --- | ---
**部署之前** | 您应该准备好一个 Azure Migrate 项目，并将 Azure Migrate： Server 评估工具添加到项目。<br/><br/>  将 Azure Migrate 设备设置为发现本地计算机后，部署依赖关系可视化。 | [了解如何](create-manage-projects.md)首次创建项目。<br/><br/> [了解如何](how-to-assess.md)向现有项目添加评估工具。<br/><br/> 了解如何设置 Azure Migrate 设备，以便评估[hyper-v](how-to-set-up-appliance-hyper-v.md)、 [VMware](how-to-set-up-appliance-vmware.md)或物理服务器。
**必需的代理** | 在要分析的每台计算机上，安装以下代理：<br/><br/> [Microsoft Monitoring agent （MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。<br/><br/> [依赖关系代理](../azure-monitor/platform/agents-overview.md#dependency-agent)。<br/><br/> 如果本地计算机未连接到 internet，则需要下载并安装 Log Analytics 网关。 | 了解有关安装[依赖关系代理](how-to-create-group-machine-dependencies.md#install-the-dependency-agent)和[MMA](how-to-create-group-machine-dependencies.md#install-the-mma)的详细信息。
**Log Analytics** | Azure Migrate 使用[Azure Monitor 日志](../log-analytics/log-analytics-overview.md)中的[服务映射](../operations-management-suite/operations-management-suite-service-map.md)解决方案进行依赖关系可视化。<br/><br/> 将新的或现有的 Log Analytics 工作区与 Azure Migrate 项目相关联。 添加 Azure Migrate 项目后，不能修改该工作区的工作区。 <br/><br/> 工作区必须与 Azure Migrate 项目位于同一订阅中。<br/><br/> 工作区必须位于美国东部、东南亚或西欧区域。 其他区域中的工作区不能与项目关联。<br/><br/> 工作区必须位于[支持服务映射](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)的区域中。<br/><br/> 在 Log Analytics 中，与 Azure Migrate 相关联的工作区用迁移项目密钥和项目名称进行标记。
**代价** | 服务映射解决方案不会在前180天内产生任何费用（从将 Log Analytics 工作区与 Azure Migrate 项目关联的那一天）/<br/><br/> 在 180 天之后，将收取标准 Log Analytics 费用。<br/><br/> 使用关联 Log Analytics 工作区中服务映射以外的任何解决方案将导致 Log Analytics 的[标准费用](https://azure.microsoft.com/pricing/details/log-analytics/)。<br/><br/> 删除 Azure Migrate 项目时，工作区不会随之一起删除。 删除项目后，服务映射使用情况并不免费，并按 Log Analytics 工作区的付费层收费<br/><br/>如果你的项目是在 Azure Migrate 正式发布（GA-28 2018 年2月）之前创建的，则可能会产生额外服务映射费用。 为了确保仅在180天后付款，建议你创建一个新项目，因为 GA 之前的现有工作区仍可计费。
**管理** | 将代理注册到工作区时，将使用 Azure Migrate 项目提供的 ID 和密钥。<br/><br/> 你可以使用 Azure Migrate 外部的 Log Analytics 工作区。<br/><br/> 如果删除关联的 Azure Migrate 项目，则不会自动删除该工作区。 请[手动将其删除](../azure-monitor/platform/manage-access.md)。<br/><br/> 请勿删除 Azure Migrate 创建的工作区，除非删除 Azure Migrate 项目。 否则，依赖项可视化功能将不会按预期工作。

## <a name="agentless-visualization"></a>无代理可视化


**要求** | **详细信息** | **了解详细信息**
--- | --- | ---
**部署之前** | 您应该准备好一个 Azure Migrate 项目，并将 Azure Migrate： Server 评估工具添加到项目。<br/><br/>  设置 Azure Migrate 设备后，请部署依赖关系可视化，以发现本地 VMWare 计算机。 | [了解如何](create-manage-projects.md)首次创建项目。<br/><br/> [了解如何](how-to-assess.md)向现有项目添加评估工具。<br/><br/> 了解如何为[VMware](how-to-set-up-appliance-vmware.md) vm 设置 Azure Migrate 设备进行评估。
**必需的代理** | 你要分析的计算机上无需代理。
**受支持的操作系统** | 查看无代理可视化所支持的[操作系统](migrate-support-matrix-vmware.md#agentless-dependency-visualization)。
**VM** | **Vmware 工具**：必须在要分析的 vm 上安装并运行 vmware 工具。<br/><br/> **帐户**：在 Azure Migrate 设备上，你需要添加可用于访问用于分析的 vm 的用户帐户。<br/><br/> **Windows vm**：用户帐户必须是计算机上的本地或域管理员。<br/><br/> **Linux vm**：帐户需要 root 权限。 此外，用户帐户需要/bin/netstat 和/bin/ls 文件上的以下两个功能： CAP_DAC_READ_SEARCH 和 CAP_SYS_PTRACE。 | [了解](migrate-appliance.md)Azure Migrate 设备。
**VMware** | **vCenter**：设备需要具有只读访问权限的 vCenter Server 帐户，并为虚拟机启用 > 来宾操作的特权。<br/><br/> **ESXi 主机**：在运行要分析的 Vm 的 ESXi 主机上，Azure Migrate 设备必须能够连接到 TCP 端口443。
**收集的数据** |  无代理依赖项分析通过从启用了它的计算机捕获 TCP 连接数据来工作。 启用依赖项发现后，设备每隔5分钟从来宾 Vm 收集 TCP 连接数据。 此数据通过 vCenter Server 使用 vSphere Api 从来宾 Vm 收集。 收集的数据将在设备上进行处理，以推导出依赖关系信息，并每隔6小时发送到 Azure Migrate。 将从每台计算机中收集以下数据： <br/> -具有活动连接的进程的名称。<br/> -运行具有活动连接的进程的应用程序的名称。<br/> -活动连接上的目标端口。


## <a name="next-steps"></a>后续步骤
- [设置依赖项可视化](how-to-create-group-machine-dependencies.md)
- 试用 VMware Vm 的[无代理依赖项可视化](how-to-create-group-machine-dependencies-agentless.md)。
- 查看有关依赖关系可视化的[常见问题](common-questions-discovery-assessment.md#what-is-dependency-visualization)。


