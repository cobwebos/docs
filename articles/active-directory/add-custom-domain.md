---
title: "将自定义域添加到 Azure AD |Microsoft Docs"
description: "介绍如何在 Azure Active Directory 中添加自定义域。"
services: active-directory
author: curtand
manager: femila
ms.assetid: 0a90c3c5-4e0e-43bd-a606-6ee00f163038
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: curtand
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: 2ea119b56f467f57b5929dec05bfd645028578ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="quickstart-add-a-custom-domain-name-to-azure-active-directory"></a>快速入门：将自定义域名添加到 Azure Active Directory

每个 Azure AD 都直接随附 domainname.onmicrosoft.com 形式的初始域名。无法更改或删除初始域名，但可以将企业域名添加到 Azure AD。 例如，你的组织可能具有用来从事商业经营的其他域名和使用公司域名登录的用户。 通过在 Azure AD 中添加自定义域名可以在目录中分配用户熟悉的用户名，例如“alice@contoso.com”。 而不是“alice@<domain name>.onmicrosoft.com”。 过程很简单：

1. 将自定义域名添加到目录
2. 在域名注册机构中为域名添加 DNS 条目
3. 在 Azure AD 中验证自定义域名

## <a name="add-the-custom-domain-name-to-your-directory"></a>将自定义域名添加到目录
1. 使用目录全局管理员的帐户登录到 [Azure 门户](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)。
2. 在左侧选择“域名”。
3. 在 directory-name -“域名”上，选择“添加”。
   
   ![选择“添加”命令](./media/active-directory-domains-add-azure-portal/add-command.png)
5. 在“域名”上的框中输入自定义域的名称（例如“contoso.com”），然后选择“添加域”。 请务必包含 .com、.net 或其他顶级扩展名。
6. 在“domainname”（新域名即为标题）中，收集稍后用于在 Azure AD 中验证自定义域名的 DNS 条目信息。
   
   ![获取 DNS 条目信息](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

> [!TIP]
> 如果计划使用 Azure AD 联合你的本地 Windows Server AD，则需要在运行 Azure AD Connect 工具来同步目录时选中“我计划将此域配置为使用本地 Active Directory 进行单一登录”复选框。 还需要在向导的“Azure AD 域”步骤中注册选择用于与本地目录进行联合的域名。 [这些说明](./connect/active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation)中示范了向导中该步骤的大致情形。 如果没有 Azure AD Connect 工具，可以 [在此处下载](http://go.microsoft.com/fwlink/?LinkId=615771)。

## <a name="add-a-dns-entry-for-the-domain-name-at-the-domain-name-registrar"></a>在域名注册机构中为域名添加 DNS 条目
在 Azure AD 中使用自定义域名的下一个步骤就是更新域的 DNS 区域文件。 然后 Azure AD 可验证你的组织是否拥有该自定义域名。 可将 [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) 用于 Azure 中的 Azure/Office 365/外部 DNS 记录，或在[其他 DNS 注册机构](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)中添加 DNS 条目。

1. 登录到域的域名注册机构。 如果无权访问域名注册机构以更新 DNS 条目，请让具有此访问权限的个人或团队完成步骤 2 并在完成时通知你。
2. 通过添加 Azure AD 提供给 DNS 条目来更新域的 DNS 区域文件。 DNS 条目不会更改任何行为，例如邮件路由或 Web 托管。

## <a name="verify-the-custom-domain-name-in-azure-ad"></a>在 Azure AD 中验证自定义域名
一旦添加了 DNS 条目，就可以使用 Azure AD 验证域名。 只有在 DNS 记录传播完成之后，才能验证域名。 此传播通常只需要几秒钟，但有时可能需要一小时以上。 如果第一次验证不成功，请稍后重试。

1. 使用目录的全局管理员帐户登录到 [Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)。
2. 在左侧选择“域名”。
3. 在“directory-name - 域名”中，选择“添加域名”命令。 
  ![选择“添加”命令](./media/active-directory-domains-add-azure-portal/add-command.png)
3. 在“directory-name - 域名”中，选择需要验证但尚未验证的域名。
4. 在“domainname”（所选域名即为标题）中，选择“验证”以完成验证。

现在，可以 [分配包含自定义域名的用户名](active-directory-users-create-azure-portal.md)。 可使用自定义域名创建基于云的用户帐户，或更新先前同步的本地用户帐户信息。 还可以使用 [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) 或[图形 API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations) 更改已同步的用户帐户域后缀信息。

> [!TIP]
> 最多可添加 900 个托管域名。 若要配置所有域以便与 Active Directory 本地联合，最多可在每个目录中添加 450 个域名。 有关详细信息，请参阅[联合域名和托管域名](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain-concepts#federated-and-managed-domain-names)。

## <a name="troubleshooting"></a>故障排除
如果无法验证自定义域名，请尝试以下故障排除步骤：

1. **等候一小时**。 必须先传播 DNS 记录，Azure AD 才能验证域。 此过程可能需要一小时以上。
2. **确保已输入正确的 DNS 记录**。 请在该域的域名注册机构网站上完成此步骤。 出现以下情况时 Azure AD 无法验证域名 
  * DNS 条目不在 DNS 区域文件中
  * DNS 条目与 Azure AD 提供的 DNS 条目不完全匹配。 
  
  如果无权访问域名注册机构以更新域的 DNS 记录，请与组织内具有此访问权限的个人或团队共享 DNS 条目，并请他们添加 DNS 条目。
3. **从 Azure AD 的另一个目录删除域名**。 域名只能在单个目录中验证。 如果域名当前已在其他目录中经过验证，则在此目录中将其删除前，它无法在新目录上进行验证。 若要了解如何删除域名，请参阅 [管理自定义域名](active-directory-domains-manage-azure-portal.md)。    

## <a name="add-more-custom-domain-names"></a>添加更多自定义域名
如果组织使用多个自定义域名，例如“contoso.com”和“contosobank.com”，最多可以添加 900 个域名。 本文中的步骤可帮助你添加每个域名。

### <a name="learn-more"></a>了解详细信息
[Azure AD 中自定义域名的概念性概述](active-directory-add-domain-concepts.md)

[管理自定义域名](active-directory-domains-manage-azure-portal.md)

## <a name="next-steps"></a>后续步骤
在本快速入门中，你已了解如何将自定义域添加到 Azure AD。 

可使用以下链接在 Azure 门户中的 Azure AD 中添加新的自定义域。

> [!div class="nextstepaction"]
> [添加自定义域](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/QuickStart) 