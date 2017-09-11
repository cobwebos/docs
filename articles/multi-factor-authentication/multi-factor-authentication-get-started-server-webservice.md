---
title: "Azure MFA 服务器移动应用 Web 服务 | Microsoft Docs"
description: "Microsoft Authenticator 应用提供一个附加的带外身份验证选项。  它可以让 MFA 服务器对用户使用推送通知。"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 6c8d6fcc-70f4-4da4-9610-c76d66635b8b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: joflore
ms.reviewer: alexwe
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: bf758d1241f2a56eba4d5c92ace713d6e563df65
ms.contentlocale: zh-cn
ms.lasthandoff: 08/25/2017

---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>使用 Azure 多重身份验证服务器启用移动应用身份验证

Microsoft Authenticator 应用提供一个附加的带外验证选项。 Azure 多重身份验证会将通知推送到用户智能手机或平板电脑上的 Microsoft Authenticator 应用中，而不是在用户登录期间对用户进行自动电话呼叫或向其发送短信。 用户只需在该应用中点击“验证”（或输入 PIN 码并点击“身份验证”）即可完成登录。

在电话信号不佳导致接收存在问题时，建议使用移动应用进行双重验证。 将应用用作 OATH 令牌生成器时，它不需要任何网络或 Internet 连接。

根据环境情况，可能需要将移动应用 Web 服务部署在 Azure 多重身份验证服务器所在的服务器上，或者部署在其他面向 Internet 的服务器上。

## <a name="requirements"></a>要求

若要使用 Microsoft Authenticator 应用，需要满足以下条件，才能使该应用成功地与移动应用 Web 服务进行通信：

* Azure 多重身份验证服务器版本 v6.0 或更高版本
* 在运行 Microsoft® [Internet Information Services (IIS) IIS 7.x 或更高版本](http://www.iis.net/)的面向 Internet 的 Web 服务器上安装移动应用 Web 服务
* 已安装和注册 ASP.NET v4.0.30319，并将其设置为“允许”
* 必需的角色服务包括 ASP.NET 和 IIS 6 元数据库兼容性
* 可通过公共 URL 访问移动应用 Web 服务
* 使用 SSL 证书保护移动应用 Web 服务。
* 将 IIS 7 或更高版本中的 Azure 多重身份验证 Web 服务 SDK 安装在Azure 多重身份验证服务器所在的服务器上
* 使用 SSL 证书保护 Azure 多重身份验证 Web 服务 SDK。
* 移动应用 Web 服务可以通过 SSL 连接到 Azure 多重身份验证 Web 服务 SDK
* 移动应用 Web 服务可以使用属于“PhoneFactor 管理员”安全组的服务帐户凭据，对 Azure MFA Web 服务 SDK 进行身份验证。 如果 Azure 多重身份验证服务器在已加入域的服务器上，则此服务帐户和组存在于 Active Directory 中。 如果 Azure Multi-Factor Authentication 服务器未加入域，则此服务帐户和组存在于本地的该服务器上。

## <a name="install-the-mobile-app-web-service"></a>安装移动应用 Web 服务

安装移动应用 Web 服务之前，请注意以下事项：

* 需要一个属于“PhoneFactor Admins”组的服务帐户。 此帐户可以是用于“用户门户”安装的帐户。
* 在面向 Internet 的 Web 服务器上打开 Ｗeb 浏览器，并导航到已输入到 web.config 文件中的 Web 服务 SDK 的 URL，这会很有用。 如果浏览器可以成功访问 Web 服务，它应提示输入凭据。 输入已输入到 web.config 文件中的用户名和密码（与文件中显示的完全相同）。 确保未显示证书警告或错误。
* 如果反向代理或防火墙位于移动应用 Web 服务 Web 服务器的前端，并正在执行 SSL 卸载，则可以编辑移动应用 Web 服务 web.config 文件，使移动应用 Web 服务可以使用 http 而非 https。 从移动应用到防火墙/反向代理仍然需要 SSL。 将以下密钥添加到 \<appSettings\> 部分：

        <add key="SSL_REQUIRED" value="false"/>

### <a name="install-the-web-service-sdk"></a>安装 Web 服务 SDK

不管什么方案，如果 Azure 多重身份验证 (MFA) 服务器上尚未安装 Azure 多重身份验证 Web 服务 SDK，请完成以下步骤。

1. 打开多重身份验证服务器控制台。
2. 转到“Web 服务 SDK”，选择“安装 Web 服务 SDK”。
3. 使用默认设置完成安装，除非因某种原因而需要更改它们。
4. 将 SSL 证书绑定到 IIS 中的站点。

如果对如何在 IIS 服务器上配置 SSL 证书存在疑问，请参阅 [How to Set Up SSL on IIS](https://docs.microsoft.com/en-us/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)（如何在 IIS 上设置 SSL）一文。

必须使用 SSL 证书保护 Web 服务 SDK。 也可以使用自签名证书实现此目的。 将该证书导入到用户门户 Web 服务器上的本地计算机帐户的“受信任的根证书颁发机构”存储中，使该服务器在发起 SSL 连接时信任该证书。

![MFA 服务器配置设置 Web 服务 SDK](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)

### <a name="install-the-service"></a>安装服务

1. 在 MFA 服务器上，浏览到安装路径。
2. 导航到安装 Azure MFA 服务器的文件夹，默认为 C:\Program Files\Azure Multi-Factor Authentication。
3. 找到安装文件 MultiFactorAuthenticationMobileAppWebServiceSetup64。 如果服务器不是面向 Internet 的，请将安装文件复制到面向 Internet 的服务器。
4. 如果 MFA 服务器不是面向 Internet 的，请切换到面向 Internet 的服务器。
5. 以管理员身份运行 MultiFactorAuthenticationMobileAppWebServiceSetup64 安装文件，根据需要更改站点并将虚拟目录更改为短名称。
6. 完成安装后，请浏览到 C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService（或基于虚拟目录名称的相应目录）并编辑 Web.Config 文件。

   * 找到项 "WEB_SERVICE_SDK_AUTHENTICATION_USERNAME"，将 value="" 更改为 value="DOMAIN\User"，其中，DOMAIN\User 是属于“PhoneFactor Admins”组的服务帐户。
   * 找到项 "WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD"，将 value="" 更改为 value="Password"，其中，Password 是在上一行中输入的服务帐户的密码。
   * 找到 pfMobile App Web Service_pfwssdk_PfWsSdk 设置，将值从 http://localhost:4898/PfWsSdk.asmx 更改为 Web 服务 SDK URL（示例：https://mfa.contoso.com/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx）。
   * 保存 Web.Config 文件并关闭记事本。

   > [!NOTE]
   > 由于为此连接使用了 SSL，因此必须按完全限定的域名 (FQDN)而非 IP 地址引用 Web 服务 SDK。 应已为 FQDN 颁发了 SSL 证书，并且使用的 URL 必须与证书上的名称匹配。

7. 如果已安装移动应用 Web 服务的网站尚未与公开签名的证书绑定，请在服务器上安装证书，打开 IIS 管理器并将证书绑定到该网站。
8. 从任何计算机打开 Web 浏览器，导航到已安装移动应用 Web 服务的 URL（示例：https://mfa.contoso.com/MultiFactorAuthMobileAppWebService）。 确保未显示证书警告或错误。

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>在 Azure Multi-Factor Authentication 服务器中配置移动应用设置

安装移动应用 Web 服务后，需要配置 Azure Multi-Factor Authentication 服务器，使其能够与门户配合工作。

1. 在多重身份验证服务器控制台中，单击“用户门户”图标。 如果允许用户控制其身份验证方法，请在“设置”选项卡上的“允许用户选择方法”下，选中“移动应用”。 如果未启用此功能，则最终用户需要联系支持人员才能完成移动应用的激活。
2. 选中“允许用户激活移动应用”框。
3. 选中“允许用户注册”框。
4. 单击“移动应用”图标。
5. 在字段“移动应用 Web 服务 URL:”中输入要与虚拟目录配合使用的 URL，该虚拟目录是在安装 MultiFactorAuthenticationMobileAppWebServiceSetup64 时创建的（示例：https://mfa.contoso.com/MultiFactorAuthMobileAppWebService/）。
6. 在“帐户名称”字段中填充要在此帐户的移动应用程序中显示的公司或组织名称。
   ![MFA 服务器配置移动应用设置](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)

## <a name="next-steps"></a>后续步骤

- [使用 Azure 多重身份验证与第三方 VPN 的高级方案](multi-factor-authentication-advanced-vpn-configurations.md)。
