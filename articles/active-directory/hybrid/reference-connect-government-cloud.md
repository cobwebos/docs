---
title: Azure AD Connect： Azure 政府的混合标识注意事项
description: 与政府云一起部署 Azure AD Connect 时的特殊注意事项。
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
ms.openlocfilehash: 2ad28beb68afb5207e7b36c5d76c4dac808c5114
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81378920"
---
# <a name="hybrid-identity-considerations-for-azure-government"></a>针对 Azure 政府的混合标识注意事项 
以下文档介绍了使用 Azure 政府云实现混合环境的注意事项。  此信息作为对使用 Azure 政府云的管理员和架构师的参考提供。  
> [!NOTE] 
> 若要将本地 AD 环境与 Azure Governemnt 云集成，需要升级到[Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)的最新版本。 

> [!NOTE] 
> 有关美国政府 DoD 终结点的完整列表，请参阅[文档](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) 

## <a name="pass-through-authentication"></a>直通身份验证 
提供以下信息以实现直通身份验证（PTA）和 Azure 政府云。

### <a name="allow-access-to-urls"></a>允许访问 URL  
在部署直通身份验证代理之前，请验证服务器与 Azure AD 之间是否存在防火墙。 如果你的防火墙或代理允许 DNS 允许列表，请添加以下连接： 
> [!NOTE]
> 以下指南也适用于为 Azure 政府版环境安装[应用程序代理连接器](https://aka.ms/whyappproxy)。

|代码 |用途|
|-----|-----| 
|*. msappproxy.us *. servicebus.usgovcloudapi.net|代理与 Azure AD 云服务之间的通信 |
|mscrl.microsoft.us:80 crl.microsoft.us:80 </br>ocsp.msocsp.us:80 www.microsoft.us:80| 代理使用这些 Url 来验证证书。| 
|login.windows.us secure.aadcdn.microsoftonline-p.com *. microsoftonline.us </br> *. microsoftonline-p.us </br>*. msauth.net </br> *. msauthimages.net </br>*. msecnd.net</br>*. msftauth.net </br>*. msftauthimages.net</br>*. phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| 代理在注册过程中使用这些 Url。| 

### <a name="install-the-agent-for-the-azure-government-cloud"></a>为 Azure 政府云安装代理 
若要为 Azure 政府版云安装代理，必须执行以下特定步骤：在命令行终端中，导航到用于安装代理的可执行文件所在的文件夹。 运行以下命令，该命令指定安装适用于 Azure 政府。 

对于直通身份验证： 
```
AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
```

对于应用程序代理：
```
AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
```

## <a name="single-sign-on"></a>单一登录 
设置 Azure AD Connect 服务器：如果使用直通身份验证作为登录方法，则无需进行其他先决条件检查。 如果使用密码哈希同步作为登录方法，并且 Azure AD Connect 和 Azure AD 之间存在防火墙，请确保：
- 使用 Azure AD Connect 版本 1.1.644.0 或更高版本。 
- 如果你的防火墙或代理允许 DNS 允许列表，则通过端口443将连接添加到 *. msapproxy.us Url。 如果不是，则允许访问每周更新的 Azure 数据中心 IP 范围。 此先决条件仅适用于启用了该功能的情况。 无需用户实际登录。 

### <a name="rolling-out-seamless-sso"></a>推出无缝 SSO 
可使用以下说明向用户逐步推出无缝 SSO。 首先，将以下 Azure AD URL 添加到 Active Directory 中的 "组策略所有或选定用户的 Intranet 区域设置：https://autologon.microsoft.us 

此外，还需要启用称为 "允许通过脚本更新状态栏" 组策略的 Intranet 区域策略设置。 浏览器注意事项 Mozilla Firefox （所有平台） Mozilla Firefox 不会自动使用 Kerberos 身份验证。 每个用户必须使用以下步骤手动将 Azure AD URL 添加到其 Firefox 设置： 
1. 运行 Firefox 并在地址栏中输入 about： config。 关闭你看到的任何通知。 
2. 搜索 "网络" "协商-身份验证-uri" 首选项。 此首选项列出了用于 Kerberos 身份验证的 Firefox 的受信任站点。 
3. 右键单击并选择 "修改"。 
4. 在https://autologon.microsoft.us字段中输入。
5. 选择 "确定"，然后重新打开浏览器。 

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>基于 Chromium 的 Microsoft Edge （所有平台） 
如果你已重写 `AuthNegotiateDelegateAllowlist` 或 `AuthServerAllowlist` 环境中的策略设置，请确保添加 Azure AD 的 URLhttps://autologon.microsoft.us) 。 

### <a name="google-chrome-all-platforms"></a>Google Chrome（所有平台） 
如果你已重写 `AuthNegotiateDelegateWhitelist` 或 `AuthServerWhitelist` 环境中的策略设置，请确保添加 Azure AD 的 URLhttps://autologon.microsoft.us) 。 

## <a name="next-steps"></a>后续步骤
[传递身份验证](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
[单一登录](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) 
