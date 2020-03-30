---
title: 使用活动报告将 AD FS 应用移动到 Azure 活动目录 |微软文档
description: 活动目录联合服务 （AD FS） 应用程序活动报告允许您快速将应用程序从 AD FS 迁移到 Azure 活动目录 （Azure AD）。 此 AD FS 迁移工具可识别与 Azure AD 的兼容性，并提供有关迁移指南。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/14/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 333e440fdd5f5062dda45fb12a83543c63e66c04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978039"
---
# <a name="use-the-ad-fs-application-activity-report-preview-to-migrate-applications-to-azure-ad"></a>使用 AD FS 应用程序活动报告（预览）将应用程序迁移到 Azure AD

许多组织使用 Active 目录联合服务 （AD FS） 为云应用程序提供单一登录。 将 AD FS 应用程序移动到 Azure AD 进行身份验证具有显著优势，尤其是在成本管理、风险管理、生产力、合规性和治理方面。 但是，了解哪些应用程序与 Azure AD 兼容并确定特定的迁移步骤可能非常耗时。

Azure 门户中的 AD FS 应用程序活动报告（预览）可让您快速识别哪些应用程序能够迁移到 Azure AD。 它评估所有 AD FS 应用程序与 Azure AD 的兼容性，检查任何问题，并提供有关准备各个应用程序进行迁移的指导。 使用 AD FS 应用程序活动报告，您可以：

* **了解 AD FS 应用程序并扩大迁移范围。** AD FS 应用程序活动报告列出了组织中的所有 AD FS 应用程序，并指示它们准备迁移到 Azure AD。
* **优先处理应用程序的迁移。** 获取过去 1、7 或 30 天内登录到应用程序的唯一用户数，以帮助确定迁移应用程序的重要性或风险。
* **运行迁移测试并修复问题。** 报告服务自动运行测试以确定应用程序是否已准备好迁移。 结果在 AD FS 应用程序活动报表中显示为迁移状态。 如果确定了潜在的迁移问题，您将获得有关如何解决这些问题的具体指导。

AD FS 应用程序活动数据可供分配这些管理员角色的用户使用：全局管理员、报表阅读器、安全读取器、应用程序管理员或云应用程序管理员。

## <a name="prerequisites"></a>先决条件

* 您的组织当前必须使用 AD FS 访问应用程序。
* 必须在 Azure AD 租户中启用 Azure AD 连接运行状况。
   * [了解有关 Azure AD 连接运行状况的更多](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)
   * [开始设置 Azure AD 连接运行状况](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>了解可迁移的 AD FS 应用程序 

AD FS 应用程序活动报告在 Azure**使用&见解**报告下的 Azure 门户中可用。 AD FS 应用程序活动报告分析每个 AD FS 应用程序，以确定是否可以根据需要迁移，或者是否需要进行其他审核。 

1. 使用有权访问 AD FS 应用程序活动数据的管理员角色（全局管理员、报表读取器、安全读取器、应用程序管理员或云应用程序管理员）登录到[Azure 门户](https://portal.azure.com)。

2. 选择**Azure 活动目录**，然后选择**企业应用程序**。

3. 在 **"活动**"下，选择 **"使用&见解（预览"），** 然后选择**AD FS 应用程序活动**以打开组织中所有 AD FS 应用程序的列表。

   ![AD FS 应用程序活动](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. 对于 AD FS 应用程序活动列表中的每个应用程序，查看**迁移状态**：

   * **准备迁移**意味着在 Azure AD 中完全支持 AD FS 应用程序配置，可以按照现在的方式迁移。

   * **需求查看**意味着应用程序的某些设置可以迁移到 Azure AD，但您需要查看无法以方式迁移的设置。

   * **所需的其他步骤**意味着 Azure AD 不支持应用程序的某些设置，因此无法以当前状态迁移应用程序。

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>评估应用程序的迁移准备情况 

1. 在 AD FS 应用程序活动列表中，单击 **"迁移状态**"列中的状态以打开迁移详细信息。 您将看到通过的配置测试的摘要，以及任何潜在的迁移问题。

   ![迁移详细信息](media/migrate-adfs-application-activity/migration-details.png)

2. 单击一条消息以打开其他迁移规则详细信息。 有关测试的属性的完整列表，请参阅下面的 AD [FS 应用程序配置测试](#ad-fs-application-configuration-tests)表。

   ![迁移规则详细信息](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>AD FS 应用程序配置测试

下表列出了在 AD FS 应用程序上执行的所有配置测试。

|结果  |通过/警告/失败  |描述  |
|---------|---------|---------|
|测试-ADFSRP 附加身份验证规则 <br> 检测到至少一个不可迁移的规则用于其他身份验证。       | 通过/警告          | 依赖方具有提示多重身份验证 （MFA） 的规则。 要移动到 Azure AD，可以将这些规则转换为条件访问策略。 如果使用本地 MFA，我们建议您迁移到 Azure MFA。 [了解有关条件访问 的更多。](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)        |
|测试-ADFSRP附加WS馈点 <br> 依赖方已设置为 true 的附加 WSFed 终结点。       | 通过/失败          | AD FS 中的依赖方允许多个 WS-Fed 断言终结点。目前，Azure AD 仅支持一个。如果您有此结果阻止迁移的情况，[请告诉我们](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints)。     |
|测试-ADFSRP 允许的身份验证类引用 <br> 依赖方已设置允许的身份验证类引用。       | 通过/失败          | AD FS 中的此设置允许您指定应用程序是否配置为仅允许某些身份验证类型。 我们建议使用条件访问来实现此功能。 如果您有此结果阻止迁移的情况，[请告诉我们](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication)。  [了解有关条件访问 的更多。](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)          |
|测试-ADFSRP始终需要认证 <br> 始终需要身份验证检查结果      | 通过/失败          | 通过 AD FS 中的此设置，您可以指定应用程序是否配置为忽略 SSO cookies 和**始终提示身份验证**。 在 Azure AD 中，可以使用条件访问策略管理身份验证会话以实现类似的行为。 [了解有关使用条件访问配置身份验证会话管理。](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)          |
|已启用测试 ADFSRP 自动更新 <br> 依赖方已自动更新启用设置为 true       | 通过/警告          | 通过 AD FS 中的此设置，您可以指定 AD FS 是否配置为根据联合元数据中的更改自动更新应用程序。 Azure AD 今天不支持此功能，但不应阻止应用程序迁移到 Azure AD。           |
|测试-ADFSRPclaims提供程序名称 <br> 依赖方已启用多个索赔提供程序       | 通过/失败          | AD FS 中的此设置会调用依赖方接受声明的标识提供程序。 在 Azure AD 中，可以使用 Azure AD B2B 启用外部协作。 [了解有关 Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)的更多。          |
|测试-ADFSRP 授权规则      | 通过/失败          | 应用程序已定义自定义委派授权规则。 这是 Azure AD 通过使用现代身份验证协议（如 OpenID 连接和 OAuth 2.0）支持的 WS 信任概念。 [了解有关微软身份平台的更多信息](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc)。          |
|测试-ADFSRP模拟授权规则       | 通过/警告          | 应用程序已定义自定义模拟授权规则。这是 Azure AD 通过使用现代身份验证协议（如 OpenID 连接和 OAuth 2.0）支持的 WS 信任概念。 [了解有关微软身份平台的更多信息](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc)。          |
|测试-ADFSRP颁发授权规则 <br> 至少检测到一个不可迁移的规则用于颁发授权。       | 通过/警告          | 该应用程序具有在 AD FS 中定义的自定义颁发授权规则。Azure AD 支持 Azure AD 条件访问此功能。 [了解有关条件访问 的更多。](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) <br> 您还可以限制分配给应用程序的用户或组对应用程序的访问。 [详细了解如何分配用户和组来访问应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)。            |
|测试-ADFSRP发放转换规则 <br> 至少检测到一个不可迁移的规则用于颁发转换。       | 通过/警告          | 该应用程序具有在 AD FS 中定义的自定义颁发转换规则。 Azure AD 支持自定义令牌中发出的声明。 要了解更多信息，请参阅[为企业应用程序的 SAML 令牌中发出的自定义声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。           |
|已启用测试 ADFSRP 监控 <br> 依托方已监控启用设置为 true。       | 通过/警告          | 通过 AD FS 中的此设置，您可以指定 AD FS 是否配置为根据联合元数据中的更改自动更新应用程序。 Azure AD 今天不支持此功能，但不应阻止应用程序迁移到 Azure AD。           |
|测试-ADFSRPnot前 <br> 未在支票结果之前      | 通过/警告          | AD FS 允许基于 SAML 令牌中的"之前"和"不OnOrOrAfter 时间"的时间偏斜。 默认情况下，Azure AD 会自动处理此问题。          |
|测试-ADFSRP请求来自索赔提供者 <br> 依托方已请求"请求"方方设置为 true。       | 通过/警告          | AD FS 中的此设置确定当用户来自不同声明提供程序时 MFA 的行为。 在 Azure AD 中，可以使用 Azure AD B2B 启用外部协作。 然后，您可以应用条件访问策略来保护来宾访问。 了解有关[Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)和[条件访问](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)的更多。          |
|测试-ADFSRP签名沙姆请求要求 <br> 依赖方已签署 Saml 请求要求设置为 true       | 通过/失败          | 应用程序在 AD FS 中配置，以验证 SAML 请求中的签名。 Azure AD 接受已签名的 SAML 请求;但是，它将不会验证签名。 Azure AD 具有不同的方法来防止恶意调用。 例如，Azure AD 使用应用程序中配置的回复 URL 来验证 SAML 请求。 Azure AD 将仅发送令牌以答复为应用程序配置的 URL。 如果您有此结果阻止迁移的情况，[请告诉我们](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature)。          |
|测试-ADFSRP令牌终身 <br> 令牌终身检查结果        | 通过/警告         | 应用程序配置为自定义令牌生存期。 AD FS 默认值为 1 小时。Azure AD 支持使用条件访问此功能。 要了解更多信息，请参阅[使用条件访问配置身份验证会话管理](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)。          |
|依赖方设置为加密索赔。 Azure AD 支持此功能       | 通过          | 使用 Azure AD，可以加密发送到应用程序的令牌。 要了解更多信息，请参阅[配置 Azure AD SAML 令牌加密](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)。          |
|加密名称 Id 必需检查结果      | 通过/失败          | 应用程序配置为加密 SAML 令牌中的名称 ID 声明。使用 Azure AD，可以加密发送到应用程序的整个令牌。尚不支持对特定声明进行加密。 要了解更多信息，请参阅[配置 Azure AD SAML 令牌加密](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)。         |

## <a name="check-the-results-of-claim-rule-tests"></a>检查声明规则测试结果

如果您在 AD FS 中为应用程序配置了声明规则，则该体验将为所有声明规则提供精细分析。 您将看到哪些声明规则可以移动到 Azure AD，哪些规则需要进一步查看。

1. 在 AD FS 应用程序活动列表中，单击 **"迁移状态**"列中的状态以打开迁移详细信息。 您将看到通过的配置测试的摘要，以及任何潜在的迁移问题。

2. 在 **"迁移规则详细信息**"页上，展开结果以显示有关潜在迁移问题的详细信息，并获得其他指导。 有关测试的所有声明规则的详细列表，请参阅下面的["检查声明规则测试结果](#check-the-results-of-claim-rule-tests)"表。

   下面的示例显示了颁发转换规则的迁移规则详细信息。 它列出了在将应用程序迁移到 Azure AD 之前需要审阅和解决的声明的特定部分。

   ![迁移规则详细介绍了其他指南](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>声明规则测试

下表列出了在 AD FS 应用程序上执行的所有声明规则测试。

|properties  |描述  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | 条件语句使用正则表达式计算声明是否与特定模式匹配。要在 Azure AD 中实现类似的功能，可以使用预定义的转换，如 IfEmpty（）、StartWith（）、包含（等）。 有关详细信息，请参阅[为企业应用程序的 SAML 令牌中发出的自定义声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。          |
|UNSUPPORTED_CONDITION_CLASS      | 条件语句具有多个条件，在运行颁发语句之前需要对其进行评估。Azure AD 可能通过声明的转换函数支持此功能，您可以在其中评估多个声明值。有关详细信息，请参阅[为企业应用程序的 SAML 令牌中发出的自定义声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。          |
|UNSUPPORTED_RULE_TYPE      | 无法识别声明规则。 有关如何在 Azure AD 中配置声明的详细信息，请参阅[为企业应用程序的 SAML 令牌中发出的自定义声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | 条件语句使用 Azure AD 中不支持的颁发者。目前，Azure AD 不会从不同活动目录或 Azure AD 的存储源声明。 如果这阻止您将应用程序迁移到 Azure AD，[请告诉我们](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)。         |
|UNSUPPORTED_CONDITION_FUNCTION      | 条件语句使用聚合函数发出或添加单个声明，而不考虑匹配数。在 Azure AD 中，可以计算用户的属性，以决定使用 IfEmpty（）、StartWith（）、包含（等函数）对声明使用的值。有关详细信息，请参阅[为企业应用程序的 SAML 令牌中发出的自定义声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。          |
|RESTRICTED_CLAIM_ISSUED      | 条件语句使用在 Azure AD 中受到限制的声明。 您可能能够发出受限声明，但不能修改其源或应用任何转换。 有关详细信息，请参阅在[Azure AD 中为特定应用自定义在令牌中发出的声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)。          |
|EXTERNAL_ATTRIBUTE_STORE      | 颁发语句使用与活动目录不同的属性存储。 目前，Azure AD 不会从不同活动目录或 Azure AD 的存储源声明。 如果此结果阻止您将应用程序迁移到 Azure AD，[请告诉我们](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)。          |
|UNSUPPORTED_ISSUANCE_CLASS      | 发行语句使用 ADD 向传入声明集添加声明。 在 Azure AD 中，这可能配置为多个声明转换。有关详细信息，请参阅[为企业应用程序的 SAML 令牌中发出的自定义声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)。         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | 颁发语句使用正则表达式来转换要发出的声明的值。要在 Azure AD 中实现类似的功能，可以使用预定义的转换，如提取（）、修剪（）、ToLower 等。 有关详细信息，请参阅[为企业应用程序的 SAML 令牌中发出的自定义声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。          |


## <a name="next-steps"></a>后续步骤

- [视频：如何使用 AD FS 活动报告迁移应用程序](https://www.youtube.com/watch?v=OThlTA239lU)
- [使用 Azure 活动目录管理应用程序](what-is-application-management.md)
- [管理对应用的访问权限](what-is-access-management.md)
- [Azure AD Connect 联合身份验证](../hybrid/how-to-connect-fed-whatis.md)
