---
title: 使用 dsregcmd.exe 命令对设备进行故障排除-Azure Active Directory
description: 使用 dsregcmd.exe 的输出来了解中设备的状态 Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4aa8f9a7c6807a2f9505559ea13fb0b4f410346d
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68987172"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>使用 dsregcmd.exe 命令对设备进行故障排除

必须以域用户帐户身份运行 dsregcmd.exe/status 实用程序。

## <a name="device-state"></a>设备状态

此部分列出了设备联接状态参数。 下表列出了设备处于不同联接状态的条件。

| AzureAdJoined | EnterpriseJoined | DomainJoined | 设备状态 |
| ---   | ---   | ---   | ---   |
| 是 | 否 | 否 | 已联接 Azure AD |
| 否 | 否 | 是 | 已加入域 |
| 是 | 否 | 是 | 已加入混合广告 |
| 否 | 是 | 是 | 已联接本地 DRS |

> [!NOTE]
> Workplace Join (Azure AD 注册) 状态显示在 "用户状态" 部分中

- **AzureAdJoined:** -如果设备已加入到 Azure AD, 则设置为 "是"。 否则为 "NO"。
- **EnterpriseJoined:** -如果设备已加入本地 DRS, 则设置为 "是"。 设备不能同时为 EnterpriseJoined 和 AzureAdJoined。
- **DomainJoined:** -如果设备已加入域 (AD), 则设置为 "是"。
- **DomainName:** -如果设备已加入域, 则设置为域的名称。

### <a name="sample-device-state-output"></a>设备状态输出示例

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+
             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : HYBRIDADFS
+----------------------------------------------------------------------+
```

## <a name="device-details"></a>设备详细信息

仅当设备 Azure AD 联接或混合 Azure AD (未注册 Azure AD) 时显示。 本部分列出了存储在云中的设备标识详细信息。

- **DeviceId:** -Azure AD 租户中的设备的唯一 ID
- **指纹:** -设备证书的指纹 
- **DeviceCertificateValidity:** -设备证书的有效性
- **KeyContainerId:** -与设备证书关联的设备私钥的 ContainerId
- **KeyProvider:** -KeyProvider (硬件/软件) 用于存储设备私钥。
- **TpmProtected:** -如果设备私钥存储在硬件 TPM 中, 则为 "是"。

### <a name="sample-device-details-output"></a>示例设备详细信息输出

```
+----------------------------------------------------------------------+
| Device Details                                                       |
+----------------------------------------------------------------------+

                  DeviceId : e92325d0-xxxx-xxxx-xxxx-94ae875dxxxx
                Thumbprint : D293213EF327483560EED8410CAE36BB67208179
 DeviceCertificateValidity : [ 2019-01-11 21:02:50.000 UTC -- 2029-01-11 21:32:50.000 UTC ]
            KeyContainerId : 13e68a58-xxxx-xxxx-xxxx-a20a2411xxxx
               KeyProvider : Microsoft Software Key Storage Provider
              TpmProtected : NO
+----------------------------------------------------------------------+
```

## <a name="tenant-details"></a>租户详细信息

仅当设备 Azure AD 联接或混合 Azure AD (未注册 Azure AD) 时显示。 此部分列出了将设备加入到 Azure AD 时的常见租户详细信息。

> [!NOTE]
> 即使您看到 MDM Url, 这并不意味着设备由 MDM 管理。 如果租户具有用于自动注册的 MDM 配置 (即使设备本身不受管理), 则会显示此信息。 

### <a name="sample-tenant-details-output"></a>示例租户详细信息输出

```
+----------------------------------------------------------------------+
| Tenant Details                                                       |
+----------------------------------------------------------------------+

                TenantName : HybridADFS
                  TenantId : 96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
                       Idp : login.windows.net
               AuthCodeUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/authorize
            AccessTokenUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token
                    MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
                 MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
          MdmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance
               SettingsUrl : eyJVxxxxIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyxxxx==
            JoinSrvVersion : 1.0
                JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/
                 JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net
             KeySrvVersion : 1.0
                 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/
                  KeySrvId : urn:ms-drs:enterpriseregistration.windows.net
        WebAuthNSrvVersion : 1.0
            WebAuthNSrvUrl : https://enterpriseregistration.windows.net/webauthn/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
             WebAuthNSrvId : urn:ms-drs:enterpriseregistration.windows.net
    DeviceManagementSrvVer : 1.0
    DeviceManagementSrvUrl : https://enterpriseregistration.windows.net/manage/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
     DeviceManagementSrvId : urn:ms-drs:enterpriseregistration.windows.net
+----------------------------------------------------------------------+
```

## <a name="user-state"></a>用户状态

此部分列出了当前登录到设备的用户的各种属性的状态。

> [!NOTE]
> 命令必须在用户上下文中运行, 才能检索有效状态。

- **NgcSet:** -如果为当前已登录用户设置 Windows Hello 密钥, 则设置为 "是"。
- **NgcKeyId:** -Windows Hello 密钥的 ID (如果为当前已登录用户设置了一个密钥)。
- **CanReset:** -表示用户是否可以重置 Windows Hello 密钥。 
- **可能的值:** -DestructiveOnly、NonDestructiveOnly、DestructiveAndNonDestructive 或 Unknown (如果错误)。 
- **WorkplaceJoined:** -如果已将 Azure AD 注册帐户添加到当前已上下文中的设备, 则设置为 "是"。
- **WamDefaultSet:** -如果为已登录用户创建 WAM 默认 WebAccount, 则设置为 "是"。 如果在管理上下文中运行 dsreg/status, 则此字段可能显示错误。 
- **WamDefaultAuthority:** -设置为 "组织" Azure AD。
- **WamDefaultId:** -Always "https://login.microsoft.com" Azure AD。
- **末尾为:** -默认 WAM WEBACCOUNT 的 WAM 提供程序的 (Azure AD/MICROSOFT 帐户) GUID。 

### <a name="sample-user-state-output"></a>用户状态输出示例

```
+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

                    NgcSet : YES
                  NgcKeyId : {FA0DB076-A5D7-4844-82D8-50A2FB42EC7B}
                  CanReset : DestructiveAndNonDestructive
           WorkplaceJoined : NO
             WamDefaultSet : YES
       WamDefaultAuthority : organizations
              WamDefaultId : https://login.microsoft.com
            WamDefaultGUID : { B16898C6-A148-4967-9171-64D755DA8520 } (AzureAd)

+----------------------------------------------------------------------+
```

## <a name="sso-state"></a>SSO 状态

对于 Azure AD 注册的设备, 可忽略此部分。

> [!NOTE]
> 此命令必须在用户上下文中运行, 以检索该用户的有效状态。

- **AzureAdPrt:** -如果登录用户的设备上存在 PRT, 则设置为 "是"。
- **AzureAdPrtUpdateTime:** -设置为上次更新 PRT 时的 UTC 时间。
- **AzureAdPrtExpiryTime:** -设置为在未续订时 PRT 将过期的时间 (UTC)。
- **AzureAdPrtAuthority:** -Azure AD 颁发机构 URL
- **EnterprisePrt:** -如果设备已从本地 ADFS PRT, 则设置为 "是"。 对于已加入混合 Azure AD 设备, 设备可能同时具有 Azure AD 和本地 AD 中的 PRT。 本地加入的设备将只有企业 PRT。
- **EnterprisePrtUpdateTime:** -设置为上次更新 Enterprise PRT 时的 UTC 时间。
- **EnterprisePrtExpiryTime:** -设置为在未续订时 PRT 将过期的时间 (UTC)。
- **EnterprisePrtAuthority:** -ADFS 证书颁发机构 URL

### <a name="sample-sso-state-output"></a>示例 SSO 状态输出

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : YES
      AzureAdPrtUpdateTime : 2019-01-24 19:15:26.000 UTC
      AzureAdPrtExpiryTime : 2019-02-07 19:15:26.000 UTC
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2019-01-24 19:15:33.000 UTC
   EnterprisePrtExpiryTime : 2019-02-07 19:15:33.000 UTC
    EnterprisePrtAuthority : https://fs.hybridadfs.nttest.microsoft.com:443/adfs

+----------------------------------------------------------------------+
```

## <a name="diagnostic-data"></a>诊断数据

### <a name="pre-join-diagnostics"></a>预先联接诊断

仅当设备已加入域并且无法 Azure AD 联接混合时, 才显示此部分。

本部分将执行各种测试来帮助诊断联接失败。 本节还包括上一个 (？) 的详细信息。 此信息包括错误阶段、错误代码、服务器请求 ID、服务器响应 http 状态、服务器响应错误消息。

- **用户上下文:** -运行诊断的上下文。 可能的值:系统, 未提升的用户, 提升的用户。 

   > [!NOTE]
   > 由于实际联接是在系统上下文中执行的, 因此在系统上下文中运行诊断最接近实际联接方案。 若要在系统上下文中运行诊断, 必须在提升的命令提示符下运行 dsregcmd.exe/status 命令。

- **客户端时间:** -UTC 格式的系统时间。
- **AD 连接测试:** -test 执行到域控制器的连接测试。 此测试中的错误可能导致预检查阶段出现联接错误。
- **AD 配置测试:** -测试读取并验证是否在本地 AD 林中正确配置了 SCP 对象。 此测试中的错误可能导致发现阶段出现联接错误, 错误代码为0x801c001d。
- **DRS 发现测试:** -Test 从发现元数据终结点获取 DRS 终结点并执行用户领域请求。 此测试中的错误可能导致发现阶段出现联接错误。
- **Drs 连接测试:** -测试对 DRS 终结点执行基本连接测试。
- **令牌获取测试:** 如果用户租户是联合的, 则测试尝试获取 Azure AD 身份验证令牌。 此测试中的错误可能导致在身份验证阶段出现联接错误。 如果身份验证失败, 则将尝试将同步联接作为回退, 除非使用注册表项显式禁用回退。
- **回退到同步连接:** -如果注册表项不存在, 则设置为 "已启用", 以防止回退与身份验证失败的联接。 此选项在 Windows 10 1803 和更高版本中可用。
- **上一次注册:** -上次进行联接尝试的时间。 仅记录失败的联接尝试。
- **错误阶段:** -已中止的联接的阶段。 可能的值包括预检查、发现、身份验证和联接。
- **客户端错误:** -返回的客户端错误代码 (HRESULT)。
- **服务器 ErrorCode:** -服务器错误代码 (如果向服务器发送了请求, 并且服务器响应了错误代码)。 
- **服务器消息:** -与错误代码一起返回的服务器消息。
- **Https 状态:** -服务器返回的 Http 状态。
- **请求 ID:** -发送到服务器的客户端 requestId。 与服务器端日志关联很有用。

### <a name="sample-pre-join-diagnostics-output"></a>示例预联接诊断输出

下面的示例演示发现错误, 诊断测试失败。

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:25:31.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x801c000c]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:23:30.000 UTC
               Error Phase : discover
          Client ErrorCode : 0x801c0021

+----------------------------------------------------------------------+
```

下面的示例演示了如何通过了诊断测试, 但注册尝试失败, 并出现目录错误, 要求同步联接。 Azure AD Connect 同步作业完成后, 设备将能够加入。

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:16:50.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : PASS
     DRS Connectivity Test : PASS
    Token acquisition Test : PASS
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e

+----------------------------------------------------------------------+
```

### <a name="post-join-diagnostics"></a>联接后诊断

此部分显示在加入云的设备上执行的检查的输出。

- **AadRecoveryEnabled:** -如果是 "YES", 则表示存储在设备中的密钥不可用, 并且设备已标记为要恢复。 下一个登录将触发恢复流, 并重新注册设备。
- **KeySignTest:** -如果 "通过" 设备密钥的运行状况良好。 如果 KeySignTest 失败, 设备通常会标记为要恢复。 下一个登录将触发恢复流, 并重新注册设备。 对于混合 Azure AD 联接的设备, 恢复是静默的。 Azure AD 加入或 Azure AD 注册后, 设备将提示用户进行身份验证, 以便在必要时恢复并重新注册设备。 **KeySignTest 需要提升的权限。**

#### <a name="sample-post-join-diagnostics-output"></a>示例联接后诊断输出

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisite-check"></a>NGC 先决条件检查

本部分将执行系统必备组件检查以预配 NGC 密钥。 

> [!NOTE]
> 如果用户已成功配置了 NGC 凭据, 则可能不会在 dsregcmd.exe/status 中看到 NGC 必备组件检查详细信息。

### <a name="sample-ngc-prerequisite-check-output"></a>示例 NGC 先决条件检查输出

```
+----------------------------------------------------------------------+
| Ngc Prerequisite Check                                               |
+----------------------------------------------------------------------+

            IsDeviceJoined : YES
             IsUserAzureAD : YES
             PolicyEnabled : YES
          PostLogonEnabled : YES
            DeviceEligible : YES
        SessionIsNotRemote : YES
            CertEnrollment : enrollment authority
          AdfsRefreshToken : YES
             AdfsRaIsReady : YES
    LogonCertTemplateReady : YES ( StateReady )
              PreReqResult : WillProvision
+----------------------------------------------------------------------+
```

## <a name="next-steps"></a>后续步骤

如有问题，请参阅[设备管理常见问题解答](faq.md)
