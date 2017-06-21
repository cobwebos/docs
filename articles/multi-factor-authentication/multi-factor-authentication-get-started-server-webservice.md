---
title: "Azure MFA 服务器移动应用 Web 服务 | Microsoft Docs"
description: "Microsoft Authenticator 应用提供一个附加的带外身份验证选项。  它可以让 MFA 服务器对用户使用推送通知。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 6c8d6fcc-70f4-4da4-9610-c76d66635b8b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/15/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: H1Hack27Feb2017,it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 20afeb3ba290ddf728d2b52c076c7a57fadc77c6
ms.openlocfilehash: 4014bf0217e25ea9bc8473ef2383279e5eb79b87
ms.contentlocale: zh-cn
ms.lasthandoff: 02/28/2017

---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>使用 Azure 多重身份验证服务器启用移动应用身份验证

Microsoft Authenticator 应用提供一个附加的带外验证选项。 Azure 多重身份验证会将通知推送到用户智能手机或平板电脑上的 Microsoft Authenticator 应用中，而不是在用户登录期间对用户进行自动电话呼叫或向其发送短信。 用户只需在该应用中点击“验证”（或输入 PIN 码并点击“身份验证”）即可完成登录。

在电话信号不佳导致接收存在问题时，建议使用移动应用进行双重验证。 将应用用作 OATH 令牌生成器时，它不需要任何网络或 Internet 连接。

在除 Azure 多重身份验证服务器以外的其他服务器上安装用户门户需要完成以下步骤：

1. 安装 Web 服务 SDK
2. 安装移动应用 Web 服务
3. 在 Azure Multi-Factor Authentication 服务器中配置移动应用设置
4. 为最终用户激活 Microsoft Authenticator 应用

## <a name="requirements"></a>要求

若要使用 Microsoft Authenticator 应用，需要满足以下条件，才能使该应用成功地与移动应用 Web 服务进行通信：

* Azure 多重身份验证服务器版本 v6.0 或更高版本
* 在运行 Microsoft® [Internet Information Services (IIS) IIS 7.x 或更高版本](http://www.iis.net/)的面向 Internet 的 Web 服务器上安装移动应用 Web 服务
* 已安装和注册 ASP.NET v4.0.30319，并将其设置为“允许”
* 必需的角色服务包括 ASP.NET 和 IIS 6 元数据库兼容性
* 可通过公共 URL 访问移动应用 Web 服务
* 使用 SSL 证书保护移动应用 Web 服务。
* 在安装 Azure 多重身份验证服务器的同一服务器上，在 IIS 7 或更高版本中安装 Azure 多重身份验证 Web 服务 SDK
* 使用 SSL 证书保护 Azure 多重身份验证 Web 服务 SDK。
* 移动应用 Web 服务可以通过 SSL 连接到 Azure 多重身份验证 Web 服务 SDK
* 移动应用 Web 服务可以使用属于“PhoneFactor 管理员”安全组的服务帐户凭据，对 Azure MFA Web 服务 SDK 进行身份验证。 如果 Azure 多重身份验证服务器在已加入域的服务器上，则此服务帐户和组存在于 Active Directory 中。 如果 Azure Multi-Factor Authentication 服务器未加入域，则此服务帐户和组存在于本地的该服务器上。


## <a name="install-the-web-service-sdk"></a>安装 Web 服务 SDK
如果 Azure 多重身份验证 (MFA) 服务器上未安装 Azure 多重身份验证 Web 服务 SDK，请转到该服务器并打开 Azure MFA 服务器。

1. 单击“Web 服务 SDK”图标。
2. 单击“安装 Web 服务 SDK”并遵照显示的说明进行操作。

必须使用 SSL 证书保护 Web 服务 SDK。 也可以使用自签名证书实现此目的。 将该证书导入到用户门户 Web 服务器上的本地计算机帐户的“受信任的根证书颁发机构”存储中，使该服务器在发起 SSL 连接时将信任该证书。

![设置](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)

## <a name="install-the-mobile-app-web-service"></a>安装移动应用 Web 服务
安装移动应用 Web 服务之前，请注意以下事项：

* 如果 Azure MFA 用户门户已安装在面向 Internet 的服务器上，则可以从用户门户的 web.config 文件中复制用户名、密码和 Web 服务 SDK 的 URL。
* 在面向 Internet 的 Web 服务器上打开 Ｗeb 浏览器，并导航到已输入到 web.config 文件中的 Web 服务 SDK 的 URL，这会很有用。 如果浏览器可以成功访问 Web 服务，它应提示你输入凭据。 输入已输入到 web.config 文件中的用户名和密码（与文件中显示的完全相同）。 确保未显示证书警告或错误。
* 如果反向代理或防火墙位于移动应用 Web 服务 Web 服务器的前端，并正在执行 SSL 卸载，则可以编辑移动应用 Web 服务 web.config 文件，使移动应用 Web 服务可以使用 http 而非 https。 从移动应用到防火墙/反向代理仍然需要 SSL。 将以下密钥添加到 \<appSettings\> 部分：

        <add key="SSL_REQUIRED" value="false"/>

### <a name="install-the-service"></a>安装服务

1. 在 Azure 多重身份验证服务器上打开 Windows 资源管理器并导航到 Azure MFA 服务器安装在其中的文件夹（通常是 C:\Program Files\Azure Multi-Factor Authentication）。 选择 32 位或 64 位版本的 Azure Multi-Factor AuthenticationPhoneAppWebServiceSetup 安装文件。 将安装文件复制到面向 Internet 的服务器。

2. 在面向 Internet 的 Web 服务器上，使用管理员权限运行安装文件。 以管理员身份打开命令提示符，并导航到安装文件所复制到的位置。

3. 运行 Multi-Factor AuthenticationMobileAppWebServiceSetup 安装文件，根据需要更改站点，并将虚拟目录更改为短名称，如“PA”。

  建议使用较短的虚拟目录名称，因为在激活期间用户必须在移动设备中输入移动应用 Web 服务 URL。

4. 完成 Azure Multi-Factor AuthenticationMobileAppWebServiceSetup 的安装后，请浏览到 C:\inetpub\wwwroot\PA（或基于虚拟目录名称的相应目录）并编辑 web.config 文件。

5. 找到 WEB_SERVICE_SDK_AUTHENTICATION_USERNAME 和 WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD 密钥。 将值设置为属于 PhoneFactor 管理员安全组的服务帐户的用户名和密码。 这可能是用作 Azure 多重身份验证用户门户标识的帐户（如果之前已安装）。 请务必在行末引号之间输入用户名和密码 (value=""/>)。 使用限定的用户名（例如“域\用户名”或“计算机\用户名”）。  

6. 找到 pfMobile App Web Service_pfwssdk_PfWsSdk 设置。 将值从 *http://localhost:4898/PfWsSdk.asmx* 更改为在 Azure 多重身份验证服务器上运行的 Web 服务 SDK 的 URL（例如 https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx）。

  由于为此连接使用的是 SSL，因此必须按服务器名称（而非 IP 地址）引用 Web 服务 SDK。 应已为服务器名称颁发了 SSL 证书，并且使用的 URL 必须与证书上的名称相匹配。 服务器名称可能无法解析为面向 Internet 的服务器的 IP 地址。 如果出现这种情况，请在该服务器上的 hosts 文件中添加一个条目，以将 Azure 多重身份验证服务器的名称映射到其 IP 地址。 进行更改之后，保存 web.config 文件。

7. 如果已安装移动应用 Web 服务的网站尚未与公开签名的证书绑定，请在服务器上安装证书，打开 IIS 管理器并将证书绑定到该网站。

8. 从任何计算机打开 Web 浏览器，然后导航到已安装移动应用 Web 服务的 URL（例如 https://www.publicwebsite.com/PA）。 确保未显示证书警告或错误。

### <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>在 Azure Multi-Factor Authentication 服务器中配置移动应用设置
安装移动应用 Web 服务后，你需要配置 Azure Multi-Factor Authentication 服务器，使其能够与门户配合工作。

1. 在 Azure MFA 服务器中，单击“用户门户”图标。 如果允许用户控制其身份验证方法，请在“设置”选项卡上的“允许用户选择方法”下，选中“移动应用”。 如果未启用此功能，则最终用户将需要联系技术支持才能完成移动应用的激活。
2. 选中“允许用户激活移动应用”框。
3. 选中“允许用户注册”框。
4. 单击“移动应用”图标。
5. 输入用于在安装 Azure Multi-Factor AuthenticationMobileAppWebServiceSetup 时创建的虚拟目录的 URL。 可以在提供的空白处输入帐户名称。 此公司名称将显示在移动应用程序中。 如果留空，则将显示在 Azure 经典门户中创建的多重身份验证提供程序的名称。

<center>![设置](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>

