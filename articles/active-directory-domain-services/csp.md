---
title: Azure AD 云解决方案提供商的域服务 |Microsoft Docs
description: 了解如何为 Azure 云解决方案提供程序启用和管理 Azure Active Directory 域服务托管域
services: active-directory-ds
author: iainfoulds
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: c8cdb75c821f45fe7fcf0f455145beb2b9be2a55
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84734854"
---
# <a name="azure-active-directory-domain-services-deployment-and-management-for-azure-cloud-solution-providers"></a>Azure Active Directory Azure 云解决方案提供商的域服务部署和管理

Azure 云解决方案提供商（CSP）是针对 Microsoft 合作伙伴的计划，并为各种 Microsoft 云服务提供许可证通道。 通过 Azure CSP，合作伙伴可以管理销售、打理自己的账单关系、提供技术和计费支持以及成为客户的单一联系点。 此外，Azure CSP 还会提供一套完整的工具，包括自助服务门户和相应的 API。 这些工具使 CSP 合作伙伴可以轻松地预配和管理 Azure 资源，并提供针对客户及其订阅的计费。

[合作伙伴中心门户](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview)是所有 Azure CSP 合作伙伴的入口点，提供丰富的客户管理功能、自动处理等功能。 Azure CSP 合作伙伴可以通过多种方式使用合作伙伴中心功能，包括基于 Web 的 UI、PowerShell 及多种 API 调用。

下图概括说明了 CSP 模型的工作原理。 此处，Contoso 有一个 Azure Active Directory （Azure AD）租户。 他们与一个 CSP 之间有合作伙伴关系，该 CSP 在其 Azure CSP 订阅中部署和管理资源。 Contoso 还可能拥有常规（直接）Azure 订阅，由 Contoso 直接支付费用。

![CSP 模型概述](./media/csp/csp_model_overview.png)

CSP 合作伙伴的租户有三个特殊代理组-*管理*代理、*支持人员*代理和*销售*代理。

*管理*代理组分配给 Contoso 的 Azure AD 租户中的租户管理员角色。 因此，属于 CSP 合作伙伴的管理代理组的用户在 Contoso 的 Azure AD 租户中具有租户管理员权限。

当 CSP 合作伙伴为 Contoso 预配 Azure CSP 订阅时，其管理代理组会分配给该订阅的所有者角色。 因此，CSP 合作伙伴的管理代理具有代表 Contoso 预配虚拟机、虚拟网络和 Azure AD 域服务等 Azure 资源所需的权限。

有关详细信息，请参阅 [Azure CSP overview](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)（Azure CSP 概述）

## <a name="benefits-of-using-azure-ad-ds-in-an-azure-csp-subscription"></a>在 Azure CSP 订阅中使用 Azure AD DS 的优点

Azure Active Directory 域服务（Azure AD DS）提供与 Windows Server Active Directory 域服务完全兼容的托管域服务，例如域加入、组策略、LDAP、Kerberos/NTLM 身份验证。 过去几十年出现了众多使用此类功能在 AD 基础上运行的应用程序。 许多独立软件供应商 (ISV) 已在客户本地生成和部署应用程序。 这些应用程序很难支持，因为你通常需要访问应用程序所部署到的不同环境。 利用 Azure CSP 订阅，借助 Azure 的规模和灵活性，解决这个问题变得简单多了。

Azure AD DS 支持 Azure CSP 订阅。 你可以将应用程序部署到与客户的 Azure AD 租户关联的 Azure CSP 订阅中。 因此，你的员工（支持人员）可以使用组织的企业凭据来管理、管理和维护部署了你的应用程序的 Vm。

你还可以在客户的 Azure AD 租户中部署 Azure AD DS 托管域。 然后，应用程序会连接到客户的托管域。 应用程序内依赖于 Kerberos/NTLM、LDAP 或[MICROSOFT.DIRECTORYSERVICES API](/dotnet/api/system.directoryservices)的功能无缝地与客户的域配合工作。 最终客户从使用应用程序即服务中受益，无需担心如何维护部署应用程序的基础结构。

在该订阅中使用的 Azure 资源的所有计费（包括 Azure AD DS）会向你收费。 当涉及销售、计费、技术支持等时，可以完全控制与客户的关系。借助 Azure CSP 平台的灵活性，一小部分的支持代理可为多个已部署应用程序实例的客户提供服务。

## <a name="csp-deployment-models-for-azure-ad-ds"></a>Azure AD DS 的 CSP 部署模型

可以通过两种方式将 Azure AD DS 与 Azure CSP 订阅结合使用。 请根据客户的安全性和简易性考虑因素，选择一种合适的方法。

### <a name="direct-deployment-model"></a>直接部署模型

在此部署模型中，Azure AD DS 在属于 Azure CSP 订阅的虚拟网络中启用。 CSP 合作伙伴的管理代理具有以下特权：

* 客户 Azure AD 租户中的*全局管理员*权限。
* Azure CSP 订阅的*订阅所有者*特权。

![直接部署模型](./media/csp/csp_direct_deployment_model.png)

在这种部署模型下，CSP 提供商的管理代理可以管理客户的标识。 这些管理代理可以执行预配新用户或组等任务，或在客户的 Azure AD 租户中添加应用程序。

此部署模型可能适用于没有专用标识管理员的小型组织，或者不希望 CSP 合作伙伴代表他们管理标识。

### <a name="peered-deployment-model"></a>对等部署模型

在此部署模型中，将在属于客户的虚拟网络中启用 Azure AD DS-客户支付的直接 Azure 订阅。 CSP 合作伙伴可以在属于客户 CSP 订阅的虚拟网络中部署应用程序。 然后可使用虚拟网络对等互连连接虚拟网络。

使用此部署时，由 Azure CSP 订阅中的 CSP 合作伙伴部署的工作负荷或应用程序可以连接到客户的直接 Azure 订阅中预配的客户托管域。

![对等部署模型](./media/csp/csp_peered_deployment_model.png)

此部署模型提供对特权的隔离，并使 CSP 合作伙伴的支持人员代理能够管理 Azure 订阅，并在其中部署和管理资源。 但是，CSP 合作伙伴的支持人员代理无需对客户的 Azure AD 目录拥有全局管理员权限。 客户的标识管理员可继续管理其组织的标识。

此部署模型可能适用于以下情况： ISV 提供其本地应用程序的托管版本，还需要连接到客户的 Azure AD。

## <a name="administer-azure-ad-ds-in-csp-subscriptions"></a>在 CSP 订阅中管理 Azure AD DS

在 Azure CSP 订阅中管理托管域时，请注意以下注意事项：

* **CSP 管理代理可以使用其凭据预配托管域：** Azure AD DS 支持 Azure CSP 订阅。 属于 CSP 合作伙伴的管理代理组的用户可以预配新的托管域。

* **Csp 可以使用 PowerShell 为其客户创建新托管域的脚本：** 有关详细信息，请参阅[如何使用 PowerShell 启用 AZURE AD DS](powershell-create-instance.md) 。

* **CSP 管理代理无法使用其凭据在托管域上执行正在进行的管理任务：** CSP 管理员用户无法使用其凭据在托管域中执行日常管理任务。 这些用户在客户的 Azure AD 租户外部，其凭据在客户的 Azure AD 租户中不可用。 Azure AD DS 无权访问这些用户的 Kerberos 和 NTLM 密码哈希，因此不能在托管域上对用户进行身份验证。

  > [!WARNING]
  > 必须在客户的目录中创建用户帐户，从而对托管域执行进行中的管理任务。
  >
  > 无法使用 CSP 管理员用户的凭据登录到托管域。 使用属于客户的 Azure AD 租户的用户帐户的凭据来执行此操作。 需要这些凭据才能将 Vm 加入托管域、管理 DNS 或管理组策略。

* **必须将为正在进行的管理创建的用户帐户添加到 " *AAD Dc 管理员*" 组：** " *aad dc 管理员*" 组有权在托管域上执行某些委派的管理任务。 这些任务包括配置 DNS、创建组织单位和管理组策略。
    
    要使 CSP 合作伙伴在托管域上执行这些任务，必须在客户的 Azure AD 租户中创建用户帐户。 此帐户的凭据必须与 CSP 合作伙伴的管理代理共享。 此外，必须将此用户帐户添加到 " *AAD DC 管理员*" 组，才能使用此用户帐户执行托管域上的配置任务。

## <a name="next-steps"></a>后续步骤

若要开始，请[注册 AZURE CSP 计划](/partner-center/enrolling-in-the-csp-program)。 然后，你可以使用[Azure 门户](tutorial-create-instance.md)或[Azure PowerShell](powershell-create-instance.md)启用 Azure AD 域服务。
