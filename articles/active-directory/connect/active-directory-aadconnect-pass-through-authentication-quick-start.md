---
title: "Azure AD 直通身份验证 - 快速入门 | Microsoft Docs"
description: "本文介绍如何开始使用 Azure Active Directory (Azure AD) 直通身份验证。"
services: active-directory
keywords: "Azure AD Connect 稀罕第身份验证, 安装 Active Directory, Azure AD 所需组件, SSO, 单一登录"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: 643937093ac04a9543ad3386fdc400a2909c1aa6
ms.contentlocale: zh-cn
ms.lasthandoff: 08/01/2017

---

# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Azure Active Directory 直通身份验证：快速入门

借助 Azure Active Directory (Azure AD) 直通身份验证证，你的用户可使用同一密码登录到本地应用程序和基于云的应用程序。 它通过直接针对本地 Active Directory 验证用户密码来登录用户。

>[!IMPORTANT]
>Azure AD 直通身份验证目前处于预览状态。 如果一直通过预览版使用此功能，应确保使用[此处](./active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md)提供的说明升级身份验证代理的预览版本。

## <a name="how-to-deploy-azure-ad-pass-through-authentication"></a>如何部署 Azure AD 直通身份验证

若要部署直通身份验证，需要按照以下说明进行操作：
1. 检查先决条件：正确设置租户和本地环境，然后再启用该功能。
2. 启用功能：在租户中打开直通身份验证，安装轻型本地代理处理密码验证请求。
3. 测试功能：使用直通身份验证测试用户登录。
4. 确保高可用性：再安装一个独立代理，为登录请求提供高可用性。

## <a name="step-1-check-prerequisites"></a>步骤 1：检查先决条件

请确保以下先决条件已准备就绪：

### <a name="on-the-azure-portal"></a>在 Azure 门户中

1. 在 Azure AD 租户中创建仅限云的全局管理员帐户。 这样一来，就可以在本地服务出现故障或不可用时管理租户的配置。 了解如何[添加仅限云的全局管理员帐户](../active-directory-users-create-azure-portal.md)。 此步骤至关重要，可确保自己不被锁定在租户外部。
2. 在 Azure AD 租户中添加一个或多个[自定义域名](../active-directory-add-domain.md)。 用户使用其中一个域名登录。

### <a name="in-your-on-premises-environment"></a>在本地环境中

1. 标识运行 Windows Server 2012 R2 或更高版本的服务器，将在其上运行 Azure AD Connect。 将该服务器添加到需进行密码验证的用户所在的同一 AD 林。
2. 在步骤 2 中标识的服务器上安装[最新版本的 Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)。 如果已在运行 Azure AD Connect，请确保其版本为 1.1.557.0 或更高版本。
3. 标识另一个运行 Windows Server 2012 R2 或更高版本的服务器，将在其上运行独立身份验证代理。 身份验证代理版本需为 1.5.193.0 或更高版本。 需要服务器来确保登录请求的高可用性。 将该服务器添加到需进行密码验证的用户所在的同一 AD 林。
4. 如果服务器和 Azure AD 之间存在防火墙，则需配置以下项：
   - 打开端口：确保服务器上安装的身份验证代理可以通过端口 **80**（用于在验证 SSL 证书时下载证书吊销列表 (CRL)）和 **443**（与服务的所有出站通信）向 Azure AD 发出出站请求。 如果防火墙根据发起用户强制实施规则，请针对来自作为网络服务运行的 Windows 服务的流量打开这些端口。
   - 允许 Azure AD 终结点：若已启用 URL 筛选，请确保身份验证代理能与 **login.windows.net** 、 **login.microsoftonline.com** 、 **\*.msappproxy.net** 和 **\*.servicebus.windows.net** 进行通信。
   - 验证直接 IP 连接：请确保服务器上的身份验证代理能够与 [Azure 数据中心 IP 范围](https://www.microsoft.com/en-us/download/details.aspx?id=41653)建立直接 IP 连接。

## <a name="step-2-enable-the-feature"></a>步骤 2：启用功能

可使用 [Azure AD Connect](active-directory-aadconnect.md) 启用直通身份验证。

>[!IMPORTANT]
>可在 Azure AD Connect 主服务器或暂存服务器上启用直通身份验证。 强烈建议从主服务器启用。

若是首次安装 Azure AD Connect，请选择[自定义安装路径](active-directory-aadconnect-get-started-custom.md)。 在“用户登录”页面，选择“直通身份验证”作为登录方法。 成功完成上述步骤后，将在 Azure AD Connect 所在的同一台服务器上安装直通身份验证代理。 此外，还会在租户中启用直通身份验证功能。

![Azure AD Connect — 用户登录](./media/active-directory-aadconnect-sso/sso3.png)

如果已安装 Azure AD Connect（使用[快速安装](active-directory-aadconnect-get-started-express.md)或[自定义安装](active-directory-aadconnect-get-started-custom.md)路径），请在 Azure AD Connect 上选择“更改用户登录页”并单击“下一步”。 然后选择“直通身份验证”作为登录方法。 成功完成上述步骤后，将在 Azure AD Connect 所在的同一台服务器上安装直通身份验证代理，并在租户中启用该功能。

![Azure AD Connect - 更改用户登录](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>直通身份验证是租户级功能。 启用此功能将影响租户内所有托管域中的用户登录方式。

## <a name="step-3-test-the-feature"></a>步骤 3：测试功能

按照这些说明验证是否已正确启用直通身份验证：

1. 使用租户的全局管理员凭据登录 [Azure 门户](https://portal.azure.com)。
2. 在左侧导航栏中，选择“Azure Active Directory”。
3. 选择“Azure AD Connect”。
4. 验证“直通身份验证”功能是否显示为“已启用”。
5. 选择“直通身份验证”。 此边栏选项卡列出了安装身份验证代理的服务器。

![Azure 门户 - Azure AD Connect 边栏选项卡](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Azure 门户 -“直通身份验证”边栏选项卡](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

在此阶段，租户中所有托管域中的用户都可以使用直通身份验证登录。 但是，联合域中的用户将继续使用 Active Directory 联合身份验证服务 (AD FS) 或以前配置的其他联合身份验证提供程序登录。 如果将联合域转换为托管域，则该域中的所有用户将自动开始使用直通身份验证登录。 仅限云的用户不受直通身份验证功能的影响。

## <a name="step-4-ensure-high-availability"></a>步骤 4： 确保高可用性

如果计划在生产环境中部署直通身份验证，则应安装独立身份验证代理。 在另一个服务器上（而非运行 Azure AD Connect 和第一个身份验证代理的服务器）安装第二个身份验证代理。 此设置将提供登录请求的高可用性。 请按照以下说明部署独立身份验证代理：

1. **下载最新版本的身份验证代理（版本 1.5.193.0 或更高版本）**：使用租户的全局管理员凭据登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧导航栏中，选择“Azure Active Directory”。
3. 依次选择 **Azure AD Connect**、“直通身份验证”。 然后选择“下载代理”。
4. 单击“接受条款并下载”按钮。
5. **安装最新版本的身份验证代理**：运行在步骤 4 中下载的可执行文件。 出现提示时，提供租户的全局管理员凭据。

![Azure 门户 -“下载身份验证代理”按钮](./media/active-directory-aadconnect-pass-through-authentication/pta9.png)

![Azure 门户 -“下载代理”边栏选项卡](./media/active-directory-aadconnect-pass-through-authentication/pta10.png)

## <a name="next-steps"></a>后续步骤
- [**当前限制**](active-directory-aadconnect-pass-through-authentication-current-limitations.md) - 此功能目前处于预览状态。 了解支持和不支持的方案。
- [**深入技术探究**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) - 了解此功能的工作原理。
- [**常见问题**](active-directory-aadconnect-pass-through-authentication-faq.md) - 常见问题解答。
- [**故障排除**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) - 了解如何解决使用此功能时遇到的常见问题。
- [**Azure AD 无缝 SSO**](active-directory-aadconnect-sso.md) - 深入了解此补充功能。
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 用于填写新功能请求。

