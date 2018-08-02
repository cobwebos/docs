---
title: Azure Active Directory 直通身份验证安全性深入研究 | Microsoft Docs
description: 本文介绍 Azure Active Directory (Azure AD) 传递身份分验证如何保护本地帐户
services: active-directory
keywords: Azure AD Connect 传递身份验证, 安装 Active Directory, Azure AD 所需的组件, SSO, 单一登录
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: ad4567ffb927694872d5b86dd38833466f944ca8
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215078"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Azure Active Directory 直通身份验证安全性深入研究

本文将更加详细地介绍 Azure Active Directory (Azure AD) 直通身份验证的工作原理。 本文注重此功能的安全性方面。 本文适用于：中小型组织或大型企业的安全管理员、IT 管理员、首席合规官、首席安全官以及负责 IT 安全性和符合性的其他 IT 专业人士。

论述的主题包括：
- 关于如何安装和注册身份验证代理的详细技术信息。
- 关于用户登录期间密码加密的详细技术信息。
- 本地身份验证代理和 Azure AD 之间通道的安全性。
- 关于如何保持身份验证代理操作安全的详细技术信息。
- 其他安全性相关主题。

## <a name="key-security-capabilities"></a>主要安全功能

此功能的安全性主要有以下方面：
- 它构建于安全的多租户体系结构上，此体系结构隔离租户之间的登录请求。
- 本地密码永远不会以任何形式存储在云中。
- 本地身份验证代理侦听和响应密码验证请求，它仅从网络内部建立出站连接。 无需在外围网络 (DMZ) 中安装这些身份验证代理。 最佳做法是将运行身份验证代理的所有服务器视为第 0 层系统（请参阅[参考](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)）。
- 从身份验证代理到 Azure AD 的出站通信仅使用标准端口（80 和 443）。 不需打开防火墙上的入站端口。 
  - 端口 443 用于所有经过身份验证的出站通信。
  - 端口 80 仅用于下载证书吊销列表 (CRL)，以确保未吊销此功能所用的任何证书。
  - 如需网络安全的完整列表，请参阅 [Azure Active Directory 直通身份验证：快速入门](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-the-prerequisites)。
- 用户在登录期间提供的密码将在云中加密，再由本地身份验证代理接受，通过 Active Directory 进行验证。
- 通过相互进行身份验证保护 Azure AD 和本地身份验证代理之间的 HTTPS 通道。
- 可通过与 [Azure AD 条件访问策略](../active-directory-conditional-access-azure-portal.md)（包括多重身份验证 (MFA)、[阻止旧式身份验证](../active-directory-conditional-access-conditions.md)）无缝协作，也可通过[筛选暴力破解密码攻击](../authentication/howto-password-smart-lockout.md)来保护用户帐户。

## <a name="components-involved"></a>涉及的组件

有关 Azure AD 操作、服务和数据安全性的常规详细信息，请参阅[信任中心](https://azure.microsoft.com/support/trust-center/)。 使用直通身份验证进行用户登录时，会涉及以下组件：
- **Azure AD STS**：一项无状态安全令牌服务 (STS)，它根据需要处理登录请求，并向用户的浏览器、客户端或服务发布安全令牌。
- Azure 服务总线：可在云端使用企业消息传递进行通信，也可采用中继进行通信，帮助将本地解决方案与云端连接。
- **Azure AD Connect 身份验证代理**：用于侦听和响应密码验证请求的本地组件。
- **Azure SQL 数据库**：保存有关租户身份验证代理的信息，包括其元数据和加密密钥。
- **Active Directory**：用于存储用户帐户及其密码的本地 Active Directory。

## <a name="installation-and-registration-of-the-authentication-agents"></a>安装和注册身份验证代理

在以下情况下安装身份验证代理并将其注册到 Azure AD：
   - [允许通过 Azure AD Connect 进行直通身份验证](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature)
   - [添加更多身份验证代理，确保登录请求的高可用性](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability) 
   
启用身份验证代理涉及三个主要阶段：

1. 安装身份验证代理
2. 注册身份验证代理
3. 初始化身份验证代理

以下各节详细讨论这些阶段。

### <a name="authentication-agent-installation"></a>安装身份验证代理

仅全局管理员可在本地服务器上安装身份验证代理（使用 Azure AD Connect 或独立安装）。 安装后，“控制面板” > “程序” > “程序和功能”列表中将显示两个新条目：
- 身份验证代理应用程序本身。 此应用程序使用 [NetworkService](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx) 特权运行。
- 用于自动更新身份验证代理的更新程序应用程序。 此应用程序使用 [LocalSystem](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) 特权运行。

### <a name="authentication-agent-registration"></a>注册身份验证代理

安装身份验证代理后，它需要将自身注册到 Azure AD。 Azure AD 为每个身份验证代理分配一个唯一数字标识证书，该证书可用于与 Azure AD 进行安全通信。

注册过程还将身份验证代理与租户绑定在一起。 这样可确保 Azure AD 知道，此特定身份验证代理是经授权处理租户密码验证请求的唯一代理。 注册每个新身份验证代理，都要重复此过程。

身份验证代理使用以下步骤自行注册到 Azure AD：

![代理注册](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta1.png)

1. Azure AD 首先请求全局管理员使用凭据登录到 Azure AD。 登录期间，身份验证代理获取一个访问令牌，它可以代表全局管理员使用该令牌。
2. 然后，身份验证代理生成一个密钥对：一个公钥和一个私钥。
    - 此密钥对通过标准 RSA 2048 位加密生成。
    - 私钥停留在本地服务器上，这是身份验证代理驻留的地方。
3. 身份验证代理通过 HTTPS 向 Azure AD 发出“注册”请求，此请求中包含以下组件：
    - 步骤 1 中获取的访问令牌。
    - 步骤 2 中生成的公钥。
    - 证书签名请求（CSR 或证书请求）。 此请求申请数字标识证书，Azure AD 为其证书颁发机构 (CA)。
4. Azure AD 验证注册请求中的访问令牌，确认该请求来自全局管理员。
5. 然后，Azure AD 对数字标识证书签名，并将其发送回身份验证代理。
    - Azure AD 中的根 CA 用于证书签名。 

     >[!NOTE]
     > 此 CA 不在 Windows 的受信任根证书颁发机构存储中。
    - 此 CA 仅由直通身份验证功能使用。 CA 仅在身份验证代理注册过程中用于签署 CSR。
    -  没有任何其他 Azure AD 服务使用此 CA。
    - 证书主题（可分辨名称或 DN）将设置为租户 ID。 此 DN 是唯一标识租户的 GUID。 此 DN 将此证书限制为仅用于租户。
6. Azure AD 将身份验证代理的公钥存储于仅 Azure AD 可访问的 Azure SQL 数据库中。
7. 将（步骤 5 中发布的）证书存储到本地服务器上的 Windows 证书存储（具体而言，即 [CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE) 位置）。 它可供身份验证代理和更新程序应用程序使用。

### <a name="authentication-agent-initialization"></a>初始化身份验证代理

身份验证代理启动时（注册后首次启动或服务器重启后），它需要通过一种方式与 Azure AD 服务安全地通信并开始接受密码验证请求。

![代理初始化](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta2.png)

下面介绍如何初始化身份验证代理：

1. 身份验证代理向 Azure AD 发出出站启动请求。 
    - 此请求通过端口 443 发出，也是通过相互进行身份验证的 HTTPS 通道发出。 请求使用的证书是在身份验证代理注册期间发出的。
2. Azure AD 向对于租户唯一的 Azure 服务总线队列（由租户 ID 所标识）提供访问令牌，以响应此请求。
3. 身份验证代理（通过端口 443）与队列建立持续出站 HTTPS 连接。 
    - 此时，身份验证代理就可以检索和处理密码验证请求了。

如果租户上注册了多个身份验证代理，初始化过程会确保每个身份验证代理连接到同一服务总线队列。 

## <a name="process-sign-in-requests"></a>处理登录请求 

下图显示了直通身份验证如何处理用户登录请求。

![处理登录](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta3.png)

传递身份验证按下列步骤处理用户登录请求： 

1. 用户尝试访问某个应用程序，例如 [Outlook Web 应用](https://outlook.office365.com/owa)。
2. 如果用户尚未登录，此应用程序将浏览器重定向到 Azure AD 登录页面。
3. Azure AD STS 服务通过“用户登录”页面反向响应。
4. 用户在“用户登录”页中输入其用户名，然后选择“下一步”按钮。
5. 用户在“用户登录”页中输入其密码，然后选择“登录”按钮。
6. 在 HTTPS POST 请求中，将用户名和密码提交到 Azure AD STS。
7. Azure AD STS 从 Azure SQL 数据库检索租户上注册的所有身份验证代理的公钥，并将其用于加密密码。 
    - 如果租户上注册了“N”个身份验证代理，它就会生成“N”个加密密码值。
8. Azure AD STS 将密码验证请求（包含用户名和加密密码值）置于特定于租户的服务总线队列上。
9. 由于初始化的身份验证代理持续连接到服务总线队列，因此其中一个可用身份验证代理会检索密码验证请求。
10. 身份验证代理将使用标识符查找特定于其公钥的加密密码值，并使用其私钥进行解密。
11. 此身份验证代理将使用 [Win32 LogonUser API](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx)（将 **dwLogonType** 参数设置为 **LOGON32_LOGON_NETWORK**），尝试向本地 Active Directory 验证用户名和密码。 
    - 在联合登录方案中，Active Directory 联合身份验证服务 (AD FS) 即使用此 API 登录用户。
    - 此 API 依赖 Windows Server 中的标准解析进程来查找域控制器。
12. 身份验证代理从 Active Directory 检索结果（例如成功、用户名或密码不正确或密码过期）。
13. 身份验证代理通过端口 443，由经相互身份验证的出站 HTTPS 通道将此结果转发回 Azure AD STS。 相互身份验证使用先前在注册期间发布给此身份验证代理的证书。
14. Azure AD STS 验证此结果与租户上的特定登录请求相关联。
15. Azure AD STS 按配置继续进行登录过程。 例如，如果密码验证成功，则可能要求用户进行多重身份验证，或者将用户重定向回该应用程序。

## <a name="operational-security-of-the-authentication-agents"></a>身份验证代理的操作安全性

为确保直通身份验证保持操作安全，Azure AD 会定期续订身份验证代理的证书。 Azure AD 触发续订。 续订不由身份验证代理自行管理。

![运营安全](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta4.png)

若要向 Azure AD 续订身份验证代理的信任关系，请执行以下操作：

1. 身份验证代理将定期 ping Azure AD（数小时一次），检查是否应续订其证书。 在证书到期前 30 天续订。
    - 此检查通过经相互身份验证的 HTTPS 通道执行，使用注册期间颁发的证书。
2. 如果服务指示应当续订，身份验证代理会生成一个新的密钥对：一个公钥和一个私钥。
    - 这些密钥通过标准 RSA 2048 位加密生成。
    - 私钥永远不会离开本地服务器。
3. 身份验证代理然后通过 HTTPS 向 Azure AD 发出“证书续订”请求，此请求中包含以下组件：
    - 现有证书（从 Windows 证书存储上的 CERT_SYSTEM_STORE_LOCAL_MACHINE 位置检索到的证书）。 此过程中不涉及全局管理员，因此无需代表全局管理员的访问令牌。
    - 步骤 2 中生成的公钥。
    - 证书签名请求（CSR 或证书请求）。 此请求申请新的数字标识证书，Azure AD 为其证书颁发机构。
4. Azure AD 验证证书续订请求中的现有证书。 然后，它将验证此请求是否来已在自租户上注册的身份验证代理。
5. 如果现有证书仍然有效，Azure AD 会对新的数字标识证书进行签名，并将其发布回身份验证代理。 
6. 如果现有证书已过期，Azure AD 会从租户的已注册身份验证代理列表中删除此身份验证代理。 然后，全局管理员需要手动安装并注册新的身份验证代理。
    - 使用 Azure AD 根 CA 对证书进行签名。
    - 将证书主题（可分辨名称或 DN）设置为租户 ID，此租户 ID 是一个用于唯一标识租户的 GUID。 此 DN 使证书仅针对你的租户。
6. Azure AD 将新的身份验证代理公钥存储于仅它可访问的 Azure SQL 数据库中。 它还会使与此身份验证代理关联的旧公钥无效。
7. 然后将（步骤 5 中颁发的）新证书存储到服务器上的 Windows 证书存储（具体而言即 [CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER) 位置）。
    - 由于信任续订过程为非交互式（不涉及全局管理员），因此身份验证代理不再有权更新 CERT_SYSTEM_STORE_LOCAL_MACHINE 位置的现有证书。 
    
   > [!NOTE]
   > 此过程不删除 CERT_SYSTEM_STORE_LOCAL_MACHINE 位置的证书本身。
8. 从此时开始，将使用新证书进行身份验证。 随后每次续订该证书都会替换 CERT_SYSTEM_STORE_LOCAL_MACHINE 位置的证书。

## <a name="auto-update-of-the-authentication-agents"></a>自动更新身份验证代理

新版本发布时，此更新程序应用程序会自动更新身份验证代理。 此应用程序不处理租户的任何密码验证请求。 

Azure AD 以已签名 Windows Installer 程序包 (MSI) 的形式，托管该软件的新版本。 使用 [Microsoft 验证码](https://msdn.microsoft.com/library/ms537359.aspx)和 SHA256 摘要算法对 MSI 进行签名。 

![自动更新](./media/active-directory-aadconnect-pass-through-authentication-security-deep-dive/pta5.png)

若要自动更新身份验证代理，请执行以下操作：

1. 更新程序应用程序会 ping Azure AD（每小时一次），检查身份验证代理有无新版本可用。 
    - 此检查通过经相互身份验证的 HTTPS 通道执行，方法是使用注册期间颁发的证书。 身份验证代理和更新程序共享服务器上存储的证书。
2. 如果有新版本可用，Azure AD 会将已签名的 MSI 返回到更新程序。
3. 更新程序验证此 MSI 已由 Microsoft 签名。
4. 更新程序运行该 MSI。 此操作涉及以下步骤：

 > [!NOTE]
 > 更新程序使用[本地系统](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx)权限运行。

    - 停止身份验证代理服务
    - 在服务器上安装新版本的身份验证代理
    - 重启身份验证代理服务

>[!NOTE]
>如果租户上注册了多个身份验证代理，Azure AD 不会同时续订其证书或更新它们。 相反，Azure AD 会逐渐进行续订或更新，确保登录请求的高可用性。
>


## <a name="next-steps"></a>后续步骤
- [当前限制](active-directory-aadconnect-pass-through-authentication-current-limitations.md)：了解支持和不支持的方案。
- [快速入门](active-directory-aadconnect-pass-through-authentication-quick-start.md)：快速了解 Azure AD 直通身份验证。
- [从 AD FS 迁移到传递身份验证](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx) - 从 AD FS（或其他联合技术）迁移到传递身份验证的详细指南。
- [智能锁定](../authentication/howto-password-smart-lockout.md)：在租户中配置智能锁定功能以保护用户帐户。
- [工作原理](active-directory-aadconnect-pass-through-authentication-how-it-works.md)：了解 Azure AD 直通身份验证的基本工作原理。
- [常见问题解答](active-directory-aadconnect-pass-through-authentication-faq.md)：查找常见问题的解答。
- [故障诊断](active-directory-aadconnect-troubleshoot-pass-through-authentication.md)：了解如何解决直通身份验证功能的常见问题。
- [Azure AD 无缝 SSO](active-directory-aadconnect-sso.md)：深入了解此补充功能。
