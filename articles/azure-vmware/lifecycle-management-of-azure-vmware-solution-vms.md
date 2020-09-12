---
title: Azure VMware 解决方案 Vm 的生命周期管理
description: 了解如何通过 Microsoft Azure 本机工具来管理 Azure VMware 解决方案 Vm 生命周期的所有方面。
ms.topic: conceptual
ms.date: 09/11/2020
ms.openlocfilehash: 20948ec088d11468b5750ca89979050965246b58
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89663752"
---
# <a name="lifecycle-management-of-azure-vmware-solution-vms"></a>Azure VMware 解决方案 Vm 的生命周期管理

Microsoft Azure 本机工具可用于监视和管理 Azure 环境中)  (Vm 的虚拟机。 此外，还允许在 Azure VMware 解决方案和本地 Vm 上监视和管理 Vm。 在此概述中，我们将介绍 Azure 提供的集成监视体系结构，以及如何使用其本机工具来管理 Azure VMware 解决方案 Vm 的整个生命周期。

## <a name="benefits"></a>优点

- Azure 本机服务可用于在混合环境中管理 Vm (Azure、Azure VMware 解决方案和本地) 。
- 集成了 Azure、Azure VMware 解决方案和本地 Vm 的监视和可见性。
- 通过 azure Automation 中的 Azure 更新管理，你可以管理 Windows 和 Linux 计算机的操作系统更新。 
- Azure 安全中心提供高级威胁防护，其中包括：
    - 文件完整性监视
    - 无文件安全警报
    - 操作系统修补程序评估
    - 安全配置错误评估
    - 终结点保护评估 
- 使用 Azure ARC 为新 Vm 轻松部署 Microsoft Monitoring Agent (MMA) 。 
- Azure Monitor 中的 Log Analytics 工作区使用 MMA 或扩展启用日志收集和性能计数器收集。 将数据和日志收集到一个点，并将这些数据提供给不同的 Azure 本机服务。 
- 增加了 Azure Monitor 的优点： 
    - 无缝监视 
    - 更好的基础结构可见性 
    - 即时通知 
    - 自动解决 
    - 成本效率 

## <a name="integrated-azure-monitoring-architecture"></a>集成的 Azure 监视体系结构

下图显示了适用于 Azure VMware 解决方案 Vm 的集成监视体系结构。

![集成的 Azure 监视体系结构](media/lifecycle-mgmt-avs-vms/integrated-azure-monitoring-architecture.png)

## <a name="integrating-and-deploying-azure-native-services"></a>集成和部署 Azure 本机服务

**AZURE ARC** 将 azure 管理扩展到任何基础结构，包括 Azure VMware 解决方案、本地或其他云平台。 可以通过在 Azure VMware 解决方案环境中安装 Azure Kubernetes Service (AKS) 群集来部署 azure ARC。 有关详细信息，请参阅 [连接启用了 Azure Arc 的 Kubernetes 群集](../azure-arc/kubernetes/connect-cluster.md)。

可以通过 MMA (（也称为 Log Analytics 代理或 OMS Linux 代理) ）来监视 Azure VMware 解决方案 Vm。 通过 ARC VM 生命周期工作流设置 Vm 时，可以自动安装 MMA。 在 vCenter 中的模板部署 Vm 时，还可以安装 MMA;同样，使用通过 ARC 工作流预配的 Vm。 然后，所有预配的 Azure VMware 解决方案 Vm 均可安装 MMA，并将日志发送到 Azure Log Analytics 工作区。 有关详细信息，请参阅 [Log Analytics 代理概述](../azure-monitor/platform/log-analytics-agent.md)。

**Log Analytics 工作区** 使用 MMA 或扩展启用日志收集和性能计数器收集。 若要创建 Log Analytics 工作区，请参阅 [在 Azure 门户中创建 Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace.md)。
- 若要将 Windows Vm 添加到 log analytics 工作区，请参阅 [在 Windows 计算机上安装 Log Analytics 代理](../azure-monitor/platform/agent-windows.md)。
- 若要将 Linux Vm 添加到 log analytics 工作区，请参阅 [在 Linux 计算机上安装 Log Analytics 代理](../azure-monitor/platform/agent-linux.md)。

Azure Automation 中的**azure 更新管理**在混合环境中为 Windows 和 Linux 计算机管理操作系统更新。 它监视修补合规性，并将修补偏差警报转发到 Azure Monitor 进行修正。 Azure 更新管理必须连接到 Log Analytics 工作区以使用存储的数据来评估 Vm 上的更新状态。
- 若要将 Log Analytics 添加到 Azure 更新管理，首先需要 [创建一个 Azure 自动化帐户](../automation/automation-create-standalone-account.md)。
- 若要将 Log Analytics 工作区与自动化帐户链接，请参阅 [Log Analytics 工作区和自动化帐户](../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account)。
- 若要为 Vm 启用 Azure 更新管理，请参阅 [从自动化帐户启用更新管理](../automation/update-management/update-mgmt-enable-automation-account.md)。
- 将 Vm 添加到 Azure 更新管理后，你可以 [在 vm 上部署更新并查看结果](../automation/update-management/update-mgmt-deploy-updates.md)。 

**Azure 安全中心** 在云中和本地跨混合工作负荷提供高级威胁防护。 它将评估 Azure VMware 解决方案 Vm 的漏洞，并根据需要引发警报。 这些安全警报可以转发到 Azure Monitor 以解决问题。
- Azure 安全中心不需要部署。 有关详细信息，请参阅 [虚拟机支持的功能](../security-center/security-center-services.md)列表。
- 若要将 Azure VMware 解决方案 Vm 和非 Azure Vm 添加到 Azure 安全中心，请参阅将 [Windows 计算机集成到 Azure 安全中心](../security-center/quick-onboard-windows-computer.md) 和 [将 Linux 计算机载入到 azure 安全中心](../security-center/quick-onboard-linux-computer.md)。
- 添加 Vm 后，Azure 安全中心将分析资源的安全状态，以识别潜在的漏洞。 它还在 "概述" 选项卡中提供建议。有关详细信息，请参阅 [Azure 安全中心中的安全建议](../security-center/security-center-recommendations.md)。
- 可以在 Azure 安全中心中定义安全策略。 有关配置安全策略的信息，请参阅使用 [安全策略](../security-center/tutorial-security-policy.md)。

**Azure Monitor** 是一种全面的解决方案，用于从云和本地环境收集、分析和操作遥测数据。 它不需要部署。
- Azure Monitor 允许收集来自不同源的数据以进行监视和分析。 有关详细信息，请参阅 [Azure Monitor 的监视数据源](../azure-monitor/platform/data-sources.md)。
- 您还可以收集不同类型的数据来进行分析、可视化和警报。 有关详细信息，请参阅 [Azure Monitor 数据平台](../azure-monitor/platform/data-platform.md)。
- 若要配置 Log Analytics 工作区 Azure Monitor，请参阅为 [用于 VM 的 Azure Monitor 配置 Log Analytics 工作区](../azure-monitor/insights/vminsights-configure-workspace.md)。
- 你可以创建警报规则，以确定你的环境中的问题，例如资源的高使用、缺少修补程序、磁盘空间不足和 Vm 的检测信号。 你还可以通过向 IT 服务管理 (ITSM) 工具发送警报，来设置对检测到的事件的自动响应。 还可以通过电子邮件发送警报检测通知。 若要创建此类规则，请参阅：
    - [使用 Azure Monitor 创建、查看和管理指标警报](../azure-monitor/platform/alerts-metric.md)。
    - [使用 Azure Monitor 创建、查看和管理日志警报](../azure-monitor/platform/alerts-log.md)。
    - 用于设置自动操作和通知的[操作规则](../azure-monitor/platform/alerts-action-rules.md)。
    - [使用 IT 服务管理连接器将 Azure 连接到 ITSM 工具](../azure-monitor/platform/itsmc-overview.md)。

**Azure 平台即服务 (PaaS) ** 是云中的开发和部署环境，可通过资源提供基于云的应用程序。 例如，可将 Azure SQL 数据库与 Azure VMware 解决方案 Vm 集成。 然后，可以将 SQL 警报与 Azure VMware 解决方案 VM 警报相关联。 例如，假设你的应用程序的 SQL 数据库阶段在 Azure PAAS 中，同一应用程序的 web 应用程序层托管在你的 Azure VMware 解决方案虚拟机上。 然后，可以将数据库警报与 web 应用程序警报相关联。 使用 Azure、Azure VMware 解决方案和本地 Vm 的单一集成可见性，简化了故障排除。
