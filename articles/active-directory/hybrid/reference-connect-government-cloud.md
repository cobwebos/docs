---
title: Azure AD Connect：针对 Azure 政府云的混合标识注意事项
description: 将 Azure AD Connect 与 Azure 政府云一起部署时的特别注意事项。
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
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115483"
---
# <a name="hybrid-identity-considerations-for-the-azure-government-cloud"></a>Azure 政府云的混合标识注意事项

本文介绍了将混合环境与 Microsoft Azure 政府云集成时的注意事项。 这些信息是为使用 Azure 政府云的管理员和架构师提供的参考。

> [!NOTE]
> 若要将本地 Microsoft Azure Active Directory (Azure AD) 环境与 Azure 政府云集成，你需要升级到最新版本的 [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)。

有关美国政府国防部终结点的完整列表，请参阅此[文档](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints)。

## <a name="azure-ad-pass-through-authentication"></a>Azure AD 直通身份验证

下面介绍了如何实现直通身份验证和 Azure 政府云。

### <a name="allow-access-to-urls"></a>允许访问 URL

在部署直通身份验证代理之前，请验证服务器与 Azure AD 之间是否存在防火墙。 如果你的防火墙或代理允许阻止了域名系统 (DNS) 的程序或安全程序，请添加以下连接。

> [!NOTE]
> 以下指南还适用于为 Azure 政府环境安装 [Azure AD 应用程序代理连接器](https://aka.ms/whyappproxy)。

|代码 |用途|
|-----|-----|
|&#42;.msappproxy.us</br>&#42;.servicebus.usgovcloudapi.net|代理使用这些 URL 与 Azure AD 云服务通信。 |
|mscrl.microsoft.us:80 </br>crl.microsoft.us:80 </br>ocsp.msocsp.us:80 </br>www.microsoft.us:80| 代理使用这些 URL 来验证证书。|
|login.windows.us </br>secure.aadcdn.microsoftonline p.com </br>&#42;.microsoftonline.us </br>&#42;.microsoftonline-p.us </br>&#42;.msauth.net </br>&#42;.msauthimages.net </br>&#42;.msecnd.net</br>&#42;.msftauth.net </br>&#42;.msftauthimages.net</br>&#42;.phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| 在注册过程中，代理使用这些 URL。

### <a name="install-the-agent-for-the-azure-government-cloud"></a>为 Azure 政府云安装代理

按照以下步骤为 Azure 政府云安装代理：

1. 在命令行终端中，转到用于安装代理的可执行文件所在的文件夹。
1. 运行以下命令，这些命令指定该安装是用于 Azure 政府的。

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

如果使用直通身份验证作为登录方法，则无需进行其他先决条件检查。 如果使用密码哈希同步作为登录方法，并且 Azure AD Connect 与 Azure AD 之间有防火墙，则请确保：

- 你使用 Azure AD Connect 版本 1.1.644.0 或更高版本。
- 如果你的防火墙或代理允许阻止了 DNS 的程序或安全程序，请添加通过端口 443 到 &#42;.msappproxy.us URL 的连接。

  否则，请允许访问每周更新的 Azure 数据中心 IP 范围。 此先决条件仅适用于启用了该功能的情况。 对于实际的用户登录，不需要满足此先决条件。

### <a name="roll-out-seamless-single-sign-on"></a>推行无缝单一登录

你可以根据以下说明逐步向你的用户推行 Azure AD 无缝单一登录。 首先，使用 Active Directory 中的组策略将 Azure AD URL [https://autologon.microsoft.us](https://autologon.microsoft.us) 添加到所有或所选用户的 Intranet 区域设置。

你还需要通过组策略启用 Intranet 区域策略设置“允许通过脚本更新状态栏”。

## <a name="browser-considerations"></a>浏览器注意事项

### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox（所有平台）

Mozilla Firefox 不会自动使用 Kerberos 身份验证。 每个用户必须通过以下步骤手动将 Azure AD URL 添加到其 Firefox 设置：

1. 运行 Firefox 并在地址栏中输入 **about:config** 。 关闭你可能会看到的任何通知。
1. 搜索 **network.negotiate-auth.trusted-uris**  首选项。 此首选项列出了 Firefox 信任的用于 Kerberos 身份验证的站点。
1. 右键单击首选项名称，然后选择“修改”。 ****
1. 在框中输入  [ **https://autologon.microsoft.us** ](https://autologon.microsoft.us**)  。
1. 选择“确定” ****  ，然后重新打开浏览器。

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>基于 Chromium 的 Microsoft Edge（所有平台）

如果覆盖了环境中的  `AuthNegotiateDelegateAllowlist`  或 `AuthServerAllowlist`  策略设置，请确保将 Azure AD URL [https://autologon.microsoft.us](https://autologon.microsoft.us) 添加到其中。

### <a name="google-chrome-all-platforms"></a>Google Chrome（所有平台）

如果覆盖了环境中的  `AuthNegotiateDelegateWhitelist`  或 `AuthServerWhitelist`  策略设置，请确保将 Azure AD URL [https://autologon.microsoft.us](https://autologon.microsoft.us) 添加到其中。

## <a name="next-steps"></a>后续步骤

- [直通身份验证](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
- [单一登录](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)
