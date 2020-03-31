---
title: 主刷新令牌 （PRT） 和 Azure AD - Azure 活动目录
description: Azure 活动目录中的主要刷新令牌 （PRT） 的作用是什么以及如何管理？
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
ms.openlocfilehash: 9a237ad35d9d5d8abee784926563d972d0ee95f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672637"
---
# <a name="what-is-a-primary-refresh-token"></a>什么是主刷新令牌？

主刷新令牌 （PRT） 是 Windows 10、iOS 和 Android 设备上 Azure AD 身份验证的关键项目。 它是专门颁发给 Microsoft 第一方令牌代理的 JSON Web 令牌 （JWT），用于跨这些设备中使用的应用程序启用单一登录 （SSO）。 在本文中，我们将提供有关如何在 Windows 10 设备上颁发、使用和保护 PRT 的详细信息。

本文假定您已经了解了 Azure AD 中可用的不同设备状态以及单一登录在 Windows 10 中的工作方式。 有关 Azure AD 中的设备的详细信息，请参阅["Azure 活动目录中什么是设备管理"](overview.md)一文。

## <a name="key-terminology-and-components"></a>关键术语和组件

以下 Windows 组件在请求和使用 PRT 时起着关键作用：

* **云身份验证提供程序**（CloudAP）：CloudAP 是 Windows 登录的现代身份验证提供程序，用于验证登录到 Windows 10 设备的用户。 CloudAP 提供了一个插件框架，身份提供商可以构建该插件框架，以便使用该标识提供程序的凭据对 Windows 启用身份验证。
* **Web 帐户管理器**（WAM）：WAM 是 Windows 10 设备上的默认令牌代理。 WAM 还提供一个插件框架，身份提供程序可以基于该插件框架，并使 SSO 能够依赖于该标识提供程序。
* **Azure AD CloudAP 插件**：在 CloudAP 框架上构建的特定于 Azure AD 的插件，用于在 Windows 登录期间使用 Azure AD 验证用户凭据。
* **Azure AD WAM 插件**：在 WAM 框架上构建的特定于 Azure AD 的插件，该插件使 SSO 能够连接到依赖 Azure AD 进行身份验证的应用程序。
* **Dsreg**： Windows 10 上特定于 Azure AD 的组件，用于处理所有设备状态的设备注册过程。
* **受信任的平台模块**（TPM）：TPM 是内置于设备中的硬件组件，为用户和设备机密提供基于硬件的安全功能。 更多详细信息请参阅文章["受信任的平台模块技术概述](/windows/security/information-protection/tpm/trusted-platform-module-overview)"。

## <a name="what-does-the-prt-contain"></a>PRT 包含哪些内容？

PRT 包含通常包含在任何 Azure AD 刷新令牌中的声明。 此外，PRT 中还包括一些特定于设备的声明。 这些限制如下：

* **设备 ID**：向特定设备上的用户发出 PRT。 设备 ID`deviceID`声明确定 PRT 颁发给用户的设备。 此声明后来颁发给通过 PRT 获得的令牌。 设备 ID 声明用于根据设备状态或合规性确定条件访问的授权。
* **会话密钥**：会话密钥是一个加密的对称密钥，由 Azure AD 身份验证服务生成，作为 PRT 的一部分发出。 当使用 PRT 获取其他应用程序的令牌时，会话密钥充当占有证明。

### <a name="can-i-see-whats-in-a-prt"></a>我能看看 PRT 中的内容吗？

PRT 是从 Azure AD 发送的不透明 Blob，其内容不为任何客户端组件所知。 您看不到 PRT 中的内容。

## <a name="how-is-a-prt-issued"></a>如何颁发 PRT？

设备注册是 Azure AD 中基于设备的身份验证的先决条件。 PRT 仅在已注册的设备上颁发给用户。 有关设备注册的更深入详细信息，请参阅["Windows Hello 用于企业和设备注册](/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration)"一文。 在设备注册期间，dsreg 组件生成两组加密密钥对：

* 设备密钥（dkpub/dkpriv）
* 运输钥匙（tkpub/tkpriv）

如果设备具有有效和正常运行的 TPM，则私钥将绑定到设备的 TPM，而公钥在设备注册过程中会发送到 Azure AD。 这些密钥用于在 PRT 请求期间验证设备状态。

PRT 在 Windows 10 设备上的用户身份验证期间在以下两种情况下发出：

* **Azure AD 已加入**或**混合 Azure AD 加入**：当用户使用组织凭据登录时，在 Windows 登录期间发出 PRT。 PRT 会颁发所有 Windows 10 支持的凭据，例如，密码和适用于企业的 Windows Hello。 在这种情况下，Azure AD CloudAP 插件是 PRT 的主要权限。
* **Azure AD 注册设备**：当用户将辅助工作帐户添加到其 Windows 10 设备时，将发出 PRT。 用户可以以两种不同的方式向 Windows 10 添加帐户 -  
   * 登录到应用后，通过 **"在此设备上的任何地方使用此帐户**"添加帐户（例如，Outlook）
   * 从**设置** > **帐户** > **访问工作或学校** > **连接**添加帐户

在 Azure AD 注册设备方案中，Azure AD WAM 插件是 PRT 的主要权限，因为此 Azure AD 帐户未发生 Windows 登录。

> [!NOTE]
> 第三方标识提供程序需要支持 WS-Trust 协议，以便在 Windows 10 设备上启用 PRT 发行。 如果没有 WS 信任，PRT 将无法颁发给混合 Azure AD 联接或 Azure AD 联接设备上的用户

## <a name="what-is-the-lifetime-of-a-prt"></a>PRT 的寿命是多少？

一旦发出，PRT 的有效期为 14 天，只要用户主动使用设备，它就会持续续订。  

## <a name="how-is-a-prt-used"></a>如何使用 PRT？

PRT 由 Windows 中的两个关键组件使用：

* **Azure AD CloudAP 插件**：在 Windows 登录期间，Azure AD CloudAP 插件使用用户提供的凭据从 Azure AD 请求 PRT。 它还缓存 PRT，以便在用户无法访问互联网连接时启用缓存登录。
* **Azure AD WAM 插件**：当用户尝试访问应用程序时，Azure AD WAM 插件使用 PRT 在 Windows 10 上启用 SSO。 Azure AD WAM 插件使用 PRT 请求刷新并访问依赖 WAM 进行令牌请求的应用程序的令牌。 它还通过将 PRT 注入浏览器请求，在浏览器上启用 SSO。 微软边缘（本机）和 Chrome（通过 Windows 10 帐户或办公室联机扩展名）支持 Windows 10 中的浏览器 SSO。

## <a name="how-is-a-prt-renewed"></a>如何续订 PRT？

PRT 以两种不同的方法续订：

* **Azure AD CloudAP 插件每 4 小时一次**：在 Windows 登录期间，云AP 插件每 4 小时更新一次 PRT。 如果用户在此期间没有互联网连接，CloudAP 插件将在设备连接到互联网后续订 PRT。
* **Azure AD WAM 插件在应用令牌请求期间**： WAM 插件通过为应用程序启用静默令牌请求，在 Windows 10 设备上启用 SSO。 WAM 插件可以在这些令牌请求期间以两种不同的方式续订 PRT：
   * 应用以静默方式请求 WAM 访问令牌，但没有可用于该应用的刷新令牌。 在这种情况下，WAM 使用 PRT 请求应用的令牌，并在响应中返回新的 PRT。
   * 应用请求 WAM 获取访问令牌，但 PRT 无效或 Azure AD 需要其他授权（例如，Azure 多重身份验证）。 在这种情况下，WAM 启动交互式登录，要求用户重新验证或提供其他验证，并在成功身份验证时颁发新的 PRT。

### <a name="key-considerations"></a>重要注意事项

* PRT 仅在本机应用身份验证期间颁发和续订。 PRT 不会在浏览器会话期间续订或颁发。
* 在 Azure AD 联接和混合 Azure AD 联接设备中，CloudAP 插件是 PRT 的主要权限。 如果在基于 WAM 的令牌请求期间续订 PRT，则 PRT 将发送回 CloudAP 插件，该插件在接受之前使用 Azure AD 验证 PRT 的有效性。

## <a name="how-is-the-prt-protected"></a>如何保护 PRT？

PRT 通过绑定到用户已登录的设备进行保护。 Azure AD 和 Windows 10 通过以下方法启用 PRT 保护：

* **在第一次登录**期间：在第一次登录期间，使用设备注册期间加密生成的设备密钥，通过签名请求发出 PRT。 在具有有效和正常运行的 TPM 的设备上，设备密钥由 TPM 保护，以防止任何恶意访问。 如果无法验证相应的设备密钥签名，则不发出 PRT。
* **在令牌请求和续订期间**：发出 PRT 时，Azure AD 还会向设备发出加密的会话密钥。 它使用生成的公共传输密钥 （tkpub） 进行加密，并作为设备注册的一部分发送到 Azure AD。 此会话密钥只能由 TPM 保护的专用传输密钥 （tkpriv） 解密。 会话密钥是发送到 Azure AD 的任何请求的占有证明 （POP） 密钥。  会话密钥还受 TPM 保护，没有其他操作系统组件可以访问它。 令牌请求或 PRT 续订请求由此会话密钥通过 TPM 安全签名，因此不能被篡改。 Azure AD 将使来自设备的任何未由相应会话密钥签名的请求无效。

通过使用 TPM 保护这些密钥，恶意参与者无法窃取密钥，也不能在其他地方重播 PRT，因为即使攻击者拥有设备，TPM 也无法访问。  因此，使用 TPM 可大大提高 Azure AD 加入、混合 Azure AD 联接和 Azure AD 注册设备的安全性，防止凭据被盗。 为性能和可靠性，TPM 2.0 是 Windows 10 上所有 Azure AD 设备注册方案的推荐版本。

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>如何保护应用令牌和浏览器 Cookie？

**应用令牌**：当应用通过 WAM 请求令牌时，Azure AD 会发出刷新令牌和访问令牌。 但是，WAM 仅将访问令牌返回到应用，并通过使用用户的数据保护应用程序编程接口 （DPAPI） 密钥加密刷新令牌来保护其缓存中的刷新令牌。 WAM 使用会话密钥对请求进行签名，从而安全地使用刷新令牌来颁发进一步的访问令牌。 DPAPI 密钥由 Azure AD 本身中基于 Azure AD 的对称密钥保护。 当设备需要使用 DPAPI 密钥解密用户配置文件时，Azure AD 提供由会话密钥加密的 DPAPI 密钥，CloudAP 插件请求 TPM 解密该密钥。 此功能可确保在保护刷新令牌时保持一致性，并避免应用程序实现自己的保护机制。  

**浏览器 Cookie**： 在 Windows 10 中，Azure AD 支持浏览器 SSO 在互联网浏览器和微软边缘本机或在谷歌 Chrome 通过 Windows 10 帐户扩展。 安全性不仅用于保护 Cookie，还用于保护 Cookie 发送到的终结点。 浏览器 Cookie 的保护方式与 PRT 一样，通过使用会话密钥对 Cookie 进行签名和保护。

当用户启动浏览器交互时，浏览器（或扩展）将调用 COM 本机客户端主机。 本机客户端主机可确保页面来自允许的域之一。 浏览器可以向本机客户端主机发送其他参数，包括 nonce，但本机客户端主机保证对主机名的验证。 本机客户端主机从 CloudAP 插件请求 PRT-cookie，该插件使用受 TPM 保护的会话密钥创建并签名。 由于 PRT-cookie 由会话密钥签名，因此无法篡改。 此 PRT-cookie 包含在 Azure AD 的请求标头中，以验证其源自的设备。 如果使用 Chrome 浏览器，则只有本机客户端主机清单中显式定义的扩展才能调用它，防止任意扩展发出这些请求。 Azure AD 验证 PRT Cookie 后，它会向浏览器发出会话 Cookie。 此会话 Cookie 还包含使用 PRT 颁发的同一会话密钥。 在后续请求中，会话密钥得到验证，有效地将 Cookie 绑定到设备，并阻止从其他地方重播。

## <a name="when-does-a-prt-get-an-mfa-claim"></a>PRT 何时获得 MFA 索赔？

PRT 可以在特定方案中获得多重身份验证 （MFA） 声明。 当基于 MFA 的 PRT 用于请求应用程序的令牌时，MFA 声明将传输到这些应用令牌。 此功能通过防止每个需要它的应用的 MFA 挑战，为用户提供无缝体验。 PRT 可以通过以下方式获得 MFA 索赔：

* **使用 Windows Hello 为企业**登录： 适用于企业的 Windows Hello 替换密码并使用加密密钥提供强大的双重身份验证。 适用于企业的 Windows Hello 特定于设备上的用户，并且本身需要 MFA 来预配。 当用户使用 Windows Hello 业务登录时，用户的 PRT 将获得 MFA 声明。 如果智能卡身份验证从 ADFS 生成 MFA 声明，则此方案也适用于使用智能卡登录的用户。
   * 由于适用于企业的 Windows Hello 被视为多重身份验证，因此在刷新 PRT 本身时，MFA 声明会更新，因此当用户使用 WIndows Hello Business 登录时，MFA 持续时间将持续延长
* **MFA 在 WAM 交互式登录期间**： 通过 WAM 进行令牌请求期间，如果用户需要执行 MFA 才能访问应用，则在此交互期间续订的 PRT 将带有 MFA 声明。
   * 在这种情况下，MFA 声明不会持续更新，因此 MFA 持续时间基于目录中设置的生存期。
   * 当以前的现有 PRT 和 RT 用于访问应用时，PRT 和 RT 将被视为身份验证的第一个证明。 需要一个新的AT，有第二个证据和印记MFA索赔。 这还将发布新的 PRT 和 RT。
* **设备注册期间的 MFA：** 如果管理员在 Azure AD 中配置了设备设置[，要求 MFA 注册设备](device-management-azure-portal.md#configure-device-settings)，则用户需要执行 MFA 才能完成注册。 在此过程中，向用户发出的 PRT 在注册期间获得了 MFA 索赔。 此功能仅适用于执行联接操作的用户，不适用于登录到该设备的其他用户。
   * 与 WAM 交互式登录类似，MFA 声明不会持续更新，因此 MFA 持续时间基于目录上设置的生存期。

Windows 10 维护每个凭据的已分区 PRT 列表。 因此，每个 Windows Hello 企业、密码或智能卡都有一个 PRT。 此分区可确保 MFA 声明根据使用的凭据隔离，不会在令牌请求期间混合。

## <a name="how-is-a-prt-invalidated"></a>PRT 如何失效？

在以下情况下，PRT 无效：

* **无效用户**：如果在 Azure AD 中删除或禁用用户，则其 PRT 将失效，不能用于获取应用程序的令牌。 如果已删除或禁用的用户之前已登录到设备，缓存的登录将登录，直到 CloudAP 知道其无效状态。 一旦 CloudAP 确定用户无效，它将阻止后续登录。 无效用户将自动阻止登录到未缓存其凭据的新设备。
* **无效设备**：如果在 Azure AD 中删除或禁用设备，则在该设备上获得的 PRT 将失效，不能用于获取其他应用程序的令牌。 如果用户已登录到无效设备，他们可以继续这样做。 但是，设备上的所有令牌都无效，并且用户没有 SSO 到该设备的任何资源。
* **密码更改**：用户更改其密码后，使用前一个密码获取的 PRT 将由 Azure AD 失效。 密码更改会导致用户获得新的 PRT。 这种失效可以通过两种不同的方式发生：
   * 如果用户使用新密码登录到 Windows，CloudAP 将丢弃旧的 PRT，并请求 Azure AD 使用新密码颁发新 PRT。 如果用户没有互联网连接，则无法验证新密码，Windows 可能要求用户输入其旧密码。
   * 如果用户在登录到 Windows 后使用旧密码登录或更改了密码，则旧 PRT 将用于任何基于 WAM 的令牌请求。 在这种情况下，系统会提示用户在 WAM 令牌请求期间重新进行身份验证，并颁发新的 PRT。
* **TPM 问题**：有时，设备的 TPM 可能会抖抖或失败，从而导致 TPM 保护的密钥无法访问。 在这种情况下，设备无法获取 PRT 或使用现有 PRT 请求令牌，因为它无法证明拥有加密密钥。 因此，任何现有的 PRT 都因 Azure AD 而失效。 当 Windows 10 检测到故障时，它会启动恢复流以使用新的加密密钥重新注册设备。 使用混合 Azure 广告联接，就像初始注册一样，恢复在没有用户输入的情况下静默进行。 对于 Azure AD 已加入或 Azure AD 注册设备，恢复需要由在设备上具有管理员权限的用户执行。 在这种情况下，恢复流由 Windows 提示启动，该提示引导用户成功恢复设备。

## <a name="detailed-flows"></a>详细流程

下图说明了颁发、续订和使用 PRT 请求应用程序访问令牌的基本详细信息。 此外，这些步骤还描述了在这些交互过程中如何应用上述安全机制。

### <a name="prt-issuance-during-first-sign-in"></a>首次登录期间的 PRT 发行

![PRT 在第一次签署期间在详细流中发出](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> 在 Azure AD 联接的设备上，此交换同步发生，以在用户可以登录到 Windows 之前发出 PRT。 在混合 Azure AD 联接设备中，本地活动目录是主要权限。 因此，用户只等待，直到他们可以获取 TGT 登录，而 PRT 发行以异步方式进行。 此方案不适用于 Azure AD 注册设备，因为登录不使用 Azure AD 凭据。

| 步骤 | 描述 |
| :---: | --- |
| A | 用户在 UI 中的登录名中输入其密码。 LogonUI 将 auth 缓冲区中的凭据传递给 LSA，LSA 在内部传递到 CloudAP。 CloudAP 将此请求转发给 CloudAP 插件。 |
| B | CloudAP 插件启动域发现请求，以标识用户的标识提供程序。 如果用户的租户具有联合提供程序设置，Azure AD 将返回联合提供程序的元数据交换终结点 （MEX）。 如果没有，Azure AD 将返回用户被管理，指示用户可以使用 Azure AD 进行身份验证。 |
| C | 如果用户已管理，CloudAP 将从 Azure AD 获取 nonce。 如果用户是联合的，CloudAP 插件会向联合提供商请求具有用户凭据的 SAML 令牌。 一旦它收到 SAML 令牌，它将请求 Azure AD 的 nonce。 |
| D | CloudAP 插件使用用户的凭据 nonce 和代理作用域构造身份验证请求，使用设备密钥 （dkpriv） 对请求进行签名，并将其发送到 Azure AD。 在联合环境中，CloudAP 插件使用联合提供程序返回的 SAML 令牌，而不是用户的凭据。 |
| E | Azure AD 验证用户凭据、nonce 和设备签名，验证设备在租户中是否有效并颁发加密 PRT。 与 PRT 一起，Azure AD 还会发出一个对称密钥，称为 Azure AD 使用传输密钥 （tkpub） 加密的会话密钥。 此外，会话密钥也嵌入 PRT 中。 此会话密钥充当 PRT 后续请求的占有证明 （PoP） 密钥。 |
| F | CloudAP 插件将加密 PRT 和会话密钥传递给 CloudAP。 CloudAP 请求 TPM 使用传输密钥 （tkpriv） 解密会话密钥，并使用 TPM 自己的密钥重新加密它。 CloudAP 将加密会话密钥与 PRT 一起存储在其缓存中。 |

### <a name="prt-renewal-in-subsequent-logons"></a>后续登录中的 PRT 续订

![后续登录中的 PRT 续订](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| 步骤 | 描述 |
| :---: | --- |
| A | 用户在 UI 中的登录名中输入其密码。 LogonUI 将 auth 缓冲区中的凭据传递给 LSA，LSA 在内部传递到 CloudAP。 CloudAP 将此请求转发给 CloudAP 插件。 |
| B | 如果用户以前登录到该用户，Windows 将启动缓存登录并验证凭据以登录用户。 每 4 小时，CloudAP 插件会异步启动 PRT 续订。 |
| C | CloudAP 插件启动域发现请求，以标识用户的标识提供程序。 如果用户的租户具有联合提供程序设置，Azure AD 将返回联合提供程序的元数据交换终结点 （MEX）。 如果没有，Azure AD 将返回用户被管理，指示用户可以使用 Azure AD 进行身份验证。 |
| D | 如果用户是联合的，CloudAP 插件会向联合提供商请求具有用户凭据的 SAML 令牌。 一旦它收到 SAML 令牌，它将请求 Azure AD 的 nonce。 如果用户是托管的，CloudAP 将直接从 Azure AD 获取 nonce。 |
| E | CloudAP 插件使用用户的凭据 nonce 和现有 PRT 构造身份验证请求，使用会话密钥对请求进行签名，并将其发送到 Azure AD。 在联合环境中，CloudAP 插件使用联合提供程序返回的 SAML 令牌，而不是用户的凭据。 |
| F | Azure AD 通过将其与 PRT 中嵌入的会话密钥进行比较来验证会话密钥签名，验证 nonce 并验证设备在租户中是否有效并颁发新的 PRT。 如前所示，PRT 再次附带由传输密钥 （tkpub） 加密的会话密钥。 |
| G | CloudAP 插件将加密 PRT 和会话密钥传递给 CloudAP。 CloudAP 请求 TPM 使用传输密钥 （tkpriv） 解密会话密钥，并使用 TPM 自己的密钥重新加密它。 CloudAP 将加密会话密钥与 PRT 一起存储在其缓存中。 |

### <a name="prt-usage-during-app-token-requests"></a>应用令牌请求期间的 PRT 使用

![应用令牌请求期间的 PRT 使用](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| 步骤 | 描述 |
| :---: | --- |
| A | 应用程序（例如 Outlook、OneNote 等）向 WAM 发起令牌请求。 WAM 反过来要求 Azure AD WAM 插件为令牌请求提供服务。 |
| B | 如果应用程序的刷新令牌已可用，Azure AD WAM 插件会使用它请求访问令牌。 为了提供设备绑定证明，WAM 插件使用会话密钥对请求进行签名。 Azure AD 验证会话密钥，并为应用颁发访问令牌和新的刷新令牌，由会话密钥加密。 WAM 插件请求 Cloud AP 插件解密令牌，而令牌又请求 TPM 使用会话密钥解密，从而导致 WAM 插件同时获取两个令牌。 接下来，WAM 插件仅提供对应用程序的访问令牌，同时使用 DPAPI 重新加密刷新令牌并将其存储在自己的缓存中  |
| C |  如果应用程序的刷新令牌不可用，Azure AD WAM 插件将使用 PRT 请求访问令牌。 为了提供拥有证明，WAM 插件使用会话密钥在包含 PRT 的请求上签名。 Azure AD 通过将其与 PRT 中嵌入的会话密钥进行比较来验证会话密钥签名，验证设备是否有效，并为应用程序颁发访问令牌和刷新令牌。 此外，Azure AD 可以发布新的 PRT（基于刷新周期），所有这些 PRT 都由会话密钥加密。 |
| D | WAM 插件请求 Cloud AP 插件解密令牌，而令牌又请求 TPM 使用会话密钥解密，从而导致 WAM 插件同时获取两个令牌。 接下来，WAM 插件仅提供对应用程序的访问令牌，同时使用 DPAPI 重新加密刷新令牌并将其存储在自己的缓存中。 WAM 插件将为此应用程序使用刷新令牌。 WAM 插件还会将新的 PRT 回馈给云 AP 插件，该插件在在其自己的缓存中更新 PRT 之前，使用 Azure AD 对其进行验证。 云 AP 插件将未来使用新的 PRT。 |
| E | WAM 向 WAM 提供新颁发的访问令牌，而 WAM 又将其发回调用应用程序|

### <a name="browser-sso-using-prt"></a>使用 PRT 的浏览器 SSO

![使用 PRT 的浏览器 SSO](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| 步骤 | 描述 |
| :---: | --- |
| A | 用户使用凭据登录到 Windows 以获取 PRT。 用户打开浏览器后，浏览器（或扩展）将从注册表加载 URL。 |
| B | 当用户打开 Azure AD 登录 URL 时，浏览器或扩展将验证 URL 与从注册表获取的 URL。 如果它们匹配，浏览器将调用本机客户端主机以获取令牌。 |
| C | 本机客户端主机验证 URL 是否属于 Microsoft 标识提供程序（Microsoft 帐户或 Azure AD），提取从 URL 发送的 nonce，并调用 CloudAP 插件以获取 PRT Cookie。 |
| D | CloudAP 插件将创建 PRT Cookie，使用 TPM 绑定会话密钥登录，并将其发送回本机客户端主机。 由于 Cookie 由会话密钥签名，因此无法篡改它。 |
| E | 本机客户端主机将此 PRT Cookie 返回到浏览器，浏览器将包含它作为称为 x-ms-RefreshToken 凭据的请求标头的一部分，并从 Azure AD 请求令牌。 |
| F | Azure AD 验证 PRT Cookie 上的会话密钥签名，验证 nonce，验证设备在租户中是否有效，并为网页发出 ID 令牌和浏览器的加密会话 Cookie。 |

## <a name="next-steps"></a>后续步骤

有关解决 PRT 相关问题疑难解答的详细信息，请参阅有关[排除混合 Azure 活动目录加入 Windows 10 和 Windows Server 2016 设备](troubleshoot-hybrid-join-windows-current.md)的文章。
