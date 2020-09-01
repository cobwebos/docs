---
title: 混合 FIDO2 安全密钥部署的常见问题解答-Azure Active Directory
description: '了解有关使用 Azure Active Directory (预览版的无密码混合 FIDO2 安全密钥登录的一些常见问题) '
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/19/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16e232cedb13dc246bf7a568adfad401c1fe3eb8
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236599"
---
# <a name="deployment-frequently-asked-questions-faqs-for-hybrid-fido2-security-keys-in-azure-ad-preview"></a>部署常见问题 (Azure AD () 预览版中的混合 FIDO2 安全密钥) 的常见问题

本文介绍了部署常见问题 (常见问题解答) 用于混合 Azure AD 加入设备和无密码登录到本地资源。 使用此无密码功能，可以使用 FIDO2 安全密钥在 Windows 10 设备上启用混合 Azure AD 加入设备的 Azure AD 身份验证。 用户可以使用新式凭据（如 FIDO2 密钥）在其设备上登录 Windows，并 Active Directory 域服务 (使用无缝单一登录 AD DS) 基于的资源，通过无缝单一登录 (的资源。

支持在混合环境中使用以下用户方案：

* 使用 FIDO2 安全密钥登录到混合 Azure AD 联接设备，并获取对本地资源的 SSO 访问权限。
* 使用 FIDO2 安全密钥登录到 Azure AD 联接的设备，并获取对本地资源的 SSO 访问权限。

若要开始使用 FIDO2 安全密钥以及对本地资源的混合访问，请参阅以下文章：

* [无密码 FIDO2 安全密钥](howto-authentication-passwordless-security-key.md)
* [无密码 Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [无密码本地](howto-authentication-passwordless-security-key-on-premises.md)

> [!NOTE]
> FIDO2 安全密钥是 Azure Active Directory 的公共预览功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="security-keys"></a>安全密钥

* [我的组织需要双重身份验证来访问资源。为支持此要求，我该做什么？](#my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement)
* [在哪里可以找到符合条件的 FIDO2 安全密钥？](#where-can-i-find-compliant-fido2-security-keys)
* [如果丢失了安全密钥，我该怎么办？](#what-if-i-lose-my-security-key)
* [如何在 FIDO2 安全密钥上保护数据？](#how-is-the-data-protected-on-the-fido2-security-key)
* [FIDO2 安全密钥的注册如何工作？](#how-does-the-registering-of-fido2-security-keys-work)
* [管理员是否有办法直接为用户预配密钥？](#is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly)

### <a name="my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>我的组织需要多重身份验证才能访问资源。 为支持此要求，我该做什么？

FIDO2 安全密钥提供各种形式的因素。 请联系感兴趣的设备制造商，讨论如何使用 PIN 或生物识别功能来启用其设备，作为第二个因素。 有关支持的提供程序的列表，请参阅 [FIDO2 security keys providers](concept-authentication-passwordless.md#fido2-security-key-providers)。

### <a name="where-can-i-find-compliant-fido2-security-keys"></a>在哪里可以找到符合条件的 FIDO2 安全密钥？

有关支持的提供程序的列表，请参阅 [FIDO2 security keys providers](concept-authentication-passwordless.md#fido2-security-key-providers)。

### <a name="what-if-i-lose-my-security-key"></a>如果安全密钥丢失了，该怎么办？

可以通过导航到 " **安全信息** " 页并删除 FIDO2 安全密钥，删除 Azure 门户中的密钥。

### <a name="how-is-the-data-protected-on-the-fido2-security-key"></a>如何在 FIDO2 安全密钥上保护数据？

FIDO2 安全密钥具有保护存储在其中的私钥的安全 enclaves。 FIDO2 安全密钥还包含内置的反攻击属性，如 Windows Hello 中的，你无法在其中提取私钥。

### <a name="how-does-the-registering-of-fido2-security-keys-work"></a>FIDO2 安全密钥的注册如何工作？

有关如何注册和使用 FIDO2 安全密钥的详细信息，请参阅 [Enable 无密码 security key sign](howto-authentication-passwordless-security-key.md)。

### <a name="is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly"></a>管理员是否有办法直接为用户预配密钥？

不，此时不能。

## <a name="prerequisites"></a>先决条件

* [如果没有 internet 连接，此功能是否起作用？](#does-this-feature-work-if-theres-no-internet-connectivity)
* [需要打开哪些特定的终结点才能 Azure AD？](#what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad)
* [如何实现确定 Windows 10 设备 (Azure AD 加入或混合 Azure AD 联接的) 的域加入类型吗？](#how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device)
* [应修补的 Dc 数量建议是什么？](#whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched)
* [能否在仅本地设备上部署 FIDO2 凭据提供程序？](#can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device)
* [FIDO2 安全密钥登录不适用于我的域管理员或其他高特权帐户。为什么?](#fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why)

### <a name="does-this-feature-work-if-theres-no-internet-connectivity"></a>如果没有 internet 连接，此功能是否起作用？

Internet 连接是启用此功能的先决条件。 用户首次使用 FIDO2 安全密钥登录时，必须具有 internet 连接。 对于后续登录事件，缓存的登录应正常工作，并让用户在没有 internet 连接的情况下进行身份验证。

为实现一致的体验，请确保设备能够访问 internet，并且可以看到 Dc 的线路。

### <a name="what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad"></a>需要打开哪些特定的终结点才能 Azure AD？

注册和身份验证需要以下终结点：

* **。 microsoftonline.com*
* **。 microsoftonline-p.com*
* **。 msauth.net*
* **。 msauthimages.net*
* **。 msecnd.net*
* **。 msftauth.net*
* **。 msftauthimages.net*
* **。 phonefactor.net*
* *enterpriseregistration.windows.net*
* *management.azure.com*
* *policykeyservice.dc.ad.msft.net*
* *secure.aadcdn.microsoftonline-p.com*

有关使用 Microsoft online 产品所需的终结点的完整列表，请参阅 [Office 365 url 和 IP 地址范围](/microsoft-365/enterprise/urls-and-ip-address-ranges)。

### <a name="how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device"></a>如何实现确定 Windows 10 设备 (Azure AD 加入或混合 Azure AD 联接的) 的域加入类型吗？

若要检查 Windows 10 客户端设备是否具有正确的域加入类型，请使用以下命令：

```console
Dsregcmd/status
```

以下示例输出显示设备已 Azure AD 联接，因为 *AzureADJoined* 设置为 *"是"*：

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: NO
```

以下示例输出显示设备是混合 Azure AD 联接，因为 *DomainedJoined* 也设置为 *"是"*。 还显示 *DomainName* ：

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: YES
DomainName: CONTOSO
```

在 Windows Server 2016 或2019域控制器上，检查是否应用了以下修补程序。 如果需要，请运行 Windows 更新安装它们：

* Windows Server 2016- [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019- [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

在客户端设备上运行以下命令，验证与安装了修补程序的适当域控制器的连接：

```console
nltest /dsgetdc:<domain> /keylist /kdc
```

### <a name="whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched"></a>应修补的 Dc 数量建议是什么？

建议使用修补程序修补大部分 Windows Server 2016 或2019域控制器，以确保它们可以处理组织的身份验证请求负载。

在 Windows Server 2016 或2019域控制器上，检查是否应用了以下修补程序。 如果需要，请运行 Windows 更新安装它们：

* Windows Server 2016- [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019- [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

### <a name="can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device"></a>能否在仅本地设备上部署 FIDO2 凭据提供程序？

不能，仅限本地设备不支持此功能。 FIDO2 凭据提供程序将不会显示。

### <a name="fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why"></a>FIDO2 安全密钥登录不适用于我的域管理员或其他高特权帐户。 为什么？

默认安全策略不授予 Azure AD 的权限对本地资源的高权限帐户进行签名。

若要取消阻止帐户，请使用 **Active Directory 用户和计算机** 来修改 Azure AD Kerberos 计算机对象的 *msds-neverrevealgroup* 属性， * (CN = AzureADKerberos，OU = 域控制器， \<domain-DN>) *。

## <a name="under-the-hood"></a>揭秘

* [Azure AD Kerberos 如何链接到我的本地 Active Directory 域服务环境？](#how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment)
* [在哪里可以查看在 AD 中创建并在 Azure AD 中发布的 Kerberos 服务器对象？](#where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad)
* [为什么我们没有将公钥注册到本地 AD DS 因此不会依赖于 internet？](#why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet)
* [密钥在 Kerberos 服务器对象上的旋转方式是什么？](#how-are-the-keys-rotated-on-the-kerberos-server-object)
* [为什么需要 Azure AD Connect？它是否会将任何信息从 Azure AD 写回 AD DS？](#why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad)
* [请求 PRT + 部分 TGT 时，HTTP 请求/响应会出现什么情况？](#what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt)

### <a name="how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment"></a>Azure AD Kerberos 如何链接到我的本地 Active Directory 域服务环境？

有两个部分-本地 AD DS 环境和 Azure AD 租户。

**Active Directory 域服务 (AD DS) **

Azure AD Kerberos 服务器在本地 AD DS 环境中表示为域控制器 (DC) 对象。 此 DC 对象由多个对象组成：

* *CN = AzureADKerberos，OU = 域控制器，\<domain-DN>*
    
    一种表示只读域控制器 (RODC) 在 AD DS 中的 *计算机* 对象。 没有与此对象相关联的计算机。 相反，它是 DC 的逻辑表示形式。

* *CN = krbtgt_AzureAD，CN = Users，\<domain-DN>*

    一个 *用户* 对象，该对象表示 (TGT) 加密密钥的 RODC Kerberos 票证授予票证。

* *CN = 900274c4-b7d2-43c8-90ee-00a9f650e335，CN = AzureAD，CN = System，\<domain-DN>*

    一个 *ServiceConnectionPoint* 对象，用于存储有关 Azure AD Kerberos 服务器对象的元数据。 管理工具使用此对象来标识和查找 Azure AD Kerberos 服务器对象。

**Azure Active Directory**

Azure AD Kerberos 服务器在 Azure AD 中表示为 *KerberosDomain* 对象。 每个本地 AD DS 环境都以 Azure AD 租户中的单个 *KerberosDomain* 对象的形式表示。

例如，你可能有一个包含两个域（例如 *contoso.com* 和 *fabrikam.com*）的 AD DS 林。 如果允许 Azure AD 为整个林颁发 Kerberos 票证授予票证 (Tgt) ，则 Azure AD 中有两个 *KerberosDomain* 对象用于 *contoso.com* ，一个用于 *fabrikam.com*。

如果有多个 AD DS 的林，则每个林中的每个域都有一个 *KerberosDomain* 对象。

### <a name="where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad"></a>在哪里可以查看在 AD DS 中创建并在 Azure AD 中发布的 Kerberos 服务器对象？

若要查看所有对象，请使用 Azure AD Connect 的最新版本随附的 Azure AD Kerberos 服务器 PowerShell cmdlet。

有关详细信息，包括如何查看对象的说明，请参阅 [创建 Kerberos 服务器对象](howto-authentication-passwordless-security-key-on-premises.md#create-kerberos-server-object)。

### <a name="why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet"></a>为什么我们没有将公钥注册到本地 AD DS 因此不会依赖于 internet？

我们收到了有关 Windows Hello 企业版的复杂性的反馈，因此需要简化部署模型，而无需使用证书和 PKI (FIDO2 不会) 使用证书。

### <a name="how-are-the-keys-rotated-on-the-kerberos-server-object"></a>密钥在 Kerberos 服务器对象上的旋转方式是什么？

与任何其他 DC 一样，Azure AD Kerberos 服务器加密 *krbtgt* 密钥应定期旋转。 建议遵循与用来旋转所有其他 AD DS *krbtgt* 密钥相同的计划。

> [!NOTE]
> 尽管有其他工具可用于轮换 *krbtgt* 密钥，但你必须 [使用 PowerShell cmdlet 来旋转 Azure AD Kerberos 服务器的 *krbtgt* 密钥](howto-authentication-passwordless-security-key-on-premises.md#rotating-the-azure-ad-kerberos-server-key) 。 此方法可确保在本地 AD DS 环境和 Azure AD 中更新密钥。

### <a name="why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad"></a>为什么需要 Azure AD Connect？ 它是否会将任何信息从 Azure AD 写回 AD DS？

Azure AD Connect 不会将信息从 Azure AD 写回 AD DS。 实用工具包含用于在 AD DS 中创建 Kerberos 服务器对象的 PowerShell 模块，并将其发布到 Azure AD 中。

### <a name="what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt"></a>请求 PRT + 部分 TGT 时，HTTP 请求/响应会出现什么情况？

HTTP 请求是标准的主刷新令牌 (PRT) 请求。 此 PRT 请求包括一个声明，指出需要 (TGT) 的 Kerberos 票证授予票证。

| 声明 | 值 | 说明                             |
|-------|-------|-----------------------------------------|
| tgt   | 是  | 声明指示客户端需要 TGT。 |

Azure AD 将加密的客户端密钥和消息缓冲区合并为 PRT 响应作为附加属性。 负载使用 Azure AD 设备会话密钥进行加密。

| 字段              | 类型   | 说明  |
|--------------------|--------|--------------|
| tgt_client_key     | string | Base64 编码的客户端密钥 (密钥) 。 此密钥是用于保护 TGT 的客户端机密。 在此无密码方案中，客户端密码由服务器作为每个 TGT 请求的一部分生成，然后在响应中返回给客户端。 |
| tgt_key_type       | int    | 本地 AD DS 密钥类型，用于客户端密钥和 KERB_MESSAGE_BUFFER 中包括的 Kerberos 会话密钥。 |
| tgt_message_buffer | string | Base64 编码 KERB_MESSAGE_BUFFER。 |

## <a name="next-steps"></a>后续步骤

若要开始使用 FIDO2 安全密钥以及对本地资源的混合访问，请参阅以下文章：

* [无密码 FIDO2 安全密钥](howto-authentication-passwordless-security-key.md)
* [无密码 Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [无密码本地](howto-authentication-passwordless-security-key-on-premises.md)
