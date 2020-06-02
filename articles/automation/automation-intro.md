---
title: Azure 自动化简介
description: 本文介绍什么是 Azure 自动化，以及如何使用 Azure 自动化自动完成基础结构和应用程序的生命周期。
services: automation
ms.subservice: process-automation
keywords: azure 自动化, DSC, powershell, 状态配置, 更新管理, 更改跟踪, DSC, 库存, runbook, python, 图形
ms.date: 10/18/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: e02cfdaac602adfe455c26d9e87939586fd9738a
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835268"
---
# <a name="an-introduction-to-azure-automation"></a>Azure 自动化简介

Azure 自动化提供基于云的自动化和配置服务，用于支持 Azure 环境和非 Azure 环境之间的一致管理。 Azure 自动化包括流程自动化、配置管理、更新管理、共享功能和异类功能。 在部署、操作和解除工作负荷与资源期间，自动化可以提供全面的控制。

![自动化功能](media/automation-overview/automation-overview.png)

## <a name="process-automation"></a>过程自动化

利用 Azure 自动化中的流程自动化可以自动完成频繁进行的、耗时的、易出错的云管理任务。 此服务使你可以专注于能够让业务增值的工作。 还可以通过自动化来减少错误和提升效率，从而降低运营成本。 [在 Azure 自动化中执行 Runbook](automation-runbook-execution.md)中详细介绍了流程自动化操作环境。

流程自动化支持将 Azure 服务与部署、配置和管理端到端流程时所需的其他公共系统相集成。 该服务允许以图形方式、在 PowerShell 中或使用 Python 创作 [Runbook](automation-runbook-types.md)。 可以使用[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)跨本地环境进行协调，实现统一管理。 可以通过 [Webhook](automation-webhooks.md) 从 ITSM、DevOps 和监视系统触发自动化，从而满足相关请求并确保持续交付和操作。 

## <a name="configuration-management"></a>配置管理

Azure 自动化中的配置管理允许访问以下两项功能：

* 更改跟踪和库存
* Azure 自动化状态配置

### <a name="change-tracking-and-inventory"></a>更改跟踪和库存

“更改跟踪和库存”结合了更改跟踪和库存功能，可跟踪虚拟机和服务器基础结构的更改。 该服务支持对环境中的不同服务、守护程序、软件、注册表和文件执行“更改跟踪”，以帮助诊断不需要的更改和引发警报。 库存支持可让你查询来宾中的资源，以洞察已安装的应用程序和其他配置项。 有关此功能的详细信息，请参阅[更改跟踪和库存](change-tracking.md)。

### <a name="azure-automation-state-configuration"></a>Azure 自动化状态配置

[Azure 自动化 State Configuration](automation-dsc-overview.md) 是一个基于云的功能，适用于为企业环境提供服务的 PowerShell Desired State Configuration (DSC)。 使用此功能可以在 Azure 自动化中管理 DSC 资源，并将配置应用于 Azure 云中 DSC 拉取服务器上的虚拟机或物理机。 

## <a name="update-management"></a>更新管理

Azure 自动化包含适用于跨混合环境的 Windows 和 Linux 系统的[更新管理](automation-update-management.md)。 使用更新管理可以洞察 Azure、其他云和本地的更新合规性。 使用此功能可以创建计划的部署，用于在定义的维护时段内协调更新的安装。 如果不应在计算机上安装某项更新，可以使用更新管理功能从部署中排除该项更新。

## <a name="shared-capabilities"></a>共享功能

Azure 自动化提供许多共享功能，包括共享资源、基于角色的访问控制、灵活计划、源代码管理集成、审核和标记。

### <a name="shared-resources"></a><a name="shared-resources"></a>共享资源

Azure 自动化包含一组共享资源，方便用户大规模地完成环境的自动化操作和配置。

* **[计划](automation-schedules.md)** - 在预定义的时间触发自动化操作。
* **[模块](automation-integration-modules.md)** - 管理 Azure 和其他系统。 可将模块导入到适用于 Microsoft、第三方、社区或自定义 cmdlet 和 DSC 资源的自动化帐户中。
* **[模块库](automation-runbook-gallery.md)** - 支持与 PowerShell 库的本机集成，使你能够查看 Runbook 并将其导入自动化帐户。 利用该库可以快速开始从 PowerShell 库与 Microsoft 脚本中心集成和创作流程。
* **[Python 2 包](python-packages.md)** - 支持用于自动化帐户的 Python 2 Runbook。
* **[凭据](automation-credentials.md)** - 安全地存储可供 Runbook 和配置在运行时使用的敏感信息。
* **[连接](automation-connections.md)** - 存储用于连接到系统的常用信息的名称/值对。 模块作者将在 Runbook 和配置中定义连接，以便在运行时使用。
* **[证书](automation-certificates.md)** - 定义要在身份验证中使用的信息，以及用于保护由 Runbook 或 DSC 配置在运行时访问的已部署资源的信息。 
* **[变量](automation-variables.md)** - 保存可在不同的 Runbook 和配置中使用的内容。 可以更改变量值，而无需修改引用这些值的 Runbook 或配置。

### <a name="role-based-access-control"></a>基于角色的访问控制

Azure 自动化支持使用基于角色的访问控制 (RBAC) 来调控对自动化帐户及其资源的访问。 若要详细了解如何对自动化帐户、Runbook 和作业配置 RBAC，请参阅 [Azure 自动化的基于角色的访问控制](automation-role-based-access-control.md)。

### <a name="source-control-integration"></a>源代码管理集成

Azure 自动化支持[源代码管理集成](source-control-integration.md)。 在可以将 Runbook 或配置签入源代码管理系统的情况下，此功能会以代码的形式提升配置。

## <a name="heterogeneous-support-windows-and-linux"></a>异类支持（Windows 和 Linux）

自动化已设计为可在整个混合云环境以及 Windows 和 Linux 系统中运行。 它提供一致的方式，用于自动化和配置所部署的工作负荷以及运行这些工作负荷的操作系统。

## <a name="common-scenarios-for-automation"></a>常用自动化方案

Azure 自动化支持在基础结构和应用程序的整个生命周期内进行管理。 常见方案包括：

* **编写 Runbook** - 以常用的语言创作 PowerShell、PowerShell 工作流、图形、Python 2 和 DSC Runbook。 
* **生成和部署资源** - 使用 Runbook 和 Azure 资源管理器模板在整个混合环境中部署虚拟机。 集成到 Jenkins 和 Azure DevOps 等开发工具中。
* **配置 VM** - 使用基础结构和应用程序的配置评估和配置 Windows 与 Linux 计算机。
* **分享知识** - 将有关组织如何交付和维护工作负荷的知识传输到系统中。 
* **检索库存** - 获取已部署资源的完整库存，以确定目标、提供报告和了解合规情况。 
* **查找更改** - 确定哪些更改可导致配置错误，哪些更改可改善运营合规性。
* **监视** - 隔离导致问题的计算机更改，采取补救措施，或者将这些更改上报到管理系统。
* **保护** - 在安全警报被引发时隔离计算机。 设置来宾内要求。
* **监管** - 为团队设置 RBAC。 恢复未使用的资源。

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-azure-automation"></a>Azure 自动化的定价

可以在[定价](https://azure.microsoft.com/pricing/details/automation/)页上查看与 Azure 自动化相关的价格。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建自动化帐户](automation-quickstart-create-account.md)