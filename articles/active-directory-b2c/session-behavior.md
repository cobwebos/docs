---
title: 配置会话行为-Azure Active Directory B2C |Microsoft Docs
description: 配置 Azure Active Directory B2C 中的会话行为。
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 7bfa34f44ca8ba53b89e4218303a7cd77cd0add9
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64700983"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>配置 Azure Active Directory B2C 中的会话行为

通过此功能，可以对以下各方面进行[按用户流](active-directory-b2c-reference-policies.md)的精细控制：

- 由 Azure AD B2C 管理的 Web 应用程序会话的生存期。
- Azure AD B2C 租户中跨多个应用和用户流的单一登录 (SSO) 行为。

这些设置不适用于密码重置用户流。

Azure AD B2C 支持 [OpenID Connect 身份验证协议](active-directory-b2c-reference-oidc.md)以启用对 Web 应用的安全登录。 可使用以下属性来管理 Web 应用程序会话：

## <a name="session-behavior-properties"></a>会话行为属性

- **Web 应用会话生存期（分钟）** - 身份验证成功后，存储在用户浏览器上的 Azure AD B2C 会话 Cookie 的生存期。
    - 默认值 = 1440 分钟。
    - 最小值（含）= 15 分钟。
    - 最大值（含）= 1440 分钟。
- **Web 应用会话超时** - 如果此开关设置为“绝对”，则在“Web 应用会话生存期(分钟)”指定的时间之后强制用户重新进行身份验证。 如果此开关设置为“滚动”（默认设置），那么只要用户持续在 Web 应用中保持活动状态，该用户就可以保持登录状态。
- **单一登录配置**B2C 租户中有多个应用程序和用户流，你将可以管理用户交互，在它们之间使用**单一登录配置**属性。 可以将该属性设为以下设置之一：
    - **租户** - 这是默认设置。 使用此设置允许 B2C 租户中的多个应用和用户流共享相同的用户会话。 例如，一旦用户登录到应用程序，就可以在访问另一个应用“Contoso 药店”时无缝登录到该应用。
    - **应用程序** - 此设置允许为某个应用程序维持独占式用户会话（独立于其他应用程序）。 例如，如果想要用户登录 Contoso 药店（具有相同的凭据），即使该用户已登录到 Contoso 购物（相同 B2C 租户中的另一个应用程序），也要输入凭据。 
    - **策略** - 此设置为某个用户流维持独占式用户会话（独立于使用它的应用程序）。 例如，如果用户已登录并完成多重身份验证 (MFA) 步骤，那么只要绑定到用户流的会话未过期，该用户就可以访问多个应用程序的具有较高安全性的部分。
    - **已禁用** - 此设置强制用户在每次执行策略时都要运行完整的用户流过程。

以下用例是使用这些属性实现的：

- 通过设置合适的 Web 应用会话生存期来满足行业的安全性和合规性要求。
- 在用户与 Web 应用程序的安全性较高的部分进行交互时，在设定的时间段之后强制重新进行身份验证。 

## <a name="configure-the-properties"></a>配置属性

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 请务必使用包含 Azure AD B2C 租户的目录，具体方法是单击顶部菜单中的“目录和订阅筛选器”，再选择包含 Azure AD B2C 租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”。
4. 选择**用户流 （策略）**。
5. 打开之前创建的用户流。 
6. 选择“属性”。
7. 配置**Web 应用会话生存期 （分钟）**， **Web 应用会话超时**，**单一登录配置**，和**要求令牌在注销请求中的 ID**根据需要。

    ![配置会话行为](./media/session-behavior/session-behavior.png)
    
8. 单击“ **保存**”。



