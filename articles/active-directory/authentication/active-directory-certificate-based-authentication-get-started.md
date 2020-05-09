---
title: 基于证书的身份验证 - Azure Active Directory
description: 了解如何在环境中配置基于证书的身份验证
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 9c3ea7596e589431412489bea4ac9a23fa604540
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82610643"
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Azure Active Directory 中基于证书的身份验证入门

如果使用基于证书的身份验证，在 Windows、Android 或 iOS 设备上将 Exchange Online 帐户连接到以下对象时，由 Azure Active Directory 使用客户端证书对你进行身份验证：

- Microsoft 移动应用程序，例如 Microsoft Outlook 和 Microsoft Word
- Exchange ActiveSync (EAS) 客户端

如果配置了此功能，就无需在移动设备上的某些邮件和 Microsoft Office 应用程序中输入用户名和密码组合。

本主题：

- 提供为 Office 365 企业版、商业版和教育版以及美国政府计划中租户的用户配置并使用基于证书的身份验证的步骤。 可在 Office 365 China（Office 365 中国版）、Office 365 US Government Defense（Office 365 美国政府国防版）和 Office 365 US Government Federal（Office 365 美国政府联邦版）计划中使用此功能。
- 假设已配置[公钥基础结构 (PKI)](https://go.microsoft.com/fwlink/?linkid=841737) 和 [AD FS](../hybrid/how-to-connect-fed-whatis.md)。

## <a name="requirements"></a>要求

若要配置基于证书的身份验证，以下语句必须为真：

- 仅浏览器应用程序的联合环境、使用新式身份验证 (ADAL) 的本机客户端或 MSAL 库支持基于证书的身份验证 (CBA)。 用于 Exchange Online (EXO) 的 Exchange Active Sync (EAS) 除外，它可用于联合帐户和托管帐户。
- 必须在 Azure Active Directory 中配置根证书颁发机构和任何中间证书颁发机构。
- 每个证书颁发机构必须有一个可通过面向 Internet 的 URL 引用的证书吊销列表 (CRL)。
- 必须已在 Azure Active Directory 中至少配置一个证书颁发机构。 可以在 [配置证书颁发机构](#step-2-configure-the-certificate-authorities) 部分查找相关步骤。
- 对于 Exchange ActiveSync 客户端，客户端证书的“使用者可选名称”字段的主体名称或 RFC822 名称值必须为 Exchange Online 中用户的可路由电子邮件地址。 Azure Active Directory 会将 RFC822 值映射到目录中的“代理地址”属性。
- 客户端设备必须能够访问至少一个颁发客户端证书的证书颁发机构。
- 必须已向客户端颁发用于客户端身份验证的客户端证书。

>[!IMPORTANT]
>Azure Active Directory 成功下载和缓存的 CRL 的最大大小为 20MB，下载 CRL 所需的时间不得超过 10 秒。  如果 Azure Active Directory 无法下载 CRL，则使用相应 CA 颁发的证书进行的基于证书的身份验证将失败。 确保 CRL 文件符合大小限制的最佳做法是将证书生存期保持在合理的限制内，并清理过期的证书。

## <a name="step-1-select-your-device-platform"></a>步骤 1：选择设备平台

第一步，用户需针对所关注的设备平台查看以下内容：

- Office 移动应用程序支持
- 特定的实现要求

存在以下设备平台的相关信息：

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)

## <a name="step-2-configure-the-certificate-authorities"></a>步骤 2：配置证书颁发机构

若要在 Azure Active Directory 中配置证书颁发机构，请为每个证书颁发机构上传以下内容：

* 证书的公共部分，格式为 *.cer*
* 证书吊销列表 (CRL) 所在的面向 Internet 的 URL

证书颁发机构的架构如下所示：

    class TrustedCAsForPasswordlessAuth
    {
       CertificateAuthorityInformation[] certificateAuthorities;
    }

    class CertificateAuthorityInformation

    {
        CertAuthorityType authorityType;
        X509Certificate trustedCertificate;
        string crlDistributionPoint;
        string deltaCrlDistributionPoint;
        string trustedIssuer;
        string trustedIssuerSKI;
    }

    enum CertAuthorityType
    {
        RootAuthority = 0,
        IntermediateAuthority = 1
    }

对于此配置，可以使用 [Azure Active Directory PowerShell 版本 2](/powershell/azure/install-adv2?view=azureadps-2.0)：

1. 使用管理员特权启动 Windows PowerShell。
2. 安装 Azure AD 模块 [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) 或更高版本。

        Install-Module -Name AzureAD –RequiredVersion 2.0.0.33

作为第一个配置步骤，需建立与租户的连接。 与租户建立连接后，即可查看、添加、删除和修改目录中定义的受信任的证书颁发机构。

### <a name="connect"></a>连接

若要建立与租户的连接，请使用 [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0) cmdlet：

    Connect-AzureAD

### <a name="retrieve"></a>检索

若要检索目录中定义的受信任的证书颁发机构，请使用 [Get-AzureADTrustedCertificateAuthority](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0) cmdlet。

    Get-AzureADTrustedCertificateAuthority

### <a name="add"></a>添加

若要创建受信任的证书颁发机构，请使用 [New-AzureADTrustedCertificateAuthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority?view=azureadps-2.0) cmdlet，并将 **crlDistributionPoint** 属性设为正确的值：

    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint="<CRL Distribution URL>"
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca

### <a name="remove"></a>删除

若要删除受信任的证书颁发机构，请使用 [Remove-AzureADTrustedCertificateAuthority](/powershell/module/azuread/remove-azureadtrustedcertificateauthority?view=azureadps-2.0) cmdlet：

    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]

### <a name="modify"></a>修改

若要修改受信任的证书颁发机构，请使用 [Set-AzureADTrustedCertificateAuthority](/powershell/module/azuread/set-azureadtrustedcertificateauthority?view=azureadps-2.0) cmdlet：

    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]

## <a name="step-3-configure-revocation"></a>步骤 3：配置吊销

若要吊销客户端证书，Azure Active Directory 会从作为证书颁发机构信息的一部分上传的 URL 中提取证书吊销列表 (CRL)，并将其缓存。 CRL 中的上次发布时间戳（“生效日期”属性）用于确保 CRL 仍然有效。  将定期引用 CRL，以撤销对该列表中证书的访问权限。

如果需要更即时的吊销（例如，如果用户丢失了设备），可以使用户的授权令牌失效。 若要使授权令牌失效，请使用 Windows PowerShell 为此特定用户设置 **StsRefreshTokenValidFrom** 字段。 必须为要撤销其访问权限的每个用户更新 **StsRefreshTokenValidFrom** 字段。

若要确保撤销仍然有效，必须将 CRL 的**生效日期**设置为晚于 **StsRefreshTokenValidFrom** 所设置的值，并确保相关的证书在 CRL 中。

以下步骤概述了通过设置 **StsRefreshTokenValidFrom** 字段更新授权令牌并使其失效的过程。

**若要配置吊销，请执行以下操作：**

1. 使用管理员凭据连接到 MSOL 服务：

        $msolcred = get-credential
        connect-msolservice -credential $msolcred

2. 检索用户的当前 StsRefreshTokensValidFrom 值：

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom

3. 将用户的新 StsRefreshTokensValidFrom 值配置为等于当前时间戳：

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")

所设日期必须属于将来。 如果日期不属于将来，则不会设置 **StsRefreshTokensValidFrom** 属性。 如果日期属于将来，则将 **StsRefreshTokensValidFrom** 设置为当前时间（而不是由 Set-MsolUser 命令指示的日期）。

## <a name="step-4-test-your-configuration"></a>步骤 4：测试配置

### <a name="testing-your-certificate"></a>测试证书

作为第一个配置测试，应尝试使用**设备上的浏览器**登录 [Outlook Web Access](https://outlook.office365.com) 或 [SharePoint Online](https://microsoft.sharepoint.com)。

如果登录成功，则可确定：

- 已为测试设备预配用户证书
- 已正确配置 AD FS

### <a name="testing-office-mobile-applications"></a>测试 Office 移动应用程序

**若要在 Office 移动应用程序上测试基于证书的身份验证，请执行以下操作：**

1. 在测试设备上，安装 Office 移动应用程序（例如 OneDrive）。
3. 启动应用程序。
4. 输入用户名，并选择要使用的用户证书。

应可以成功登录。

### <a name="testing-exchange-activesync-client-applications"></a>测试 Exchange ActiveSync 客户端应用程序

若要通过基于证书的身份验证访问 Exchange ActiveSync (EAS)，必须为应用程序提供包含客户端证书的 EAS 配置文件。

EAS 配置文件必须包含以下信息：

- 用于身份验证的用户证书

- EAS 终结点（例如 outlook.office365.com）

若要配置 EAS 配置文件并将其放置在设备上，可以使用移动设备管理 (MDM)，例如 Intune，也可以手动将 EAS 配置文件中的证书放置在设备上。

### <a name="testing-eas-client-applications-on-android"></a>在 Android 上测试 EAS 客户端应用程序

**若要测试证书身份验证，请执行以下操作：**

1. 在应用程序中配置满足上一部分中要求的 EAS 配置文件。
2. 打开应用程序，验证邮件是否正在同步。

## <a name="next-steps"></a>后续步骤

[有关 Android 设备上基于证书的身份验证的其他信息。](active-directory-certificate-based-authentication-android.md)

[有关 iOS 设备上基于证书的身份验证的其他信息。](active-directory-certificate-based-authentication-ios.md)
