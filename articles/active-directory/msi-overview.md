---
title: "Azure Active Directory 的托管服务标识 (MSI)"
description: "概述了 Azure 资源的托管服务标识。"
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 09/15/2017
ms.author: skwan
ms.translationtype: HT
ms.sourcegitcommit: 1868e5fd0427a5e1b1eeed244c80a570a39eb6a9
ms.openlocfilehash: c18fd5d5b528dfbafa456b3702996b80c3a60a02
ms.contentlocale: zh-cn
ms.lasthandoff: 09/19/2017

---

#  <a name="managed-service-identity-msi-for-azure-resources"></a>Azure 资源的托管服务标识 (MSI)

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

生成云应用程序时需要应对的常见挑战是，如何管理为了通过云服务的身份验证而需要插入代码的凭据。 保护这些凭据是一项非常重要的任务。 理想情况下，它们永远不会出现在开发者工作站上，也永远不会被签入源代码管理系统中。 虽然 Azure Key Vault 可用于安全存储凭据以及其他密钥和机密，但代码需要通过 Key Vault 的身份验证才能检索它们。 托管服务标识 (MSI) 为 Azure 服务提供了 Azure Active Directory (Azure AD) 中的自动托管标识，更巧妙地解决了这个问题。 此标识可用于通过支持 Azure AD 身份验证的任何服务（包括 Key Vault）的身份验证，这样就无需在代码中插入任何凭据了。

## <a name="how-does-it-work"></a>工作原理

如果 Azure 服务上已启用托管服务标识，Azure 会自动为 Azure 订阅使用的 Azure AD 租户中的服务实例创建标识。  幕后运行机制是，Azure 会将标识凭据预配到服务实例上。  然后，代码可以发出本地请求，以获取支持 Azure AD 身份验证的服务的访问令牌。  Azure 负责滚动更新服务实例使用的凭据。  如果服务实例遭删除，Azure 会自动清理 Azure AD 中的凭据和标识。

下面的示例展示了托管服务标识如何与 Azure 虚拟机协同工作。

![虚拟机 MSI 示例](./media/msi-vm-example.png)

1. Azure 资源管理器收到消息，要在 VM 上启用 MSI。
2. Azure 资源管理器在 Azure AD 中创建服务主体，用于表示 VM 的标识。 服务主体是在此订阅信任的 Azure AD 租户中进行创建。
3. Azure 资源管理器在 VM 的 MSI VM 扩展中配置服务主体的详细信息。  这一步包括配置扩展从 Azure AD 获取访问令牌所需的客户端 ID 和证书。
4. 至此，VM 的服务主体标识已知，现在可以授予 VM 对 Azure 资源的访问权限了。  例如，如果代码需要调用 Azure 资源管理器，将会在 Azure AD 中使用基于角色的访问控制 (RBAC) 向 VM 的服务主体分配相应的角色。  如果代码需要调用 Key Vault，将会授予代码对 Key Vault 中特定机密或密钥的访问权限。
5. 在 VM 上运行的代码请求从 MSI VM 扩展托管的本地终结点 (http://localhost:50342/oauth2/token) 获取令牌。  resource 参数指定了要向其发送令牌的服务。 例如，如果希望代码通过 Azure 资源管理器的身份验证，需要使用 resource=https://management.azure.com/。
6. MSI VM 扩展使用自己配置的客户端 ID 和证书从 Azure AD 请求获取访问令牌。  Azure AD 返回 JSON Web 令牌 (JWT) 访问令牌。
7. 代码在调用支持 Azure AD 身份验证的服务时发送访问令牌。

为了让代码获取访问令牌，每个支持托管服务标识的 Azure 服务都有自己的方法。 请查看各个服务的相关教程，确定用于获取令牌的具体方法。

## <a name="which-azure-services-support-managed-service-identity"></a>哪些 Azure 服务支持托管服务标识？

支持托管服务标识的 Azure 服务可以使用 MSI，通过支持 Azure AD 身份验证的服务的身份验证。  我们正在跨 Azure 将 MSI 与 Azure AD 身份验证相集成。  请经常再回来看看，确定这部分内容是否有更新。

### <a name="azure-services-that-support-managed-service-identity"></a>支持托管服务标识的 Azure 服务

以下 Azure 服务支持托管服务标识。

| 服务 | 状态 | 日期 |
| --- | --- | --- |
| Azure 虚拟机 | 预览 | 2017 年 9 月 |
| Azure 应用服务 | 预览 | 2017 年 9 月 |
| Azure Functions | 预览 | 2017 年 9 月 |

### <a name="azure-services-that-support-azure-ad-authentication"></a>支持 Azure AD 身份验证的 Azure 服务

以下服务支持 Azure AD 身份验证，已通过使用托管服务标识的客户端服务进行测试。

| 服务 | 资源 ID | 状态 | 日期 |
| --- | --- | --- | --- |
| Azure 资源管理器 | https://management.azure.com/ | 可用 | 2017 年 9 月 |
| Azure 密钥保管库 | https://vault.azure.net/ | 可用 | 2017 年 9 月 |
| Azure Data Lake | https://datalake.azure.net/ | 可用 | 2017 年 9 月 |

## <a name="how-much-does-managed-service-identity-cost"></a>托管服务标识的费用如何？

Azure Active Directory Free 随附托管服务标识，这是 Azure 订阅的默认设置。  无需额外付费，即可使用托管服务标识。

## <a name="support-and-feedback"></a>支持和反馈

我们希望听取大家的意见反馈！

* 在 Stack Overflow 上使用标记 [azure-msi](http://stackoverflow.com/questions/tagged/azure-msi) 提出操作方法方面的问题。
* 在[面向开发者的 Azure AD 反馈论坛](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences)上提交功能请求或提供反馈。

## <a name="try-managed-service-identity"></a>试一试托管服务标识

试用“托管服务标识”教程以了解用于访问不同 Azure 资源的端到端方案：

| 从已启用 MSI 的资源 | 了解如何操作 |
| ------- | -------- |
| A1 VM (Windows) | [使用 Windows VM 托管服务标识访问 Azure 资源管理器](msi-tutorial-windows-vm-access-arm.md) |
|                    | [使用 Windows VM 托管服务标识访问 Azure 存储](msi-tutorial-windows-vm-access-storage.md) |
|                    | [使用 Windows VM 托管服务标识访问非 Azure AD 资源和 Azure Key Vault](msi-tutorial-windows-vm-access-nonaad.md) |
| Azure VM (Linux)   | [使用 Linux VM 托管服务标识访问 Azure 资源管理器](msi-tutorial-linux-vm-access-arm.md) |
|                    | [使用 Linux VM 托管服务标识访问 Azure 存储](msi-tutorial-linux-vm-access-storage.md) |
|                    | [使用 Linux VM 托管服务标识访问非 Azure AD 资源](msi-tutorial-linux-vm-access-nonaad.md) |
| Azure 应用服务  | [将托管服务标识与 Azure 应用服务或 Azure Functions 结合使用](/azure/app-service/app-service-managed-service-identity) |
| Azure 函数     | [将托管服务标识与 Azure 应用服务或 Azure Functions 结合使用](/azure/app-service/app-service-managed-service-identity) |

如果只是想要学习在 Azure 资源上启用 MSI 的基础知识：

| 对于 Azure 资源 | 使用以下项启用/删除 MSI |
| ------------------ | ------------------------------------ |
| A1 VM (Windows) | [Azure 门户](msi-qs-configure-portal-windows-vm.md) |
|                    | [PowerShell](msi-qs-configure-powershell-windows-vm.md) |
|                    | [Azure CLI](msi-qs-configure-cli-windows-vm.md)|
|                    | [Azure Resource Manager 模板](msi-qs-configure-template-windows-vm.md) |

然后了解如何使用基于角色的访问控制 (RBAC) 提供可访问另一 Azure 资源的 MSI 权限：

| 从已启用 MSI 的资源 | 使用以下项分配对另一 Azure 资源的访问权限 |
| ------------------------ | ---------------------------------------------------------- |
| A1 VM (Windows) | [Azure 门户](msi-howto-assign-access-portal.md) |
|                    | [PowerShell](msi-howto-assign-access-powershell.md) |
|                    | [Azure CLI](msi-howto-assign-access-CLI.md) |





