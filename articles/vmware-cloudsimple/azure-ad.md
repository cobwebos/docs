---
title: Azure VMware 解决方案（按 CloudSimple）-在私有云上使用 Azure AD 作为标识源
description: 介绍如何将 Azure AD 作为标识提供程序添加到 CloudSimple 私有云上，以便对从 Azure 访问 CloudSimple 的用户进行身份验证
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1a5871a052998e9dd32d698c5a89f57064cc7d6b
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72987561"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>使用 Azure AD 作为 CloudSimple 私有云上的 vCenter 的标识提供者

可以将 CloudSimple 私有云 vCenter 设置为使用 Azure Active Directory （Azure AD）进行身份验证，以便 VMware 管理员可以访问 vCenter。 设置单一登录标识源后， **cloudowner**用户可将用户从标识源添加到 vCenter。  

可以通过以下任一方式设置 Active Directory 域和域控制器：

* Active Directory 在本地运行的域和域控制器
* Active Directory azure 订阅中作为虚拟机在 Azure 上运行的域和域控制器
* 新 Active Directory 在 CloudSimple 私有云中运行的域和域控制器
* Azure Active Directory 服务

本指南介绍将 Azure AD 设置为标识源所需的任务。  有关使用本地 Active Directory 或 Active Directory 在 Azure 中运行的信息，请参阅[设置 vCenter 标识源以使用 Active Directory](set-vcenter-identity.md) ，以获取有关设置标识源的详细说明。

## <a name="about-azure-ad"></a>关于 Azure AD

Azure AD 是 Microsoft 多租户、基于云的目录和标识管理服务。  Azure AD 提供了一个可缩放、一致和可靠的身份验证机制，使用户能够在 Azure 上进行身份验证和访问不同的服务。  它还为任何第三方服务提供安全 LDAP 服务，以将 Azure AD 用作身份验证/标识源。  Azure AD 结合了核心目录服务、高级标识监管和应用程序访问管理，可用于为管理私有云的用户提供对私有云的访问权限。

若要将 Azure AD 用作包含 vCenter 的标识源，必须设置 Azure AD 和 Azure AD 的域服务。 请按照以下说明操作：

1. [如何设置 Azure AD 和 Azure AD 域服务](#set-up-azure-ad-and-azure-ad-domain-services)
2. [如何在私有云 vCenter 上设置标识源](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>设置 Azure AD 和 Azure AD 域服务

在开始之前，需要具有全局管理员权限才能访问 Azure 订阅。  以下步骤给出了一般准则。 Azure 文档中包含详细信息。

### <a name="azure-ad"></a>Azure AD

> [!NOTE]
> 如果你已有 Azure AD，则可以跳过此部分。

1. 根据[Azure AD 文档](../active-directory/fundamentals/get-started-azure-ad.md)中所述，在订阅上设置 Azure AD。
2. 按照[注册 Azure Active Directory Premium](../active-directory/fundamentals/active-directory-get-started-premium.md)中所述，在订阅上启用 Azure Active Directory Premium。
3. 设置自定义域名并验证自定义域名，如[将自定义域名添加到 Azure Active Directory](../active-directory/fundamentals/add-custom-domain.md)中所述。
    1. 使用 Azure 上提供的信息，在域注册机构中设置 DNS 记录。
    2. 将自定义域名设置为主域。

您可以根据需要配置其他 Azure AD 功能。  这不是在 Azure AD 中启用 vCenter 身份验证所必需的。

### <a name="azure-ad-domain-services"></a>Azure AD 域服务

> [!NOTE]
> 这是启用 Azure AD 作为 vCenter 标识源的一个重要步骤。  若要避免任何问题，请确保所有步骤均正确执行。

1. 启用 Azure AD 域服务，如[使用 Azure 门户启用 Azure Active Directory 域服务](../active-directory-domain-services/active-directory-ds-getting-started.md)中所述。
2. 按照[使用 Azure 门户启用 Azure Active Directory 域服务](../active-directory-domain-services/active-directory-ds-getting-started-network.md)中所述，设置 Azure AD 域服务将使用的网络。
3. 如[使用 Azure 门户启用 Azure Active Directory 域服务](../active-directory-domain-services/active-directory-ds-getting-started-admingroup.md)中所述，配置用于管理 Azure AD 域服务的管理员组。
4. 根据[启用 Azure Active Directory 域服务](../active-directory-domain-services/active-directory-ds-getting-started-dns.md)中所述，更新 Azure AD 域服务的 DNS 设置。  如果要通过 Internet 连接到 AD，请将 Azure AD 域服务的公共 IP 地址的 DNS 记录设置为域名。
5. 为用户启用密码哈希同步。  此步骤启用对 NT LAN Manager （NTLM）和 Kerberos 身份验证所需的密码哈希的同步，以便 Azure AD 域服务。 设置密码哈希同步以后，用户即可使用其公司凭据登录到托管域。 请参阅[启用 Azure Active Directory 域服务的密码哈希同步](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)。
    1. 如果存在仅限云的用户，他们必须使用<a href="http://myapps.microsoft.com/" target="_blank">Azure AD 访问面板</a>更改其密码，以确保密码哈希以 NTLM 或 Kerberos 所需的格式存储。  按照为[仅限云的用户帐户的托管域启用密码哈希同步](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)中的说明进行操作。  必须针对单个用户和使用 Azure 门户或 Azure AD PowerShell cmdlet 在 Azure AD 目录中创建的任何新用户执行此步骤。 需要访问 Azure AD 域服务的用户必须使用<a href="http://myapps.microsoft.com/" target="_blank">Azure AD 访问面板</a>，并访问其配置文件以更改密码。

        > [!NOTE]
        > 如果你的组织具有仅限云的用户帐户，则需要使用 Azure Active Directory 域服务的所有用户必须更改其密码。 仅限云的用户帐户是在 Azure AD 目录中使用 Azure 门户或 Azure AD PowerShell cmdlet 创建的帐户。 此类用户帐户不是从本地目录同步的。

    2. 如果要同步本地 Active directory 中的密码，请按照[Active Directory 文档](../active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)中的步骤进行操作。

6.  按照为[Azure AD 域服务托管域配置安全 ldap （LDAPS）](../active-directory-domain-services/tutorial-configure-ldaps.md)中所述，在 Azure Active Directory 域服务上配置安全 ldap。
    1. 按 Azure 主题[获取安全 ldap 的证书](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)中所述，上传用于安全 ldap 的证书。  CloudSimple 建议使用证书颁发机构颁发的签名证书，以确保 vCenter 可以信任该证书。
    2. 启用安全 LDAP，如上所述，为[Azure AD 域服务托管域启用安全 ldap （LDAPS）](../active-directory-domain-services/tutorial-configure-ldaps.md)。
    3. 在配置标识源时，以 .cer 格式保存证书的公共部分（不带私钥）以用于 vCenter。
    4. 如果需要对 Azure AD 域服务的 Internet 访问，请启用 "允许通过 internet 安全访问 LDAP" 选项。
    5. 为 TCP 端口 636 Azure AD 域服务 NSG 添加入站安全规则。

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>在私有云 vCenter 上设置标识源

1. [提升](escalate-private-cloud-privileges.md)私有云 vCenter 的权限。
2. 收集为标识源设置所需的配置参数。

    | **选项** | **说明** |
    |------------|-----------------|
    | **名称** | 标识源的名称。 |
    | **用户的基本 DN** | 用户的基本可分辨名称。  对于 Azure AD，请使用： `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` 示例： `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`。|
    | **域名** | 域的 FDQN，例如 example.com。 不要在此文本框中提供 IP 地址。 |
    | **域别名** | *（可选）* 域 NetBIOS 名称。 如果使用的是 SSPI 身份验证，请将 Active Directory 域的 NetBIOS 名称添加为标识源的别名。 |
    | **组的基本 DN** | 组的基本可分辨名称。 对于 Azure AD，请使用： `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` 示例： `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **主服务器 URL** | 域的主域控制器 LDAP 服务器。<br><br>使用格式 `ldaps://hostname:port`。 对于 LDAPS 连接，此端口通常为636。 <br><br>在主或辅助 LDAP URL 中使用 `ldaps://` 时，需要为 Active Directory 服务器的 LDAPS 终结点建立信任的证书。 |
    | **辅助服务器 URL** | 用于故障转移的辅助域控制器 LDAP 服务器的地址。 |
    | **选择证书** | 如果要将 LDAPS 与 Active Directory LDAP 服务器或 OpenLDAP 服务器标识源结合使用，则在 "URL" 文本框中键入 `ldaps://` 后，将显示 "选择证书" 按钮。 不需要辅助 URL。 |
    | **用户名** | 域中用户的 ID，这些用户和组的基本 DN 至少具有只读访问权限。 |
    | **密码** | Username 指定的用户的密码。 |

3. 升级权限后，登录到私有云 vCenter。
4. 按照使用上一步骤中的值在[vCenter 上添加标识源](set-vcenter-identity.md#add-an-identity-source-on-vcenter)中的说明，将 Azure Active Directory 设置为标识源。
5. 按照 VMware 主题[将成员添加到 Vcenter 单一登录组](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)中所述，将 Azure AD 中的用户/组添加到 vcenter 组。

> [!CAUTION]
> 新用户必须仅添加到*云所有者组*、*云全局-群集管理组*、云全局*存储管理*组、云全局*网络管理*组或*云-全局-VM 管理*组的用户组。  添加到*管理员*组的用户将被自动删除。  只有服务帐户才能添加到*Administrators*组。

## <a name="next-steps"></a>后续步骤

* [了解私有云权限模型](learn-private-cloud-permissions.md)
