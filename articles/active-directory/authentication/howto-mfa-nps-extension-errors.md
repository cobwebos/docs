---
title: Azure MFA NPS 扩展故障排除-Azure Active Directory
description: 获取有关解决 Azure 多重身份验证的 NPS 扩展问题的帮助
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 09468272397925d9afd1d3014f4fcc1d6a222198
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611375"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-multi-factor-authentication"></a>解决 Azure 多重身份验证的 NPS 扩展出现的错误消息

如果在使用 Azure 多重身份验证的 NPS 扩展时遇到错误，请参考本文快速解决问题。 Nps 扩展日志位于安装了 nps 扩展的服务器上的 "**自定义视图** > **服务器角色** > "**网络策略和访问服务**下的事件查看器中。

## <a name="troubleshooting-steps-for-common-errors"></a>解决常见错误的故障排除步骤

| 错误代码 | 疑难解答步骤 |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [联系支持人员](#contact-microsoft-support)，并指明收集日志的步骤列表。 尽量详细地提供出错之前发生的情况，包括租户 ID 和用户主体名称 (UPN)。 |
| **CLIENT_CERT_INSTALL_ERROR** | 客户端证书的安装方式或者与租户的关联方式可能有问题。 遵循[排查 MFA NPS 扩展问题](howto-mfa-nps-extension.md#troubleshooting)中的说明调查客户端证书问题。 |
| **ESTS_TOKEN_ERROR** | 遵循[排查 MFA NPS 扩展问题](howto-mfa-nps-extension.md#troubleshooting)中的说明调查客户端证书和 ADAL 令牌问题。 |
| **HTTPS_COMMUNICATION_ERROR** | NPS 服务器无法从 Azure MFA 接收响应。 验证防火墙是否双向打开，用于传入和传出 https://adnotifications.windowsazure.com 的流量 |
| **HTTP_CONNECT_ERROR** | 在运行 NPS 扩展的服务器上，验证是否可访问 `https://adnotifications.windowsazure.com` 和 `https://login.microsoftonline.com/`。 如果无法加载这些站点，请排查该服务器上的连接问题。 |
| **适用于 Azure MFA 的 NPS 扩展：** <br> 适用于 Azure MFA 的 NPS 扩展仅对处于 AccessAccept 状态的 Radius 请求执行辅助身份验证。 收到响应状态为 AccessReject 的用户用户名请求，将忽略请求。 | 此错误通常反映了 AD 中的身份验证失败，或者 NPS 服务器无法接收来自 Azure AD 的响应。 使用端口 80 和 443 验证防火墙是否双向打开，以便流量进出 `https://adnotifications.windowsazure.com` 和 `https://login.microsoftonline.com`。 另外，还要务必检查“网络访问权限”的“拨入”选项卡上的设置是否设置为“通过 NPS 网络策略控制访问”。 如果没有为用户分配许可证，也会触发此错误。 |
| **REGISTRY_CONFIG_ERROR** | 注册表中缺少应用程序的某个项，原因可能是 [PowerShell 脚本](howto-mfa-nps-extension.md#install-the-nps-extension)不是在安装后运行的。 错误消息应包括缺少的项。 请确保在 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa 下创建该项。 |
| **REQUEST_FORMAT_ERROR** <br> Radius 请求缺少必需的 Radius userName\Identifier 属性。请验证 NPS 是否能够接收 RADIUS 请求 | 此错误通常反映了安装问题。 必须在可以接收 RADIUS 请求的 NPS 服务器上安装 NPS 扩展。 安装为 RRAS 和 RDG 等服务的依赖项的 NPS 服务器无法接收 Radius 请求。 安装在此类安装中的 NPS 扩展无法正常工作并会出错，因为它无法读取身份验证请求中的详细信息。 |
| **REQUEST_MISSING_CODE** | 请确保 NPS 和 NAS 服务器之间密码加密协议支持你正在使用的辅助身份验证方法。 PAP 在云中支持 Azure MFA 的所有身份验证方法：电话呼叫、单向短信、移动应用通知和移动应用验证码****。 **CHAPV2**和**EAP**支持电话呼叫和移动应用通知。 |
| **USERNAME_CANONICALIZATION_ERROR** | 验证该用户是否在本地 Active Directory 实例中存在，以及 NPS 服务是否有权访问目录。 如果使用跨林信任，请[联系支持人员](#contact-microsoft-support)，以获得进一步的帮助。 |

### <a name="alternate-login-id-errors"></a>备用登录 ID 错误

| 错误代码 | 错误消息 | 疑难解答步骤 |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | 错误：userObjectSid 查找失败 | 验证用户是否存在于本地 Active Directory 实例中。 如果使用跨林信任，请[联系支持人员](#contact-microsoft-support)，以获得进一步的帮助。 |
| **ALTERNATE_LOGIN_ID_ERROR** | 错误：备用 LoginId 查找失败 | 验证 LDAP_ALTERNATE_LOGINID_ATTRIBUTE 是否已设置为[有效的 Active Directory 属性](https://msdn.microsoft.com/library/ms675090(v=vs.85).aspx)。 <br><br> 如果 LDAP_FORCE_GLOBAL_CATALOG 设置为 True，或者 LDAP_LOOKUP_FORESTS 配置了非空值，请验证是否已配置全局目录以及是否已将 AlternateLoginId 属性添加到它。 <br><br> 如果 LDAP_LOOKUP_FORESTS 配置了非空值，请验证该值是否正确。 如果有多个林名称，必须用分号（而不是空格）分隔名称。 <br><br> 如果这些步骤不能解决此问题，请[与支持人员联系](#contact-microsoft-support)获取更多帮助。 |
| **ALTERNATE_LOGIN_ID_ERROR** | 错误：备用 LoginId 值为空 | 验证是否为用户配置了 AlternateLoginId 属性。 |

## <a name="errors-your-users-may-encounter"></a>用户可能会遇到的错误

| 错误代码 | 错误消息 | 疑难解答步骤 |
| ---------- | ------------- | --------------------- |
| **AccessDenied** | 调用方租户无权针对用户执行身份验证 | 检查租户域和用户主体名称 (UPN) 的域是否相同。 例如，确保 user@contoso.com 正在尝试向 Contoso 租户进行身份验证。 UPN 代表 Azure 中的租户的有效用户。 |
| **AuthenticationMethodNotConfigured** | 未为用户配置指定的身份验证方法 | 请让用户根据[管理双重验证设置](../user-help/multi-factor-authentication-end-user-manage-settings.md)中的说明添加或检查其验证方法。 |
| **AuthenticationMethodNotSupported** | 指定的身份验证方法不受支持。 | 请收集包含此错误的所有日志，并[联系支持人员](#contact-microsoft-support)。 联系支持人员时，请提供用户名以及触发该错误的辅助验证方法。 |
| **BecAccessDenied** | MSODS Bec 调用返回了拒绝访问错误，原因可能是租户中未定义用户名 | 该用户在本地 Active Directory 中存在，但未由 AD Connect 同步到 Azure AD。 或者，租户中缺少该用户。 请将该用户添加到 Azure AD，并让其根据[管理双重验证设置](../user-help/multi-factor-authentication-end-user-manage-settings.md)中的说明添加或检查其验证方法。 |
| **InvalidFormat** 或 **StrongAuthenticationServiceInvalidParameter** | 电话号码采用了无法识别的格式 | 请让用户更正其验证电话号码。 |
| **InvalidSession** | 指定的会话无效或已过期 | 完成会话花费的时间超过三分钟。 验证用户是否在发起身份验证请求后的三分钟内输入了验证码或者对应用通知做出了响应。 如果仍未解决问题，请检查客户端、NAS 服务器、NPS 服务器和 Azure MFA 终结点之间是否未出现网络延迟。  |
| **NoDefaultAuthenticationMethodIsConfigured** | 未为用户配置默认的身份验证方法 | 请让用户根据[管理双重验证设置](../user-help/multi-factor-authentication-end-user-manage-settings.md)中的说明添加或检查其验证方法。 验证用户是否已选择默认身份验证方法，并为其帐户配置了该方法。 |
| **OathCodePinIncorrect** | 输入了错误的代码和 PIN。 | NPS 扩展中应该不会出现此错误。 如果用户遇到此错误，请[联系支持人员](#contact-microsoft-support)以获得故障排除帮助。 |
| **ProofDataNotFound** | 未为指定的身份验证方法配置证明数据。 | 请让用户尝试不同的验证方法，或者根据[管理双重验证设置](../user-help/multi-factor-authentication-end-user-manage-settings.md)中的说明添加新的验证方法。 如果确认用户的验证方法已正确设置后用户仍看到此错误，请[联系支持人员](#contact-microsoft-support)。 |
| **SMSAuthFailedWrongCodePinEntered** | 输入了错误的代码和 PIN。 (OneWaySMS) | NPS 扩展中应该不会出现此错误。 如果用户遇到此错误，请[联系支持人员](#contact-microsoft-support)以获得故障排除帮助。 |
| **TenantIsBlocked** | 租户已被阻止 | [请联系支持人员](#contact-microsoft-support)并提供 Azure 门户中 Azure AD 属性页上的目录 ID。 |
| **UserNotFound** | 找不到指定的用户 | 该租户在 Azure AD 中不再显示为活动状态。 检查订阅是否处于活动状态，并且已创建所需的第一方应用。 此外，请确保证书使用者中的租户符合预期，并且该证书仍然有效且已在服务主体下注册。 |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>用户可能会遇到的不属于错误的消息

有时，由于身份验证请求失败，用户可能会收到多重身份验证发出的消息。 在配置产品中，这些消息并不属于错误，而是有意发出的警告，旨在解释身份验证请求被拒绝的原因。

| 错误代码 | 错误消息 | 建议的步骤 |
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | 输入了错误的代码\OATH 代码不正确 | 用户输入了错误的代码。 让他们通过请求新的代码或重新登录来重试。 |
| **SMSAuthFailedMaxAllowedCodeRetryReached** | 达到了允许的代码重试次数上限 | 用户通不过验证质询的次数过多。 根据设置，管理员可能需要立即将他们解除阻止。  |
| **SMSAuthFailedWrongCodeEntered** | 输入了错误的代码/短信 OTP 不正确 | 用户输入了错误的代码。 让他们通过请求新的代码或重新登录来重试。 |

## <a name="errors-that-require-support"></a>需要支持人员解决的错误

如果遇到以下错误之一，我们建议[联系支持人员](#contact-microsoft-support)来获得诊断帮助。 没有任何一组标准步骤可以解决这些错误。 联系支持人员时，请务必尽量详细地包含有关哪些步骤导致出错的信息以及租户信息。

| 错误代码 | 错误消息 |
| ---------- | ------------- |
| **InvalidParameter** | 请求不能 null |
| **InvalidParameter** | ReplicationScope {0} 的 ObjectId 不能为 null 或空 |
| **InvalidParameter** | CompanyName \{0}\ 的长度超过最大允许长度 {1} |
| **InvalidParameter** | UserPrincipalName 不能为 null 或空 |
| **InvalidParameter** | 提供的 TenantId 未采用正确的格式 |
| **InvalidParameter** | SessionId 不能为 null 或空 |
| **InvalidParameter** | 无法解析请求或 Msods 中的任何 ProofData。 ProofData 未知 |
| **InternalError** |  |
| **OathCodePinIncorrect** |  |
| **VersionNotSupported** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>后续步骤

### <a name="troubleshoot-user-accounts"></a>排查用户帐户问题

如果用户[在使用双重验证时遇到问题](../user-help/multi-factor-authentication-end-user-troubleshoot.md)，请帮助他们自我诊断问题。

### <a name="health-check-script"></a>运行状况检查脚本

排查 NPS 扩展问题时， [AZURE MFA NPS 扩展运行状况检查脚本](https://docs.microsoft.com/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)会执行基本运行状况检查。 运行脚本并选择选项3。

### <a name="contact-microsoft-support"></a>请与 Microsoft 支持部门联系

如需更多帮助，请通过 [Azure 多重身份验证服务器支持](https://support.microsoft.com/oas/default.aspx?prid=14947)联系支持专业人员。 与我们联系时，尽可能包含有关问题的更多信息将很有帮助。 可提供的信息包括看到错误的页面、特定错误代码、特定会话 ID、看到错误的用户的 ID 和调试日志。

若要收集支持诊断的调试日志，请在 NPS 服务器上使用以下步骤：

1. 打开注册表编辑器并浏览到 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa，将 VERBOSE_LOG 设置为 TRUE********
2. 打开管理员命令提示符并运行以下命令：

   ```
   Mkdir c:\NPS
   Cd NPS
   netsh trace start Scenario=NetConnection capture=yes tracefile=c:\NPS\nettrace.etl
   logman create trace "NPSExtension" -ow -o c:\NPS\NPSExtension.etl -p {7237ED00-E119-430B-AB0F-C63360C8EE81} 0xffffffffffffffff 0xff -nb 16 16 -bs 1024 -mode Circular -f bincirc -max 4096 -ets
   logman update trace "NPSExtension" -p {EC2E6D3A-C958-4C76-8EA4-0262520886FF} 0xffffffffffffffff 0xff -ets
   ```

3. 再现问题

4. 使用以下命令停止跟踪：

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

5. 打开注册表编辑器并浏览到 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa，将 VERBOSE_LOG 设置为 FALSE********
6. 压缩 C:\NPS 文件夹的内容，并将压缩文件附加到支持案例中。
