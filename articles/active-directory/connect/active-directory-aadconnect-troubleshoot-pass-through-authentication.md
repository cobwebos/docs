---
title: "Azure AD Connect：对直通身份验证进行故障排除 | Microsoft Docs"
description: "本文介绍如何对 Azure Active Directory (Azure AD) 直通身份验证进行故障排除。"
services: active-directory
keywords: "对 Azure AD Connect 直通身份验证进行故障排除, 安装 Active Directory, Azure AD 所需的组件, SSO, 单一登录"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 05d7c50aaa1209220b6cff3305fdb05dd2c421f8
ms.contentlocale: zh-cn
ms.lasthandoff: 06/17/2017

---

# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>对 Azure Active Directory 直通身份验证进行故障排除

本文可帮助你找到有关 Azure AD 直通身份验证常见问题的故障排除信息。

>[!IMPORTANT]
>如果使用直通身份验证时遇到用户登录问题，请在回退仅限云的全局管理员帐户后，再禁用此功能或卸载直通身份验证代理。 了解如何[添加仅限云的全局管理员帐户](../active-directory-users-create-azure-portal.md)。 此步骤至关重要，可确保你不被锁定在租户外部。

## <a name="general-issues"></a>常规问题

### <a name="user-facing-sign-in-error-messages"></a>面向用户的登录错误消息

如果用户无法使用直通身份验证进行登录，可能会在 Azure AD 登录屏幕上看到下列面向用户的错误之一： 

|错误|说明|解决方法
| --- | --- | ---
|AADSTS80001|无法连接到 Active Directory|确保代理服务器是需要验证其密码的用户所在的 AD 林的成员，并且能够连接到 Active Directory。  
|AADSTS8002|连接到 Active Directory 时超时|检查以确保 Active Directory 可用，并且可以响应代理的请求。
|AADSTS80004|传递到代理的用户名无效|确保用户尝试使用正确的用户名登录。
|AADSTS80005|验证遇到了不可预知的 WebException|暂时性的错误。 重试请求。 如果持续失败，请与 Microsoft 支持人员联系。
|AADSTS80007|与 Active Directory 通信时出错|检查代理日志以了解更多信息，并验证 Active Directory 是否按预期方式运行。

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center"></a>Azure Active Directory 管理中心登录失败原因

若要启动使用直通身份验证的用户登录问题故障排除，可以在 [Azure Active Directory 管理中心](https://aad.portal.azure.com/)查看[登录活动报告](../active-directory-reporting-activity-sign-ins.md)。

![登录报告](./media/active-directory-aadconnect-pass-through-authentication/pta4.png)

导航到 [Azure Active Directory 管理中心](https://aad.portal.azure.com/)的“Azure Active Directory” -> “登录”，然后单击特定用户的登录活动。 查找“登录错误代码”字段。 使用下表将该字段的值映射到某个失败原因和解决方法：

|登录错误代码|登录失败原因|解决方法
| --- | --- | ---
| 50144 | 用户的 Active Directory 密码已过期。 | 在本地 Active Directory 中重置用户的密码。
| 80001 | 没有可用的身份验证代理。 | 安装并注册身份验证代理。
| 80002 | 身份验证代理的密码验证请求已超时。 | 检查是否可以从身份验证代理访问你的 Active Directory。
| 80003 | 身份验证代理收到的响应无效。 | 如果在多个用户中均可重现此问题，请检查你的 Active Directory 配置。
| 80004 | 在登录请求中使用的用户主体名称 (UPN) 不正确。 | 要求用户使用正确的用户名登录。
| 80005 | 身份验证代理：出现错误。 | 暂时性的错误。 请稍后重试。
| 80007 | 身份验证代理无法连接到 Active Directory。 | 检查是否可以从身份验证代理访问你的 Active Directory。
| 80010 | 身份验证代理无法解密密码。 | 如果可始终重现该问题，请安装并注册新的身份验证代理。 并卸载当前的代理。 
| 80011 | 身份验证代理无法检索到解密密钥。 | 如果可始终重现该问题，请安装并注册新的身份验证代理。 并卸载当前的代理。

## <a name="authentication-agent-installation-issues"></a>身份验证代理安装问题

### <a name="an-azure-ad-application-proxy-connector-already-exists"></a>Azure AD 应用程序代理连接器已存在

不能在 [Azure AD 应用程序代理](../../active-directory/active-directory-application-proxy-get-started.md)连接器所在的同一台服务器上安装直通身份验证代理。 在单独的服务器上安装直通身份验证代理。

### <a name="an-unexpected-error-occurred"></a>发生了意外的错误

从服务器[收集代理日志](#collecting-pass-through-authentication-agent-logs)，然后联系 Microsoft 支持人员反映问题。

## <a name="authentication-agent-registration-issues"></a>身份验证代理注册问题

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>由于端口被阻止，身份验证代理注册失败

确保安装身份验证代理的服务器能够与我们的服务 URL 和[此处](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-prerequisites)列出的端口通信。

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>由于令牌或帐户授权错误，身份验证代理注册失败

确保对所有 Azure AD Connect 或独立身份验证代理安装和注册操作使用仅限云的全局管理员帐户。 已启用 MFA 的全局管理员帐户存在一个已知问题；作为解决方法，请暂时关闭 MFA（只是为了完成操作）。

### <a name="an-unexpected-error-occurred"></a>发生了意外的错误

从服务器[收集代理日志](#collecting-pass-through-authentication-agent-logs)，然后联系 Microsoft 支持人员反映问题。

## <a name="authentication-agent-uninstallation-issues"></a>身份验证代理卸载问题

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>卸载 Azure AD Connect 时出现警告消息

如果在租户中启用了直通身份验证，则当尝试卸载 Azure AD Connect 时，会显示以下警告消息：“除非已在其他服务器上安装其他直通身份验证代理，否则用户无法登录到 Azure AD。”

在卸载 Azure AD Connect 之前，确保设置具有[高可用性](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-4-ensure-high-availability)，以免影响用户登录。

## <a name="issues-with-enabling-the-feature"></a>启用该功能时的问题

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>由于没有可用的身份验证代理，启用该功能失败

必须至少有一个活动的身份验证代理才能在租户中启用直通身份验证。 可以通过安装 Azure AD Connect 或独立的身份验证代理来安装身份验证代理。

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>由于端口被阻止，启用该功能失败

确保安装 Azure AD Connect 的服务器能够与我们的服务 URL 和[此处](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-prerequisites)列出的端口通信。

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>由于令牌或帐户授权错误，启用该功能失败

启用该功能时，确保使用仅限云的全局管理员帐户。 已启用多重身份验证 (MFA) 的全局管理员帐户存在一个已知问题；作为解决方法，请暂时关闭 MFA（只是为了完成操作）。

## <a name="collecting-pass-through-authentication-agent-logs"></a>收集直通身份验证代理日志

根据遇到的问题类型，需要在不同的位置查看直通身份验证代理日志。

### <a name="authentication-agent-event-logs"></a>身份验证代理事件日志

对于与身份验证代理相关的错误，请在服务器上打开事件查看器应用程序，然后检查 Application and Service Logs\Microsoft\AadApplicationProxy\Connector\Admin 中的日志。

若要查看详细的分析，请启用“会话”日志。 在正常操作期间，请不要在启用此日志的情况下运行身份验证代理；仅用于故障排除。 日志内容只会在再次禁用日志后才可见。

### <a name="detailed-trace-logs"></a>详细跟踪日志

若要排查用户登录失败原因，请查看 C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace 中的跟踪日志。 这些日志包含特定用户使用直通身份验证功能登录失败的原因。 这些错误也会映射到前面的[表格](#sign-in-failure-reasons-on-the-Azure-portal)中所示的登录失败原因。 下面是一个日志项目示例：

```
    ApplicationProxyConnectorService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

可以通过打开命令提示符并运行以下命令来获取错误（在上述示例中为错误“1328”）的描述性详细信息（注意：请将“1328”替换为在日志中看到的实际错误编号）：

`Net helpmsg 1328`

![直通身份验证](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>域控制器日志

如果已启用审核日志记录，可以在域控制器的安全日志中找到更多信息。 下面演示了一种查询直通身份验证代理发送的登录请求的简单方法：

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe')]]</Select>
    </Query>
    </QueryList>
```

