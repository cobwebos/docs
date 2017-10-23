---
title: "Azure Active Directory 传递身份验证安全性深入研究 | Microsoft Docs"
description: "本文介绍 Azure Active Directory (Azure AD) 传递身份分验证如何保护本地帐户。"
services: active-directory
keywords: "Azure AD Connect 传递身份验证, 安装 Active Directory, Azure AD 所需的组件, SSO, 单一登录"
documentationcenter: 
author: swkrish
manager: femila
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: billmath
ms.openlocfilehash: 7a886cdb0c36008bdb66592a8d3428889739627e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Azure Active Directory 传递身份验证安全性深入研究

本文将更加详细地介绍传递身份验证的工作原理。 它更注重此功能的安全性方面。 本主题对以下人员有益：中小型组织或大型企业的安全管理员、IT 管理员、首席合规官、首席安全官以及负责 IT 安全性和符合性的其他 IT 专业人士。

论述的主题包括：
- 关于如何安装和注册身份验证代理的详细技术信息。
- 关于用户登录期间密码加密的详细技术信息。
- 本地身份验证代理和 Azure Active Directory (Azure AD) 之间通道的安全性。
- 关于如何保持身份验证代理操作安全的详细技术信息。
- 其他安全性相关主题。

## <a name="key-security-capabilities"></a>主要安全功能

此功能的安全性主要有以下方面：
- 它构建于安全的多租户体系结构上，此体系结构隔离租户之间的登录请求。
- 本地密码永远不会以任何形式存储在云中。
- 本地身份验证代理侦听和响应密码验证请求，它仅从网络内部建立出站连接。 无需在外围网络 (DMZ) 中安装这些身份验证代理。
- 从身份验证代理到 Azure AD 的出站通信仅使用标准端口（80 和 443）。 防火墙上无需打开任何入站端口。 
  - 端口 443 用于所有经过身份验证的出站通信
  - 端口 80 仅用于下载证书吊销列表 (CRL)，以确保未吊销此功能所用的任何证书。
  - 有关网络要求的完整列表，请参阅[此处](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-prerequisites)。
- 用户在登录期间提供的密码将在云中加密，再由本地身份验证代码接受，以对 Active Directory 进行验证。
- 通过相互验证保护 Azure AD 和本地身份验证代理之间的 HTTPS 通道。
- 它与条件访问策略（包括多重身份验证）、标识保护和智能锁定等 Azure AD 云保护功能无缝集成。

## <a name="components-involved"></a>涉及的组件

有关 Azure AD 操作、服务和数据安全性的常规详细信息，请参阅[信任中心](https://azure.microsoft.com/support/trust-center/)。 使用传递身份验证实现用户登录时，会涉及以下组件：
- Azure AD STS：一项无状态安全令牌服务 (STS)，它根据需要处理登录请求，并向用户的浏览器、客户端或服务发布安全令牌。
- Azure 服务总线：可在云端使用企业消息传递进行通信，也可采用中继进行通信，帮助将本地解决方案与云端连接。
- Azure AD Connect 身份验证代理（身份验证代理）：用于侦听和响应密码验证请求的本地组件。
- Azure SQL 数据库：保存有关租户身份验证代理的信息，包括其元数据和加密密钥。
- Active Directory (AD)：用于存储用户帐户（及其密码）的本地 Active Directory。

## <a name="installation-and-registration-of-authentication-agents"></a>安装和注册身份验证代理

[通过 Azure AD Connect 启用传递身份验证](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature)或[添加其他身份验证代理以确保登录请求的高可用性](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability)时，将安装身份验证代理并将其注册到 Azure AD。 启用身份验证代理涉及三个主要阶段：

- 安装身份验证代理
- 注册身份验证代理
- 初始化身份验证代理

以下主题将详细介绍其中每个阶段。

### <a name="authentication-agent-installation"></a>安装身份验证代理

仅全局管理员可在本地服务器上安装身份验证代理（使用 Azure AD Connect 或独立安装）。 安装后，“控制窗格”->“程序”->“程序和功能”列表中将显示以下两个新条目：
- 身份验证代理应用程序本身。 它使用[网络服务](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx)权限运行。
- 用于自动更新身份验证代理的更新程序应用程序。 它使用[本地系统](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx)权限运行。


### <a name="authentication-agent-registration"></a>注册身份验证代理

安装身份验证代理后，它需要将自身注册到 Azure AD。 Azure AD 通过为每个身份验证代理分配一个唯一数字标识证书来实现这一点，该证书可用于与 Azure AD 进行安全通信。

注册过程还会将身份验证代理与你的租户绑定，从而告知 Azure AD，此特定身份验证代理是经授权处理租户密码验证请求的唯一代理。 注册每个新身份验证代理，都要重复此过程。

下面介绍身份验证代理如何将自身注册到 Azure AD：

![代理注册](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta1.png)

1. Azure AD 首先请求全局管理员使用其凭据登录到 Azure AD。 登录期间，身份验证代理获取一个访问令牌，它可以代表全局管理员使用该令牌。
2. 然后，身份验证代理生成一个密钥对：一个公钥和一个私钥。
    - 此密钥对采用标准 RSA 2048 位加密而生成。
    - 私钥始终位于安装了身份验证代理的本地服务器上。
3.  身份验证代理通过 HTTPS 向 Azure AD 发出“注册”请求，此请求中包含以下组件：
    - 步骤 1 中获取的访问令牌。
    - 步骤 2 中生成的公钥。
    - 证书签名请求（CSR 或证书请求）。 这用于申请数字标识证书，其中 Azure AD 作为其证书颁发机构。
4. Azure AD 验证注册请求中的访问令牌，确认该请求来自全局管理员。
5. 然后，Azure AD 对数字标识证书签名，并将其发布回身份验证代理。
    - 证书使用 Azure AD 的根证书颁发机构 (CA) 签名。 请注意，此 CA 不在 Windows 的受信任根证书颁发机构存储中。
    - 证书主题（可分辨名称或 DN）将设置为租户 ID。 这是唯一标识你的租户的 GUID。 它将此证书限制为仅用于你的租户。
6. Azure AD 将身份验证代理的公钥存储于仅 Azure AD 可访问的 Azure SQL 数据库中。
7. （步骤 5 中发布的）证书存储于本地服务器上 Windows 证书存储空间内（具体而言位于 [CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE) 位置），由身份验证代理应用程序和更新程序应用程序使用。

### <a name="authentication-agent-initialization"></a>初始化身份验证代理

身份验证代理启动时（注册后首次启动或服务器重启后），它需要通过安全的方式与 Azure AD 服务 通信并开始接受密码验证请求。

![代理初始化](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta2.png)

下面介绍如何初始化身份验证代理：



1. 身份验证代理首先向 Azure AD 发出出站“启动”请求。 
    - 此启动请求通过端口 443 由经相互身份验证的 HTTPS 通道发出（使用身份验证代理注册期间所发布的证书）。
2. Azure AD 向对于租户唯一的 Azure 服务总线队列（由租户 ID 所标识）提供访问令牌，以响应此启动请求。
3. 身份验证代理（通过端口 443）与队列建立持续出站 HTTPS 连接。 
    - 此时，它就可以检索和处理密码验证请求了。

如果你的租户上注册了多个身份验证代理，初始化过程会确保每个身份验证代理连接到同一 Azure 服务总线队列。 

## <a name="processing-sign-in-requests"></a>处理登录请求 

下图显示了传递身份验证如何处理用户登录请求。

![处理登录](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta3.png)

传递身份验证按下列步骤处理用户登录请求： 

1. 用户尝试访问应用程序（例如 Outlook Web App - [https://outlook.office365.com/owa](https://outlook.office365.com/owa)。）
2. 如果用户尚未登录，此应用程序将浏览器重定向到 Azure AD 登录页面。
3. Azure AD STS 服务通过用户登录页面反向响应。
4. 用户在 Azure AD 登录页中输入其用户名和密码，并单击“登录”按钮。
5. 在 HTTPS POST 请求中，将用户名和密码提交到 Azure AD STS。
6. Azure AD STS 从 Azure SQL 数据库检索租户上注册的所有身份验证代理的公钥，并将其用于加密密码。 
    - 如果租户上注册了“N”个身份验证代理，它就会生成“N”个加密密码值。
7. Azure AD STS 将密码验证请求（用户名和加密密码值）置于特定于租户的 Azure 服务总线队列上。
8. 初始化的身份验证代理持续连接到 Azure 服务总线队列后，其中一个可用身份验证代理会检索密码验证请求。
9. 此身份验证代理将（使用标识符）查找特定于其公钥的加密密码值，并使用其私钥进行解密。
10. 此身份验证代理将使用 **[Win32 LogonUser API](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx)**（将 dwLogonType 参数设置为 LOGON32_LOGON_NETWORK），尝试针对本地 Active Directory 验证用户名和密码。 
    - 在联合登录方案中，Active Directory 联合身份验证服务 (AD FS) 即使用此 API 登录用户。
11. 身份验证代理从 Active Directory 检索结果（成功状态、用户名或密码不正确、密码过期、用户锁定等）。
12. 身份验证代理通过端口 443，由经相互身份验证的出站 HTTPS 通道将此结果转发回 Azure AD STS。 相互身份验证使用先前在注册期间发布给此身份验证代理的证书。
13. Azure AD STS 验证此结果与租户上的特定登录请求相关联。
14. Azure AD STS 按配置继续进行登录过程。 例如，如果密码验证成功，则可能要求用户进行多重身份验证，或者将用户重定向回该应用程序。

## <a name="operational-security-of-authentication-agents"></a>身份验证代理的操作安全性

为确保传递身份验证保持操作安全，Azure AD 会定期续订其证书。 这些续订由 Azure AD 触发，而不由身份验证代理自行管理。

![运营安全](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta4.png)

下面介绍身份验证代理如何向 Azure AD 续订其信任关系：

1. 身份验证代理将定期 ping Azure AD（数小时一次），检查是否应续订其证书。 
    - 此检查通过经相互身份验证的 HTTPS 通道执行（使用注册期间发布的证书）。
2. 如果服务指示应当续订，身份验证代理会生成一个新的密钥对：一个公钥和一个私钥。
    - 这些密钥采用标准 RSA 2048 位加密而生成。
    - 私钥永远不会离开本地服务器。
3. 身份验证代理然后通过 HTTPS 向 Azure AD 发出“证书续订”请求，此请求中包含以下组件：
    - 现有证书（从 Windows证书存储的 CERT_SYSTEM_STORE_LOCAL_MACHINE 位置所检索到的证书）。 此过程中不涉及全局管理员，因此无需代表全局管理员的访问令牌。
    - 步骤 2 中生成的公钥。
    - 证书签名请求（CSR 或证书请求）。 这用于申请新的数字标识证书，其中 Azure AD 作为其证书颁发机构。
4. Azure AD 验证证书续订请求中的现有证书。 然后，它将验证此请求是否来已在自租户上注册的身份验证代理。
5. 如果现有证书仍然有效，Azure AD 会对新的数字标识证书进行签名，并将其发布回身份验证代理。 
6. 如果现有证书已过期，Azure AD 会从租户的已注册身份验证代理列表中删除此身份验证代理。 然后，全局管理员需要手动安装并注册新的身份验证代理。
    - 证书使用 Azure AD 的根证书颁发机构 (CA) 签名。
    - 证书主题（可分辨名称或 DN）被设置为租户 ID，此租户 ID 是一个用于唯一标识租户的 GUID。 也就是说，此证书仅针对你的租户。
6. Azure AD 将新的身份验证代理公钥存储于仅它可访问的 Azure SQL 数据库中。 它还会使与此身份验证代理关联的“旧”公钥无效。
7. 然后将（步骤 5 中发布的）新证书存储到服务器上的 Windows 证书存储空间（具体而言即 [CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER) 位置）。
    - 由于信任续订过程为非交互式（不涉及全局管理员），因此身份验证代理不再有权更新 CERT_SYSTEM_STORE_LOCAL_MACHINE 位置的现有证书。 请注意，此过程中不会删除 CERT_SYSTEM_STORE_LOCAL_MACHINE 位置的证书本身。
8. 从此时开始，将使用新证书进行身份验证。 随后每次续订该证书都会替换 CERT_SYSTEM_STORE_LOCAL_MACHINE 位置的证书。

## <a name="auto-update-of-authentication-agents"></a>自动更新身份验证代理

新版本发布时，此更新程序应用程序会自动更新身份验证代理。 它不处理租户的任何密码验证请求。 

Azure AD 以已签名 Windows Installer 程序包 (MSI) 的形式，托管该软件的新版本。 使用 [Microsoft 验证码](https://msdn.microsoft.com/library/ms537359.aspx)和 SHA256 摘要算法对 MSI 进行签名。 

![自动更新](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta5.png)

下面介绍如何自动更新身份验证代理：

1. 更新程序会定期 ping Azure AD（每小时一次），检查身份验证代理有无新版本可用。 
    - 此检查通过经相互身份验证的 HTTPS 通道执行（使用注册期间发布的证书）。 身份验证代理和更新程序共享服务器上存储的证书。
2. 如果有新版本可用，Azure AD 会将已签名的 MSI 返回到更新程序。
3. 更新程序验证此 MSI 已由 Microsoft 签名。
4. 更新程序运行该 MSI。 本文执行以下步骤（请注意，更新程序使用[本地系统](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx)权限运行）：
    - 停止身份验证代理服务。
    - 在服务器上安装新版本的身份验证代理。
    - 重新启动身份验证代理服务。

>[!NOTE]
>如果租户上注册了多个身份验证代理，Azure AD 不会同时续订其证书或更新它们。 相反，Azure AD 会逐渐进行续订或更新，以确保登录请求的高可用性。


## <a name="next-steps"></a>后续步骤
- [当前限制](active-directory-aadconnect-pass-through-authentication-current-limitations.md) - 了解支持和不支持的方案。
- [快速入门](active-directory-aadconnect-pass-through-authentication-quick-start.md) - 快速了解 Azure AD 直通身份验证。
- [**智能锁定**](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) - 在租户中配置智能锁定功能以保护用户帐户。
- [工作原理](active-directory-aadconnect-pass-through-authentication-how-it-works.md) - 了解 Azure AD 传递身份验证的基本工作原理。
- [**常见问题**](active-directory-aadconnect-pass-through-authentication-faq.md) - 常见问题解答。
- [故障排除](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) - 了解如何解决使用此功能时遇到的常见问题。
- [**Azure AD 无缝 SSO**](active-directory-aadconnect-sso.md) - 深入了解此补充功能。

