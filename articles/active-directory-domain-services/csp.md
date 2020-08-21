---
title: 适用于云解决方案提供商的 Azure AD 域服务 | Microsoft Docs
description: 了解如何为 Azure 云解决方案提供商启用和管理 Azure Active Directory 域服务托管域
services: active-directory-ds
author: iainfoulds
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: 42364e791711258e42883bdfcd658ba2c45ecd28
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723140"
---
# <a name="azure-active-directory-domain-services-deployment-and-management-for-azure-cloud-solution-providers"></a>适用于 Azure 云解决方案提供商的 Azure Active Directory 域服务部署和管理

Azure 云解决方案提供商 (CSP) 是 Microsoft 合作伙伴的计划，并为各种 Microsoft 云服务提供许可证通道。 通过 Azure CSP，合作伙伴可以管理销售、打理自己的账单关系、提供技术和计费支持以及成为客户的单一联系点。 此外，Azure CSP 还会提供一套完整的工具，包括自助服务门户和相应的 API。 这些工具使 CSP 合作伙伴可以轻松地预配和管理 Azure 资源，并提供针对客户及其订阅的计费。

[合作伙伴中心门户](/azure/cloud-solution-provider/overview/partner-center-overview)是所有 Azure CSP 合作伙伴的入口点，它提供了丰富的客户管理功能、自动化处理和其他功能。 Azure CSP 合作伙伴可以通过多种方式使用合作伙伴中心功能，包括基于 Web 的 UI、PowerShell 及多种 API 调用。

下图概括说明了 CSP 模型的工作原理。 此处，Contoso 有一个 Azure Active Directory (Azure AD) 租户。 他们与一个 CSP 之间有合作伙伴关系，该 CSP 在其 Azure CSP 订阅中部署和管理资源。 Contoso 还可能拥有常规（直接）Azure 订阅，由 Contoso 直接支付费用。

![CSP 模型概述](./media/csp/csp_model_overview.png)

CSP 合作伙伴的租户有三个特殊的代理组 - 管理代理、支持人员代理和销售代理。  

管理代理组会分配给 Contoso 的 Azure AD 租户中的租户管理员角色。 因此，CSP 合作伙伴管理代理组中的用户在 Contoso 的 Azure AD 租户中具有租户管理权限。

当 CSP 合作伙伴为 Contoso 预配 Azure CSP 订阅时，其管理代理组会分配给该订阅的所有者角色。 因此，CSP 合作伙伴的管理代理具有代表 Contoso 预配虚拟机、虚拟网络和 Azure AD 域服务等 Azure 资源所需的权限。

有关详细信息，请参阅 [Azure CSP overview](/azure/cloud-solution-provider/overview/azure-csp-overview)（Azure CSP 概述）

## <a name="benefits-of-using-azure-ad-ds-in-an-azure-csp-subscription"></a>使用 Azure CSP 订阅中的 Azure AD DS 的优势

Azure Active Directory 域服务 (Azure AD DS) 提供与 Windows Server Active Directory 域服务完全兼容的托管域服务，例如域加入、组策略、LDAP、Kerberos/NTLM 身份验证。 过去几十年出现了众多使用此类功能在 AD 基础上运行的应用程序。 许多独立软件供应商 (ISV) 已在客户本地生成和部署应用程序。 为这些应用程序提供支持非常困难，因为这通常需要访问部署了这些应用程序的不同环境。 利用 Azure CSP 订阅，借助 Azure 的规模和灵活性，解决这个问题变得简单多了。

Azure AD DS 支持 Azure CSP 订阅。 你可以在关联到客户的 Azure AD 租户的 Azure CSP 订阅中部署应用程序。 因此，员工（支持人员）可以使用组织的企业凭据控制、管理和维护部署了应用程序的 VM。

你还可以在客户的 Azure AD 租户中部署 Azure AD DS 托管域。 你的应用程序然后将连接到客户的托管域。 应用程序内依赖于 Kerberos/NTLM、LDAP 或 [System.DirectoryServices API](/dotnet/api/system.directoryservices) 的功能可针对客户的域无缝地工作。 最终客户将应用程序作为服务使用，不需要担心如何维护部署应用程序的基础结构，因此将从中受益。

用户将为其在该订阅中使用的 Azure 资源（包括 Azure AD DS）付费。 在销售、计费、技术支持等方面，你可保持对客户关系的完全控制。借助 Azure CSP 平台的灵活性，少量的支持代理可为大量已部署应用程序实例的客户提供服务。

## <a name="csp-deployment-models-for-azure-ad-ds"></a>Azure AD DS 的 CSP 部署模型

有两种方法可将 Azure AD DS 与 Azure CSP 订阅结合使用。 请根据客户的安全性和简易性考虑因素，选择一种合适的方法。

### <a name="direct-deployment-model"></a>直接部署模型

在此部署模型下，Azure AD DS 在属于 Azure CSP 订阅的虚拟网络中启用。 CSP 合作伙伴的管理代理具有以下特权：

* 客户的 Azure AD 租户中的全局管理员特权。
* 对 Azure CSP 订阅的订阅所有者特权。

![直接部署模型](./media/csp/csp_direct_deployment_model.png)

在这种部署模型下，CSP 提供商的管理代理可以管理客户的标识。 这些管理代理可以在客户的 Azure AD 租户中执行预配新用户或组或者添加应用程序之类的任务。

此部署模型可能适用于没有专门的标识管理员或更愿意由 CSP 合作伙伴代表其管理标识的小型组织。

### <a name="peered-deployment-model"></a>对等部署模型

在此部署模型下，Azure AD DS 在属于客户的虚拟网络（客户付费的直接 Azure 订阅）中启用。 CSP 合作伙伴可在属于客户的 CSP 订阅的虚拟网络中部署应用程序。 然后可使用虚拟网络对等互连连接虚拟网络。

使用此部署，由 Azure CSP 订阅中的 CSP 合作伙伴部署的工作负载或应用程序可连接到在客户的直接 Azure 订阅中预配的客户托管域。

![对等部署模型](./media/csp/csp_peered_deployment_model.png)

此部署模型提供对特权的隔离，并使 CSP 合作伙伴的支持人员代理能够管理 Azure 订阅，并在其中部署和管理资源。 但是，CSP 合作伙伴的支持人员代理不需要对客户的 Azure AD 目录拥有全局管理员特权。 客户的标识管理员可继续管理其组织的标识。

此部署模型可能适用于以下情况：ISV 提供本地应用程序的托管版本，该版本也需要连接到客户的 Azure AD。

## <a name="administer-azure-ad-ds-in-csp-subscriptions"></a>管理 CSP 订阅中的 Azure AD DS

在 Azure CSP 订阅中管理托管域时，请注意以下注意事项：

* **CSP 管理代理可以使用其凭据预配托管域：** Azure AD DS 支持 Azure CSP 订阅。 CSP 合作伙伴的管理代理组中的用户可以预配新的托管域。

* **CSP 可以使用 PowerShell 为其客户编写新的托管域创建脚本：** 有关详细信息，请参阅[如何使用 PowerShell 启用 Azure AD DS](powershell-create-instance.md)。

* **CSP 管理代理不能使用其凭据在托管域上执行日常管理任务：** CSP 管理员用户不能使用其凭据在托管域中执行日常管理任务。 这些用户不在客户的 Azure AD 租户中，并且其凭据在客户的 Azure AD 租户中不可用。 Azure AD DS 无权访问这些用户的 Kerberos 和 NTLM 密码哈希，因此无法在托管域上对这些用户进行身份验证。

  > [!WARNING]
  > 必须在客户的目录中创建用户帐户，从而对托管域执行日常管理任务。
  >
  > 无法使用 CSP 管理员用户的凭据登录到托管域。 请使用客户 Azure AD 租户中的用户帐户的凭据进行登录。 对于将 VM 加入到托管域、管理 DNS、管理组策略之类的任务，这些凭据是必需的。

* **必须将为日常管理创建的用户帐户添加到“AAD DC 管理员”组：** “AAD DC 管理员”组有权在托管域上执行某些委托的管理任务。 这些任务包括配置 DNS、创建组织单位，以及管理组策略。
    
    为使 CSP 合作伙伴能够在托管域上执行这些任务，必须在客户的 Azure AD 租户中创建用户帐户。 此帐户的凭据必须与 CSP 合作伙伴的管理代理共享。 此外，此用户帐户必须添加到“AAD DC 管理员”组，从而在托管域上启用要使用此用户帐户执行的配置任务。

## <a name="next-steps"></a>后续步骤

若要开始使用，请[在 Azure CSP 计划中注册](/partner-center/enrolling-in-the-csp-program)。 然后，你可以使用 [Azure 门户](tutorial-create-instance.md)或 [Azure PowerShell](powershell-create-instance.md) 启用 Azure AD 域服务。