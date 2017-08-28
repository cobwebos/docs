---
title: "在 Azure AD 域服务中配置安全 LDAP (LDAPS) | Microsoft 文档"
description: "为 Azure AD 域服务托管域配置安全 LDAP (LDAPS)"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 9d563c45-9578-410d-96c8-355af64feae8
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 3aafe209aad7383cd0610d147b5fdba673023c93
ms.contentlocale: zh-cn
ms.lasthandoff: 08/16/2017

---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>为 Azure AD 域服务托管域配置安全 LDAP (LDAPS)

## <a name="before-you-begin"></a>开始之前
请确保已完成[任务 2 - 将安全 LDAP 证书导出到 .PFX 文件](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)。

选择是使用 Azure 门户预览版体验还是使用 Azure 经典门户来完成此任务。
> [!div class="op_single_selector"]
> * Azure 门户（预览版）：[使用 Azure 门户启用安全 LDAP](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
> * Azure 经典门户：[使用经典 Azure 门户启用安全 LDAP](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps-classic.md)
>
>


## <a name="task-3---enable-secure-ldap-for-the-managed-domain-using-the-classic-azure-portal"></a>任务 3：使用经典 Azure 门户为托管域启用安全 LDAP
若要启用安全 LDAP，请执行以下配置步骤：

1. 导航到 **[Azure 经典门户](https://manage.windowsazure.com)**。
2. 在左窗格中，选择“Active Directory”节点。
3. 选择已启用 Azure AD 域服务的 Azure AD 目录（也称为“租户”）。

    ![选择 Azure AD 目录](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. 单击“配置”  选项卡。

    ![目录的“配置”选项卡](./media/active-directory-domain-services-getting-started/configure-tab.png)
5. 向下滚动到标题为“域服务”的部分。 应会看到标题为“安全 LDAP (LDAPS)”的选项，如以下屏幕截图中所示：

    ![域服务配置部分](./media/active-directory-domain-services-admin-guide/secure-ldap-start.png)
6. 单击“配置证书...”按钮显示“设置安全 LDAP 的证书”对话框。

    ![配置安全 LDAP 的证书](./media/active-directory-domain-services-admin-guide/secure-ldap-configure-cert-page.png)
7. 单击“包含证书的 PFX 文件”后面的文件夹图标，指定要用于对托管域进行安全 LDAP 访问的证书所在的 PFX 文件。 此外，请输入将证书导出到 PFX 文件时指定的密码。 然后，单击底部的完成按钮。

    ![指定安全 LDAP 的 PFX 文件和密码](./media/active-directory-domain-services-admin-guide/secure-ldap-specify-pfx.png)
8. “配置”选项卡的“域服务”部分应该灰显，并且会处在“挂起...”状态几分钟。 在此期间，系统会验证 LDAPS 证书的正确性，同时为托管域配置安全 LDAP。

    ![安全 LDAP - 挂起状态](./media/active-directory-domain-services-admin-guide/secure-ldap-pending-state.png)

   > [!NOTE]
   > 为托管域启用安全 LDAP 大约需要 10 到 15 分钟时间。 如果提供的安全 LDAP 证书不符合所需的条件，则不会为目录启用安全 LDAP，并且会显示失败消息。 例如，域名不正确、证书已过期或即将过期。
   >
   >

9. 为托管域成功启用安全 LDAP 后，“挂起...”消息应会消失。 此时会显示证书的指纹。

    ![安全 LDAP 已启用](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled.png)

<br>

## <a name="task-4---enable-secure-ldap-access-over-the-internet"></a>任务 4 - 启用通过 Internet 进行安全 LDAP 访问

            **可选任务** - 如果不打算使用 LDAPS 来通过 Internet 访问托管域，请跳过此配置任务。

开始此任务之前，请确保已完成[任务 3](#task-3---enable-secure-ldap-for-the-managed-domain-using-the-classic-azure-portal) 中所述的步骤。

1. 应会在“配置”页的“域服务”部分中看到“启用通过 Internet 进行安全 LDAP 访问”的选项。 此选项设置为“否”，因为默认情况下已禁用通过安全 LDAP 对托管域进行 Internet 访问。

    ![安全 LDAP 已启用](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled2.png)
2. 将“启用通过 Internet 进行安全 LDAP 访问”切换为“是”。 在底部面板上单击“保存”按钮。
    ![安全 LDAP 已启用](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access.png)
3. “配置”选项卡的“域服务”部分应该灰显，并且会处在“挂起...”状态几分钟。 经过片刻时间后，将启用通过安全 LDAP 对托管域进行 Internet 访问。

    ![安全 LDAP - 挂起状态](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access-pending-state.png)

   > [!NOTE]
   > 为托管域启用通过安全 LDAP 的 Internet 访问大约需要 10 分钟时间。
   >
   >
4. 在成功启用通过 Internet 对托管域进行安全 LDAP 访问后，“挂起...”消息应会消失。 应会在“LDAPS 访问的外部 IP 地址”字段中看到可用于通过 LDAPS 访问目录的外部 IP 地址。

    ![安全 LDAP 已启用](./media/active-directory-domain-services-admin-guide/secure-ldap-internet-access-enabled.png)

<br>

## <a name="task-5---configure-dns-to-access-the-managed-domain-from-the-internet"></a>任务 5 - 配置 DNS 以便从 Internet 访问托管域

            **可选任务** - 如果不打算使用 LDAPS 来通过 Internet 访问托管域，请跳过此配置任务。

开始此任务之前，请确保已完成[任务 4](#task-4---enable-secure-ldap-access-over-the-internet) 中所述的步骤。

为托管域启用通过 Internet 的安全 LDAP 访问后，需要更新 DNS，使客户端计算机能够找到此托管域。 在任务 4 的最后阶段，“配置”选项卡的“LDAPS 访问的外部 IP 地址”中会显示外部 IP 地址。

请配置外部 DNS 提供程序，使托管域的 DNS 名称（例如“ldaps.contoso100.com”）指向此外部 IP 地址。 在本例中，需要创建以下 DNS 条目：

    ldaps.contoso100.com  -> 52.165.38.113

大功告成。现在，可以使用安全 LDAP 通过 Internet 连接到托管域。

> [!WARNING]
> 请记住，客户端计算机必须信任 LDAPS 证书的颁发者，才能成功使用 LDAPS 连接到托管域。 如果使用企业证书颁发机构或公开的受信任证书颁发机构，则不需要采取任何操作，因为客户端计算机信任这些证书颁发者。 如果使用自签名证书，则必须在客户端计算机的受信任证书存储中安装自签名证书的公开部分。
>
>


## <a name="lock-down-ldaps-access-to-your-managed-domain-over-the-internet"></a>通过 Internet 锁定对托管域的 LDAPS 访问
> [!NOTE]
> 可选任务 - 如果尚未启用 LDAPS 来通过 Internet 访问托管域，请跳过此配置任务。
>
>

开始此任务之前，请确保已完成[任务 4](#task-4---enable-secure-ldap-access-over-the-internet) 中所述的步骤。

如果公开托管域以便 LDAPS 通过 Internet 进行访问，则会对安全造成威胁。 可在安全 LDAP 所用的端口处（即端口 636）通过 Internet 访问托管域。 因此，可选择将对托管域的访问限制为特定的已知 IP 地址。 为了提高安全性，请创建网络安全组 (NSG) 并将其与已启用 Azure AD 域服务的子网关联。

下表是可以配置的示例 NSG，通过 Internet 锁定安全 LDAP 访问。 NSG 包含一组规则，允许仅从指定的一组 IP 地址通过 TCP 端口 636 进行入站 LDAPS 访问。 默认的“DenyAll”规则适用于来自 Internet 的所有其他入站流量。 NSG 规则允许从指定 IP 地址通过 Internet 进行 LDAPS 访问，此规则的优先级比 DenyAll NSG 规则的优先级高。

![通过 Internet 进行安全 LDAPS 访问的示例 NSG](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**详细信息** - [网络安全组](../virtual-network/virtual-networks-nsg.md)。

<br>

## <a name="related-content"></a>相关内容
* [Azure AD 域服务 - 入门指南](active-directory-ds-getting-started.md)
* [管理受 Azure AD 域服务管理的域](active-directory-ds-admin-guide-administer-domain.md)
* [管理 Azure AD 域服务托管域上的组策略](active-directory-ds-admin-guide-administer-group-policy.md)
* [网络安全组](../virtual-network/virtual-networks-nsg.md)
* [创建网络安全组](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

