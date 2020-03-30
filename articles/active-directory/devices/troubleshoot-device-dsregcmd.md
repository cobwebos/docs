---
title: 使用 dsregcmd 命令进行故障排除 - Azure 活动目录
description: 使用 dsregcmd 的输出了解 Azure AD 中的设备状态
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cd782cdab625934fe60617142e5ac0baf756398
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128765"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>使用 dsregcmd 命令对设备进行故障排除

dsregcmd /状态实用程序必须作为域用户帐户运行。

## <a name="device-state"></a>设备状态

本节列出设备联接状态参数。 下表列出了设备处于各种联接状态的条件。

| AzureAdad | 企业加入 | 域已加入 | 设备状态 |
| ---   | ---   | ---   | ---   |
| YES | 是 | 是 | Azure AD 已加入 |
| 是 | 是 | YES | 已加入域 |
| YES | 是 | YES | 混合 AD 加入 |
| 是 | YES | YES | 本地 DRS 已加入 |

> [!NOTE]
> 工作区加入（Azure AD 已注册）状态显示在"用户状态"部分

- **AzureAdad：-** 如果设备已加入 Azure AD，则设置为"是"。 否则"否"。
- **企业加入：** - 如果设备已加入本地 DRS，则设置为"是"。 设备不能同时是企业加入和 AzureAdAd.。
- **域已加入：** - 如果设备已加入域 （AD），则设置为"是"。
- **域名：** - 如果设备已加入域，则设置为域的名称。

### <a name="sample-device-state-output"></a>示例设备状态输出

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

仅当设备加入 Azure AD 或混合 Azure AD 加入（未注册 Azure AD）时显示。 本节列出了存储在云中的设备标识详细信息。

- **设备 ID：** - Azure AD 租户中设备的唯一 ID
- **指纹：** - 设备证书的指纹 
- **设备证书有效性：** - 设备证书的有效性
- **密钥容器Id：** - 与设备证书关联的设备私钥的容器 Id
- **密钥提供程序：** - 用于存储设备私钥的密钥提供程序（硬件/软件）。
- **Tpm 保护：** - 如果设备私钥存储在硬件 TPM 中，则"是"。

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

仅当设备加入 Azure AD 或混合 Azure AD 加入（未注册 Azure AD）时显示。 本节列出设备加入 Azure AD 时的常见租户详细信息。

> [!NOTE]
> 如果此部分中的 MDM URL 为空，则表示 MDM 未配置，或者当前用户不在 MDM 注册范围内。 检查 Azure AD 中的移动设置以查看 MDM 配置。

> [!NOTE]
> 即使您看到 MDM URL，也不表示设备由 MDM 管理。 如果租户具有用于自动注册的 MDM 配置，即使设备本身未进行管理，也会显示该信息。 

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

本节列出当前登录到设备的用户的各种属性的状态。

> [!NOTE]
> 该命令必须在用户上下文中运行才能检索有效状态。

- **NgcSet：** - 如果为当前登录的用户设置了 Windows Hello 密钥，则设置为"是"。
- **NgcKeyId：** - Windows Hello 密钥的 ID（如果为当前登录用户设置了密钥）。
- **Can重置：** - 表示用户是否可以重置 Windows Hello 密钥。 
- **可能的值：** - 仅破坏性、仅破坏性、破坏性和非破坏性值，如果错误，则未知。 
- **工作区：-** 如果 Azure AD 注册帐户已添加到当前 NTUSER 上下文中的设备，则设置为"是"。
- **WamDefaultSet：** - 如果为登录用户创建了 WAM 默认 Web 帐户，则设置为"是"。 如果 dsreg /状态从提升的命令提示符运行，则此字段可能会显示错误。 
- **WamDefaultAuthority：** - 设置为 Azure AD 的"组织"。
- **WamDefaultId：** -https://login.microsoft.com始终"""为 Azure AD。
- **WamDefaultGUID：** - WAM 提供商的（Azure AD/Microsoft 帐户）GUID 用于默认 WAM Web 帐户。 

### <a name="sample-user-state-output"></a>示例用户状态输出

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

对于 Azure AD 注册设备，可以忽略此部分。

> [!NOTE]
> 该命令必须在用户上下文中运行才能检索该用户的有效状态。

- **AzureAdPrt：** - 如果设备上存在已登录用户的 PRT，则设置为"是"。
- **AzureAdPrt 更新时间：** - 设置为上次更新 PRT 时以 UTC 表示的时间。
- **AzureAdPrt 过期时间：** - 设置为在 UTC 中，如果 PRT 未续订，它将过期。
- **Azure AdPrt 授权：** - Azure AD 权限 URL
- **企业 Prt：** - 如果设备具有来自本地 ADFS 的 PRT，则设置为"是"。 对于混合 Azure AD 联接设备，设备可以同时从 Azure AD 和本地 AD 同时具有 PRT。 本地连接的设备将仅具有企业 PRT。
- **企业 Prt 更新时间：** - 设置为上次更新企业 PRT 时 UTC 的时间。
- **企业 Prt 过期时间：** - 设置为在 UTC 中，如果 PRT 未续订，它将过期。
- **企业授权：** - ADFS 授权 URL

### <a name="sample-sso-state-output"></a>样本 SSO 状态输出

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

### <a name="pre-join-diagnostics"></a>加入前诊断

仅当设备已加入域且无法混合 Azure AD 联接时，才会显示此部分。

本节执行各种测试以帮助诊断联接失败。 本节还包括上一个 （？） 的详细信息。 此信息包括错误阶段、错误代码、服务器请求 ID、服务器响应 http 状态、服务器响应错误消息。

- **用户上下文：** - 运行诊断的上下文。 可能的值：系统、UN-高架用户、提升用户。 

   > [!NOTE]
   > 由于实际联接是在 SYSTEM 上下文中执行的，因此在 SYSTEM 上下文中运行诊断最接近实际联接方案。 要在 SYSTEM 上下文中运行诊断，必须从提升的命令提示符运行 dsregcmd /状态命令。

- **客户端时间：** - 系统时间（以 UTC 表示）。
- **AD 连接测试：** - 测试对域控制器执行连接测试。 此测试中的错误可能会导致预检查阶段的联接错误。
- **AD 配置测试：** - 测试读取并验证 SCP 对象是否在本地 AD 林中正确配置。 此测试中的错误可能会导致发现阶段的联接错误，错误代码 0x801c001d。
- **DRS 发现测试：** - 测试从发现元数据终结点获取 DRS 终结点，并执行用户域请求。 此测试中的错误可能会导致发现阶段的联接错误。
- **DRS 连接测试：** - 测试执行与 DRS 终结点的基本连接测试。
- **令牌获取测试：** - 如果用户租户联合，测试尝试获取 Azure AD 身份验证令牌。 此测试中的错误可能会导致 auth 阶段的联接错误。 如果 auth 失败，将尝试将同步联接作为回退，除非使用以下注册表键设置显式禁用回退。
```
    Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
    Value: FallbackToSyncJoin
    Type:  REG_DWORD
    Value: 0x0 -> Disabled
    Value: 0x1 -> Enabled
    Default (No Key): Enabled
 ```
- **回退到同步连接：** - 设置为"已启用"，如果上述注册表项，以防止回退同步联接与 auth 失败， 不存在。 此选项可从 Windows 10 1803 及更高版本获得。
- **上一次注册：** - 上次"加入"尝试发生的时间。 只记录失败的联接尝试。
- **错误阶段：** - 已中止的联接阶段。 可能的值是预先检查、发现、auth、联接。
- **客户端错误代码：** - 返回的客户端错误代码 （HRESULT）。
- **服务器错误代码：** - 如果请求发送到服务器，服务器会使用错误代码进行回复，则服务器错误代码。 
- **服务器消息：** - 服务器消息随错误代码一起返回。
- **Https 状态：** - 服务器返回的 Http 状态。
- **请求 ID：** - 发送到服务器的客户端请求 ID。 可用于与服务器端日志关联。

### <a name="sample-pre-join-diagnostics-output"></a>样品预加入诊断输出

下面的示例显示诊断测试失败，并出现发现错误。

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

下面的示例显示诊断测试正在通过，但注册尝试失败，出现目录错误，该错误预期用于同步联接。 Azure AD 连接同步作业完成后，设备将能够加入。

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

### <a name="post-join-diagnostics"></a>加入后诊断

本节显示在加入云的设备上执行的健全性检查的输出。

- **启用 AadRecovery：** - 如果"是"，则存储在设备中的密钥不可用，并且设备标记为恢复。 下一个登录将触发恢复流并重新注册设备。
- **密钥测试：** - 如果"通过"设备密钥处于良好运行状况。 如果 KeySignTest 失败，设备通常会标记为恢复。 下一个登录将触发恢复流并重新注册设备。 对于混合 Azure AD 联接设备，恢复为静默状态。 在 Azure AD 加入或 Azure AD 注册时，设备将提示用户身份验证以在必要时恢复和重新注册设备。 **密钥签名测试需要提升的权限。**

#### <a name="sample-post-join-diagnostics-output"></a>样品连接后诊断输出

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisite-check"></a>NGC 先决条件检查

本节对为企业 （WHFB） 预配 Windows Hello 执行渗透检查。 

> [!NOTE]
> 如果用户已成功配置 WHFB，则可能无法在 dsregcmd /状态中看到 NGC 先决条件检查详细信息。

- **已加入：** - 如果设备已加入 Azure AD，则设置为"是"。
- **IsUserAzureAD：** - 如果登录的用户存在于 Azure AD 中，则设置为"是"。
- **策略启用：** - 如果设备上启用了 WHFB 策略，则设置为"是"。
- **启用后日志：** - 如果 WHFB 注册由平台本机触发，则设置为"是"。 如果设置为"否"，则表示"适用于企业的 Windows Hello"注册由自定义机制触发
- **设备合格：** - 如果设备满足使用 WHFB 注册的硬件要求，则设置为"是"。
- **会话不是远程：** - 如果当前用户直接登录到设备而不是远程登录到设备，则设置为"是"。
- **证书注册：** - 特定于 WHFB 证书信任部署，指示 WHFB 的证书注册权限。 如果 WHFB 策略的来源是组策略，则设置为"注册权限"，如果源为 MDM，则设置为"移动设备管理"。 否则"无"
- **AdfsRefreshToken：** - 特定于 WHFB 证书信任部署。 仅当 Cert注册是"注册权限"时才存在。 指示设备是否具有针对用户的企业 PRT。
- **AdfsRaIs 就绪：** - 特定于 WHFB 证书信任部署。  仅当 Cert注册是"注册权限"时才存在。 如果 ADFS 在发现元数据中指示它支持*WHFB，并且*登录证书模板可用，则设置为"是"。
- **登录证书就绪：** - 特定于 WHFB 证书信任部署。 仅当 Cert注册是"注册权限"时才存在。 如果登录证书模板的状态有效，则设置为"是"，并有助于排除 ADFS RA 的故障。
- **预结果：** - 提供所有 WHFB 先决条件评估的结果。 如果 WHFB 注册将在用户下次登录时作为登录后任务启动，则设置为"将预配"。

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
