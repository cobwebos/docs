---
title: "将自定义域名添加到 Azure Active Directory | Microsoft Docs"
description: "如何将公司域名添加到 Azure Active Directory，以及如何验证域名。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: d97e57c6-578a-4929-8fb8-42e858a711c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: curtand
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: ad72f768add7edc1d34a85c27dc2aa1b4e4b3a50
ms.contentlocale: zh-cn
ms.lasthandoff: 07/26/2017

---
# <a name="add-a-custom-domain-name-to-azure-active-directory"></a>将自定义域名添加到 Azure Active Directory
> [!div class="op_single_selector"]
> * [Azure 门户](active-directory-domains-add-azure-portal.md)
> * [Azure 经典门户](active-directory-add-domain.md)
> 

使用 Azure Active Directory (Azure AD) 时，也可以将企业域名添加到 Azure AD。 你可能拥有组织用来从事商业经营的域名和使用公司域名登录的用户。 通过在 Azure AD 中添加域名可以在目录中分配用户熟悉的用户名，例如“alice@contoso.com”。 过程很简单：

1. 将自定义域名添加到目录
2. 在域名注册机构中为域名添加 DNS 条目
3. 在 Azure AD 中验证自定义域名

## <a name="how-do-i-add-a-domain-name"></a>如何添加域名？
1. 使用目录全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“更多服务”，在文本框中输入 **Azure Active Directory**，并选择“Enter”。
   
   ![打开“用户管理”](./media/active-directory-domains-add-azure-portal/user-management.png)
3. 在“directory-name”边栏选项卡中，选择“域名”。
4. 在“directory-name - 域名”边栏选项卡中，选择“添加”命令。
   
   ![选择“添加”命令](./media/active-directory-domains-add-azure-portal/add-command.png)
5. 在“域名”边栏选项卡的相应框中输入自定义域的名称（例如“contoso.com”），并选择“添加域”。 请务必包含 .com、.net 或其他顶级扩展名。
6. 在“domainname”边栏选项卡（即打开的且其名称中有新域名的边栏选项卡）中获取 DNS 条目信息，Azure AD 将使用此信息验证用户的组织是否拥有该自定义域名。
   
   ![获取 DNS 条目信息](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

添加域名后，Azure AD 必须验证组织是否拥有该域名。 必须先在 DNS 区域文件中添加该域名的 DNS 条目，Azure AD 才能执行此验证。 此任务是在域名注册机构网站上执行的。

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>在域名注册机构中为域添加 DNS 条目
在 Azure AD 中使用自定义域名的下一个步骤就是更新域的 DNS 区域文件。 这可让 Azure AD 确认组织拥有该自定义域名。

1. 登录到域的域名注册机构。 如果无权访问域名注册机构以更新 DNS 条目，请让具有此访问权限的个人或团队完成步骤 2 并在完成时通知你。
2. 通过添加 Azure AD 提供给 DNS 条目来更新域的 DNS 区域文件。 此 DNS 条目可让 Azure AD 验证你是否拥有该域。 DNS 条目不会更改任何行为，例如邮件路由或 Web 托管。

有关添加 DNS 条目的帮助，请参阅 [有关在常见 DNS 注册机构中添加 DNS 条目的说明](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>使用 Azure AD 验证域名
一旦添加了 DNS 条目，就可以使用 Azure AD 验证域名。

只有在 DNS 记录传播完成之后，才能验证域名。 此传播通常只需要几秒钟，但有时可能需要一小时以上。 如果第一次验证不成功，请稍后重试。

1. 使用目录全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“浏览”，在文本框中输入“用户管理”，并选择 **Enter**。
   
   ![打开“用户管理”](./media/active-directory-domains-add-azure-portal/user-management.png)
3. 在“用户管理 - 域名”边栏选项卡中，选择需要验证但尚未验证的域名。
4. 在“domainname”边栏选项卡（即打开的且其名称中有新域名的边栏选项卡）中，选择“验证”完成验证步骤。

现在，可以 [分配包含自定义域名的用户名](active-directory-users-create-azure-portal.md)。

## <a name="troubleshooting"></a>故障排除
如果无法验证自定义域名，请尝试以下方法。 我们从最常见到最不常见的原因逐一分析。

1. **等候一小时**。 必须先传播 DNS 记录，Azure AD 才能验证域。 这可能需要一小时以上。
2. **确保已输入正确的 DNS 记录**。 请在该域的域名注册机构网站上完成此步骤。 如果 DNS 条目不在 DNS 区域文件中，或者与 Azure AD 提供给 DNS 条目不完全匹配，则 Azure AD 无法验证域名。 如果无权访问域名注册机构以更新域的 DNS 记录，请与组织内具有此访问权限的个人或团队共享 DNS 条目，并请他们添加 DNS 条目。
3. **从 Azure AD 的另一个目录删除域名**。 域名只能在单个目录中验证。 如果域名先前在另一个目录中验证过，则必须先在那里将其删除后，才可在新的目录中验证。 若要了解如何删除域名，请参阅 [管理自定义域名](active-directory-domains-manage-azure-portal.md)。    

## <a name="add-more-custom-domain-names"></a>添加更多自定义域名
如果组织使用多个自定义域名，例如“contoso.com”和“contosobank.com”，最多可以添加 900 个域名。 请使用本文中的相同步骤来添加每个域名。

## <a name="next-steps"></a>后续步骤
[管理自定义域名](active-directory-domains-manage-azure-portal.md)


