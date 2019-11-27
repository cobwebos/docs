---
title: 使用活动报表将 AD FS 应用移动到 Azure Active Directory |Microsoft Docs "
description: Active Directory 联合身份验证服务（AD FS）应用程序活动报表可让你快速将应用程序从 AD FS 迁移到 Azure Active Directory （Azure AD）。 此 AD FS 迁移工具标识与 Azure AD 的兼容性，并提供迁移指南。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 11/22/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: de64385e21604188a5c9002f2e007dad86b2674c
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420430"
---
# <a name="use-the-ad-fs-application-activity-report-preview-to-migrate-applications-to-azure-ad"></a>使用 AD FS 应用程序活动报告（预览版）将应用程序迁移到 Azure AD

许多组织使用 Active Directory 联合身份验证服务（AD FS）来提供云应用程序的单一登录。 将 AD FS 应用程序迁移到 Azure AD 进行身份验证有很大的优势，特别是在成本管理、风险管理、生产力、合规性和监管方面。 但了解哪些应用程序与 Azure AD 兼容，确定具体的迁移步骤可能会非常耗时。

Azure 门户中的 AD FS 应用程序活动报告（预览）可让你快速识别哪些应用程序能够迁移到 Azure AD。 它会评估所有 AD FS 应用程序的兼容性 Azure AD、检查是否有任何问题，并提供有关准备要迁移的单个应用程序的指南。 通过 AD FS 应用程序活动报告，你可以：

* **发现 AD FS 应用程序并确定迁移范围。** "AD FS 应用程序活动" 报表列出组织中的所有 AD FS 应用程序，并指示它们在迁移到 Azure AD 时的准备情况。
* **确定要迁移的应用程序的优先级。** 获取过去1、7或30天内登录到应用程序的唯一用户数，以帮助确定迁移应用程序的关键程度或风险。
* **运行迁移测试并解决问题。** 报表服务会自动运行测试，以确定应用程序是否已准备好进行迁移。 结果将显示在 "AD FS 应用程序活动报表" 中作为迁移状态。 如果发现潜在的迁移问题，则可以获取有关如何解决这些问题的具体指导。

AD FS 应用程序活动数据适用于分配了下列任意管理角色的用户：全局管理员、报表读者、安全读者、应用程序管理员或云应用程序管理员。

## <a name="prerequisites"></a>先决条件

* 你的组织当前必须使用 AD FS 来访问应用程序。
* 必须在 Azure AD 租户中启用 Azure AD Connect Health。
   * [详细了解 Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)
   * [开始设置 Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>发现可以迁移 AD FS 应用程序 

"AD FS 应用程序活动" 报表在 "Azure AD**使用 & insights**报告" 下的 Azure 门户中可用。 AD FS 应用程序活动报表分析每个 AD FS 应用程序，以确定它是否可以按原样迁移，或者是否需要进行其他审阅。 

1. 使用有权访问 AD FS 应用程序活动数据的管理员角色（全局管理员、报表读者、安全读者、应用程序管理员或云应用程序管理员）登录[Azure 门户](https://portal.azure.com)。

2. 选择 " **Azure Active Directory**"，然后选择 "**企业应用程序**"。

3. 在 "**活动**" 下，选择 "**使用情况 & Insights （预览版）** "，然后选择 " **AD FS 应用程序活动**" 以打开组织中所有 AD FS 应用程序的列表。

   ![AD FS 应用程序活动](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. 对于 "AD FS 应用程序活动列表中的每个应用程序，查看**迁移状态**：

   * "已**准备好迁移**" 意味着 Azure AD 完全支持 AD FS 应用程序配置，并且可以按原样迁移。

   * **需要检查**意味着某些应用程序的设置可以迁移到 Azure AD，但你需要查看不能按原样迁移的设置。

   * **所需的其他步骤**意味着 Azure AD 不支持某些应用程序设置，因此无法在其当前状态下迁移应用程序。

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>评估要迁移的应用程序的准备情况 

1. 在 AD FS 应用程序活动列表中，单击 "**迁移状态**" 列中的状态以打开 "迁移详细信息"。 你会看到通过的配置测试的摘要，以及任何潜在的迁移问题。

   ![迁移详细信息](media/migrate-adfs-application-activity/migration-details.png)

2. 单击消息以打开其他迁移规则详细信息。 有关已测试属性的完整列表，请参阅下面的[AD FS 应用程序配置测试](#ad-fs-application-configuration-tests)表。

   ![迁移规则详细信息](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>AD FS 应用程序配置测试

下表列出了在 AD FS 应用程序上执行的所有配置测试。

|结果  |通过/警告/失败  |说明  |
|---------|---------|---------|
|ADFSRPAdditionalAuthenticationRules <br> 至少检测到 AdditionalAuthentication 的一个非可迁移规则。       | 通过/警告          | 依赖方包含用于提示进行多重身份验证（MFA）的规则。 若要移动到 Azure AD，请将这些规则转换为条件访问策略。 如果你使用的是本地 MFA，则建议移动到 Azure MFA。 [了解有关条件性访问的详细信息](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)。        |
|ADFSRPAdditionalWSFedEndpoint <br> 信赖方的 AdditionalWSFedEndpoint 设置为 true。       | 通过/失败          | AD FS 中的信赖方允许多个 WS-ADDRESSING 断言终结点。 目前 Azure AD 仅支持一个。 如果有这样的情况，该结果会阻止迁移，请[告诉我们](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints)。     |
|ADFSRPAllowedAuthenticationClassReferences <br> 依赖方已设置 AllowedAuthenticationClassReferences。       | 通过/失败          | 通过 AD FS 中的此设置，你可以指定应用程序是否配置为仅允许某些身份验证类型。 建议使用条件性访问来实现此功能。  如果有这样的情况，该结果会阻止迁移，请[告诉我们](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication)。  [了解有关条件性访问的详细信息](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)。          |
|ADFSRPAlwaysRequireAuthentication <br> AlwaysRequireAuthenticationCheckResult      | 通过/失败          | 通过 AD FS 中的此设置，你可以指定应用程序是否配置为忽略 SSO cookie 并**始终提示进行身份验证**。 在 Azure AD 中，可以使用条件性访问策略来管理身份验证会话，以实现类似的行为。 [详细了解如何使用条件访问配置身份验证会话管理](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)。          |
|ADFSRPAutoUpdateEnabled <br> 依赖方已将 AutoUpdateEnabled 设置为 true       | 通过/警告          | AD FS 中的此设置允许您指定是否将 AD FS 配置为基于联合元数据中的更改自动更新应用程序。 Azure AD 目前尚不支持此支持，但不应阻止将应用程序迁移到 Azure AD。           |
|ADFSRPClaimsProviderName <br> 信赖方启用了多个 ClaimsProviders       | 通过/失败          | AD FS 中的此设置将调用信赖方接受声明的标识提供者。 在 Azure AD 中，可以使用 Azure AD B2B 启用外部协作。 [详细了解 AZURE AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)。          |
|ADFSRPDelegationAuthorizationRules      | 通过/失败          | 应用程序定义了自定义委派授权规则。 这是一种 WS-TRUST 概念，通过使用新的身份验证协议（例如 OpenID Connect 和 OAuth 2.0） Azure AD 支持。 [了解有关 Microsoft 标识平台的详细信息](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc)。          |
|ADFSRPImpersonationAuthorizationRules       | 通过/警告          | 应用程序定义了自定义模拟授权规则。 这是一种 WS-TRUST 概念，通过使用新的身份验证协议（例如 OpenID Connect 和 OAuth 2.0） Azure AD 支持。 [了解有关 Microsoft 标识平台的详细信息](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc)。          |
|ADFSRPIssuanceAuthorizationRules <br> 至少检测到 IssuanceAuthorization 的一个非可迁移规则。       | 通过/警告          | 应用程序具有在 AD FS 中定义的自定义颁发授权规则。 Azure AD 通过 Azure AD 条件访问支持此功能。 [了解有关条件性访问的详细信息](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)。 <br> 你还可以通过分配给应用程序的用户或组限制对应用程序的访问。 [详细了解如何分配用户和组来访问应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)。            |
|ADFSRPIssuanceTransformRules <br> 至少检测到 IssuanceTransform 的一个非可迁移规则。       | 通过/警告          | 应用程序具有在 AD FS 中定义的自定义颁发转换规则。 Azure AD 支持自定义令牌中颁发的声明。 若要了解详细信息，请参阅[自定义用于企业应用程序的 SAML 令牌中颁发的声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。           |
|ADFSRPMonitoringEnabled <br> 信赖方的 MonitoringEnabled 设置为 true。       | 通过/警告          | AD FS 中的此设置允许您指定是否将 AD FS 配置为基于联合元数据中的更改自动更新应用程序。 Azure AD 目前尚不支持此支持，但不应阻止将应用程序迁移到 Azure AD。           |
|ADFSRPNotBeforeSkew <br> NotBeforeSkewCheckResult      | 通过/警告          | AD FS 允许基于 SAML 令牌中的 NotBefore 和 NotOnOrAfter 时间进行时间偏差。 默认情况下，Azure AD 会自动处理这种情况。          |
|ADFSRPRequestMFAFromClaimsProviders <br> 信赖方的 RequestMFAFromClaimsProviders 设置为 true。       | 通过/警告          | AD FS 中的此设置确定用户来自不同声明提供程序时的 MFA 行为。 在 Azure AD 中，可以使用 Azure AD B2B 启用外部协作。 然后，你可以应用条件性访问策略来保护来宾访问权限。 详细了解[AZURE AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)和[条件性访问](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)。          |
|ADFSRPSignedSamlRequestsRequired <br> 依赖方已将 SignedSamlRequestsRequired 设置为 true       | 通过/失败          | 在 AD FS 中配置该应用程序以验证 SAML 请求中的签名。 Azure AD 接受签名的 SAML 请求;但是，它不会验证该签名。 Azure AD 具有不同的方法来防范恶意调用。 例如，Azure AD 使用在应用程序中配置的回复 Url 来验证 SAML 请求。 Azure AD 只会将令牌发送到为应用程序配置的回复 Url。 如果有这样的情况，该结果会阻止迁移，请[告诉我们](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature)。          |
|ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | 通过/警告         | 应用程序配置为使用自定义令牌生存期。 AD FS 默认值为一小时。 Azure AD 使用条件性访问支持此功能。 若要了解详细信息，请参阅[使用条件访问配置身份验证会话管理](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)。          |
|信赖方设置为加密声明。 此 Azure AD 支持       | 通过          | 在 Azure AD 中，可以加密发送到应用程序的令牌。 若要了解详细信息，请参阅[Configure AZURE AD SAML 令牌 encryption](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)。          |
|EncryptedNameIdRequiredCheckResult      | 通过/失败          | 应用程序配置为加密 SAML 令牌中的 nameID 声明。 在 Azure AD 中，可以加密发送到应用程序的整个令牌。 尚不支持加密特定声明。 若要了解详细信息，请参阅[Configure AZURE AD SAML 令牌 encryption](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)。         |

## <a name="check-the-results-of-claim-rule-tests"></a>检查声明规则测试的结果

如果已为 AD FS 中的应用程序配置了声明规则，则体验将为所有声明规则提供具体的分析。 你将看到哪些声明规则可以移动到 Azure AD，以及哪些声明规则需要进一步查看。

1. 在 AD FS 应用程序活动列表中，单击 "**迁移状态**" 列中的状态以打开 "迁移详细信息"。 你会看到通过的配置测试的摘要，以及任何潜在的迁移问题。

2. 在 "**迁移规则详细信息**" 页上，展开结果以显示有关潜在迁移问题的详细信息，并获取其他指导。 有关测试的所有声明规则的详细列表，请参阅下面的[检查声明规则测试的结果](#check-the-results-of-claim-rule-tests)表。

   下面的示例显示了 IssuanceTransform 规则的迁移规则详细信息。 它列出了声明中需要查看和解决的特定部分，然后才能将应用程序迁移到 Azure AD。

   ![迁移规则详细介绍其他指南](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>声明规则测试

下表列出了对 AD FS 应用程序执行的所有声明规则测试。

|属性  |说明  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | Condition 语句使用正则表达式来计算声明是否与特定模式匹配。  若要在 Azure AD 中实现类似的功能，可以使用预定义的转换，例如 IfEmpty （）、StartWith （）、Contains （），等等。 有关详细信息，请参阅[自定义用于企业应用程序的 SAML 令牌中颁发的声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。          |
|UNSUPPORTED_CONDITION_CLASS      | Condition 语句包含多个需要在运行发出语句之前计算的条件。 Azure AD 可以通过声明的转换函数（可在其中评估多个声明值）支持此功能。  有关详细信息，请参阅[自定义用于企业应用程序的 SAML 令牌中颁发的声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。          |
|UNSUPPORTED_RULE_TYPE      | 无法识别声明规则。 有关如何在 Azure AD 中配置声明的详细信息，请参阅[自定义用于企业应用程序的 SAML 令牌中颁发的声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | Condition 语句使用 Azure AD 中不支持的颁发者。 目前，Azure AD 不是 Active Directory 或 Azure AD 存储不同的源声明。 如果这会阻止你将应用程序迁移到 Azure AD，请[告知我们](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)。         |
|UNSUPPORTED_CONDITION_FUNCTION      | Condition 语句使用聚合函数发出或添加单个声明，而不考虑匹配的数目。  在 Azure AD 中，你可以使用 IfEmpty （）、StartWith （）、Contains （）等函数来评估用户的属性，以确定要用于该声明的值。 有关详细信息，请参阅[自定义用于企业应用程序的 SAML 令牌中颁发的声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。          |
|RESTRICTED_CLAIM_ISSUED      | Condition 语句使用 Azure AD 中限制的声明。 你或许能够颁发受限声明，但不能修改其源或应用任何转换。 有关详细信息，请参阅[在 Azure AD 中的特定应用程序的令牌中发出自定义声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)。          |
|EXTERNAL_ATTRIBUTE_STORE      | 发出语句使用 Active Directory 不同的属性存储。 目前，Azure AD 不是 Active Directory 或 Azure AD 存储不同的源声明。 如果此结果阻止你将应用程序迁移到 Azure AD，请[告知我们](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)。          |
|UNSUPPORTED_ISSUANCE_CLASS      | 颁发语句使用 ADD 向传入声明集添加声明。 在 Azure AD 中，这可能配置为多个声明转换。  有关详细信息，请参阅[自定义用于企业应用程序的 SAML 令牌中颁发的声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)。         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | 此颁发语句使用正则表达式来转换要发出的声明的值。 若要在 Azure AD 中实现类似的功能，可以使用预定义的转换，如析取（）、Trim （）、ToLower 等。 有关详细信息，请参阅[自定义用于企业应用程序的 SAML 令牌中颁发的声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。          |


## <a name="next-steps"></a>后续步骤

- [使用 Azure Active Directory 管理应用程序](what-is-application-management.md)
- [管理对应用的访问权限](what-is-access-management.md)
- [Azure AD Connect 联合身份验证](../hybrid/how-to-connect-fed-whatis.md)
