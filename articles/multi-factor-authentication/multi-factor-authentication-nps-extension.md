---
title: "使用现有的 NPS 服务器提供 Azure MFA 功能 | Microsoft 文档"
description: "适用于 Azure 多重身份验证的网络策略服务器扩展是一个简单的解决方案，可将基于云的双步验证功能添加到现有的身份验证基础结构。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 68f1088a7303b5eddecc9ff99f7330c0462baafc
ms.openlocfilehash: 9fb517a08fb56a1adb9e23f820aa2e4aa20eacf6


---
# <a name="augment-your-existing-authentication-infrastructure-with-the-nps-extension-for-azure-multi-factor-authentication---public-preview"></a>通过适用于 Azure 多重身份验证的 NPS 扩展增强现有的身份验证基础结构 - 公共预览版

适用于 Azure MFA 的网络策略服务器 (NPS) 扩展可以使用现有的服务器将基于云的 MFA 功能添加到身份验证基础结构。 使用 NPS 扩展，可将电话呼叫、短信或电话应用验证添加到现有的身份验证流，而无需安装、配置和维护新服务器。 
 
使用适用于 Azure MFA 的 NPS 扩展时，身份验证流包括以下组件： 

1. **NAS/VPN 服务器**接收来自 VPN 客户端的请求，并将其转换为可发往 NPS 服务器的 RADIUS 请求。 
2. **NPS 服务器**连接到 Active Directory，针对 RADIUS 请求执行主要身份验证，成功后，将请求传递到所有已安装的扩展。  
3. **NPS 扩展**向 Azure MFA 触发一个执行辅助身份验证的请求。 该扩展收到响应后，如果 MFA 质询成功，该扩展将通过向 NPS 服务器提供由 Azure STS 颁发的、包含 MFA 声明的安全令牌，来完成身份验证请求。  
4. **Azure MFA** 与 Azure Active Directory 通信，检索用户的详细信息并使用配置给用户的验证方法执行辅助身份验证。

下图显示了此高级身份验证请求流： 

![身份验证流示意图](./media/multi-factor-authentication-nps-extension/auth-flow.png)

## <a name="prerequisites"></a>先决条件

NPS 扩展需与现有基础结构配合工作。 在开始之前，请确保满足以下先决条件。

### <a name="licenses"></a>许可证

适用于 Azure MFA 的 NPS 扩展向持有 [Azure 多重身份验证许可证](multi-factor-authentication.md)（Azure AD Premium、EMS 或 MFA 订阅已随附）的客户提供。

### <a name="software"></a>软件

已启用 NPS 组件的 Windows Server 2008 R2 SP1 或更高版本。

### <a name="libraries"></a>库

-    [Visual C++ Redistributable Packages for Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-    [用于 Windows PowerShell 的 Microsoft Azure Active Directory 模块版本 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

### <a name="azure-active-directory"></a>Azure Active Directory

使用 NPS 扩展的任何用户必须使用 Azure AD Connect 同步到 Azure Active Directory，并且必须启用 MFA。

安装该扩展时，需要使用 Azure AD 租户的目录 ID 和管理员凭据。 可在 [Azure 门户](https://portal.azure.com)中找到该目录 ID。 以管理员身份登录，在左侧选择“Azure Active Directory”图标，然后选择“属性”即可。 复制“目录 ID”框中的 GUID 并保存。

![在 Azure Active Directory 属性下找到目录 ID](./media/multi-factor-authentication-nps-extension/find-directory-id.png)

## <a name="install-the-nps-extension"></a>安装 NPS 扩展

> [!IMPORTANT]
> 在不同于 VPN 接入点的服务器上安装 NPS 扩展。 

若要安装适用于 Azure MFA 的 NPS 扩展，请执行以下操作：

1.    从 Microsoft 下载中心[下载 NPS 扩展](https://aka.ms/npsmfa)
2.    将二进制文件复制到想要配置的网络策略服务器
3.    运行 *setup.exe* 并遵照安装说明操作

完成安装后，安装程序将在以下位置创建 PowerShell 脚本：`C:\Program Files\Microsoft\AzureMfa\Config`（其中，C:\ 是安装驱动器）。 此 PowerShell 脚本执行以下操作：

-    创建自签名证书。
-    将证书的公钥关联到 Azure AD 上的服务主体。
-    将证书存储在本地计算机证书存储中。
-    向网络用户授予对证书私钥的访问权限。
-    重新启动 NPS。

除非你想要使用自己的证书（而不是 PowerShell 脚本生成的自签名证书），否则请运行该 PowerShell 脚本来完成安装。

## <a name="configure-your-nps-extension"></a>配置 NPS 扩展

本部分包含一些设计注意事项和建议，帮助用户成功完成 NPS 扩展的部署。

### <a name="configurations-limitations"></a>配置限制

- NPS 扩展需与现有部署配合工作，而不适用于新部署。 因此，适用于 Azure MFA 的 NPS 扩展不包含用于将用户和设置从 MFA 服务器迁移到云的工具。

- NPS 扩展使用本地 Active Directory 中的 UPN 来标识 Azure MFA 中的用户，以便执行辅助身份验证。 无法将该扩展配置为使用其他标识符，例如备用登录 ID，或者除 UPN 以外的自定义 AD 字段。  

### <a name="control-radius-clients-that-require-mfa"></a>控制需要 MFA 的 RADIUS 客户端

使用 NPS 扩展为 RADIUS 客户端启用 MFA 后，此客户端的所有身份验证都需要执行 MFA。 如果想要为一部分（而不是全部）RADIUS 客户端启用 MFA，可以配置两台 NPS 服务器，并只在其中一台服务器上安装该扩展。 将需要执行 MFA 的 RADIUS 客户端配置为向配置了该扩展的 NPS 服务器发送请求，将其他 RADIUS 客户端配置为向未配置该扩展的 NPS 服务器发送请求。

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>为未注册 MFA 的用户做好准备

如果你的某些用户未注册 MFA，你可以确定当他们尝试身份验证时要发生什么行为。 使用注册表路径 *HKLM\Software\Microsoft\AzureMFA* 中的注册表设置 *REQUIRE_USER_MATCH* 可以控制功能行为。 此项设置提供单个配置选项：

| 键 | 值 | 默认 |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | TRUE/FALSE | 未设置（相当于 TRUE） |

此项设置的目的是确定当某个用户未注册 MFA 时要执行哪个操作。 如果该键不存在、未设置或设置为 TRUE，并且用户未注册，则该扩展将通不过 MFA 质询。 如果该键设置为 FALSE 并且用户未注册，身份验证将会继续且不执行 MFA。

可以在登记用户的过程中选择创建此键，并将其设置为 FALSE。 由于设置此键会允许未注册 MFA 的用户登录，并且系统不会向其提出质询，因此，在转到生产环境之前应删除此键。

## <a name="troubleshooting"></a>故障排除

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>如何验证是否已按预期安装了客户端证书？

请在证书存储中查找安装程序创建的自签名证书，然后检查私钥中是否包含授予“网络服务”用户的权限。 该证书的使用者名称为 **CN \<tenantid\>，OU 为 Microsoft NPS Extension**

-------------------------------------------------------------

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>如何验证客户端证书是否已关联到 Azure Active Directory 中的租户？

打开 PowerShell 命令提示符并运行以下命令：

```
> import-module MSOnline
> Connect-MsolService
> Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 
```

这些命令会在 PowerShell 会话中列显所有可将你的租户与 NPS 扩展实例相关联的证书。 通过将客户端证书导出为不带私钥的“Base-64 编码 X.509(.cer)”文件来找到你的证书，并将它与 PowerShell 中的列表进行比较。

如果该命令返回了多个证书，可以使用 Valid-From 和 Valid-Until 时间戳（采用用户可读格式）来筛选出每个不相符的项。

-------------------------------------------------------------

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>我的请求为何失败并返回 ADAL 令牌错误？

此错误可能是多种原因之一造成的。 使用以下步骤可帮助排查问题：

1. 重新启动 NPS 服务器。
2. 验证是否已按预期安装了该客户端证书。
3. 验证该证书是否与 Azure AD 上的租户关联。
4. 验证是否可以从运行该扩展的服务器访问 https://login.windows.net/。

-------------------------------------------------------------

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>身份验证为何失败并在 HTTP 日志中返回一条错误，指出找不到用户？

验证 AD Connect 是否正在运行，并且该用户是否在 Windows Active Directory 和 Azure Active Directory 中存在。

------------------------------------------------------------

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>HTTP 日志中为何出现错误，并且所有身份验证都失败？

验证是否可以从运行 NPS 扩展的服务器访问 https://adnotifications.windowsazure.com。

## <a name="next-steps"></a>后续步骤

了解如何将 Azure MFA 与 [Active Directory](multi-factor-authentication-get-started-server-dirint.md)、[RADIUS 身份验证](multi-factor-authentication-get-started-server-radius.md)和 [LDAP 身份验证](multi-factor-authentication-get-started-server-ldap.md)相集成。



<!--HONumber=Feb17_HO2-->


