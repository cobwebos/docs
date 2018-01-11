---
title: "Azure Active Direcory 租户目录使用方法概述 | Microsoft Docs"
description: "介绍什么是 Azure AD 租户，以及如何使用 Azure Active Directory 管理 Azure"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro;oldportal
ms.openlocfilehash: 0218365a21f241b5672774005cd6e0417fba358b
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2018
---
# <a name="manage-your-azure-ad-directory"></a>管理 Azure AD 目录

## <a name="what-is-an-azure-ad-tenant"></a>什么是 Azure AD 租户？
在 Azure Active Directory (Azure AD) 中，租户是组织在注册 Azure 或 Office 365 之类的 Microsoft 云服务时接收的 Azure AD 目录的专用实例。 每个 Azure AD 目录都是独特的，独立于其他 Azure AD 目录。 就像公司办公大楼是组织特有的安全资产一样，根据设计，Azure AD 目录也是仅供组织使用的安全资产。 Azure AD 体系结构将客户数据和标识信息隔离开来，因此，一个 Azure AD 目录的用户和管理员不可能意外或恶意性地访问另一目录中的数据。

![管理 Azure Active Directory](./media/active-directory-administer/aad_portals.png)

## <a name="how-can-i-get-an-azure-ad-directory"></a>如何获取 Azure AD 目录？
Azure AD 在大多数 Microsoft 云服务的后面提供核心目录和身份管理功能，其中包括：

* Azure
* Microsoft Office 365
* Microsoft Dynamics CRM Online
* Microsoft Intune

注册其中任何一个 Microsoft 云服务便会获得一个 Azure AD 目录。 可根据需要创建更多的目录。 例如，可以将第一个目录保留为生产目录，并创建另一个目录进行测试或过渡。

### <a name="using-the-azure-ad-directory-that-comes-with-a-new-azure-subscription"></a>使用附带新 Azure 订阅的 Azure AD 目录

注册其他 Microsoft 服务时，建议使用你在注册第一项服务时使用过的管理员帐户。 首次注册 Microsoft 服务时提供的信息将用于为组织创建新的 Azure AD 目录实例。 如果你在订阅其他 Microsoft 服务时使用该目录对登录尝试进行身份验证，这些服务可能会使用你在默认目录中配置的现有用户帐户、策略、设置或本地目录集成。

例如，如果先注册一个 Microsoft Intune 订阅，然后进一步将本地 Active Directory 与 Azure AD 目录同步，则可注册另一 Microsoft 服务（例如 Office 365），轻松获得使用 Microsoft Intune 时获得的目录集成优势。

有关如何将本地目录与 Azure AD 集成的详细信息，请参阅[与 Azure AD Connect 进行目录集成](active-directory-aadconnect.md)。

### <a name="associate-an-existing-azure-ad-directory-with-a-new-azure-subscription"></a>将现有的 Azure AD 目录与新的 Azure 订阅相关联
可以将新的 Azure 订阅，与用于对现有 Office 365 或 Microsoft Intune 订阅的登录进行身份验证的相同目录进行关联。 有关该方案的详细信息，请参阅[将 Azure 订阅所有权转让给其他帐户](../billing/billing-subscription-transfer.md)

### <a name="create-an-azure-ad-directory-by-signing-up-for-a-microsoft-cloud-service-as-an-organization"></a>通过以组织名义注册 Microsoft 云服务来创建 Azure AD 目录
如果尚未订阅 Microsoft 云服务，可使用以下链接之一进行注册。 注册第一个服务后，会自动创建 Azure AD 目录。

* [Microsoft Azure](https://account.azure.com/organization)
* [Office 365](http://products.office.com/business/compare-office-365-for-business-plans/)
* [Microsoft Intune](https://portal.office.com/Signup/Signup.aspx?OfferId=40BE278A-DFD1-470a-9EF7-9F2596EA7FF9&dl=INTUNE_A&ali=1#0%20)

### <a name="how-to-change-the-default-directory-for-a-subscription"></a>如何更改订阅的默认目录

1. 使用作为订阅的“帐户管理员”的帐户登录到 [Azure 帐户中心](https://account.azure.com/Subscriptions)以转让订阅所有权。
2. 确保需要让其成为订阅所有者的用户位于目标目录中。
3. 单击“转让订阅”。
4. 指定接收方。 接收方会自动收到含有接受链接的电子邮件。
5. 接收方单击该链接并遵照说明操作，包括输入他们的付款信息。 接收方成功后，订阅将转让。 
6. 如果订阅所有权转让成功，则订阅的默认目录会更改为该用户所在的目录。

若要了解详细信息，请参阅[将 Azure 订阅所有权转让给其他帐户](../billing/billing-subscription-transfer.md)

### <a name="manage-the-default-directory-in-azure"></a>管理 Azure 中的默认目录
注册 Azure 时，默认的 Azure AD 目录会与订阅相关联。 Azure AD 没有使用费用，目录是免费资源。 有单独进行许可的付费 Azure AD 服务，此类服务提供更多的功能，例如在登录时显示公司品牌，以及自助式密码重置。 也可创建自定义域，使用自己拥有的 DNS 名称，代替默认的 *.onmicrosoft.com 域。

## <a name="how-can-i-manage-directory-data"></a>如何管理目录数据？
管理一个或多个 Microsoft 云服务订阅时，可以使用 [Azure AD 管理中心](https://aad.portal.azure.com)、Microsoft Intune 帐户门户或 [Office 365 管理中心](https://portal.office.com/)来管理组织的目录数据。 还可以使用 [Azure Active Directory PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/active-directory) 来帮助管理 Azure AD 中存储的数据。

通过上述任一门户（或 cmdlet），可以执行以下操作：

* 创建和管理用户帐户和组帐户
* 管理组织订阅的相关云服务
* 设置与 Azure AD 标识和身份验证服务的本地集成

Azure AD 管理中心、Office 365 管理中心、Microsoft Intune 帐户门户和 Azure AD cmdlet 均从与组织的目录关联的 Azure AD 的单个共享实例读取数据并将数据写入到该实例中。 每个这样的工具均可充当前端接口，用于拉入或更改目录数据。
使用任意门户或 cmdlet 更改组织的数据时，如果此时已在此类服务之一的上下文中登录，则下一次登录时，所做的更改也会显示在其他门户中。 此数据在所订阅的 Microsoft 云服务中共享。

例如，如果使用 Office 365 管理中心阻止某个用户登录，则该操作会阻止该用户登录到组织当前订阅的任何其他服务。 如果在 Microsoft Intune 帐户门户中查看同一用户帐户，则也会看到该用户被阻止。

## <a name="how-can-i-add-and-manage-multiple-directories"></a>如何添加和管理多个目录？
可以[在 Azure 门户中添加 Azure AD 目录](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory)。 填充信息并选择“创建”。

可将每个目录作为完全独立的资源进行管理：每个目录都是一个具有完整功能的对等方，在逻辑上独立于所管理的其他目录；目录之间不存在父子关系。 目录之间的这种独立性包括资源独立性、管理独立性和同步独立性。

* **资源独立性**。 在一个目录中创建或删除一个资源不影响另一个目录中的任何资源，但对于外部用户来说，情况并不完全如此。 如果将自定义域“contoso.com”用于一个目录，则不能将它用于任何其他目录。
* **管理独立性**。  如果某个不是目录“Contoso”管理员的用户创建了测试目录“Test”，那么：
  
  * 目录“Contoso”的管理员对目录“Test”没有直接管理特权，除非“Test”的管理员专门向其授予了这些特权。 “Contoso”的管理员可以控制对目录“Test”的访问，因为他们可以控制创建“Test”的用户帐户。
    
  * 在某个目录中为用户分配或删除管理员角色时，所做的更改不会影响该用户在另一个目录中可能具有的任何管理员角色。
* **同步独立性**。 可以单独配置每个 Azure AD 租户，以便通过 Azure AD Connect 目录同步工具的单个实例来同步数据。

与其他 Azure 资源不同，目录不是 Azure 订阅的子资源。 因此，如果取消 Azure 订阅或让 Azure 订阅过期，仍可以使用 Azure AD PowerShell、Azure Graph API 或 Office 365 管理中心之类的其他界面访问目录数据。 还可以将其他订阅与目录相关联。

## <a name="how-to-prepare-to-delete-an-azure-ad-directory"></a>如何进行准备，以便删除 Azure AD 目录
全局管理员可以从门户删除 Azure AD 目录。 删除目录时，也会删除包含在该目录中的所有资源。 删除目录之前，请验证你是否不需要该目录。

> [!NOTE]
> 如果用户是使用工作或学校帐户登录的，则该用户不得尝试删除其主目录。 例如，如果用户是作为 joe@contoso.onmicrosoft.com 登录的，则该用户不能删除默认域为 contoso.onmicrosoft.com 的目录。

Azure AD 要求删除目录之前必须符合特定的条件。 这可以降低删除目录后对用户或应用程序造成负面影响（例如，影响用户登录 Office 365 或访问 Azure 中的资源）的风险。 例如，如果用于订阅的某个目录被无意删除，则用户无法访问该订阅的 Azure 资源。

需检查以下条件：

* 该目录中的唯一用户应该是要删除该目录的全局管理员。 只有在删除所有其他用户后，才能删除该目录。 如果用户是从本地同步的，则必须关闭同步，并且必须使用 Azure 门户或 Azure PowerShell cmdlet 从云目录中删除这些用户。 不要求删除组或联系人，例如，从 Office 365 管理中心添加的联系人。
* 该目录中不能有任何应用程序。 只有在删除所有应用程序后，才能删除该目录。
* 不能有任何多重身份验证提供程序链接到该目录。
* 与该目录相关联的任何 Microsoft Online Services（例如 Microsoft Azure、Office 365 或 Azure AD Premium）不存在任何订阅。 例如，如果在 Azure 中创建了一个默认目录，并且 Azure 订阅仍然依赖于此目录进行身份验证，则你不能删除此目录。 类似地，如果其他用户已将订阅与某个目录相关联，则你无法删除该目录。 


## <a name="next-steps"></a>后续步骤
* [Azure AD 论坛](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=windowsazuread)
* [Azure 多重身份验证论坛](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=windowsazureactiveauthentication)
* [StackOverflow for Azure 问题](https://stackoverflow.com/questions/tagged/azure-active-directory)
* [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory)
* [在 Azure AD 中分配管理员角色](active-directory-assign-admin-roles-azure-portal.md)
