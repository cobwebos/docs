---
title: Azure 管理 - 监视 | Microsoft Docs
description: Azure 包含多个服务和工具，它们协同工作，不仅为 Azure 中运行的应用程序提供全面管理，而且还为其他云中以及本地运行的应用程序提供全面管理。  本文概要介绍了有关用于管理云应用程序和资源的 Azure 工具的不同管理方面，并提供了有关这些工具的内容的链接。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2018
ms.author: bwren
ms.openlocfilehash: 36dd04be167d9e8e63ab38e4af80c5766ba55370
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
ms.locfileid: "31410288"
---
# <a name="azure-management---monitoring"></a>Azure 管理 - 监视

Azure 中的监视是 Azure 管理的一个方面。  本文简要介绍了在 Azure 中部署和维护应用程序与资源所需的不同管理方面，并提供了相应的入门文档链接。

## <a name="management-in-azure"></a>Azure 中的管理

管理指的是维护业务应用程序以及为其提供支持的资源所需的任务和流程。  Azure 包含多个服务和工具，它们协同工作，不仅为 Azure 中运行的应用程序提供全面管理，而且还为其他云中以及本地运行的应用程序提供全面管理。  了解可用的不同工具以及如何将它们一起用于各种管理方案，是设计一个完整管理环境的第一步。

下图说明了维护任何应用程序或资源所需的不同管理方面。  这些不同方面可被视为一个生命周期，其中每个方面都是资源生命期的连续交替所必需的。  这始于其初始部署，贯穿于其持续运转，在其停用时结束。

![管理功能](media/management-overview/management-capabilities.png)


没有任何单个 Azure 服务可完全满足特定管理方面的要求，而每个管理方面都是由多个协同工作的服务实现的。  某些服务提供目标功能，例如 Application Insights，它为 Web 应用程序提供监视功能。  其他服务提供常见功能，例如 Log Analytics，它存储其他服务的管理数据，使你可以分析不同服务收集的不同类型的数据。  

下列部分简要介绍了不同的管理领域，并提供了用于处理这些领域的主要 Azure 服务的详细内容链接。

## <a name="monitor"></a>监视
监视是一种数据收集和分析操作，用于确定商业应用程序的性能、运行状况及应用程序和其所依赖的资源的可用性。 通过实施有效的监视策略，可了解应用程序中不同组件的具体操作情况，通过主动发出有关关键问题的通知，可帮助在问题发生前解决问题，从而延长运行时间。  可以阅读“Azure 中的监视”概述，以便在[监视 Azure 应用程序和资源](monitoring-overview.md)部分确定用于监视策略的不同服务。


## <a name="configure"></a>配置
配置指的是应用程序和资源的初始部署和配置，以及其通过修补程序和更新进行的当前维护。  通过脚本和策略自动执行这些任务，可以消除冗余，最大限度地节省时间和工作量，以及提高准确性和效率。  [Azure 自动化](..\automation\automation-intro.md)提供了大量用于自动执行配置任务的服务。  除了用于自动执行流程的 runbook 之外，它还提供了配置和更新管理，以帮助你通过策略管理配置以及识别和部署更新。

## <a name="govern"></a>治理
“治理”提供了机制和流程来保持对 Azure 中的应用程序和资源的控制。  它涉及规划计划和设置战略优先级。  Azure 中的治理主要是通过两个服务实现的。  [Azure 策略](../azure-policy/azure-policy-introduction.md)可用于创建、分配和管理策略定义，用以对资源强制实施不同的规则和操作，从而使这些资源保持符合公司标准和服务级别协议。 [Cloudyn 提供的 Azure 成本管理](../cost-management/overview.md)可用于跟踪 Azure 资源和其他云提供商（包括 AWS 和 Google）的云使用情况和开支。

## <a name="secure"></a>安全
管理应用程序、资源和数据的安全性涉及以下事项的组合：评估威胁、收集和分析安全数据，以及确保应用程序和资源以安全方式设计并配置。  安全监视和威胁分析由 [Azure 安全中心](../security-center/security-center-intro.md)提供，该中心包括跨混合云工作负荷的统一安全管理和高级威胁防护。  另请参阅 [Azure 安全性简介](../security/azure-security.md)以了解有关 Azure 中的安全性的全面信息，以及有关安全配置 Azure 资源的指南。


## <a name="protect"></a>保护
保护指的是确保应用程序和数据始终可用（即使在发生你无法控制的故障时也是如此）。  Azure 中的保护由两个服务提供。  [Azure 备份](../backup/backup-introduction-to-azure-backup.md)提供数据备份和恢复（在云中或本地）。    [Azure Site Recovery](../site-recovery/site-recovery-overview.md) 通过在发生灾难时提供业务连续性和即时恢复，来确保应用程序的高可用性。

## <a name="migrate"></a>迁移 
迁移指的是将当前在本地运行的工作负荷转换到 Azure 云中。  [Azure Migrate](../migrate/migrate-overview.md) 是一项服务，可帮助评估本地虚拟机到 Azure 的迁移适用性，包括基于性能的大小调整和成本估计。  Azure Site Recovery 可帮助执行[从本地](../site-recovery/migrate-tutorial-on-premises-azure.md)或[从 Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md) 实际迁移虚拟机。  [Azure 数据库迁移](../dms/dms-overview.md)会帮助你将多个数据库源迁移到 Azure 数据平台。


## <a name="operations-management-suite"></a>Operations Management Suite
以前的与 Azure 管理相关的技术文档包括 Operations Management Suite (OMS)，后者捆绑了以下 Azure 管理服务：

- Azure 自动化
- Azure 备份
- Log Analytics
- Site Recovery

我们将不再在技术文档中介绍此捆绑内容，因为 Azure 中的完整管理内容已经进行了扩展，包含其他服务。 属于 OMS 的服务均未更改，每一项服务仍在管理 Azure 应用程序和资源的过程中发挥重要作用。 应重点注意需执行的管理任务，以及为了完成每项任务而需一起运行的不同 Azure 服务。