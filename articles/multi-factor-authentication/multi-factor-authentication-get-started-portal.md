---
title: "Azure MFA 服务器的用户门户 | Microsoft 文档"
description: "这是与 Azure Multi-Factor Authentication 相关的页面，介绍如何开始使用 Azure MFA 和用户门户。"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 06b419fa-3507-4980-96a4-d2e3960e1772
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 15f4ef7b70027a820dc50a9399891bbab77d7e12
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="user-portal-for-the-azure-multi-factor-authentication-server"></a>适用于 Azure 多重身份验证服务器的用户门户

用户门户是一个 IIS 网站，可让用户注册 Azure 多重身份验证 (MFA) 以及维护其帐户。 用户可以在下次登录期间更改其电话号码、更改其 PIN 码或选择绕过双重验证。

用户可使用其普通用户名和密码登录到用户门户，并根据电话呼叫完成双重验证或回答安全问题，完成其身份验证。 如果允许用户注册，用户可在首次登录到用户门户时配置其电话号码和 PIN 码。

可以设置用户门户管理员并向其授予添加新用户和更新现有用户的权限。

根据环境情况，可能需要将用户门户部署在 Azure 多重身份验证服务器所在的服务器上，或者部署在其他面向 Internet 的服务器上。

![MFA 用户门户](./media/multi-factor-authentication-get-started-portal/portal.png)

> [!NOTE]
> 用户门户仅适用于多重身份验证服务器。 如果在云中使用多重身份验证，则让用户参阅[设置双重验证帐户](./end-user/multi-factor-authentication-end-user-first-time.md)或[管理双重验证设置](./end-user/multi-factor-authentication-end-user-manage-settings.md)。

## <a name="install-the-web-service-sdk"></a>安装 Web 服务 SDK

不管什么方案，如果 Azure 多重身份验证 (MFA) 服务器上尚未安装 Azure 多重身份验证 Web 服务 SDK，请完成以下步骤。

1. 打开多重身份验证服务器控制台。
2. 转到“Web 服务 SDK”，选择“安装 Web 服务 SDK”。
3. 使用默认设置完成安装，除非因某种原因而需要更改它们。
4. 将 SSL 证书绑定到 IIS 中的站点。

如果对如何在 IIS 服务器上配置 SSL 证书存在疑问，请参阅 [How to Set Up SSL on IIS](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)（如何在 IIS 上设置 SSL）一文。

必须使用 SSL 证书保护 Web 服务 SDK。 也可以使用自签名证书实现此目的。 将该证书导入到用户门户 Web 服务器上的本地计算机帐户的“受信任的根证书颁发机构”存储中，使该服务器在发起 SSL 连接时信任该证书。

![MFA 服务器配置设置 Web 服务 SDK](./media/multi-factor-authentication-get-started-portal/sdk.png)

## <a name="deploy-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>在与 Azure 多重身份验证服务器所在的同一台服务器上部署用户门户

若要在 Azure 多重身份验证服务器所在的同一台服务器上安装用户门户，必须满足以下先决条件：

* 安装 IIS，包括 ASP.NET 和 IIS 6 元数据库兼容性（适用于 IIS 7 或更高版本）
* 具有计算机和域的管理员权限的帐户（如果适用）。 帐户需有创建 Active Directory 安全组的权限。
* 请使用 SSL 证书保护用户门户。
* 请使用 SSL 证书保护 Azure 多重身份验证 Web 服务 SDK。

若要部署该用户门户，请执行以下步骤：

1. 打开 Azure 多重身份验证服务器控制台，单击左侧菜单中的“用户门户”图标，然后单击“安装用户门户”。
2. 使用默认设置完成安装，除非因某种原因而需要更改它们。
3. 将 SSL 证书绑定到 IIS 中的站点。

   > [!NOTE]
   > 此 SSL 证书通常是公开签名的 SSL 证书。

4. 从任何计算机打开 Web 浏览器，并导航到安装用户门户的 URL（示例：https://mfa.contoso.com/MultiFactorAuth）。 确保未显示证书警告或错误。

![MFA 服务器用户门户安装](./media/multi-factor-authentication-get-started-portal/install.png)

如果对如何在 IIS 服务器上配置 SSL 证书存在疑问，请参阅 [How to Set Up SSL on IIS](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)（如何在 IIS 上设置 SSL）一文。

## <a name="deploy-the-user-portal-on-a-separate-server"></a>在单独的服务器上部署用户门户

如果运行 Azure 多重身份验证服务器的服务器不是面向 Internet 的，则应将用户门户安装在单独的面向 Internet 的服务器上。

如果组织使用 Microsoft Authenticator 应用作为验证方法之一，并且需要将用户门户部署在其自己的服务器上，则请完成下述要求的操作：

* 使用 v6.0 或更高版本的 Azure 多重身份验证服务器。
* 将用户门户安装在面向 Internet 的 Web 服务器上，该服务器运行 Microsoft Internet Information Services (IIS) 6.x 或更高版本。
* 使用 IIS 6.x 时，请确保 ASP.NET v2.0.50727 已安装、注册并设置为“允许”。
* 使用 IIS 7.x 或更高版本时，请完成 IIS 的安装，包括基本身份验证、ASP.NET 以及 IIS 6 元数据库兼容性。
* 请使用 SSL 证书保护用户门户。
* 请使用 SSL 证书保护 Azure 多重身份验证 Web 服务 SDK。
* 请确保用户门户能够通过 SSL 连接到 Azure 多重身份验证 Web 服务 SDK。
* 请确保用户门户能够使用“PhoneFactor 管理员”安全组中某个服务帐户的凭据，在 Azure 多重身份验证 Web 服务 SDK 中进行身份验证。 如果 Azure 多重身份验证服务器运行在已加入域的服务器上，则此服务帐户和组应存在于 Active Directory 中。 如果 Azure 多重身份验证服务器未加入域，则此服务帐户和组存在于本地的该服务器上。

在除 Azure 多重身份验证服务器以外的其他服务器上安装用户门户需要完成以下步骤：

1. **在 MFA 服务器上**，浏览到安装路径（示例：C:\Program Files\Multi-Factor Authentication Server），将文件 MultiFactorAuthenticationUserPortalSetup64 复制到允许面向 Internet 的服务器访问的位置，以便在该服务器中安装此文件。
2. 在面向 Internet 的 Web 服务器上，以管理员身份运行 MultiFactorAuthenticationUserPortalSetup64 安装文件，根据需要更改站点并将虚拟目录更改为短名称。
3. 将 SSL 证书绑定到 IIS 中的站点。

   > [!NOTE]
   > 此 SSL 证书通常是公开签名的 SSL 证书。

4. 浏览到 C:\inetpub\wwwroot\MultiFactorAuth
5. 在记事本中编辑 Web.Config 文件

    * 找到项 "USE_WEB_SERVICE_SDK"，将 value="false" 更改为 value="true"
    * 找到项 "WEB_SERVICE_SDK_AUTHENTICATION_USERNAME"，将 value="" 更改为 value="DOMAIN\User"，其中，DOMAIN\User 是属于“PhoneFactor Admins”组的服务帐户。
    * 找到项 "WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD"，将 value="" 更改为 value="Password"，其中，Password 是在上一行中输入的服务帐户的密码。
    * 找到值 https://www.contoso.com/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx，将此占位符 URL 更改为在步骤 2 中安装的 Web 服务 SDK URL。
    * 保存 Web.Config 文件并关闭记事本。

6. 从任何计算机打开 Web 浏览器，并导航到安装用户门户的 URL（示例：https://mfa.contoso.com/MultiFactorAuth）。 确保未显示证书警告或错误。

如果对如何在 IIS 服务器上配置 SSL 证书存在疑问，请参阅 [How to Set Up SSL on IIS](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)（如何在 IIS 上设置 SSL）一文。

## <a name="configure-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>在 Azure 多重身份验证服务器中配置用户门户设置

安装用户门户后，需要配置 Azure 多重身份验证服务器，使其能够与 Azure 门户配合工作。

1. 在 Azure 多重身份验证服务器控制台中，单击“用户门户”图标。 在“设置”选项卡上的“用户门户 URL”文本框中，输入用户门户的 URL。 如果已启用电子邮件功能，则会将此 URL 添加到电子邮件中，再将该电子邮件发送给导入到 Azure 多重身份验证服务器的用户。
2. 选择要在用户门户中使用的设置。 例如，如果允许用户选择其身份验证方法，请确保选中“允许用户选择方法”以及这些用户可以从中选择的方法。
3. 在“管理员”选项卡上定义谁应该是管理员。可以在“添加/编辑”框中使用复选框和下拉列表创建精确的管理权限。

可选配置：
- 安全性问题 - 针对环境定义核准的安全性问题，并定义显示这些问题的语言。
- 已传递的会话 - 使用 MFA 配置用户门户与基于表单的网站的集成。
- 受信任的 IP - 允许用户在通过受信任的 IP 或范围的列表进行身份验证时，跳过 MFA。

![MFA 服务器用户门户配置](./media/multi-factor-authentication-get-started-portal/config.png)

Azure 多重身份验证服务器为用户门户提供了多个选项。 下表列出了这些选项及其用途的说明。

| 用户门户设置 | 说明 |
|:--- |:--- |
| 用户门户 URL | 输入托管门户的 URL。 |
| 主要身份验证 | 指定在登录门户时要使用的身份验证类型。 Windows、Radius 或 LDAP 身份验证。 |
| 允许用户登录 | 允许用户在用户门户的登录页中输入用户名和密码。 如果未选择此选项，框会灰显。 |
| 允许用户注册 | 将用户定向到设置屏幕并提示输入其他信息（例如电话号码），使用户能够注册多重身份验证。 备用电话的提示可让用户指定备用电话号码。 第三方 OATH 令牌的提示可让用户指定第三方 OATH 令牌。 |
| 允许用户启动一次性跳过 | 允许用户启动一次性跳过。 如果用户设置此选项，该设置会在下次登录时生效。 出现跳过秒数的提示时，会显示一个框让用户更改默认的 300 秒。 否则，一次性跳过只生效 300 秒。 |
| 允许用户选择方法 | 允许用户指定其主要联系方法。 此方法可以是电话呼叫、短信、移动应用或 OATH 令牌。 |
| 允许用户选择语言 | 允许用户更改通话、短信、移动应用或 OATH 令牌使用的语言。 |
| 允许用户激活移动应用 | 允许用户生成激活代码，以完成服务器使用的移动应用激活过程。  还可以设置允许用户在其上激活该应用的设备数（1 到 10 个）。 |
| 使用安全提问进行回退 | 在双重验证失败的情况下允许回答安全提问。 可以指定必须正确回答的安全提问数。 |
| 允许用户关联第三方 OATH 令牌 | 允许用户指定第三方 OATH 令牌。 |
| 使用 OATH 令牌进行回退 | 在双重验证失败的情况下允许使用 OATH 令牌。 也可以指定会话超时（以分钟为单位）。 |
| 启用日志记录 | 在用户门户上启用日志记录。 日志文件位于：C:\Program Files\Multi-Factor Authentication Server\Logs。 |

启用这些设置后，当用户登录用户门户时，就会看到这些设置。

![用户门户设置](./media/multi-factor-authentication-get-started-portal/portalsettings.png)

### <a name="self-service-user-enrollment"></a>自助用户注册

如果要让用户登录并注册，必须在“设置”选项卡下面选择“允许用户登录”和“允许用户注册”选项。请记住，选择的设置会影响用户的登录体验。

例如，当用户首次登录到用户门户时，会转到“Azure 多重身份验证用户设置”页。 根据 Azure 多重身份验证的配置方式，用户也许能够选择身份验证方法。

如果他们选择“语音呼叫”验证方法，或已预先配置为使用该方法，此页会提示用户输入主要电话号码和分机（如果有）。 他们还可以输入备用电话号码。

![注册主要的和备用的电话号码](./media/multi-factor-authentication-get-started-portal/backupphone.png)

如果要求用户在身份验证时使用 PIN 码，此页会提示他们创建 PIN 码。 在输入其电话号码和 PIN 码（如果适用）之后，用户可单击“立即打电话给我进行身份验证”按钮。 Azure 多重身份验证通过用户的主电话号码执行电话呼叫验证。 用户必须接听电话并输入其 PIN 码（如果适用），并按 # 转到自我注册过程的下一步。

如果用户选择“短信”验证方法或已预先配置为使用该方法，则该页会提示用户输入其手机号码。 如果要求用户在身份验证时使用 PIN 码，此页还会提示他们输入 PIN 码。  在输入其电话号码和 PIN 码（如果适用）之后，用户可单击“立即发短信给我进行身份验证”按钮。 Azure 多重身份验证通过用户的手机执行短信验证。 用户将收到包含一次性密码 (OTP) 的短信，然后应使用该 OTP 和 PIN 码（如果适用）回复该短信。

![用户门户 SMS](./media/multi-factor-authentication-get-started-portal/text.png)

如果用户选择“移动应用”验证方法，该页会提示用户在其设备上安装 Microsoft Authenticator 应用并生成激活代码。 安装该应用后，用户可单击“生成激活代码”按钮。

> [!NOTE]
> 若要使用 Microsoft 验证器应用，用户必须为其设备启用推送通知。

然后，该页会显示激活代码和 URL 以及条形码图片。 如果要求用户在身份验证时使用 PIN 码，此页还会提示他们输入 PIN 码。 用户将激活代码和 URL 输入到 Microsoft 验证器应用中，或使用条形码扫描仪扫描条形码图片，然后单击“激活”按钮。

完成激活后，用户可单击“立即对我进行身份验证”按钮。 Azure 多重身份验证通过用户的移动应用执行验证。 用户必须输入其 PIN 码（如果适用），并按其移动应用中的“身份验证”按钮，以转到自我注册过程的下一步。

如果管理员已将 Azure 多重身份验证服务器配置为收集安全提问和答案，则用户将转到“安全提问”页。 用户必须选择四个安全问题，并提供其所选问题的答案。

![用户门户安全提问](./media/multi-factor-authentication-get-started-portal/secq.png)

现在，用户自我注册已完成，用户可登录到用户门户。 用户在以后可随时重新登录到用户门户，以更改其电话号码、PIN 码、身份验证方法和安全问题（如果更改其方法受管理员允许）。

## <a name="next-steps"></a>后续步骤

- [部署 Azure 多重身份验证服务器移动应用 Web 服务](multi-factor-authentication-get-started-server-webservice.md)