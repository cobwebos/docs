---
title: 在 Azure AD 域服务中启用安全 LDAP (LDAPS) | Microsoft Docs
description: 为 Azure AD 域服务托管域启用安全 LDAP (LDAPS)
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: maheshu
ms.openlocfilehash: befab32a9daf5a22a326396c84223e07d401f72b
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502788"
---
# <a name="enable-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>为 Azure AD 域服务托管域启用安全 LDAP (LDAPS)

## <a name="before-you-begin"></a>开始之前
完成[任务 2 - 将安全 LDAP 证书导出到 .PFX 文件](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)。


## <a name="task-3-enable-secure-ldap-for-the-managed-domain-using-the-azure-portal"></a>任务 3：使用 Azure 门户为托管域启用安全 LDAP
若要启用安全 LDAP，请执行以下配置步骤：

1. 导航到 [Azure 门户](https://portal.azure.com)。

2. 在“搜索资源”搜索框中搜索“域服务”。 从搜索结果中“选择 Azure AD 域服务”。 “Azure AD 域服务”页将列出托管域。

    ![查找正在预配的托管域](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. 单击托管域的名称（例如，“contoso100.com”），以查看有关域的更多详细信息。

    ![域服务 - 预配状态](./media/getting-started/domain-services-provisioning-state.png)

3. 在导航窗格中单击“安全 LDAP”。

    ![“域服务 - 安全 LDAP”页](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. 默认情况下，已禁用对托管域的安全 LDAP 访问。 将“安全 LDAP”切换为“启用”。

    ![启用安全 LDAP](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. 默认情况下，已禁用通过 Internet 对托管域的安全 LDAP 访问。 如果需要，将“允许通过 Internet 进行安全 LDAP 访问”切换为“启用”。

    > [!WARNING]
    > 在通过 Internet 启用安全 LDAP 访问时，你的域容易受到来自 Internet 的密钥搜索攻击。 因此，我们建议设置 NSG 以将访问锁定到所需的源 IP 地址范围。 请参阅[通过 Internet 锁定对托管域的 LDAPS 访问](#task-5---lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet)的说明。
    >

6. 单击具有安全 LDAP 证书的 .PFX 文件后面的文件夹图标。 指定具有证书的 PFX 文件的路径，以便对托管域进行安全 LDAP 访问。

7. 指定用于解密 .PFX 文件的密码。 请提供将证书导出到 PFX 文件时所用的密码。

8. 完成后，请单击“保存”按钮。

9. 你将收到通知，告知正在为托管域配置安全 LDAP。 完成此操作之前，无法修改域的其他设置。

    ![为托管域配置安全 LDAP](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configuring.png)

> [!NOTE]
> 为托管域启用安全 LDAP 大约需要 10 到 15 分钟时间。 如果提供的安全 LDAP 证书不符合所需的条件，则不会为目录启用安全 LDAP，并且会显示失败消息。 例如，域名不正确、证书已过期或即将过期。 在这种情况下，请使用有效证书重试。
>
>

## <a name="next-step"></a>后续步骤
[任务 4：配置 DNS 以便从 Internet 访问托管域](active-directory-ds-ldaps-configure-dns.md)
