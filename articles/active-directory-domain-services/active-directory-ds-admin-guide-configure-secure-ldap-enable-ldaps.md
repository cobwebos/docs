---
title: "在 Azure AD 域服务中配置安全 LDAP (LDAPS) | Microsoft 文档"
description: "为 Azure AD 域服务托管域配置安全 LDAP (LDAPS)"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: maheshu
ms.openlocfilehash: d2ef65bb4dc8e12a18265ae8264def2bb32e191f
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2017
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>为 Azure AD 域服务托管域配置安全 LDAP (LDAPS)

## <a name="before-you-begin"></a>开始之前
请确保已完成[任务 2 - 将安全 LDAP 证书导出到 .PFX 文件](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)。


## <a name="task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal"></a>任务 3：使用 Azure 门户为托管域启用安全 LDAP
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
5. 默认情况下，已禁用通过 Internet 对托管域的安全 LDAP 访问。 将“允许通过 Internet 进行安全 LDAP 访问”切换为“启用”。 

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

<br>

## <a name="task-4---configure-dns-to-access-the-managed-domain-from-the-internet"></a>任务 4 - 配置 DNS 以便从 Internet 访问托管域
> [!NOTE]
> **可选任务** - 如果不打算使用 LDAPS 来通过 Internet 访问托管域，请跳过此配置任务。
>
>

开始此任务之前，请确保已完成[任务 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview) 中所述的步骤。

为托管域启用通过 Internet 的安全 LDAP 访问后，需要更新 DNS，使客户端计算机能够找到此托管域。 在任务 3 的最后阶段，“LDAPS 访问的外部 IP 地址”中的“属性”选项卡中会显示外部 IP 地址。

请配置外部 DNS 提供程序，使托管域的 DNS 名称（例如“ldaps.contoso100.com”）指向此外部 IP 地址。 例如，创建以下 DNS 条目：

    ldaps.contoso100.com  -> 52.165.38.113

大功告成。现在，可以使用安全 LDAP 通过 Internet 连接到托管域。

> [!WARNING]
> 请记住，客户端计算机必须信任 LDAPS 证书的颁发者，才能成功使用 LDAPS 连接到托管域。 如果使用公开的受信任证书颁发机构，则不需要采取任何操作，因为客户端计算机信任这些证书颁发者。 如果使用自签名证书，请在客户端计算机的受信任证书存储中安装自签名证书的公开部分。
>
>


## <a name="task-5---lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>任务 5 - 通过 Internet 锁定对托管域的安全 LDAP 访问
> [!NOTE]
> 如果尚未启用 LDAPS 来通过 Internet 访问托管域，请跳过此配置任务。
>
>

开始此任务之前，请确保已完成[任务 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-azure-portal-preview) 中所述的步骤。

如果公开托管域以便 LDAPS 通过 Internet 进行访问，则会对安全造成威胁。 可在安全 LDAP 所用的端口处（即端口 636）通过 Internet 访问托管域。 因此，可选择将对托管域的访问限制为特定的已知 IP 地址。 为了提高安全性，请创建网络安全组 (NSG) 并将其与已启用 Azure AD 域服务的子网关联。

下表是可以配置的示例 NSG，通过 Internet 锁定安全 LDAP 访问。 NSG 包含一组规则，允许仅从指定的一组 IP 地址通过 TCP 端口 636 进行入站安全 LDAP 访问。 默认的“DenyAll”规则适用于来自 Internet 的所有其他入站流量。 NSG 规则允许从指定 IP 地址通过 Internet 进行 LDAPS 访问，此规则的优先级比 DenyAll NSG 规则的优先级高。

![通过 Internet 进行安全 LDAPS 访问的示例 NSG](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**详细信息** - [网络安全组](../virtual-network/virtual-networks-nsg.md)。

<br>


## <a name="troubleshooting"></a>故障排除
如果在使用安全 LDAP 连接到托管域时遇到问题，请执行以下故障排除步骤：
* 请确保安全 LDAP 证书的证书颁发者链在客户端上受信任。 要建立信任，可选择向客户端上受信任的根证书存储添加根证书颁发机构。
* 验证安全 LDAP 证书的颁发机构不是陌生 Windows 计算机上默认不受信用的中间证书颁发机构。
* 验证 LDAP 客户端（例如 ldp.exe）连接到安全 LDAP 终结点时使用的是 DNS 名称，而不是 IP 地址。
* 验证 LDAP 客户端所连接的 DNS 名称解析为托管域上安全 LDAP 的公共 IP 地址。
* 验证托管域的安全 LDAP 证书具有“使用者”或“使用者可选名称”属性中的 DNS 名称。

如果在使用安全 LDAP 连接托管域的过程中仍遇到问题，请[联系产品团队](active-directory-ds-contact-us.md)寻求帮助。 包括以下有助于优化问题诊断的信息：
* ldp.exe 建立连接并失败的屏幕截图。
* Azure AD 租户 ID，以及托管域的 DNS 域名。
* 尝试绑定为的完全匹配用户名。


## <a name="related-content"></a>相关内容
* [Azure AD 域服务 - 入门指南](active-directory-ds-getting-started.md)
* [管理受 Azure AD 域服务管理的域](active-directory-ds-admin-guide-administer-domain.md)
* [管理 Azure AD 域服务托管域上的组策略](active-directory-ds-admin-guide-administer-group-policy.md)
* [网络安全组](../virtual-network/virtual-networks-nsg.md)
* [创建网络安全组](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)
