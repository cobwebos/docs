---
title: 将 Azure 自动化更新管理与 Microsoft 终结点集成 Configuration Manager
description: 本文介绍如何使用更新管理配置 Microsoft Endpoint Configuration Manager，以便将软件更新部署到管理器客户端。
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 09c8ef818428157c7de8c3a87bcce8a7b80e62ea
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245904"
---
# <a name="integrate-update-management-with-microsoft-endpoint-configuration-manager"></a>将更新管理与 Microsoft 终结点集成 Configuration Manager

在软件更新管理 (SUM) 周期中，已经投资购买 Microsoft Endpoint Configuration Manager 来管理电脑、服务器和移动设备的客户还可以依赖其在管理软件更新方面的优势和成熟度。

你可以通过在 Microsoft 端点 Configuration Manager 中创建和预暂存软件更新部署来报告和更新托管 Windows 服务器，并使用 [更新管理](update-mgmt-overview.md)获取已完成的更新部署的详细状态。 如果你使用 Microsoft 端点 Configuration Manager 来更新相容性报告，但不使用 Windows server 管理更新部署，则可以继续向 Microsoft 终结点报告 Configuration Manager，同时使用 Azure 自动化更新管理管理安全更新。

>[!NOTE]
>虽然更新管理支持 Windows Server 2008 R2 的更新评估和修补，但它不支持运行此操作系统 Configuration Manager Microsoft 终结点管理的客户端。

## <a name="prerequisites"></a>先决条件

* 必须将 [Azure 自动化更新管理](update-mgmt-overview.md)添加到自动化帐户。
* 当前由你的 Microsoft 终结点管理的 Windows 服务器 Configuration Manager 环境还需要向更新管理启用的 Log Analytics 工作区进行报告。
* 此功能在 Microsoft 端点 Configuration Manager 当前分支版本1606及更高版本中启用。 若要将 Microsoft Endpoint Configuration Manager 管理中心站点或独立主站点与 Azure Monitor 日志和导入集合进行集成，请查看 [Connect Configuration Manager 到 Azure Monitor 日志](../../azure-monitor/platform/collect-sccm.md)。  
* Windows 代理必须配置为与 Windows Server Update Services (WSUS) 服务器进行通信，或者，如果用户未从 Microsoft 终结点 Configuration Manager 接收安全更新，则可以访问 Microsoft 更新。

如何使用现有的 Microsoft 终结点管理托管在 Azure IaaS 中的客户端 Configuration Manager 环境主要取决于 Azure 数据中心与基础结构之间的连接。 此连接会影响你可能需要对 Microsoft 终结点进行的任何设计更改 Configuration Manager 基础结构和相关成本来支持这些必要的更改。 若要了解在继续操作之前需要评估哪些规划注意事项，请查看 [Azure 上的 Configuration Manager - 常见问题解答](/configmgr/core/understand/configuration-manager-on-azure#networking)。

## <a name="manage-software-updates-from-microsoft-endpoint-configuration-manager"></a>从 Microsoft 终结点管理软件更新 Configuration Manager

如果要继续从 Microsoft Endpoint Configuration Manager 管理更新部署，请执行以下步骤。 Azure Automation 连接到 Microsoft 终结点 Configuration Manager，将更新应用到连接到 Log Analytics 工作区的客户端计算机。 更新内容可以从客户端计算机缓存中获取，就好像部署是由 Microsoft 终结点管理 Configuration Manager。

1. 使用 [部署软件更新](/configmgr/sum/deploy-use/deploy-software-updates)中所述的过程，在 Microsoft 终结点 Configuration Manager 层次结构中的顶层站点创建软件更新部署。 与标准部署不同的必须配置的唯一设置是选项“不安装软件更新”，此选项用于控制部署包的下载行为。 通过在下一步中创建计划的更新部署，可以在更新管理中管理此行为。

2. 在 Azure 自动化中，选择“更新管理”。 按照[创建更新部署](update-mgmt-deploy-updates.md#schedule-an-update-deployment)中所述的步骤创建一个新部署，并在 "**类型**" 下拉列表中选择 "**导入的组**" 来选择相应的 Microsoft 端点 Configuration Manager 集合。 请记住以下要点：

    a. 如果在所选的 Microsoft 端点 Configuration Manager 设备集合上定义维护时段，则集合的成员将服从该时间，而不是在计划部署中定义的 " **持续时间** " 设置。

    b. 目标集合的成员必须连接到 Internet（直接连接、通过代理服务器或者通过 Log Analytics 网关）。

通过 Azure 自动化完成更新部署后，属于所选计算机组的成员的目标计算机将按计划的时间从本地客户端缓存中安装更新。 可以[查看更新部署状态](update-mgmt-deploy-updates.md#check-deployment-status)来监视部署结果。

## <a name="manage-software-updates-from-azure-automation"></a>从 Azure 自动化管理软件更新

若要管理 Microsoft Endpoint Configuration Manager 客户端的 Windows Server Vm 的更新，需要配置客户端策略以禁用更新管理管理的所有客户端的软件更新管理功能。 默认情况下，客户端设置以层次结构中的所有设备为应用目标。 有关此策略设置以及如何配置此设置的详细信息，请查看[如何在 Configuration Manager 中配置客户端设置](/configmgr/core/clients/deploy/configure-client-settings)。

执行此配置更改后，根据[创建更新部署](update-mgmt-deploy-updates.md#schedule-an-update-deployment)中所述的步骤创建一个新部署，并在 "**类型**" 下拉选择 "**导入组**" 以选择相应的 Microsoft 端点 Configuration Manager 收集。

## <a name="next-steps"></a>后续步骤

若要设置集成计划，请参阅[计划更新部署](update-mgmt-deploy-updates.md#schedule-an-update-deployment)。
