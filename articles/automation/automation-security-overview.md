---
title: "Azure 自动化中的身份验证简介 | Microsoft 文档"
description: "本文概述了 Azure 自动化中自动化帐户的自动化安全性以及可供使用的不同身份验证方法。"
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: tysonn
keywords: "自动化安全性, 安全的自动化; 自动化身份验证"
ms.assetid: 4a6bc2f5-c5a2-4dfb-b10d-7950d750dee8
ms.service: automation
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2017
ms.author: magoedte
ROBOTS: NOINDEX
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 1a0258e872449bf2f2f08345cbe86564e28d964e
ms.contentlocale: zh-cn
ms.lasthandoff: 04/18/2017

---
# <a name="introduction-to-authentication-in-azure-automation"></a>Azure 自动化中的身份验证简介  
Azure 自动化让你可以通过其他云提供程序（如 Amazon Web Services (AWS)）针对 Azure、本地中的资源来自动执行任务。  为了使 Runbook 执行所需操作，Runbook 必须有权使用订阅中所需的最小权限来安全地访问资源。

本文将介绍 Azure 自动化支持的各种身份验证方案，并介绍如何根据你需要管理的单个或多个环境来入门。  

## <a name="automation-account-overview"></a>自动化帐户概述
首次启动 Azure 自动化时，你必须创建至少一个自动化帐户。 使用自动化帐户，可以将你的自动化资源（Runbook、资产、配置）与其他自动化帐户中包含的资源相隔离。 可以使用自动化帐户将资源隔离到独立的逻辑环境中。 例如，你可以在开发环境中使用一个帐户，在生产环境中使用另一个帐户，并在本地环境中使用另一个账户。  Azure 自动化帐户不同于 Microsoft 帐户或在你的 Azure 订阅中创建的帐户。

每个自动化帐户的自动化资源与单个 Azure 区域相关联，但自动化帐户可以管理订阅中的所有资源。 在不同区域中创建自动化帐户的主要原因是，你的策略要求数据和资源隔离到特定的区域。

> [!NOTE]
> 无法在 Azure 经典门户中访问使用在 Azure 门户创建的自动化帐户及其包含的资源。 如果你想要使用 Windows PowerShell 来管理这些帐户或其资源，必须使用 Azure 资源管理器模块。
>

所有使用 Azure Resource Manager 和 Azure 自动化中的 Azure cmdlet 对资源执行的任务必须使用 Azure Active Directory 组织标识基于凭据的身份验证向 Azure 进行身份验证。  基于证书的身份验证是使用 Azure 服务管理模式的原始身份验证方法，但设置很复杂。  在 2014 年引入了使用 Azure AD 用户向 Azure 进行身份验证，不仅简化了配置身份验证帐户的过程，也支持使用在 Azure Resource Manager 和经典资源模式下均可使用的单个用户帐户向 Azure 进行非交互式身份验证的功能。   

目前，在 Azure 门户中创建新的自动化帐户时，还会自动创建以下帐户：

* 运行方式帐户（用于在 Azure Active Directory 中创建新的服务主体）、证书以及分配参与者基于角色的访问控制 (RBAC)（用于使用 Runbook 管理 Resource Manager 资源）。
* 经典运行方式帐户（通过上载管理证书），用于使用 Runbook 管理 Azure 服务管理或经典资源。  

基于角色的访问控制在 Azure Resource Manager 中可用，向 Azure AD 用户帐户和运行方式帐户授予允许的操作，并对该服务主体进行身份验证。  请阅读 [Azure 自动化中基于角色的访问控制](automation-role-based-access-control.md)一文，了解帮助开发用于管理自动化权限的模型的详细信息。  

在你的数据中心的混合 Runbook 辅助角色上运行或针对 AWS 中的计算服务的 Runbook 不能使用通常用于针对 Azure 资源进行 Runbook 身份验证的相同方法。  这是因为这些资源在 Azure 外部运行，因此，它们需要在自动化中定义自己的安全凭据，以便向需要在本地访问的资源进行身份验证。  

## <a name="authentication-methods"></a>身份验证方法
下表总结了适用于 Azure 自动化所支持的每个环境的不同身份验证方法，文章描述了如何为你的 Runbook 设置身份验证。

| 方法 | 环境 | 文章 |
| --- | --- | --- |
| Azure AD 用户帐户 |Azure 资源管理器和 Azure 服务管理 |[Authenticate Runbooks with Azure AD User account（使用 Azure AD 用户帐户进行 Runbook 身份验证）](automation-create-aduser-account.md) |
| Azure 运行方式帐户 |Azure 资源管理器 |[Authenticate Runbooks with Azure Run As account（使用 Azure 运行方式帐户进行 Runbook 身份验证）](automation-sec-configure-azure-runas-account.md) |
| Azure 经典运行方式帐户 |Azure 服务管理 |[Authenticate Runbooks with Azure Run As account（使用 Azure 运行方式帐户进行 Runbook 身份验证）](automation-sec-configure-azure-runas-account.md) |
| Windows 身份验证 |本地数据中心 |[对混合 Runbook 辅助角色进行 Runbook 身份验证](automation-hybrid-runbook-worker.md) |
| AWS 凭据 |Amazon Web Services |[Authenticate Runbooks with Amazon Web Services (AWS)（使用 Amazon Web Services (AWS) 进行 Runbook 身份验证）](automation-config-aws-account.md) |

