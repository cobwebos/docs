---
title: 排查已加入混合 Azure Active Directory 的 Windows 10 和 Windows Server 2016 设备问题 | Microsoft Docs
description: 排查已加入混合 Azure Active Directory 的 Windows 10 和 Windows Server 2016 设备问题。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 584a36e766e9e054af2ad1ce4f2105108fe80525
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39415660"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-windows-10-and-windows-server-2016-devices"></a>排查已加入混合 Azure Active Directory 的 Windows 10 和 Windows Server 2016 设备问题 

本文适用于以下客户端：

-   Windows 10
-   Windows Server 2016

对于其他 Windows 客户端，请参阅[排查已加入混合 Azure Active Directory 的下层设备问题](troubleshoot-hybrid-join-windows-legacy.md)。

本文假设你已[配置已加入混合 Azure Active Directory 的设备](hybrid-azuread-join-plan.md)，以支持以下方案：

- 基于设备的条件访问

- [企业设置漫游](../active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello for Business](../active-directory-azureadjoin-passport-deployment.md)


本文档提供有关如何解决潜在问题的故障排除指导。 


对于 Windows 10 和 Windows Server 2016，混合 Azure Active Directory 加入功能支持 Windows 10 November 2015 Update 和更高版本。 我们建议使用周年更新版。

## <a name="step-1-retrieve-the-join-status"></a>步骤 1：检索加入状态 

**检索加入状态：**

1. 以管理员身份打开命令提示符

2. 键入 **dsregcmd /status**



    +----------------------------------------------------------------------+
    | 设备状态                                                         |  +----------------------------------------------------------------------+
    
        AzureAdJoined: YES
     EnterpriseJoined: NO DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7 Thumbprint: B753A6679CE720451921302CA873794D94C6204A KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9 KeyProvider: Microsoft Platform Crypto Provider TpmProtected: YES KeySignTest: : MUST Run elevated to test.
                  Idp: login.windows.net TenantId: 72b988bf-86f1-41af-91ab-2d7cd011db47 TenantName: Contoso AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ== JoinSrvVersion: 1.0 JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/ JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net KeySrvVersion: 1.0 KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/ KeySrvId: urn:ms-drs:enterpriseregistration.windows.net DomainJoined: YES DomainName: CONTOSO
    
    +----------------------------------------------------------------------+
    | 用户状态                                                           |  +----------------------------------------------------------------------+
    
                 NgcSet: YES
               NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
        WorkplaceJoined: NO
          WamDefaultSet: YES
    WamDefaultAuthority: organizations         WamDefaultId: https://login.microsoft.com       WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)           AzureAdPrt: YES



## <a name="step-2-evaluate-the-join-status"></a>步骤 2：评估加入状态 

检查以下字段，确保它们包含预期值：

### <a name="azureadjoined--yes"></a>AzureAdJoined : YES  

此字段指示设备是否已加入 Azure AD。 如果值为 **NO**，则表示加入到 Azure AD 的过程尚未完成。 

**可能的原因：**

- 要加入的计算机的身份验证失败。

- 计算机无法发现组织中的某个 HTTP 代理

- 计算机无法访问 Azure AD 来完成身份验证，或者无法访问 Azure DRS 来完成注册

- 计算机可能不在组织的内部网络上，或者不在本地 AD 域控制器可直接访问的 VPN 上。

- 如果计算机上装有 TPM，该 TPM 可能处于错误状态。

- 可能存在文档中前面所述的，需要再次检查的服务配置不当问题。 常见示例包括：

    - 联合服务器中未启用 WS-Trust 终结点

    - 联合服务器不允许使用 Windows 集成身份验证通过网络中的计算机执行入站身份验证。

    - 在计算机所属的 AD 林中的 Azure AD 内，不存在指向已验证域名的“服务连接点”对象

---

### <a name="domainjoined--yes"></a>DomainJoined : YES  

此字段指示设备是否已加入本地 Active Directory。 如果值为 **NO**，则设备无法执行混合 Azure AD 加入。  

---

### <a name="workplacejoined--no"></a>WorkplaceJoined : NO  

此字段指示设备是否以个人设备的形式注册到 Azure AD（标记为“已加入工作区”）。 对于已加入域，同时已加入混合 Azure AD 的计算机，此值应为 **NO**。 如果值为 **YES**，则表示在完成混合 Azure AD 加入之前已添加工作或学校帐户。 在这种情况下，如果使用 Windows 10 周年更新版 (1607)，则会忽略该帐户。

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet : YES，AzureADPrt : YES
  
这些字段指示用户在登录设备时是否已成功通过 Azure AD 的身份验证。 如果值为 **NO**，原因可能是：

- 与设备关联的 TPM 在注册时包含错误的存储密钥 (STK)（以提升的权限运行时检查 KeySignTest）。

- 备用登录 ID

- 找不到 HTTP 代理

## <a name="next-steps"></a>后续步骤

如有问题，请参阅[设备管理常见问题解答](faq.md) 