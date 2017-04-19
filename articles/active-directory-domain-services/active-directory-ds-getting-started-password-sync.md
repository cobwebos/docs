---
title: "Azure Active Directory 域服务：启用密码同步 | Microsoft Docs"
description: "Azure Active Directory 域服务入门"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 5a32a0df-a3ca-4ebe-b980-91f58f8030fc
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 51e4665b6372859021237144a0bf7f6763a2638b
ms.lasthandoff: 04/14/2017


---
# <a name="enable-password-synchronization-with-azure-active-directory-domain-services"></a>启用 Azure Active Directory 域服务的密码同步
在前述任务中，你为 Azure Active Directory (Azure AD) 租户启用了 Azure Active Directory 域服务 (AD DS)。 下一任务是启用凭据哈希，这是进行 NT LAN Manager (NTLM) 和 Kerberos 身份验证所必需的，否则无法通过 Azure Active Directory 域服务进行同步。 设置凭据同步以后，用户即可使用其公司凭据登录到托管域。

各过程并不相同，具体取决于组织是具有仅限云的 Azure AD 租户，还是被设置为通过 Azure AD Connect 与本地目录同步。

> [!div class="op_single_selector"]
> * [仅限云的 Azure AD 租户](active-directory-ds-getting-started-password-sync.md)
> * [同步的 Azure AD 租户](active-directory-ds-getting-started-password-sync-synced-tenant.md)
>
>

## <a name="task-5-enable-password-synchronization-with-azure-active-directory-domain-services-for-a-cloud-only-azure-ad-tenant"></a>任务 5：对仅限云的 Azure AD 租户启用 Azure Active Directory 域服务密码同步
Azure Active Directory 域服务需要凭据哈希的格式适用于 NTLM 和 Kerberos 身份验证，以对托管域上的用户进行身份验证。 除非你为租户启用了 Azure Active Directory 域服务，否则 Azure AD 不会以 NTLM 或 Kerberos 身份验证所需的格式生成或存储凭据哈希。 出于显而易见的安全考虑，Azure AD 也不以明文形式存储任何凭据。 因此，根据用户的现有凭据，Azure AD 没有方法来生成这些 NTLM 或 Kerberos 凭据哈希。

> [!NOTE]
> 如果你的组织具有仅限云的 Azure AD 租户，则需要使用 Azure Active Directory 域服务的用户必须更改其密码。
>
>

此密码更改过程会导致在 Azure AD 中生成进行 Kerberos 和 NTLM 身份验证时 Azure Active Directory 域服务所需的凭据哈希。 也可以对需要使用 Azure Active Directory 域服务的所有租户中的用户终止密码或指示这些用户更改其密码。

### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-azure-ad-tenant"></a>对仅限云的 Azure AD 租户启用 NTLM 和 Kerberos 凭据哈希生成
下面是你需要向用户提供的说明，以便用户可以更改其密码：

1. 转到组织的 [Azure AD 访问面板](http://myapps.microsoft.com)页。
2. 在“访问面板”窗口中，选择“配置文件”选项卡。
3. 单击“更改密码”磁贴。

    ![Azure AD 访问面板的“更改密码”磁贴](./media/active-directory-domain-services-getting-started/user-change-password.png)

   > [!NOTE]
   > 如果在“访问面板”窗口中没有显示“更改密码”选项，请确保组织已配置 [Azure AD 中的密码管理](../active-directory/active-directory-passwords-getting-started.md)。
   >
   >
4. 在“更改密码”页上，键入现有（旧）密码，然后键入新密码并确认。

    ![为 Azure AD 域服务创建虚拟网络。](./media/active-directory-domain-services-getting-started/user-change-password2.png)

5. 单击“ **提交**”。

更改密码后，新密码数分钟后将在 Azure Active Directory 域服务中可用。 再过几分钟后（通常情况下，大约为 20 分钟），你就可以使用新更改的密码登录到已加入托管域的计算机了。

## <a name="next-steps"></a>后续步骤
* [如何更新自己的密码](../active-directory/active-directory-passwords-update-your-own-password.md#reset-my-password)
* [Azure AD 中的密码管理入门](../active-directory/active-directory-passwords-getting-started.md)
* [对已同步的 Azure AD 租户启用 Azure Active Directory 域服务密码同步](active-directory-ds-getting-started-password-sync-synced-tenant.md)
* [管理 Azure Active Directory 域服务托管域](active-directory-ds-admin-guide-administer-domain.md)
* [将 Windows 虚拟机加入到 Azure Active Directory 域服务托管域](active-directory-ds-admin-guide-join-windows-vm.md)
* [将 Red Hat Enterprise Linux 虚拟机加入到 Azure Active Directory 域服务托管域](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

