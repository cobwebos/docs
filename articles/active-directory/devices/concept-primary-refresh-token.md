---
title: 主刷新令牌 (PRT) 和 Azure AD - Azure Active Directory
description: 在 Azure Active Directory 中，主刷新令牌 (PRT) 有什么作用？我们如何管理此类令牌？
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
ms.openlocfilehash: 3ccd51bd69c982aeae25dbf52d1e5d076542cf35
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83771190"
---
# <a name="what-is-a-primary-refresh-token"></a>什么是主刷新令牌？

主刷新令牌 (PRT) 是 Windows 10、iOS 和 Android 设备上 Azure AD 身份验证的关键项目。 它是专门颁发给 Microsoft 第一方令牌代理的 JSON Web 令牌 (JWT)，用于在这些设备上使用的应用程序之间实现单一登录 (SSO)。 本文将详细介绍如何在 Windows 10 设备上颁发、使用和保护 PRT。

本文假设你已了解 Azure AD 中的不同设备状态以及 Windows 10 中单一登录的工作原理。 要详细了解 Azure AD 中的设备，请参阅 [Azure Active Directory 中的设备管理是什么？](overview.md)

## <a name="key-terminology-and-components"></a>关键术语和组件

以下 Windows 组件在请求和使用 PRT 时起着重要作用：

* **云身份验证提供程序** (CloudAP)：CloudAP 是 Windows 登录的新式身份验证提供程序，用于验证登录到 Windows 10 设备的用户。 CloudAP 提供一个插件框架，可基于该框架构建标识提供者，以便使用该标识提供者的凭据完成 Windows 身份验证。
* **Web 帐户管理器** (WAM)：WAM 是 Windows 10 设备上的默认令牌代理。 WAM 也提供一个插件框架，可基于该框架构建标识提供者，并为依赖该标识提供者的应用程序启用 SSO。
* **Azure AD CloudAP 插件**：基于 CloudAP 框架构建的 Azure AD 特定插件，用于在 Windows 登录期间通过 Azure AD 验证用户凭据。
* **Azure AD WAM 插件**：基于 WAM 框架构建的 Azure AD 特定插件，可为依赖于 Azure AD 进行身份验证的应用程序启用 SSO。
* **Dsreg**：Windows 10 上的 Azure AD 特定组件，用于处理所有设备状态的设备注册过程。
* **受信任的平台模块** (TPM)：TPM 是内置于设备的硬件组件，为用户和设备机密提供基于硬件的安全功能。 可在[受信任的平台模块技术概述](/windows/security/information-protection/tpm/trusted-platform-module-overview)一文中查看更多详细信息。

## <a name="what-does-the-prt-contain"></a>PRT 包含哪些内容？

PRT 包含任何 Azure AD 刷新令牌中通常包含的声明。 此外，PRT 中还包含一些特定于设备的声明。 这些限制如下：

* **设备 ID**：PRT 将颁发给特定设备上的用户。 设备 ID 声明 `deviceID` 确定要将 PRT 颁发给哪台设备上的用户。 此声明稍后将颁发给通过 PRT 获取的令牌。 设备 ID 声明用于根据设备状态或合规性来确定条件性访问的授权。
* **会话密钥**：会话密钥是 Azure AD 身份验证服务生成的加密对称密钥，作为 PRT 的一部分颁发。 当使用 PRT 为其他应用程序获取令牌时，会话密钥将用作所有权证明。

### <a name="can-i-see-whats-in-a-prt"></a>我可以看到 PRT 中的内容吗？

PRT 是从 Azure AD 发送的不透明 blob，其内容对于任何客户端组件都是不可见的。 你无法看到 PRT 中的内容。

## <a name="how-is-a-prt-issued"></a>PRT 是如何颁发的？

在 Azure AD 中，设备注册是基于设备的身份验证的必备条件。 仅对已注册设备上的用户颁发 PRT。 有关设备注册的更深入详细信息，请参阅 [Windows Hello 企业版和设备注册](/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration)一文。 在设备注册过程中，dsreg 组件会生成两组加密密钥对：

* 设备密钥 (dkpub/dkpriv)
* 传输密钥 (tkpub/tkpriv)

如果设备具有有效且正常的 TPM，则私钥会绑定到设备的 TPM，而公钥将在设备注册过程中发送到 Azure AD。 这些密钥用于在 PRT 请求中验证设备状态。

在以下两种方案中，当用户在 Windows 10 设备上进行身份验证时，会颁发 PRT：

* **已建立 Azure AD 联接**或**已建立混合 Azure AD 联接**：当用户使用组织凭据登录时，在 Windows 登录过程中将颁发 PRT。 使用 Windows 10 支持的所有凭据（例如密码和 Windows Hello 企业版）颁发 PRT。 在此方案中，Azure AD CloudAP 插件是 PRT 的主要颁发机构。
* **Azure AD 注册设备**：当用户将辅助工作帐户添加到 Windows 10 设备时，将颁发 PRT。 用户可以通过两种不同的方式将帐户添加到 Windows 10：  
   * 登录到某个应用（例如 Outlook）后，会出现“在此设备上的所有位置使用此帐户”提示，通过该提示添加帐户
   * 通过“设置” > “帐户” > “访问工作或学校” > “连接”添加帐户   

在设备已注册 Azure AD 的方案中，Azure AD WAM 插件是 PRT 的主要颁发机构，因为此 Azure AD 帐户未发生 Windows 登录。

> [!NOTE]
> 第三方标识提供者需要支持 WS-Trust 协议，才能在 Windows 10 设备上颁发 PRT。 若没有 WS-Trust，则无法将 PRT 颁发给已建立混合 Azure AD 联接或已建立 Azure AD 联接的设备上的用户

## <a name="what-is-the-lifetime-of-a-prt"></a>PRT 的生存期有多长？

一经颁发，PRT 的有效期为 14 天，只要用户活跃地使用该设备，就会持续地续订。  

## <a name="how-is-a-prt-used"></a>如何使用 PRT？

PRT 由 Windows 中的两个关键组件使用：

* **Azure AD CloudAP 插件**：在 Windows 登录过程中，Azure AD CloudAP 插件使用用户提供的凭据从 Azure AD 请求一个 PRT。 当用户无权访问 Internet 连接时，它还会缓存 PRT 以启用缓存的登录。
* **Azure AD WAM 插件**：当用户尝试访问应用程序时，Azure AD WAM 插件将使用 PRT 在 Windows 10 上启用 SSO。 Azure AD WAM 插件使用 PRT 为依赖 WAM 来请求令牌的应用程序请求刷新和访问令牌。 它还通过将 PRT 注入浏览器请求在浏览器上启用 SSO。 Microsoft Edge（原生）和 Chrome（通过 [Windows 10 帐户](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji?hl=en)或 [Office Online](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb?hl=en) 扩展）支持 Windows 10 中的浏览器 SSO。

## <a name="how-is-a-prt-renewed"></a>如何续订 PRT？

PRT 通过两种不同的方法续订：

* **通过 Azure AD CloudAP 插件每 4 小时续订一次**：在 Windows 登录过程中，CloudAP 插件每 4 小时就会续订一次 PRT。 如果在这段时间内用户没有 Internet 连接，CloudAP 插件将在设备连接到 Internet 后续订 PRT。
* **通过 Azure AD WAM 插件在应用令牌请求中续订**：WAM 插件通过启用应用程序的无提示令牌请求，在 Windows 10 设备上启用 SSO。 WAM 插件可以通过两种不同的方式在这些令牌请求中续订 PRT：
   * 应用以无提示的方式向 WAM 请求访问令牌，但没有该应用可用的刷新令牌。 在这种情况下，WAM 使用 PRT 请求应用的令牌，并在响应中返回新的 PRT。
   * 应用向 WAM 请求访问令牌，但 PRT 无效或 Azure AD 需要额外的授权（例如 Azure 多重身份验证）。 在此方案中，WAM 会启动交互式登录，要求用户重新进行身份验证或提供附加验证，并会在身份验证成功后颁发新的 PRT。

### <a name="key-considerations"></a>重要注意事项

* 仅在原生应用身份验证期间颁发并续订 PRT。 在浏览器会话期间，不会续订或颁发 PRT。
* 在已建立 Azure AD 连接和已建立混合 Azure AD 联接的设备中，CloudAP 插件是 PRT 的主要颁发机构。 如果在基于 WAM 的令牌请求中续订了 PRT，则会将 PRT 发回 CloudAP 插件，该插件会在接受该 PRT 之前使用 Azure AD 验证其有效性。

## <a name="how-is-the-prt-protected"></a>PRT 是如何受到保护的？

通过将 PRT 绑定到用户已登录的设备，对其进行保护。 Azure AD 和 Windows 10 通过以下方法实现对 PRT 的保护：

* **首次登录期间**：首次登录时，会使用在设备注册过程以加密方式生成的设备密钥对请求进行签名，从而颁发 PRT。 在具有有效且正常的 TPM 的设备上，TPM 会保护设备密钥以阻止所有恶意访问。 如果无法验证相应的设备密钥签名，则不会颁发 PRT。
* **在令牌请求和续订过程中**：颁发 PRT 时，Azure AD 还会向设备颁发一个加密会话密钥。 它通过生成的公共传输密钥 (tkpub) 进行加密，并在设备注册过程中发送到 Azure AD。 此会话密钥只能由受 TPM 保护的专用传输密钥 (tkpriv) 解密。 会话密钥是发送到 Azure AD 的任何请求的所有权证明 (POP) 密钥。  会话密钥也受 TPM 保护，并且其他 OS 组件都不能访问它。 令牌请求或 PRT 续订请求通过 TPM 由该会话密钥安全地签名，以此确保不会被篡改。 若设备发出的请求未由相应的会话密钥签名，则 Azure AD 将使之无效。

通过使用 TPM 保护这些密钥，恶意活动的参与者就不能盗取密钥，也无法在其他位置重播 PRT，因为即使攻击者已实际占用了该设备，也无法访问 TPM。  因此，使用 TPM 极大地增强了已建立 Azure AD 联接、已建立混合 Azure AD 联接以及已注册 Azure AD 的设备的安全性，可防止凭据被盗。 至于性能和可靠性，Windows 10 上的所有 Azure AD 设备注册方案都推荐使用 TPM 2.0 版。

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>应用令牌和浏览器 cookie 是如何受到保护的？

**应用令牌**：当应用通过 WAM 请求令牌时，Azure AD 会颁发一个刷新令牌和一个访问令牌。 但是，WAM 只会将访问令牌返回到应用，而在缓存中保护刷新令牌，方法是使用用户的数据保护应用程序编程接口 (DPAPI) 密钥来对其进行加密。 WAM 通过使用会话密钥对请求进行签名来安全地使用刷新令牌，以进一步颁发访问令牌。 DPAPI 密钥由 Azure AD 中基于 Azure AD 的对称密钥保护。 当设备需要使用 DPAPI 密钥对用户配置文件进行解密时，Azure AD 提供由会话密钥加密的 DPAPI 密钥，CloudAP 插件会请求 TPM 对其进行解密。 此功能可在保护刷新令牌方面确保一致性，并避免应用程序实现自己的保护机制。  

**浏览器 cookie**：在 Windows 10 中，Azure AD 以原生方式支持 Internet Explorer 和 Microsoft Edge 中的浏览器 SSO，或通过 Windows 10 帐户扩展支持 Google Chrome 中的浏览器 SSO。 建立安全性不仅是为了保护 cookie，还可以保护要将 cookie 发送到的终结点。 浏览器 cookie 的保护方式与 PRT 相同，也是使用会话密钥对 cookie 进行签名和保护。

当用户启动浏览器交互时，浏览器（或扩展）会调用 COM 原生客户端主机。 原生客户端主机确保该页面来自允许的域。 浏览器可以将其他参数发送到原生客户端主机（包括 nonce），但原生客户端主机保证对主机名进行验证。 原生客户端主机从 CloudAP 插件请求 PRT-cookie，此插件使用受 TPM 保护的会话密钥创建 PRT-cookie 并对其进行签名。 因为 PRT-cookie 由会话密钥签名，所以不会被篡改。 此 PRT-cookie 包括在 Azure AD 的请求标头中，用于验证发出请求的设备。 如果使用的是 Chrome 浏览器，则只有在原生客户端主机的清单中显式定义的扩展才能调用它，从而防止任意扩展发出这些请求。 Azure AD 验证 PRT cookie 后，会向浏览器颁发会话 cookie。 此会话 cookie 还包含使用 PRT 颁发的相同会话密钥。 在后续请求中，会验证会话密钥，以将 cookie 绑定到设备，并阻止在其他位置重播。

## <a name="when-does-a-prt-get-an-mfa-claim"></a>PRT 何时获得 MFA 声明？

在特定方案中，PRT 可以获取多重身份验证 (MFA) 声明。 当使用基于 MFA 的 PRT 请求应用程序的令牌时，MFA 声明会传输到这些应用令牌。 此功能可为每个需要 MFA 质询的应用阻止 MFA 质询，从而为用户提供无缝体验。 PRT 可以通过下列方式获取 MFA 声明：

* **使用 Windows Hello 企业版登录**：Windows Hello 企业版替换密码，并使用加密密钥来提供强双重身份验证。 Windows Hello 企业版特定于设备上的用户，且其自身需要 MFA 才能进行预配。 当用户使用 Windows Hello 企业版登录时，用户的 PRT 会获取 MFA 声明。 此方案还适用于使用智能卡登录的用户（如果智能卡身份验证通过 ADFS 生成 MFA 声明）。
   * 由于 Windows Hello 企业版被视为多重身份验证，因此在刷新 PRT 本身时，会更新 MFA 声明，当用户登录到 WIndows Hello 企业版时，MFA 的持续时间会不断延长
* **WAM 交互式登录中的 MFA**：在通过 WAM 请求令牌时，如果用户需要进行 MFA 才能访问应用，则在此交互过程中续订的 PRT 将带有 MFA 声明。
   * 在这种情况下，MFA 声明不会持续更新，因此，MFA 的持续时间取决于目录上设置的生存期。
   * 使用已有的 PRT 和 RT 访问应用时，PRT 和 RT 将被视为身份验证的第一项证明。 将需要具有第二项证明和 MFA 声明的新 AT。 这还将颁发新的 PRT 和 RT。
* **设备注册期间的 MFA**：如果管理员已将 Azure AD 中的设备设置配置为[需要完成 MFA 才能注册设备](device-management-azure-portal.md#configure-device-settings)，则用户需要进行 MFA 才能完成注册。 在此过程中，颁发给用户的 PRT 具有在注册期间获得的 MFA 声明。 此功能仅适用于执行联接操作的用户，不适用于登录到该设备的其他用户。
   * 与 WAM 交互式登录类似，MFA 声明不会持续更新，因此，MFA 的持续时间取决于目录上设置的生存期。

Windows 10 维护每个凭据的 PRT 分区列表。 Windows Hello 企业版、密码或智能卡中的每一个都有 PRT。 此分区确保根据使用的凭据隔离 MFA 声明，而不会在令牌请求过程中混合在一起。

## <a name="how-is-a-prt-invalidated"></a>PRT 会如何失效？

在以下方案中，PRT 会失效：

* **用户无效**：如果用户在 Azure AD 中被删除或禁用，则其 PRT 将失效，且无法用于获取应用程序的令牌。 如果已删除或已禁用的用户以前已登录到某个设备，则可以进行缓存登录，直到 CloudAP 识别其无效状态。 CloudAP 判定用户无效后，会阻止其再进行登录。 自动阻止无效的用户登录到未缓存凭据的新设备。
* **设备无效**：如果设备在 Azure AD 中被删除或禁用，则通过该设备获取的 PRT 将失效，且无法用于获取其他应用程序的令牌。 如果用户已登录到无效设备，则可以继续登录。 但设备上的所有令牌都已失效，并且用户不具有该设备中任何资源的 SSO。
* **密码更改**：用户更改其密码后，Azure AD 会将使用旧密码获取的 PRT 判定为无效。 当密码更改时，用户需要获取新的 PRT。 这种失效会发生于以下两种情况：
   * 如果用户使用新密码登录到 Windows，则 CloudAP 会弃用旧的 PRT 并使用新密码请求 Azure AD 颁发新的 PRT。 如果用户没有 Internet 连接，则无法验证新密码，Windows 可能会要求用户输入其旧密码。
   * 如果用户已使用旧密码登录或在登录 Windows 后更改了密码，则会将旧的 PRT 用于任何基于 WAM 的令牌请求。 在此方案中，系统会提示用户在 WAM 令牌请求中重新完成身份验证，并获得新的 PRT。
* **TPM 问题**：有时设备的 TPM 会出现故障，导致受 TPM 保护的密钥无法访问。 在这种情况下，设备不能使用现有的 PRT 获取 PRT 或请求令牌，因为不能证明自己拥有加密密钥。 所以 Azure AD 会将所有现有 PRT 判定为无效。 当 Windows 10 检测到故障时，它会启动一个恢复流，以使用新的加密密钥重新注册设备。 利用混合 Azure AD 联接，恢复会以无提示的方式进行，无需用户输入，这一点与初始注册相似。 对于已建立 Azure AD 联接或已注册 Azure AD 的设备，需要由对设备具有管理员权限的用户执行恢复。 在此方案中，系统会通过 Windows 提示启动恢复流，引导用户成功恢复设备。

## <a name="detailed-flows"></a>详细流程

下图展示了颁发、更新以及使用 PRT 来请求应用程序访问令牌的基本细节。 此外，这些步骤还描述了如何在这些交互过程中应用上述安全机制。

### <a name="prt-issuance-during-first-sign-in"></a>首次登录时的 PRT 颁发

![首次登录时颁发 PRT 的详细流程](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> 在已建立 Azure AD 联接的设备中，会同步进行此交换，以在用户登录到 Windows 之前颁发 PRT。 在已建立混合 Azure AD 联接的设备中，本地 Active Directory 是主要颁发机构。 所以用户仅等待获取 TGT 以登录，同时异步颁发 PRT。 此方案不适用于已注册 Azure AD 的设备，因为登录不使用 Azure AD 凭据。

| 步骤 | 说明 |
| :---: | --- |
| A | 用户在登录 UI 中输入自己的密码。 LogonUI 会将身份验证缓冲区中的凭据传递到 LSA，后者会在内部将其传递到 CloudAP。 CloudAP 会将此请求转发到 CloudAP 插件。 |
| B | CloudAP 插件会发起领域发现请求，以识别用户的标识提供者。 如果用户的租户安装了联合身份验证提供程序，Azure AD 将返回联合身份验证提供程序的元数据交换 (MEX) 终结点。 如果没有安装联合身份验证提供程序，Azure AD 将返回“用户是托管用户”的消息，表示用户可以使用 Azure AD 进行身份验证。 |
| C | 如果用户是托管用户，CloudAP 将从 Azure AD 获取 nonce。 如果用户是联合用户，则 CloudAP 插件将使用用户的凭据从联合身份验证提供程序请求 SAML 令牌。 收到 SAML 令牌后，会从 Azure AD 请求 nonce。 |
| D | CloudAP 插件使用用户的凭据、nonce 和代理作用域构造身份验证请求，使用设备密钥 (dkpriv) 对请求进行签名，并将其发送到 Azure AD。 在联合环境中，CloudAP 插件会使用联合身份验证提供程序返回的 SAML 令牌，而不是用户的凭据。 |
| E | Azure AD 验证用户凭据、nonce 和设备签名，验证设备在租户中是否有效，然后颁发加密的 PRT。 除了 PRT，Azure AD 还颁发对称密钥，称为“会话密钥”，该密钥由 Azure AD 使用传输密钥 (tkpub) 加密。 此外，会话密钥还会嵌入在 PRT 中。 在使用 PRT 进行后续请求时，该会话密钥将充当所有权证明 (PoP) 密钥。 |
| F | CloudAP 插件将加密的 PRT 和会话密钥传递到 CloudAP。 CloudAP 请求 TPM 使用传输密钥 (tkpriv) 对会话密钥进行解密，并使用 TPM 自己的密钥对其重新加密。 CloudAP 将加密的会话密钥与 PRT 一起存储在其缓存中。 |

### <a name="prt-renewal-in-subsequent-logons"></a>后续登录中的 PRT 续订

![后续登录中的 PRT 续订](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| 步骤 | 说明 |
| :---: | --- |
| A | 用户在登录 UI 中输入自己的密码。 LogonUI 会将身份验证缓冲区中的凭据传递到 LSA，后者会在内部将其传递到 CloudAP。 CloudAP 会将此请求转发到 CloudAP 插件。 |
| B | 如果用户以前已登录到该设备，则 Windows 将启动缓存登录并验证凭据以让用户登录。 CloudAP 插件每 4 小时异步启动一次 PRT 续订。 |
| C | CloudAP 插件会发起领域发现请求，以识别用户的标识提供者。 如果用户的租户安装了联合身份验证提供程序，Azure AD 将返回联合身份验证提供程序的元数据交换 (MEX) 终结点。 如果没有安装联合身份验证提供程序，Azure AD 将返回“用户是托管用户”的消息，表示用户可以使用 Azure AD 进行身份验证。 |
| D | 如果用户是联合用户，则 CloudAP 插件将使用用户的凭据从联合身份验证提供程序请求 SAML 令牌。 收到 SAML 令牌后，会从 Azure AD 请求 nonce。 如果用户是托管用户，CloudAP 将直接从 Azure AD 获取 nonce。 |
| E | CloudAP 插件使用用户的凭据、nonce 和现有 PRT 构造身份验证请求，使用会话密钥对请求进行签名，并将其发送到 Azure AD。 在联合环境中，CloudAP 插件会使用联合身份验证提供程序返回的 SAML 令牌，而不是用户的凭据。 |
| F | Azure AD 通过将会话密钥签名与 PRT 中嵌入的会话密钥进行比较来验证该签名，验证 nonce，并验证设备在租户中是否有效，然后颁发新的 PRT。 如上所示，PRT 再次附带由传输密钥 (tkpub) 加密的会话密钥。 |
| G | CloudAP 插件将加密的 PRT 和会话密钥传递到 CloudAP。 CloudAP 请求 TPM 使用传输密钥 (tkpriv) 对会话密钥进行解密，并使用 TPM 自己的密钥对其重新加密。 CloudAP 将加密的会话密钥与 PRT 一起存储在其缓存中。 |

### <a name="prt-usage-during-app-token-requests"></a>应用令牌请求过程中的 PRT 使用

![应用令牌请求过程中的 PRT 使用](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| 步骤 | 说明 |
| :---: | --- |
| A | 应用程序（例如 Outlook、OneNote 等）向 WAM 发出令牌请求。 随后 WAM 会要求 Azure AD WAM 插件处理该令牌请求。 |
| B | 如果应用程序的刷新令牌已可用，Azure AD WAM 插件将使用该令牌来请求访问令牌。 为了提供设备绑定的证明，WAM 插件会使用会话密钥对请求进行签名。 Azure AD 验证会话密钥，颁发应用的访问令牌和新的刷新令牌，并使用会话密钥进行加密。 WAM 插件请求 Cloud AP 插件对令牌进行解密，再由 Cloud AP 插件请求 TPM 使用会话密钥进行解密，从而让 WAM 插件获取这两个令牌。 接下来，WAM 插件仅提供应用程序的访问令牌，同时使用 DPAPI 重新加密刷新令牌并将其存储在自己的缓存中  |
| C |  如果应用程序的刷新令牌不可用，Azure AD WAM 插件将使用 PRT 来请求访问令牌。 为了提供所有权证明，WAM 插件会使用会话密钥对包含 PRT 的请求进行签名。 Azure AD 通过将会话密钥签名与 PRT 中嵌入的会话密钥进行比较来验证该签名，并验证设备是否有效，然后颁发应用程序的访问令牌和刷新令牌。 此外，Azure AD 可以基于刷新周期颁发新的 PRT，这些内容全都由会话密钥加密。 |
| D | WAM 插件请求 Cloud AP 插件对令牌进行解密，再由 Cloud AP 插件请求 TPM 使用会话密钥进行解密，从而让 WAM 插件获取这两个令牌。 接下来，WAM 插件仅提供应用程序的访问令牌，同时使用 DPAPI 重新加密刷新令牌并将其存储在自己的缓存中。 WAM 插件从此会将刷新密钥用于该应用程序。 WAM 插件还将新的 PRT 返回至 Cloud AP 插件，该插件会先通过 Azure AD 验证该 PRT，然后再在其自己的缓存中进行更新。 Cloud AP 插件从此将使用新的 PRT。 |
| E | WAM 向 WAM 提供新颁发的访问令牌，随后 WAM 又将其返回至调用应用程序|

### <a name="browser-sso-using-prt"></a>使用 PRT 的浏览器 SSO

![使用 PRT 的浏览器 SSO](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| 步骤 | 说明 |
| :---: | --- |
| A | 用户使用其凭据登录到 Windows 以获取 PRT。 用户打开浏览器后，浏览器（或扩展）将从注册表加载 URL。 |
| B | 当用户打开 Azure AD 登录 URL 时，浏览器或扩展会使用从注册表获取的 URL 来验证该 URL。 如果它们匹配，则浏览器调用原生客户端主机以获取令牌。 |
| C | 原生客户端主机验证 URL 是否属于 Microsoft 标识提供者（Microsoft 帐户或 Azure AD），提取从 URL 发送的 nonce，并调用 CloudAP 插件以获取 PRT cookie。 |
| D | CloudAP 插件将创建 PRT cookie，使用 TPM 绑定的会话密钥进行登录，然后将其发送回原生客户端主机。 因为 cookie 由会话密钥签名，所以不会被篡改。 |
| E | 原生客户端主机会将此 PRT cookie 返回到浏览器，浏览器会将其包含在名为 x-ms-RefreshTokenCredential 的请求标头中，并从 Azure AD 请求令牌。 |
| F | Azure AD 验证 PRT cookie 上的会话密钥签名，验证 nonce，验证设备在租户中是否有效，然后颁发网页的 ID 令牌和浏览器的已加密会话 cookie。 |

## <a name="next-steps"></a>后续步骤

要详细了解如何解决与 PRT 相关的问题，请参阅[排查已建立混合 Azure Active Directory 联接的 Windows 10 和 Windows Server 2016 设备问题](troubleshoot-hybrid-join-windows-current.md)一文。
