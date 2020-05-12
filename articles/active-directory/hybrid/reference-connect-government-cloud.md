---
title: Azure AD Connect： Azure 政府云的混合标识注意事项
description: 有关部署 Azure AD Connect 与 Azure 政府云的特别注意事项。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: acdc99ca50255bd9b75828f0a051f364c5218471
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115483"
---
# <a name="hybrid-identity-considerations-for-the-azure-government-cloud"></a>Azure 政府云的混合标识注意事项

本文介绍将混合环境与 Microsoft Azure 政府云集成的注意事项。 此信息作为对使用 Azure 政府云的管理员和架构师的参考提供。

> [!NOTE]
> 若要将本地 Microsoft Azure Active Directory （Azure AD）环境与 Azure 政府版云集成，需升级到[Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)的最新版本。

有关美国政府版的政府版终结点的完整列表，请参阅[文档](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints)。

## <a name="azure-ad-pass-through-authentication"></a>Azure AD 直通身份验证

以下信息介绍如何实现直通身份验证和 Azure 政府云。

### <a name="allow-access-to-urls"></a>允许访问 URL

在部署直通身份验证代理之前，请验证服务器与 Azure AD 之间是否存在防火墙。 如果你的防火墙或代理允许域名系统（DNS）已阻止或安全程序，则添加以下连接。

> [!NOTE]
> 以下指南也适用于安装适用于 Azure 政府环境的[Azure AD 应用程序代理连接器](https://aka.ms/whyappproxy)。

|代码 |用途|
|-----|-----|
|&#42;. msappproxy.us</br>&#42;.servicebus.usgovcloudapi.net|代理使用这些 Url 与 Azure AD 云服务进行通信。 |
|mscrl.microsoft.us:80 </br>crl.microsoft.us:80 </br>ocsp.msocsp.us:80 </br>www.microsoft.us:80| 代理使用这些 Url 来验证证书。|
|login.windows.us </br>secure.aadcdn.microsoftonline p.com </br>&#42;. microsoftonline.us </br>&#42;. microsoftonline-p.us </br>&#42;. msauth.net </br>&#42;. msauthimages.net </br>&#42;. msecnd.net</br>&#42;. msftauth.net </br>&#42;. msftauthimages.net</br>&#42;. phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| 代理在注册过程中使用这些 Url。

### <a name="install-the-agent-for-the-azure-government-cloud"></a>为 Azure 政府云安装代理

按照以下步骤安装 Azure 政府云的代理：

1. 在命令行终端中，前往包含安装代理的可执行文件的文件夹。
1. 运行以下命令，这些命令指定安装适用于 Azure 政府。

   对于直通身份验证：

   ```
   AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
   ```

   对于应用程序代理：

   ```
   AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
   ```

## <a name="single-sign-on"></a>单一登录

### <a name="set-up-your-azure-ad-connect-server"></a>设置 Azure AD Connect 服务器

如果使用直通身份验证作为登录方法，则无需进行其他先决条件检查。 如果使用密码哈希同步作为登录方法，并且 Azure AD Connect 和 Azure AD 之间存在防火墙，请确保：

- 使用 Azure AD Connect 版本1.1.644.0 或更高版本。
- 如果你的防火墙或代理允许 DNS 被阻止或安全程序，则通过端口443将连接添加 &#42; 到 msappproxy.us Url。

  如果不是，则允许访问每周更新的 Azure 数据中心 IP 范围。 仅当你启用此功能时，此先决条件才适用。 实际的用户登录不需要此项。

### <a name="roll-out-seamless-single-sign-on"></a>推出无缝单一登录

你可以使用以下说明逐步推出 Azure AD 无缝单一登录到你的用户。 首先， [https://autologon.microsoft.us](https://autologon.microsoft.us) 请使用 Active Directory 中的 "组策略将 AZURE AD URL 添加到所有或选定用户的 Intranet 区域设置。

还需要启用 intranet 区域策略设置 "**允许通过脚本更新状态栏" 组策略**。

## <a name="browser-considerations"></a>浏览器注意事项

### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox（所有平台）

Mozilla Firefox 不会自动使用 Kerberos 身份验证。 每个用户必须通过执行以下步骤，手动将 Azure AD URL 添加到其 Firefox 设置：

1. 运行 Firefox 并在地址栏中输入 **about： config**   。 关闭可能会看到的任何通知。
1. 搜索 "网络" " **协商-身份验证-uri**"   首选项。 此首选项列出了 Firefox 信任的用于 Kerberos 身份验证的站点。
1. 右键单击首选项名称，然后选择 " **修改**"。
1.  [**https://autologon.microsoft.us**](https://autologon.microsoft.us**)   在框中输入。
1. 选择 **"确定"**   ，然后重新打开浏览器。

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>基于 Chromium 的 Microsoft Edge （所有平台）

如果你  `AuthNegotiateDelegateAllowlist`   `AuthServerAllowlist`   在环境中重写或策略设置，请确保将 Azure AD URL 添加 [https://autologon.microsoft.us](https://autologon.microsoft.us) 到其中。

### <a name="google-chrome-all-platforms"></a>Google Chrome（所有平台）

如果你  `AuthNegotiateDelegateWhitelist`   `AuthServerWhitelist`   在环境中重写或策略设置，请确保将 Azure AD URL 添加 [https://autologon.microsoft.us](https://autologon.microsoft.us) 到其中。

## <a name="next-steps"></a>后续步骤

- [传递身份验证](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
- [单一登录](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)
