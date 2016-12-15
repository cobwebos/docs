---
title: "管理 Azure Active Directory 预览版中的自定义域名 | Microsoft Docs"
description: "用于管理 Azure Active Directory 中域名的管理概念和操作指南"
services: active-directory
documentationcenter: 
author: jeffsta
manager: femila
editor: 
ms.assetid: 5063cd0a-dba2-4ba9-aa65-b8117490d73a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: curtand;jeffsta
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2751b3bdc890fee15d1976de961c2032b9ced2e5


---
# <a name="managing-custom-domain-names-in-your-azure-active-directory-preview"></a>管理 Azure Active Directory 预览版中的自定义域名
域名是许多目录资源标识符的重要部分：它可能是用户的用户名或电子邮件地址的一部分、组地址的一部分，也可能是应用程序的应用 ID URI 的一部分。 Azure Active Directory (Azure AD) 预览版中的资源可能包含一个域名，该域名已确认属于包含该资源的目录所有。 [预览包括哪些内容？](active-directory-preview-explainer.md) 只有全局管理员才能在 Azure AD 中执行域管理任务。

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>设置 Azure AD 目录的主域名
创建目录后，初始域名（例如“contoso.onmicrosoft.com”）也是主域名。 创建新用户时，主域名是新用户的默认域名。 这简化了管理员在门户中创建新用户的过程。 若要更改主域名，请执行以下操作：

1. 使用目录全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“更多服务”，在文本框中输入 **Azure Active Directory**，然后选择 **Enter**。
   
   ![打开“用户管理”](./media/active-directory-domains-add-azure-portal/user-management.png)
3. 在“directory-name”边栏选项卡中，选择“域名”。
4. 在“directory-name - 域名”边栏选项卡中，选择将成为主域名的域名。
5. 在“domainname”边栏选项卡（即打开的且其名称中有新域名的边栏选项卡）中，选择“设为主域名”命令。 出现提示时确认所做的选择。
   
   ![将域名设为主域名](./media/active-directory-domains-manage-azure-portal/make-primary.png)

你可以将目录的主域名更改为任何未联合的已验证自定义域。 更改目录的主域不会更改任何现有用户的用户名。

## <a name="add-custom-domain-names-to-your-azure-ad"></a>将自定义域名添加到你的 Azure AD
可以将最多 900 个自定义域名添加到每个 Azure AD 目录。 [添加其他自定义域名](active-directory-domains-add-azure-portal.md)的过程与第一个自定义域名的相同。

## <a name="add-subdomains-of-a-custom-domain"></a>添加自定义域的子域
如果想要将第三级域名（如 “europe.contoso.com”）添加到目录，则应首先添加并验证第二级域，例如 contoso.com。 子域将由 Azure AD 自动验证。 若要查看刚添加的子域是否已经过验证，请刷新浏览器中的页面，其中列出了域。

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>更改自定义域名的 DNS 注册机构会发生什么情况
如果你更改自定义域名的 DNS 注册机构，则可以继续将你的自定义域名用于 Azure AD 本身，而不会发生中断，也不需要执行其他配置任务。 如果你在 Office 365、Intune 或其他依赖于 Azure AD 中的自定义域名的服务中使用自定义域名，请参阅这些服务的文档。

## <a name="delete-a-custom-domain-name"></a>删除自定义域名
如果你的组织不再使用某个自定义域名，或者需要在另一个 Azure AD 中使用该域名，你可以从 Azure AD 中删除该域名。

若要删除自定义域名，则必须先确保你的目录中没有任何资源依赖域名。 在以下情况中，你无法从目录删除域名：

* 任何用户都有包含域名的用户名、电子邮件地址或代理地址。
* 任何组都有包含域名的电子邮件地址或代理地址。
* Azure AD 中的任何应用程序都具有包含域名的应用 ID URI。

必须更改或删除 Azure AD 目录中的任何此类资源，然后才能删除自定义域名。

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>使用 PowerShell 或图形 API 管理域名
针对 Azure Active Directory 中域名的大多数管理任务也可以使用 Microsoft PowerShell 或者使用 Azure AD 图形 API（公共预览版）以编程方式来完成。

* [使用 PowerShell 管理 Azure AD 中的域名](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [使用图形 API 管理 Azure AD 中的域名](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>后续步骤
* [添加自定义域名](active-directory-domains-add-azure-portal.md)




<!--HONumber=Nov16_HO3-->


