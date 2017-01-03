---
title: "Azure Active Directory 审核报告事件 | Microsoft 文档"
description: "可从 Azure Active Directory 查看和下载的已审核事件"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: mbaldwin
editor: 
ms.assetid: 307eedf7-05bc-448d-a84d-bead5a4c5770
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/14/2016
ms.author: dhanyahk
translationtype: Human Translation
ms.sourcegitcommit: 6950072b8970dfc1b80674efe0be6cb7496fd5ce
ms.openlocfilehash: 2dda60160bbcaa729bc8e5cec4f47efc10d120cf


---
# <a name="azure-active-directory-audit-report-events"></a>Azure Active Directory 审核报告事件
*本文档是 [Azure Active Directory 报告指南](active-directory-reporting-guide.md)的一部分。*

Azure Active Directory 审核报告可帮助客户识别其 Azure Active Directory 中发生的特权操作。 特权操作包括提升更改（例如，创建角色或密码重置）、更改策略配置（例如密码策略）或更改目录配置（例如，对域联合身份验证设置的更改）。 报告提供了事件名称的审核记录、操作执行者、更改影响的目标资源，以及日期和时间 (UTC)。 客户可以根据 [View your Audit Logs](active-directory-reporting-azure-portal.md)（查看审核日志）中所述，通过 [Azure 门户](https://portal.azure.com/)检索其 Azure Active Directory 的审核事件列表。

## <a name="list-of-audit-report-events"></a>审核报告事件列表
<!--- audit event descriptions should be in the past tense --->

| 事件 | 事件说明 |
| --- | --- |
| **用户事件** | |
| 添加用户 |已将用户添加到目录。 |
| 删除用户 |已从目录中删除用户。 |
| 设置许可证属性 |已设置目录中用户的许可证属性。 |
| 重置用户密码 |已重置目录中用户的密码。 |
| 更改用户密码 |已更改目录中用户的密码。 |
| 更改用户许可证 |已更改分配给目录中用户的许可证。 若要查看哪些许可证已更新，请查看下面的“更新用户”属性[](#update-user-attributes) |
| 更新用户 |已更新目录中的用户。 有关可更新的属性，请[参阅下文](#update-user-attributes)。 |
| 设置强制更改用户密码 |已设置强制用户在登录时更改其密码的属性。 |
| 更新用户凭据 |用户已更改密码 |
| **组事件** | |
| 添加组 |已在目录中创建组。 |
| 更新组 |已在目录中更新组。 若要查看哪些组属性已更新，请参阅以下部分中的“已审核组属性”[](#update-group-attributes) |
| 删除组 |已从目录中删除组。 |
| CreateGroupSettings |已创建组设置 |
| UpdateGroupSettings |已更新组设置。 若要查看哪些组设置已更新，请参阅以下部分中的“已审核组属性”[](#update-group-attributes) |
| DeleteGroupSettings |已删除组设置 |
| SetGroupLicense |已设置组许可证。 |
| SetGroupManagedBy |已设置要由用户管理的组 |
| AddGroupMember |已将成员添加到组 |
| RemoveGroupMember |从组中删除成员 |
| AddGroupOwner |已将所有者添加到组 |
| RemoveGroupOwner |已从组中删除所有者 |
| **应用程序事件** | |
| 添加服务主体 |已在目录中添加服务主体。 |
| 删除服务主体 |已从目录中删除服务主体。 |
| 添加服务主体凭据 |已将凭据添加到服务主体。 |
| 删除服务主体凭据 |已从服务主体中删除凭据。 |
| 添加委派条目 |已在目录中创建 [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity)。 |
| 设置委派条目 |已在目录中更新 [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity)。 |
| 删除委派条目 |已在目录中删除 [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity)。 |
| **角色事件** | |
| 将角色成员添加到角色 |已将用户添加到目录角色。 |
| 从角色中删除角色成员 |已从目录角色中删除用户。 |
| AddRoleDefinition |已添加角色定义。 |
| UpdateRoleDefinition |已更新角色定义。 若要查看哪些角色设置已更新，请参阅以下部分中的“已审核角色定义属性”[](#update-role-definition-attributes) |
| DeleteRoleDefinition |已删除角色定义。 |
| AddRoleAssignmentToRoleDefinition |已将角色分配添加到角色定义。 |
| RemoveRoleAssignmentFromRoleDefinition |已从角色定义中删除角色分配。 |
| AddRoleFromTemplate |已从模板添加角色。 |
| UpdateRole |已更新角色。 |
| AddRoleScopeMemberToRole |已将带有范围的成员添加到角色。 |
| RemoveRoleScopedMemberFromRole |已从角色中删除带有范围的成员。 |
| **设备事件（所有新事件）** | |
| AddDevice |已添加设备。 |
| UpdateDevice |已更新设备。 若要查看哪些设备属性已更新，请参阅以下部分中的“已审核设备属性”[](#update-device-attributes) |
| DeleteDevice |已删除设备 |
| AddDeviceConfiguration |已添加设备配置。 |
| UpdateDeviceConfiguration |已更新设备配置。 若要查看哪些设备配置属性已更新，请参阅以下部分中的“已审核设备配置属性”[](#update-device-configuration-attributes) |
| DeleteDeviceConfiguration |已删除设备配置。 |
| AddRegisteredOwner |已将注册的所有者添加到设备。 |
| AddRegisteredUsers |已将注册的用户添加到设备。 |
| RemoveRegisteredOwner |已从设备中删除注册的所有者。 |
| RemoveRegisteredUsers |已从设备中删除注册的用户。 |
| RemoveDeviceCredentials |删除设备凭据。 |
| **B2B 事件** | |
| 已上载批量邀请。 |管理员已上载一个文件，其中包含要发送到合作伙伴用户的邀请。 |
| 已处理批量邀请。 |已处理包含合作伙伴用户邀请的文件。 |
| 邀请外部用户。 |已邀请外部用户加入目录。 |
| 兑换外部用户邀请。 |外部用户已兑换其加入目录的邀请。 |
| 将外部用户添加到组。 |已将目录中组的成员资格分配给外部用户。 |
| 将外部用户分配到应用程序。 |已将应用程序直接访问权限分配给外部用户。 |
| 创建活跃租户。 |已通过邀请兑换在 Azure AD 中创建新租户。 |
| 创建活跃用户。 |已通过邀请兑换在 Azure AD 中为现有租户创建用户。 |
| **管理单元（所有新事件）** | |
| AddAdministrativeUnit |添加管理单元。 |
| UpdateAdministrativeUnit |更新管理单元。 若要查看哪些管理单元属性已更新，请参阅以下部分中的“已审核管理单元属性”[](#update-administrative-unit-attributes) |
| DeleteAdministrativeUnit |删除管理单元。 |
| AddMemberToAdministrativeUnit |将成员添加到管理单元。 |
| RemoveMemberFromAdministrativeUnit |从管理单元中删除成员。 |
| **目录事件** | |
| 将合作伙伴添加到公司 |已将合作伙伴添加到目录。 |
| 从公司中删除合作伙伴 |已从目录中删除合作伙伴。 |
| DemotePartner |降级合作伙伴。 |
| 将域添加到公司 |已将域添加到目录。 |
| 从公司中删除域 |已从目录中删除域。 |
| 更新域 |已更新目录中的域。 若要查看哪些域属性已更新，请参阅以下部分中的“已审核域属性”[](#update-domain-attributes) |
| 设置域身份验证 |已更改公司的默认域设置。 |
| 设置公司联系信息 |已设置公司级的联系方法首选项。 这包括营销电子邮件地址，以及有关 Microsoft Online Services 的技术通知。 |
| 在域中设置联合设置 |已更新域的联合设置。 |
| 验证域 |已验证目录中的域。 |
| 验证电子邮件验证域 |已使用电子邮件验证来验证目录中的域。 |
| 设置公司的 DirSyncEnabled 标志 |已设置用来为 Azure AD Sync 启用目录的属性。 |
| 设置密码策略 |已设置用户密码的长度和字符约束。 |
| 设置公司信息 |已更新公司级信息。 有关详细信息，请参阅 [Get-MsolCompanyInformation](https://msdn.microsoft.com/library/azure/dn194126.aspx) PowerShell cmdlet。 |
| SetCompanyAllowedDataLocation |设置公司允许的数据位置。 |
| SetCompanyDirSyncEnabled |设置 DirSyncEnabled 标志。 |
| SetCompanyDirSyncFeature |设置 DirSync 功能。 |
| SetCompanyInformation |设置公司信息。 |
| SetCompanyMultiNationalEnabled |设置为启用公司跨国功能。 |
| SetDirectoryFeatureOnTenant |在租户上设置目录功能。 |
| SetTenantLicenseProperties |设置租户许可证属性。 |
| CreateCompanySettings |创建公司设置 |
| UpdateCompanySettings |更新公司设置。 若要查看哪些公司属性已更新，请参阅以下部分中的“已审核公司属性”[](#update-company-attributes) |
| DeleteCompanySettings |删除公司设置 |
| SetAccidentalDeletionThreshold |设置意外删除阈值。 |
| SetRightsManagementProperties |设置 Rights Management 属性。 |
| PurgeRightsManagementProperties |清除 Rights Management 属性。 |
| UpdateExternalSecrets |更新外部机密 |
| **策略事件（所有新事件）** | |
| AddPolicy |添加策略。 |
| UpdatePolicy |更新策略。 |
| DeletePolicy |删除策略。 |
| AddDefaultPolicyApplication |将策略添加到应用程序。 |
| AddDefaultPolicyServicePrincipal |将策略添加到服务主体。 |
| RemoveDefaultPolicyApplication |从应用程序中删除策略。 |
| RemoveDefaultPolicyServicePrincipal |从服务主体中删除策略。 |
| RemovePolicyCredentials |删除策略凭据。 |

## <a name="audit-report-retention"></a>审核报告保留期
Azure AD 审核报告中的事件将保留 180 天。 有关保留报告的详细信息，请参阅 [Azure Active Directory 报告保留策略](active-directory-reporting-retention.md)。

对于想要以更长的保留期存储其审核事件的客户，可以使用报告 API 定期将审核事件提取到独立的数据存储中。 有关详细信息，请参阅[报告 API 入门](active-directory-reporting-api-getting-started.md)。

## <a name="properties-included-with-each-audit-event"></a>每个审核事件随附的属性
| 属性 | 说明 |
| --- | --- |
| 日期和时间 |审核事件发生的日期和时间 |
| 执行组件 |执行操作的用户或服务主体 |
| 操作 |执行的操作 |
| 目标 |执行的操作所针对的用户或服务主体 |

## <a name="update-user-attributes"></a>“更新用户”属性
“更新用户”审核事件包含有关更新了哪些用户属性的附加信息。 对于每个属性，将包含以前的值和新值。

| 属性 | 说明 |
| --- | --- |
| AccountEnabled |用户可以登录。 |
| AssignedLicense |分配给用户的所有许可证。 |
| AssignedPlan |向用户分配许可证后生成的服务计划。 |
| LicenseAssignmentDetail |有关分配给用户的许可证的详细信息。 例如，如果涉及到基于组的许可，则这包括授予许可证的组。 |
| 移动电话 |用户的手机号码。 |
| OtherMail |用户的备用电子邮件地址。 |
| OtherMobile |用户的备用手机号码。 |
| StrongAuthenticationMethod |用户为多重身份验证配置的验证方法列表，例如语音呼叫、短信或移动应用发送的验证码。 |
| StrongAuthenticationRequirement |是否为此用户强制实施、启用或禁用多重身份验证。 |
| StrongAuthenticationUserDetails |用户用于多重身份验证和密码重置验证的电话号码、备用电话号码和电子邮件地址。 |
| StrongAuthenticationPhoneAppDetail |为了执行 2FA 登录而注册的电话应用的详细信息 |
| TelephoneNumber |用户的电话号码。 |
| AlternativeSecurityId |对象的备用安全 ID。 |
| CreationType |用户的创建方法（通过邀请或促销）。 |
| InviteTicket |用户的邀请票证列表。 |
| InviteReplyUrl |接受邀请时所要回复的 url 列表。 |
| InviteResources |用户受邀的资源列表。 |
| LastDirSyncTime |上次由于从权威（客户、本地）目录同步而更新对象的时间。 |
| MSExchRemoteRecipientType |映射到 MSO 收件人类型。 请参阅 [MSO 收件人类型] https://msdn.microsoft.com/library/microsoft.office.interop.outlook.recipient.type.aspx 了解收件人类型 |
| PreferredDataLocation |用户、组、联系人、公共文件夹或设备的数据首选位置。 |
| ProxyAddresses |在外部邮件系统中用于标识 Exchange Server 收件人对象的地址。 |
| StsRefreshTokensValidFrom |传递刷新令牌吊销信息 - 在此时间之前发出的任何 STS 刷新令牌被视为已过期。 |
| UserPrincipalName |UPN 是用户的 Internet 登录名。 |
| UserState |用户 PendingApproval/PendingAcceptance/Accepted/PendingVerification 状态。 |
| UserStateChangedOn |上次更改 UserState 的时间戳。 用于触发生命周期工作流。 |
| UserType |用户的类型。 成员 (0)、来宾 (1)、促销 (2)。 |

## <a name="update-group-attributes"></a>“更新组”属性
| 属性 | 说明 |
| --- | --- |
| 分类 |统一组的分类（HBI、MBI 等）。 |
| 说明 |用户可读的对象相关说明性短语。 |
| DisplayName |对象的显示名称 |
| DirSyncEnabled |指示同步是否从权威（客户、本地）目录发生。 |
| GroupLicenseAssignment |组的许可证分配 |
| GroupType |组类型、统一 (0) |
| IsMembershipRuleLocked |指示 MembershipRule 属性由自助式组管理服务设置，用户无法更改。 只适用于 GroupType 包含 GroupType.DynamicMembership 的组 |
| IsPublic |用于指示组为公开/专用的标志。 |
| LastDirSyncTime |上次由于从权威（客户、本地）目录同步而更新对象的时间。 |
| Mail |主要电子邮件地址 |
| MailEnabled |指示此组是否有电子邮件功能。 |
| MailNickname |通讯簿对象的 Moniker，通常为其电子邮件名称前面加上 "@" 符号的部分。 |
| MembershipRule |一个字符串，表示自助式组管理服务用于确定哪些成员应属于此组的准则。 另请参阅 IsMembershipRuleLocked。 只适用于 GroupType 包含 GroupType.DynamicMembership 的组。 |
| MembershipRuleProcessingState |自助式组管理服务所定义的枚举值，用于定义此组的成员身份处理状态。 只适用于 GroupType 包含 GroupType.DynamicMembership 的组。 |
| ProxyAddresses |在外部邮件系统中用于标识 Exchange Server 收件人对象的地址。 |
| RenewedDateTime |最近更新组时的时间戳记录。 |
| SecurityEnabled |指示组中的成员身份是否可能影响授权决策。 |
| WellKnownObject |标记目录对象，并将它指定为预先定义的集之一。 |

## <a name="update-device-attributes"></a>“更新设备”属性
| 属性 | 说明 |
| --- | --- |
| AccountEnabled |指示安全主体是否可以身份验证。 |
| CloudAccountEnabled |指示安全主体是否可以身份验证。 设备在本地控制时由 InTune 写入。 |
| CloudDeviceOSType |基于 OS（如 Windows RT、iOS）的设备类型。 由云服务（例如 Intune）设置时，此属性在 DeviceOSType 的目录中具有权威性。 |
| CloudDeviceOSVersion |OS 的版本。 由云服务（例如 Intune）设置时，此属性在 DeviceOSVersion 的目录中具有权威性。 |
| CloudDisplayName |displayName LDAP 属性的值。 由云服务（例如 Intune）设置时，此属性在 displayName 的目录中具有权威性。 |
| CloudCreated |指示对象是否由云服务创建。 |
| CompliantUntil |等到何时设备被视为合规。 |
| DeviceMetadata |设备的自定义元数据 |
| DeviceObjectVersion |此属性用于标识设备的架构版本。 |
| DeviceOSType |基于 OS（如 Windows RT、iOS）的设备类型。 由注册服务写入，预定由 MDM 管理服务或 STS 轻型管理服务更新。 |
| DeviceOSVersion |OS 的版本。 由注册服务写入，预定由 MDM 管理服务或 STS 轻型管理服务更新。 |
| DevicePhysicalIds |多值属性，预定用于存储物理设备的标识符。 这可能包括 BIOS ID、TPM 指纹、硬件特定的 ID 等。 |
| DirSyncEnabled |指示同步是否从权威（客户、本地）目录发生。 |
| DisplayName |对象的显示名称 |
| IsCompliant |此属性用于管理设备的移动设备管理状态。 |
| IsManaged |此属性用于指示设备由云 MDM 管理。 |
| LastDirSyncTime |上次由于从权威（客户、本地）目录同步而更新对象的时间。 |

## <a name="update-device-configuration-attributes"></a>“更新设备配置”属性
| 属性 | 说明 |
| --- | --- |
| MaximumRegistrationInactivityPeriod |在考虑删除之前，设备可保持非活动状态的最多天数。 |
| RegistrationQuota |用于限制单个用户允许的设备注册数的策略。 |

## <a name="update-service-principal-configuration-attributes"></a>“更新服务主体配置”属性
| 属性 | 说明 |
| --- | --- |
| AccountEnabled |指示安全主体是否可以身份验证。 |
| AppPrincipalId |安全主体的外部标识（由应用程序定义）。 |
| DisplayName |对象的显示名称 |
| ServicePrincipalName |服务主体名称，其中包含“名称/颁发机构”，其中名称指定应用程序类值，颁发机构至少包含可指定服务主体标识符的“主机名[:端口]”或“名称”。 |

## <a name="update-app-attributes"></a>“更新应用”属性
| 属性 | 说明 |
| --- | --- |
| AppAddress |分配给服务主体的地址集（重定向 URL）。 |
| AppId |应用的应用程序 ID |
| AppIdentifierUri |用于标识应用程序的应用程序 URI。  通常是应用程序访问 URL。 |
| AppLogoUrl |存储在 CDN 中的应用程序徽标图像的 url。 |
| AvailableToOtherTenants |如果应用程序是多租户应用程序（即，可供其他租户使用），则为 True。 |
| DisplayName |应用程序名称的显示名称 |
| Entitlement |应用程序权利列表。 |
| ExternalUserAccountDelegationsAllowed |指示资源应用程序是否为受信任资源的标志，可为外部用户帐户创建委托项。 |
| GroupMembershipClaims |组成员身份声明策略。 |
| PublicClient |如果客户端无法保守机密（即，OAuth2.0 中的非机密客户端），则为 True |
| RecordConsentConditions |同意条件的类型，由合约条款定义：None (0)、SilentConsentForPartnerManagedApp (1)。 此值在图形 API 架构中公开，只可由租户管理员设置/更改。 |
| RequiredResourceAccess |RequiredResourceAccess 属性值的 XML 内容。 |
| WebApp |如果为 true，则表示此应用程序是 Web 应用。 |
| WwwHomepage |主要网页。 |

## <a name="update-role-attributes"></a>“更新角色”属性
| 属性 | 说明 |
| --- | --- |
| AppAddress |分配给服务主体的地址集（重定向 URL）。 |
| BelongsToFirstLoginObjectSet |如果为 true，则表示此对象属于使新租户第一个管理员能够登录所需的对象集。 |
| Builtin |指示对象的生存期是否由系统拥有。 |
| 说明 |用户可读的对象相关说明性短语。 |
| DisplayName |对象的显示名称 |
| MailNickname |通讯簿对象的 Moniker，通常为其电子邮件名称前面加上 "@" 符号的部分。 |
| RoleDisabled |指示是否应该为了访问检查的目的而忽略角色。 |
| RoleTemplateId |角色模板的标识。 |
| ServiceInfo |服务特定的预配信息，可供 MOAC 和/或其他服务实例（相同或不同的服务类型）使用。 |
| TaskSetScopeReference |标识 TaskSet 和一组与 Role 或 RoleTemplate 关联的范围。 |
| ValidationError |联合身份验证服务发布的信息，说明有关属性的非暂时性、服务特定的错误，或要解决的对象管理员操作的链接。 |
| WellKnownObject |标记目录对象，并将它指定为预先定义的集之一。 |

## <a name="update-role-definition-attributes"></a>“更新角色定义”属性
| 属性 | 说明 |
| --- | --- |
| AssignableScopes |将此 RoleDefinition 分配给安全主体时，可引用的授权范围集合。 |
| DisplayName |对象的显示名称 |
| GrantedPermissions |此 RoleDefinition 授予的权限。 |

## <a name="update-administrative-unit-attributes"></a>“更新管理单元”属性
| 属性 | 说明 |
| --- | --- |
| 说明 |此属性在更改管理单元的说明时更新。 |
| DisplayName |此属性在更改管理单元的名称时更新。 |

## <a name="update-company-attributes"></a>“更新公司”属性
| 属性 | 说明 |
| --- | --- |
| AllowedDataLocation |允许预配公司用户的位置。 |
| AuthorizedServiceInstance |可将计划部署到的服务实例的名称。 |
| DirSyncEnabled |指示同步是否从权威（客户、本地）目录发生。 |
| DirSyncStatus |指示此租户上下文中的通讯簿对象是否从权威（客户、本地）目录发生；公司对象中 DirSyncEnabled 属性的扩展。 |
| DirSyncFeatures |位标志，用于跟踪租户的已启用和禁用的目录同步功能。 |
| DirectoryFeatures |已启用/禁用目录功能。 |
| DirSyncConfiguration |包含当前租户特定的所有 DirSync 配置。 |
| DisplayName |对象的显示名称 |
| IsMnc |一个布尔值标志，如果公司已启用跨国公司功能，则设置为“true”。 |
| ObjectSettings |适用于对象范围的设置集合。 |
| PartnerCommerceUrl |合作伙伴商业站点的 URL。 |
| PartnerHelpUrl |合作伙伴帮助站点的 URL。 |
| PartnerSupportEmail |合作伙伴支持电子邮件的 URL。 |
| PartnerSupportTelephone |合作伙伴支持电话的 URL。 |
| PartnerSupportUrl |合作伙伴支持站点的 URL。 |
| StrongAuthenticationDetails |与 StrongAuthentication 相关的详细信息。 |
| StrongAuthenticationPolicy |公司的强式身份验证策略。 |
| TechnicalNotificationMail |用于通知公司相关技术问题的电子邮件地址。 |
| TelephoneNumber |符合 ITU Recommendation E.123 的电话号码。 |
| TenantType |租户的类型。 如果未指定此值，则租户是公司。 可能的值为：MicrosoftSupport (0)、SyndicatePartner (1)、BreadthPartner (2)、BreadthPartnerDelegatedAdmin (3)、ResellerPartnerDelegatedAdmin (4)、ValueAddedResellerPartnerDelegatedAdmin (5)。 |
| VerifiedDomain |绑定到公司的一组 DNS 域名。 |

## <a name="update-domain-attributes"></a>“更新域”属性
| 属性 | 说明 |
| --- | --- |
| 功能 |说明域功能（如果有）的位标志。 |
| 默认 |指示域是否为默认值；例如，当管理员在 MOAC 中创建新用户时的默认 UserPrincipalName 后缀。 |
| Initial |指示域是否为公司的初始域（如 OCP 所分配）。 初始域是 Microsoft Online 域的唯一子域，例如 contoso3.microsoftonline.com。 |
| LiveType |相应 Windows Live 命名空间（如果有）的类型。 |
| Name |终结点的标识符。 |
| PasswordNotificationWindowDays |在密码过期前的多少天通知用户。 |
| PasswordValidityPeriodDays |必须更改密码的合理天数。 |

审核记录是许多标准规定的必要控制机制。 对于根据法规要求使用 Azure Active Directory 审核报告的客户，建议提交本帮助主题的副本（连同客户导出的审核报告副本）来解释报告的详细信息。 如果审核人员想要了解 Azure 当前符合的法规，请将审核人员定向到 Microsoft Azure 信任中心的[合规性页面](https://azure.microsoft.com/support/trust-center/compliance/)。




<!--HONumber=Dec16_HO5-->


