---
title: Azure 安全控制 - 安全配置
description: Azure 安全控制安全配置
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 981e13e772ee21e0e4a680e6d07b4ad4892a7cee
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417069"
---
# <a name="security-control-secure-configuration"></a>安全控制：安全配置

建立、实施并积极管理 Azure 资源的安全配置（跟踪、报告、更正），以防止攻击者利用易受攻击的服务和设置。

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：为所有 Azure 资源建立安全配置

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 7.1 | 5.1 | 客户 |

使用 Azure 策略别名创建自定义策略以审核或强制执行 Azure 资源的配置。 您还可以使用内置的 Azure 策略定义。

此外，Azure 资源管理器能够导出 JavaScript 对象符号 （JSON） 中的模板，应对其进行审查，以确保配置满足/超过组织的安全要求。

您还可以使用 Azure 安全中心的建议作为 Azure 资源的安全配置基线。

- [如何查看可用的 Azure 策略别名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [教程：创建和管理策略以强制实施合规性](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [在 Azure 门户中将单资源和多资源导出到模板](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [安全建议 - 参考指南](https://docs.microsoft.com/azure/security-center/recommendations-reference)

## <a name="72-establish-secure-operating-system-configurations"></a>7.2： 建立安全的操作系统配置

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 7.2 | 5.1 | 客户 |

使用 Azure 安全中心建议维护所有计算资源的安全配置。  此外，您可以使用自定义操作系统映像或 Azure 自动化状态配置来建立组织所需的操作系统的安全配置。

- [如何监视 Azure 安全中心建议](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

- [安全建议 - 参考指南](https://docs.microsoft.com/azure/security-center/recommendations-reference)

- [Azure 自动化状态配置概述](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [上传 VHD 并使用它在 Azure 中创建新的 Windows VM](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

- [使用 Azure CLI 从自定义磁盘创建 Linux VM](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：维护安全的 Azure 资源配置

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 7.3 | 5.2 | 客户 |

使用 Azure 策略 [拒绝] 和 [部署（如果不存在）在 Azure 资源中强制实施安全设置。  此外，可以使用 Azure 资源管理器模板来维护组织所需的 Azure 资源的安全配置。 

- [了解 Azure Policy 效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [创建和管理策略以强制实施合规性](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure 资源管理器模板概述](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4： 维护安全的操作系统配置

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 7.4 | 5.2 | Shared |

按照 Azure 安全中心的建议对 Azure 计算资源执行漏洞评估。  此外，您可以使用 Azure 资源管理器模板、自定义操作系统映像或 Azure 自动化状态配置来维护组织所需的操作系统的安全配置。   Microsoft 虚拟机模板与 Azure 自动化所需状态配置相结合，可能有助于满足和维护安全要求。 

此外，请注意，由 Microsoft 发布的 Azure 应用商店虚拟机映像由 Microsoft 管理和维护。 

- [如何实施 Azure 安全中心漏洞评估建议](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

- [如何从 Azure 资源管理器模板创建 Azure 虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

- [Azure 自动化状态配置概述](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [在 Azure 门户中创建 Windows 虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

- [有关如何下载 VM 模板的信息](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

- [将 VHD 上传到 Azure 并创建新 VM 的示例脚本](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5： 安全存储 Azure 资源的配置

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 7.5 | 5.3 | 客户 |

使用 Azure DevOps 安全地存储和管理代码，如自定义 Azure 策略、Azure 资源管理器模板和所需的状态配置脚本。 要访问在 Azure DevOps 中管理的资源，可以授予或拒绝特定用户、内置安全组或与 Azure DevOps 集成或在 Azure 活动目录（如果与 TFS 集成）中定义的组的权限。

- [如何在 Azure DevOps 中存储代码](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [关于 Azure DevOps 中的权限和组](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7.6： 安全地存储自定义操作系统映像

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 7.6 | 5.3 | 客户 |

如果使用自定义映像，请使用基于角色的访问控制 （RBAC） 来确保只有经过授权的用户才能访问映像。 使用共享映像库，可以将映像共享给组织内的不同用户、服务主体或 AD 组。  对于容器映像，将它们存储在 Azure 容器注册表中，并利用 RBAC 确保只有经过授权的用户才能访问映像。  

- [了解 Azure 中的 RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

- [了解容器注册表的 RBAC](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

- [如何在 Azure 中配置 RBAC](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

- [共享映像库概述](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：为 Azure 资源部署配置管理工具

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 7.7 | 5.4 | 客户 |

使用 Azure 策略定义和实现 Azure 资源的标准安全配置。 使用 Azure 策略别名创建自定义策略以审核或强制执行 Azure 资源的网络配置。 您还可以使用与您的特定资源相关的内置策略定义。  此外，您可以使用 Azure 自动化来部署配置更改。

- [如何配置和管理 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [如何使用别名](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8： 为操作系统部署配置管理工具

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 7.8 | 5.4 | 客户 |

Azure Automation State Configuration 是一个配置管理服务，适用于任何云或本地数据中心内的 Desired State Configuration (DSC) 节点。 可以轻松登记计算机、为其分配声明性配置并查看显示每台计算机是否符合指定的所需状态的报告。 

- [加入 Azure Automation State Configuration 管理的计算机](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：对 Azure 资源实施自动配置监视

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 7.9 | 5.5 | 客户 |

使用 Azure 安全中心对 Azure 资源执行基线扫描。  此外，使用 Azure 策略来警报和审核 Azure 资源配置。

- [如何修复 Azure 安全中心中的建议](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10： 对操作系统实施自动配置监控

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 7.10 | 5.5 | 客户 |

使用 Azure 安全中心对容器的操作系统和 Docker 设置执行基线扫描。

- [了解 Azure 安全中心容器建议](https://docs.microsoft.com/azure/security-center/security-center-container-recommendations)

## <a name="711-manage-azure-secrets-securely"></a>7.11： 安全管理 Azure 机密

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 7.11 | 13.1 | 客户 |

将托管服务标识与 Azure 密钥保管库结合使用，以简化和保护云应用程序的秘密管理。

- [如何与 Azure 托管标识集成](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

- [如何创建密钥保管库](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [如何使用托管标识提供密钥保管库身份验证](https://docs.microsoft.com/azure/key-vault/managed-identity)

## <a name="712-manage-identities-securely-and-automatically"></a>7.12： 安全、自动地管理身份

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 7.12 | 4.1 | 客户 |

使用托管标识在 Azure AD 中为 Azure 服务提供自动托管标识。 托管标识允许您对支持 Azure AD 身份验证的任何服务（包括密钥保管库）进行身份验证，而无需在代码中进行任何凭据。

- [如何配置托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13： 消除意外的凭据暴露

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 7.13 | 18.1, 18.7 | 客户 |

实现凭据扫描程序以标识代码中的凭据。 凭据扫描程序还将鼓励将发现的凭据移动到更安全的位置，如 Azure 密钥保管库。 

- [如何设置凭据扫描程序](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>后续步骤

- 请参阅下一个安全控制：[恶意软件防御](security-control-malware-defense.md)