---
title: 故障排除混合 Azure 活动目录联接的设备
description: 排查已加入混合 Azure Active Directory 的 Windows 10 和 Windows Server 2016 设备问题。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26e52930211611673b6fe2309e2dca067a91ebc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331769"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>故障排除混合 Azure 活动目录联接的设备 

本文的内容适用于运行 Windows 10 或 Windows 服务器 2016 的设备。

有关其他 Windows 客户端，请参阅"[故障排除混合 Azure 活动目录"加入低级设备](troubleshoot-hybrid-join-windows-legacy.md)。

本文假设你已[配置已加入混合 Azure Active Directory 的设备](hybrid-azuread-join-plan.md)，以支持以下方案：

- 基于设备的条件访问
- [企业设置漫游](../active-directory-windows-enterprise-state-roaming-overview.md)
- [适合商务的窗口](../active-directory-azureadjoin-passport-deployment.md)

本文档提供故障排除指南，以解决潜在问题。 

对于 Windows 10 和 Windows Server 2016，混合 Azure Active Directory 加入功能支持 Windows 10 November 2015 Update 和更高版本。

## <a name="troubleshoot-join-failures"></a>排除联接故障

### <a name="step-1-retrieve-the-join-status"></a>步骤 1：检索加入状态 

**检索加入状态：**

1. 以管理员身份打开命令提示
2. 键入 `dsregcmd /status`

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-xxxx-xxxx-xxxx-2d7cd011xxxx
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```

### <a name="step-2-evaluate-the-join-status"></a>步骤 2：评估加入状态 

检查以下字段，确保它们包含预期值：

#### <a name="domainjoined--yes"></a>DomainJoined : YES  

此字段指示设备是否已加入本地 Active Directory。 如果值为 **NO**，则设备无法执行混合 Azure AD 加入。  

#### <a name="workplacejoined--no"></a>WorkplaceJoined : NO  

此字段指示设备是否以个人设备的形式注册到 Azure AD（标记为“已加入工作区”）。** 对于已加入域，同时已加入混合 Azure AD 的计算机，此值应为 **NO**。 如果值为 **YES**，则表示在完成混合 Azure AD 加入之前已添加工作或学校帐户。 在这种情况下，如果使用 Windows 10 周年更新版 (1607)，则会忽略该帐户。

#### <a name="azureadjoined--yes"></a>AzureAdJoined : YES  

此字段指示设备是否已加入。 如果设备是 Azure AD 联接设备或混合 Azure AD 联接设备，则该值将为 **"是**"。
如果值为 **NO**，则表示加入到 Azure AD 的过程尚未完成。 

继续执行后续步骤以进行进一步的故障排除。

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>步骤 3：查找联接失败和错误代码的阶段

#### <a name="windows-10-1803-and-above"></a>视窗 10 1803 及以上

在联接状态输出的"诊断数据"部分中查找"以前的注册"小节。 仅当设备已加入域且无法混合 Azure AD 联接时，才会显示此部分。
"错误阶段"字段表示联接失败阶段，而"客户端错误代码"表示联接操作的错误代码。

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>较旧的 Windows 10 版本

使用事件查看器日志查找联接失败的阶段和错误代码。

1. 在事件查看器中打开**用户设备注册**事件日志。 位于**应用程序和服务下日志** > **微软** > **Windows** > **用户设备注册**
2. 查找具有以下事件 I304、305、307 的事件。

![故障日志事件](./media/troubleshoot-hybrid-join-windows-current/1.png)

![故障日志事件](./media/troubleshoot-hybrid-join-windows-current/2.png)

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>第 4 步：从以下列表中检查可能的原因和解决方法

#### <a name="pre-check-phase"></a>预检查阶段

故障的可能原因：

- 设备对域控制器没有视线。
   - 设备必须位于组织的内部网络或 VPN 上，其网络视线必须连接到本地活动目录 （AD） 域控制器。

#### <a name="discover-phase"></a>发现阶段

故障的可能原因：

- 服务连接点 （SCP） 对象配置错误/无法从 DC 读取 SCP 对象。
   - 在设备所属的 AD 林中，需要有效的 SCP 对象，该对象指向 Azure AD 中的已验证域名。
   - 详细信息可在["配置服务连接点](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)"一节中找到。
- 无法从发现终结点连接和提取发现元数据。
   - 设备应该能够访问`https://enterpriseregistration.windows.net`SYSTEM 上下文中的 注册和授权终结点。 
   - 如果本地环境需要出站代理，IT 管理员必须确保设备的计算机帐户能够发现并静默地对出站代理进行身份验证。
- 无法连接到用户域终结点并执行域发现。 （仅限 Windows 10 版本 1809 及更高版本）
   - 设备应该能够访问`https://login.microsoftonline.com`SYSTEM 上下文中，以执行已验证域的域发现并确定域类型（托管/联合）。
   - 如果本地环境需要出站代理，IT 管理员必须确保设备上的 SYSTEM 上下文能够发现并静默地对出站代理进行身份验证。

**常见错误代码：**

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** （0x801c001d/-2145648611）
   - 原因：无法读取 SCP 对象并获取 Azure AD 租户信息。
   - 解决方法：请参阅[配置服务连接点](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)的部分。
- **DSREG_AUTOJOIN_DISC_FAILED** （0x801c0021/-2145648607）
   - 原因：通用发现失败。 无法从 DRS 获取发现元数据。
   - 解决方法：查找下面的子错误以进一步调查。
- **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT** （0x801c001f/-2145648609）
   - 原因：执行发现时操作超时。
   - 解决方法：确保`https://enterpriseregistration.windows.net`在 SYSTEM 上下文中可以访问该功能。 有关详细信息，请参阅"[网络连接要求](hybrid-azuread-join-managed-domains.md#prerequisites)"一节。
- **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** （0x801c0021/-2145648611）
   - 原因：通用领域发现失败。 无法从 STS 确定域类型（托管/联合）。 
   - 解决方法：查找下面的子错误以进一步调查。

**常见子错误代码：**

要查找发现错误代码的子错误代码，请使用以下方法之一。

##### <a name="windows-10-1803-and-above"></a>视窗 10 1803 及以上

在联接状态输出的"诊断数据"部分中查找"DRS 发现测试"。 仅当设备已加入域且无法混合 Azure AD 联接时，才会显示此部分。

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : UN-ELEVATED User
               Client Time : 2019-06-05 08:25:29.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x80072ee2]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

+----------------------------------------------------------------------+
```

##### <a name="older-windows-10-versions"></a>较旧的 Windows 10 版本

使用事件查看器日志查找联接失败的阶段和错误代码。

1. 在事件查看器中打开**用户设备注册**事件日志。 位于**应用程序和服务下日志** > **微软** > **Windows** > **用户设备注册**
2. 查找具有以下事件 201 的事件

![故障日志事件](./media/troubleshoot-hybrid-join-windows-current/5.png)

###### <a name="network-errors"></a>网络错误

- **WININET_E_CANNOT_CONNECT** （0x80072efd/-2147012867）
   - 原因：无法与服务器建立连接
   - 解决方法：确保与所需的 Microsoft 资源进行网络连接。 有关详细信息，请参阅[网络连接要求](hybrid-azuread-join-managed-domains.md#prerequisites)。
- **WININET_E_TIMEOUT** （0x80072ee2/-2147012894）
   - 原因：一般网络超时。
   - 解决方法：确保与所需的 Microsoft 资源进行网络连接。 有关详细信息，请参阅[网络连接要求](hybrid-azuread-join-managed-domains.md#prerequisites)。
- **WININET_E_DECODING_FAILED** （0x80072f8f/-2147012721）
   - 原因：网络堆栈无法解码来自服务器的响应。
   - 解决方法：确保网络代理不会干扰和修改服务器响应。

###### <a name="http-errors"></a>HTTP 错误

- **DSREG_DISCOVERY_TENANT_NOT_FOUND** （0x801c003a/-2145648582）
   - 原因：配置错误的租户 ID 的 SCP 对象。 或者在租户中找不到活动订阅。
   - 解决方法：确保 SCP 对象配置了正确的 Azure AD 租户 ID 和活动订阅，或存在于租户中。
- **DSREG_SERVER_BUSY** （0x801c0025/-2145648603）
   - 原因：来自 DRS 服务器的 HTTP 503。
   - 解决方法：服务器当前不可用。 一旦服务器重新联机，未来的联接尝试可能会成功。

###### <a name="other-errors"></a>其他错误

- **E_INVALIDDATA** （0x8007000d/-2147024883）
   - 原因：无法分析服务器响应 JSON。 可能是由于代理返回 HTTP 200 与 HTML auth 页面。
   - 解决方法：如果本地环境需要出站代理，IT 管理员必须确保设备上的 SYSTEM 上下文能够发现并静默地对出站代理进行身份验证。

#### <a name="authentication-phase"></a>身份验证阶段

仅适用于联合域帐户。

失败的原因：

- 无法为 DRS 资源静默获取访问令牌。
   - Windows 10 设备使用集成 Windows 身份验证从联合身份验证服务获取身份验证令牌，以访问活动的 WS-Trust 终结点。 详细信息：[联合服务配置](hybrid-azuread-join-manual.md#set-up-issuance-of-claims)

**常见错误代码：**

使用事件查看器日志查找错误代码、子错误代码、服务器错误代码和服务器错误消息。

1. 在事件查看器中打开**用户设备注册**事件日志。 位于**应用程序和服务下日志** > **微软** > **Windows** > **用户设备注册**
2. 查找具有以下事件 ID 305 的事件

![故障日志事件](./media/troubleshoot-hybrid-join-windows-current/3.png)

##### <a name="configuration-errors"></a>配置错误

- **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** （0xcaa90017/-894894057）
   - 原因：身份验证协议不是 WS 信任。
   - 解决方法：本地标识提供程序必须支持 WS 信任 
- **ERROR_ADAL_FAILED_TO_PARSE_XML** （0xcaa9002c/-894894036）
   - 原因：本地联合身份验证服务未返回 XML 响应。
   - 解决方法：确保 MEX 终结点返回有效的 XML。 确保代理不会干扰并返回非 xml 响应。
- **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** （0xcaa90023/-894894045）
   - 原因：找不到用户名/密码身份验证的终结点。
   - 解决方法：检查本地标识提供程序设置。 确保启用 WS 信任终结点，并确保 MEX 响应包含这些正确的终结点。

##### <a name="network-errors"></a>网络错误

- **ERROR_ADAL_INTERNET_TIMEOUT** （0xcaa82ee2/-894947614）
   - 原因：一般网络超时。
   - 解决方法：确保`https://login.microsoftonline.com`在 SYSTEM 上下文中可以访问该功能。 确保本地标识提供程序在 SYSTEM 上下文中可访问。 有关详细信息，请参阅[网络连接要求](hybrid-azuread-join-managed-domains.md#prerequisites)。
- **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** （0xcaa82efe/-894947586）
   - 原因：与 auth 终结点的连接已中止。
   - 解决方法：在某个时间重试或尝试从备用稳定网络位置加入。
- **ERROR_ADAL_INTERNET_SECURE_FAILURE** （0xcaa82f8f/-894947441）
   - 原因：无法验证服务器发送的传输层安全 （TLS），以前称为安全套接字层 （SSL）。
   - 分辨率：检查客户端时间偏差。 重试某个时间后，或尝试从备用稳定网络位置加入。 
- **ERROR_ADAL_INTERNET_CANNOT_CONNECT** （0xcaa82efd/-894947587）
   - 原因：尝试连接到`https://login.microsoftonline.com`失败。
   - 解决方法：检查到`https://login.microsoftonline.com`的网络连接。

##### <a name="other-errors"></a>其他错误

- **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** （0xcaa20003/-895352829）
   - 原因：Azure AD 不接受来自本地标识提供程序的 SAML 令牌。
   - 解决方法：检查联合服务器设置。 在身份验证日志中查找服务器错误代码。
- **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** （0xcaa90014/-894894060）
   - 原因：服务器 WS-Trust 响应报告故障异常，无法获取断言
   - 解决方法：检查联合服务器设置。 在身份验证日志中查找服务器错误代码。
- **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** （0xcaa90006/-894894074）
   - 原因：尝试从令牌终结点获取访问令牌时收到错误。
   - 解决方法：查找 ADAL 日志中的基础错误。 
- **ERROR_ADAL_OPERATION_PENDING** （0xcaa1002d/-895418323）
   - 原因：一般 ADAL 失败
   - 解决方法：从身份验证日志中查找子错误代码或服务器错误代码。
    
#### <a name="join-phase"></a>加入阶段

失败的原因：

查找注册类型并从下面的列表中查找错误代码。

#### <a name="windows-10-1803-and-above"></a>视窗 10 1803 及以上

在联接状态输出的"诊断数据"部分中查找"以前的注册"小节。 仅当设备已加入域且无法混合 Azure AD 联接时，才会显示此部分。
"注册类型"字段表示执行的联接类型。

```
+----------------------------------------------------------------------+
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

#### <a name="older-windows-10-versions"></a>较旧的 Windows 10 版本

使用事件查看器日志查找联接失败的阶段和错误代码。

1. 在事件查看器中打开**用户设备注册**事件日志。 位于**应用程序和服务下日志** > **微软** > **Windows** > **用户设备注册**
2. 查找具有以下事件 204 的事件

![故障日志事件](./media/troubleshoot-hybrid-join-windows-current/4.png)

##### <a name="http-errors-returned-from-drs-server"></a>从 DRS 服务器返回的 HTTP 错误

- **DSREG_E_DIRECTORY_FAILURE** （0x801c03f2/-2145647630）
   - 原因：收到来自带有错误代码的 DRS 的错误响应："目录错误"
   - 解决方法：有关可能的原因和解决方法，请参阅服务器错误代码。
- **DSREG_E_DEVICE_AUTHENTICATION_ERROR** （0x801c0002/-2145648638）
   - 原因：收到带有错误代码的 DRS 的错误响应："身份验证错误"和"错误子代码"不是"设备未找到"。 
   - 解决方法：有关可能的原因和解决方法，请参阅服务器错误代码。
- **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** （0x801c0006/-2145648634）
   - 原因：收到来自带有错误代码的 DRS 的错误响应："目录错误"
   - 解决方法：有关可能的原因和解决方法，请参阅服务器错误代码。

##### <a name="tpm-errors"></a>TPM 错误

- **NTE_BAD_KEYSET** （0x80090016/-2146893802）
   - 原因：TPM 操作失败或无效
   - 分辨率：可能是由于系统准备映像错误。 确保从中创建 sysprep 映像的计算机不是 Azure AD 联接、混合 Azure AD 加入或注册 Azure AD。
- **TPM_E_PCP_INTERNAL_ERROR** （0x80290407/-2144795641）
   - 原因：通用 TPM 错误。 
   - 解决方法：禁用出现此错误的设备上的 TPM。 Windows 10 版本 1809 及以上会自动检测 TPM 故障，无需使用 TPM 即可完成混合 Azure AD 联接。
- **TPM_E_NOTFIPS** （0x80280036/-2144862154）
   - 原因：FIPS 模式下的 TPM 当前不受支持。
   - 解决方法：禁用出现此错误的设备上的 TPM。 Windows 1809 自动检测 TPM 故障，无需使用 TPM 即可完成混合 Azure AD 联接。
- **NTE_AUTHENTICATION_IGNORED** （0x80090031/-2146893775）
   - 原因：TPM 被锁定。
   - 分辨率：瞬态错误。 等待冷却期。 一段时间后加入尝试应该会成功。 更多信息请参阅文章[TPM 基础知识](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering)

##### <a name="network-errors"></a>网络错误

- **WININET_E_TIMEOUT** （0x80072ee2/-2147012894）
   - 原因：尝试在 DRS 注册设备时常规网络超时
   - 解决方法：检查到`https://enterpriseregistration.windows.net`的网络连接。
- **WININET_E_NAME_NOT_RESOLVED** （0x80072ee7/-2147012889）
   - 原因：无法解析服务器名称或地址。
   - 解决方法：检查到`https://enterpriseregistration.windows.net`的网络连接。 确保主机名的 DNS 分辨率在 n/w 和设备上准确无误。
- **WININET_E_CONNECTION_ABORTED** （0x80072efe/-2147012866）
   - 原因：与服务器的连接异常终止。
   - 解决方法：在某个时间重试或尝试从备用稳定网络位置加入。

##### <a name="federated-join-server-errors"></a>联合联接服务器错误

| 服务器错误代码 | 服务器错误消息 | 可能的原因 | 解决方法 |
| --- | --- | --- | --- |
| 目录错误 | 你的请求暂时受到限制。 请在 300 秒后重试。 | 预期错误。 可能是由于连续多次提出注册请求。 | 冷却期后重试联接 |

##### <a name="sync-join-server-errors"></a>同步联接服务器错误

| 服务器错误代码 | 服务器错误消息 | 可能的原因 | 解决方法 |
| --- | --- | --- | --- |
| 目录错误 | AADSTS90002：未<UUID>找到租户。 如果租户没有活动订阅，则可能发生此错误。 请咨询订阅管理员。 | SCP 对象中的租户 ID 不正确 | 确保 SCP 对象配置了正确的 Azure AD 租户 ID 和活动订阅，并存在于租户中。 |
| 目录错误 | 找不到给定 ID 的设备对象。 | 同步联接的预期错误。 设备对象尚未从 AD 同步到 Azure AD | 等待 Azure AD 连接同步完成，同步完成后的下一次加入尝试将解决此问题 |
| 身份验证错误 | 目标计算机 SID 的验证 | Azure AD 设备上的证书与用于在同步联接期间对 Blob 进行签名的证书不匹配。 此错误通常表示同步尚未完成。 |  等待 Azure AD 连接同步完成，同步完成后的下一次加入尝试将解决此问题 |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>第 5 步：收集日志并联系 Microsoft 支持

在此处获取公共脚本[https://1drv.ms/u/s：！AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ]( https://1drv.ms/u/s!AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ)

1. 打开管理员命令提示符并运行`start_ngc_tracing_public.cmd`。
2. 执行重现问题的步骤。
3. 通过 执行 停止运行日志记录脚本`stop_ngc_tracing_public.cmd`。
4. Zip 并发送下`%SYSTEMDRIVE%\TraceDJPP\*`日志进行分析。

## <a name="troubleshoot-post-join-issues"></a>解决加入后问题

### <a name="retrieve-the-join-status"></a>检索加入状态 

#### <a name="wamdefaultset-yes-and-azureadprt-yes"></a>WamDefaultSet：是，AzureADPrt：是
  
这些字段指示用户在登录设备时是否已成功通过 Azure AD 的身份验证。 如果值为 **NO**，原因可能是：

- 注册时与设备关联的 TPM 中的存储密钥错误（在运行提升时选中 KeySignTest）。
- 备用登录 ID
- 找不到 HTTP 代理

## <a name="known-issues"></a>已知问题
- 在"设置 -> 帐户 -> 访问工作或学校"下，混合 Azure AD 加入的设备可能会显示两个不同的帐户，一个用于 Azure AD，另一个用于本地 AD，当连接到移动热点或外部 WiFi 网络时。 这只是一个 UI 问题，对功能没有任何影响。 
 
## <a name="next-steps"></a>后续步骤

[使用 dsregcmd 命令继续对设备进行故障排除](troubleshoot-device-dsregcmd.md)

如有问题，请参阅[设备管理常见问题解答](faq.md)
