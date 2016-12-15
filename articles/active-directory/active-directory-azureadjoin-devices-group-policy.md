---
title: "体验 Windows 10 时将已加入域的设备连接到 Azure AD | Microsoft 文档"
description: "介绍了管理员可以如何配置支持设备域加入到企业网络的组策略。"
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
tags: azure-classic-portal
ms.assetid: 2ff29f3e-5325-4f43-9baa-6ae8d6bad3e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: femila
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0e211d13e41526157f6ade960b86f31dfdfd54e1


---
# <a name="connect-domain-joined-devices-to-azure-ad-for-windows-10-experiences"></a>Connect domain-joined devices to Azure AD for Windows 10 experiences（体验 Windows 10 时将已加入域的设备连接到 Azure AD）
过去 15 年及更长时间，域加入一直是组织连接设备进行工作的惯用方法。 它支持用户使用其 Windows Server Active Directory (Active Directory) 工作帐户或学校帐户登录到其设备，支持获准的 IT 部门全权管理这些设备。 组织通常依靠映像方法将设备分配给用户，通常使用 System Center Configuration Manager (SCCM) 或组策略管理这些设备。

将设备连接到 Azure Active Directory (Azure AD) 后，Windows 10 中的域加入功能可提供以下好处：

* 从任意位置单一登录 (SSO) 到 Azure AD 资源
* 使用工作帐户或学校帐户（不需要 Microsoft 帐户）访问企业版 Windows 应用商店
* 使用工作帐户或学校帐户（不需要 Microsoft 帐户）在设备间符合企业规范地漫游用户设置
* 工作帐户或学校帐户与 Microsoft Passport 和 Windows Hello 结合使用，实现强身份验证和方便登录
* 可以将访问只限于符合组织的设备组策略设置的设备

## <a name="prerequisites"></a>先决条件
域加入仍非常有用。 不过，若要获取 SSO 的 Azure AD 好处、漫游工作帐户或学校帐户的设置以及使用工作帐户或学校帐户访问 Windows 应用商店，需要满足以下条件：

* Azure AD 订阅
* 将本地目录扩展到 Azure AD 的 Azure AD Connect
* 设置为将已加入域的设备连接到 Azure AD 的策略
* 适用于设备的 Windows 10 版本（版本 10551 或更高版本）

若要启用 Microsoft Passport for Work 和 Windows Hello，还需要满足以下条件：

* 用于用户证书颁发的公钥基础结构 (PKI)。
* System Center Configuration Manager 版本 1509 技术预览版。 有关详细信息，请参阅 [Microsoft System Center Configuration Manager Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) 和 [System Center Configuration Manager 团队博客](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx)。 这要求基于 Microsoft Passport 密钥部署用户证书。

作为 PKI 部署要求的替代方法，可以执行以下操作：

* 使多个域控制器具有 Windows Server 2016 Active Directory 域服务。

若要支持条件访问，可以创建用于允许访问已加入域、但未另行部署的设备的组策略设置。 若要基于设备的合规性管理访问控制，需要满足以下条件：

* 适用于 Passport 方案的 System Center Configuration Manager 版本 1509 技术预览版

## <a name="deployment-instructions"></a>部署说明
### <a name="step-1-deploy-azure-active-directory-connect"></a>步骤 1：部署 Azure Active Directory Connect
Azure AD Connect 可支持你将本地计算机配置为云中的设备对象。 若要部署 Azure AD Connect，请参阅[将本地标识与 Azure Active Directory 集成](active-directory-aadconnect.md#install-azure-ad-connect)一文中的“安装 Azure AD Connect”。

* 如果遵循 [Azure AD Connect 的自定义安装](connect/active-directory-aadconnect-get-started-custom.md)（非快速安装），那么请按照本步骤稍后部分中的**在本地 Active Directory 中创建服务连接点**这一步操作。
* 如果在安装 Azure AD Connect 之前已具有 Azure AD 的联合配置（例如，如果之前部署了 Active Directory Federation Services (AD FS)），那么请按照本步骤稍后部分中的**配置 AD FS 声明规则**这一步操作。

#### <a name="create-a-service-connection-point-in-on-premises-active-directory"></a>在本地 Active Directory 中创建服务连接点
已加入域的设备在向 Azure 设备注册服务进行自动注册时，会使用该服务连接点来发现 Azure AD 租户信息。

在 Azure AD Connect 服务器上，运行以下 PowerShell 命令：

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;


运行 cmdlet $aadAdminCred = Get-Credential 时，对要输入的凭据的用户名使用 *user@example.com* 格式（显示 Get-Credential 弹出窗口时）。

运行 cmdlet Initialize-ADSyncDomainJoinedComputerSync ... 时，将 [*连接器帐户名*] 替换为将用作 Active Directory 连接器帐户的域帐户。

#### <a name="configure-ad-fs-claim-rules"></a>配置 AD FS 声明规则
配置 AD FS 声明规则通过以下方式支持向 Azure 设备注册服务即时注册计算机：允许计算机通过 AD FS 使用 Kerberos/NTLM 进行身份验证。 在未执行此步骤的情况下，计算机将以延迟方式访问 Azure AD（受限于 Azure AD Connect 同步时间）。

> [!NOTE]
> 如果不具有作为本地联合身份验证服务器的 AD FS，请按照你供应商的指示操作创建声明规则。
> 
> 

在 AD FS 服务器（或与 AD FS 服务器连接的会话）上，运行以下 PowerShell 命令：

      <#----------------------------------------------------------------------
     |   Modify the Azure AD Relying Party to include the claims needed
     |   for DomainJoin++. The rules include:
     |   -ObjectGuid
     |   -AccountType
     |   -ObjectSid
     +---------------------------------------------------------------------#>

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules

    $rule1 = '@RuleName = "Issue object GUID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);'

    $rule2 = '@RuleName = "Issue account type for domain joined computers"
          c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");'

    $rule3 = '@RuleName = "Pass through primary SID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(claim = c2);'

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString

> [!NOTE]
> Windows 10 计算机将通过对 AD FS 托管的 WS-Trust 活动终结点使用 Windows 集成身份验证进行身份验证。 确保已启用此终结点。 如果使用的是 Web 身份验证代理，还要确保已通过该代理发布了此终结点。 可以通过查看 adfs/services/trust/13/windowstransport 执行此操作。 在 AD FS 管理控制台的“服务” > “终结点”下，该项应显示为“已启用”。
> 
> 

### <a name="step-2-configure-automatic-device-registration-via-group-policy-in-active-directory"></a>步骤 2：通过 Active Directory 中的组策略配置自动设备注册
可以使用 Active Directory 中的组策略将已加入域的 Windows 10 设备配置为自动注册到 Azure AD。

> [!NOTE]
> 有关如何设置自动设备注册的最新说明，请参阅[如何设置自动注册已加入域的 Windows 设备到 Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md)。
> 
> 在 Windows 10 中，此组策略模板已重命名。 如果正在 Windows 10 计算机上运行组策略工具，该策略将显示为： <br>
> **将已加入域的计算机注册为设备**<br>
> 该策略位于以下位置：<br>
> ***计算机配置/策略/管理模板/Windows 组件/设备注册***
> 
> 

## <a name="additional-information"></a>其他信息
* [面向企业的 Windows 10：在工作中使用设备的方式](active-directory-azureadjoin-windows10-devices-overview.md)
* [通过 Azure Active Directory Join 将云功能扩展到 Windows 10 设备](active-directory-azureadjoin-user-upgrade.md)
* [了解 Azure AD Join 的使用方案](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connect domain-joined devices to Azure AD for Windows 10 experiences（体验 Windows 10 时将已加入域的设备连接到 Azure AD）](active-directory-azureadjoin-devices-group-policy.md)
* [设置 Azure AD Join](active-directory-azureadjoin-setup.md)




<!--HONumber=Nov16_HO3-->


