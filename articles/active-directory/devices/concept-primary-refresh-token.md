---
title: 主刷新令牌 (PRT) 和 Azure AD-Azure Active Directory
description: 我们如何管理主刷新令牌 (PRT) 在 Azure Active Directory 和的角色是什么？
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e195a93209875b9eabfaa2ad00772281922443c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476112"
---
# <a name="what-is-a-primary-refresh-token"></a>主刷新令牌是什么？

主刷新令牌 (PRT) 是 Windows 10、 iOS 和 Android 设备上的 Azure AD 身份验证密钥项目。 它是 JSON Web 令牌 (JWT) 专门颁发给 Microsoft 第一方令牌中转站来启用单一登录 (SSO) 在这些设备上使用的应用程序。 在本文中，我们将提供有关 PRT 颁发、 使用，并在 Windows 10 设备上受保护的详细信息。

本文假定你已了解不同的设备状态在 Azure AD 中可用和单一登录适用于 Windows 10。 Azure AD 中设备的详细信息，请参阅文章[什么是 Azure Active Directory 中的设备管理？](overview.md)

## <a name="key-terminology-and-components"></a>重要术语和组件

以下 Windows 组件中请求和使用 PRT 至关重要：

* **云身份验证提供程序**(CloudAP):CloudAP 是新式身份验证提供程序中，登录的 Windows 验证登录到 Windows 10 设备的用户。 CloudAP 提供了一个插件框架该标识提供程序基础，可以启用对 Windows 进行身份验证使用该标识提供程序的凭据。
* **Web 帐户管理器**(WAM):WAM 是 Windows 10 设备上的默认令牌代理。 WAM 还提供一个插件框架，该标识提供程序可以构建并向其应用程序依赖于该标识提供程序启用 SSO。
* **Azure AD CloudAP 插件**:CloudAP framework 上构建的 Azure AD 特定插件，该验证 Windows 登录期间使用 Azure AD 的用户凭据。
* **Azure AD WAM 插件**:WAM framework 上构建的 Azure AD 特定插件，这样，SSO 依赖 Azure AD 进行身份验证的应用程序。
* **Dsreg**:一种 Azure AD 特定组件，在 Windows 10 中，处理所有的设备状态的设备注册过程。
* **受信任的平台模块**(TPM):TPM 是内置于设备，它提供的用户和设备机密的基于硬件的安全功能的硬件组件。 可以在文章中找到更多详细信息[受信任的平台模块技术概述](https://docs.microsoft.com/windows/security/information-protection/tpm/trusted-platform-module-overview)。

## <a name="what-does-the-prt-contain"></a>PRT 包含哪些内容？

PRT 包含通常包含在任何 Azure AD 刷新令牌的声明。 此外，还有 PRT 中包含一些特定于设备的声明。 这些限制如下：

* **设备 ID**:PRT 颁发给特定设备上的用户。 设备 ID 声明`deviceID`确定 PRT 已颁发给用户的设备。 更高版本到通过 PRT 获取的令牌颁发此声明。 设备 ID 声明用于确定授权，以基于设备状态或符合性的条件性访问。
* **会话密钥**:会话密钥是加密的对称密钥生成的 Azure AD 身份验证服务，作为 PRT 的一部分发出。 会话键可用作所有权证明 PRT 用于获取令牌，对其他应用程序时。

### <a name="can-i-see-whats-in-a-prt"></a>可以看到什么是 PRT 中？

PRT 是从其内容未知的任何客户端组件的 Azure AD 发送的不透明 blob。 您不能查看内容 PRT。

## <a name="how-is-a-prt-issued"></a>PRT 颁发方式？

设备注册是基于设备身份验证，Azure AD 中的先决条件。 PRT 颁发给仅在已注册的设备上的用户。 设备注册的更深入的信息，请参阅文章[Windows Hello for Business 和设备注册](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration)。 在设备注册期间 dsreg 组件生成加密密钥对的两个的集：

* 设备密钥 (dkpub/dkpriv)
* 传输密钥 (tkpub/tkpriv)

如果设备具有有效且正常运行的 TPM，而公钥发送到 Azure AD 设备注册过程，私钥将绑定到设备的 TPM。 这些密钥用于在 PRT 请求过程中验证设备状态。

在两个方案中在 Windows 10 设备上的用户身份验证过程则会发出 PRT:

* **已加入 azure AD**或**已加入混合 Azure AD**:当用户使用其组织凭据登录时，Windows 登录期间发出 PRT。 PRT 发出使用所有支持的 Windows 10 凭据，例如，密码和 Windows hello 企业版。 在此方案中，Azure AD CloudAP 插件是 PRT 的主机构。
* **Azure AD 注册设备**:用户辅助工作帐户添加到其 Windows 10 设备时，会发出 PRT。 用户可以将帐户添加到 Windows 10 中两种不同方式-  
   * 添加帐户，通过**无处不在此设备上使用此帐户**提示后登录到应用程序 (例如，Outlook)
   * 添加从帐户**设置** > **帐户** > **访问工作或学校** > **连接**

在 Azure AD 注册设备的情况下，Azure AD WAM 插件是 PRT 的主机构，因为与此 Azure AD 帐户未出现 Windows 登录。

> [!NOTE]
> 第三方标识提供程序必须支持要启用 Windows 10 设备上的 PRT 颁发的 WS 信任协议。 没有 WS 信任、 PRT 无法发出到混合 Azure AD 加入或 Azure AD 用户在已加入的设备

## <a name="what-is-the-lifetime-of-a-prt"></a>PRT 生存期是什么？

一旦发出，PRT 有效期为 14 天，且持续续订，只要用户主动使用该设备。  

## <a name="how-is-a-prt-used"></a>如何使用 PRT？

在 Windows 中的两个关键组件使用 PRT:

* **Azure AD CloudAP 插件**:Windows 登录过程中，Azure AD CloudAP 插件使用用户提供的凭据从 Azure AD 请求 PRT。 它还将缓存 PRT 若要启用缓存的登录用户不具有对 internet 连接的访问。
* **Azure AD WAM 插件**:当用户尝试访问应用程序时，Azure AD WAM 插件使用 PRT Windows 10 上启用 SSO。 Azure AD WAM 插件使用 PRT 请求刷新和访问令牌的应用程序依赖于 WAM 提供令牌请求。 它还允许 SSO 在浏览器通过将 PRT 注入到浏览器请求。 浏览器在 Windows 10 中的 SSO （本机） 支持在 Microsoft Edge 和 Chrome （通过 Windows 10 帐户或 Office Online 扩展名）。

## <a name="how-is-a-prt-renewed"></a>如何续订 PRT？

PRT 续订在两种不同方法：

* **Azure AD CloudAP 插件每 4 小时**:CloudAP 插件续订 PRT Windows 登录期间每 4 小时。 如果用户不具有 internet 连接，这段时间内的，CloudAP 插件将设备连接到 internet 后续订 PRT。
* **在应用令牌请求期间的 azure AD WAM 插件**:WAM 插件通过启用应用程序的无提示令牌请求，Windows 10 设备上启用 SSO。 WAM 插件可以两种不同方式在这些令牌请求期间续订 PRT:
   * 应用程序的访问令牌请求 WAM 以无提示方式，但没有无刷新令牌可用于该应用。 在这种情况下，WAM PRT 用于为应用程序请求令牌，然后在响应中取回新 PRT。
   * 应用程序的访问令牌请求 WAM 但 PRT 无效或 Azure AD 需要其他身份验证 （例如，Azure 多重身份验证）。 在此方案中，WAM 启动交互式登录要求用户重新进行身份验证或提供其他验证和新 PRT 发出的成功进行身份验证。

### <a name="key-considerations"></a>重要注意事项

* PRT 只能是颁发和续订在本机应用身份验证过程。 PRT 不续订或在浏览器会话过程中发出。
* 在 Azure AD 中加入，混合 Azure AD 加入设备，CloudAP 插件是 PRT 的主机构。 如果基于 WAM 的令牌请求期间续订 PRT，则 PRT 发送回 CloudAP 插件，将验证与 Azure AD 在接受前 PRT 的有效性。

## <a name="how-is-the-prt-protected"></a>如何保护 PRT？

绑定到设备的用户已登录到受 PRT。 Azure AD 和 Windows 10 通过以下方法启用 PRT 保护：

* **在第一次登录期间**:第一个在登录过程中，签名使用密码学角度上生成设备注册过程的设备密钥的请求颁发 PRT。 有效且正常运行 TPM 的设备，设备是将密钥保护由 TPM 防止任何恶意访问。 如果无法验证相应的设备密钥签名不颁发 PRT。
* **在令牌请求和续订过程**:当发出 PRT 时，Azure AD 还向设备颁发一个加密的会话密钥。 它使用的公共传输密钥 (tkpub) 生成并发送到 Azure AD 设备注册的一部分进行加密。 此会话密钥只能由 TPM 保护的专用传输密钥 (tkpriv) 进行解密。 会话密钥是发送到 Azure AD 的任何请求的证明所有权 (POP) 键。  会话密钥还受 TPM 和任何其他操作系统组件可以访问它。 令牌请求或 PRT 续订请求由 TPM 通过此会话密钥安全地签名，并且因此，不会遭到篡改。 Azure AD 将使来自该设备不由相应的会话密钥签名的任何请求无效。

恶意执行组件不能通过保护这些密钥和 TPM，窃取的密钥和 TPM 不可用，即使攻击者实际拥有该设备时重播 PRT。  因此，使用 TPM 极大地增强了安全性的已加入 Azure AD，加入的混合 Azure AD，并且 Azure AD 注册以免凭据被盗的设备。 有关性能和可靠性，TPM 2.0 是 Windows 10 上的所有 Azure AD 设备注册方案的推荐的版本。

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>如何将应用令牌和受保护的浏览器 cookie？

**应用令牌**:当应用程序请求令牌通过 WAM 时，Azure AD 颁发的刷新令牌和一个访问令牌。 但是，WAM 仅返回到应用的访问令牌，并通过使用用户的数据保护应用程序编程接口 (DPAPI) 密钥进行加密来保护其缓存中的刷新令牌。 WAM 安全地使用刷新令牌的签名请求使用的会话密钥进一步颁发访问令牌。 DPAPI 密钥是在 Azure AD 本身中保护基于 Azure AD 对称密钥。 当设备需要使用 DPAPI 密钥解密的用户配置文件时，Azure AD 提供的哪些 CloudAP 插件请求 TPM 进行解密的会话密钥加密的 DPAPI 密钥。 此功能可确保保护刷新令牌中的一致性并避免应用程序实现其自己的保护机制。  

**浏览器 cookie**:在 Windows 10 中，Azure AD 支持 SSO 在 Internet Explorer 和 Microsoft Edge 浏览器以本机模式或 Google Chrome 中通过 Windows 10 帐户扩展。 安全性不仅旨在保护 cookie，但还 cookie 发送到的终结点。 浏览器 cookie 受保护的相同方式 PRT 是，通过利用会话密钥进行签名和保护 cookie。

当用户启动浏览器交互时，浏览器 （或扩展） 将调用 COM 的本机客户端主机。 本机客户端主机可确保该页面是从一个允许的域。 在浏览器可能会发送到本机客户端的其他参数承载包括 nonce，但是本机客户端主机保证主机名的验证。 本机客户端主机请求 PRT cookie 从 CloudAP 插件创建和使用 TPM 保护会话密钥对其进行签名。 PRT cookie 的会话密钥进行签名，因为它不会遭到篡改。 Azure AD，以验证它所源自的设备的请求标头中包含此 PRT cookie。 如果使用 Chrome 浏览器，仅在本机客户端主机的清单中显式定义的扩展可以调用它防止任意扩展进行这些请求。 Azure AD 验证 PRT cookie 后, 一个会话 cookie 颁发给浏览器。 此会话 cookie 还包含与 PRT 颁发的同一个会话密钥。 在后续请求期间有效地绑定到设备的 cookie 和防止重播从其他位置进行验证的会话密钥。

## <a name="when-does-a-prt-get-an-mfa-claim"></a>PRT 时获得的 MFA 声明？

PRT 可以在特定情况下获取多重身份验证 (MFA) 声明。 当基于 MFA 的 PRT 用于请求令牌的应用程序时，MFA 声明会传输到这些应用程序令牌。 此功能通过阻止 MFA 质询，每个应用都需要它来向用户提供无缝体验。 PRT 可以按以下方式获取 MFA 声明：

* **使用 Windows Hello for Business 登录**:Windows hello 企业版将替换密码并使用加密密钥来提供强大的双因素身份验证。 Windows hello 企业版是特定于设备上的用户和本身需要预配进行 MFA。 当用户记录时使用 Windows hello 企业版时，用户的 PRT 获取 MFA 声明。 这种情况下也适用于如果智能卡身份验证生成从 ADFS MFA 声明使用智能卡登录的用户。
   * 因为 Windows hello 企业版被认为多重身份验证，MFA 声明时，将更新刷新 PRT 本身，因此当使用 WIndows hello 企业版的用户登录时，将持续延长 MFA 持续时间
* **WAM 交互式登录的 MFA**:通过 WAM 的令牌请求，期间是否需要执行 MFA，以便在访问该应用，用户在这种交互过程续订 PRT 是压印使用 MFA 声明。
   * 在这种情况下，MFA 声明不会更新连续，因此 MFA 持续时间开始算起的目录上设置的生存期。
* **设备注册期间 MFA**:如果管理员已向 Azure AD 中配置其设备设置[要求进行 MFA 注册设备](device-management-azure-portal.md#configure-device-settings)，用户需要执行 MFA，以便在完成注册。 在此过程中，向用户颁发 PRT 已在注册过程中获取的 MFA 声明。 此功能仅适用于执行联接操作，不适用于其他用户登录到该设备的用户。
   * 类似于在 WAM 交互式登录，MFA 声明是不会不断更新，因此 MFA 持续时间开始算起的目录上设置的生存期。

Windows 10 维护每个凭据 Prt 的分区的列表。 因此，是为每个 Windows Hello for Business、 密码或智能卡 PRT。 分区可确保 MFA 声明是独立基于使用，并且不混在令牌请求期间的凭据。

## <a name="how-is-a-prt-invalidated"></a>如何为失效 PRT？

在以下方案中验证 PRT:

* **无效的用户**:如果用户已删除或禁用在 Azure AD 中，其 PRT 将失效，不能用于获取令牌的应用程序。 如果已删除或已禁用的用户已登录到设备之前，缓存登录会记录它们在中，直到 CloudAP 都不知道其无效的状态。 一旦 CloudAP 确定用户无效，它将阻止后续登录。 无效的用户自动阻止登录到没有缓存其凭据的新设备。
* **无效设备**:如果设备已删除或禁用在 Azure AD 中，在该设备上获得 PRT 将失效，不能用于获取令牌，对其他应用程序。 如果用户已登录到无效的设备，他们可以继续执行此操作。 在设备上的所有令牌都无效，但用户不具有 SSO 对任何资源从该设备。
* **密码更改**:用户更改其密码后，获取与以前的密码 PRT 会失效由 Azure AD。 密码更改会导致用户获得新 PRT。 两个不同的方式会发生此失效：
   * 如果用户登录到 Windows 使用他们的新密码，CloudAP 丢弃旧 PRT 并请求 Azure AD 颁发新 PRT 使用他们的新密码。 如果用户不具有 internet 连接，不能验证新密码，Windows 可能会要求用户输入旧密码。
   * 如果用户已使用旧密码登录或更改其密码登录到 Windows 后，旧 PRT 用于任何基于 WAM 的令牌请求。 在此方案中，系统会提示用户 WAM 令牌请求期间重新进行身份验证并颁发新 PRT。
* **TPM 问题**:有时，设备的 TPM 可以出现故障或故障，从而导致无法访问受保护的 TPM 的密钥。 在这种情况下，设备是为获取 PRT 或请求令牌使用现有 PRT，因为它不能证明持有的加密密钥不能进行。 因此，任何现有 PRT 无效由 Azure AD。 当 Windows 10 检测到故障时，它将启动恢复流以使用新的加密密钥重新注册设备。 对于混合 Azure Ad 联接，就像在初始注册中，执行恢复以无提示方式而无需用户输入。 对于 Azure AD 加入或 Azure AD 注册设备，需要在设备上具有管理员权限的用户执行恢复。 在此方案中，由指导用户才能成功恢复设备的 Windows 提示启动的恢复流。

## <a name="detailed-flows"></a>详细的流

下图显示了颁发、 续订和使用 PRT 请求应用程序的访问令牌中的基础详细信息。 此外，这些步骤还介绍如何在这些交互过程中应用的前面提到的安全机制。

### <a name="prt-issuance-during-first-sign-in"></a>在第一次登录期间 PRT 颁发

![在详细流的第一次登录期间 PRT 颁发](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> 在 Azure AD 中已加入的设备，这种交换以同步方式发生之前用户可以登录到 Windows 发出 PRT。 在混合环境中已加入 Azure AD 的设备，在本地 Active Directory 是主要授权机构。 因此，直到它们能够获取 TGT 为登录名，以异步方式 PRT 颁发时，仅正在等待用户。 此方案中不适用于 Azure AD 注册设备一样登录不会使用 Azure AD 凭据。

| 步骤 | 描述 |
| :---: | --- |
| A | 用户输入其密码的登录 UI。 LogonUI 将身份验证缓冲区中的凭据传递到 LSA，后者又在内部将其传递给 CloudAP。 CloudAP 此将请求转发到 CloudAP 插件。 |
| B | CloudAP 插件启动领域发现请求来标识用户的标识提供程序。 如果用户的租户联合身份验证提供程序安装程序，Azure AD 返回联合身份验证提供程序的元数据交换终结点 (MEX) 终结点。 如果不是，Azure AD 将返回用户为托管，该值指示该用户可以与 Azure AD 进行身份验证。 |
| C | 如果用户为托管用户，CloudAP 将从 Azure AD 获取的 nonce。 如果联合用户，CloudAP 插件从用户的凭据的联合身份验证提供程序请求 SAML 令牌中。 一旦收到，SAML 令牌中，将从 Azure AD 请求 nonce。 |
| D | CloudAP 插件构造用户的凭据、 nonce 和 broker 作用域的身份验证请求、 使用的设备密钥 (dkpriv) 对请求进行签名并将其发送到 Azure AD。 在联合环境中，CloudAP 插件使用而不是用户的联合身份验证提供程序返回的 SAML 令牌的凭据。 |
| E | Azure AD 验证用户凭据，nonce，设备签名确认设备已在租户中有效，并发出加密的 PRT。 PRT，以及 Azure AD 还颁发称为 Azure AD 中使用的传输密钥 (tkpub) 进行加密的会话密钥的对称密钥。 此外，会话密钥还可以嵌入在 PRT。 此会话密钥用作的后续请求的 PRT-的所有权证明 (PoP) 键。 |
| F | CloudAP 插件将加密的 PRT 和会话密钥传递给 CloudAP。 CloudAP 请求 TPM 使用的传输密钥 (tkpriv) 会话密钥进行解密和重新使用 TPM 的密钥进行加密。 CloudAP PRT 以及其缓存中存储的加密的会话密钥。 |

### <a name="prt-renewal-in-subsequent-logons"></a>在后续登录中 PRT 续订

![在后续登录中 PRT 续订](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| 步骤 | 描述 |
| :---: | --- |
| A | 用户输入其密码的登录 UI。 LogonUI 将身份验证缓冲区中的凭据传递到 LSA，后者又在内部将其传递给 CloudAP。 CloudAP 此将请求转发到 CloudAP 插件。 |
| B | 如果用户具有以前登录到用户，Windows 启动缓存登录并验证凭据以登录用户。 每隔 4 小时，CloudAP 插件以异步方式启动 PRT 续订。 |
| C | CloudAP 插件启动领域发现请求来标识用户的标识提供程序。 如果用户的租户联合身份验证提供程序安装程序，Azure AD 返回联合身份验证提供程序的元数据交换终结点 (MEX) 终结点。 如果不是，Azure AD 将返回用户为托管，该值指示该用户可以与 Azure AD 进行身份验证。 |
| D | 如果联合用户，CloudAP 插件从用户的凭据的联合身份验证提供程序请求 SAML 令牌中。 一旦收到，SAML 令牌中，将从 Azure AD 请求 nonce。 如果用户为托管用户，CloudAP 直接将从 Azure AD 中获取的 nonce。 |
| E | CloudAP 插件构造与用户的凭据、 nonce 和现有 PRT 身份验证请求、 使用会话密钥对请求进行签名并将其发送到 Azure AD。 在联合环境中，CloudAP 插件使用而不是用户的联合身份验证提供程序返回的 SAML 令牌的凭据。 |
| F | Azure AD 针对 PRT 中嵌入的会话密钥进行比较，来验证会话密钥签名、 验证 nonce，确认设备已在租户中有效，并颁发新 PRT。 如之前所示，PRT 再次都附有会话密钥加密的传输密钥 (tkpub)。 |
| G | CloudAP 插件将加密的 PRT 和会话密钥传递给 CloudAP。 CloudAP 请求 TPM 来解密会话密钥使用的传输密钥 (tkpriv) 并重新对其使用 TPM 的密钥进行加密。 CloudAP PRT 以及其缓存中存储的加密的会话密钥。 |

### <a name="prt-usage-during-app-token-requests"></a>在应用令牌请求期间 PRT 使用情况

![在应用令牌请求期间 PRT 使用情况](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| 步骤 | 描述 |
| :---: | --- |
| A | （例如，Outlook、 OneNote 等） 的应用程序启动对 WAM 的令牌请求。 WAM，反过来，会要求 Azure AD WAM 插件令牌请求提供服务。 |
| B | 如果应用程序的刷新令牌已可用，Azure AD WAM 插件将使用它来请求访问令牌。 若要提供证明设备绑定，WAM 插件使用的会话密钥进行签名请求。 Azure AD 验证的会话密钥，颁发访问令牌和新的刷新令牌的应用程序中，由会话密钥加密。 WAM 插件请求云 AP 插件来解密令牌，其中，请求 TPM 使用会话密钥，从而导致 WAM 插件获取这两个令牌进行解密。 接下来，WAM 插件提供的访问令牌将应用程序，而它重新加密 DPAPI 使用刷新令牌并将其存储在其自己的缓存  |
| C |  如果刷新令牌的应用程序不可用，Azure AD WAM 插件使用 PRT 请求访问令牌。 若要提供所有权证明，WAM 插件对使用的会话密钥包含 PRT 的请求进行签名。 Azure AD 针对 PRT 中嵌入的会话密钥进行比较，来验证会话密钥签名，验证设备有效，并颁发访问令牌和刷新令牌的应用程序。 此外，Azure AD 可以颁发新 PRT （基于刷新周期），所有这些会话密钥进行加密。 |
| D | WAM 插件请求云 AP 插件来解密令牌，其中，请求 TPM 使用会话密钥，从而导致 WAM 插件获取这两个令牌进行解密。 接下来，WAM 插件提供的访问令牌将应用程序，而它重新加密 DPAPI 使用刷新令牌并将其存储在其自己的缓存。 WAM 插件将继续为此应用程序的刷新令牌。 WAM 插件还可以返回新 PRT 云 AP 插件，验证与 Azure AD 之前在其自己的缓存中更新 PRT。 云 AP 插件将使用新 PRT 今后。 |
| E | WAM 到 WAM，反过来，提供它返回到调用应用程序提供新颁发的访问令牌|

### <a name="browser-sso-using-prt"></a>浏览器 SSO 使用 PRT

![浏览器 SSO 使用 PRT](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| 步骤 | 描述 |
| :---: | --- |
| A | 用户登录到 Windows 使用以获取 PRT 其凭据。 一旦用户打开浏览器，浏览器 （或扩展） 可从注册表加载 Url。 |
| B | 当用户打开的 Azure AD 登录 URL 时，浏览器或扩展从注册表获取与验证 URL。 如果它们匹配，则在浏览器调用本机客户端主机用于获取令牌。 |
| C | 本机客户端主机验证 Url 属于 Microsoft 标识提供者 （Microsoft 帐户或 Azure AD）、 nonce 发送从 URL 中提取和将 CloudAP 插件调用以获取 PRT cookie。 |
| D | CloudAP 插件将创建 PRT cookie、 使用 TPM 绑定会话密钥登录，将其发送回本机客户端主机。 因为 cookie 由会话密钥签名的它不会遭到篡改。 |
| E | 本机客户端主机将此 PRT cookie 返回到浏览器中，其中包括其作为从 Azure AD 中称为 x ms RefreshTokenCredential 和请求令牌的请求标头的一部分。 |
| F | Azure AD 验证上 PRT cookie 的会话密钥签名、 验证 nonce，验证设备已在租户中有效，并发出 web 页的 ID 令牌，并在浏览器的加密的会话 cookie。 |

## <a name="next-steps"></a>后续步骤

故障排除与 PRT 相关的问题的详细信息，请参阅文章[故障排除混合 Azure Active Directory 已加入 Windows 10 和 Windows Server 2016 设备](troubleshoot-hybrid-join-windows-current.md)。
