---
title: 添加和验证自定义域名 - Azure Active Directory | Microsoft Docs
description: 用于管理 Azure Active Directory 中域名的管理概念和操作指南
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e21d850f03fdca300085c864a12611acb968aa8
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582967"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>管理 Azure Active Directory 中的自定义域名

域名是许多目录资源标识符的重要部分：它可能是用户的用户名或电子邮件地址的一部分、组地址的一部分，有时是应用程序的应用 ID URI 的一部分。 Azure Active Directory (Azure AD) 中的资源可包含目录（包含该资源）所拥有的域名。 只有全局管理员可以在 Azure AD 中管理域。

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>设置 Azure AD 目录的主域名

创建目录后，初始域名（例如“contoso.onmicrosoft.com”）也是主域名。 创建新用户时，主域名是新用户的默认域名。 设置主域名简化了管理员在门户中创建新用户的过程。 若要更改主域名，请执行以下操作：

1. 使用目录全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“Azure Active Directory”  。
3. 选择“自定义域名”  。
  
   ![打开用户管理页面](./media/domains-manage/add-custom-domain.png)
4. 选择你希望设为主域的域名。
5. 选择“设置主域”  命令。 出现提示时确认所做的选择。
  
   ![将域名设为主域名](./media/domains-manage/make-primary-domain.png)

可以将目录的主域名更改为任何未联合的已验证自定义域。 更改目录的主域不会更改任何现有用户的用户名。

## <a name="add-custom-domain-names-to-your-azure-ad-organization"></a>将自定义域名添加到 Azure AD 组织

最多可以添加 900 个托管域名。 若要配置所有域以便与本地 Active Directory 联合，最多可在每个目录中添加 450 个域名。

## <a name="add-subdomains-of-a-custom-domain"></a>添加自定义域的子域

如果想要将第三级域名（如 “europe.contoso.com”）添加到目录，则应首先添加并验证第二级域，例如 contoso.com。 子域由 Azure AD 自动验证。 若要查看添加的子域是否已验证，请在浏览器中刷新域列表。

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>更改自定义域名的 DNS 注册机构会发生什么情况

如果更改 DNS 注册机构，不需要 Azure AD 中执行额外的配置任务。 可以继续对 Azure AD 使用该域名，而不会遇到中断。 如果在 Office 365、Intune 或其他依赖于 Azure AD 中的自定义域名的服务中使用自定义域名，请参阅这些服务的文档。

## <a name="delete-a-custom-domain-name"></a>删除自定义域名

如果组织不再使用某个自定义域名，或者需要在另一个 Azure AD 中使用该域名，可以从 Azure AD 中删除该域名。

要删除自定义域名，则必须先确保目录中没有任何资源依赖域名。 在以下情况下，，无法从目录删除域名：

* 任何用户都有包含域名的用户名、电子邮件地址或代理地址。
* 任何组都有包含域名的电子邮件地址或代理地址。
* Azure AD 中的任何应用程序都具有包含域名的应用 ID URI。

必须更改或删除 Azure AD 目录中的任何此类资源，才能删除自定义域名。

### <a name="forcedelete-option"></a>ForceDelete 选项

可以在 [Azure AD 管理中心](https://aad.portal.azure.com)使用 **ForceDelete** 来强制删除域名，或使用 [Microsoft 图形 API](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta) 执行此操作。 这些选项使用异步操作，并将自定义域名（例如“user@contoso.com”）中的所有引用更新为类似于“user@contoso.onmicrosoft.com”的初始默认域名称。 

若要在 Azure 门户中调用 **ForceDelete**，必须确保对该域名的引用少于 1000 个，并且必须在 [Exchange 管理中心](https://outlook.office365.com/ecp/)更新或删除预配服务是 Exchange 的所有引用。 这包括支持 Exchange 邮件的安全组和分发列表；有关详细信息，请参阅[删除支持邮件的安全组](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)。 此外，如果存在以下任一情况，则 **ForceDelete** 操作不会成功：

* 通过 Office 365 域订阅服务购买了域
* 你是代表另一个客户组织的合作伙伴管理

在执行 **ForceDelete** 操作过程中，将执行以下操作：

* 将引用了自定义域名的用户的 UPN、EmailAddress 和 ProxyAddress 重命名为初始默认域名。
* 将引用了自定义域名的组的 EmailAddress 重命名为初始默认域名。
* 将引用了自定义域名的应用程序的 identifierUris 重命名为初始默认域名。

出现以下情况时会返回错误：

* 要重命名的对象数大于 1000
* 要重命名的某个应用程序是多租户应用

### <a name="frequently-asked-questions"></a>常见问题

**问：为何域删除操作失败，同时有一条错误指出，此域名包含 Exchange 主控的组？** <br>
**答：** 目前，某些组（例如，支持邮件的安全组和分发列表）由 Exchange 预配，需要手动在 [Exchange 管理中心 (EAC)](https://outlook.office365.com/ecp/) 清理这些组。 可能有遗留的 ProxyAddresses 依赖于自定义域名，需要手动将其更新为另一个域名。 

**问：我是以管理员\@身份登录的 contoso.com，但无法删除域名 "contoso.com"？**<br>
**答：** 无法引用你尝试在用户帐户名中删除的自定义域名。 请确保全局管理员帐户使用初始默认域名 (.onmicrosoft.com)，例如 admin@contoso.onmicrosoft.com。 使用不同的全局管理员帐户（例如 admin@contoso.onmicrosoft.com），或帐户为 admin@fabrikam.com 的另一个自定义域名（例如“fabrikam.com”）登录。

**问：我单击了 "删除域" 按钮`In Progress` ，并查看删除操作的状态。需要多长时间？如果失败，会发生什么情况？**<br>
**答：** 域删除操作是一个异步后台任务，会重命名对域名的所有引用。 它在一两分钟内应会完成。 如果域删除失败，请确保不存在以下情况：

* 使用 appIdentifierURI 在域名中配置了应用
* 有任何支持邮件的组引用了自定义域名
* 对域名的引用超过 1000 个

如果不符合上述任何情况，请手动清理引用，然后重试删除域。

## <a name="use-powershell-or-the-microsoft-graph-api-to-manage-domain-names"></a>使用 PowerShell 或 Microsoft Graph API 管理域名

Azure Active Directory 中的域名的大多数管理任务也可以使用 Microsoft PowerShell 或以编程方式使用 Microsoft Graph API 来完成。

* [使用 PowerShell 管理 Azure AD 中的域名](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#domains)
* [域资源类型](https://docs.microsoft.com/graph/api/resources/domain?view=graph-rest-1.0)

## <a name="next-steps"></a>后续步骤

* [添加自定义域名](/azure/active-directory/fundamentals/add-custom-domain?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [在 Exchange 管理中心删除 Azure AD 的自定义域名中支持 Exchange 邮件的安全组](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)
* [使用 Microsoft 图形 API 强制删除自定义域名](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta)
