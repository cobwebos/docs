---
title: Azure 自动化中的身份验证简介
description: 本文概述了 Azure 自动化中自动化帐户的自动化安全性以及可供使用的不同身份验证方法。
keywords: 自动化安全性, 安全的自动化; 自动化身份验证
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 3d3dbaad18f6acbe1ddf17d81f54e4232c838dd7
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82787407"
---
# <a name="introduction-to-authentication-in-azure-automation"></a>Azure 自动化中的身份验证简介

Azure 自动化让可以通过其他云提供程序（如 Amazon Web Services (AWS)）针对 Azure、本地中的资源来自动执行任务。 为了使 Runbook 执行所需操作，Runbook 必须有权使用订阅中所需的最小权限来安全地访问资源。

本文介绍 Azure 自动化支持的各种身份验证方案，以及如何根据需要管理的一个或多个环境开始使用。  

## <a name="automation-account-overview"></a>自动化帐户概述

首次启动 Azure 自动化时，必须创建至少一个自动化帐户。 使用自动化帐户，可以会自动化资源（Runbook、资产、配置）与其他自动化帐户中包含的资源相隔离。 可以使用自动化帐户将资源隔离到独立的逻辑环境中。 例如，可以在开发环境中使用一个帐户，在生产环境中使用另一个帐户，并在本地环境中使用另一个账户。 Azure 自动化帐户不同于 Microsoft 帐户或在 Azure 订阅中创建的帐户。

每个自动化帐户的自动化资源与单个 Azure 区域相关联，但自动化帐户可以管理订阅中的所有资源。 在不同区域中创建自动化帐户的主要原因是，策略要求数据和资源隔离到特定的区域。

所有使用 Azure 资源管理器和 Azure 自动化中的 Azure cmdlet 对资源执行的任务必须使用 Azure Active Directory 组织标识基于凭据的身份验证向 Azure 进行身份验证。 Azure Automation 中的运行方式帐户在 azure 中使用 Azure cmdlet 为管理资源提供身份验证。 创建运行方式帐户时，它会在 Azure Active Directory （AD）中创建新的服务主体用户，并在订阅级别向此用户分配参与者角色。 对于在 Azure 虚拟机上使用混合 Runbook 辅助角色的 runbook，你可以将[Runbook 身份验证与托管标识](automation-hrw-run-runbooks.md#runbook-auth-managed-identities)（而不是运行方式帐户）配合使用来对 azure 资源进行身份验证。

默认情况下，运行方式帐户的服务主体不具有读取 Azure AD 的权限。 如果要添加读取或管理 Azure AD 的权限，则需要在**API 权限**下授予对服务主体的权限。 若要了解详细信息，请参阅[添加用于访问 Web API 的权限](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)。

基于角色的访问控制在 Azure 资源管理器中可用，向 Azure AD 用户帐户和运行方式帐户授予允许的操作，并对该服务主体进行身份验证。 请阅读 [Azure 自动化中基于角色的访问控制](automation-role-based-access-control.md)一文，详细了解如何开发自动化权限管理模型。  

在数据中心或其他云环境（如 AWS）中的混合 Runbook 辅助角色上运行的 runbook 不能使用通常用于针对 Azure 资源进行 runbook 身份验证的方法。 这是因为这些资源在 Azure 外部运行，因此，它们需要在自动化中定义自己的安全凭据，以便向需要在本地访问的资源进行身份验证。 有关 runbook 辅助角色的 runbook 身份验证的详细信息，请参阅[对混合 Runbook 辅助角色的 Runbook 进行身份](automation-hrw-run-runbooks.md)验证。 

## <a name="next-steps"></a>后续步骤

* [从 Azure 门户创建自动化帐户](automation-create-standalone-account.md)。

* [使用 Azure 资源管理器模板创建自动化帐户](automation-create-account-template.md)。

* [用 Amazon Web Services 进行身份验证（AWS）](automation-config-aws-account.md)。
