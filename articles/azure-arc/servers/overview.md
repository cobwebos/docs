---
title: 已启用 Azure Arc 的服务器概述
description: 了解如何使用已启用 Azure Arc 的服务器像管理 Azure 资源一样，管理托管在 Azure 外部的服务器。
keywords: azure automation, DSC, powershell, desired state configuration, update management, change tracking, inventory, runbooks, python, graphical, hybrid
ms.date: 09/16/2020
ms.topic: overview
ms.openlocfilehash: 9fcac35f943eefb15a200e9b148b22ce67cc3941
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "90887538"
---
# <a name="what-is-azure-arc-enabled-servers"></a>什么是已启用 Azure Arc 的服务器？

使用已启用 Azure Arc 的服务器，你可以管理企业网络或其他云提供商中的、托管在 Azure 外部的 Windows 和 Linux 计算机，就像管理本地 Azure 虚拟机一样。 当混合计算机连接到 Azure 时，它将成为一台联网计算机，被视为 Azure 中的资源。 每台联网计算机都有一个资源 ID，作为订阅内资源组的一部分进行管理，并可受益于标准的 Azure 构造，例如 Azure Policy 和应用标记。 为客户管理本地基础结构的服务提供商可以通过结合使用 Azure Arc 和 [Azure Lighthouse](../../lighthouse/how-to/manage-hybrid-infrastructure-arc.md) 来管理其混合计算机，与当前跨多个客户环境管理本机 Azure 资源一样。

若要为托管在 Azure 外部的混合计算机提供此体验，需要在打算连接到 Azure 的每台计算机上安装 Azure Connected Machine 代理。 此代理不提供任何其他功能，而且不会取代 Azure [Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)。 若要主动监视计算机上运行的 OS 和工作负荷、使用自动化 Runbook 或更新管理等解决方案对其进行管理，或使用其他 Azure 服务（例如 [Azure 安全中心](../../security-center/security-center-intro.md)），需要安装适用于 Windows 和 Linux 的 Log Analytics 代理。

## <a name="supported-scenarios"></a>支持的方案

将计算机连接到已启用 Azure Arc 的服务器时，它可以执行以下配置管理和监视任务：

- 使用与适用于 Azure 虚拟机的策略分配相同的体验，分配 [Azure Policy 来宾配置](../../governance/policy/concepts/guest-configuration.md)。

- 使用 Azure 自动化的“[更改跟踪和库存](../../automation/change-tracking.md)”功能，报告有关在受监视的服务器上安装的软件、Microsoft 服务、Windows 注册表和文件以及 Linux 守护程序的配置更改。

- 监视已连接的计算机来宾操作系统性能，并发现应用程序组件，以使用[用于 VM 的 Azure Monitor](../../azure-monitor/insights/vminsights-overview.md) 来监视其进程以及与应用程序通信的其他资源的依赖项。

- 使用其他 Azure 服务（如使用受支持的 [Azure VM 扩展](manage-vm-extensions.md)的 Azure 自动化[状态配置](../../automation/automation-dsc-overview.md)和 Azure Monitor Log Analytics 工作区）为非 Azure Windows 或 Linux 计算机简化部署。 这包括使用自定义脚本扩展执行部署后配置或软件安装。

- 使用 Azure 自动化中的[更新管理](../../automation/update-management/update-mgmt-overview.md)为 Windows 和 Linux 服务器管理操作系统更新。 首先部署“[混合 Runbook 辅助角色](../../automation/automation-hybrid-runbook-worker.md)”角色，然后按照相应步骤在非 Azure Windows 或 Linux 计算机上[启用更新管理](../../automation/update-management/update-mgmt-enable-portal.md)。

- 使用 [Azure 安全中心](../../security-center/security-center-intro.md)将用于检测威胁的非 Azure 服务器包含在内，并主动监视潜在的安全威胁。

从混合计算机收集并存储在 Log Analytics 工作区中的日志数据现在包含特定于计算机的属性，例如资源 ID。 这可用于支持[资源上下文](../../azure-monitor/platform/design-logs-deployment.md#access-mode)日志访问。

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>支持的区域

有关启用了 Azure Arc 的服务器的支持区域的最终列表，请参见[按区域划分的 Azure 产品](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)页。

在大多数情况下，创建安装脚本时选择的位置应该是在地理位置上最接近你的计算机位置的 Azure 区域。 静态数据将存储在包含你指定区域的 Azure 地理区域中，如果你有数据驻留要求，这可能也会影响你对区域的选择。 如果你的计算机连接到的 Azure 区域受中断影响，则已连接的计算机不受影响，但使用 Azure 的管理操作可能无法完成。 发生区域性服务中断时，如果你有可支持异地冗余服务的多个位置，最好将每个位置的计算机连接到不同的 Azure 区域。

### <a name="agent-status"></a>代理状态

Connected Machine 代理每 5 分钟向服务发送一条定期检测信号消息。 如果服务停止从计算机接收这些检测信号消息，系统会将该计算机视为脱机，并会在 15 到 30 分钟内在门户中将状态自动更改为“已断开连接”。 收到来自 Connected Machine 代理的后续检测信号消息后，其状态会自动更改为“已连接”。

## <a name="next-steps"></a>后续步骤

在为多台混合计算机评估或启用已启用 Arc 的服务器之前，请查看[连接的计算机代理概述](agent-overview.md)，了解要求、有关代理的技术详细信息以及部署方法。
