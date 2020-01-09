---
title: Azure 安全控制-安全配置
description: 安全控制安全配置
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 08458777d8a8c5c74ac20a63245135ffaf0a5c04
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564109"
---
# <a name="security-control-secure-configuration"></a>安全控制：安全配置

建立、实现并主动管理（跟踪、报告、更正） Azure 资源的安全配置，以防止攻击者利用有漏洞的服务和设置。

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 7.1 | 5.1 | 客户 |

使用 Azure 策略或 Azure 安全中心来维护所有 Azure 资源的安全配置。

如何配置和管理 Azure 策略： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的操作系统配置

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 7.2 | 5.1 | 客户 |

使用 Azure 安全中心建议 &quot;更正虚拟机上的安全配置中的漏洞&quot; 来维护所有计算资源的安全配置。

如何监视 Azure 安全中心建议：

https://docs.microsoft.com/azure/security-center/security-center-recommendations

如何修正 Azure 安全中心建议：

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 7.3 | 5.2 | 客户 |

使用 Azure 策略 [拒绝] 和 [部署（如果不存在））在 Azure 资源中强制实施安全设置。

如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

了解 Azure 策略影响：

https://docs.microsoft.com/azure/governance/policy/concepts/effects

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4：维护安全的操作系统配置

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 7.4 | 5.2 | 共享 |

基本操作系统映像由 Microsoft 管理和维护。

但是，你可以使用 Azure 资源管理器模板和/或所需状态配置来应用组织所需的安全设置。

如何从 Azure 资源管理器模板创建 Azure 虚拟机：

https://docs.microsoft.com/azure/virtual-machines/windows/ps-template

了解 Azure 虚拟机的所需状态配置：

https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-overview

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地存储 Azure 资源的配置

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 7.5 | 5.3 | 客户 |

如果使用自定义 Azure 策略定义，请使用 Azure Azure DevOps 或 Azure Repos 来安全地存储和管理你的代码。

如何在 Azure DevOps 中存储代码：

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos 文档：

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

## <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全存储自定义操作系统映像

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 7.6 | 5.3 | 客户 |

如果使用自定义映像，请使用 RBAC 确保只有经过授权的用户才能访问映像。 对于容器映像，请将其存储在 Azure 容器注册表中，并利用 RBAC 确保只有经过授权的用户才能访问这些映像。

了解 Azure 中的 RBAC：

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

了解容器注册表的 RBAC：

https://docs.microsoft.com/azure/container-registry/container-registry-roles

如何在 Azure 中配置 RBAC：

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

## <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系统配置管理工具

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 7.7 | 5.4 | 客户 |

使用 Azure 策略来警报、审核和强制实施系统配置。 此外，开发用于管理策略异常的进程和管道。

如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：部署适用于操作系统的系统配置管理工具

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 7.8 | 5.4 | 客户 |

使用 Azure 计算扩展，例如适用于 Windows 计算的 PowerShell Desired State Configuration 或适用于 Linux 的 Linux Chef 扩展。

如何在 Azure 中安装虚拟机扩展：

https://docs.microsoft.com/azure/virtual-machines/extensions/overview#how-can-i-install-an-extension

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：实现 Azure 服务的自动配置监视

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 7.9 | 5.5 | 客户 |

使用 Azure 安全中心对 Azure 资源执行基线扫描

如何在 Azure 安全中心修正建议：

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：为操作系统实现自动配置监视

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 7.1 | 5.5 | 客户 |

使用 Azure 安全中心对 OS 和容器的 Docker 设置执行基线扫描。

了解 Azure 安全中心容器建议：

https://docs.microsoft.com/azure/security-center/security-center-container-recommendations

## <a name="711-manage-azure-secrets-securely"></a>7.11：安全管理 Azure 机密 

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 7.11 | 13.1 | 客户 |

将托管服务标识与 Azure Key Vault 结合使用，为你的云应用程序简化和安全密钥管理。

如何与 Azure 托管标识集成： https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何创建 Key Vault： https://docs.microsoft.com/azure/key-vault/quick-create-portal

如何使用托管标识提供 Key Vault 身份验证： https://docs.microsoft.com/azure/key-vault/managed-identity

## <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全、自动管理标识

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 7.12 | 4.1 | 客户 |

使用托管标识在 Azure AD 中通过自动管理的标识来提供 Azure 服务。 通过托管标识，你可以对任何支持 Azure AD 身份验证的服务进行身份验证，包括 Key Vault，而无需在代码中包含任何凭据。

如何配置托管标识： https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除意外的凭据公开

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 7.13 | 13.3 | 客户 |

实现凭据扫描器来识别代码中的凭据。 凭据扫描器还鼓励将发现的凭据迁移到更安全的位置，例如 Azure Key Vault。 

如何设置凭据扫描器： https://secdevtools.azurewebsites.net/helpcredscan.html

## <a name="next-steps"></a>后续步骤

请参阅下一安全控制：[恶意软件防护](security-control-malware-defense.md)
