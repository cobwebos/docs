---
title: Azure 自动化帐户身份验证概述
description: 本文概述了 Azure 自动化帐户身份验证。
keywords: 自动化安全性, 安全的自动化; 自动化身份验证
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 8068d6ebe67dee1408420441aacd83726a1986df
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89434259"
---
# <a name="automation-account-authentication-overview"></a>自动化帐户身份验证概述

Azure 自动化让可以通过其他云提供程序（如 Amazon Web Services (AWS)）针对 Azure、本地中的资源来自动执行任务。 可以使用 Runbook 来自动完成任务；如果你需要管理非 Azure 任务，也可以使用混合 Runbook 辅助角色。 任一环境都要求你拥有相应的权限，以便能够在 Azure 订阅中使用必需的最低权限来安全访问资源。

本文介绍了 Azure 自动化支持的身份验证方案，以及如何基于你需要管理的一个或多个环境开始操作。

## <a name="automation-account"></a>自动化帐户

首次启动 Azure 自动化时，必须创建至少一个自动化帐户。 使用自动化帐户，你可以将自动化资源、Runbook、资产、配置与其他帐户的资源相隔离。 可以使用自动化帐户将资源隔离到独立的逻辑环境中。 例如，可以在开发环境中使用一个帐户，在生产环境中使用另一个帐户，并在本地环境中使用另一个账户。 Azure 自动化帐户不同于 Microsoft 帐户或在 Azure 订阅中创建的帐户。 有关创建自动化帐户的介绍，请参阅[创建自动化帐户](automation-quickstart-create-account.md)。

## <a name="automation-resources"></a>自动化资源

每个自动化帐户的自动化资源都与单个 Azure 区域相关联，但该帐户可以管理 Azure 订阅中的所有资源。 如果你的策略要求将数据和资源隔离到特定的区域，则这是在不同区域中创建自动化帐户的主要原因。

在 Azure 自动化中使用 Azure 资源管理器和 PowerShell cmdlet 针对资源创建的所有任务必须使用基于 Azure Active Directory (Azure AD) 组织标识凭据的身份验证向 Azure 进行身份验证。

## <a name="run-as-account"></a>运行方式帐户

Azure 自动化中的运行方式帐户为使用 PowerShell cmdlet 管理 Azure 资源提供身份验证。 创建运行方式帐户时，将在 Azure AD 中创建新的服务主体用户，并在订阅级别向此用户分配“参与者”角色。 对于在 Azure VM 上使用混合 Runbook 辅助角色的 Runbook，可以通过[使用托管标识的 Runbook 身份验证](automation-hrw-run-runbooks.md#runbook-auth-managed-identities)而不是运行方式帐户来向 Azure 资源进行身份验证。

## <a name="service-principal-for-run-as-account"></a>运行方式帐户的服务主体

默认情况下，运行方式帐户的服务主体对 Azure AD 没有读取权限。 如果你希望添加读取或管理 Azure AD 的权限，需要在“API 权限”下对服务主体授予该权限。 若要了解详细信息，请参阅 [添加访问 WEB API 的权限](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)。

## <a name="role-based-access-control"></a>基于角色的访问控制

基于角色的访问控制在 Azure 资源管理器中可用，用于向 Azure AD 用户帐户和运行方式帐户授予允许的操作，并对服务主体进行身份验证。 请阅读 [Azure 自动化中基于角色的访问控制](automation-role-based-access-control.md)一文，详细了解如何开发自动化权限管理模型。

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>使用混合 Runbook 辅助角色的 Runbook 身份验证

在数据中心的混合 Runbook 辅助角色上运行的或针对其他云环境（如 AWS）中的计算服务运行的 Runbook 不能使用通常用于向 Azure 资源进行Runbook 身份验证的方法。 这是因为这些资源在 Azure 外部运行，因此，它们需要在自动化中定义自己的安全凭据，以便向需要在本地访问的资源进行身份验证。 有关使用 Runbook 辅助角色的 Runbook 身份验证的详细信息，请参阅[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)。

## <a name="next-steps"></a>后续步骤

* 若要从 Azure 门户创建自动化帐户，请参阅[创建独立的 Azure 自动化帐户](automation-create-standalone-account.md)。
* 如果你喜欢使用模板创建帐户，请参阅[使用 Azure 资源管理器模板创建自动化帐户](quickstart-create-automation-account-template.md)。
* 有关如何使用 Amazon Web Services 进行身份验证，请参阅[使用 Amazon Web Services 对 Runbook 进行身份验证](automation-config-aws-account.md)。