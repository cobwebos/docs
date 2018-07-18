---
title: Azure Active Directory 门户中的登录活动报告错误代码 | Microsoft Docs
description: 登录活动报告错误代码参考。
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/31/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: dc01a775579455ae24c95ecc6f3858ce28149dea
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231863"
---
# <a name="sign-in-activity-report-error-codes-in-the-azure-active-directory-portal"></a>Azure Active Directory 门户中的登录活动报告错误代码

通过用户登录报告提供的信息，可以找到一些问题的答案，例如：

- 谁使用 Azure Active Directory 进行了登录？
- 登录到了哪些应用中？
- 哪些登录失败？失败原因是什么？

本文列出了错误代码和相关说明。 

## <a name="how-can-i-display-failed-sign-ins"></a>如何才能显示失败的登录？ 

所有登录活动数据的第一个入口点为 Azure Active Directory 的“活动”部分中的**[登录](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)**。


![登录活动](./media/active-directory-reporting-activity-sign-ins-errors/61.png "登录活动")

在登录报告中，选择“失败”作为“登录状态”即可显示所有失败的登录。

![登录活动](./media/active-directory-reporting-activity-sign-ins-errors/06.png "登录活动")

单击已显示列表中的某个项，打开“活动详细信息: 登录”边栏选项卡。 此视图提供 Azure Active Directory 跟踪的有关登录的所有详细信息，包括“登录错误代码”和“失败原因”。

![登录活动](./media/active-directory-reporting-activity-sign-ins-errors/05.png "登录活动")


也可使用[报告 API](active-directory-reporting-api-getting-started-azure-portal.md)，作为使用 Azure 门户访问登录数据的替代。


以下部分完整概述了所有可能的错误和相关说明。 

## <a name="error-codes"></a>错误代码


|错误|说明|
|---|---|
|20001|联合标识提供者出现问题。 请联系 IDP 解决此问题。|
|20012|联合标识提供者出现问题。 请联系 IDP 解决此问题。|
|20033|联合标识提供者出现问题。 请联系 IDP 解决此问题。|
|40008|联合标识提供者出现问题。 请联系 IDP 解决此问题。|
|40009|联合标识提供者出现问题。 请联系 IDP 解决此问题。|
|40014|联合标识提供者出现问题。 请联系 IDP 解决此问题。|
|50000|登录服务出现问题。 请[开具支持票证](fundamentals/active-directory-troubleshooting-support-howto.md)以解决此问题。|
|50001|在此租户中找不到服务主体名称。 如果应用程序尚未由租户管理员安装，则可能会发生这种情况。 或者，资源主体在目录中找不到或无效。|
|50002|由于租户中的代理访问权限受限，登录失败。 如果这是你自己的租户策略，可以更改受限的租户设置来解决此问题|
|50003|由于缺少签名密钥或证书，登录失败。 这可能是因为应用程序中未配置任何签名密钥。 请查看 [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured) 中所述的解决方法。 如果仍然出现问题，请联系应用程序所有者或应用程序管理员|
|50005|用户尝试从条件访问策略目前不支持的平台登录到设备|
|50006| 由于签名无效，签名验证失败。 请查看 [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery) 中所述的解决方法。 如果仍然出现问题，请联系应用程序所有者或应用管理员|
|50007|找不到此应用程序的合作伙伴加密证书。 请向 Microsoft [开具支持票证](fundamentals/active-directory-troubleshooting-support-howto.md)以解决此问题。|
|50008|SAML 断言在令牌中缺失或配置错误。 请联系联合提供者。|
|50010|由于未配置令牌受众，应用程序的受众 URI 验证失败。 请联系应用程序所有者|
|50011|回复地址缺失、配置错误或者与为应用程序配置的回复地址不匹配。 请尝试 [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application) 中所列的解决方法。 如果仍然出现问题，请联系应用程序所有者或应用管理员|
|50013|多种原因导致断言无效 - 令牌颁发者与令牌有效时间范围内的 API 版本不匹配 - 已过期 - 格式不正确 - 断言中的刷新令牌不是主要刷新令牌。|
|50017|证书验证失败，原因如下：<ul><li>在受信任的证书列表中找不到颁发证书</li><li>找不到所需的 CrlSegment</li><li>在受信任的证书列表中找不到颁发证书</li><li>在没有对应 CRL 分发点的情况下配置了增量 CRL 分发点</li><li>由于超时问题，无法检索有效的 CRL 段</li><li>无法下载 CRL</li></ul>请联系租户管理员。|
|50020|用户未授权 - 由于版本问题，无法颁发令牌 - 未指定颁发者名称 - 颁发者名称有问题（null - 最大长度）。 请联系应用所有者|
|50027|以下原因导致 JWT 令牌无效：<ul><li>不包含 nonce 声明和子声明</li><li>使用者标识符不匹配</li><li>idToken 声明中存在重复声明</li><li>意外的颁发者</li><li>意外的受众</li><li>不在有效的时间范围内 </li><li>令牌格式不正确</li><li>颁发者的外部 ID 令牌未通过签名验证。</li></ul>请联系应用程序所有者|
|50029|URI 无效 - 域名包含无效字符。 请联系租户管理员。|
|50034|用户在目录中不存在。 请联系租户管理员。|
|50042|主体中缺少用于生成成对标识符的盐。 请联系租户管理员。|
|50048|使用者与客户端断言中的颁发者声明不匹配。 请联系租户管理员。|
|50050|请求格式不正确。 请联系应用程序所有者。|
|50053|帐户已锁定，因为用户尝试使用不正确的用户 ID 或密码登录的次数过多。|
|50055|密码无效，输入的密码已过期。|
|50056|密码无效或为 null - 密码在此用户的存储中不存在。|
|50057|用户帐户已禁用。 帐户已被管理员禁用。|
|50058|应用程序尝试执行无提示登录，而用户无法以无提示方式登录。 应用程序需要启动交互式流，为用户提供登录选项。 请联系应用所有者。|
|50059|用户在目录中不存在。 请联系租户管理员|
|50061|注销请求无效。 请联系应用程序所有者|
|50072|用户需注册进行双重身份验证（交互式）。|
|50074|用户未通过 MFA 质询。|
|50076|用户未通过 MFA 质询（非交互式）|
|50079|用户需要登记双重身份验证（非交互式登录）。|
|50085|刷新令牌需要社交 IDP 登录。 请让用户尝试使用用户名和密码再次登录|
|50089|流令牌过期 - 身份验证失败。 请让用户尝试使用用户名和密码再次登录|
|50097|需要设备身份验证 - DeviceId -DeviceAltSecId 声明为 null，或者与设备标识符对应的设备不存在|
|50099|JWT 签名无效。 请联系应用程序所有者。|
|50105|未将已登录用户分配到已登录应用程序的角色。 请将该用户分配到应用程序。 有关详细信息，请参阅 [https://docs.microsoft.com/en-us/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/en-us/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role)|
|50107|请求的联合领域对象不存在。 请联系租户管理员。|
|50120|JWT 标头有问题。 请联系租户管理员。|
|50124|声明转换包含无效的输入参数。 请联系租户管理员来更新策略。|
|50125|由于密码重置或密码注册项，登录已中断|
|50126|用户名或密码无效，或者本地用户名或密码无效。|
|50127|用户需要安装中转站应用，才能访问此内容。|
|50128|域名无效 - 未在请求中找到或提供的任何凭据均未暗示任何租户标识信息。|
|50129|设备未加入工作区 - 需要加入工作区才能注册设备。|
|50130|无法将声明值解释为已知的身份验证方法|
|50131|用于各种条件性访问错误。 例如 Windows 设备状态不正确，请求因活动、访问策略和安全策略决策可疑而被阻止。|
|50132|以下原因导致凭据被吊销：<ul><li>SSO 项目无效或已过期</li><li>应用程序的会话不够新</li><li>已发送无提示登录请求，但用户在 Azure AD 中的会话无效或已过期。</li></ul>|
|50133|会话因过期或最近更改了密码而无效。|
|50135|由于存在帐户风险，需要更改密码|
|50136|将 MSA 会话重定向到应用 - 检测到单个 MSA 会话 |
|50140|此错误是由于用户登录时出现“使我保持登录状态”中断而发生的。 [开具支持票证](fundamentals/active-directory-troubleshooting-support-howto.md)并提供相关性 ID、请求 ID 和错误代码，以获取更多详细信息。 |
|50143|会话不匹配 - 会话无效，因为不同的资源导致用户租户与域提示不匹配。 [开具支持票证](fundamentals/active-directory-troubleshooting-support-howto.md)并提供相关性 ID、请求 ID 和错误代码，以获取更多详细信息。|
|50144|用户的 Active Directory 密码已过期。 为用户生成新密码，或者让最终用户使用自助重置工具|
|50146|需要为此应用程序配置特定于应用程序的签名密钥。 没有为此应用程序配置签名密钥，或者密钥已过期或尚未生效。 请联系应用程序所有者|
|50148|code_verifier 与 PKCE 的授权请求中提供的 code_challenge 不匹配。 请与应用程序开发人员联系。 |
|50155|此用户的设备身份验证失败|
|50158|不满足外部安全质询|
|50161|外部提供程序发送的声明不够，或者向外部提供程序请求的声明缺失|
|50166|无法将请求发送到声明提供程序|
|50169|领域不是当前服务命名空间的已配置领域。|
|50172|外部声明提供程序未获批准。 请联系租户管理员|
|50173|需要提供刷新身份验证令牌。 让用户使用刷新凭据重新签名|
|50177|直通用户不支持外部质询|
|50178|直通用户不支持会话控制|
|50180|需要 Windows 集成身份验证。 为租户启用无缝 SSO。|
|51001|本地安全标识符中不存在域提示 - 本地 UPN|
|51004|用户帐户在目录中不存在。|
|51006|需要 Windows 集成身份验证。 用户已使用缺少 wia 声明的会话令牌登录。 请求用户重新登录。|
|52004|用户尚未许可 LinkedIn 资源的访问权限。 |
|53000|条件访问策略需要符合的设备，该设备不符合。 让用户使用已批准的 MDM 提供程序（例如 Intune）登记其设备|
|53001|条件访问策略需要已加入域的设备，而该设备未加入域。 让用户使用已加入域的设备|
|53002|使用的应用程序不是支持条件访问的获批准应用程序。 用户需使用可用的获批准应用程序列表中的某个应用才能获取访问权限。|
|53003|访问因条件访问策略而被阻止。|
|53004|在访问此内容之前，用户需要完成多重身份验证注册过程。 用户应注册多重身份验证。|
|65001|应用程序 X 无权访问应用程序 Y，或者权限已被吊销。 或者，用户或管理员尚未同意将应用程序与 ID X 配合使用。请发送针对该用户和资源的交互式授权请求。 或者，用户或管理员尚未同意将应用程序与 ID X 配合使用。请代表应用 Y 向租户管理员发送针对资源 Z 的授权请求。|
|65004|用户拒绝许可该应用的访问权限。 让用户重试登录并许可应用|
|65005|应用程序所需的资源访问列表不包含可以通过资源来发现的应用程序，或者客户端应用程序请求访问的资源未在其必需的资源访问列表中指定，或者 Graph 服务返回了错误的请求，或者资源找不到。 如果应用程序支持 SAML，则原因可能是使用错误的标识符（实体）配置了应用程序。 使用以下链接，尝试针对 SAML 列出的解决方法：[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list)|
|70000|以下原因导致授权无效：<ul><li>请求的 SAML 2.0 断言包含无效的使用者确认方法</li><li>V2 不支持应用 OnBehalfOf 流</li><li>未使用会话密钥为主要刷新令牌签名</li><li>外部刷新令牌无效</li><li>获取了其他租户的访问权限授权。</li></ul>|
|70001|在名为 Y 的租户中找不到名为 X 的应用程序。如果标识符为 X 的应用程序尚未由租户管理员安装，或者尚未获得租户中的任何用户同意，则可能会发生这种情况。 可能错误配置了应用程序的标识符值，或者将身份验证请求发送到了错误的租户|
|70002|应用程序返回了无效的客户端凭据。 请联系应用所有者|
|70003|应用程序返回了不受支持的授权类型。 请联系应用所有者|
|70004|应用程序返回了无效的重定向 URI。 客户端指定的重定向地址与配置的任何地址或者 OIDC 批准列表中的任何地址都不匹配。 请联系应用所有者|
|70005|由于以下原因，应用程序返回了不受支持的响应类型：<ul><li>没有为应用程序启用响应类型“token”</li><li>响应类型“id_token”需要“OpenID”作用域 - 编码的 wctx 中包含不支持的 OAuth 参数值</li></ul>请联系应用程序所有者|
|70007|请求令牌时，应用程序返回了不受支持的“response_mode”值。 请联系应用所有者|
|70008|提供的授权代码或刷新令牌已过期 - 已吊销。 让用户重试登录|
|70011|应用程序请求的作用域无效。 请联系应用程序所有者|
|70012|对 MSA（使用者）用户进行身份验证时发生服务器错误。 请重试。 如果仍然失败，请[开具支持票证](fundamentals/active-directory-troubleshooting-support-howto.md) |
|70018|由于用户为设备代码流键入了错误的用户代码，验证码无效。 授权未获批准|
|70019|验证码已过期。 让用户重试登录|
|70037|提供了错误的质询响应。 已拒绝远程控制身份验证会话。|
|75001|SAML 消息绑定期间出错。|
|75003|应用程序返回了与不受支持的绑定相关的错误（无法通过 HTTP POST 以外的绑定发送 SAML 协议响应）。 请联系应用所有者|
|75005|Azure AD 不支持应用程序针对单一登录所发送的 SAML 请求。 请联系应用所有者|
|75008|由于 SAML 请求的目标不符合预期，来自应用程序的请求被拒绝。 请联系应用所有者|
|75011|用户在服务中用于身份验证的身份验证方法与请求的身份验证方法不匹配。 请联系应用所有者|
|75016|SAML2 身份验证请求包含无效的 NameIdPolicy。 请联系应用所有者|
|80001|身份验证代理无法连接到 Active Directory。 请务必在可为用户登录请求提供服务的 DC 建立直接连接的、已加入域的计算机上安装身份验证代理。|
|80002|内部错误。 密码验证请求超时。我们无法将身份验证请求发送到内部混合标识服务。 请[开具支持票证](fundamentals/active-directory-troubleshooting-support-howto.md)，获取有关该错误的更多详细信息|
|80003|身份验证代理收到的响应无效。 尝试对本地 Active Directory 进行身份验证时发生未知错误。 请[开具支持票证](fundamentals/active-directory-troubleshooting-support-howto.md)，获取有关该错误的更多详细信息。|
|80005|身份验证代理：处理来自身份验证代理的响应时出现未知错误。 请[开具支持票证](fundamentals/active-directory-troubleshooting-support-howto.md)，获取有关该错误的更多详细信息。|
|80007|身份验证代理无法验证用户的密码。|
|80010|身份验证代理无法解密密码。 |
|80011|身份验证代理无法检索加密密钥。|
|80012|用户尝试在允许的小时（在 AD 中指定）以外登录|
|80013|由于运行身份验证代理的计算机与 AD 之间存在时间偏差，身份验证尝试无法完成。 解决时间同步问题|
|80014|身份验证代理超时。[开具支持票证](fundamentals/active-directory-troubleshooting-support-howto.md)并提供错误代码、相关性 ID和日期时间，以获取有关此错误的更多详细信息|
|81001|用户的 Kerberos 票证太大。 如果用户处于过多的组中，从而使得 Kerberos 票证包含过多的组成员身份，则可能会出现此问题。 减少用户的组成员身份，然后重试。|
|81005|身份验证包不受支持|
|81007|没有为租户启用无缝 SSO|


## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Azure Active Directory 门户中的登录活动报告](active-directory-reporting-activity-sign-ins.md)。
