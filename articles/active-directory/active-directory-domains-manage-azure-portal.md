---
title: "管理 Azure Active Directory 中的自定义域名 | Microsoft 文档"
description: "用于管理 Azure Active Directory 中域名的管理概念和操作指南"
services: active-directory
documentationcenter: 
author: curtand
manager: michael.tillman
editor: 
ms.assetid: 5063cd0a-dba2-4ba9-aa65-b8117490d73a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.openlocfilehash: e77ea5c3b04a6717e6434f03ca61084af883c31c
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2017
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>管理 Azure Active Directory 中的自定义域名
域名是许多目录资源标识符的重要部分：它可能是用户的用户名或电子邮件地址的一部分、组地址的一部分，也可能是应用程序的应用 ID URI 的一部分。 Azure Active Directory (Azure AD) 中的资源可包含已验证为目录（包含该资源）所拥有的域名。 只有全局管理员才能在 Azure AD 中执行域管理任务。

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>设置 Azure AD 目录的主域名
创建目录后，初始域名（例如“contoso.onmicrosoft.com”）也是主域名。 创建新用户时，主域名是新用户的默认域名。 设置主域名简化了管理员在门户中创建新用户的过程。 若要更改主域名，请执行以下操作：

1. 使用目录全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“Azure Active Directory”。
3. 选择“自定义域名”。
     
   ![打开“用户管理”](./media/active-directory-domains-manage-azure-portal/add-custom-domain.png)
4. 选择你希望设为主域的域名。
5. 选择“设置主域”命令。 出现提示时确认所做的选择。
   
   ![将域名设为主域名](./media/active-directory-domains-manage-azure-portal/make-primary-domain.png)

可以将目录的主域名更改为任何未联合的已验证自定义域。 更改目录的主域不会更改任何现有用户的用户名。

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>将自定义域名添加到 Azure AD 租户
最多可添加 900 个托管域名。 若要配置所有域以便与本地 Active Directory 联合，最多可在每个目录中添加 450 个域名。 有关详细信息，请参阅[联合域名和托管域名](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain-concepts#federated-and-managed-domain-names)。

## <a name="add-subdomains-of-a-custom-domain"></a>添加自定义域的子域
如果想要将第三级域名（如 “europe.contoso.com”）添加到目录，则应首先添加并验证第二级域，例如 contoso.com。子域由 Azure AD 自动验证。 若要查看刚添加的子域是否已经过验证，请刷新浏览器中的页面，其中列出了域。

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>更改自定义域名的 DNS 注册机构会发生什么情况
如果更改自定义域名的 DNS 注册机构，则可以继续将自定义域名用于 Azure AD 本身，而不会发生中断，也不需要执行其他配置任务。 如果在 Office 365、Intune 或其他依赖于 Azure AD 中的自定义域名的服务中使用自定义域名，请参阅这些服务的文档。

## <a name="delete-a-custom-domain-name"></a>删除自定义域名
如果组织不再使用某个自定义域名，或者需要在另一个 Azure AD 中使用该域名，可以从 Azure AD 中删除该域名。

要删除自定义域名，则必须先确保目录中没有任何资源依赖域名。 在以下情况下，，无法从目录删除域名：

* 任何用户都有包含域名的用户名、电子邮件地址或代理地址。
* 任何组都有包含域名的电子邮件地址或代理地址。
* Azure AD 中的任何应用程序都具有包含域名的应用 ID URI。

必须更改或删除 Azure AD 目录中的任何此类资源，才能删除自定义域名。

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>使用 PowerShell 或图形 API 管理域名
针对 Azure Active Directory 中域名的大多数管理任务也可以使用 Microsoft PowerShell 或者使用 Azure AD 图形 API 以编程方式来完成。

* [使用 PowerShell 管理 Azure AD 中的域名](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [使用图形 API 管理 Azure AD 中的域名](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>后续步骤
* [添加自定义域名](add-custom-domain.md)

