---
title: Azure VMware 解决方案（按云简单 ） - 使用 Azure AD 作为私有云上的身份源
description: 描述如何在云简单私有云上添加 Azure AD 作为标识提供程序，以验证从 Azure 访问云简单的用户
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 674ca8bea110d60557d1e50e7b68c9c3f7a92bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564578"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>使用 Azure AD 作为云简单私有云上 vCenter 的标识提供程序

您可以设置云简单私有云 vCenter，以便使用 Azure 活动目录 （Azure AD） 进行身份验证，以便 VMware 管理员访问 vCenter。 设置单一登录标识源后，**云所有者**用户可以将用户从标识源添加到 vCenter。  

您可以通过以下任何方式设置活动目录域和域控制器：

* 在本地运行的活动目录域和域控制器
* 在 Azure 订阅中作为虚拟机在 Azure 上运行的活动目录域和域控制器
* 在云简单私有云中运行的新活动目录域和域控制器
* Azure Active Directory 服务

本指南介绍了将 Azure AD 设置为标识源所需的任务。  有关使用在 Azure 中运行的本地活动目录或活动目录的信息，请参阅[设置 vCenter 标识源以在](set-vcenter-identity.md)设置标识源时使用 Active Directory 以获取详细说明。

## <a name="about-azure-ad"></a>关于 Azure AD

Azure AD 是 Microsoft 多租户、基于云的目录和标识管理服务。  Azure AD 为用户提供了可扩展、一致和可靠的身份验证机制，以便用户对 Azure 上的不同服务进行身份验证和访问。  它还为任何第三方服务提供安全的 LDAP 服务，以便使用 Azure AD 作为身份验证/标识源。  Azure AD 结合了核心目录服务、高级标识治理和应用程序访问管理，可用于为管理私有云的用户授予对私有云的访问权限。

要使用 Azure AD 作为具有 vCenter 的标识源，必须设置 Azure AD 和 Azure AD 域服务。 请按照以下说明操作：

1. [如何设置 Azure AD 和 Azure AD 域服务](#set-up-azure-ad-and-azure-ad-domain-services)
2. [如何在私有云 vCenter 上设置标识源](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>设置 Azure AD 和 Azure AD 域服务

在开始之前，您需要使用全局管理员权限访问 Azure 订阅。  以下步骤给出了一般准则。 详细信息包含在 Azure 文档中。

### <a name="azure-ad"></a>Azure AD

> [!NOTE]
> 如果已有 Azure AD，则可以跳过此部分。

1. 在订阅上设置 Azure AD，如[Azure AD 文档中](../active-directory/fundamentals/get-started-azure-ad.md)所述。
2. 在订阅上启用 Azure 活动目录高级版，如[注册 Azure 活动目录高级版](../active-directory/fundamentals/active-directory-get-started-premium.md)中所述。
3. 设置自定义域名并验证自定义域名，如[将自定义域名添加到 Azure 活动目录](../active-directory/fundamentals/add-custom-domain.md)中所述。
    1. 使用 Azure 上提供的信息在域注册商上设置 DNS 记录。
    2. 将自定义域名设置为主域。

您可以选择配置其他 Azure AD 功能。  使用 Azure AD 启用 vCenter 身份验证不需要这些身份验证。

### <a name="azure-ad-domain-services"></a>Azure AD 域服务

> [!NOTE]
> 这是启用 Azure AD 作为 vCenter 标识源的重要步骤。  为避免任何问题，请确保正确执行所有步骤。

1. 使用 Azure 门户 启用[Azure 活动目录域服务](../active-directory-domain-services/active-directory-ds-getting-started.md)中所述的 Azure AD 域服务。
2. 设置 Azure AD 域服务将使用的网络，如使用 Azure[门户启用 Azure 活动目录域服务](../active-directory-domain-services/active-directory-ds-getting-started-network.md)中所述。
3. 配置管理员组以管理 Azure AD 域服务，如[启用 Azure 活动目录域服务时使用 Azure 门户](../active-directory-domain-services/active-directory-ds-getting-started-admingroup.md)。
4. 更新 Azure AD 域服务的 DNS 设置，如[启用 Azure 活动目录域服务](../active-directory-domain-services/active-directory-ds-getting-started-dns.md)中所述。  如果要通过 Internet 连接到 AD，请将 Azure AD 域服务的公共 IP 地址的 DNS 记录设置为域名。
5. 为用户启用密码哈希同步。  此步骤支持将 NT LAN 管理器 （NTLM） 和 Kerberos 身份验证所需的密码哈希同步到 Azure AD 域服务。 设置密码哈希同步以后，用户即可使用其公司凭据登录到托管域。 请参阅[将密码哈希同步连接到 Azure 活动目录域服务](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)。
    1. 如果存在仅云用户，则必须使用<a href="http://myapps.microsoft.com/" target="_blank">Azure AD 访问面板</a>更改其密码，以确保密码哈希以 NTLM 或 Kerberos 所需的格式存储。  按照[启用密码哈希同步到仅云用户帐户的托管域中的](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)说明。  此步骤必须针对使用 Azure 门户或 Azure AD PowerShell cmdlet 在 Azure AD 目录中创建的任何新用户执行。 需要访问 Azure AD 域服务的用户必须使用<a href="http://myapps.microsoft.com/" target="_blank">Azure AD 访问面板</a>并访问其配置文件以更改密码。

        > [!NOTE]
        > 如果组织有仅限云的用户帐户，则需要使用 Azure Active Directory 域服务的所有用户必须更改其密码。 仅限云的用户帐户是在 Azure AD 目录中使用 Azure 门户或 Azure AD PowerShell cmdlet 创建的帐户。 此类用户帐户不是从本地目录同步的。

    2. 如果要从本地活动目录中同步密码，请按照[活动目录文档中](../active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)的步骤操作。

6.  按照[为 Azure AD 域服务托管域配置安全 LDAP （LDAPS）](../active-directory-domain-services/tutorial-configure-ldaps.md)中所述，在 Azure 活动目录域服务上配置安全 LDAP。
    1. 上传证书供安全 LDAP 使用，如 Azure 主题所述，[获取安全 LDAP 的证书](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)。  CloudSimple 建议使用证书颁发机构颁发的签名证书，以确保 vCenter 可以信任证书。
    2. 启用安全 LDAP，如所述[为 Azure AD 域服务托管域启用安全 LDAP （LDAPS）。](../active-directory-domain-services/tutorial-configure-ldaps.md)
    3. 以 .cer 格式保存证书的公共部分（没有私钥），以便在配置标识源时与 vCenter 一起使用。
    4. 如果需要 Internet 访问 Azure AD 域服务，请启用"允许通过互联网安全访问 LDAP"选项。
    5. 为 TCP 端口 636 添加 Azure AD 域服务 NSG 的入站安全规则。

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>在私有云 vCenter 上设置标识源

1. [升级](escalate-private-cloud-privileges.md)私有云 vCenter 的权限。
2. 收集设置标识源所需的配置参数。

    | **选项** | **说明** |
    |------------|-----------------|
    | **名称** | 标识源的名称。 |
    | **用户基本 DN** | 用户的基本可分辨名称。  对于 Azure AD，`OU=AADDC Users,DC=<domain>,DC=<domain suffix>`请使用：`OU=AADDC Users,DC=cloudsimplecustomer,DC=com`示例： 。|
    | **域名** | 例如，域的 FQDN example.com。 请勿在此文本框中提供 IP 地址。 |
    | **域别名** | *（可选）* 域 NetBIOS 名称。 如果使用 SSPI 身份验证，则将 Active Directory 域的 NetBIOS 名称添加为标识源的别名。 |
    | **组的基本 DN** | 组的基本可分辨名称。 对于 Azure AD，`OU=AADDC Users,DC=<domain>,DC=<domain suffix>`请使用：示例：`OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **主服务器 URL** | 域的主域控制器 LDAP 服务器。<br><br>使用格式 `ldaps://hostname:port`。 对于 LDAPS 连接，端口通常为 636。 <br><br>当您 `ldaps://` 在主数据库或辅助 LDAP URL 中使用时，需要为活动目录服务器的 LDAPS 终结点建立信任的证书。 |
    | **辅助服务器 URL** | 用于故障转移的辅助域控制器 LDAP 服务器的地址。 |
    | **选择证书** | 如果要将 LDAPS 与活动目录 LDAP 服务器或 OpenLDAP 服务器标识源一起使用，则在 `ldaps://` URL 文本框中键入后将显示"选择证书"按钮。 不需要辅助 URL。 |
    | **用户** | 域中用户 ID，该用户和组对基本 DN 具有最少的只读访问权限。 |
    | **密码** | 由用户名指定的用户的密码。 |

3. 权限升级后登录到私有云 vCenter。
4. 使用上一步中的值在[vCenter 上添加标识源](set-vcenter-identity.md#add-an-identity-source-on-vcenter)中的说明将 Azure 活动目录设置为标识源。
5. 将用户/组从 Azure AD 添加到 vCenter 组，如 VMware 主题中所述，[将成员添加到 vCenter 单一登录组](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)。

> [!CAUTION]
> 新用户只能添加到*云所有者组*、*云-全球群集-管理员组*、*云-全球-存储-管理员组*、*云-全球-网络-管理员组*或*云-全球-VM-管理员组*。  添加到*管理员*组的用户将自动删除。  只能将服务帐户添加到*管理员*组。

## <a name="next-steps"></a>后续步骤

* [了解私有云权限模型](learn-private-cloud-permissions.md)
