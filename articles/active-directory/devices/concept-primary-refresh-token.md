---
title: 主刷新令牌（PRT）和 Azure AD Azure Active Directory
description: 什么是角色，如何管理 Azure Active Directory 中的主刷新令牌（PRT）？
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
ms.openlocfilehash: 7b9240b863eef4d460cd8d3a47304fb96ffb4bc8
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77917767"
---
# <a name="what-is-a-primary-refresh-token"></a>什么是主刷新令牌？

主刷新令牌（PRT）是 Windows 10、iOS 和 Android 设备上 Azure AD 身份验证的关键项目。 它是专门颁发给 Microsoft 第一方令牌代理的 JSON Web 令牌（JWT），可在这些设备上使用的应用程序中启用单一登录（SSO）。 在本文中，我们将提供有关如何在 Windows 10 设备上发出、使用和保护 PRT 的详细信息。

本文假设你已了解 Azure AD 中可用的不同设备状态以及 Windows 10 中单一登录的工作原理。 有关 Azure AD 中的设备的详细信息，请参阅[什么是 Azure Active Directory 中的设备管理？](overview.md)

## <a name="key-terminology-and-components"></a>关键术语和组件

以下 Windows 组件在请求和使用 PRT 时起着重要作用：

* **云身份验证提供程序**（CloudAP）： CloudAP 是用于 windows 登录的新式身份验证提供程序，用于验证用户是否登录到 Windows 10 设备。 CloudAP 提供了一个插件框架，标识提供程序可以使用该框架生成，以便使用该标识提供者的凭据对 Windows 进行身份验证。
* **Web 帐户管理器**（WAM）： WAM 是 Windows 10 设备上的默认令牌代理。 WAM 还提供了一个可在其上构建标识提供程序的插件框架，并为依赖该标识提供程序的应用程序启用 SSO。
* **Azure AD CloudAP 插件**：在 CloudAP framework 上构建的 Azure AD 特定插件，用于在 Windows 登录期间使用 Azure AD 验证用户凭据。
* **AZURE AD WAM 插件**：在 WAM 框架上构建的 Azure AD 特定插件，该插件可启用依赖于 Azure AD 进行身份验证的应用程序。
* **Dsreg**： Windows 10 上的 Azure AD 特定组件，用于处理所有设备状态的设备注册过程。
* **受信任的平台模块**（tpm）： TPM 是内置于设备中的硬件组件，为用户和设备机密提供基于硬件的安全功能。 有关更多详细信息，请参阅[受信任的平台模块技术概述](https://docs.microsoft.com/windows/security/information-protection/tpm/trusted-platform-module-overview)。

## <a name="what-does-the-prt-contain"></a>PRT 包含哪些内容？

PRT 包含包含在任何 Azure AD 刷新令牌中的声明。 此外，PRT 中还包含一些特定于设备的声明。 这些限制如下：

* **设备 ID**：向特定设备上的用户颁发 PRT。 设备 ID 声明 `deviceID` 确定 PRT 颁发给用户的设备。 此声明稍后将颁发给通过 PRT 获取的令牌。 设备 ID 声明用于根据设备状态或合规性来确定条件性访问的授权。
* **会话密钥**：会话密钥是 Azure AD authentication 服务生成的加密对称密钥，作为 PRT 的一部分发出。 当使用 PRT 为其他应用程序获取令牌时，会话密钥将用作所有权证明。

### <a name="can-i-see-whats-in-a-prt"></a>我可以看到 PRT 的内容吗？

PRT 是从 Azure AD 发送的不透明 blob，其内容对于任何客户端组件都是未知的。 看不到 PRT 内部的内容。

## <a name="how-is-a-prt-issued"></a>如何发出 PRT？

在 Azure AD 中，设备注册是基于设备的身份验证的先决条件。 PRT 仅对已注册设备上的用户发出。 有关设备注册的更深入的详细信息，请参阅[Windows Hello 企业版和设备注册](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration)一文。 在设备注册过程中，dsreg 组件会生成两组加密密钥对：

* 设备密钥（dkpub/dkpriv）
* 传输密钥（tkpub/tkpriv）

如果设备具有有效且有效的 TPM，则私钥会绑定到设备的 TPM，而在设备注册过程中，公钥将发送到 Azure AD。 这些密钥用于在 PRT 请求期间验证设备状态。

在以下两种情况下，PRT 在 Windows 10 设备上的用户身份验证过程中发出：

* 联接或**混合 Azure AD 联接** **Azure AD** ：当用户使用其组织凭据登录时，将在 Windows 登录过程中发出 PRT。 使用所有 Windows 10 支持的凭据（例如，密码和 Windows Hello 企业版）发出 PRT。 在此方案中，Azure AD CloudAP 插件是 PRT 的主要颁发机构。
* **Azure AD 注册的设备**：当用户将辅助工作帐户添加到其 Windows 10 设备时，将发出 PRT。 用户可以通过两种不同的方式将帐户添加到 Windows 10-  
   * 在登录到应用（例如 Outlook）后，通过在**此设备上的任何位置使用此帐户**来添加帐户
   *  > **帐户**添加帐户 > **访问工作或学校** > **连接**

在 Azure AD 注册的设备方案中，Azure AD WAM 插件是 PRT 的主要权限，因为此 Azure AD 帐户未发生 Windows 登录。

> [!NOTE]
> 第三方标识提供者需要支持 WS 信任协议，才能在 Windows 10 设备上启用 PRT 颁发。 在没有 WS-TRUST 的情况下，无法将 PRT 颁发给混合 Azure AD 已加入或已加入 Azure AD 设备上的用户

## <a name="what-is-the-lifetime-of-a-prt"></a>PRT 的生存期是什么？

发出后，PRT 的有效期为14天，只要用户主动使用设备，就会持续续订。  

## <a name="how-is-a-prt-used"></a>如何使用 PRT？

Windows 中的两个关键组件使用 PRT：

* **Azure AD CloudAP 插件**：在 Windows 登录过程中，Azure AD CloudAP 插件使用用户提供的凭据从 AZURE AD 请求 PRT。 当用户无权访问 internet 连接时，它还会缓存 PRT 以启用缓存的登录。
* **AZURE AD WAM 插件**：当用户尝试访问应用程序时，Azure AD WAM 插件将使用 PRT 在 Windows 10 上启用 SSO。 Azure AD WAM 插件使用 PRT 为依赖于 WAM for token 请求的应用程序请求刷新和访问令牌。 它还通过将 PRT 注入浏览器请求来启用浏览器上的 SSO。 Windows 10 中的浏览器 SSO 在 Microsoft Edge （本机）和 Chrome （通过 Windows 10 帐户或 Office Online 扩展）上受支持。

## <a name="how-is-a-prt-renewed"></a>如何续订 PRT？

PRT 是通过两种不同的方法续订的：

* **每4小时 Azure AD CloudAP 插件**： CloudAP 插件在 Windows 登录期间每隔4小时续订 PRT。 如果在这段时间内用户没有 internet 连接，CloudAP 插件将在设备连接到 internet 后续订 PRT。
* **在应用令牌请求期间 AZURE AD WAM 插件**： WAM 插件通过启用应用程序的无提示令牌请求，在 Windows 10 设备上启用 SSO。 WAM 插件可以通过两种不同的方式在这些令牌请求期间续订 PRT：
   * 应用程序为 WAM 请求访问令牌，但没有可用于该应用程序的刷新令牌。 在这种情况下，WAM 使用 PRT 请求应用的令牌，并在响应中返回新的 PRT。
   * 应用程序请求 WAM 访问令牌，但 PRT 无效或 Azure AD 需要额外的授权（例如，Azure 多重身份验证）。 在这种情况下，WAM 会启动交互式登录，要求用户重新进行身份验证，或提供其他验证，并在身份验证成功后发出新的 PRT。

### <a name="key-considerations"></a>重要注意事项

* 仅在本机应用身份验证期间发出并续订 PRT。 在浏览器会话期间，不会续订或发出 PRT。
* 在已加入和混合 Azure AD Azure AD 联接的设备中，CloudAP 插件是 PRT 的主要颁发机构。 如果在基于 WAM 的令牌请求期间续订了 PRT，则会将 PRT 发回 CloudAP 插件，该插件会在接受 Azure AD 之前验证 PRT 的有效性。

## <a name="how-is-the-prt-protected"></a>PRT 如何受到保护？

通过将 PRT 绑定到用户已登录到的设备，对其进行保护。 Azure AD 和 Windows 10 通过以下方法启用 PRT 保护：

* **首次登录期间**：在首次登录期间，将使用设备注册过程中生成的设备密钥对请求进行签名，以 PRT。 在具有有效且正常工作的 TPM 的设备上，设备密钥受 TPM 保护，以防止任何恶意访问。 如果无法验证相应的设备密钥签名，则不会发出 PRT。
* **令牌请求和续订期间**：发出 PRT 时，Azure AD 还会向设备发出加密的会话密钥。 它通过生成的公共传输密钥（tkpub）进行加密，并在设备注册过程中发送到 Azure AD。 此会话密钥只能由 TPM 保护的专用传输密钥（tkpriv）解密。 会话密钥是发送到 Azure AD 的任何请求的所有权验证（POP）密钥。  会话密钥也受 TPM 保护，其他操作系统组件都不能访问它。 令牌请求或 PRT 续订请求由该会话密钥通过 TPM 安全签名，因此无法篡改。 Azure AD 将会使未由相应会话密钥签名的设备发出的任何请求无效。

通过使用 TPM 保护这些密钥，恶意执行组件不能盗取密钥，也不会在其他位置重播 PRT，因为即使攻击者已物理拥有设备，也无法访问 TPM。  因此，使用 TPM 极大地增强了 Azure AD 联接、混合 Azure AD 联接和 Azure AD 注册的设备免受凭据被盗的安全性。 对于性能和可靠性，Windows 10 上的所有 Azure AD 设备注册方案的建议版本都是 TPM 2.0。

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>应用令牌和浏览器 cookie 如何受到保护？

**应用令牌**：当应用通过 WAM 请求令牌时，Azure AD 会发出刷新令牌和访问令牌。 但是，WAM 只会将访问令牌返回到应用，并通过使用用户的数据保护应用程序编程接口（DPAPI）密钥对其进行加密来保护缓存中的刷新令牌。 WAM 通过使用会话密钥对请求进行签名以颁发进一步的访问令牌，从而安全地使用刷新令牌。 DPAPI 密钥由 Azure AD 本身中基于 Azure AD 的对称密钥保护。 当设备需要使用 DPAPI 密钥对用户配置文件进行解密时，Azure AD 提供由会话密钥加密的 DPAPI 密钥，CloudAP 插件请求 TPM 进行解密。 此功能可确保确保刷新令牌的一致性，并避免应用程序实现自己的保护机制。  

**浏览器 cookie**：在 windows 10 中，Azure AD 支持 Internet Explorer 中的浏览器 SSO、本机或 Google Chrome 中的浏览器 SSO，通过 Windows 10 帐户扩展。 安全不仅可以保护 cookie，还可以保护 cookie 发送到的终结点。 使用会话密钥对 cookie 进行签名和保护，以与 PRT 相同的方式来保护浏览器 cookie。

当用户启动浏览器交互时，浏览器（或扩展）调用 COM native client 主机。 Native client 主机确保该页来自允许的域之一。 浏览器可以将其他参数发送到 native client 主机（包括 nonce），但 native client 主机保证主机名的验证。 Native client 主机从 CloudAP 插件请求 PRT-cookie，此插件使用受 TPM 保护的会话密钥创建并签署该 cookie。 由于 PRT 是由会话密钥进行签名，因此不能将其篡改。 此 PRT 包括在 Azure AD 的请求标头中，用于验证它所源自的设备。 如果使用的是 Chrome 浏览器，则只有在本机客户端主机的清单中显式定义的扩展才能调用它，从而防止任意扩展发出这些请求。 Azure AD 验证 PRT cookie 后，它会向浏览器颁发会话 cookie。 此会话 cookie 还包含使用 PRT 颁发的同一会话密钥。 在后续请求期间，将验证会话密钥，从而有效地将 cookie 绑定到设备，并阻止其他位置的重播。

## <a name="when-does-a-prt-get-an-mfa-claim"></a>PRT 何时获得 MFA 声明？

在特定方案中，PRT 可以获取多重身份验证（MFA）声明。 当使用基于 MFA 的 PRT 请求应用程序的令牌时，MFA 声明会传输到这些应用令牌。 此功能可为用户提供无缝体验，因为这会阻止每个需要它的应用的 MFA 质询。 PRT 可以通过下列方式获取 MFA 声明：

* **使用 Windows Hello 企业版登录**： Windows Hello 企业版替代密码，并使用加密密钥提供强双重身份验证。 Windows Hello 企业版专用于设备上的用户，并且自身需要 MFA 才能进行设置。 当用户使用 Windows Hello 企业版登录时，用户的 PRT 会获取 MFA 声明。 此方案还适用于使用智能卡登录的用户（如果智能卡身份验证从 ADFS 生成 MFA 声明）。
   * 由于 Windows Hello 企业版被视为多重身份验证，因此在刷新 PRT 本身时，会更新 MFA 声明，因此，当用户登录到 WIndows Hello 企业版时，MFA 持续时间会不断延长
* **WAM 交互式登录期间的 MFA**：通过 WAM 的令牌请求期间，如果用户需要进行 mfa 才能访问应用，则在此交互过程中续订的 PRT 将 IMPRINTED 使用 MFA 声明。
   * 在这种情况下，MFA 声明不会连续更新，因此，MFA 持续时间基于目录上设置的生存期。
   * 使用以前的现有 PRT 和 RT 访问应用时，PRT 和 RT 将被视为第一次身份验证。 将需要一个新的，其中包含第二个证明和 imprinted MFA 声明。 这还将发出新的 PRT 和 RT。
* **设备注册期间的 MFA**：如果管理员已将 Azure AD 中的设备设置配置为[要求 mfa 注册设备](device-management-azure-portal.md#configure-device-settings)，则用户需要进行 mfa 才能完成注册。 在此过程中，颁发给用户的 PRT 具有在注册期间获得的 MFA 声明。 此功能仅适用于执行加入操作的用户，不适用于登录到该设备的其他用户。
   * 与 WAM 交互登录类似，MFA 声明不会连续更新，因此，MFA 持续时间基于目录上设置的生存期。

Windows 10 维护每个凭据的 Prt 的分区列表。 对于每个 Windows Hello 企业版、密码或智能卡，都有一个 PRT。 此分区确保根据使用的凭据隔离 MFA 声明，而不会在令牌请求过程中混合使用。

## <a name="how-is-a-prt-invalidated"></a>PRT 如何失效？

在以下情况下，PRT 无效：

* **无效的用户**：如果 Azure AD 中删除或禁用用户，则其 PRT 将失效，无法用于获取应用程序的令牌。 如果已删除或已禁用的用户以前已登录到某个设备，则缓存登录会将其记录在中，直到 CloudAP 知道其无效状态。 CloudAP 确定用户无效后，会阻止后续登录。 自动阻止无效的用户登录到未缓存其凭据的新设备。
* **设备无效**：如果在 Azure AD 中删除或禁用了某个设备，则在该设备上获得的 PRT 将失效，无法用于获取其他应用程序的令牌。 如果用户已登录到无效设备，则可以继续执行此操作。 但设备上的所有令牌都已失效，用户不具有该设备的任何资源的 SSO。
* **密码更改**：用户更改其密码后，Azure AD 使用之前密码获得的 PRT 失效。 密码更改将导致用户获取新 PRT。 这种失效的发生方式有两种：
   * 如果用户使用新密码登录到 Windows，则 CloudAP 会丢弃旧的 PRT 并请求 Azure AD 使用新密码颁发新的 PRT。 如果用户没有 internet 连接，则无法验证新密码，Windows 可能会要求用户输入其旧密码。
   * 如果用户已使用旧密码登录，或在登录 Windows 后更改了密码，则会将旧的 PRT 用于任何基于 WAM 的令牌请求。 在这种情况下，系统会提示用户在 WAM 令牌请求期间进行身份验证，并发出新的 PRT。
* **TPM 问题**：有时，设备的 TPM 可能 falter 或失败，从而导致 tpm 保护的密钥大量无法访问。 在这种情况下，设备不能使用现有的 PRT 获取 PRT 或请求令牌，因为它不能证明持有加密密钥。 因此，任何现有的 PRT 都 Azure AD 无效。 当 Windows 10 检测到故障时，它会启动一个恢复流，以使用新的加密密钥重新注册设备。 利用混合 Azure Ad join，就像初始注册一样，恢复无需用户输入便会自动进行。 对于已加入或 Azure AD 注册设备的 Azure AD，恢复需要由对设备具有管理员权限的用户执行。 在这种情况下，系统会引导用户成功恢复设备，并通过 Windows 提示符启动恢复流。

## <a name="detailed-flows"></a>详细流

以下关系图演示了发出、续订和使用 PRT 请求应用程序的访问令牌的基础详细信息。 此外，这些步骤还介绍如何在这些交互过程中应用上述安全机制。

### <a name="prt-issuance-during-first-sign-in"></a>首次登录时的 PRT 颁发

![第一次登录详细流期间的 PRT 颁发](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> 在 Azure AD 联接的设备中，此交换在用户可以登录到 Windows 之前以同步方式进行 PRT。 在混合 Azure AD 联接的设备中，本地 Active Directory 是主颁发机构。 因此，用户只会等待，直到它们可以获取 TGT 来登录，同时 PRT 颁发会以异步方式进行。 此方案不适用于 Azure AD 注册的设备，因为登录不使用 Azure AD 凭据。

| 步骤 | 说明 |
| :---: | --- |
| A | 用户在登录用户界面中输入其密码。 LogonUI 将身份验证缓冲区中的凭据传递到 LSA，后者会在内部将其传递到 CloudAP。 CloudAP 将此请求转发到 CloudAP 插件。 |
| B | CloudAP 插件启动领域发现请求，以确定用户的标识提供者。 如果用户的租户安装了联合身份验证提供程序，Azure AD 将返回联合身份验证提供程序的元数据交换终结点（MEX）终结点。 如果不是，Azure AD 将返回 "管理用户"，指示用户可以用 Azure AD 进行身份验证。 |
| C | 如果用户是托管用户，CloudAP 将从 Azure AD 获取 nonce。 如果用户是联合用户，则 CloudAP 插件将使用用户凭据从联合提供程序请求 SAML 令牌。 收到 SAML 令牌后，它会从 Azure AD 请求 nonce。 |
| D | CloudAP 插件使用用户的凭据、nonce 和 broker 范围构造身份验证请求，使用设备密钥（dkpriv）对请求进行签名，并将其发送到 Azure AD。 在联合环境中，CloudAP 插件使用联合身份验证提供程序返回的 SAML 令牌，而不是用户的凭据。 |
| E | Azure AD 验证用户凭据、nonce 和设备签名，验证设备在租户中是否有效，并颁发加密的 PRT。 除了 PRT，Azure AD 还颁发对称密钥，该密钥称为 Azure AD 使用传输密钥（tkpub）加密的会话密钥。 此外，还会在 PRT 中嵌入会话密钥。 此会话密钥充当 PRT 的后续请求的所有权证明（PoP）密钥。 |
| F | CloudAP 插件将加密的 PRT 和会话密钥传递到 CloudAP。 CloudAP 请求 TPM 使用传输密钥（tkpriv）对会话密钥进行解密，并使用 TPM 自己的密钥对其进行重新加密。 CloudAP 将加密的会话密钥与 PRT 一起存储在其缓存中。 |

### <a name="prt-renewal-in-subsequent-logons"></a>后续登录时的 PRT 续订

![后续登录时的 PRT 续订](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| 步骤 | 说明 |
| :---: | --- |
| A | 用户在登录用户界面中输入其密码。 LogonUI 将身份验证缓冲区中的凭据传递到 LSA，后者会在内部将其传递到 CloudAP。 CloudAP 将此请求转发到 CloudAP 插件。 |
| B | 如果用户以前已登录到用户，则 Windows 将启动缓存登录并验证凭据以登录用户。 每4小时，CloudAP 插件将异步启动 PRT 续订。 |
| C | CloudAP 插件启动领域发现请求，以确定用户的标识提供者。 如果用户的租户安装了联合身份验证提供程序，Azure AD 将返回联合身份验证提供程序的元数据交换终结点（MEX）终结点。 如果不是，Azure AD 将返回 "管理用户"，指示用户可以用 Azure AD 进行身份验证。 |
| D | 如果用户是联合用户，则 CloudAP 插件将使用用户凭据从联合提供程序请求 SAML 令牌。 收到 SAML 令牌后，它会从 Azure AD 请求 nonce。 如果用户是托管用户，则 CloudAP 将直接从 Azure AD 获取 nonce。 |
| E | CloudAP 插件用用户的凭据、nonce 和现有的 PRT 构造身份验证请求，并用会话密钥对请求进行签名，并将其发送到 Azure AD。 在联合环境中，CloudAP 插件使用联合身份验证提供程序返回的 SAML 令牌，而不是用户的凭据。 |
| F | Azure AD 通过将会话密钥签名与 PRT 中嵌入的会话密钥进行比较来验证该签名，验证 nonce 并验证设备在租户中是否有效，并颁发新的 PRT。 如上所示，PRT 再次附带由传输密钥（tkpub）加密的会话密钥。 |
| G | CloudAP 插件将加密的 PRT 和会话密钥传递到 CloudAP。 CloudAP 请求 TPM 使用传输密钥（tkpriv）对会话密钥进行解密，并使用 TPM 自己的密钥对其进行重新加密。 CloudAP 将加密的会话密钥与 PRT 一起存储在其缓存中。 |

### <a name="prt-usage-during-app-token-requests"></a>应用令牌请求过程中的 PRT 用法

![应用令牌请求过程中的 PRT 用法](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| 步骤 | 说明 |
| :---: | --- |
| A | 应用程序（例如，Outlook、OneNote 等）启动 WAM 的令牌请求。 WAM 会要求 Azure AD WAM 插件为令牌请求提供服务。 |
| B | 如果应用程序的刷新令牌已可用，Azure AD WAM 插件将使用该令牌来请求访问令牌。 为了提供设备绑定的证明，WAM 插件通过会话密钥对请求进行签名。 Azure AD 验证会话密钥并颁发应用的访问令牌和新的刷新令牌，并通过会话密钥进行加密。 WAM 插件请求云 AP 插件来对令牌进行解密，进而请求 TPM 使用会话密钥进行解密，导致 WAM 插件获取这两个令牌。 接下来，WAM 插件仅提供应用程序的访问令牌，同时使用 DPAPI 重新加密刷新令牌并将其存储在自己的缓存中  |
| C |  如果应用程序的刷新令牌不可用，Azure AD WAM 插件将使用 PRT 请求访问令牌。 为了提供所有权证明，WAM 插件用会话密钥对包含 PRT 的请求进行签名。 Azure AD 通过将会话密钥签名与嵌入在 PRT 中的会话密钥进行比较来验证该签名，验证该设备是否有效，并为应用程序颁发访问令牌和刷新令牌。 此外，Azure AD 可以发出新的 PRT （基于刷新周期），所有这些都由会话密钥进行加密。 |
| D | WAM 插件请求云 AP 插件来对令牌进行解密，进而请求 TPM 使用会话密钥进行解密，导致 WAM 插件获取这两个令牌。 接下来，WAM 插件仅提供应用程序的访问令牌，而使用 DPAPI 重新加密刷新令牌并将其存储在自己的缓存中。 WAM 插件将使用此应用程序的刷新令牌。 WAM 插件还提供了新的 PRT 到云 AP 插件，该插件会在其自己的缓存中进行更新之前，通过 Azure AD 来验证 PRT。 云 AP 插件将使用新的 PRT。 |
| E | WAM 向 WAM 提供新颁发的访问令牌，而后者又将其返回给调用应用程序|

### <a name="browser-sso-using-prt"></a>使用 PRT 的浏览器 SSO

![使用 PRT 的浏览器 SSO](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| 步骤 | 说明 |
| :---: | --- |
| A | 用户使用其凭据登录到 Windows 以获取 PRT。 用户打开浏览器后，浏览器（或扩展）将从注册表加载 Url。 |
| B | 当用户打开 Azure AD 登录 URL 时，浏览器或扩展会使用从注册表获得的 URL 来验证 URL。 如果它们匹配，则浏览器调用 native client 主机以获取令牌。 |
| C | Native client 主机验证 Url 是否属于 Microsoft 标识提供者（Microsoft 帐户或 Azure AD），提取从 URL 发送的 nonce，并调用 CloudAP 插件获取 PRT cookie。 |
| D | CloudAP 插件将创建 PRT cookie，并登录到与 TPM 绑定的会话密钥，然后将其发送回 native client 主机。 当 cookie 由会话密钥进行签名时，它不能被篡改。 |
| E | Native client 主机会将此 PRT cookie 返回到浏览器，该浏览器会将其包含为名为 RefreshTokenCredential 的请求标头和 Azure AD 中的请求令牌。 |
| F | Azure AD 验证 PRT cookie 上的会话密钥签名，验证 nonce，验证设备在租户中是否有效，并为网页颁发 ID 令牌和浏览器的已加密会话 cookie。 |

## <a name="next-steps"></a>后续步骤

有关解决与 PRT 相关的问题的详细信息，请参阅[排查混合 Azure Active Directory 联接 windows 10 和 Windows Server 2016 设备的故障排除](troubleshoot-hybrid-join-windows-current.md)一文。
