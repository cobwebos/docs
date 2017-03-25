---
title: "排查已加入 Azure AD 域的计算机的自动注册问题 - Windows 10 和 Windows Server 2016 | Microsoft 文档"
description: "排查已加入 Azure AD 域的计算机的自动注册问题 - Windows 10 和 Windows Server 2016。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 2faf328e6622b9a1e3b529d62b61061659041fbd
ms.lasthandoff: 03/10/2017


---
# <a name="troubleshooting-auto-registration-of-domain-joined-computers-to-azure-ad--windows-10-and-windows-server-2016"></a>排查已加入 Azure AD 域的计算机的自动注册问题 - Windows 10 和 Windows Server 2016

本主题是适用于以下客户端：

-    Windows 10
-    Windows Server 2016

对于其他 Windows 客户端，请参阅[排查已加入 Azure AD 域的计算机的自动注册问题 - Windows 下层客户端](active-directory-device-registration-troubleshoot-windows-legacy.md)。

本主题假设已根据[如何使用 Azure Active Directory 配置已加入 Windows 域的设备的自动注册](active-directory-device-registration-get-started.md)中所述配置了已加入域的设备的自动注册，以支持以下方案：

- [基于设备的条件访问](active-directory-conditional-access-automatic-device-registration-setup.md)

- [企业设置漫游](active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello for Business](active-directory-azureadjoin-passport-deployment.md)


本文档提供有关如何解决潜在问题的故障排除指导。 

Windows 10 2015 年 11 月更新版和更高版本支持注册。  
建议使用周年更新启用上述方案。

## <a name="step-1-retrieve-the-registration-status"></a>步骤 1：检索注册状态 

**检索注册状态：**

1. 以管理员身份打开命令提示符。

2. 键入 **dsregcmd /status**



    +----------------------------------------------------------------------+
    | 设备状态                                                         |  +----------------------------------------------------------------------+
    
        AzureAdJoined : YES
     EnterpriseJoined : NO DeviceId : 5820fbe9-60c8-43b0-bb11-44aee233e4e7 Thumbprint : B753A6679CE720451921302CA873794D94C6204A KeyContainerId : bae6a60b-1d2f-4d2a-a298-33385f6d05e9 KeyProvider : Microsoft Platform Crypto Provider TpmProtected : YES KeySignTest: : MUST Run elevated to test.
                  Idp : login.windows.net TenantId : 72b988bf-86f1-41af-91ab-2d7cd011db47 TenantName : Contoso AuthCodeUrl : https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize AccessTokenUrl : https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx dmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance SettingsUrl : eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ== JoinSrvVersion : 1.0 JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/ JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net KeySrvVersion : 1.0 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/ KeySrvId : urn:ms-drs:enterpriseregistration.windows.net DomainJoined : YES DomainName : CONTOSO
    
    +----------------------------------------------------------------------+
    | 用户状态                                                           |  +----------------------------------------------------------------------+
    
                 NgcSet : YES
               NgcKeyId : {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
        WorkplaceJoined : NO
          WamDefaultSet : YES
    WamDefaultAuthority : organizations         WamDefaultId : https://login.microsoft.com       WamDefaultGUID : {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)           AzureAdPrt : YES



## <a name="step-2-evaluate-the-registration-status"></a>步骤 2：评估注册状态 

检查以下字段，确保它们包含预期值：

### <a name="azureadjoined--yes"></a>AzureAdJoined : YES  

此字段显示是否已将设备注册到 Azure AD。 如果值显示为“NO”，则表示未完成注册。 

**可能的原因：**

- 要注册的计算机的身份验证失败。

- 计算机无法发现组织中的某个 HTTP 代理

- 计算机无法访问 Azure AD 来完成身份验证，或者无法访问 Azure DRS 来完成注册

- 计算机可能不在组织的内部网络上，或者不在本地 AD 域控制器可直接访问的 VPN 上。

- 如果计算机上装有 TPM，该 TPM 可能处于错误状态。

- 可能存在文档中前面所述的，需要再次检查的服务配置不当问题。 常见示例包括：

    - 联合服务器中未启用 WS-Trust 终结点

    - 联合服务器可能不允许使用 Windows 集成身份验证通过网络中的计算机执行入站身份验证。

    - 在计算机所属的 AD 林中的 Azure AD 内，不存在指向已验证域名的“服务连接点”对象

---

### <a name="domainjoined--yes"></a>DomainJoined : YES  

此字段显示设备是否已加入本地 Active Directory。 如果该值显示为“NO”，则设备无法自动注册到 Azure AD。 首先请检查设备在注册到 Azure AD 之前是否能够加入本地 Active Directory。 如果想要将计算机直接加入 Azure AD，请参阅“了解 Azure Active Directory Join 功能”。

---

### <a name="workplacejoined--no"></a>WorkplaceJoined : NO  

此字段显示设备是否以个人设备的形式注册到 Azure AD（标记为“已加入工作区”）。 如果此值显示为“NO”，表示加入域的计算机已注册到 Azure AD；如果显示为“YES”，表示在计算机完成注册之前已添加工作或学校帐户。 在这种情况下，如果使用 Windows 10 周年更新版（在“运行”窗口或命令提示窗口中运行 WinVer 命令时返回 1607），则会忽略该帐户。

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet : YES，AzureADPrt : YES
  
这些字段显示用户在登录设备时已成功通过 Azure AD 的身份验证。 如果显示“NO”，可能的原因包括：

- 与设备关联的 TPM 在注册时包含错误的存储密钥 (STK)（以提升的权限运行时检查 KeySignTest）。

- 备用登录 ID

- 找不到 HTTP 代理

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[自动设备注册常见问题](active-directory-device-registration-faq.md) 
