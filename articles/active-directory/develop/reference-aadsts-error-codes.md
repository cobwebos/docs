---
title: Azure Active Directory 身份验证和授权错误代码 | Microsoft Docs
description: 了解 Azure AD 安全令牌服务 (STS) 返回的 AADSTS 错误代码。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: hirsin, justhu
ms.custom: aaddev
ms.openlocfilehash: 0643ae39d24f8ef0c1c1c18dcf73cf840c95e277
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956315"
---
# <a name="authentication-and-authorization-error-codes"></a>身份验证和授权错误代码

想要查找有关 Azure Active Directory (Azure AD) 安全令牌服务 (STS) 返回的 AADSTS 错误代码的信息？ 请阅读本文档来查找 AADSTS 错误说明、修复方法和一些建议的解决方法。

> [!NOTE]
> 本文中的信息属于初步信息，随时可能更改。 遇到了问题或者找不到所需的内容？ 请创建 GitHub 问题，或查看[面向开发人员的支持和帮助选项](active-directory-develop-help-support.md)来了解其他可以获得帮助和支持的方法。

## <a name="aadsts-error-codes"></a>AADSTS 错误代码

| 错误 | Description |
|---|---|
| AADSTS16000 | SelectUserAccount - 这是 Azure AD 引发的中断，使得 UI 允许用户从多个有效 SSO 会话中进行选择。 此错误相当常见。如果指定了 `prompt=none`，可能会在应用程序中返回此错误。 |
| AADSTS16001 | UserAccountSelectionInvalid - 如果用户单击会话选择逻辑已拒绝的某个磁贴，则会出现此错误。 触发此错误时，用户可以从更新的磁贴/会话列表中进行选择或选择另一个帐户进行恢复。 此错误的原因可能是代码缺陷或出现争用状况。 |
| AADSTS16002 | AppSessionSelectionInvalid - 不符合应用指定的 SID 要求。  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant |
| AADSTS17003 | CredentialKeyProvisioningFailed |
| AADSTS20001 | WsFedSignInResponseError - 联合标识提供者出现问题。 请联系 IDP 解决此问题。 |
| AADSTS20012 | WsFedMessageInvalid - 联合标识提供者出现问题。 请联系 IDP 解决此问题。 |
| AADSTS20033 | FedMetadataInvalidTenantName - 联合标识提供者出现问题。 请联系 IDP 解决此问题。 |
| AADSTS40008 | OAuth2IdPUnretryableServerError - 联合标识提供者出现问题。 请联系 IDP 解决此问题。 |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError - 联合标识提供者出现问题。 请联系 IDP 解决此问题。 |
| AADSTS40010 | OAuth2IdPRetryableServerError - 联合标识提供者出现问题。 请联系 IDP 解决此问题。 |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError - 联合标识提供者出现问题。 请联系 IDP 解决此问题。 |
| AADSTS50000 | TokenIssuanceError - 单一登录服务出现问题。 请[开具支持票证](../fundamentals/active-directory-troubleshooting-support-howto.md)以解决此问题。 |
| AADSTS50001 | InvalidResource - 资源已禁用或不存在。 请检查应用代码，确保为尝试访问的资源指定了确切的资源 URL。  |
| AADSTS50002 | NotAllowedTenant - 由于租户中的代理访问权限受限，登录失败。 如果这是你自己的租户策略，可以更改受限的租户设置来解决此问题。 |
| AADSTS50003 | MissingSigningKey - 由于缺少签名密钥或证书，登录失败。 这可能是因为应用中未配置任何签名密钥。 请查看 [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured) 中所述的解决方法。 如果仍然出现问题，请联系应用所有者或应用管理员。 |
| AADSTS50005 | DevicePolicyError - 用户尝试从条件访问策略目前不支持的平台登录到设备。 |
| AADSTS50006 | InvalidSignature - 由于签名无效，签名验证失败。 |
| AADSTS50007 | PartnerEncryptionCertificateMissing - 未找到此应用的合作伙伴加密证书。 请向 Microsoft [开具支持票证](../fundamentals/active-directory-troubleshooting-support-howto.md)以解决此问题。 |
| AADSTS50008 | InvalidSamlToken - SAML 断言在令牌中缺失或配置错误。 请联系联合提供者。 |
| AADSTS50010 | AudienceUriValidationFailed - 由于未配置令牌受众，应用的受众 URI 验证失败。 |
| AADSTS50011 | InvalidReplyTo - 回复地址缺失、配置错误或者与为应用配置的回复地址不匹配。 请尝试 [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application) 中所列的解决方法。 如果仍然出现问题，请联系应用所有者或应用管理员。 |
| AADSTS50012 | AuthenticationFailed - 身份验证由于以下原因之一而失败：<ul><li>未授权签名证书的使用者名称</li><li>找不到与已授权使用者名称匹配的受信任颁发机构策略</li><li>证书链无效</li><li>签名证书无效</li><li>未在租户中配置策略</li><li>未授权签名证书的指纹</li><li>客户端断言包含无效的签名</li></ul> |
| AADSTS50013 | InvalidAssertion - 多种原因导致断言无效 - 令牌颁发者与令牌有效时间范围内的 API 版本不匹配 - 已过期 - 格式不正确 - 断言中的刷新令牌不是主要刷新令牌。 |
| AADSTS50014 | GuestUserInPendingState |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState |
| AADSTS50017 | CertificateValidationFailed - 证书验证失败，原因如下：<ul><li>在受信任的证书列表中找不到颁发证书</li><li>找不到所需的 CrlSegment</li><li>在受信任的证书列表中找不到颁发证书</li><li>在没有对应 CRL 分发点的情况下配置了增量 CRL 分发点</li><li>由于超时问题，无法检索有效的 CRL 段</li><li>无法下载 CRL</li></ul>请联系租户管理员。 |
| AADSTS50020 | UserUnauthorized - 未授权用户调用此终结点。 |
| AADSTS50027 | InvalidJwtToken - 以下原因导致 JWT 令牌无效：<ul><li>不包含 nonce 声明和子声明</li><li>使用者标识符不匹配</li><li>idToken 声明中存在重复声明</li><li>意外的颁发者</li><li>意外的受众</li><li>不在有效的时间范围内 </li><li>令牌格式不正确</li><li>颁发者的外部 ID 令牌未通过签名验证。</li></ul> |
| AADSTS50029 | URI 无效 - 域名包含无效字符。 请联系租户管理员。 |
| AADSTS50032 | WeakRsaKey - 指示错误的用户尝试使用弱 RSA 密钥。 |
| AADSTS50033 | RetryableError - 指示与数据库操作不相关的暂时性错误。 |
| AADSTS50034 | UserAccountNotFound - 若要登录到此应用程序，必须将帐户添加到目录中。 |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt - 主体中缺少用于生成成对标识符的盐。 请联系租户管理员。 |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer - 使用者与客户端断言中的颁发者声明不匹配。 请联系租户管理员。 |
| AADSTS50049 | NoSuchInstanceForDiscovery - 未知或无效的实例。 |
| AADSTS50050 | MalformedDiscoveryRequest - 请求格式不正确。 |
| AADSTS50053 | IdsLocked - 帐户已锁定，因为用户尝试使用不正确的用户 ID 或密码登录的次数过多。 |
| AADSTS50055 | InvalidPasswordExpiredPassword - 密码已过期。 |
| AADSTS50056 | 密码无效或为 null - 密码在此用户的存储中不存在。 |
| AADSTS50057 | UserDisabled - 用户帐户处于禁用状态。 帐户已被管理员禁用。 |
| AADSTS50058 | UserInformationNotProvided - 表示用户未登录。 这是一个很常见的错误，如果用户未经过身份验证并且尚未登录，则预期会出现此错误。</br>如果在用户刚刚登录的 SSO 上下文中遇到此错误，则表示 SSO 会话未找到或无效。</br>如果指定了 prompt=none，则可能会在应用程序中返回此错误。 |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided - 在请求中未找到租户标识信息，或者任何提供的凭据未隐式指定此信息。 用户可以联系租户管理员来帮助解决此问题。 |
| AADSTS50061 | SignoutInvalidRequest - 注销请求无效。 |
| AADSTS50064 | CredentialAuthenticationError |
| AADSTS50068 | SignoutInitiatorNotParticipant |
| AADSTS50070 | SignoutUnknownSessionIdentifier |
| AADSTS50071 | SignoutMessageExpired |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt - 用户需要注册双重身份验证（交互式）。 |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt - 需要强身份验证，用户未通过 MFA 质询。 |
| AADSTS50076 | UserStrongAuthClientAuthNRequired - 用户必须使用多重身份验证访问此资源。 请使用针对资源的新授权请求重试。 |
| AADSTS50079 | UserStrongAuthEnrollmentRequired - 由于管理员做了配置更改，或者用户已移到新位置，该用户需要使用多重身份验证。 |
| AADSTS50085 | 刷新令牌需要社交 IDP 登录。 请让用户尝试使用用户名和密码再次登录 |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError |
| AADSTS50089 | 流令牌过期 - 身份验证失败。 请让用户尝试使用用户名和密码再次登录 |
| AADSTS50097 | DeviceAuthenticationRequired - 必须使用设备身份验证。 |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized - JWT 签名无效。 |
| AADSTS50105 | EntitlementGrantsNotFound - 未将已登录用户分配到已登录应用的角色。 请将该用户分配到该应用。 有关详细信息，请参阅 [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role)。 |
| AADSTS50107 | InvalidRealmUri - 请求的联合领域对象不存在。 请联系租户管理员。 |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat - JWT 标头有问题。 请联系租户管理员。 |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter - 声明转换包含无效的输入参数。 请联系租户管理员来更新策略。 |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt - 密码重置或密码注册条目导致登录中断。 |
| AADSTS50126 | InvalidUserNameOrPassword - 无效的用户名或密码导致验证凭据时出错。 |
| AADSTS50127 | BrokerAppNotInstalled - 用户需要安装中转站应用才能访问此内容。 |
| AADSTS50128 | 域名无效 - 未在请求中找到或提供的任何凭据均未暗示任何租户标识信息。|
| AADSTS50129 | DeviceIsNotWorkplaceJoined - 需要加入工作区才能注册设备。 |
| AADSTS50131 | ConditionalAccessFailed - 指示各种条件访问错误，例如，Windows 设备状态不正确，请求因活动、访问策略和安全策略决策可疑而被阻止。 |
| AADSTS50132 | SsoArtifactInvalidOrExpired - 会话由于密码过期或最近更改了密码而无效。 |
| AADSTS50133 | SsoArtifactRevoked - 会话由于密码过期或最近更改了密码而无效。 |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter |
| AADSTS50135 | PasswordChangeCompromisedPassword - 由于帐户风险，需要更改密码。 |
| AADSTS50136 | RedirectMsaSessionToApp - 检测到单个 MSA 会话。 |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken |
| AADSTS50140 | KmsiInterrupt - 此错误是由于用户登录时出现“使我保持登录状态”中断而发生的。 [开具支持票证](../fundamentals/active-directory-troubleshooting-support-howto.md)并提供相关性 ID、请求 ID 和错误代码，以获取更多详细信息。 |
| AADSTS50143 | 会话不匹配 - 会话无效，因为不同的资源导致用户租户与域提示不匹配。 [开具支持票证](../fundamentals/active-directory-troubleshooting-support-howto.md)并提供相关性 ID、请求 ID 和错误代码，以获取更多详细信息。 |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword - 用户的 Active Directory 密码已过期。 为用户生成新密码，或者让用户使用自助重置工具重置其密码。 |
| AADSTS50146 | MissingCustomSigningKey - 需要为此应用配置特定于应用的签名密钥。 没有为此应用程序配置签名密钥，或者密钥已过期或尚未生效。 |
| AADSTS50147 | MissingCodeChallenge |
| AADSTS50155 | DeviceAuthenticationFailed - 此用户的设备身份验证失败。 |
| AADSTS50158 | ExternalSecurityChallenge - 不符合外部安全质询。 |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration - 外部提供程序发送的声明不够，或者向外部提供程序请求的声明缺失。 |
| AADSTS50166 | ExternalClaimsProviderThrottled - 无法将请求发送到声明提供程序。 |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired - 客户端能够通过 Windows 10 帐户扩展获取 SSO 令牌，但在请求中找不到令牌，或提供的令牌已过期。 |
| AADSTS50169 | InvalidRequestBadRealm - 领域不是当前服务命名空间的已配置领域。 |
| AADSTS50170 | MissingExternalClaimsProviderMapping |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers - 直通用户不支持外部质询。 |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers - 直通用户不支持会话控制。 |
| AADSTS50180 | WindowsIntegratedAuthMissing - 需要 Windows 集成身份验证。 为租户启用无缝 SSO。 |
| AADSTS50187 | DeviceInformationNotProvided |
| AADSTS51000 | RequiredFeatureNotEnabled |
| AADSTS51001 | DomainHintMustbePresent - 必须使用本地安全标识符或本地 UPN 提供域提示。 |
| AADSTS51004 | UserAccountNotInDirectory - 目录中不存在该用户帐户。 |
| AADSTS51005 | TemporaryRedirect - 等效于 HTTP 状态 307，表示请求的信息位于 location 标头中指定的 URI 处。 如果收到此状态，请遵循与响应关联的 location 标头操作。 如果原始请求方法是 POST，则重定向的请求也会使用 POST 方法。 |
| AADSTS51006 | ForceReauthDueToInsufficientAuth - 需要 Windows 集成身份验证。 用户已使用缺少 Windows 集成身份验证声明的会话令牌登录。 请求用户重新登录。 |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn - 用户未许可访问 LinkedIn 资源。 |
| AADSTS53000 | DeviceNotCompliant - 条件访问策略需要符合的设备，该设备不符合。 用户必须使用已批准的 MDM 提供程序（例如 Intune）注册其设备。 |
| AADSTS53001 | DeviceNotDomainJoined - 条件访问策略需要已加入域的设备，而该设备未加入域。 让用户使用已加入域的设备。 |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp - 使用的应用不是批准用于条件访问的应用。 用户需使用可用的获批准应用列表中的某个应用才能获取访问权限。 |
| AADSTS53003 | BlockedByConditionalAccess - 条件访问策略已阻止访问。 访问策略不允许令牌颁发。 |
| AADSTS53004 | ProofUpBlockedDueToRisk - 在访问此内容之前，用户需要完成多重身份验证注册过程。 用户应注册多重身份验证。 |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist - 用户或管理员尚未许可将应用程序与 ID X 配合使用。请发送针对该用户和资源的交互式授权请求。 |
| AADSTS65004 | UserDeclinedConsent - 用户已拒绝许可访问该应用。 让用户重试登录并许可应用|
| AADSTS65005 | MisconfiguredApplication - 应用所需的资源访问列表不包含可以通过资源来发现的应用，或者客户端应用请求访问的资源未在其必需的资源访问列表中指定，或者 Graph 服务返回了错误的请求，或者资源找不到。 如果应用支持 SAML，则原因可能是使用错误的标识符（实体）配置了应用。 使用以下链接，尝试针对 SAML 列出的解决方法：[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list) |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant - 身份验证失败。 刷新令牌无效。 该错误的可能原因如下：<ul><li>令牌绑定标头为空</li><li>令牌绑定哈希不匹配</li></ul> |
| AADSTS70001 | UnauthorizedClient - 应用程序处于禁用状态。 |
| AADSTS70002 | InvalidClient - 验证凭据时出错。 指定的 client_secret 与此客户端的预期值不匹配。 请更正 client_secret，然后重试。 有关详细信息，请参阅[使用授权代码请求访问令牌](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)。 |
| AADSTS70003 | UnsupportedGrantType - 应用返回了不受支持的授权类型。 |
| AADSTS70004 | InvalidRedirectUri - 应用返回了无效的重定向 URI。 客户端指定的重定向地址与配置的任何地址或者 OIDC 批准列表中的任何地址都不匹配。 |
| AADSTS70005 | UnsupportedResponseType - 由于以下原因，应用返回了不受支持的响应类型：<ul><li>没有为应用启用响应类型“token”</li><li>响应类型“id_token”需要“OpenID”作用域 - 编码的 wctx 中包含不支持的 OAuth 参数值</li></ul> |
| AADSTS70007 | UnsupportedResponseMode - 请求令牌时，应用返回了不受支持的 `response_mode` 值。  |
| AADSTS70008 | ExpiredOrRevokedGrant - 刷新令牌由于非活动状态而过期。 该令牌是在 XXX 颁发的，并在特定的时间内处于非活动状态。 |
| AADSTS70011 | InvalidScope - 应用请求的范围无效。 |
| AADSTS70012 | MsaServerError - 对 MSA（使用者）用户进行身份验证时发生服务器错误。 请重试。 如果仍然失败，请[开具支持票证](../fundamentals/active-directory-troubleshooting-support-howto.md) |
| AADSTS70016 | AuthorizationPending - OAuth 2.0 设备流错误。 授权处于挂起状态。 设备将重试轮询请求。 |
| AADSTS70018 | BadVerificationCode - 由于用户为设备代码流键入了错误的用户代码，验证码无效。 授权未获批准。 |
| AADSTS70019 | CodeExpired - 验证码已过期。 让用户重试登录。 |
| AADSTS75001 | BindingSerializationError - SAML 消息绑定期间出错。 |
| AADSTS75003 | UnsupportedBindingError - 应用返回了与不受支持的绑定相关的错误（无法通过 HTTP POST 以外的绑定发送 SAML 协议响应）。 |
| AADSTS75005 | Saml2MessageInvalid - Azure AD 不支持应用针对 SSO 所发送的 SAML 请求。 |
| AADSTS75008 | RequestDeniedError - 由于 SAML 请求的目标不符合预期，来自应用的请求被拒绝。 |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims - 用户在服务中用于身份验证的身份验证方法与请求的身份验证方法不匹配。 |
| AADSTS75016 | Saml2AuthenticationRequestInvalidNameIDPolicy - SAML2 身份验证请求包含无效的 NameIdPolicy。 |
| AADSTS80001 | OnPremiseStoreIsNotAvailable - 身份验证代理无法连接到 Active Directory。 确保代理服务器是需要验证其密码的用户所在的 AD 林的成员，并且能够连接到 Active Directory。 |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout - 密码验证请求超时。确保 Active Directory 可用，并且可以响应代理的请求。 |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException - 处理来自身份验证代理的响应时发生未知的错误。 重试请求。 如果仍旧失败，请[开具支持票证](../fundamentals/active-directory-troubleshooting-support-howto.md)，获取有关该错误的更多详细信息。 |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem - 身份验证代理无法验证用户的密码。 检查代理日志以了解更多信息，并验证 Active Directory 是否按预期方式运行。 |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException - 身份验证代理无法解密密码。 |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours - 用户尝试在允许的时间（在 AD 中指定）以外登录。 |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew - 由于运行身份验证代理的计算机与 AD 之间存在时间偏差，身份验证尝试无法完成。 解决时间同步问题。 |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated - Kerberos 身份验证尝试失败。 |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported - 不支持该身份验证包。 |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader - 找不到授权标头。 |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn - 未为租户启用无缝 SSO。 |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat - 无法验证用户的 Kerberos 票证。 |
| AADSTS81010 | DesktopSsoAuthTokenInvalid - 由于用户的 Kerberos 票证已过期或无效，无缝 SSO 失败。 |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed - 无法根据用户的 Kerberos 票证中的信息找到用户对象。 |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn - 尝试登录到 Azure AD 的用户不同于已登录到设备的用户。 |
| AADSTS90002 | InvalidTenantName - 在数据存储中找不到该租户名称。 请确保租户 ID 正确。 |
| AADSTS90004 | InvalidRequestFormat |
| AADSTS90005 | InvalidRequestWithMultipleRequirements |
| AADSTS90006 | ExternalServerRetryableError |
| AADSTS90007 | InvalidSessionId |
| AADSTS90008 | TokenForItselfRequiresGraphPermission |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier |
| AADSTS90010 | NotSupported |
| AADSTS90012 | RequestTimeout |
| AADSTS90013 | InvalidUserInput |
| AADSTS90014 | MissingRequiredField - 如果预期的字段在凭据中不存在，则可能会显示此错误代码。 |
| AADSTS90015 | QueryStringTooLong |
| AADSTS90016 | MissingRequiredClaim |
| AADSTS90019 | MissingTenantRealm - Azure AD 无法确定请求中的租户标识符。 |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat - 主体名称格式无效，或者不符合预期的 `name[/host][@realm]` 格式。 主体名称是必需的，而主机和领域是可选的，可设置为 null。 |
| AADSTS90023 | InvalidRequest |
| AADSTS90024 | RequestBudgetExceededError |
| AADSTS90033 | MsodsServiceUnavailable |
| AADSTS90036 | MsodsServiceUnretryableFailure |
| AADSTS90038 | NationalCloudTenantRedirection - 指定的租户“Y”属于国家/地区云“X”。 当前云实例“Z”未与 X 联合。返回了云重定向错误。 |
| AADSTS90043 | NationalCloudAuthCodeRedirection |
| AADSTS90051 | InvalidNationalCloudId |
| AADSTS90055 | TenantThrottlingError - 传入的请求过多。 此异常是针对阻止的租户引发的。 |
| AADSTS90056 | BadResourceRequest - 若要兑换访问令牌的代码，应用应该向 `/token` 终结点发送 POST 请求。 另外，在此之前，应该提供授权代码，并在发往 `/token` 终结点的 POST 请求中发送此代码。 请参阅以下文章中有关 OAuth 2.0 授权代码流的概述：[https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)。 必须先将用户定向到 `/authorize` 终结点，该终结点会返回 authorization_code。 通过向 `/token` 终结点发布请求，用户可以获取访问令牌。 在 Azure 门户中登录，并检查“应用注册”>“终结点”以确认是否正确配置了两个终结点。 |
| AADSTS90072 | PassThroughUserMfaError |
| AADSTS90081 | OrgIdWsFederationMessageInvalid |
| AADSTS90082 | OrgIdWsFederationNotSupported |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed |
| AADSTS90090 | GraphRetryableError |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized - Graph 返回了针对请求的禁止访问错误代码。 |
| AADSTS90094 | AdminConsentRequired |
| AADSTS90100 | InvalidRequestParameter |
| AADSTS90101 | InvalidEmailAddress |
| AADSTS90102 | InvalidUriParameter |
| AADSTS90107 | InvalidXml |
| AADSTS90114 | InvalidExpiryDate |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode |
| AADSTS90120 | InvalidDeviceFlowRequest |
| AADSTS90121 | InvalidEmptyRequest |
| AADSTS90123 | IdentityProviderAccessDenied |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported |
| AADSTS90125 | DebugModeEnrollTenantNotFound |
| AADSTS90126 | DebugModeEnrollTenantNotInferred |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated |
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound |
| AADSTS130005 | NgcInvalidSignature |
| AADSTS130006 | NgcTransportKeyNotFound |
| AADSTS130007 | NgcDeviceIsDisabled |
| AADSTS130008 | NgcDeviceIsNotFound |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | InvalidRequestNonce |
| AADSTS140001 | InvalidSessionKey |
| AADSTS165000 | InvalidRequestCanary |
| AADSTS165004 | InvalidApiCanary |
| AADSTS165900 | InvalidApiRequest |
| AADSTS220450 | UnsupportedAndroidWebViewVersion |
| AADSTS220501 | InvalidCrlDownload |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource - 资源未配置为接受仅限设备的令牌。 |
| AADSTS240001 | BulkAADJTokenUnauthorized |
| AADSTS240002 | RequiredClaimIsMissing |
| AADSTS700020 | InteractionRequired |
| AADSTS700022 | InvalidMultipleResroucesScope |
| AADSTS700023 | InvalidResourcelessScope |
| AADSTS900000 | WebWatsonEnvironmentError |
| AADSTS1000000 | UserNotBoundError |
| AADSTS1000002 | BindCompleteInterruptError |

## <a name="next-steps"></a>后续步骤

* 遇到了问题或者找不到所需的内容？ 请创建 GitHub 问题，或查看[面向开发人员的支持和帮助选项](active-directory-develop-help-support.md)来了解其他可以获得帮助和支持的方法。