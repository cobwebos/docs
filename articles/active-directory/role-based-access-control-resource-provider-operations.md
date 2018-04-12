---
title: Azure 资源管理器提供程序操作 | Microsoft Docs
description: 可对 Microsoft Azure 资源管理器资源提供程序使用的操作的详细信息
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/06/2018
ms.author: rolyon
ms.openlocfilehash: 0b8c8823c6d21df96dcfd926db1855169f1570e4
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/11/2018
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Azure 资源管理器资源提供程序操作

本文档列出可对每个 Microsoft Azure 资源管理器资源提供程序使用的操作。 可在自定义角色中使用这些操作，针对 Azure 中的资源提供基于角色的访问控制 (RBAC) 的精细权限。 请注意，此列表并不详尽，随着每个提供程序的更新，可能会添加或删除操作。 操作字符串遵循格式 `Microsoft.<ProviderName>/<ChildResourceType>/<action>`。 

> [!NOTE]
> 如需最新的详尽列表，请使用 `Get-AzureRmProviderOperation`（在 PowerShell 中）或 `az provider operation list`（在 Azure CLI v2 中）列出 Azure 资源提供程序的操作。

## <a name="microsoftaad"></a>Microsoft.AAD

| Operation | 说明 |
|---|---|
|/domainServices/delete|删除域服务。|
|/domainServices/read|读取域服务。|
|/domainServices/write|写入域服务|
|/locations/operationresults/read|读取异步操作的状态。|
|/Operations/read|应该向用户显示对操作的本地化友好描述。|

## <a name="microsoftaadiam"></a>microsoft.aadiam

| Operation | 说明 |
|---|---|
|/diagnosticsettings/delete|删除诊断设置|
|/diagnosticsettings/read|读取诊断设置|
|/diagnosticsettings/write|写入诊断设置|
|/diagnosticsettingscategories/read|读取诊断设置类别|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/read|获取资源的诊断设置|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新资源的诊断设置|
|/tenants/providers/Microsoft.Insights/logDefinitions/read|获取租户的可用日志|

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

| Operation | 说明 |
|---|---|
|/configuration/action|更新租户配置。|
|/configuration/read|读取租户配置。|
|/configuration/write|创建租户配置。|
|/services/action|更新租户中的服务实例。|
|/services/alerts/read|读取服务的警报。|
|/services/alerts/read|读取服务的警报。|
|/services/delete|删除租户中的服务实例。|
|/services/read|读取租户中的服务实例。|
|/services/servicemembers/action|在服务中创建服务成员实例。|
|/services/servicemembers/alerts/read|读取服务成员的警报。|
|/services/servicemembers/delete|删除服务中的服务成员实例。|
|/services/servicemembers/read|读取服务中的服务成员实例。|
|/services/write|在租户中创建服务实例。|

## <a name="microsoftadvisor"></a>Microsoft.Advisor

| Operation | 说明 |
|---|---|
|/configurations/read|获取配置|
|/configurations/write|创建/更新配置|
|/generateRecommendations/action|生成建议|
|/generateRecommendations/read|获取“生成建议”状态|
|/operations/read|获取 Microsoft 顾问的操作|
|/recommendations/read|读取建议|
|/recommendations/suppressions/delete|删除禁止显示|
|/recommendations/suppressions/read|获取禁止显示|
|/recommendations/suppressions/write|创建/更新禁止显示|
|/register/action|注册 Microsoft 顾问的订阅|
|/suppressions/delete|删除禁止显示|
|/suppressions/read|获取禁止显示|
|/suppressions/write|创建/更新禁止显示|
|/unregister/action|取消注册 Microsoft 顾问的订阅|

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

| Operation | 说明 |
|---|---|
|/alerts/read|获取输入筛选器的所有警报。|
|/alerts/resolve/action|将警报状态更改为“解决”|
|/alertsSummary/read|获取警报的摘要|
|/Operations/read|读取提供的操作|

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

| Operation | 说明 |
|---|---|
|/locations/checkNameAvailability/action|检查给定的 Analysis Server 名称是否有效且未被使用。|
|/locations/operationresults/read|检索指定操作结果的信息。|
|/locations/operationstatuses/read|检索指定操作状态的信息。|
|/operations/read|检索操作的信息|
|/register/action|注册 Analysis Services 资源提供程序。|
|/servers/delete|删除 Analysis Server。|
|/servers/listGatewayStatus/action|列出与服务器关联的网关的状态。|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|获取 Analysis Server 的诊断设置|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新 Analysis Server 的诊断设置|
|/servers/providers/Microsoft.Insights/logDefinitions/read|获取服务器的可用日志|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|获取 Analysis Server 的可用指标|
|/servers/read|检索指定 Analysis Server 的信息。|
|/servers/resume/action|恢复 Analysis Server。|
|/servers/skus/read|检索服务器的可用 SKU 信息|
|/servers/suspend/action|暂停 Analysis Server。|
|/servers/write|创建或更新指定的 Analysis Server。|
|/skus/read|检索 SKU 的信息|

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

| Operation | 说明 |
|---|---|
|/checkNameAvailability/read|检查提供的服务名称是否可用|
|/operations/read|读取适用于 Microsoft.ApiManagement 资源的所有 API 操作|
|/register/action|注册 Microsoft.ApiManagement 资源提供程序的订阅|
|/reports/read|获取按时间段、地理区域、开发者、产品、API、操作、订阅和 byRequest 聚合的报表。|
|/service/apis/delete|删除现有 API|
|/service/apis/diagnostics/delete|删除现有诊断|
|/service/apis/diagnostics/loggers/delete|删除带有诊断设置的记录器的映射|
|/service/apis/diagnostics/loggers/read|获取现有诊断记录器的列表|
|/service/apis/diagnostics/loggers/write|将记录器映射到诊断设置|
|/service/apis/diagnostics/read|获取诊断列表，或获取诊断的详细信息|
|/service/apis/diagnostics/write|添加新的诊断，或更新现有诊断详细信息|
|/service/apis/operations/delete|删除现有的 API 操作|
|/service/apis/operations/policies/delete|从 API 操作策略中删除策略配置|
|/service/apis/operations/policies/read|获取 API 操作的策略，或获取 API 操作的策略配置详细信息|
|/service/apis/operations/policies/write|设置 API 操作的策略配置详细信息|
|/service/apis/operations/policy/delete|从操作中删除策略配置|
|/service/apis/operations/policy/read|获取操作的策略配置详细信息|
|/service/apis/operations/policy/write|设置操作的策略配置详细信息|
|/service/apis/operations/read|获取现有 API 操作的列表，或获取 API 操作的详细信息|
|/service/apis/operations/tags/delete|删除现有标记与现有操作之间的关联|
|/service/apis/operations/tags/read|获取与操作关联的标记，或获取标记详细信息|
|/service/apis/operations/tags/write|将现有标记与现有操作相关联|
|/service/apis/operations/write|创建新的 API 操作，或更新现有的 API 操作|
|/service/apis/operationsByTags/read|获取操作/标记关联的列表|
|/service/apis/policies/delete|从 API 策略中删除策略配置|
|/service/apis/policies/read|获取 API 的策略，或获取 API 的策略配置详细信息|
|/service/apis/policies/write|设置 API 的策略配置详细信息|
|/service/apis/policy/delete|从 API 中删除策略配置|
|/service/apis/policy/read|获取 API 的策略配置详细信息|
|/service/apis/policy/write|设置 API 的策略配置详细信息|
|/service/apis/products/read|获取 API 所属的全部产品|
|/service/apis/read|获取所有已注册 API 的列表，或获取 API 的详细信息|
|/service/apis/releases/delete|删除 API 的所有发布，或删除 API 发布|
|/service/apis/releases/read|获取 API 的发布，或获取 API 发布的详细信息|
|/service/apis/releases/write|创建新的 API 发布，或更新现有 API 发布|
|/service/apis/revisions/delete|删除 API 的所有修订|
|/service/apis/revisions/read|获取属于 API 的修订|
|/service/apis/schemas/delete|删除现有架构|
|/service/apis/schemas/document/read|获取描述架构的文档|
|/service/apis/schemas/document/write|更新描述架构的文档|
|/service/apis/schemas/read|获取给定 API 的所有架构，或获取 API 使用的架构|
|/service/apis/schemas/write|设置 API 使用的架构|
|/service/apis/tagDescriptions/delete|从 API 删除标记说明|
|/service/apis/tagDescriptions/read|在 API 范围内获取标记说明，或在 API 范围内获取标记说明|
|/service/apis/tagDescriptions/write|在 API 范围内创建/更改标记说明|
|/service/apis/tags/delete|删除现有的 API/标记关联|
|/service/apis/tags/read|获取 API 的所有 API/标记关联，或获取 API/标记关联的详细信息|
|/service/apis/tags/write|添加新的 API/标记关联|
|/service/apis/write|创建新 API，或更新现有 API 详细信息|
|/service/apisByTags/read|获取 API/标记关联的列表|
|/service/api-version-sets/delete|删除现有 VersionSet|
|/service/api-version-sets/read|获取版本组实体的列表，或获取 VersionSet 的详细信息|
|/service/api-version-sets/versions/read|获取版本实体的列表|
|/service/api-version-sets/write|创建新 VersionSet 或更新现有 VersionSet 详细信息|
|/service/applynetworkconfigurationupdates/action|更新虚拟网络中运行的 Microsoft.ApiManagement 资源，以提取更新的网络设置。|
|/service/authorizationServers/delete|删除现有的授权服务器|
|/service/authorizationServers/read|获取授权服务器列表，或获取授权服务器的详细信息|
|/service/authorizationServers/write|创建新的授权服务器，或更新现有授权服务器的详细信息|
|/service/backends/delete|删除现有后端|
|/service/backends/read|获取后端列表，或获取后端详细信息|
|/service/backends/reconnect/action|创建重新连接请求|
|/service/backends/write|添加新后端，或更新现有后端详细信息|
|/service/backup/action|将 API 管理服务备份到用户提供的存储帐户中的指定容器|
|/service/certificates/delete|删除现有证书|
|/service/certificates/read|获取证书列表，或获取证书的详细信息|
|/service/certificates/write|添加新证书|
|/service/delete|删除 API 管理服务实例|
|/service/diagnostics/delete|删除现有诊断|
|/service/diagnostics/loggers/delete|删除带有诊断设置的记录器的映射|
|/service/diagnostics/loggers/read|获取现有诊断记录器的列表|
|/service/diagnostics/loggers/write|将记录器映射到诊断设置|
|/service/diagnostics/read|获取诊断列表，或获取诊断的详细信息|
|/service/diagnostics/write|添加新的诊断，或更新现有诊断详细信息|
|/service/getssotoken/action|获取可用于以管理员身份登录到 API 管理服务旧版门户的 SSO 令牌|
|/service/groups/delete|删除现有组|
|/service/groups/read|获取组列表，或获取组的详细信息|
|/service/groups/users/delete|从现有组中删除现有用户|
|/service/groups/users/read|获取组用户列表|
|/service/groups/users/write|将现有用户添加到现有组|
|/service/groups/write|创建新组，或更新现有组详细信息|
|/service/identityProviders/delete|删除现有的标识提供者|
|/service/identityProviders/read|获取标识提供者的列表，或获取标识提供者的详细信息|
|/service/identityProviders/write|创建新的标识提供者，或更新现有标识提供者的详细信息|
|/service/locations/networkstatus/read|获取位置中服务所依赖的资源的网络访问状态。|
|/service/loggers/delete|删除现有的记录器|
|/service/loggers/read|获取记录器列表，或获取记录器详细信息|
|/service/loggers/write|添加新记录器，或更新现有的记录器详细信息|
|/service/managedeployments/action|更改 API 管理服务的 SKU/单位，以及添加/删除其区域部署|
|/service/networkstatus/read|获取服务所依赖的资源的网络访问状态。|
|/service/notifications/action|向指定用户发送通知|
|/service/notifications/read|获取所有 API 管理发布者通知，或获取 API 管理发布者通知详细信息|
|/service/notifications/recipientEmails/delete|删除与通知关联的现有电子邮件|
|/service/notifications/recipientEmails/read|获取与 API 管理发布者通知关联的电子邮件收件人|
|/service/notifications/recipientEmails/write|创建通知的新电子邮件收件人|
|/service/notifications/recipientUsers/delete|删除与通知收件人关联的用户|
|/service/notifications/recipientUsers/read|获取与通知关联的收件人用户|
|/service/notifications/recipientUsers/write|将用户添加到通知收件人|
|/service/notifications/write|创建或更新 API 管理发布者通知|
|/service/openidConnectProviders/delete|删除现有的 OpenID Connect 提供程序|
|/service/openidConnectProviders/read|获取 OpenID Connect 提供程序的列表，或获取 OpenID Connect 提供程序的详细信息|
|/service/openidConnectProviders/write|创建新的 OpenID Connect 提供程序，或更新现有 OpenID Connect 提供程序的详细信息|
|/service/operationresults/read|获取长时间运行的操作的当前状态|
|/service/policies/delete|从租户策略中删除策略配置|
|/service/policies/read|获取租户的策略，或获取租户的策略配置详细信息|
|/service/policies/write|设置租户的策略配置详细信息|
|/service/policySnippets/read|获取所有策略片段|
|/service/portalsettings/read|获取门户的注册设置，或获取门户的登录设置，或获取门户的委派设置|
|/service/portalsettings/write|更新注册设置，或更新注册设置，或更新登录设置，或更新登录设置，或更新委派设置，或更新委派设置|
|/service/products/apis/delete|从现有产品中删除现有 API|
|/service/products/apis/read|获取已添加到现有产品的 API 列表|
|/service/products/apis/write|将现有 API 添加到现有产品|
|/service/products/delete|删除现有产品|
|/service/products/groups/delete|删除现有开发人员组与现有产品之间的关联|
|/service/products/groups/read|获取与产品关联的开发人员组的列表|
|/service/products/groups/write|将现有开发人员组与现有产品相关联|
|/service/products/policies/delete|从产品策略中删除策略配置|
|/service/products/policies/read|获取产品的策略，或获取产品的策略配置详细信息|
|/service/products/policies/write|设置产品的策略配置详细信息|
|/service/products/policy/delete|从现有产品中删除策略配置|
|/service/products/policy/read|获取现有产品的策略配置|
|/service/products/policy/write|设置现有产品的策略配置|
|/service/products/read|获取产品列表，或获取产品的详细信息|
|/service/products/subscriptions/read|获取产品订阅的列表|
|/service/products/tags/delete|删除现有标记与现有产品之间的关联|
|/service/products/tags/read|获取与产品关联的标记，或获取标记详细信息|
|/service/products/tags/write|将现有标记与现有产品相关联|
|/service/products/write|创建新产品，或更新现有产品详细信息|
|/service/properties/delete|删除现有属性|
|/service/properties/read|获取所有属性的列表，或获取指定属性的详细信息|
|/service/properties/write|创建新属性，或更新指定属性的值|
|/service/providers/Microsoft.Insights/diagnosticSettings/read|获取 API 管理服务的诊断设置|
|/service/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新 API 管理服务的诊断设置|
|/service/providers/Microsoft.Insights/logDefinitions/read|获取 API 管理服务的可用日志|
|/service/providers/Microsoft.Insights/metricDefinitions/read|获取 API 管理服务的可用指标|
|/service/quotas/periods/read|获取某时间段的配额计数器值|
|/service/quotas/periods/write|设置配额计数器当前值|
|/service/quotas/read|获取配额值|
|/service/quotas/write|设置配额计数器当前值|
|/service/read|读取 API 管理服务实例的元数据|
|/service/reports/read|获取按时间段聚合的报表，或获取按地理区域聚合的报表，或获取按开发者聚合的报表。 或获取按产品聚合的报表。 或获取按 API 聚合的报表，或获取按操作聚合的报表，或获取按订阅聚合的报表。 或获取报告数据的请求|
|/service/restore/action|从用户提供的存储帐户中的指定容器还原 API 管理服务|
|/service/subscriptions/delete|删除订阅。 此操作可用于删除订阅|
|/service/subscriptions/read|获取产品订阅的列表，或获取产品订阅的详细信息|
|/service/subscriptions/regeneratePrimaryKey/action|再生成订阅主密钥|
|/service/subscriptions/regenerateSecondaryKey/action|再生成订阅辅助密钥|
|/service/subscriptions/write|让现有用户订阅现有产品，或更新现有订阅的详细信息。 此操作可用于续订订阅|
|/service/tagResources/read|获取包含关联资源的标记列表|
|/service/tags/delete|删除现有标记|
|/service/tags/read|获取标记列表，或获取标记的详细信息|
|/service/tags/write|添加新的标记或更新现有标记详细信息|
|/service/templates/delete|重置默认 API 管理电子邮件模板|
|/service/templates/read|获取所有电子邮件模板，或获取 API 管理电子邮件模板详细信息|
|/service/templates/write|创建或更新 API 管理电子邮件模板或更新 API 管理电子邮件模板|
|/service/tenant/delete|删除租户的策略配置|
|/service/tenant/deploy/action|运行部署任务，以便将指定的 git 分支中的更改应用到数据库中的配置。|
|/service/tenant/operationResults/read|获取操作结果的列表，或获取特定操作的结果|
|/service/tenant/read|获取租户的策略配置，或获取租户访问详细信息|
|/service/tenant/regeneratePrimaryKey/action|再生成主访问密钥|
|/service/tenant/regenerateSecondaryKey/action|再生成辅助访问密钥|
|/service/tenant/save/action|创建包含配置快照的、目标为储存库中的指定的分支的提交内容|
|/service/tenant/syncState/read|获取上次 git 同步的状态|
|/service/tenant/validate/action|验证指定的 git 分支中的更改|
|/service/tenant/write|设置租户的策略配置或更新租户访问详细信息|
|/service/updatecertificate/action|上传 API 管理服务的 SSL 证书|
|/service/updatehostname/action|设置、更新或删除 API 管理服务的自定义域名|
|/service/users/action|注册新用户|
|/service/users/applications/attachments/delete|删除附件|
|/service/users/applications/attachments/read|获取应用程序附件，或获取附件|
|/service/users/applications/attachments/write|将附件添加到应用程序|
|/service/users/applications/delete|删除现有应用程序|
|/service/users/applications/read|获取所有用户应用程序的列表，或获取 API 管理应用程序详细信息|
|/service/users/applications/write|向 API 管理注册应用程序或更新应用程序详细信息|
|/service/users/delete|删除用户帐户|
|/service/users/generateSsoUrl/action|生成 SSO URL。 可用于访问管理门户的 URL|
|/service/users/groups/read|获取用户组的列表|
|/service/users/keys/read|获取用户密钥的列表|
|/service/users/read|获取已注册用户的列表，或获取用户的帐户详细信息|
|/service/users/subscriptions/read|获取用户订阅的列表|
|/service/users/token/action|获取用户的令牌访问令牌|
|/service/users/write|注册新用户，或更新现有用户的帐户详细信息|
|/service/write|创建 API 管理服务的新实例|
|/unregister/action|取消注册 Microsoft.ApiManagement 资源提供程序的订阅|

## <a name="microsoftauthorization"></a>Microsoft.Authorization

| Operation | 说明 |
|---|---|
|/checkAccess/action|查看调用方是否有权执行特定操作|
|/classicAdministrators/delete|从订阅中删除管理员。|
|/classicAdministrators/read|读取订阅的管理员。|
|/classicAdministrators/write|在订阅中添加或修改管理员。|
|/elevateAccess/action|向调用方授予租户范围的“用户访问管理员”访问权限|
|/locks/delete|删除指定范围的锁。|
|/locks/read|获取指定范围的锁。|
|/locks/write|添加指定范围的锁。|
|/permissions/read|列出调用方在给定范围拥有的所有权限。|
|/policyAssignments/delete|删除指定范围的策略分配。|
|/policyAssignments/read|获取有关策略分配的信息。|
|/policyAssignments/write|创建指定范围的策略分配。|
|/policyDefinitions/delete|删除策略定义。|
|/policyDefinitions/read|获取有关策略定义的信息。|
|/policyDefinitions/write|创建自定义策略定义。|
|/policySetDefinitions/delete|删除策略集定义。|
|/policySetDefinitions/read|获取有关策略集定义的信息。|
|/policySetDefinitions/write|创建自定义策略集定义。|
|/providerOperations/read|获取可在角色定义中使用的所有资源提供程序的操作。|
|/roleAssignments/delete|删除指定范围的角色分配。|
|/roleAssignments/read|获取有关角色分配的信息。|
|/roleAssignments/write|创建指定范围的角色分配。|
|/roleDefinitions/delete|删除指定的自定义角色定义。|
|/roleDefinitions/read|获取有关角色定义的信息。|
|/roleDefinitions/write|使用指定的权限和可分配的范围创建或更新自定义角色定义。|

## <a name="microsoftautomation"></a>Microsoft.Automation

| Operation | 说明 |
|---|---|
|/automationAccounts/agentRegistrationInformation/read|读取 Azure Automation DSC 的注册信息|
|/automationAccounts/agentRegistrationInformation/regenerateKey/action|写入重新生成 Azure Automation DSC 密钥的请求|
|/automationAccounts/certificates/delete|删除 Azure 自动化证书资产|
|/automationAccounts/certificates/read|获取 Azure 自动化证书资产|
|/automationAccounts/certificates/write|创建或更新 Azure 自动化证书资产|
|/automationAccounts/compilationjobs/read|读取 Azure Automation DSC 的编译|
|/automationAccounts/compilationjobs/write|写入 Azure Automation DSC 的编译|
|/automationAccounts/configurations/delete|删除 Azure Automation DSC 的内容|
|/automationAccounts/configurations/getCount/action|读取 Azure Automation DSC 的内容计数|
|/automationAccounts/configurations/read|获取 Azure Automation DSC 的内容|
|/automationAccounts/configurations/write|写入 Azure Automation DSC 的内容|
|/automationAccounts/connections/delete|删除 Azure 自动化连接资产|
|/automationAccounts/connections/read|获取 Azure 自动化连接资产|
|/automationAccounts/connections/write|创建或更新 Azure 自动化连接资产|
|/automationAccounts/connectionTypes/delete|删除 Azure 自动化连接类型资产|
|/automationAccounts/connectionTypes/read|获取 Azure 自动化连接类型资产|
|/automationAccounts/connectionTypes/write|创建 Azure 自动化连接类型资产|
|/automationAccounts/credentials/delete|删除 Azure 自动化凭据资产|
|/automationAccounts/credentials/read|获取 Azure 自动化凭据资产|
|/automationAccounts/credentials/write|创建或更新 Azure 自动化凭据资产|
|/automationAccounts/delete|删除 Azure 自动化帐户|
|/automationAccounts/diagnosticSettings/read|获取资源的诊断设置|
|/automationAccounts/diagnosticSettings/write|设置资源的诊断设置|
|/automationAccounts/hybridRunbookWorkerGroups/delete|删除混合 Runbook 辅助角色资源|
|/automationAccounts/hybridRunbookWorkerGroups/read|读取混合 Runbook 辅助角色资源|
|/automationAccounts/jobs/output/action|获取作业的输出|
|/automationAccounts/jobs/output/action|获取作业的输出|
|/automationAccounts/jobs/read|获取 Azure 自动化作业|
|/automationAccounts/jobs/read|获取 Azure 自动化作业|
|/automationAccounts/jobs/resume/action|恢复 Azure 自动化作业|
|/automationAccounts/jobs/resume/action|恢复 Azure 自动化作业|
|/automationAccounts/jobs/runbookContent/action|执行作业时获取 Azure 自动化 Runbook 的内容|
|/automationAccounts/jobs/runbookContent/action|执行作业时获取 Azure 自动化 Runbook 的内容|
|/automationAccounts/jobs/stop/action|停止 Azure 自动化作业|
|/automationAccounts/jobs/stop/action|停止 Azure 自动化作业|
|/automationAccounts/jobs/streams/read|获取 Azure 自动化作业流|
|/automationAccounts/jobs/streams/read|获取 Azure 自动化作业流|
|/automationAccounts/jobs/suspend/action|暂停 Azure 自动化作业|
|/automationAccounts/jobs/suspend/action|暂停 Azure 自动化作业|
|/automationAccounts/jobs/write|创建 Azure 自动化作业|
|/automationAccounts/jobs/write|创建 Azure 自动化作业|
|/automationAccounts/jobSchedules/delete|删除 Azure 自动化作业计划|
|/automationAccounts/jobSchedules/read|获取 Azure 自动化作业计划|
|/automationAccounts/jobSchedules/write|创建 Azure 自动化作业计划|
|/automationAccounts/linkedWorkspace/read|获取链接到自动化帐户的工作区|
|/automationAccounts/logDefinitions/read|获取自动化帐户的可用日志|
|/automationAccounts/modules/activities/read|获取 Azure 自动化活动|
|/automationAccounts/modules/delete|删除 Azure 自动化模块|
|/automationAccounts/modules/read|获取 Azure 自动化模块|
|/automationAccounts/modules/write|创建或更新 Azure 自动化模块|
|/automationAccounts/nodeConfigurations/delete|删除 Azure Automation DSC 的节点配置|
|/automationAccounts/nodeConfigurations/read|读取 Azure Automation DSC 的节点配置|
|/automationAccounts/nodeConfigurations/readContent/action|读取 Azure Automation DSC 的节点配置内容|
|/automationAccounts/nodeConfigurations/write|写入 Azure Automation DSC 的节点配置|
|/automationAccounts/nodes/delete|删除 Azure Automation DSC 节点|
|/automationAccounts/nodes/read|读取 Azure Automation DSC 节点|
|/automationAccounts/nodes/reports/read|读取 Azure Automation DSC 报表内容|
|/automationAccounts/nodes/reports/read|读取 Azure Automation DSC 报表|
|/automationAccounts/objectDataTypes/fields/read|获取 Azure 自动化 TypeField|
|/automationAccounts/providers/Microsoft.Insights/metricDefinitions/read|获取自动化指标定义|
|/automationAccounts/read|获取 Azure 自动化帐户。|
|/automationAccounts/runbooks/delete|删除 Azure 自动化 Runbook|
|/automationAccounts/runbooks/draft/publish/action|发布 Azure 自动化 Runbook 草稿|
|/automationAccounts/runbooks/draft/read|获取 Azure 自动化 Runbook 草稿|
|/automationAccounts/runbooks/draft/readContent/action|获取 Azure 自动化 Runbook 草稿的内容|
|/automationAccounts/runbooks/draft/testJob/read|获取 Azure 自动化 Runbook 草稿测试作业|
|/automationAccounts/runbooks/draft/testJob/resume/action|恢复 Azure 自动化 Runbook 草稿测试作业|
|/automationAccounts/runbooks/draft/testJob/stop/action|停止 Azure 自动化 Runbook 草稿测试作业|
|/automationAccounts/runbooks/draft/testJob/suspend/action|暂停 Azure 自动化 Runbook 草稿测试作业|
|/automationAccounts/runbooks/draft/testJob/write|创建 Azure 自动化 Runbook 草稿测试作业|
|/automationAccounts/runbooks/draft/undoEdit/action|撤消对 Azure 自动化 Runbook 草稿的编辑|
|/automationAccounts/runbooks/draft/writeContent/action|创建 Azure 自动化 Runbook 草稿的内容|
|/automationAccounts/runbooks/read|获取 Azure 自动化 Runbook|
|/automationAccounts/runbooks/readContent/action|获取 Azure 自动化 Runbook 的内容|
|/automationAccounts/runbooks/write|创建或更新 Azure 自动化 Runbook|
|/automationAccounts/schedules/delete|删除 Azure 自动化计划资产|
|/automationAccounts/schedules/read|获取 Azure 自动化计划资产|
|/automationAccounts/schedules/write|创建或更新 Azure 自动化计划资产|
|/automationAccounts/statistics/read|获取 Azure 自动化统计信息|
|/automationAccounts/usages/read|获取 Azure 自动化使用情况|
|/automationAccounts/variables/delete|删除 Azure 自动化变量资产|
|/automationAccounts/variables/read|读取 Azure 自动化变量资产|
|/automationAccounts/variables/write|创建或更新 Azure 自动化变量资产|
|/automationAccounts/watchers/streams/read|获取 Azure 自动化观察程序作业流|
|/automationAccounts/webhooks/delete|删除 Azure 自动化 Webhook |
|/automationAccounts/webhooks/generateUri/action|生成 Azure 自动化 Webhook 的 URI|
|/automationAccounts/webhooks/read|读取 Azure 自动化 Webhook|
|/automationAccounts/webhooks/write|创建或更新 Azure 自动化 Webhook|
|/automationAccounts/write|创建或更新 Azure 自动化帐户|
|/automationAccounts/write|创建或更新 Azure 自动化帐户|

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

| Operation | 说明 |
|---|---|
|/b2cDirectories/delete|删除 B2C 目录资源|
|/b2cDirectories/read|查看 B2C 目录资源|
|/b2cDirectories/write|创建或更新 B2C 目录资源|
|/operations/read|读取适用于 Microsoft.AzureActiveDirectory 资源提供程序的所有 API 操作|
|/register/action|注册 Microsoft.AzureActiveDirectory 资源提供程序的订阅|

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

| Operation | 说明 |
|---|---|
|/Operations/read|获取资源提供程序操作的属性|
|/register/action|使用 Microsoft.AzureStack 资源提供程序注册订阅|
|/registrations/customerSubscriptions/delete|删除 Azure Stack 客户订阅|
|/registrations/customerSubscriptions/read|获取 Azure Stack 客户订阅的属性|
|/registrations/customerSubscriptions/write|创建或更新 Azure Stack 客户订阅|
|/registrations/delete|删除 Azure Stack 注册|
|/registrations/getActivationKey/action|获取最新的 Azure Stack 激活密钥|
|/registrations/products/listDetails/action|检索 Azure Stack Marketplace 产品的扩展详细信息|
|/registrations/products/read|获取 Azure Stack Marketplace 产品的属性|
|/registrations/read|获取 Azure Stack 注册的属性|
|/registrations/write|创建或更新 Azure Stack 注册|

## <a name="microsoftbatch"></a>Microsoft.Batch

| Operation | 说明 |
|---|---|
|/batchAccounts/applications/delete|删除应用程序|
|/batchAccounts/applications/read|列出应用程序，或获取应用程序的属性|
|/batchAccounts/applications/versions/activate/action|激活应用程序包|
|/batchAccounts/applications/versions/delete|删除应用程序包|
|/batchAccounts/applications/versions/read|获取应用程序包的属性|
|/batchAccounts/applications/versions/write|创建新的应用程序包，或更新现有的应用程序包|
|/batchAccounts/applications/write|创建新的应用程序，或更新现有的应用程序|
|/batchAccounts/certificateOperationResults/read|获取 Batch 帐户上长时间运行的证书操作的结果|
|/batchAccounts/certificates/cancelDelete/action|取消对 Batch 帐户上证书删除失败的操作|
|/batchAccounts/certificates/delete|从 Batch 帐户中删除证书|
|/batchAccounts/certificates/read|列出 Batch 帐户上的证书或获取证书的属性|
|/batchAccounts/certificates/write|在 Batch 帐户上创建新的证书或更新现有证书|
|/batchAccounts/delete|删除批处理帐户|
|/batchAccounts/listkeys/action|列出批处理帐户的访问密钥|
|/batchAccounts/operationResults/read|获取长时间运行的 Batch 帐户操作的结果|
|/batchAccounts/poolOperationResults/read|获取 Batch 帐户上长时间运行的池操作的结果|
|/batchAccounts/pools/delete|从 Batch 帐户中删除池|
|/batchAccounts/pools/disableAutoscale/action|禁用 Batch 帐户池的自动缩放|
|/batchAccounts/pools/read|列出 Batch 帐户上的池，或获取池的属性|
|/batchAccounts/pools/stopResize/action|停止正在对 Batch 帐户池进行的重设大小操作|
|/batchAccounts/pools/upgradeOs/action|升级 Batch 帐户池的操作系统|
|/batchAccounts/pools/write|在 Batch 帐户上创建新池，或更新现有池|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/read|获取资源的诊断设置|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新资源的诊断设置|
|/batchAccounts/providers/Microsoft.Insights/logDefinitions/read|获取 Batch 服务的可用日志|
|/batchAccounts/providers/Microsoft.Insights/metricDefinitions/read|获取 Batch 服务的可用指标|
|/batchAccounts/read|列出批处理帐户，或获取批处理帐户的属性|
|/batchAccounts/regeneratekeys/action|再生成批处理帐户的访问密钥|
|/batchAccounts/syncAutoStorageKeys/action|同步针对批处理帐户配置的自动存储帐户的访问密钥|
|/batchAccounts/write|创建新的批处理帐户，或更新现有的批处理帐户|
|/locations/checkNameAvailability/action|检查帐户名称是否有效且未被使用。|
|/locations/quotas/read|获取指定 Azure 区域中指定订阅的批处理配额|
|/register/action|注册批处理资源提供程序的订阅，并启用批处理帐户的创建|
|/unregister/action|取消注册 Batch 资源提供程序的订阅，阻止创建 Batch 帐户|

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

| Operation | 说明 |
|---|---|
|/clusters/delete|删除 Batch AI 群集|
|/clusters/read|列出 Batch AI 群集，或获取 Batch AI 群集的属性|
|/clusters/remoteLoginInformation/action|列出 Batch AI 群集的远程登录信息|
|/clusters/write|创建新的 Batch AI 群集，或更新现有的 Batch AI 群集|
|/fileservers/delete|删除 Batch AI 文件服务器|
|/fileservers/read|列出 Batch AI 文件服务器，或获取 Batch AI 文件服务器的属性|
|/fileservers/resume/action|恢复 Batch AI 文件服务器|
|/fileservers/suspend/action|暂停 Batch AI 文件服务器|
|/fileservers/write|创建新的 Batch AI 文件服务器，或更新现有的 Batch AI 文件服务器|
|/jobs/delete|删除 Batch AI 作业|
|/jobs/read|列出 Batch AI 作业，或获取 Batch AI 作业的属性|
|/jobs/remoteLoginInformation/action|列出 Batch AI 作业的远程登录信息|
|/jobs/terminate/action|终止 Batch AI 作业|
|/jobs/write|创建新的 Batch AI 作业，或更新现有的 Batch AI 作业|
|/register/action|注册 Batch AI 资源提供程序的订阅，并启用 Batch AI 资源的创建|

## <a name="microsoftbilling"></a>Microsoft.Billing

| Operation | 说明 |
|---|---|
|/billingPeriods/read|列出可用的计费周期|
|/invoices/read|列出提供的发票|

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

| Operation | 说明 |
|---|---|
|/mapApis/Delete|删除操作|
|/mapApis/listSecrets/action|列出机密|
|/mapApis/listSingleSignOnToken/action|读取资源的单一登录授权令牌|
|/mapApis/Read|读取操作|
|/mapApis/regenerateKey/action|再生成密钥|
|/mapApis/Write|写入操作|
|/Operations/read|操作说明。|

## <a name="microsoftcache"></a>Microsoft.Cache

| Operation | 说明 |
|---|---|
|/checknameavailability/action|检查名称是否可用于新的 Redis 缓存|
|/operations/read|列出“Microsoft.Cache”提供程序支持的操作。|
|/redis/delete|删除整个 Redis 缓存|
|/redis/export/action|将 Redis 数据以指定的格式导出到带前缀的存储 Blob|
|/redis/firewallRules/delete|删除 Redis 缓存的 IP 防火墙规则|
|/redis/firewallRules/read|获取 Redis 缓存的 IP 防火墙规则|
|/redis/firewallRules/write|编辑 Redis 缓存的 IP 防火墙规则|
|/redis/forceReboot/action|强制重新启动缓存实例（可能会发生数据丢失）。|
|/redis/import/action|将多个 Blob 中指定格式的数据导入 Redis|
|/redis/linkedservers/delete|从 Redis 缓存中删除链接服务器|
|/redis/linkedservers/read|获取与 Redis 缓存关联的链接服务器。|
|/redis/linkedservers/write|将链接服务器添加到 Redis 缓存|
|/redis/listKeys/action|在管理门户中查看 Redis 缓存访问密钥的值|
|/redis/listUpgradeNotifications/read|列出缓存租户的最新升级通知。|
|/redis/locations/operationresults/read|获取之前将“Location”标头返回给客户端的长时间运行的操作结果|
|/redis/metricDefinitions/read|获取 Redis 缓存的可用指标|
|/redis/patchSchedules/delete|删除 Redis 缓存的修补计划|
|/redis/patchSchedules/read|获取 Redis 缓存的修补计划|
|/redis/patchSchedules/write|修改 Redis 缓存的修补计划|
|/redis/read|在管理门户中查看 Redis 缓存的设置和配置|
|/redis/regenerateKey/action|在管理门户中更改 Redis 缓存访问密钥的值|
|/redis/start/action|启动缓存实例。|
|/redis/stop/action|停止缓存实例。|
|/redis/write|在管理门户中修改 Redis 缓存的设置和配置|
|/register/action|将“Microsoft.Cache”资源提供程序注册到订阅|
|/unregister/action|从订阅中取消注册“Microsoft.Cache”资源提供程序|

## <a name="microsoftcapacity"></a>Microsoft.Capacity

| Operation | 说明 |
|---|---|
|/register/action|注册容量资源提供程序，并启用容量资源的创建。|
|/reservationorders/action|更新任何预订|
|/reservationorders/delete|删除任何预订|
|/reservationorders/read|读取所有预订|
|/reservationorders/reservations/action|更新任何预订|
|/reservationorders/reservations/delete|删除任何预订|
|/reservationorders/reservations/read|读取所有预订|
|/reservationorders/reservations/revisions/read|读取所有预订|
|/reservationorders/reservations/write|创建任何预订|
|/reservationorders/write|创建任何预订|

## <a name="microsoftcdn"></a>Microsoft.Cdn

| Operation | 说明 |
|---|---|
|/CheckNameAvailability/action||
|/CheckResourceUsage/action||
|/edgenodes/delete||
|/edgenodes/read||
|/edgenodes/write||
|/operationresults/delete||
|/operationresults/profileresults/CheckResourceUsage/action||
|/operationresults/profileresults/delete||
|/operationresults/profileresults/endpointresults/CheckResourceUsage/action||
|/operationresults/profileresults/endpointresults/customdomainresults/delete||
|/operationresults/profileresults/endpointresults/customdomainresults/ DisableCustomHttps/action||
|/operationresults/profileresults/endpointresults/customdomainresults/ EnableCustomHttps/action||
|/operationresults/profileresults/endpointresults/customdomainresults/read||
|/operationresults/profileresults/endpointresults/customdomainresults/write||
|/operationresults/profileresults/endpointresults/delete||
|/operationresults/profileresults/endpointresults/Load/action||
|/operationresults/profileresults/endpointresults/originresults/delete||
|/operationresults/profileresults/endpointresults/originresults/read||
|/operationresults/profileresults/endpointresults/originresults/write||
|/operationresults/profileresults/endpointresults/Purge/action||
|/operationresults/profileresults/endpointresults/read||
|/operationresults/profileresults/endpointresults/Start/action||
|/operationresults/profileresults/endpointresults/Stop/action||
|/operationresults/profileresults/endpointresults/ValidateCustomDomain/action||
|/operationresults/profileresults/endpointresults/write||
|/operationresults/profileresults/GenerateSsoUri/action||
|/operationresults/profileresults/GetSupportedOptimizationTypes/action||
|/operationresults/profileresults/read||
|/operationresults/profileresults/write||
|/operationresults/read||
|/operationresults/write||
|/operations/read||
|/profiles/CheckResourceUsage/action||
|/profiles/delete||
|/profiles/endpoints/CheckResourceUsage/action||
|/profiles/endpoints/customdomains/delete||
|/profiles/endpoints/customdomains/DisableCustomHttps/action||
|/profiles/endpoints/customdomains/EnableCustomHttps/action||
|/profiles/endpoints/customdomains/read||
|/profiles/endpoints/customdomains/write||
|/profiles/endpoints/delete||
|/profiles/endpoints/Load/action||
|/profiles/endpoints/origins/delete||
|/profiles/endpoints/origins/read||
|/profiles/endpoints/origins/write||
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/read|获取资源的诊断设置|
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新资源的诊断设置|
|/profiles/endpoints/providers/Microsoft.Insights/logDefinitions/read|获取 Microsoft.Cdn 的可用日志|
|/profiles/endpoints/Purge/action||
|/profiles/endpoints/read||
|/profiles/endpoints/Start/action||
|/profiles/endpoints/Stop/action||
|/profiles/endpoints/ValidateCustomDomain/action||
|/profiles/endpoints/write||
|/profiles/GenerateSsoUri/action||
|/profiles/GetSupportedOptimizationTypes/action||
|/profiles/read||
|/profiles/write||
|/register/action|注册 CDN 资源提供程序的订阅，并启用 CDN 配置文件的创建。|
|/ValidateProbe/action||

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

| Operation | 说明 |
|---|---|
|/certificateOrders/certificates/Delete|删除现有证书|
|/certificateOrders/certificates/Read|获取证书列表|
|/certificateOrders/certificates/Write|添加新的证书，或更新现有的证书|
|/certificateOrders/Delete|删除现有的 AppServiceCertificate|
|/certificateOrders/operations/Read|列出应用服务证书注册的所有操作|
|/certificateOrders/Read|获取 CertificateOrders 列表|
|/certificateOrders/reissue/Action|重新颁发现有的 certificateorder|
|/certificateOrders/renew/Action|续订现有的 certificateorder|
|/certificateOrders/resendEmail/Action|重新发送证书电子邮件|
|/certificateOrders/resendRequestEmails/Action|将请求电子邮件重新发送到另一个电子邮件地址|
|/certificateOrders/resendRequestEmails/Action|检索颁发的应用服务证书的站点封印|
|/certificateOrders/retrieveCertificateActions/Action|检索证书操作的列表|
|/certificateOrders/retrieveEmailHistory/Action|检索证书电子邮件历史记录|
|/certificateOrders/verifyDomainOwnership/Action|验证域所有权|
|/certificateOrders/Write|添加新的或更新现有的 certificateOrder|
|/provisionGlobalAppServicePrincipalInUserTenant/Action|为服务应用主体预配服务主体|
|/register/action|注册订阅的 Microsoft 证书资源提供程序|
|/validateCertificateRegistrationInformation/Action|验证证书购买对象但不提交该对象|

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

| Operation | 说明 |
|---|---|
|/capabilities/read|显示功能|
|/checkDomainNameAvailability/action|检查给定域名的可用性。|
|/domainNames/active/write|设置活动域名。|
|/domainNames/availabilitySets/read|显示资源的可用性集。|
|/domainNames/capabilities/read|显示域名功能|
|/domainNames/delete|删除资源的域名。|
|/domainNames/extensions/delete|删除域名扩展。|
|/domainNames/extensions/operationStatuses/read|读取域名扩展的操作状态。|
|/domainNames/extensions/read|返回域名扩展。|
|/domainNames/extensions/write|添加域名扩展。|
|/domainNames/internalLoadBalancers/delete|删除新的内部负载均衡。|
|/domainNames/internalLoadBalancers/operationStatuses/read|读取域名内部负载均衡器的操作状态。|
|/domainNames/internalLoadBalancers/read|获取内部负载均衡器。|
|/domainNames/internalLoadBalancers/write|创建新的内部负载均衡。|
|/domainNames/loadBalancedEndpointSets/operationStatuses/read|读取域名负载均衡的终结点集的操作状态。|
|/domainNames/loadBalancedEndpointSets/read|显示负载均衡的终结点集|
|/domainNames/read|返回资源的域名。|
|/domainNames/serviceCertificates/delete|删除使用的服务证书。|
|/domainNames/serviceCertificates/operationStatuses/read|读取域名服务证书的操作状态。|
|/domainNames/serviceCertificates/read|返回使用的服务证书。|
|/domainNames/serviceCertificates/write|添加或修改使用的服务证书。|
|/domainNames/slots/delete|删除给定的部署槽。|
|/domainNames/slots/operationStatuses/read|读取域名槽的操作状态。|
|/domainNames/slots/read|显示部署槽。|
|/domainNames/slots/roles/extensionReferences/delete|删除部署槽角色的扩展引用。|
|/domainNames/slots/roles/extensionReferences/operationStatuses/read|读取域名槽角色扩展引用的操作状态。|
|/domainNames/slots/roles/extensionReferences/read|返回部署槽角色的扩展引用。|
|/domainNames/slots/roles/extensionReferences/write|添加或修改部署槽角色的扩展引用。|
|/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read|获取诊断设置。|
|/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write|添加或修改诊断设置。|
|/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read|获取指标定义。|
|/domainNames/slots/roles/read|获取部署槽的角色。|
|/domainNames/slots/roles/roleInstances/operationStatuses/read|读取域名槽角色角色实例的操作状态。|
|/domainNames/slots/roles/roleInstances/read|获取角色实例。|
|/domainNames/slots/roles/roleInstances/rebuild/action|重新生成角色实例。|
|/domainNames/slots/roles/roleInstances/reimage/action|重置角色实例的映像。|
|/domainNames/slots/roles/roleInstances/restart/action|重新启动角色实例。|
|/domainNames/slots/start/action|启动部署槽。|
|/domainNames/slots/state/start/write|将部署槽状态更改为已停止。|
|/domainNames/slots/state/stop/write|将部署槽状态更改为已启动。|
|/domainNames/slots/stop/action|暂停部署槽。|
|/domainNames/slots/upgradeDomain/write|逐步升级域。|
|/domainNames/slots/write|创建或更新部署。|
|/domainNames/swap/action|将过渡槽交换到生产槽。|
|/domainNames/write|添加或修改资源的域名。|
|/moveSubscriptionResources/action|将所有经典资源移到不同的订阅。|
|/operatingSystemFamilies/read|列出了 Microsoft Azure 中可用的来宾操作系统系列，还列出了每个系列可用的操作系统版本。|
|/operatingSystems/read|列出 Microsoft Azure 中当前可用的来宾操作系统版本。|
|/quotas/read|获取订阅的配额。|
|/register/action|注册到经典计算|
|/resourceTypes/skus/read|获取受支持资源类型的 SKU 列表。|
|/validateSubscriptionMoveAvailability/action|验证订阅是否可用于经典移动操作。|
|/virtualMachines/associatedNetworkSecurityGroups/delete|删除与虚拟机关联的网络安全组。|
|/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read|读取与网络安全组关联的虚拟机的操作状态。|
|/virtualMachines/associatedNetworkSecurityGroups/read|获取与虚拟机关联的网络安全组。|
|/virtualMachines/associatedNetworkSecurityGroups/write|添加与虚拟机关联的网络安全组。|
|/virtualMachines/asyncOperations/read|获取可能的异步操作|
|/virtualMachines/attachDisk/action|将数据磁盘附加到虚拟机。|
|/virtualMachines/delete|删除虚拟机。|
|/virtualMachines/detachDisk/action|从虚拟机中分离数据磁盘。|
|/virtualMachines/disks/read|检索数据磁盘的列表|
|/virtualMachines/downloadRemoteDesktopConnectionFile/action|下载虚拟机的 RDP 文件。|
|/virtualMachines/extensions/operationStatuses/read|读取虚拟机扩展的操作状态。|
|/virtualMachines/extensions/read|获取虚拟机扩展。|
|/virtualMachines/extensions/write|放置虚拟机扩展。|
|/virtualMachines/metrics/read|获取指标。|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete|删除与网络接口关联的网络安全组。|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/ operationStatuses/read|读取与网络安全组关联的虚拟机的操作状态。|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read|获取与网络接口关联的网络安全组。|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write|添加与网络接口关联的网络安全组。|
|/virtualMachines/operationStatuses/read|读取虚拟机的操作状态。|
|/virtualMachines/performMaintenance/action|在虚拟机上执行维护。|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read|获取诊断设置。|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write|添加或修改诊断设置。|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|获取指标定义。|
|/virtualMachines/read|检索虚拟机列表。|
|/virtualMachines/redeploy/action|重新部署虚拟机。|
|/virtualMachines/restart/action|重新启动虚拟机。|
|/virtualMachines/shutdown/action|关闭虚拟机。|
|/virtualMachines/start/action|启动虚拟机。|
|/virtualMachines/stop/action|停止虚拟机。|
|/virtualMachines/write|添加或修改虚拟机。|

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

| Operation | 说明 |
|---|---|
|/gatewaySupportedDevices/read|检索受支持设备的列表。|
|/networkSecurityGroups/delete|删除网络安全组。|
|/networkSecurityGroups/operationStatuses/read|读取网络安全组的操作状态。|
|/networkSecurityGroups/read|获取网络安全组。|
|/networkSecurityGroups/securityRules/delete|删除安全规则。|
|/networkSecurityGroups/securityRules/operationStatuses/read|读取网络安全组安全规则的操作状态。|
|/networkSecurityGroups/securityRules/read|获取安全规则。|
|/networkSecurityGroups/securityRules/write|添加或更新安全规则。|
|/networkSecurityGroups/write|添加新的网络安全组。|
|/quotas/read|获取订阅的配额。|
|/register/action|注册到经典网络|
|/reservedIps/delete|删除保留 IP。|
|/reservedIps/join/action|联接保留 IP|
|/reservedIps/link/action|链接保留 IP|
|/reservedIps/operationStatuses/read|读取保留 IP 的操作状态。|
|/reservedIps/read|获取保留 IP|
|/reservedIps/write|添加新的保留 IP|
|/virtualNetworks/capabilities/read|显示功能|
|/virtualNetworks/checkIPAddressAvailability/action|检查虚拟网络中给定 IP 地址的可用性。|
|/virtualNetworks/delete|删除虚拟网络。|
|/virtualNetworks/gateways/clientRevokedCertificates/delete|取消吊销客户端证书。|
|/virtualNetworks/gateways/clientRevokedCertificates/read|读取已吊销的客户端证书。|
|/virtualNetworks/gateways/clientRevokedCertificates/write|吊销客户端证书。|
|/virtualNetworks/gateways/clientRootCertificates/delete|删除虚拟网络网关客户端证书。|
|/virtualNetworks/gateways/clientRootCertificates/download/action|按指纹下载证书。|
|/virtualNetworks/gateways/clientRootCertificates/listPackage/action|列出虚拟网络网关证书包。|
|/virtualNetworks/gateways/clientRootCertificates/read|查找客户端根证书。|
|/virtualNetworks/gateways/clientRootCertificates/write|上传新的客户端根证书。|
|/virtualNetworks/gateways/connections/connect/action|建立站点到站点网关连接。|
|/virtualNetworks/gateways/connections/disconnect/action|断开站点到站点网关连接。|
|/virtualNetworks/gateways/connections/read|检索连接列表。|
|/virtualNetworks/gateways/connections/test/action|测试站点到站点网关连接。|
|/virtualNetworks/gateways/delete|删除虚拟网络网关。|
|/virtualNetworks/gateways/downloadDeviceConfigurationScript/action|下载设备配置脚本。|
|/virtualNetworks/gateways/downloadDiagnostics/action|下载网关诊断。|
|/virtualNetworks/gateways/listCircuitServiceKey/action|检索线路服务密钥。|
|/virtualNetworks/gateways/listPackage/action|列出虚拟网络网关包。|
|/virtualNetworks/gateways/operationStatuses/read|读取虚拟网络网关的操作状态。|
|/virtualNetworks/gateways/packages/read|获取虚拟网络网关包。|
|/virtualNetworks/gateways/read|获取虚拟网络网关。|
|/virtualNetworks/gateways/startDiagnostics/action|启动虚拟网络网关的诊断。|
|/virtualNetworks/gateways/stopDiagnostics/action|停止虚拟网络网关的诊断。|
|/virtualNetworks/gateways/write|添加虚拟网络网关。|
|/virtualNetworks/join/action|加入虚拟网络。|
|/virtualNetworks/operationStatuses/read|读取虚拟网络的操作状态。|
|/virtualNetworks/peer/action|在两个不同的虚拟网络之间建立对等互连。|
|/virtualNetworks/read|获取虚拟网络。|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete|删除与子网关联的网络安全组。|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read|读取与网络安全组关联的虚拟网络子网的操作状态。|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/read|获取与子网关联的网络安全组。|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/write|添加与子网关联的网络安全组。|
|/virtualNetworks/write|添加新的虚拟网络。|

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

| Operation | 说明 |
|---|---|
|/capabilities/read|显示功能|
|/checkStorageAccountAvailability/action|检查存储帐户的可用性。|
|/disks/read|返回存储帐户磁盘。|
|/images/read|返回映像。|
|/osImages/read|返回操作系统映像。|
|/publicImages/read|获取公共虚拟机映像。|
|/quotas/read|获取订阅的配额。|
|/register/action|注册到经典存储|
|/storageAccounts/delete|删除存储帐户。|
|/storageAccounts/disks/delete|删除给定的存储帐户磁盘。|
|/storageAccounts/disks/operationStatuses/read|读取资源的操作状态。|
|/storageAccounts/disks/read|返回存储帐户磁盘。|
|/storageAccounts/disks/write|添加存储帐户磁盘。|
|/storageAccounts/images/delete|删除给定的存储帐户映像。|
|/storageAccounts/images/read|返回存储帐户映像。|
|/storageAccounts/listKeys/action|列出存储帐户的访问密钥。|
|/storageAccounts/operationStatuses/read|读取资源的操作状态。|
|/storageAccounts/osImages/delete|删除给定的存储帐户操作系统映像。|
|/storageAccounts/osImages/read|返回存储帐户操作系统映像。|
|/storageAccounts/read|返回包含给定帐户的存储帐户。|
|/storageAccounts/regenerateKey/action|再生成存储帐户的现有访问密钥。|
|/storageAccounts/services/diagnosticSettings/read|获取诊断设置。|
|/storageAccounts/services/diagnosticSettings/write|添加或修改诊断设置。|
|/storageAccounts/services/metricDefinitions/read|获取指标定义。|
|/storageAccounts/services/metrics/read|获取指标。|
|/storageAccounts/services/read|获取可用服务。|
|/storageAccounts/write|添加新的存储帐户。|

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

| 操作 | 说明 |
|---|---|
|/accounts/delete|删除 API 帐户|
|/accounts/listKeys/action|列出密钥|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|获取资源的诊断设置。|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新资源的诊断设置。|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|获取认知服务的可用指标。|
|/accounts/read|读取 API 帐户。|
|/accounts/regenerateKey/action|再生成密钥|
|/accounts/skus/read|读取现有资源的可用 SKU。|
|/accounts/usages/read|获取现有资源的配额用量。|
|/accounts/write|写入 API 帐户。|
|/locations/checkSkuAvailability/action|读取订阅的可用 SKU。|
|/Operations/read|读取所有可用操作|
|/register/action|注册认知服务的订阅|
|/skus/read|读取认知服务的可用 SKU。|

## <a name="microsoftcommerce"></a>Microsoft.Commerce

| Operation | 说明 |
|---|---|
|/RateCard/read|返回给定订阅的产品数据、资源/计量元数据和费率。|
|/UsageAggregates/read|检索订阅的 Microsoft Azure 消耗量。 结果包含特定时间范围内的聚合用量数据、订阅和资源相关信息。|

## <a name="microsoftcompute"></a>Microsoft.Compute

| Operation | 说明 |
|---|---|
|/availabilitySets/delete|删除可用性集|
|/availabilitySets/read|获取可用性集的属性|
|/availabilitySets/vmSizes/read|列出可在可用性集中创建或更新的虚拟机大小|
|/availabilitySets/write|创建新的可用性集，或更新现有的可用性集|
|/disks/beginGetAccess/action|获取用于 Blob 访问的磁盘 SAS URI|
|/disks/delete|删除磁盘|
|/disks/endGetAccess/action|吊销磁盘的 SAS URI|
|/disks/read|获取磁盘的属性|
|/disks/write|创建新的磁盘，或更新现有的磁盘|
|/images/delete|删除映像|
|/images/read|获取映像的属性|
|/images/write|创建新的映像，或更新现有的映像|
|/locations/capsOperations/read|获取异步大写操作的状态|
|/locations/diskOperations/read|获取异步磁盘操作的状态|
|/locations/operations/read|获取异步操作的状态|
|/locations/publishers/artifacttypes/offers/read|获取平台映像产品/服务的属性|
|/locations/publishers/artifacttypes/offers/skus/read|获取平台映像 SKU 的属性|
|/locations/publishers/artifacttypes/offers/skus/versions/read|获取平台映像版本的属性|
|/locations/publishers/artifacttypes/types/read|获取 VMExtension 类型的属性|
|/locations/publishers/artifacttypes/types/versions/read|获取 VMExtension 版本的属性|
|/locations/publishers/read|获取发布服务器的属性|
|/locations/runCommands/read|列出位置中的可用运行命令|
|/locations/usages/read|获取某个位置中订阅的计算资源的服务限制和当前用量|
|/locations/vmSizes/read|列出某个位置的可用虚拟机大小|
|/operations/read|列出可对 Microsoft.Compute 资源提供程序使用的操作|
|/register/action|将订阅注册到 Microsoft.Compute 资源提供程序|
|/restorePointCollections/delete|删除恢复点集合以及包含的还原点|
|/restorePointCollections/read|获取还原点集合的属性|
|/restorePointCollections/restorePoints/delete|删除还原点|
|/restorePointCollections/restorePoints/read|获取还原点的属性|
|/restorePointCollections/restorePoints/retrieveSasUris/action|获取还原点的属性以及 Blob SAS URI|
|/restorePointCollections/restorePoints/write|创建新的还原点|
|/restorePointCollections/write|创建新的或更新现有的还原点集合|
|/sharedVMImages/delete|删除 SharedVMImage|
|/sharedVMImages/read|获取 SharedVMImage 的属性|
|/sharedVMImages/versions/delete|删除 SharedVMImageVersion|
|/sharedVMImages/versions/read|获取 SharedVMImageVersion 的属性|
|/sharedVMImages/versions/replicate/action|将 SharedVMImageVersion 复制到目标区域|
|/sharedVMImages/versions/write|创建新的 SharedVMImageVersion 或更新现有的 SharedVMImageVersion|
|/sharedVMImages/write|创建新的 SharedVMImage 或更新现有的 SharedVMImage|
|/skus/read|获取订阅可用的 Microsoft.Compute SKU 列表|
|/snapshots/beginGetAccess/action|获取用于 blob 访问的快照 SAS URI|
|/snapshots/delete|删除快照|
|/snapshots/endGetAccess/action|撤销快照的 SAS URI|
|/snapshots/read|获取快照的属性|
|/snapshots/write|创建新的快照，或更新现有的快照|
|/virtualMachines/capture/action|通过复制虚拟硬盘捕获虚拟机，并生成可用于创建类似虚拟机的模板|
|/virtualMachines/convertToManagedDisks/action|将虚拟机的基于 Blob 的磁盘转换为托管磁盘|
|/virtualMachines/deallocate/action|关闭虚拟机并释放计算资源|
|/virtualMachines/delete|删除虚拟机|
|/virtualMachines/extensions/delete|删除虚拟机扩展|
|/virtualMachines/extensions/read|获取虚拟机扩展的属性|
|/virtualMachines/extensions/write|创建新的或更新现有的虚拟机扩展|
|/virtualMachines/generalize/action|将虚拟机状态设置为通用化，并准备要捕获的虚拟机|
|/virtualMachines/instanceView/read|获取虚拟机的详细运行时状态及其资源|
|/virtualMachines/performMaintenance/action|在 VM 上执行维护操作。|
|/virtualMachines/powerOff/action|关闭虚拟机。 请注意，该虚拟机会继续产生费用。|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|读取虚拟机指标定义|
|/virtualMachines/read|获取虚拟机的属性|
|/virtualMachines/redeploy/action|重新部署虚拟机|
|/virtualMachines/reimage/action|对使用差异磁盘的虚拟机重置映像。|
|/virtualMachines/restart/action|重新启动虚拟机|
|/virtualMachines/runCommand/action|执行虚拟机上的预定义脚本|
|/virtualMachines/start/action|启动虚拟机|
|/virtualMachines/vmSizes/read|列出可将虚拟机更新到的大小|
|/virtualMachines/write|创建新的虚拟机，或更新现有的虚拟机|
|/virtualMachineScaleSets/deallocate/action|关闭并释放虚拟机规模集实例的计算资源 |
|/virtualMachineScaleSets/delete|删除虚拟机规模集|
|/virtualMachineScaleSets/delete/action|删除虚拟机规模集的实例|
|/virtualMachineScaleSets/extensions/delete|删除虚拟机规模集扩展|
|/virtualMachineScaleSets/extensions/read|获取虚拟机规模集扩展的属性|
|/virtualMachineScaleSets/extensions/write|创建新的虚拟机规模集扩展或更新现有虚拟机规模集扩展|
|/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action|手动浏览服务结构虚拟机规模集的平台更新域，完成卡住的挂起更新|
|/virtualMachineScaleSets/instanceView/read|获取虚拟机规模集的实例视图|
|/virtualMachineScaleSets/manualUpgrade/action|手动将实例更新到虚拟机规模集的最新模型|
|/virtualMachineScaleSets/networkInterfaces/read|获取虚拟机规模集的所有网络接口的属性|
|/virtualMachineScaleSets/osUpgradeHistory/read|获取虚拟机规模集的 OS 升级历史记录|
|/virtualMachineScaleSets/performMaintenance/action|在虚拟机规模集的实例上执行计划内维护|
|/virtualMachineScaleSets/powerOff/action|关闭虚拟机规模集的实例|
|/virtualMachineScaleSets/providers/Microsoft.Insights/metricDefinitions/read|读取虚拟机规模集指标定义|
|/virtualMachineScaleSets/publicIPAddresses/read|获取虚拟机规模集的所有公用 IP 地址的属性|
|/virtualMachineScaleSets/read|获取虚拟机规模集的属性|
|/virtualMachineScaleSets/redeploy/action|重新部署虚拟机规模集的实例|
|/virtualMachineScaleSets/reimage/action|对虚拟机规模集的实例进行映像重置|
|/virtualMachineScaleSets/restart/action|重新启动虚拟机规模集的实例|
|/virtualMachineScaleSets/rollingUpgrades/cancel/action|取消虚拟机规模集的滚动升级|
|/virtualMachineScaleSets/rollingUpgrades/read|获取虚拟机规模集的最新滚动升级状态|
|/virtualMachineScaleSets/scale/action|验证现有虚拟机规模集是否可以缩小/扩大到指定的实例计数|
|/virtualMachineScaleSets/skus/read|列出现有虚拟机规模集的有效 SKU|
|/virtualMachineScaleSets/start/action|启动虚拟机规模集的实例|
|/virtualMachineScaleSets/virtualMachines/deallocate/action|关闭并释放 VM 规模集中虚拟机的计算资源。|
|/virtualMachineScaleSets/virtualMachines/delete|删除 VM 规模集中的特定虚拟机。|
|/virtualMachineScaleSets/virtualMachines/instanceView/read|检索 VM 规模集中虚拟机的实例视图。|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/ ipConfigurations/publicIPAddresses/read|获取使用虚拟机规模集创建的公用 IP 地址的属性。 虚拟机规模集可为每个 ipconfiguration（专用 IP）最多创建一个公用 IP|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read|获取使用虚拟机规模集创建的网络接口的一个或所有 IP 配置的属性。 IP 配置表示专用 IP|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/read|获取使用虚拟机规模集创建的虚拟机的一个或所有网络接口的属性|
|/virtualMachineScaleSets/virtualMachines/performMaintenance/action|在虚拟机规模集的虚拟机实例上执行计划内维护|
|/virtualMachineScaleSets/virtualMachines/powerOff/action|关闭 VM 规模集中的虚拟机实例。|
|/virtualMachineScaleSets/virtualMachines/providers/ Microsoft.Insights/metricDefinitions/read|读取虚拟机的规模集指标定义|
|/virtualMachineScaleSets/virtualMachines/read|检索 VM 规模集中虚拟机的属性|
|/virtualMachineScaleSets/virtualMachines/redeploy/action|重新部署虚拟机规模集中的虚拟机实例|
|/virtualMachineScaleSets/virtualMachines/reimage/action|对虚拟机规模集中的虚拟机实例进行映像重置。|
|/virtualMachineScaleSets/virtualMachines/restart/action|重新启动 VM 规模集中的虚拟机实例。|
|/virtualMachineScaleSets/virtualMachines/start/action|启动 VM 规模集中的虚拟机实例。|
|/virtualMachineScaleSets/virtualMachines/write|更新 VM 规模集中虚拟机的属性|
|/virtualMachineScaleSets/write|创建新的或更新现有的虚拟机规模集|

## <a name="microsoftconsumption"></a>Microsoft.Consumption

| Operation | 说明 |
|---|---|
|/balances/read|列出管理组的计费周期的使用情况摘要。|
|/budgets/read|按订阅或管理组列出预算。|
|/budgets/write|按订阅或管理组创建、更新和删除预算。|
|/marketplaces/read|列出 EA 和 WebDirect 订阅的商城资源使用情况。|
|/operations/read|列出 Microsoft.Consumption 资源提供程序支持的所有操作。|
|/pricesheets/read|列出订阅或管理组的 Pricesheets 数据。|
|/reservationDetails/read|按预订订单或管理组列出保留实例的使用情况详细信息。 详细信息数据为每天每个实例级别。|
|/reservationSummaries/read|按预订订单或管理组列出保留实例的使用情况摘要。 摘要数据为每月或每天级别。|
|/reservationTransactions/read|按管理组列出保留实例的事务历史记录。|
|/terms/read|列出订阅或管理组的条款。|
|/usageDetails/read|列出 EA 和 WebDirect 订阅的范围的使用情况详细信息。|

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

| Operation | 说明 |
|---|---|
|/containerGroups/containers/logs/read|获取特定容器的日志。|
|/containerGroups/delete|删除特定容器组。|
|/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read|获取容器组的诊断设置。|
|/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新容器组的诊断设置。|
|/containerGroups/providers/Microsoft.Insights/metricDefinitions/read|获取容器组的可用指标。|
|/containerGroups/read|获取所有容器组。|
|/containerGroups/write|创建或更新特定容器组。|

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

| Operation | 说明 |
|---|---|
|/checkNameAvailability/read|检查容器注册表名称是否可用。|
|/locations/operationResults/read|获取异步操作结果|
|/operations/read|列出所有可用的 Azure 容器注册表 REST API 操作|
|/register/action|注册容器注册表资源提供程序的订阅，并启用容器注册表的创建|
|/registries/delete|删除容器注册表。|
|/registries/eventGridFilters/delete|从容器注册表中删除事件网格筛选器。|
|/registries/eventGridFilters/read|获取指定事件网格筛选器的属性，或列出指定容器注册表的所有事件网格筛选器。|
|/registries/eventGridFilters/write|使用指定参数创建或更新容器注册表的事件网格筛选器。|
|/registries/listCredentials/action|列出指定容器注册表的登录凭据。|
|/registries/listUsages/read|列出指定容器注册表的配额使用情况。|
|/registries/operationStatuses/read|获取注册表异步操作状态|
|/registries/read|获取指定容器注册表的属性，或列出指定资源组或订阅下的所有容器注册表。|
|/registries/regenerateCredential/action|为指定容器注册表重新生成一个登录凭据。|
|/registries/replications/delete|从容器注册表中删除复制。|
|/registries/replications/operationStatuses/read|获取复制步操作状态|
|/registries/replications/read|获取指定复制的属性，或列出指定容器注册表的所有复制。|
|/registries/replications/write|使用指定参数创建或更新容器注册表的复制。|
|/registries/webhooks/delete|从容器注册表中删除 Webhook。|
|/registries/webhooks/getCallbackConfig/action|获取服务 URI 的配置和 Webhook 的自定义标头。|
|/registries/webhooks/listEvents/action|列出指定 Webhook 的最新事件。|
|/registries/webhooks/operationStatuses/read|获取 Webhook 异步操作状态|
|/registries/webhooks/ping/action|触发一个将发送到 Webhook 的 ping 事件。|
|/registries/webhooks/read|获取指定 Webhook 的属性，或列出指定容器注册表的所有 Webhook。|
|/registries/webhooks/write|使用指定参数创建或更新容器注册表的 Webhook。|
|/registries/write|使用指定参数创建或更新容器注册表。|

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

| Operation | 说明 |
|---|---|
|/containerServices/delete|删除指定的容器服务|
|/containerServices/read|获取指定的容器服务|
|/containerServices/write|放置或更新指定的容器服务|

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

| Operation | 说明 |
|---|---|
|/applications/delete|删除操作|
|/applications/listSecrets/action|列出机密|
|/applications/listSingleSignOnToken/action|读取单一登录令牌|
|/applications/read|读取操作|
|/applications/write|写入操作|
|/applications/write|写入操作|
|/listCommunicationPreference/action|列出通信首选项|
|/operations/read|读取操作|
|/updateCommunicationPreference/action|更新通信首选项|

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

| Operation | 说明 |
|---|---|
|/hubs/adobemetadata/action|创建或更新任何 Azure Customer Insights Adobe 元数据|
|/hubs/adobemetadata/read|读取任何 Azure Customer Insights Adobe 元数据|
|/hubs/authorizationPolicies/delete|删除任何 Azure Customer Insights 共享访问签名策略|
|/hubs/authorizationPolicies/read|读取任何 Azure Customer Insights 共享访问签名策略|
|/hubs/authorizationPolicies/regeneratePrimaryKey/action|再生成 Azure Customer Insights 共享访问签名策略主密钥|
|/hubs/authorizationPolicies/regenerateSecondaryKey/action|再生成 Azure Customer Insights 共享访问签名策略辅助密钥|
|/hubs/authorizationPolicies/write|创建或更新任何 Azure Customer Insights 共享访问签名策略|
|/hubs/connectors/activate/action|激活任何 Azure Customer Insights 连接器|
|/hubs/connectors/activate/action|激活任何 Azure Customer Insights 连接器|
|/hubs/connectors/delete|删除任何 Azure Customer Insights 连接器|
|/hubs/connectors/getruntimestatus/action|获取 Azure Customer Insights 连接器运行时状态|
|/hubs/connectors/mappings/activate/action|激活任何 Azure Customer Insights 连接器映射|
|/hubs/connectors/mappings/delete|删除任何 Azure Customer Insights 连接器映射|
|/hubs/connectors/mappings/operations/read|读取任何 Azure Customer Insights 连接器映射操作结果|
|/hubs/connectors/mappings/read|读取任何 Azure Customer Insights 连接器映射|
|/hubs/connectors/mappings/write|创建或更新任何 Azure Customer Insights 连接器映射|
|/hubs/connectors/operations/read|读取任何 Azure Customer Insights 连接器操作结果|
|/hubs/connectors/read|读取任何 Azure Customer Insights 连接器|
|/hubs/connectors/saveauthinfo/action|创建或更新任何 Azure Customer Insights 连接器身份验证信息|
|/hubs/connectors/update/action|更新任何 Azure Customer Insights 连接器|
|/hubs/connectors/write|创建或更新任何 Azure Customer Insights 连接器|
|/hubs/crmmetadata/action|创建或更新任何 Azure Customer Insights Crm 元数据|
|/hubs/crmmetadata/read|读取任何 Azure Customer Insights Crm 元数据|
|/hubs/delete|删除任何 Azure Customer Insights 中心|
|/hubs/gdpr/delete|删除任何 Azure Customer Insights Gdpr|
|/hubs/gdpr/read|读取任何 Azure Customer Insights Gdpr|
|/hubs/gdpr/write|创建或更新任何 Azure Customer Insights Gdpr|
|/hubs/getbillingcredits/read|获取 Azure Customer Insights 中心账单额度|
|/hubs/getbillinghistory/read|获取 Azure Customer Insights 中心账单历史记录|
|/hubs/images/delete|删除任何 Azure Customer Insights 映像|
|/hubs/images/read|读取任何 Azure Customer Insights 映像|
|/hubs/images/write|创建或更新任何 Azure Customer Insights 映像|
|/hubs/interactions/delete|删除任何 Azure Customer Insights 交互|
|/hubs/interactions/operations/read|读取任何 Azure Customer Insights 交互操作结果|
|/hubs/interactions/read|读取任何 Azure Customer Insights 交互|
|/hubs/interactions/suggestrelationshiplinks/action|建议任何 Azure Customer Insights 交互的关系链接|
|/hubs/interactions/write|创建或更新任何 Azure Customer Insights 交互|
|/hubs/kpi/delete|删除任何 Azure Customer Insights 关键性能指标|
|/hubs/kpi/operations/read|读取任何 Azure Customer Insights 关键性能指标操作结果|
|/hubs/kpi/read|读取任何 Azure Customer Insights 关键性能指标|
|/hubs/kpi/reprocess/action|重新处理任何 Azure Customer Insights 关键性能指标|
|/hubs/kpi/write|创建或更新任何 Azure Customer Insights 关键性能指标|
|/hubs/links/delete|删除任何 Azure Customer Insights 链接|
|/hubs/links/operations/read|读取任何 Azure Customer Insights 链接操作结果|
|/hubs/links/read|读取任何 Azure Customer Insights 链接|
|/hubs/links/write|创建或更新任何 Azure 客户链接|
|/hubs/msemetadata/action|创建或更新任何 Azure Customer Insights Mse 元数据|
|/hubs/msemetadata/read|读取任何 Azure Customer Insights Mse 元数据|
|/hubs/operationresults/read|获取 Azure Customer Insights 中心操作结果|
|/hubs/predictions/delete|删除任何 Azure Customer Insights 预测|
|/hubs/predictions/operations/read|读取任何 Azure Customer Insights 预测操作结果|
|/hubs/predictions/read|读取任何 Azure Customer Insights 预测|
|/hubs/predictions/write|创建或更新任何 Azure 客户预测|
|/hubs/predictivematchpolicies/delete|删除任何 Azure Customer Insights 预测匹配策略|
|/hubs/predictivematchpolicies/operations/read|读取任何 Azure Customer Insights 预测匹配策略操作结果|
|/hubs/predictivematchpolicies/read|读取任何 Azure Customer Insights 预测匹配策略|
|/hubs/predictivematchpolicies/write|读取或更新任何 Azure Customer Insights 预测匹配策略|
|/hubs/profiles/delete|删除任何 Azure Customer Insights 配置文件|
|/hubs/profiles/operations/read|读取任何 Azure Customer Insights 配置文件操作结果|
|/hubs/profiles/read|读取任何 Azure Customer Insights 配置文件|
|/hubs/profiles/write|写入任何 Azure Customer Insights 配置文件|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/read|获取资源的诊断设置|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新资源的诊断设置|
|/hubs/providers/Microsoft.Insights/logDefinitions/read|获取资源的可用日志|
|/hubs/providers/Microsoft.Insights/metricDefinitions/read|获取资源的可用指标|
|/hubs/read|读取任何 Azure Customer Insights 中心|
|/hubs/relationshiplinks/delete|删除任何 Azure Customer Insights 关系链接|
|/hubs/relationshiplinks/operations/read|读取任何 Azure Customer Insights 关系链接操作结果|
|/hubs/relationshiplinks/read|读取任何 Azure Customer Insights 关系链接|
|/hubs/relationshiplinks/write|创建或更新任何 Azure Customer Insights 关系链接|
|/hubs/relationships/delete|删除任何 Azure Customer Insights 关系|
|/hubs/relationships/operations/read|读取任何 Azure Customer Insights 关系操作结果|
|/hubs/relationships/read|读取任何 Azure Customer Insights 关系|
|/hubs/relationships/write|创建或更新任何 Azure Customer Insights 关系|
|/hubs/roleAssignments/delete|删除任何 Azure Customer Insights RBAC 分配|
|/hubs/roleAssignments/operations/read|读取任何 Azure Customer Insights RBAC 分配操作结果|
|/hubs/roleAssignments/read|读取任何 Azure Customer Insights RBAC 分配|
|/hubs/roleAssignments/write|创建或更新任何 Azure Customer Insights RBAC 分配|
|/hubs/roles/read|读取任何 Azure Customer Insights RBAC 角色|
|/hubs/salesforcemetadata/action|创建或更新任何 Azure Customer Insights SalesForce 元数据|
|/hubs/salesforcemetadata/read|读取任何 Azure Customer Insights SalesForce 元数据|
|/hubs/segments/delete|删除任何 Azure Customer Insights 段|
|/hubs/segments/dynamic/action|管理任何 Azure Customer Insights 动态段|
|/hubs/segments/read|读取任何 Azure Customer Insights 段|
|/hubs/segments/static/action|管理任何 Azure Customer Insights 静态段|
|/hubs/segments/write|创建或更新任何 Azure Customer Insights 段|
|/hubs/sqlconnectionstrings/delete|删除任何 Azure Customer Insights SqlConnectionStrings|
|/hubs/sqlconnectionstrings/read|读取任何 Azure Customer Insights SqlConnectionStrings|
|/hubs/sqlconnectionstrings/write|创建或更新任何 Azure Customer Insights SqlConnectionStrings|
|/hubs/suggesttypeschema/action|从示例数据生成建议类型架构|
|/hubs/tenantmanagement/read|管理任何 Azure Customer Insights 中心设置|
|/hubs/views/delete|删除任何 Azure Customer Insights 应用视图|
|/hubs/views/read|读取任何 Azure Customer Insights 应用视图|
|/hubs/views/write|创建或更新任何 Azure Customer Insights 应用视图|
|/hubs/widgettypes/read|读取任何 Azure Customer Insights 应用小组件类型|
|/hubs/write|创建或更新任何 Azure Customer Insights 中心|
|/operations/read|读取 Azure Customer Insights API 元数据|
|/register/action|注册 Customer Insights 资源提供程序的订阅，并启用 Customer Insights 资源的创建|
|/unregister/action|取消注册 Customer Insights 资源提供程序的订阅|

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

| Operation | 说明 |
|---|---|
|/catalogs/delete|删除目录。|
|/catalogs/read|获取订阅或资源组下一个或多个目录的属性。|
|/catalogs/write|创建目录，或更新目录的标记和属性。|
|/checkNameAvailability/action|检查租户的目录名称可用性。|
|/operations/read|列出可对 Microsoft.DataCatalog 资源提供程序执行的操作。|
|/register/action|使用 Microsoft.DataCatalog 资源提供程序注册订阅。|
|/unregister/action|从 Microsoft.DataCatalog 资源提供程序取消注册订阅。|

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

| Operation | 说明 |
|---|---|
|/datafactories/providers/Microsoft.Insights/diagnosticSettings/read|获取资源的诊断设置|
|/datafactories/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新资源的诊断设置|
|/datafactories/providers/Microsoft.Insights/metricDefinitions/read|获取数据工厂的可用指标|
|/factories/providers/Microsoft.Insights/diagnosticSettings/read|获取资源的诊断设置|
|/factories/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新资源的诊断设置|
|/factories/providers/Microsoft.Insights/logDefinitions/read|获取工厂的可用日志|
|/factories/providers/Microsoft.Insights/metricDefinitions/read|获取工厂的可用指标|

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

| Operation | 说明 |
|---|---|
|/accounts/computePolicies/delete|删除计算策略。|
|/accounts/computePolicies/read|获取有关计算策略的信息。|
|/accounts/computePolicies/write|创建或更新计算策略。|
|/accounts/dataLakeStoreAccounts/delete|从 DataLakeAnalytics 帐户取消链接 DataLakeStore 帐户。|
|/accounts/dataLakeStoreAccounts/read|获取有关 DataLakeAnalytics 帐户的链接 DataLakeStore 帐户的信息。|
|/accounts/dataLakeStoreAccounts/write|获取或更新 DataLakeAnalytics 帐户的链接 DataLakeStore 帐户。|
|/accounts/delete|删除 DataLakeAnalytics 帐户。|
|/accounts/firewallRules/delete|删除防火墙规则。|
|/accounts/firewallRules/read|获取有关防火墙规则的信息。|
|/accounts/firewallRules/write|创建或更新防火墙规则。|
|/accounts/operationResults/read|获取 DataLakeAnalytics 帐户操作的结果。|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|获取 DataLakeAnalytics 帐户的诊断设置。|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新 DataLakeAnalytics 帐户的诊断设置。|
|/accounts/providers/Microsoft.Insights/logDefinitions/read|获取 DataLakeAnalytics 帐户的可用日志。|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|获取 DataLakeAnalytics 帐户的可用指标。|
|/accounts/read|获取有关现有 DataLakeAnalytics 帐户的信息。|
|/accounts/storageAccounts/Containers/listSasTokens/action|列出 DataLakeAnalytics 帐户的链接存储帐户的存储容器的 SAS 令牌。|
|/accounts/storageAccounts/Containers/read|获取 DataLakeAnalytics 帐户的链接存储帐户的容器。|
|/accounts/storageAccounts/delete|从 DataLakeAnalytics 帐户取消链接存储帐户。|
|/accounts/storageAccounts/read|获取有关 DataLakeAnalytics 帐户的链接存储帐户的信息。|
|/accounts/storageAccounts/write|创建或更新 DataLakeAnalytics 帐户的链接存储帐户。|
|/accounts/TakeOwnership/action|授予取消由其他用户提交的作业的权限。|
|/accounts/write|创建或更新 DataLakeAnalytics 帐户。|
|/locations/capability/read|获取有关使用 DataLakeAnalytics 的订阅的功能信息。|
|/locations/checkNameAvailability/action|检查 DataLakeAnalytics 帐户名称的可用性。|
|/locations/operationResults/read|获取 DataLakeAnalytics 帐户操作的结果。|
|/operations/read|获取 DataLakeAnalytics 的可用操作。|
|/register/action|将订阅注册到 DataLakeAnalytics。|

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

| Operation | 说明 |
|---|---|
|/accounts/delete|删除 DataLakeStore 帐户。|
|/accounts/enableKeyVault/action|为 DataLakeStore 帐户启用 KeyVault。|
|/accounts/firewallRules/delete|删除防火墙规则。|
|/accounts/firewallRules/read|获取有关防火墙规则的信息。|
|/accounts/firewallRules/write|创建或更新防火墙规则。|
|/accounts/operationResults/read|获取 DataLakeStore 帐户操作的结果。|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|获取 DataLakeStore 帐户的诊断设置。|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新 DataLakeStore 帐户的诊断设置。|
|/accounts/providers/Microsoft.Insights/logDefinitions/read|获取 DataLakeStore 帐户的可用日志。|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|获取 DataLakeStore 帐户的可用指标。|
|/accounts/read|获取有关现有 DataLakeStore 帐户的信息。|
|/accounts/Superuser/action|使用 Microsoft.Authorization/roleAssignments/write 授权后，对 Data Lake Store 上的超级用户授权。|
|/accounts/trustedIdProviders/delete|删除受信任的标识提供者。|
|/accounts/trustedIdProviders/read|获取有关受信任标识提供者的信息。|
|/accounts/trustedIdProviders/write|创建或更新受信任的标识提供者。|
|/accounts/write|创建或更新 DataLakeStore 帐户。|
|/locations/capability/read|获取有关使用 DataLakeStore 的订阅的功能信息。|
|/locations/checkNameAvailability/action|检查 DataLakeStore 帐户名称的可用性。|
|/locations/operationResults/read|获取 DataLakeStore 帐户操作的结果。|
|/operations/read|获取 DataLakeStore 的可用操作。|
|/register/action|将订阅注册到 DataLakeStore。|

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

| Operation | 说明 |
|---|---|
|/locations/performanceTiers/read|返回可用的性能层列表。|
|/performanceTiers/read|返回可用的性能层列表。|
|/servers/delete|删除现有服务器。|
|/servers/firewallRules/delete|删除现有防火墙规则。|
|/servers/firewallRules/read|返回服务器的防火墙规则列表，或获取指定防火墙规则的属性。|
|/servers/firewallRules/write|使用指定参数创建防火墙规则，或更新现有规则。|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|获取资源的诊断设置|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新资源的诊断设置|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|返回可用于数据库的指标类型|
|/servers/read|返回服务器列表，或获取指定服务器的属性。|
|/servers/recoverableServers/read|返回可恢复的 MySQL 服务器信息|
|/servers/virtualNetworkRules/delete|删除现有虚拟网络规则|
|/servers/virtualNetworkRules/read|返回虚拟网络规则列表，或获取指定虚拟网络规则的属性。|
|/servers/virtualNetworkRules/write|使用指定参数创建虚拟网络规则，或更新指定虚拟网络规则的属性或标记。|
|/servers/write|使用指定参数创建服务器，或更新指定服务器的属性或标记。|

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

| Operation | 说明 |
|---|---|
|/locations/performanceTiers/read|返回可用的性能层列表。|
|/performanceTiers/read|返回可用的性能层列表。|
|/servers/delete|删除现有服务器。|
|/servers/firewallRules/delete|删除现有防火墙规则。|
|/servers/firewallRules/read|返回服务器的防火墙规则列表，或获取指定防火墙规则的属性。|
|/servers/firewallRules/write|使用指定参数创建防火墙规则，或更新现有规则。|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|获取资源的诊断设置|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新资源的诊断设置|
|/servers/providers/Microsoft.Insights/logDefinitions/read|返回数据库可用的日志类型|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|返回可用于数据库的指标类型|
|/servers/read|返回服务器列表，或获取指定服务器的属性。|
|/servers/recoverableServers/read|返回可恢复的 PostgreSQL 服务器信息|
|/servers/virtualNetworkRules/delete|删除现有虚拟网络规则|
|/servers/virtualNetworkRules/read|返回虚拟网络规则列表，或获取指定虚拟网络规则的属性。|
|/servers/virtualNetworkRules/write|使用指定参数创建虚拟网络规则，或更新指定虚拟网络规则的属性或标记。|
|/servers/write|使用指定参数创建服务器，或更新指定服务器的属性或标记。|

## <a name="microsoftdevices"></a>Microsoft.Devices

| Operation | 说明 |
|---|---|
|/checkNameAvailability/Action|检查 IotHub 名称是否可用|
|/checkProvisioningServiceNameAvailability/Action|检查 IotHub 名称是否可用|
|/ElasticPools/diagnosticSettings/read|获取资源的诊断设置|
|/ElasticPools/diagnosticSettings/write|创建或更新资源的诊断设置|
|/elasticPools/iotHubTenants/Delete|删除 IotHub 租户资源|
|/ElasticPools/IotHubTenants/diagnosticSettings/read|获取资源的诊断设置|
|/ElasticPools/IotHubTenants/diagnosticSettings/write|创建或更新资源的诊断设置|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete|删除 EventHub 使用者组|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read|获取 EventHub 使用者组|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write|创建 EventHub 使用者组|
|/elasticPools/iotHubTenants/exportDevices/Action|导出设备|
|/elasticPools/iotHubTenants/getStats/Read|获取 IotHub 租户统计资源|
|/elasticPools/iotHubTenants/importDevices/Action|导入设备|
|/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action|获取 IotHub 租户密钥|
|/elasticPools/iotHubTenants/jobs/Read|获取在给定 IotHub 上提交的作业详细信息|
|/elasticPools/iotHubTenants/listKeys/Action|获取 IotHub 租户密钥|
|/ElasticPools/IotHubTenants/logDefinitions/read|获取 IotHub 服务的可用日志定义|
|/ElasticPools/IotHubTenants/metricDefinitions/read|获取 IotHub 服务的可用指标|
|/elasticPools/iotHubTenants/quotaMetrics/Read|获取配额指标|
|/elasticPools/iotHubTenants/Read|获取 IotHub 租户资源|
|/elasticPools/iotHubTenants/routing/routes/$testall/Action|针对所有现有路由测试消息|
|/elasticPools/iotHubTenants/routing/routes/$testnew/Action|针对提供的测试路由测试消息|
|/elasticPools/iotHubTenants/routingEndpointsHealth/Read|获取 IotHub 的所有路由终结点的运行状况|
|/elasticPools/iotHubTenants/Write|创建或更新 IotHub 租户资源|
|/ElasticPools/metricDefinitions/read|获取 IotHub 服务的可用指标|
|/iotHubs/certificates/generateVerificationCode/Action|生成验证码|
|/iotHubs/certificates/verify/Action|验证证书资源|
|/iotHubs/Delete|删除 IotHub 资源|
|/IotHubs/diagnosticSettings/read|获取资源的诊断设置|
|/IotHubs/diagnosticSettings/write|创建或更新资源的诊断设置|
|/iotHubs/eventGridFilters/Delete|删除事件网格筛选器|
|/iotHubs/eventGridFilters/Read|获取事件网格筛选器|
|/iotHubs/eventGridFilters/Write|创建新的或更新现有的事件网格筛选器|
|/iotHubs/eventHubEndpoints/consumerGroups/Delete|删除 EventHub 使用者组|
|/iotHubs/eventHubEndpoints/consumerGroups/Read|获取 EventHub 使用者组|
|/iotHubs/eventHubEndpoints/consumerGroups/Write|创建 EventHub 使用者组|
|/iotHubs/exportDevices/Action|导出设备|
|/iotHubs/importDevices/Action|导入设备|
|/iotHubs/iotHubKeys/listkeys/Action|获取给定名称的 IotHub 密钥|
|/iotHubs/iotHubStats/Read|获取 IotHub 统计信息|
|/iotHubs/jobs/Read|获取在给定 IotHub 上提交的作业详细信息|
|/iotHubs/listkeys/Action|获取所有 IotHub 密钥|
|/IotHubs/logDefinitions/read|获取 IotHub 服务的可用日志定义|
|/IotHubs/metricDefinitions/read|获取 IotHub 服务的可用指标|
|/iotHubs/quotaMetrics/Read|获取配额指标|
|/iotHubs/Read|获取 IotHub 资源|
|/iotHubs/routing/$testall/Action|针对所有现有路由测试消息|
|/iotHubs/routing/$testnew/Action|针对提供的测试路由测试消息|
|/iotHubs/routingEndpointsHealth/Read|获取 IotHub 的所有路由终结点的运行状况|
|/iotHubs/skus/Read|获取有效的 IotHub SKU|
|/iotHubs/Write|创建或更新 IotHub 资源|
|/operations/Read|获取所有 ResourceProvider 操作|
|/provisioningServices/certificates/generateVerificationCode/Action|生成验证码|
|/provisioningServices/certificates/verify/Action|验证证书资源|
|/provisioningServices/Delete|删除 IotDps 资源|
|/provisioningServices/diagnosticSettings/read|获取资源的诊断设置|
|/provisioningServices/diagnosticSettings/write|创建或更新资源的诊断设置|
|/provisioningServices/listkeys/Action|获取所有 IotDps 密钥|
|/provisioningServices/logDefinitions/read|获取预配服务的可用日志定义|
|/provisioningServices/metricDefinitions/read|获取预配服务的可用指标|
|/provisioningServices/ProvisioningServiceKeys/listkeys/Action|获取 IotDps 密钥的密钥名称|
|/provisioningServices/Read|获取 IotDps 资源|
|/provisioningServices/skus/Read|获取有效的 IotDps SKU|
|/provisioningServices/Write|创建 IotDps 资源|
|/register/action|注册 IotHub 资源提供程序的订阅，并启用 IotHub 资源的创建|
|/register/action|注册 IotHub 资源提供程序的订阅，并启用 IotHub 资源的创建|
|/usages/Read|获取此提供程序的订阅用量详细信息。|

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

| Operation | 说明 |
|---|---|
|/labCenters/delete|删除实验室中心。|
|/labCenters/read|读取实验室中心。|
|/labCenters/write|添加或修改实验室中心。|
|/labs/artifactSources/armTemplates/read|读取 Azure Resource Manager 模板。|
|/labs/artifactSources/artifacts/GenerateArmTemplate/action|为给定的项目生成 ARM 模板，将所需的文件上传到存储帐户，然后验证生成的项目。|
|/labs/artifactSources/artifacts/read|读取项目。|
|/labs/artifactSources/delete|删除项目源。|
|/labs/artifactSources/read|读取项目源。|
|/labs/artifactSources/write|添加或修改项目源。|
|/labs/ClaimAnyVm/action|在实验室中认领随机可认领虚拟机。|
|/labs/costs/read|读取成本。|
|/labs/costs/write|添加或修改成本。|
|/labs/CreateEnvironment/action|在实验室中创建虚拟机。|
|/labs/customImages/delete|删除自定义映像。|
|/labs/customImages/read|读取自定义映像。|
|/labs/customImages/write|添加或修改自定义映像。|
|/labs/delete|删除实验室。|
|/labs/ExportResourceUsage/action|将实验室资源使用情况导出到存储帐户|
|/labs/formulas/delete|删除公式。|
|/labs/formulas/read|读取公式。|
|/labs/formulas/write|添加或修改公式。|
|/labs/galleryImages/read|读取库映像。|
|/labs/GenerateUploadUri/action|生成用于将自定义磁盘映像上传到实验室的 URI。|
|/labs/ImportVirtualMachine/action|将虚拟机导入到不同实验室。|
|/labs/ListVhds/action|列出可用于创建自定义映像的磁盘映像。|
|/labs/notificationChannels/delete|删除 notificationchannels。|
|/labs/notificationChannels/Notify/action|将通知发送到提供的通道。|
|/labs/notificationChannels/read|读取 notificationchannels。|
|/labs/notificationChannels/write|添加或修改 notificationchannels。|
|/labs/policySets/EvaluatePolicies/action|评估实验室策略。|
|/labs/policySets/policies/delete|删除策略。|
|/labs/policySets/policies/read|读取策略。|
|/labs/policySets/policies/write|添加或修改策略。|
|/labs/read|读取实验室。|
|/labs/schedules/delete|删除计划。|
|/labs/schedules/Execute/action|执行计划。|
|/labs/schedules/ListApplicable/action|列出所有适用的计划|
|/labs/schedules/read|读取计划。|
|/labs/schedules/write|添加或修改计划。|
|/labs/serviceRunners/delete|删除服务运行程序。|
|/labs/serviceRunners/read|读取服务运行程序。|
|/labs/serviceRunners/write|添加或修改服务运行程序。|
|/labs/users/delete|删除用户配置文件。|
|/labs/users/disks/Attach/action|在虚拟机上附加和创建磁盘的租约。|
|/labs/users/disks/delete|删除磁盘。|
|/labs/users/disks/Detach/action|分离并破坏已附加到虚拟机的磁盘的租约。|
|/labs/users/disks/read|读取磁盘。|
|/labs/users/disks/write|添加或修改磁盘。|
|/labs/users/environments/delete|删除环境。|
|/labs/users/environments/read|读取环境。|
|/labs/users/environments/write|添加或修改环境。|
|/labs/users/read|读取用户配置文件。|
|/labs/users/secrets/delete|删除机密。|
|/labs/users/secrets/read|读取机密。|
|/labs/users/secrets/write|添加或修改机密。|
|/labs/users/serviceFabrics/delete|删除 Service Fabric。|
|/labs/users/serviceFabrics/ListApplicableSchedules/action|列出所有适用的计划|
|/labs/users/serviceFabrics/read|读取 Service Fabric。|
|/labs/users/serviceFabrics/schedules/delete|删除计划。|
|/labs/users/serviceFabrics/schedules/Execute/action|执行计划。|
|/labs/users/serviceFabrics/schedules/read|读取计划。|
|/labs/users/serviceFabrics/schedules/write|添加或修改计划。|
|/labs/users/serviceFabrics/Start/action|启动 Service Fabric。|
|/labs/users/serviceFabrics/Stop/action|停止 Service Fabric|
|/labs/users/serviceFabrics/write|添加或修改 Service Fabric。|
|/labs/users/write|添加或修改用户配置文件。|
|/labs/virtualMachines/AddDataDisk/action|将新的或现有的数据磁盘附加到虚拟机。|
|/labs/virtualMachines/ApplyArtifacts/action|将项目应用到虚拟机。|
|/labs/virtualMachines/Claim/action|获得现有虚拟机的所有权|
|/labs/virtualMachines/delete|删除虚拟机。|
|/labs/virtualMachines/DetachDataDisk/action|从虚拟机中分离指定的磁盘。|
|/labs/virtualMachines/ListApplicableSchedules/action|列出所有适用的计划|
|/labs/virtualMachines/read|读取虚拟机。|
|/labs/virtualMachines/Restart/action|重新启动虚拟机。|
|/labs/virtualMachines/schedules/delete|删除计划。|
|/labs/virtualMachines/schedules/Execute/action|执行计划。|
|/labs/virtualMachines/schedules/read|读取计划。|
|/labs/virtualMachines/schedules/write|添加或修改计划。|
|/labs/virtualMachines/Start/action|启动虚拟机。|
|/labs/virtualMachines/Stop/action|停止虚拟机|
|/labs/virtualMachines/TransferDisks/action|将虚拟机数据磁盘的所有权转让给自己|
|/labs/virtualMachines/UnClaim/action|释放现有虚拟机的所有权|
|/labs/virtualMachines/write|添加或修改虚拟机。|
|/labs/virtualNetworks/delete|创建虚拟网络。|
|/labs/virtualNetworks/read|读取虚拟网络。|
|/labs/virtualNetworks/write|添加或修改虚拟网络。|
|/labs/write|添加或修改实验室。|
|/locations/operations/read|读取操作。|
|/register/action|注册订阅|
|/schedules/delete|删除计划。|
|/schedules/Execute/action|执行计划。|
|/schedules/read|读取计划。|
|/schedules/Retarget/action|更新计划的目标资源 ID。|
|/schedules/write|添加或修改计划。|

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

| Operation | 说明 |
|---|---|
|/databaseAccountNames/read|检查名称可用性。|
|/databaseAccounts/changeResourceGroup/action|更改数据库帐户的资源组|
|/databaseAccounts/databases/collections/metricDefinitions/read|读取集合指标定义。|
|/databaseAccounts/databases/collections/metrics/read|读取集合指标。|
|/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read|读取数据库帐户分区密钥级别指标|
|/databaseAccounts/databases/collections/partitions/metrics/read|读取数据库帐户分区级别指标|
|/databaseAccounts/databases/collections/partitions/usages/read|读取数据库帐户分区级别使用情况|
|/databaseAccounts/databases/collections/usages/read|读取集合使用情况。|
|/databaseAccounts/databases/metricDefinitions/read|读取数据库指标定义|
|/databaseAccounts/databases/metrics/read|读取数据库指标。|
|/databaseAccounts/databases/usages/read|读取数据库使用情况。|
|/databaseAccounts/delete|删除数据库帐户。|
|/databaseAccounts/failoverPriorityChange/action|更改数据库帐户的区域故障转移优先级。 用于执行手动故障转移操作|
|/databaseAccounts/listConnectionStrings/action|获取数据库帐户的连接字符串|
|/databaseAccounts/listKeys/action|列出数据库帐户的密钥|
|/databaseAccounts/metricDefinitions/read|读取数据库帐户指标定义。|
|/databaseAccounts/metrics/read|读取数据库帐户指标。|
|/databaseAccounts/operationResults/read|读取异步操作的状态|
|/databaseAccounts/percentile/metrics/read|读取延迟指标|
|/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read|读取特定源和目标区域的延迟指标|
|/databaseAccounts/percentile/targetRegion/metrics/read|读取特定目标区域的延迟指标|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/read|获取资源的诊断设置|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新资源的诊断设置|
|/databaseAccounts/providers/Microsoft.Insights/logDefinitions/read|获取数据库帐户的可用日志类别|
|/databaseAccounts/providers/Microsoft.Insights/metricDefinitions/read|获取数据库帐户的可用指标|
|/databaseAccounts/read|读取数据库帐户。|
|/databaseAccounts/readonlykeys/action|读取数据库帐户只读密钥。|
|/databaseAccounts/readonlykeys/read|读取数据库帐户只读密钥。|
|/databaseAccounts/regenerateKey/action|轮替数据库帐户的密钥|
|/databaseAccounts/region/databases/collections/metrics/read|读取区域集合指标。|
|/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read|读取区域数据库帐户分区密钥级别指标|
|/databaseAccounts/region/databases/collections/partitions/metrics/read|读取区域数据库帐户分区级别指标|
|/databaseAccounts/region/databases/collections/partitions/read|读取集合中的数据库帐户分区|
|/databaseAccounts/region/metrics/read|读取区域和数据库帐户指标。|
|/databaseAccounts/usages/read|读取数据库帐户使用情况。|
|/databaseAccounts/write|更新数据库帐户。|
|/locations/deleteVirtualNetworkOrSubnets/action|向 Microsoft.DocumentDB 通知正在删除虚拟网络或子网|
|/operationResults/read|读取异步操作的状态|
|/operations/read|读取适用于 Microsoft DocumentDB 的操作 |
|/register/action| 注册订阅的 Microsoft DocumentDB 资源提供程序|

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

| Operation | 说明 |
|---|---|
|/checkDomainAvailability/Action|检查是否可购买某个域|
|/domains/Delete|删除现有域。|
|/domains/domainownershipidentifiers/Delete|删除所有权标识符|
|/domains/domainownershipidentifiers/Read|列出所有权标识符|
|/domains/domainownershipidentifiers/Read|获取所有权标识符|
|/domains/domainownershipidentifiers/Write|创建或更新标识符|
|/domains/operationresults/Read|获取域操作|
|/domains/operations/Read|列出应用服务域注册的所有操作|
|/domains/Read|获取域的列表|
|/domains/Read|获取域|
|/domains/renew/Action|续订现有域。|
|/domains/Write|添加新域，或更新现有域|
|/generateSsoRequest/Action|生成登录域控制中心的请求。|
|/listDomainRecommendations/Action|根据关键字检索列表域建议|
|/register/action|注册订阅的 Microsoft 域资源提供程序|
|/topLevelDomains/listAgreements/Action|列出协议操作|
|/topLevelDomains/Read|获取顶级域|
|/topLevelDomains/Read|获取顶级域|
|/validateDomainRegistrationInformation/Action|验证域购买对象但不提交该对象|

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

| Operation | 说明 |
|---|---|
|/lcsprojects/clouddeployments/read|显示属于用户的 Microsoft Dynamics Lifecycle Services 项目中的 Microsoft Dynamics AX 2012 R3 评估部署|
|/lcsprojects/clouddeployments/write|在属于用户的 Microsoft Dynamics Lifecycle Services 项目中创建 Microsoft Dynamics AX 2012 R3 评估部署。 可从 Azure 管理门户管理部署|
|/lcsprojects/connectors/read|读取属于 Microsoft Dynamics Lifecycle Services 项目的连接器|
|/lcsprojects/connectors/write|创建和更新属于 Microsoft Dynamics Lifecycle Services 项目的连接器|
|/lcsprojects/delete|删除属于用户的 Microsoft Dynamics Lifecycle Services 项目|
|/lcsprojects/read|显示属于用户的 Microsoft Dynamics Lifecycle Services 项目|
|/lcsprojects/write|创建和更新属于用户的 Microsoft Dynamics Lifecycle Services 项目。 只能更新名称和说明属性。 与项目关联的订阅和位置在创建后无法更新|

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

| Operation | 说明 |
|---|---|
|/eventSubscriptions/delete|删除 eventSubscription|
|/eventSubscriptions/getFullUrl/action|获取事件订阅的完整 URL|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read|获取事件订阅的诊断设置|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新事件订阅的诊断设置|
|/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read|获取 eventSubscriptions 的可用指标|
|/eventSubscriptions/read|读取 eventSubscription|
|/eventSubscriptions/write|创建或更新 eventSubscription|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read|获取主题的诊断设置|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新主题的诊断设置|
|/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read|获取主题的可用指标|
|/register/action|注册 EventGrid 资源提供程序的 eventSubscription，并启用事件网格订阅的创建。|
|/topics/delete|删除主题|
|/topics/listKeys/action|列出主题的密钥|
|/topics/providers/Microsoft.Insights/diagnosticSettings/read|获取主题的诊断设置|
|/topics/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新主题的诊断设置|
|/topics/providers/Microsoft.Insights/metricDefinitions/read|获取主题的可用指标|
|/topics/read|读取主题|
|/topics/regenerateKey/action|重新生成主题的密钥|
|/topics/write|创建或更新主题|

## <a name="microsofteventhub"></a>Microsoft.EventHub

| Operation | 说明 |
|---|---|
|/checkNameAvailability/action|检查给定订阅下的命名空间可用性。|
|/checkNamespaceAvailability/action|检查给定订阅下的命名空间可用性。 已弃用此 API，请改为使用 CheckNameAvailabiltiy。|
|/namespaces/authorizationRules/action|更新命名空间授权规则。 已弃用此 API。 请改为使用 PUT 调用来更新命名空间授权规则。 不支持对 API 版本 2017-04-01 执行此操作。|
|/namespaces/authorizationRules/delete|删除命名空间授权规则。 无法删除默认的命名空间授权规则。 |
|/namespaces/authorizationRules/listkeys/action|获取命名空间的连接字符串|
|/namespaces/authorizationRules/read|获取命名空间授权规则说明列表。|
|/namespaces/authorizationRules/regenerateKeys/action|再生成资源的主密钥或辅助密钥|
|/namespaces/authorizationRules/write|创建命名空间级授权规则并更新其属性。 可以更新授权规则访问权限、主密钥和辅助密钥。|
|/namespaces/Delete|删除命名空间资源|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|获取灾难恢复主命名空间的授权规则密钥|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|获取灾难恢复主命名空间的授权规则|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|禁用灾难恢复，并停止从主命名空间向辅助命名空间复制更改。|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|检查给定订阅下命名空间别名的可用性。|
|/namespaces/disasterRecoveryConfigs/delete|删除与命名空间关联的灾难恢复配置。 只能通过主命名空间调用此操作。|
|/namespaces/disasterRecoveryConfigs/failover/action|调用异地灾难恢复故障转移并将命名空间别名重新配置为指向辅助命名空间。|
|/namespaces/disasterRecoveryConfigs/read|获取与命名空间关联的灾难恢复配置。|
|/namespaces/disasterRecoveryConfigs/write|创建或更新与命名空间关联的灾难恢复配置。|
|/namespaces/eventhubs/authorizationRules/action|更新 EventHub 的操作。 不支持对 API 版本 2017-04-01 执行此操作。 授权规则。 请使用 PUT 调用来更新授权规则。|
|/namespaces/eventhubs/authorizationRules/delete|用于删除 EventHub 授权规则的操作|
|/namespaces/eventhubs/authorizationRules/listkeys/action|获取 EventHub 的连接字符串|
|/namespaces/eventhubs/authorizationRules/read| 获取 EventHub 授权规则列表|
|/namespaces/eventhubs/authorizationRules/regenerateKeys/action|再生成资源的主密钥或辅助密钥|
|/namespaces/eventhubs/authorizationRules/write|创建 EventHub 授权规则并更新其属性。 可以更新授权规则访问权限。|
|/namespaces/eventHubs/consumergroups/Delete|用于删除 ConsumerGroup 资源的操作|
|/namespaces/eventHubs/consumergroups/read|获取 ConsumerGroup 资源说明列表|
|/namespaces/eventHubs/consumergroups/write|创建或更新 ConsumerGroup 属性。|
|/namespaces/eventhubs/Delete|用于删除 EventHub 资源的操作|
|/namespaces/eventhubs/read|获取 EventHub 资源说明列表|
|/namespaces/eventhubs/write|创建或更新 EventHub 属性。|
|/namespaces/messagingPlan/read|获取命名空间的消息传递计划。 已弃用此 API。 在以后的 API 版本中，通过 MessagingPlan 资源公开的属性将移动到（父）命名空间资源。 不支持对 API 版本 2017-04-01 执行此操作。|
|/namespaces/messagingPlan/write|更新命名空间的消息传递计划。 已弃用此 API。 在以后的 API 版本中，通过 MessagingPlan 资源公开的属性将移动到（父）命名空间资源。 不支持对 API 版本 2017-04-01 执行此操作。|
|/namespaces/operationresults/read|获取命名空间操作的状态|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|获取命名空间诊断设置资源说明列表|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|获取命名空间诊断设置资源说明列表|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|获取命名空间日志资源说明列表|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|获取命名空间指标资源说明列表|
|/namespaces/read|获取命名空间资源说明列表|
|/namespaces/write|创建命名空间资源并更新其属性。 命名空间的标记和容量是可更新的属性。|
|/operations/read|获取操作|
|/register/action|注册 EventHub 资源提供程序的订阅，并启用 EventHub 资源的创建|
|/sku/read|获取 SKU 资源说明列表|
|/sku/regions/read|获取 SKU 区域资源说明列表|
|/unregister/action|注册 EventHub 资源提供程序|

## <a name="microsoftfeatures"></a>Microsoft.Features

| Operation | 说明 |
|---|---|
|/features/read|获取订阅的功能。|
|/providers/features/read|获取给定资源提供程序中某个订阅的功能。|
|/providers/features/register/action|在给定的资源提供程序中注册某个订阅的功能。|
|/providers/features/unregister/action|取消注册给定资源提供程序中的订阅的功能。|

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

| Operation | 说明 |
|---|---|
|/clusters/changerdpsetting/action|更改 HDInsight 群集的 RDP 设置|
|/clusters/configurations/action|更新 HDInsight 群集配置|
|/clusters/configurations/read|获取 HDInsight 群集配置|
|/clusters/delete|删除 HDInsight 群集|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/read|获取资源 HDInsight 群集的诊断设置|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新资源 HDInsight 群集的诊断设置|
|/clusters/providers/Microsoft.Insights/metricDefinitions/read|获取 HDInsight 群集的可用指标|
|/clusters/read|获取有关 HDInsight 群集的详细信息|
|/clusters/roles/resize/action|调整 HDInsight 群集的大小|
|/clusters/write|创建或更新 HDInsight 群集|
|/locations/capabilities/read|获取订阅功能|
|/locations/checkNameAvailability/read|检查名称可用性|

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

| Operation | 说明 |
|---|---|
|/jobs/delete|删除现有的作业。|
|/jobs/listBitLockerKeys/action|获取指定作业的 BitLocker 密钥。|
|/jobs/read|获取指定作业的属性，或返回作业列表。|
|/jobs/write|使用指定的参数创建作业，或更新指定作业的属性或标记。|
|/locations/read|获取指定位置的属性，或返回位置列表。|
|/register/action|注册导入/导出资源提供程序的订阅，并启用导入/导出作业的创建。|

## <a name="microsoftinsights"></a>Microsoft.Insights

| Operation | 说明 |
|---|---|
|/ActionGroups/Delete|删除操作组|
|/ActionGroups/Read|读取操作组|
|/ActionGroups/Write|写入操作组|
|/ActivityLogAlerts/Activated/Action|已触发活动日志警报|
|/ActivityLogAlerts/Delete|删除活动日志警报|
|/ActivityLogAlerts/Read|读取活动日志警报|
|/ActivityLogAlerts/Write|读取活动日志警报|
|/AlertRules/Activated/Action|已激活警报规则|
|/AlertRules/Delete|删除警报规则配置|
|/AlertRules/Incidents/Read|读取警报规则事件配置|
|/AlertRules/Read|读取警报规则配置|
|/AlertRules/Resolved/Action|已解决警报规则|
|/AlertRules/Throttled/Action|已限制警报规则|
|/AlertRules/Write|写入警报规则配置|
|/AutoscaleSettings/Delete|删除自动缩放设置配置|
|/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read|读取指标定义|
|/AutoscaleSettings/Read|读取自动缩放设置配置|
|/AutoscaleSettings/Scaledown/Action|自动缩放缩减操作|
|/AutoscaleSettings/Scaleup/Action|自动缩放扩展操作|
|/AutoscaleSettings/Write|写入自动缩放设置配置|
|/Components/AnalyticsItems/Delete|添加 Application Insights 分析项|
|/Components/AnalyticsItems/Read|读取 Application Insights 分析项|
|/Components/AnalyticsItems/Write|写入 Application Insights 分析项|
|/Components/AnalyticsTables/Action|Application Insights 分析表操作|
|/Components/AnalyticsTables/Delete|删除 Application Insights 分析表架构|
|/Components/AnalyticsTables/Read|读取 Application Insights 分析表架构|
|/Components/AnalyticsTables/Write|写入 Application Insights 分析表架构|
|/Components/Annotations/Delete|删除 Application Insights 注释|
|/Components/Annotations/Read|读取 Application Insights 注释|
|/Components/Annotations/Write|写入 Application Insights 注释|
|/Components/Api/Read|读取 Application Insights 组件数据 API|
|/Components/ApiKeys/Action|生成 Application Insights API 密钥|
|/Components/ApiKeys/Delete|删除 Application Insights API 密钥|
|/Components/ApiKeys/Read|读取 Application Insights API 密钥|
|/Components/BillingPlanForComponent/Read|读取 Application Insights 组件的计费计划|
|/Components/CurrentBillingFeatures/Read|读取 Application Insights 组件的当前计费计划|
|/Components/CurrentBillingFeatures/Write|写入 Application Insights 组件的当前计费计划|
|/Components/DefaultWorkItemConfig/Read|读取 Application Insights 默认 ALM 集成配置|
|/Components/Delete|删除 Application Insights 组件配置|
|/Components/ExportConfiguration/Action|Application Insights 导出设置操作|
|/Components/ExportConfiguration/Delete|删除 Application Insights 导出设置|
|/Components/ExportConfiguration/Read|读取 Application Insights 导出设置|
|/Components/ExportConfiguration/Write|写入 Application Insights 导出设置|
|/Components/ExtendQueries/Read|读取 Application Insights 组件扩展查询结果|
|/Components/Favorites/Delete|删除 Application Insights 收藏夹|
|/Components/Favorites/Read|读取 Application Insights 收藏夹|
|/Components/Favorites/Write|写入 Application Insights 收藏夹|
|/Components/FeatureCapabilities/Read|读取 Application Insights 组件功能|
|/Components/GetAvailableBillingFeatures/Read|读取 Application Insights 组件可用计费功能|
|/Components/GetToken/Read|读取 Application Insights 组件令牌|
|/Components/ListMigrationDate/Action|返回订阅迁移日期|
|/Components/ListMigrationDate/Read|返回订阅迁移日期|
|/Components/MetricDefinitions/Read|读取 Application Insights 组件指标定义|
|/Components/Metrics/Read|读取 Application Insights 组件指标|
|/Components/MigrateToNewpricingModel/Action|将订阅迁移到新的定价模型|
|/Components/Move/Action|将 Application Insights 组件移动到另一个资源组或订阅|
|/Components/MyAnalyticsItems/Delete|删除 Application Insights 个人分析项|
|/Components/MyAnalyticsItems/Read|读取 Application Insights 个人分析项|
|/Components/MyAnalyticsItems/Write|写入 Application Insights 个人分析项|
|/Components/MyFavorites/Read|读取 Application Insights 个人收藏夹|
|/Components/PricingPlans/Read|读取 Application Insights 组件定价计划|
|/Components/PricingPlans/Write|写入 Application Insights 组件定价计划|
|/Components/ProactiveDetectionConfigs/Read|读取 Application Insights 主动检测配置|
|/Components/ProactiveDetectionConfigs/Write|写入 Application Insights 主动检测配置|
|/Components/providers/Microsoft.Insights/MetricDefinitions/Read|读取指标定义|
|/Components/QuotaStatus/Read|读取 Application Insights 组件配额状态|
|/Components/Read|读取 Application Insights 组件配置|
|/Components/RollbackToLegacyPricingModel/Action|将订阅回滚到旧定价模型|
|/Components/SyntheticMonitorLocations/Read|读取 Application Insights webtest 位置|
|/Components/WorkItemConfigs/Delete|删除 Application Insights ALM 集成配置|
|/Components/WorkItemConfigs/Read|读取 Application Insights ALM 集成配置|
|/Components/WorkItemConfigs/Write|写入 Application Insights ALM 集成配置|
|/Components/Write|写入到 Application Insights 组件配置|
|/DiagnosticSettings/Delete|删除诊断设置配置|
|/DiagnosticSettings/Read|读取诊断设置配置|
|/DiagnosticSettings/Write|写入诊断设置配置|
|/EventCategories/Read|读取事件类别|
|/eventtypes/digestevents/Read|读取管理事件类型摘要|
|/eventtypes/values/Read|读取管理事件类型值|
|/ExtendedDiagnosticSettings/Delete|删除扩展的诊断设置配置|
|/ExtendedDiagnosticSettings/Read|读取扩展的诊断设置配置|
|/ExtendedDiagnosticSettings/Write|写入扩展的诊断设置配置|
|/LogDefinitions/Read|读取日志定义|
|/LogProfiles/Delete|删除日志配置文件配置|
|/LogProfiles/Read|读取日志配置文件|
|/LogProfiles/Write|写入日志配置文件配置|
|/MetricAlerts/Delete|删除指标警报|
|/MetricAlerts/Read|读取指标警报|
|/MetricAlerts/Write|写入指标警报|
|/MetricDefinitions/Microsoft.Insights/Read|读取指标定义|
|/MetricDefinitions/providers/Microsoft.Insights/Read|读取指标定义|
|/MetricDefinitions/Read|读取指标定义|
|/Metrics/providers/Metrics/Read|添加指标|
|/Metrics/Read|添加指标|
|/Metrics/Write|写入指标|
|/Operations/Read|读取操作|
|/Register/Action|注册 Microsoft.Insights 提供程序|
|/Tenants/Register/Action|初始化 Microsoft Insights 提供程序|
|/Unregister/Action|注册 Microsoft.Insights 提供程序|
|/Webtests/Delete|删除 webtest 配置|
|/Webtests/GetToken/Read|读取 webtest 令牌|
|/Webtests/MetricDefinitions/Read|读取 webtest 指标定义|
|/Webtests/Metrics/Read|读取 webtest 指标|
|/Webtests/Read|读取 webtest 配置|
|/Webtests/Write|写入到 webtest 配置|

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

| Operation | 说明 |
|---|---|
|/checkNameAvailability/read|检查 Key Vault 名称是否有效且未被使用|
|/deletedVaults/read|查看软删除的 Key Vault 的属性|
|/hsmPools/delete|删除 HSM 池|
|/hsmPools/joinVault/action|将密钥保管库加入 HSM 池|
|/hsmPools/read|查看 HSM 池的属性|
|/hsmPools/write|创建新 HSM 池或更新现有 HSM 池的属性|
|/locations/deletedVaults/purge/action|清除软删除的 Key Vault|
|/locations/deletedVaults/read|查看软删除的 Key Vault 的属性|
|/locations/deleteVirtualNetworkOrSubnets/action|通知 Microsoft.KeyVault 正在删除虚拟网络或子网|
|/locations/operationResults/read|检查长时间运行的操作的结果|
|/operations/read|列出可对 Microsoft.KeyVault 资源提供程序执行的操作|
|/register/action|注册订阅|
|/unregister/action|取消注册订阅|
|/vaults/accessPolicies/write|通过合并或替换操作更新现有的访问策略，或者将新的访问策略添加到保管库。|
|/vaults/delete|删除 Key Vault|
|/vaults/deploy/action|部署 Azure 资源时启用对 Key Vault 中的机密的访问|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Read|获取资源的诊断设置|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Write|创建或更新资源的诊断设置|
|/vaults/providers/Microsoft.Insights/logDefinitions/read|获取密钥保管库的可用日志|
|/vaults/providers/Microsoft.Insights/metricDefinitions/read|获取密钥保管库的可用指标|
|/vaults/read|查看 Key Vault 的属性|
|/vaults/secrets/read|查看机密的属性，但不查看其值|
|/vaults/secrets/write|创建新的机密，或更新现有机密的值|
|/vaults/write|创建新的 Key Vault，或更新现有 Key Vault 的属性|

## <a name="microsoftlabservices"></a>Microsoft.LabServices

| Operation | 说明 |
|---|---|
|/labAccounts/CreateLab/action|在实验室帐户中创建实验室。|
|/labAccounts/delete|删除实验室帐户。|
|/labAccounts/labs/delete|删除实验室。|
|/labAccounts/labs/environmentSettings/delete|删除环境设置。|
|/labAccounts/labs/environmentSettings/environments/delete|删除环境。|
|/labAccounts/labs/environmentSettings/environments/read|读取环境。|
|/labAccounts/labs/environmentSettings/environments/write|添加或修改环境。|
|/labAccounts/labs/environmentSettings/Publish/action|基于实验室/环境设置的当前状态设置/取消设置环境设置的所需资源。|
|/labAccounts/labs/environmentSettings/read|读取环境设置。|
|/labAccounts/labs/environmentSettings/write|添加或修改环境设置。|
|/labAccounts/labs/read|读取实验室。|
|/labAccounts/labs/users/delete|删除用户。|
|/labAccounts/labs/users/read|读取用户。|
|/labAccounts/labs/users/write|添加或修改用户。|
|/labAccounts/labs/write|添加或修改实验室。|
|/labAccounts/read|读取实验室帐户。|
|/labAccounts/write|添加或修改实验室帐户。|
|/locations/operations/read|读取操作。|
|/register/action|注册订阅|

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

| Operation | 说明 |
|---|---|
|/accounts/delete|删除 Location Based Services 帐户。|
|/accounts/listKeys/action|列出 Location Based Services 帐户密钥|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|获取资源的诊断设置|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新资源的诊断设置|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|获取 Location Based Services 帐户的可用指标|
|/accounts/read|获取 Location Based Services 帐户。|
|/accounts/regenerateKey/action|生成新的 Location Based Services 帐户主要或辅助密钥|
|/accounts/write|创建或更新 Location Based Services 帐户。|
|/register/action|注册提供程序|

## <a name="microsoftlogic"></a>Microsoft.Logic

| Operation | 说明 |
|---|---|
|/integrationAccounts/agreements/delete|删除集成帐户中的协议。|
|/integrationAccounts/agreements/listContentCallbackUrl/action|获取集成帐户中的协议内容的回调 URL。|
|/integrationAccounts/agreements/read|读取集成帐户中的协议。|
|/integrationAccounts/agreements/write|创建或更新集成帐户中的协议。|
|/integrationAccounts/assemblies/delete|删除集成帐户中的程序集。|
|/integrationAccounts/assemblies/listContentCallbackUrl/action|获取集成帐户中的程序集内容的回调 URL。|
|/integrationAccounts/assemblies/read|读取集成帐户中的程序集。|
|/integrationAccounts/assemblies/write|创建或更新集成帐户中的程序集。|
|/integrationAccounts/batchConfigurations/delete|删除集成帐户中的批配置。|
|/integrationAccounts/batchConfigurations/read|读取集成帐户中的批配置。|
|/integrationAccounts/batchConfigurations/write|创建或更新集成帐户中的批配置。|
|/integrationAccounts/certificates/delete|删除集成帐户中的证书。|
|/integrationAccounts/certificates/read|读取集成帐户中的证书。|
|/integrationAccounts/certificates/write|创建或更新集成帐户中的证书。|
|/integrationAccounts/delete|删除集成帐户。|
|/integrationAccounts/listCallbackUrl/action|获取集成帐户的回调 URL。|
|/integrationAccounts/listKeyVaultKeys/action|获取密钥保管库中的密钥。|
|/integrationAccounts/logTrackingEvents/action|记录集成帐户中的跟踪事件。|
|/integrationAccounts/maps/delete|删除集成帐户中的映射。|
|/integrationAccounts/maps/listContentCallbackUrl/action|获取集成帐户中的映射内容的回调 URL。|
|/integrationAccounts/maps/read|读取集成帐户中的映射。|
|/integrationAccounts/maps/write|创建或更新集成帐户中的映射。|
|/integrationAccounts/partners/delete|删除集成帐户中的合作伙伴。|
|/integrationAccounts/partners/listContentCallbackUrl/action|获取集成帐户中的合作伙伴内容的回调 URL。|
|/integrationAccounts/partners/read|读取集成帐户中的合作伙伴。|
|/integrationAccounts/partners/write|创建或更新集成帐户中的合作伙伴。|
|/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read|读取集成帐户日志定义。|
|/integrationAccounts/read|读取集成帐户。|
|/integrationAccounts/regenerateAccessKey/action|再生成访问密钥机密。|
|/integrationAccounts/schemas/delete|删除集成帐户中的架构。|
|/integrationAccounts/schemas/listContentCallbackUrl/action|获取集成帐户中的架构内容的回调 URL。|
|/integrationAccounts/schemas/read|读取集成帐户中的架构。|
|/integrationAccounts/schemas/write|创建或更新集成帐户中的架构。|
|/integrationAccounts/sessions/delete|删除集成帐户中的会话。|
|/integrationAccounts/sessions/read|读取集成帐户中的批配置。|
|/integrationAccounts/sessions/write|创建或更新集成帐户中的会话。|
|/integrationAccounts/write|创建或更新集成帐户。|
|/locations/workflows/validate/action|验证工作流。|
|/operations/read|获取操作。|
|/register/action|为给定的订阅注册 Microsoft.Logic 资源提供程序。|
|/workflows/accessKeys/delete|删除访问密钥。|
|/workflows/accessKeys/list/action|列出访问密钥机密。|
|/workflows/accessKeys/read|读取访问密钥。|
|/workflows/accessKeys/regenerate/action|再生成访问密钥机密。|
|/workflows/accessKeys/write|创建或更新访问密钥。|
|/workflows/delete|删除工作流。|
|/workflows/disable/action|禁用工作流。|
|/workflows/enable/action|启用工作流。|
|/workflows/listCallbackUrl/action|获取工作流的回调 URL。|
|/workflows/listSwagger/action|获取工作流 Swagger 定义。|
|/workflows/move/action|将工作流从其现有订阅 ID、资源组和/或名称移到其他订阅 ID、资源组和/或名称。|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/read|读取工作流诊断设置。|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新工作流诊断设置。|
|/workflows/providers/Microsoft.Insights/logDefinitions/read|读取工作流日志定义。|
|/workflows/providers/Microsoft.Insights/metricDefinitions/read|读取工作流指标定义。|
|/workflows/read|读取工作流。|
|/workflows/regenerateAccessKey/action|再生成访问密钥机密。|
|/workflows/run/action|启动工作流的运行。|
|/workflows/runs/actions/listExpressionTraces/action|获取工作流运行操作表达式跟踪。|
|/workflows/runs/actions/read|读取工作流运行操作。|
|/workflows/runs/actions/repetitions/listExpressionTraces/action|获取工作流运行操作重复表达式跟踪。|
|/workflows/runs/actions/repetitions/read|读取工作流运行操作重复。|
|/workflows/runs/actions/scoperepetitions/read|读取工作流运行操作范围重复。|
|/workflows/runs/cancel/action|取消工作流的运行。|
|/workflows/runs/operations/read|读取工作流运行操作状态。|
|/workflows/runs/read|读取工作流运行。|
|/workflows/suspend/action|暂停工作流。|
|/workflows/triggers/histories/read|读取触发器历史记录。|
|/workflows/triggers/histories/resubmit/action|重新提交工作流触发器。|
|/workflows/triggers/listCallbackUrl/action|获取触发器的回调 URL。|
|/workflows/triggers/read|读取触发器。|
|/workflows/triggers/reset/action|重置触发器。|
|/workflows/triggers/run/action|执行触发器。|
|/workflows/triggers/setState/action|设置触发器状态。|
|/workflows/validate/action|验证工作流。|
|/workflows/versions/read|读取工作流版本。|
|/workflows/versions/triggers/listCallbackUrl/action|获取触发器的回调 URL。|
|/workflows/write|创建或更新工作流。|

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

| Operation | 说明 |
|---|---|
|/commitmentPlans/commitmentAssociations/move/action|移动任何机器学习承诺计划关联|
|/commitmentPlans/commitmentAssociations/read|读取任何机器学习承诺计划关联|
|/commitmentPlans/delete|删除任何机器学习承诺计划|
|/commitmentPlans/join/action|加入任何机器学习承诺计划|
|/commitmentPlans/read|读取任何机器学习承诺计划|
|/commitmentPlans/write|创建或更新任何机器学习承诺计划|
|/locations/operationresults/read|获取机器学习操作的结果|
|/locations/operationsstatus/read|获取正在进行的机器学习操作的状态|
|/operations/read|获取机器学习操作|
|/register/action|注册机器学习 Web 服务资源提供程序的订阅，并启用 Web 服务的创建。|
|/skus/read|获取机器学习承诺计划 SKU|
|/webServices/action|为受支持的区域创建区域 Web 服务属性|
|/webServices/delete|删除任何机器学习 Web 服务|
|/webServices/read|读取任何机器学习 Web 服务|
|/webServices/write|创建或更新任何机器学习 Web 服务|
|/Workspaces/delete|删除任何机器学习工作区|
|/Workspaces/listworkspacekeys/action|列出机器学习工作区的密钥|
|/Workspaces/read|读取任何机器学习工作区|
|/Workspaces/resyncstoragekeys/action|重新同步针对机器学习工作区配置的存储帐户密钥|
|/Workspaces/write|创建或更新任何机器学习工作区|

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

| Operation | 说明 |
|---|---|
|/operationalizationClusters/checkUpdate/action|检查操作群集的系统服务是否有可用更新|
|/operationalizationClusters/delete|删除任何托管帐户|
|/operationalizationClusters/listKeys/action|列出与操作群集关联的密钥|
|/operationalizationClusters/read|读取任何托管帐户|
|/operationalizationClusters/updateSystem/action|更新操作群集中的系统服务|
|/operationalizationClusters/write|创建或更新任何托管帐户|
|/register/action|将订阅 ID 注册到资源提供程序，并启用机器学习计算资源的创建|

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

| Operation | 说明 |
|---|---|
|/accounts/delete|删除任何托管帐户|
|/accounts/read|读取任何托管帐户|
|/accounts/write|创建或更新任何托管帐户|
|/register/action|将订阅 ID 注册到资源提供程序，并启用托管帐户的创建|

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

| Operation | 说明 |
|---|---|
|/userAssignedIdentities/assign/action|RBAC 操作用于将现有用户分配标识分配给资源|
|/userAssignedIdentities/delete|删除现有用户分配标识|
|/userAssignedIdentities/read|获取现有用户分配标识|
|/userAssignedIdentities/write|创建新的用户分配标识或更新与现有用户分配标识关联的标记|

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

| Operation | 说明 |
|---|---|
|/labAccounts/CreateLab/action|在实验室帐户中创建实验室。|
|/labAccounts/delete|删除实验室帐户。|
|/labAccounts/labs/delete|删除实验室。|
|/labAccounts/labs/environmentSettings/delete|删除环境设置。|
|/labAccounts/labs/environmentSettings/environments/delete|删除环境。|
|/labAccounts/labs/environmentSettings/environments/read|读取环境。|
|/labAccounts/labs/environmentSettings/environments/write|添加或修改环境。|
|/labAccounts/labs/environmentSettings/read|读取环境设置。|
|/labAccounts/labs/environmentSettings/write|添加或修改环境设置。|
|/labAccounts/labs/labVms/delete|删除实验室虚拟机。|
|/labAccounts/labs/labVms/read|读取实验室虚拟机。|
|/labAccounts/labs/labVms/write|添加或修改实验室虚拟机。|
|/labAccounts/labs/read|读取实验室。|
|/labAccounts/labs/write|添加或修改实验室。|
|/labAccounts/read|读取实验室帐户。|
|/labAccounts/write|添加或修改实验室帐户。|
|/locations/operations/read|读取操作。|
|/register/action|注册订阅|

## <a name="microsoftmanagement"></a>Microsoft.Management

| Operation | 说明 |
|---|---|
|/checkNameAvailability/action|检查指定的管理组名称是否有效且唯一。|
|/getEntities/action|列出已通过身份验证的用户的所有实体（管理组、订阅等）。|
|/managementGroups/delete|删除管理组。|
|/managementGroups/read|列出已通过身份验证的用户的管理组。|
|/managementGroups/subscriptions/delete|从管理组取消关联订阅。|
|/managementGroups/subscriptions/write|将现有订阅与管理组关联。|
|/managementGroups/write|创建或更新管理组。|

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

| Operation | 说明 |
|---|---|
|/ClassicDevServices/delete|对经典开发人员服务资源执行 DELETE 操作。|
|/ClassicDevServices/listSecrets/action|获取经典开发人员服务资源管理密钥。|
|/ClassicDevServices/listSingleSignOnToken/action|获取经典开发人员服务的单一登录 URL。|
|/ClassicDevServices/read|对经典开发人员服务执行 GET 操作。|
|/ClassicDevServices/regenerateKey/action|生成经典开发人员服务资源管理密钥。|
|/Operations/read|读取所有资源类型的操作。|

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

| Operation | 说明 |
|---|---|
|/agreements/offers/plans/cancel/action|取消给定应用商店项的协议|
|/agreements/offers/plans/read|返回给定应用商店项的协议|
|/agreements/offers/plans/sign/action|为给定应用商店项的协议签名|
|/agreements/read|返回给定订阅下的所有协议|
|/offertypes/publishers/offers/plans/agreements/read|获取给定商城虚拟机项的协议|
|/offertypes/publishers/offers/plans/agreements/write|签订或取消给定商城虚拟机项的协议|

## <a name="microsoftmedia"></a>Microsoft.Media

| Operation | 说明 |
|---|---|
|/checknameavailability/action|检查媒体服务帐户名称是否可用|
|/mediaservices/delete|删除任何媒体服务帐户|
|/mediaservices/listKeys/action|列出媒体服务帐户的 ACS 密钥|
|/mediaservices/read|读取任何媒体服务帐户|
|/mediaservices/regenerateKey/action|生成媒体服务 ACS 密钥|
|/mediaservices/syncStorageKeys/action|同步附加的 Azure 存储帐户的存储密钥|
|/mediaservices/write|创建或更新任何媒体服务帐户|
|/operations/read|读取任何媒体服务帐户|
|/register/action|注册媒体服务资源提供程序的订阅，并启用媒体服务帐户的创建|

## <a name="microsoftmigrate"></a>Microsoft.Migrate

| Operation | 说明 |
|---|---|
|/Operations/read|读取公开的操作|

## <a name="microsoftnetwork"></a>Microsoft.Network

| Operation | 说明 |
|---|---|
|/applicationGatewayAvailableSslOptions/predefinedPolicies/read|应用程序网关 SSL 预定义策略|
|/applicationGatewayAvailableSslOptions/read|应用程序网关可用 SSL 选项|
|/applicationGatewayAvailableWafRuleSets/read|获取应用程序网关可用的 WAF 规则集|
|/applicationGateways/backendAddressPools/join/action|加入应用程序网关后端地址池|
|/applicationGateways/backendhealth/action|获取应用程序网关后端运行状况|
|/applicationGateways/delete|删除应用程序网关|
|/applicationGateways/effectiveNetworkSecurityGroups/action|获取应用程序网关上配置的路由表|
|/applicationGateways/effectiveRouteTable/action|获取应用程序网关上配置的路由表|
|/applicationGateways/providers/Microsoft.Insights/logDefinitions/read|获取应用程序网关的事件|
|/applicationGateways/providers/Microsoft.Insights/metricDefinitions/read|获取应用程序网关的可用指标|
|/applicationGateways/read|获取应用程序网关|
|/applicationGateways/setSecurityCenterConfiguration/action|设置应用程序网关安全中心配置|
|/applicationGateways/start/action|启动应用程序网关|
|/applicationGateways/stop/action|停止应用程序网关|
|/applicationGateways/write|创建应用程序网关，或更新应用程序网关|
|/applicationSecurityGroups/delete|删除应用程序安全组|
|/applicationSecurityGroups/joinIpConfiguration/action|将 IP 配置加入到应用程序安全组中。|
|/applicationSecurityGroups/joinNetworkSecurityRule/action|将安全规则加入到应用程序安全组中。|
|/applicationSecurityGroups/read|获取应用程序安全组 ID。|
|/applicationSecurityGroups/write|创建应用程序安全组，或更新现有应用程序安全组。|
|/bgpServiceCommunities/read|获取 BGP 服务社区|
|/checkTrafficManagerNameAvailability/action|检查流量管理器相对 DNS 名称的可用性。|
|/connections/delete|删除 VirtualNetworkGatewayConnection|
|/connections/read|获取 VirtualNetworkGatewayConnection|
|/connections/sharedkey/action|获取 VirtualNetworkGatewayConnection SharedKey|
|/connections/sharedKey/read|获取 VirtualNetworkGatewayConnection SharedKey|
|/connections/sharedKey/write|创建新的或更新现有的 VirtualNetworkGatewayConnection SharedKey|
|/connections/vpndeviceconfigurationscript/read|获取 VirtualNetworkGatewayConnection 的 VPN 设备配置|
|/connections/write|创建新的或更新现有的 VirtualNetworkGatewayConnection|
|/ddosProtectionPlans/ddosProtectionPlanProxies/delete|删除 DDoS 保护计划代理|
|/ddosProtectionPlans/ddosProtectionPlanProxies/read|获取 DDoS 保护计划代理定义|
|/ddosProtectionPlans/ddosProtectionPlanProxies/write|创建 DDoS 保护计划代理或更新现有 DDoS 保护计划代理|
|/ddosProtectionPlans/delete|删除 DDoS 保护计划|
|/ddosProtectionPlans/join/action|加入 DDoS 保护计划|
|/ddosProtectionPlans/read|获取 DDoS 保护计划|
|/ddosProtectionPlans/write|创建 DDoS 保护计划或更新 DDoS 保护计划 |
|/dnsoperationresults/read|获取 DNS 操作的结果|
|/dnsoperationstatuses/read|获取 DNS 操作的状态 |
|/dnszones/A/delete|从 DNS 区域中删除具有给定名称的、类型为“A”的记录集。|
|/dnszones/A/read|获取 JSON 格式的、“A”类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。|
|/dnszones/A/write|在 DNS 区域中创建或更新“A”类型的记录集。 指定的记录将替换记录集中的当前记录。|
|/dnszones/AAAA/delete|从 DNS 区域中删除具有给定名称的、类型为“AAAA”的记录集。|
|/dnszones/AAAA/read|获取 JSON 格式的、“AAAA”类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。|
|/dnszones/AAAA/write|在 DNS 区域中创建或更新“AAAA”类型的记录集。 指定的记录将替换记录集中的当前记录。|
|/dnszones/all/read|获取各种类型的 DNS 记录集|
|/dnszones/CAA/delete|从 DNS 区域中删除具有给定名称的、类型为“CAA”的记录集。|
|/dnszones/CAA/read|获取 JSON 格式的、“CAA”类型的记录集。 记录集包含 TTL、标记和 etag。|
|/dnszones/CAA/write|在 DNS 区域中创建或更新“CAA”类型的记录集。 指定的记录将替换记录集中的当前记录。|
|/dnszones/CNAME/delete|从 DNS 区域中删除具有给定名称的、类型为“CNAME”的记录集。|
|/dnszones/CNAME/read|获取 JSON 格式的、“CNAME”类型的记录集。 记录集包含 TTL、标记和 etag。|
|/dnszones/CNAME/write|在 DNS 区域中创建或更新“CNAME”类型的记录集。 指定的记录将替换记录集中的当前记录。|
|/dnszones/delete|删除 JSON 格式的 DNS 区域。 区域属性包括 tags、etag、numberOfRecordSets 和 maxNumberOfRecordSets。|
|/dnszones/MX/delete|从 DNS 区域中删除具有给定名称的、类型为“MX”的记录集。|
|/dnszones/MX/read|获取 JSON 格式的、“MX”类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。|
|/dnszones/MX/write|在 DNS 区域中创建或更新“MX”类型的记录集。 指定的记录将替换记录集中的当前记录。|
|/dnszones/NS/delete|删除 NS 类型的 DNS 记录集|
|/dnszones/NS/read|获取 NS 类型的 DNS 记录集|
|/dnszones/NS/write|创建或更新 NS 类型的 DNS 记录集|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/read|获取 DNS 区域诊断设置|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新 DNS 区域诊断设置|
|/dnszones/providers/Microsoft.Insights/metricDefinitions/read|获取 DNS 区域指标定义|
|/dnszones/PTR/delete|从 DNS 区域中删除具有给定名称的、类型为“PTR”的记录集。|
|/dnszones/PTR/read|获取 JSON 格式的、“PTR”类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。|
|/dnszones/PTR/write|在 DNS 区域中创建或更新“PTR”类型的记录集。 指定的记录将替换记录集中的当前记录。|
|/dnszones/read|获取 JSON 格式的 DNS 区域。 区域属性包括 tags、etag、numberOfRecordSets 和 maxNumberOfRecordSets。 请注意，此命令不会检索区域中包含的记录集。|
|/dnszones/recordsets/read|获取各种类型的 DNS 记录集|
|/dnszones/SOA/read|获取 SOA 类型的 DNS 记录集|
|/dnszones/SOA/write|创建或更新 SOA 类型的 DNS 记录集|
|/dnszones/SRV/delete|从 DNS 区域中删除具有给定名称的、类型为“SRV”的记录集。|
|/dnszones/SRV/read|获取 JSON 格式的、“SRV”类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。|
|/dnszones/SRV/write|创建或更新 SRV 类型的记录集|
|/dnszones/TXT/delete|从 DNS 区域中删除具有给定名称的、类型为“TXT”的记录集。|
|/dnszones/TXT/read|获取 JSON 格式的、“TXT”类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。|
|/dnszones/TXT/write|在 DNS 区域中创建或更新“TXT”类型的记录集。 指定的记录将替换记录集中的当前记录。|
|/dnszones/write|在资源组中创建或更新 DNS 区域。  用于更新 DNS 区域资源上的标记。 请注意，无法使用此命令在区域中创建或更新记录集。|
|/expressRouteCircuits/authorizations/delete|删除 ExpressRouteCircuit 授权|
|/expressRouteCircuits/authorizations/read|获取 ExpressRouteCircuit 授权|
|/expressRouteCircuits/authorizations/write|创建新的或更新现有的 ExpressRouteCircuit 授权|
|/expressRouteCircuits/delete|删除 ExpressRouteCircuit|
|/expressRouteCircuits/peerings/arpTables/action|获取 ExpressRouteCircuit 对等互连 ArpTable|
|/expressRouteCircuits/peerings/connections/delete|删除 ExpressRouteCircuit 连接|
|/expressRouteCircuits/peerings/connections/read|获取 ExpressRouteCircuit 连接|
|/expressRouteCircuits/peerings/connections/write|创建或更新现有 ExpressRouteCircuit 连接资源|
|/expressRouteCircuits/peerings/delete|删除 ExpressRouteCircuit 对等互连|
|/expressRouteCircuits/peerings/read|获取 ExpressRouteCircuit 对等互连|
|/expressRouteCircuits/peerings/routeTables/action|获取 ExpressRouteCircuit 对等互连 RouteTable|
|/expressRouteCircuits/peerings/routeTablesSummary/action|获取 ExpressRouteCircuit 对等互连 RouteTable 摘要|
|/expressRouteCircuits/peerings/stats/read|获取 ExpressRouteCircuit 对等互连统计信息|
|/expressRouteCircuits/peerings/write|创建新的或更新现有的 ExpressRouteCircuit 对等互连|
|/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/read|获取 ExpressRoute 线路的诊断设置|
|/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新 ExpressRoute 线路的诊断设置|
|/expressRouteCircuits/providers/Microsoft.Insights/logDefinitions/read|获取 ExpressRoute 线路的事件|
|/expressRouteCircuits/providers/Microsoft.Insights/metricDefinitions/read|获取 ExpressRoute 线路的指标定义|
|/expressRouteCircuits/read|获取 ExpressRouteCircuit|
|/expressRouteCircuits/stats/read|获取 ExpressRouteCircuit 统计信息|
|/expressRouteCircuits/write|创建新的或更新现有的 ExpressRouteCircuit|
|/expressRouteCrossConnections/delete|删除快速路由交叉连接|
|/expressRouteCrossConnections/join/action|加入快速路由交叉连接|
|/expressRouteCrossConnections/peerings/arpTables/action|获取快速路由交叉对等互连 ARP 表|
|/expressRouteCrossConnections/peerings/delete|删除快速路由交叉对等互连|
|/expressRouteCrossConnections/peerings/read|获取快速路由交叉对等互连|
|/expressRouteCrossConnections/peerings/routeTables/action|获取快速路由交叉对等互连路由表|
|/expressRouteCrossConnections/peerings/routeTableSummary/action|获取快速路由交叉对等互连路由表摘要|
|/expressRouteCrossConnections/peerings/stats/read|获取快速路由交叉对等互连统计信息|
|/expressRouteCrossConnections/peerings/write|创建快速路由交叉对等互连，或更新现有快速路由交叉对等互连|
|/expressRouteCrossConnections/read|获取快速路由交叉连接|
|/expressRouteCrossConnections/write|创建或更新快速路由交叉连接|
|/expressRouteServiceProviders/read|获取 Express Route 服务提供商|
|/loadBalancers/backendAddressPools/join/action|加入负载均衡器后端地址池|
|/loadBalancers/backendAddressPools/read|获取负载均衡器的后端地址池定义|
|/loadBalancers/delete|删除负载均衡器|
|/loadBalancers/frontendIPConfigurations/read|获取负载均衡器的前端 IP 配置定义|
|/loadBalancers/inboundNatPools/join/action|加入负载均衡器入站 NAT 池|
|/loadBalancers/inboundNatPools/read|获取负载均衡器的入站 NAT 池定义|
|/loadBalancers/inboundNatRules/delete|删除负载均衡器入站 NAT 规则|
|/loadBalancers/inboundNatRules/join/action|加入负载均衡器入站 NAT 规则|
|/loadBalancers/inboundNatRules/read|获取负载均衡器的入站 NAT 规则定义|
|/loadBalancers/inboundNatRules/write|创建负载均衡器入站 NAT 规则，或更新现有的负载均衡器入站 NAT 规则|
|/loadBalancers/loadBalancingRules/read|获取负载均衡器的负载均衡规则定义|
|/loadBalancers/networkInterfaces/read|获取对负载均衡器下的所有网络接口的引用|
|/loadBalancers/outboundNatRules/read|获取负载均衡器的出站 NAT 规则定义|
|/loadBalancers/probes/join/action|允许使用负载均衡器的探测。 例如，使用此权限，VM 规模集的 healthProbe 属性可以引用探测。|
|/loadBalancers/probes/read|获取负载均衡器探测|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/read|获取负载均衡器诊断设置|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新负载均衡器诊断设置|
|/loadBalancers/providers/Microsoft.Insights/logDefinitions/read|获取负载均衡器的事件|
|/loadBalancers/providers/Microsoft.Insights/metricDefinitions/read|获取负载均衡器的可用指标|
|/loadBalancers/read|获取负载均衡器定义|
|/loadBalancers/virtualMachines/read|获取对负载均衡器下的所有虚拟机的引用|
|/loadBalancers/write|创建负载均衡器，或更新现有的负载均衡器|
|/localnetworkgateways/delete|删除 LocalNetworkGateway|
|/localnetworkgateways/read|获取 LocalNetworkGateway|
|/localnetworkgateways/write|创建新的或更新现有的 LocalNetworkGateway|
|/locations/checkDnsNameAvailability/read|检查 DNS 标签是否可在指定的位置使用|
|/locations/operationResults/read|获取异步 POST 或 DELETE 操作的操作结果|
|/locations/operations/read|获取表示异步操作状态的操作资源|
|/locations/usages/read|获取资源用量指标|
|/locations/virtualNetworkAvailableEndpointServices/read|获取可用虚拟网络终结点服务的列表|
|/networkInterfaces/delete|删除网络接口|
|/networkInterfaces/diagnosticIdentity/read|获取资源的诊断标识|
|/networkInterfaces/effectiveNetworkSecurityGroups/action|获取 VM 的网络接口上配置的网络安全组|
|/networkInterfaces/effectiveRouteTable/action|获取 VM 的网络接口上配置的路由表|
|/networkInterfaces/ipconfigurations/read|获取网络接口 IP 配置定义。 |
|/networkInterfaces/join/action|将虚拟机加入到网络接口|
|/networkInterfaces/loadBalancers/read|获取网络接口所属的所有负载均衡器|
|/networkInterfaces/providers/Microsoft.Insights/metricDefinitions/read|获取网络接口的可用指标|
|/networkInterfaces/read|获取网络接口定义。 |
|/networkInterfaces/write|创建网络接口，或更新现有的网络接口。 |
|/networkSecurityGroups/defaultSecurityRules/read|获取默认的安全规则定义|
|/networkSecurityGroups/delete|删除网络安全组|
|/networkSecurityGroups/join/action|加入网络安全组|
|/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read|获取网络安全组诊断设置|
|/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新网络安全组诊断设置，此操作由 Insights 资源提供程序进行补充。|
|/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read|获取网络安全组的事件|
|/networkSecurityGroups/read|获取网络安全组定义|
|/networkSecurityGroups/securityRules/delete|删除安全规则|
|/networkSecurityGroups/securityRules/read|获取安全规则定义|
|/networkSecurityGroups/securityRules/write|创建安全规则，或更新现有的安全规则|
|/networkSecurityGroups/write|创建网络安全组，或更新现有的网络安全组|
|/networkWatchers/availableProvidersList/action|返回指定 Azure 区域的所有可用 Internet 服务提供商。|
|/networkWatchers/azureReachabilityReport/action|返回 Internet 服务提供商从指定位置到 Azure 区域的相对延迟评分。|
|/networkWatchers/configureFlowLog/action|配置目标资源的流日志记录。|
|/networkWatchers/connectionMonitors/delete|删除连接监视器|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ diagnosticSettings/read|获取连接监视器的诊断设置|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ diagnosticSettings/write|创建或更新连接监视器诊断设置|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ metricDefinitions/read|获取连接监视器的可用指标|
|/networkWatchers/connectionMonitors/query/action|监视指定终结点之间连接的查询|
|/networkWatchers/connectionMonitors/read|获取连接监视器详细信息|
|/networkWatchers/connectionMonitors/start/action|开始监视指定终结点之间的连接|
|/networkWatchers/connectionMonitors/stop/action|停止/暂停监视指定终结点之间的连接|
|/networkWatchers/connectionMonitors/write|创建连接监视器|
|/networkWatchers/connectivityCheck/action|验证从虚拟机到给定终结点（包括另一个 VM 或任意远程服务器）建立直接 TCP 连接的可能性。|
|/networkWatchers/delete|删除网络观察程序|
|/networkWatchers/ipFlowVerify/action|返回是允许还是拒绝与特定的目标相互发送数据包。|
|/networkWatchers/lenses/delete|删除可重用功能区|
|/networkWatchers/lenses/query/action|监视指定终结点上流量的查询|
|/networkWatchers/lenses/read|获取可重用功能区详细信息|
|/networkWatchers/lenses/start/action|开始监视指定终结点上的流量|
|/networkWatchers/lenses/stop/action|停止/暂停监视指定终结点上的流量|
|/networkWatchers/lenses/write|创建可重用功能区|
|/networkWatchers/nextHop/action|对于指定的目标和目标 IP 地址，返回下一跃点类型和下一跃点 IP 地址。|
|/networkWatchers/packetCaptures/delete|删除数据包捕获|
|/networkWatchers/packetCaptures/queryStatus/action|获取有关数据包捕获资源的属性和状态的信息。|
|/networkWatchers/packetCaptures/read|获取数据包捕获定义|
|/networkWatchers/packetCaptures/stop/action|停止正在运行的数据包捕获会话。|
|/networkWatchers/packetCaptures/write|创建数据包捕获|
|/networkWatchers/queryFlowLogStatus/action|获取资源上流日志记录的状态。|
|/networkWatchers/queryTroubleshootResult/action|获取上次运行的或当前正在运行的故障排除操作的故障排除结果。|
|/networkWatchers/read|获取网络观察程序定义|
|/networkWatchers/securityGroupView/action|查看 VM 上应用的已配置和有效的网络安全组规则。|
|/networkWatchers/topology/action|获取资源组中的资源及其关系的网络级视图。|
|/networkWatchers/troubleshoot/action|开始针对 Azure 中的网络资源进行故障排除。|
|/networkWatchers/write|创建网络观察程序，或更新现有的网络观察程序|
|/operations/read|获取可用操作|
|/publicIPAddresses/delete|删除公共 IP 地址。|
|/publicIPAddresses/join/action|联接公共 IP 地址|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/read|获取公用 IP 地址的诊断设置|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新公用 IP 地址的诊断设置|
|/publicIPAddresses/providers/Microsoft.Insights/logDefinitions/read|获取公用 IP 地址的日志定义|
|/publicIPAddresses/providers/Microsoft.Insights/metricDefinitions/read|获取公用 IP 地址的指标定义|
|/publicIPAddresses/read|获取公共 IP 地址定义。|
|/publicIPAddresses/write|创建公共 IP 地址，或更新现有的公共 IP 地址。 |
|/register/action|注册订阅|
|/routeFilters/delete|删除路由筛选器定义|
|/routeFilters/join/action|联接路由筛选器|
|/routeFilters/read|获取路由筛选器定义|
|/routeFilters/routeFilterRules/delete|删除路由筛选器规则定义|
|/routeFilters/routeFilterRules/read|获取路由筛选器规则定义|
|/routeFilters/routeFilterRules/write|创建路由筛选器规则，或更新现有的路由筛选器规则|
|/routeFilters/write|创建路由筛选器，或更新现有的路由筛选器|
|/routeTables/delete|删除路由表定义|
|/routeTables/join/action|加入路由表|
|/routeTables/read|获取路由表定义|
|/routeTables/routes/delete|删除路由定义|
|/routeTables/routes/read|获取路由定义|
|/routeTables/routes/write|创建路由，或更新现有的路由|
|/routeTables/write|创建路由表，或更新现有的路由表|
|/securegateways/applicationRuleCollections/delete|删除安全网关的应用程序规则集合|
|/securegateways/applicationRuleCollections/read|检索给定安全网关的应用程序规则集合|
|/securegateways/applicationRuleCollections/write|创建或更新安全网关的应用程序规则集合|
|/securegateways/delete|删除安全网关|
|/securegateways/networkRuleCollections/delete|删除安全网关的网络规则集合|
|/securegateways/networkRuleCollections/read|检索给定安全网关的网络规则集合|
|/securegateways/networkRuleCollections/write|创建或更新安全网关的网络规则集合|
|/securegateways/read|获取安全网关|
|/securegateways/write|创建或更新安全网关|
|/serviceEndpointPolicies/delete|删除服务终结点策略|
|/serviceEndpointPolicies/join/action|加入服务终结点策略|
|/serviceEndpointPolicies/joinSubnet/action|将子网加入到服务终结点策略|
|/serviceEndpointPolicies/read|获取服务终结点策略说明|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete|删除服务终结点策略定义|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read|获取服务终结点策略定义说明|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write|创建服务终结点策略定义或更新现有服务终结点策略定义|
|/serviceEndpointPolicies/write|创建服务终结点策略或更新现有服务终结点策略|
|/trafficManagerGeographicHierarchies/read|获取流量管理器地理层次结构，其中包含可以配合地理流量路由方法使用的区域|
|/trafficManagerProfiles/azureEndpoints/delete|从现有流量管理器配置文件中删除 Azure 终结点。 流量管理器会停止将流量路由到已删除的 Azure 终结点。|
|/trafficManagerProfiles/azureEndpoints/read|获取属于流量管理器配置文件的 Azure 终结点，包括该 Azure 终结点的所有属性。|
|/trafficManagerProfiles/azureEndpoints/write|在现有流量管理器配置文件中添加新的 Azure 终结点，或更新该流量管理器配置文件中的现有 Azure 终结点的属性。|
|/trafficManagerProfiles/delete|删除流量管理器配置文件。 与流量管理器配置文件关联的所有设置都将丢失，该配置文件不再可用于路由流量。|
|/trafficManagerProfiles/externalEndpoints/delete|从现有流量管理器配置文件中删除外部终结点。 流量管理器会停止将流量路由到已删除的外部终结点。|
|/trafficManagerProfiles/externalEndpoints/read|获取属于流量管理器配置文件的外部终结点，包括该外部终结点的所有属性。|
|/trafficManagerProfiles/externalEndpoints/write|在现有流量管理器配置文件中添加新的外部终结点，或更新该流量管理器配置文件中的现有外部终结点的属性。|
|/trafficManagerProfiles/heatMaps/read|获取给定流量管理器配置文件的流量管理器热度地图，其中包含按位置和源 IP 的查询计数和延迟数据。|
|/trafficManagerProfiles/nestedEndpoints/delete|从现有流量管理器配置文件中删除嵌套终结点。 流量管理器会停止将流量路由到已删除的嵌套终结点。|
|/trafficManagerProfiles/nestedEndpoints/read|获取属于流量管理器配置文件的嵌套终结点，包括该嵌套终结点的所有属性。|
|/trafficManagerProfiles/nestedEndpoints/write|在现有流量管理器配置文件中添加新的嵌套终结点，或更新该流量管理器配置文件中的现有嵌套终结点的属性。|
|/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/read|获取流量管理器诊断设置|
|/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新流量管理器诊断设置，此操作由 Insights 资源提供程序进行补充。|
|/trafficManagerProfiles/providers/Microsoft.Insights/logDefinitions/read|获取流量管理器的事件|
|/trafficManagerProfiles/providers/Microsoft.Insights/metricDefinitions/read|获取流量管理器的可用指标。|
|/trafficManagerProfiles/read|获取流量管理器配置文件配置。 此配置包含 DNS 设置、流量路由设置、终结点监视设置，以及此流量管理器配置文件路由的终结点列表。|
|/trafficManagerProfiles/write|创建流量管理器配置文件，或修改现有流量管理器配置文件的配置。 这包括启用或禁用配置文件，以及修改 DNS 设置、流量路由设置或终结点监视设置。 可以添加、删除、启用或禁用流量管理器配置文件路由的终结点。|
|/trafficManagerUserMetricsKeys/delete|删除用于实时用户指标集合的订阅级别密钥。|
|/trafficManagerUserMetricsKeys/read|获取用于实时用户指标集合的订阅级别密钥。|
|/trafficManagerUserMetricsKeys/write|创建将用于实时用户指标集合的新订阅级别密钥。|
|/unregister/action|取消注册订阅|
|/virtualHubs/delete|删除虚拟中心|
|/virtualHubs/hubVirtualNetworkConnections/delete|删除 HubVirtualNetworkConnection|
|/virtualHubs/hubVirtualNetworkConnections/read|获取 HubVirtualNetworkConnection|
|/virtualHubs/hubVirtualNetworkConnections/write|创建或更新 HubVirtualNetworkConnection|
|/virtualHubs/read|获取虚拟中心|
|/virtualHubs/write|创建或更新虚拟中心|
|/virtualnetworkgateways/connections/read|获取 VirtualNetworkGatewayConnection|
|/virtualNetworkGateways/delete|删除 virtualNetworkGateway|
|/virtualnetworkgateways/generatevpnclientpackage/action|生成 virtualNetworkGateway 的 VpnClient 包|
|/virtualnetworkgateways/generatevpnprofile/action|生成 VirtualNetworkGateway 的 VpnProfile 包|
|/virtualnetworkgateways/getadvertisedroutes/action|获取 virtualNetworkGateway 播发路由|
|/virtualnetworkgateways/getbgppeerstatus/action|获取 virtualNetworkGateway BGP 对等状态|
|/virtualnetworkgateways/getlearnedroutes/action|获取 virtualnetworkgateway 获知的路由|
|/virtualnetworkgateways/getvpnclientipsecparameters/action|获取 VirtualNetworkGateway P2S 客户端的 Vpnclient Ipsec 参数。|
|/virtualnetworkgateways/getvpnprofilepackageurl/action|获取预生成的 VPN 客户端配置文件包的 URL|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/read|获取虚拟网络网关诊断设置|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新虚拟网络网关诊断设置，此操作由 Insights 资源提供程序进行补充。|
|/virtualNetworkGateways/providers/Microsoft.Insights/logDefinitions/read|获取虚拟网络网关的事件|
|/virtualNetworkGateways/providers/Microsoft.Insights/metricDefinitions/read|获取虚拟网络网关的可用指标|
|/virtualNetworkGateways/read|获取 VirtualNetworkGateway|
|/virtualnetworkgateways/reset/action|重置 virtualNetworkGateway|
|/virtualnetworkgateways/setvpnclientipsecparameters/action|设置 VirtualNetworkGateway P2S 客户端的 Vpnclient Ipsec 参数。|
|/virtualnetworkgateways/supportedvpndevices/action|列出支持的 VPN 设备|
|/virtualNetworkGateways/write|创建或更新 VirtualNetworkGateway|
|/virtualNetworks/checkIpAddressAvailability/read|检查 IP 地址是否在指定的虚拟网络中可用|
|/virtualNetworks/customViews/get/action|获取虚拟网络自定义视图内容|
|/virtualNetworks/customViews/read|获取虚拟网络的自定义视图的定义|
|/virtualNetworks/delete|删除虚拟网络|
|/virtualNetworks/peer/action|在两个不同的虚拟网络之间建立对等互连|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/read|获取虚拟网络的诊断设置|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新虚拟网络的诊断设置|
|/virtualNetworks/providers/Microsoft.Insights/logDefinitions/read|获取虚拟网络的日志定义|
|/virtualNetworks/providers/Microsoft.Insights/metricDefinitions/read|获取虚拟网络的指标定义|
|/virtualNetworks/read|获取虚拟网络定义|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete|删除虚拟网络对等互连代理|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/read|获取虚拟网络对等互连代理定义|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/write|创建虚拟网络对等互连代理，或更新现有虚拟网络对等互连代理|
|/virtualNetworks/subnets/delete|删除虚拟网络子网|
|/virtualNetworks/subnets/join/action|加入虚拟网络|
|/virtualNetworks/subnets/joinViaServiceEndpoint/action|将存储帐户或 SQL 数据库等资源加入到子网。|
|/virtualNetworks/subnets/read|获取虚拟网络子网定义|
|/virtualNetworks/subnets/resourceNavigationLinks/delete|删除资源导航链接|
|/virtualNetworks/subnets/resourceNavigationLinks/read|获取资源导航链接定义|
|/virtualNetworks/subnets/resourceNavigationLinks/write|创建资源导航链接，或更新现有资源导航链接|
|/virtualNetworks/subnets/virtualMachines/read|获取对虚拟网络子网中的所有虚拟机的引用|
|/virtualNetworks/subnets/write|创建虚拟网络子网，或更新现有的虚拟网络子网|
|/virtualNetworks/taggedTrafficConsumers/delete|删除标记的流量使用者|
|/virtualNetworks/taggedTrafficConsumers/read|获取标记的流量使用者定义|
|/virtualNetworks/taggedTrafficConsumers/validate/action|验证标记的流量使用者|
|/virtualNetworks/taggedTrafficConsumers/write|创建标记的流量使用者，或更新现有标记的流量使用者|
|/virtualNetworks/usages/read|获取虚拟网络的每个子网的 IP 使用情况|
|/virtualNetworks/virtualMachines/read|获取对虚拟网络中的所有虚拟机的引用|
|/virtualNetworks/virtualNetworkPeerings/delete|删除虚拟网络对等互连|
|/virtualNetworks/virtualNetworkPeerings/read|获取虚拟网络对等互连定义|
|/virtualNetworks/virtualNetworkPeerings/write|创建虚拟网络对等互连，或更新现有的虚拟网络对等互连|
|/virtualNetworks/write|创建虚拟网络，或更新现有的虚拟网络|
|/virtualNetworkTaps/delete|删除虚拟网络点击|
|/virtualNetworkTaps/join/action|加入虚拟网络点击|
|/virtualNetworkTaps/read|获取虚拟网络点击|
|/virtualNetworkTaps/write|创建或更新虚拟网络点击|
|/virtualwans/delete|删除虚拟 WAN|
|/virtualwans/read|获取虚拟 WAN|
|/virtualWans/virtualHubProxies/delete|删除虚拟中心代理|
|/virtualWans/virtualHubProxies/read|获取虚拟中心代理定义|
|/virtualWans/virtualHubProxies/write|创建虚拟中心代理，或更新虚拟中心代理|
|/virtualwans/virtualHubs/read|获取与虚拟 WAN 关联的所有虚拟中心。|
|/virtualwans/vpnconfiguration/read|获取 VPN 配置|
|/virtualWans/vpnSiteProxies/delete|删除 VPN 站点代理|
|/virtualWans/vpnSiteProxies/read|获取 VPN 站点代理定义|
|/virtualWans/vpnSiteProxies/write|创建一个 VPN 站点代理，或更新 VPN 站点代理|
|/virtualwans/vpnSites/read|获取与虚拟 WAN 关联的所有 VPN 站点。|
|/virtualwans/write|创建或更新虚拟 WAN|
|/vpnGateways/read|获取 VpnGateway。|
|/vpnGateways/vpnConnections/read|获取 VpnConnection。|
|/vpnGateways/vpnConnections/write|放置 VpnConnection。|
|/vpnGateways/write|放置 VpnGateway。|
|/vpnsites/delete|删除 VPN 站点资源。|
|/vpnsites/read|获取 VPN 站点资源。|
|/vpnsites/write|创建或更新 VPN 站点资源。|

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

| Operation | 说明 |
|---|---|
|/CheckNamespaceAvailability/action|检查给定的命名空间资源名称是否在 NotificationHub 服务中可用。|
|/Namespaces/authorizationRules/action|获取命名空间授权规则说明列表。|
|/Namespaces/authorizationRules/delete|删除命名空间授权规则。 无法删除默认的命名空间授权规则。 |
|/Namespaces/authorizationRules/listkeys/action|获取命名空间的连接字符串|
|/Namespaces/authorizationRules/read|获取命名空间授权规则说明列表。|
|/Namespaces/authorizationRules/regenerateKeys/action|命名空间授权规则再生成主密钥/辅助密钥，指定需要再生成的密钥|
|/Namespaces/authorizationRules/write|创建命名空间级授权规则并更新其属性。 可以更新授权规则访问权限、主密钥和辅助密钥。|
|/Namespaces/CheckNotificationHubAvailability/action|检查给定的 NotificationHub 名称是否在命名空间中可用。|
|/Namespaces/Delete|删除命名空间资源|
|/Namespaces/NotificationHubs/authorizationRules/action|获取通知中心授权规则列表|
|/Namespaces/NotificationHubs/authorizationRules/delete|删除通知中心授权规则|
|/Namespaces/NotificationHubs/authorizationRules/listkeys/action|获取通知中心的连接字符串|
|/Namespaces/NotificationHubs/authorizationRules/read|获取通知中心授权规则列表|
|/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action|通知中心授权规则再生成主密钥/辅助密钥，指定需要再生成的密钥|
|/Namespaces/NotificationHubs/authorizationRules/write|创建通知中心授权规则并更新其属性。 可以更新授权规则访问权限、主密钥和辅助密钥。|
|/Namespaces/NotificationHubs/debugSend/action|发送测试推送通知。|
|/Namespaces/NotificationHubs/Delete|删除通知中心资源|
|/Namespaces/NotificationHubs/metricDefinitions/read|获取命名空间指标资源说明列表|
|/Namespaces/NotificationHubs/pnsCredentials/action|获取所有通知中心的 PNS 凭据。 这包括 WNS、MPNS、APNS、GCM 和百度凭据|
|/Namespaces/NotificationHubs/read|获取通知中心资源说明列表|
|/Namespaces/NotificationHubs/write|创建通知中心并更新其属性。 其属性主要包括 PNS 凭据。 授权规则和 TTL|
|/Namespaces/read|获取命名空间资源说明列表|
|/Namespaces/write|创建命名空间资源并更新其属性。 命名空间的标记和容量是可更新的属性。|
|/register/action|注册 NotifciationHubs 资源提供程序的订阅，并启用命名空间和 NotificationHubs 的创建|

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

| Operation | 说明 |
|---|---|
|/linkTargets/read|列出不与 Azure 订阅关联的现有帐户。 要将此 Azure 订阅链接到工作区，请使用此操作在“创建工作区”操作的客户 ID 属性中返回的客户 ID。|
|/register/action|将订阅注册到资源提供程序。|
|/workspaces/analytics/query/action|使用新引擎进行搜索。|
|/workspaces/analytics/query/schema/read|获取搜索架构 V2。|
|/workspaces/api/query/action|使用新引擎进行搜索。|
|/workspaces/api/query/schema/read|获取搜索架构 V2。|
|/workspaces/configurationScopes/delete|删除配置范围|
|/workspaces/configurationScopes/read|获取配置范围|
|/workspaces/configurationScopes/write|设置配置范围|
|/workspaces/datasources/delete|删除工作区下面的数据源。|
|/workspaces/datasources/read|获取工作区下面的数据源。|
|/workspaces/datasources/write|在工作区下面创建/更新数据源。|
|/workspaces/delete|删除工作区。 如果该工作区在创建时已链接到现有工作区，则不会删除它链接到的工作区。|
|/workspaces/generateregistrationcertificate/action|为工作区生成注册证书。 此证书用于将 Microsoft System Center Operation Manager 连接到工作区。|
|/workspaces/intelligencepacks/disable/action|为给定的工作区禁用智能包。|
|/workspaces/intelligencepacks/enable/action|为给定的工作区启用智能包。|
|/workspaces/intelligencepacks/read|列出给定的工作区可见的所有智能包，并列出是为该工作区启用还是禁用了智能包。|
|/workspaces/linkedServices/delete|删除给定工作区下的链接服务。|
|/workspaces/linkedServices/read|获取给定工作区下的链接服务。|
|/workspaces/linkedServices/write|创建/更新给定工作区下的链接服务。|
|/workspaces/listKeys/action|检索工作区的列表密钥。 这些密钥用于将 Microsoft Operational Insights 代理连接到工作区。|
|/workspaces/listKeys/read|检索工作区的列表密钥。 这些密钥用于将 Microsoft Operational Insights 代理连接到工作区。|
|/workspaces/managementGroups/read|获取已连接到此工作区的 System Center Operations Manager 管理组的名称和元数据。|
|/workspaces/metricDefinitions/read|获取工作区下的指标定义|
|/workspaces/notificationSettings/delete|删除工作区的用户通知设置。|
|/workspaces/notificationSettings/read|获取工作区的用户通知设置。|
|/workspaces/notificationSettings/write|设置工作区的用户通知设置。|
|/workspaces/purge/action|从工作区中删除指定数据|
|/workspaces/read|获取现有工作区|
|/workspaces/savedSearches/delete|删除保存的搜索查询|
|/workspaces/savedSearches/read|获取保存的搜索查询|
|/workspaces/savedSearches/write|创建保存的搜索查询|
|/workspaces/schema/read|获取工作区的搜索架构。  搜索架构包含公开的字段及其类型。|
|/workspaces/search/action|执行搜索查询|
|/workspaces/sharedKeys/action|检索工作区的共享密钥。 这些密钥用于将 Microsoft Operational Insights 代理连接到工作区。|
|/workspaces/sharedKeys/read|检索工作区的共享密钥。 这些密钥用于将 Microsoft Operational Insights 代理连接到工作区。|
|/workspaces/storageinsightconfigs/delete|删除存储配置。 这会阻止 Microsoft Operational Insights 从存储帐户中读取数据。|
|/workspaces/storageinsightconfigs/read|获取存储配置。|
|/workspaces/storageinsightconfigs/write|创建新的存储配置。 这些配置用于从现有存储帐户中的某个位置提取数据。|
|/workspaces/usages/read|获取工作区的使用情况数据，包括工作区读取的数据量。|
|/workspaces/write|创建新的工作区，或者通过提供现有工作区中的客户 ID 链接到现有工作区。|

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

| Operation | 说明 |
|---|---|
|/managementAssociations/delete|删除现有管理关联|
|/managementAssociations/read|获取现有管理关联|
|/managementAssociations/write|创建新的管理关联|
|/managementConfigurations/delete|删除现有管理配置|
|/managementConfigurations/read|获取现有管理配置|
|/managementConfigurations/write|创建新的管理配置|
|/register/action|将订阅注册到资源提供程序。|
|/solutions/delete|删除现有的 OMS 解决方案|
|/solutions/read|获取现有的 OMS 解决方案|
|/solutions/write|创建新的 OMS 解决方案|

## <a name="microsoftportal"></a>Microsoft.Portal

| Operation | 说明 |
|---|---|
|/dashboards/delete|从订阅删除仪表板。|
|/dashboards/read|读取订阅的仪表板。|
|/dashboards/write|向订阅添加仪表板或修改仪表板。|

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

| Operation | 说明 |
|---|---|
|/capacities/checkNameAvailability/action|检查给定的 Power BI 专用容量名称是否有效且未被使用。|
|/capacities/delete|删除 Power BI 专用容量。|
|/capacities/providers/Microsoft.Insights/metricDefinitions/read|获取 Power BI 专用容量的可用指标。|
|/capacities/read|检索指定 Power BI 专用容量的信息。|
|/capacities/write|创建或更新指定的 Power BI 专用容量。|

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

| Operation | 说明 |
|---|---|
|/locations/allocatedStamp/read|GetAllocatedStamp 是服务使用的内部操作|
|/locations/allocateStamp/action|AllocateStamp 是服务使用的内部操作|
|/locations/backupPreValidateProtection/action||
|/locations/backupStatus/action|检查恢复服务保管库的备份状态|
|/locations/backupValidateFeatures/action|验证功能|
|/operations/read|操作返回资源提供程序的操作列表|
|/register/action|注册给定资源提供程序的订阅|
|/Vaults/backupconfig/read|返回恢复服务保管库的配置。|
|/Vaults/backupconfig/write|更新恢复服务保管库的配置。|
|/Vaults/backupEngines/read|返回使用保管库注册的所有备份管理服务器。|
|/Vaults/backupFabrics/{fabricName}/protectionContainers/{containerName}/items/read|获取容器中的所有项|
|/Vaults/backupFabrics/backupProtectionIntent/write|创建备份保护意向|
|/Vaults/backupFabrics/operationResults/read|返回操作状态|
|/Vaults/backupFabrics/protectableContainers/read|获取所有可保护的容器|
|/Vaults/backupFabrics/protectionContainers/inquire/action|在容器内进行工作负载的查询|
|/Vaults/backupFabrics/protectionContainers/operationResults/read|获取对保护容器执行的操作的结果。|
|/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action|对受保护的项执行备份。|
|/Vaults/backupFabrics/protectionContainers/protectedItems/delete|删除受保护的项|
|/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read|获取对受保护项执行的操作的结果。|
|/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read|返回对受保护项执行的操作的状态。|
|/Vaults/backupFabrics/protectionContainers/protectedItems/read|返回受保护项的对象详细信息|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/provisionInstantItemRecovery/action|预配受保护项的即时项恢复|
|/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read|获取受保护项的恢复点。|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/restore/action|还原受保护项的恢复点。|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/revokeInstantItemRecovery/action|吊销受保护项的即时项恢复|
|/Vaults/backupFabrics/protectionContainers/protectedItems/write|创建备份受保护项|
|/Vaults/backupFabrics/protectionContainers/read|返回所有已注册的容器|
|/Vaults/backupFabrics/protectionContainers/write|创建已注册的容器|
|/Vaults/backupFabrics/refreshContainers/action|刷新容器列表|
|/Vaults/backupJobs/cancel/action|取消作业|
|/Vaults/backupJobs/operationResults/read|返回作业操作的结果。|
|/Vaults/backupJobs/read|返回所有作业对象|
|/Vaults/backupJobsExport/action|导出作业|
|/Vaults/backupJobsExport/operationResults/read|返回导出作业操作的结果。|
|/Vaults/backupManagementMetaData/read|返回恢复服务保管库的备份管理元数据。|
|/Vaults/backupOperationResults/read|返回恢复服务保管库的备份操作结果。|
|/Vaults/backupOperations/read|返回恢复服务保管库的备份操作状态。|
|/Vaults/backupPolicies/delete|删除保护策略|
|/Vaults/backupPolicies/operationResults/read|获取策略操作的结果。|
|/Vaults/backupPolicies/operations/read|获取策略操作的状态。|
|/Vaults/backupPolicies/read|返回所有保护策略|
|/Vaults/backupPolicies/write|创建保护策略|
|/Vaults/backupProtectableItems/read|返回所有可保护项的列表。|
|/Vaults/backupProtectedItems/read|返回所有受保护项的列表。|
|/Vaults/backupProtectionContainers/read|返回属于订阅的所有容器|
|/Vaults/backupSecurityPIN/action|返回恢复服务保管库的安全 PIN 信息。|
|/Vaults/backupstorageconfig/read|返回恢复服务保管库的存储配置。|
|/Vaults/backupstorageconfig/write|更新恢复服务保管库的存储配置。|
|/Vaults/backupUsageSummaries/read|返回恢复服务的受保护项和受保护服务器的摘要。|
|/Vaults/certificates/write|“更新资源证书”操作更新资源/保管库凭据证书。|
|/Vaults/delete|“删除保管库”操作删除“vault”类型的指定 Azure 资源|
|/Vaults/extendedInformation/delete|“获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象的扩展信息|
|/Vaults/extendedInformation/read|“获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象的扩展信息|
|/Vaults/extendedInformation/write|“获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象的扩展信息|
|/Vaults/monitoringAlerts/read|获取恢复服务保管库的警报。|
|/Vaults/monitoringAlerts/write|解决警报。|
|/Vaults/monitoringConfigurations/read|获取恢复服务保管库通知配置。|
|/Vaults/monitoringConfigurations/write|配置到恢复服务保管库的电子邮件通知。|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/read|Azure 备份诊断|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/write|Azure 备份诊断|
|/Vaults/providers/Microsoft.Insights/logDefinitions/read|Azure 备份日志|
|/Vaults/providers/Microsoft.Insights/metricDefinitions/read|Azure 备份指标|
|/Vaults/read|“获取保管库”操作获取表示“vault”类型的 Azure 资源的对象|
|/Vaults/registeredIdentities/delete|“取消注册容器”操作可用于取消注册容器。|
|/Vaults/registeredIdentities/operationResults/read|“获取操作结果”操作可用于获取异步提交的操作的操作状态和结果|
|/Vaults/registeredIdentities/read|“获取容器”操作可用于获取针对资源注册的容器。|
|/Vaults/registeredIdentities/write|“注册服务容器”操作可用于向恢复服务注册容器。|
|/vaults/replicationAlertSettings/read|读取任何警报设置|
|/vaults/replicationAlertSettings/write|创建或更新任何警报设置|
|/vaults/replicationEvents/read|读取任何事件|
|/vaults/replicationFabrics/checkConsistency/action|检查结构的一致性|
|/vaults/replicationFabrics/delete|删除任何结构|
|/vaults/replicationFabrics/deployProcessServerImage/action|部署进程服务器映像|
|/vaults/replicationFabrics/read|读取任何结构|
|/vaults/replicationFabrics/reassociateGateway/action|重新关联网关|
|/vaults/replicationFabrics/remove/action|删除结构|
|/vaults/replicationFabrics/renewcertificate/action|续订 Fabric 的证书|
|/vaults/replicationFabrics/replicationNetworks/read|读取任何网络|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete|删除任何网络映射|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read|读取任何网络映射|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write|创建或更新任何网络映射|
|/vaults/replicationFabrics/replicationProtectionContainers/ discoverProtectableItem/action|发现可保护项|
|/vaults/replicationFabrics/replicationProtectionContainers/read|读取任何保护容器|
|/vaults/replicationFabrics/replicationProtectionContainers/remove/action|删除保护容器|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectableItems/read|读取任何可保护项|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/applyRecoveryPoint/action|应用还原点|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/delete|删除任何受保护的项|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/failoverCommit/action|故障转移提交|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/plannedFailover/action|计划内故障转移|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/read|读取任何受保护的项|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/recoveryPoints/read|读取任何复制恢复点|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/remove/action|删除受保护的项|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/repairReplication/action|修复复制|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/reProtect/action|重新保护受保护的项|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/testFailover/action|测试故障转移|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/testFailoverCleanup/action|测试故障转移清理|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/unplannedFailover/action|故障转移|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/updateMobilityService/action|更新移动服务|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/write|创建或更新任何受保护的项|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/delete|删除任何保护容器映射|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/read|读取任何保护容器映射|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/remove/action|删除保护容器映射|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/write|创建或更新任何保护容器映射|
|/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action|交换保护容器|
|/vaults/replicationFabrics/replicationProtectionContainers/write|创建或更新任何保护容器|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete|删除任何恢复服务提供程序|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/read|读取任何恢复服务提供程序|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/ refreshProvider/action|刷新提供程序|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action|删除恢复服务提供程序|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/write|创建或更新任何恢复服务提供程序|
|/vaults/replicationFabrics/replicationStorageClassifications/read|读取任何存储分类|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/delete|删除任何存储分类映射|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/read|读取任何存储分类映射|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/write|创建或更新任何存储分类映射|
|/vaults/replicationFabrics/replicationvCenters/delete|删除任何作业|
|/vaults/replicationFabrics/replicationvCenters/read|读取任何作业|
|/vaults/replicationFabrics/replicationvCenters/write|创建或更新任何作业|
|/vaults/replicationFabrics/write|创建或更新任何结构|
|/vaults/replicationJobs/cancel/action|取消作业|
|/vaults/replicationJobs/read|读取任何作业|
|/vaults/replicationJobs/restart/action|重新启动作业|
|/vaults/replicationJobs/resume/action|恢复作业|
|/vaults/replicationPolicies/delete|删除任何策略|
|/vaults/replicationPolicies/read|读取任何策略|
|/vaults/replicationPolicies/write|创建或更新任何策略|
|/vaults/replicationRecoveryPlans/delete|删除任何恢复计划|
|/vaults/replicationRecoveryPlans/failoverCommit/action|故障转移提交恢复计划|
|/vaults/replicationRecoveryPlans/plannedFailover/action|计划内故障转移恢复计划|
|/vaults/replicationRecoveryPlans/read|读取任何恢复计划|
|/vaults/replicationRecoveryPlans/reProtect/action|重新保护恢复计划|
|/vaults/replicationRecoveryPlans/testFailover/action|测试故障转移恢复计划|
|/vaults/replicationRecoveryPlans/testFailoverCleanup/action|测试故障转移清理恢复计划|
|/vaults/replicationRecoveryPlans/unplannedFailover/action|故障转移恢复计划|
|/vaults/replicationRecoveryPlans/write|创建或更新任何恢复计划|
|/Vaults/tokenInfo/read|返回恢复服务保管库的令牌信息。|
|/vaults/usages/read|读取任何保管库使用情况|
|/Vaults/usages/read|返回恢复服务保管库的使用情况详细信息。|
|/Vaults/vaultTokens/read|“保管库令牌”操作可用于获取保管库级后端操作的保管库令牌。|
|/Vaults/write|“创建保管库”操作创建“vault”类型的 Azure 资源|

## <a name="microsoftrelay"></a>Microsoft.Relay

| Operation | 说明 |
|---|---|
|/checkNameAvailability/action|检查给定订阅下的命名空间可用性。|
|/checkNamespaceAvailability/action|检查给定订阅下的命名空间可用性。 已弃用此 API，请改为使用 CheckNameAvailabiltiy。|
|/namespaces/authorizationRules/action|更新命名空间授权规则。 已弃用此 API。 请改为使用 PUT 调用来更新命名空间授权规则。 不支持对 API 版本 2017-04-01 执行此操作。|
|/namespaces/authorizationRules/delete|删除命名空间授权规则。 无法删除默认的命名空间授权规则。 |
|/namespaces/authorizationRules/listkeys/action|获取命名空间的连接字符串|
|/namespaces/authorizationRules/read|获取命名空间授权规则说明列表。|
|/namespaces/authorizationRules/regenerateKeys/action|再生成资源的主密钥或辅助密钥|
|/namespaces/authorizationRules/write|创建命名空间级授权规则并更新其属性。 可以更新授权规则访问权限、主密钥和辅助密钥。|
|/namespaces/Delete|删除命名空间资源|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|获取灾难恢复主命名空间的授权规则密钥|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|获取灾难恢复主命名空间的授权规则|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|禁用灾难恢复，并停止从主命名空间向辅助命名空间复制更改。|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|检查给定订阅下命名空间别名的可用性。|
|/namespaces/disasterRecoveryConfigs/delete|删除与命名空间关联的灾难恢复配置。 只能通过主命名空间调用此操作。|
|/namespaces/disasterRecoveryConfigs/failover/action|调用异地灾难恢复故障转移并将命名空间别名重新配置为指向辅助命名空间。|
|/namespaces/disasterRecoveryConfigs/read|获取与命名空间关联的灾难恢复配置。|
|/namespaces/disasterRecoveryConfigs/write|创建或更新与命名空间关联的灾难恢复配置。|
|/namespaces/HybridConnections/authorizationRules/action|更新 HybridConnection 的操作。 不支持对 API 版本 2017-04-01 执行此操作。 授权规则。 请使用 PUT 调用来更新授权规则。|
|/namespaces/HybridConnections/authorizationRules/delete|用于删除 HybridConnection 授权规则的操作|
|/namespaces/HybridConnections/authorizationRules/listkeys/action|获取 HybridConnection 的连接字符串|
|/namespaces/HybridConnections/authorizationRules/read| 获取 HybridConnection 授权规则的列表|
|/namespaces/HybridConnections/authorizationRules/regeneratekeys/action|再生成资源的主密钥或辅助密钥|
|/namespaces/HybridConnections/authorizationRules/write|创建 HybridConnection 授权规则并更新其属性。 可以更新授权规则访问权限。|
|/namespaces/HybridConnections/Delete|用于删除 HybridConnection 资源的操作|
|/namespaces/HybridConnections/read|获取 HybridConnection 资源说明列表|
|/namespaces/HybridConnections/write|创建或更新 HybridConnection 属性。|
|/namespaces/messagingPlan/read|获取命名空间的消息传递计划。 已弃用此 API。 在以后的 API 版本中，通过 MessagingPlan 资源公开的属性将移动到（父）命名空间资源。 不支持对 API 版本 2017-04-01 执行此操作。|
|/namespaces/messagingPlan/write|更新命名空间的消息传递计划。 已弃用此 API。 在以后的 API 版本中，通过 MessagingPlan 资源公开的属性将移动到（父）命名空间资源。 不支持对 API 版本 2017-04-01 执行此操作。|
|/namespaces/operationresults/read|获取命名空间操作的状态|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|获取命名空间指标资源说明列表|
|/namespaces/read|获取命名空间资源说明列表|
|/namespaces/WcfRelays/authorizationRules/action|更新 WcfRelay 的操作。 不支持对 API 版本 2017-04-01 执行此操作。 授权规则。 请使用 PUT 调用来更新授权规则。|
|/namespaces/WcfRelays/authorizationRules/delete|用于删除 WcfRelay 授权规则的操作|
|/namespaces/WcfRelays/authorizationRules/listkeys/action|获取 WcfRelay 的连接字符串|
|/namespaces/WcfRelays/authorizationRules/read| 获取 WcfRelay 授权规则的列表|
|/namespaces/WcfRelays/authorizationRules/regeneratekeys/action|再生成资源的主密钥或辅助密钥|
|/namespaces/WcfRelays/authorizationRules/write|创建 WcfRelay 授权规则并更新其属性。 可以更新授权规则访问权限。|
|/namespaces/WcfRelays/Delete|用于删除 WcfRelay 资源的操作|
|/namespaces/WcfRelays/read|获取 WcfRelay 资源说明列表|
|/namespaces/WcfRelays/write|创建或更新 WcfRelay 属性。|
|/namespaces/write|创建命名空间资源并更新其属性。 命名空间的标记和容量是可更新的属性。|
|/operations/read|获取操作|
|/register/action|注册中继资源提供程序的订阅，并启用中继资源的创建|
|/unregister/action|注册中继资源提供程序的订阅，并启用中继资源的创建|

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

| Operation | 说明 |
|---|---|
|/AvailabilityStatuses/current/read|获取指定资源的可用性状态|
|/AvailabilityStatuses/read|获取指定范围内所有资源的可用性状态|
|/healthevent/action|表示指定资源的运行状况状态的更改|
|/healthevent/Activated/action|表示指定资源的运行状况状态的更改|
|/healthevent/InProgress/action|表示指定资源的运行状况状态的更改|
|/healthevent/Pending/action|表示指定资源的运行状况状态的更改|
|/healthevent/Resolved/action|表示指定资源的运行状况状态的更改|
|/healthevent/Updated/action|表示指定资源的运行状况状态的更改|
|/register/action|注册 Microsoft ResourceHealth 的订阅|

## <a name="microsoftresources"></a>Microsoft.Resources

| Operation | 说明 |
|---|---|
|/checkResourceName/action|检查资源名称的有效性。|
|/deployments/cancel/action|取消部署。|
|/deployments/delete|删除部署。|
|/deployments/operations/read|获取或列出部署操作。|
|/deployments/read|获取或列出部署。|
|/deployments/validate/action|验证部署。|
|/deployments/write|创建或更新部署。|
|/links/delete|删除资源链接。|
|/links/read|获取或列出资源链接。|
|/links/write|创建或更新资源链接。|
|/marketplace/purchase/action|从商城购买资源。|
|/providers/read|获取提供程序的列表。|
|/resources/read|基于筛选器获取资源的列表。|
|/subscriptions/locations/read|获取支持的位置列表。|
|/subscriptions/operationresults/read|获取订阅操作结果。|
|/subscriptions/providers/read|获取或列出资源提供程序。|
|/subscriptions/read|获取订阅的列表。|
|/subscriptions/resourceGroups/delete|删除资源组及其所有资源。|
|/subscriptions/resourcegroups/deployments/operations/read|获取或列出部署操作。|
|/subscriptions/resourcegroups/deployments/operationstatuses/read|获取或列出部署操作状态。|
|/subscriptions/resourcegroups/deployments/read|获取或列出部署。|
|/subscriptions/resourcegroups/deployments/write|创建或更新部署。|
|/subscriptions/resourceGroups/moveResources/action|将资源从一个资源组移到另一个资源组。|
|/subscriptions/resourceGroups/read|获取或列出资源组。|
|/subscriptions/resourcegroups/resources/read|获取资源组的资源。|
|/subscriptions/resourceGroups/validateMoveResources/action|验证是否已将资源从一个资源组移到另一个资源组。|
|/subscriptions/resourceGroups/write|创建或更新资源组。|
|/subscriptions/resources/read|获取订阅的资源。|
|/subscriptions/tagNames/delete|删除订阅标记。|
|/subscriptions/tagNames/read|获取或列出订阅标记。|
|/subscriptions/tagNames/tagValues/delete|删除订阅标记值。|
|/subscriptions/tagNames/tagValues/read|获取或列出订阅标记值。|
|/subscriptions/tagNames/tagValues/write|添加订阅标记值。|
|/subscriptions/tagNames/write|添加订阅标记。|
|/tenants/read|获取租户的列表。|

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

| Operation | 说明 |
|---|---|
|/jobcollections/delete|删除作业集合。|
|/jobcollections/disable/action|禁用作业集合。|
|/jobcollections/enable/action|启用作业集合。|
|/jobcollections/jobs/delete|删除作业。|
|/jobcollections/jobs/generateLogicAppDefinition/action|基于计划程序作业生成逻辑应用定义。|
|/jobcollections/jobs/jobhistories/read|获取作业历史记录。|
|/jobcollections/jobs/read|获取作业。|
|/jobcollections/jobs/run/action|运行作业。|
|/jobcollections/jobs/write|创建或更新作业。|
|/jobcollections/read|获取作业集合|
|/jobcollections/write|创建或更新作业集合。|

## <a name="microsoftsearch"></a>Microsoft.Search

| Operation | 说明 |
|---|---|
|/checkNameAvailability/action|检查服务名称的可用性。|
|/register/action|注册搜索资源提供程序的订阅，并启用搜索服务的创建。|
|/searchServices/createQueryKey/action|创建查询密钥。|
|/searchServices/delete|删除搜索服务。|
|/searchServices/diagnosticSettings/read|获取用于读取资源的诊断设置|
|/searchServices/diagnosticSettings/write|创建或更新资源的诊断设置|
|/searchServices/listAdminKeys/action|读取管理密钥。|
|/searchServices/logDefinitions/read|获取搜索服务的可用日志|
|/searchServices/metricDefinitions/read|获取搜索服务的可用指标|
|/searchServices/queryKey/delete|删除查询密钥。|
|/searchServices/queryKey/read|读取查询密钥。|
|/searchServices/read|读取搜索服务。|
|/searchServices/regenerateAdminKey/action|再生成管理密钥。|
|/searchServices/start/action|启动搜索服务。|
|/searchServices/stop/action|停止搜索服务。|
|/searchServices/write|创建或更新搜索服务。|

## <a name="microsoftsecurity"></a>Microsoft.Security

| Operation | 说明 |
|---|---|
|/alerts/read|获取所有可用的安全警报|
|/applicationWhitelistings/read|获取应用程序允许列表|
|/applicationWhitelistings/write|创建新的或更新现有的应用程序允许列表|
|/complianceResults/read|获取资源的符合性结果|
|/locations/alerts/activate/action|激活安全警报|
|/locations/alerts/dismiss/action|消除安全警报|
|/locations/alerts/read|获取所有可用的安全警报|
|/locations/jitNetworkAccessPolicies/initiate/action|启动适时网络访问策略|
|/locations/jitNetworkAccessPolicies/read|获取适时网络访问策略|
|/locations/jitNetworkAccessPolicies/write|创建新的更新现有的适时网络访问策略|
|/locations/read|获取安全数据位置|
|/locations/tasks/activate/action|激活安全建议|
|/locations/tasks/dismiss/action|关闭安全建议|
|/locations/tasks/read|获取所有可用的安全建议|
|/locations/tasks/resolve/action|解决安全建议|
|/locations/tasks/start/action|启用安全建议|
|/policies/read|获取安全策略|
|/policies/write|更新安全策略|
|/pricings/delete|删除某一范围的定价设置|
|/pricings/read|获取某一范围的定价设置|
|/pricings/write|更新某一范围的定价设置|
|/register/action|注册 Azure 安全中心的订阅|
|/securityContacts/delete|删除安全联系信息|
|/securityContacts/read|获取安全联系信息|
|/securityContacts/write|更新安全联系信息|
|/securitySolutions/delete|删除安全解决方案|
|/securitySolutions/read|获取安全解决方案|
|/securitySolutions/write|创建新的或更新现有的安全解决方案|
|/securitySolutionsReferenceData/read|获取安全解决方案引用数据|
|/securityStatuses/read|获取 Azure 资源的安全运行状况|
|/securityStatusesSummaries/read|获取某一范围的安全状态摘要|
|/tasks/read|获取所有可用的安全建议|
|/webApplicationFirewalls/delete|删除 Web 应用程序防火墙|
|/webApplicationFirewalls/read|获取 Web 应用程序防火墙|
|/webApplicationFirewalls/write|创建新的或更新现有的 Web 应用程序防火墙|
|/workspaceSettings/connect/action|更改工作区设置重新连接设置|
|/workspaceSettings/delete|删除工作区设置|
|/workspaceSettings/read|获取工作区设置|
|/workspaceSettings/write|更新工作区设置|

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

| Operation | 说明 |
|---|---|
|/checkNameAvailability/action|检查给定订阅下的命名空间可用性。|
|/checkNamespaceAvailability/action|检查给定订阅下的命名空间可用性。 已弃用此 API，请改为使用 CheckNameAvailabiltiy。|
|/namespaces/authorizationRules/action|更新命名空间授权规则。 已弃用此 API。 请改为使用 PUT 调用来更新命名空间授权规则。 不支持对 API 版本 2017-04-01 执行此操作。|
|/namespaces/authorizationRules/delete|删除命名空间授权规则。 无法删除默认的命名空间授权规则。 |
|/namespaces/authorizationRules/listkeys/action|获取命名空间的连接字符串|
|/namespaces/authorizationRules/read|获取命名空间授权规则说明列表。|
|/namespaces/authorizationRules/regenerateKeys/action|再生成资源的主密钥或辅助密钥|
|/namespaces/authorizationRules/write|创建命名空间级授权规则并更新其属性。 可以更新授权规则访问权限、主密钥和辅助密钥。|
|/namespaces/Delete|删除命名空间资源|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|获取灾难恢复主命名空间的授权规则密钥|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|获取灾难恢复主命名空间的授权规则|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|禁用灾难恢复，并停止从主命名空间向辅助命名空间复制更改。|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|检查给定订阅下命名空间别名的可用性。|
|/namespaces/disasterRecoveryConfigs/delete|删除与命名空间关联的灾难恢复配置。 只能通过主命名空间调用此操作。|
|/namespaces/disasterRecoveryConfigs/failover/action|调用异地灾难恢复故障转移并将命名空间别名重新配置为指向辅助命名空间。|
|/namespaces/disasterRecoveryConfigs/read|获取与命名空间关联的灾难恢复配置。|
|/namespaces/disasterRecoveryConfigs/write|创建或更新与命名空间关联的灾难恢复配置。|
|/namespaces/eventGridFilters/delete|删除与命名空间关联的事件网格筛选器。|
|/namespaces/eventGridFilters/read|获取与命名空间关联的事件网格筛选器。|
|/namespaces/eventGridFilters/write|创建或更新与命名空间关联的事件网格筛选器。|
|/namespaces/eventhubs/read|获取 EventHub 资源说明列表|
|/namespaces/messagingPlan/read|获取命名空间的消息传递计划。 已弃用此 API。 在以后的 API 版本中，通过 MessagingPlan 资源公开的属性将移动到（父）命名空间资源。 不支持对 API 版本 2017-04-01 执行此操作。|
|/namespaces/messagingPlan/write|更新命名空间的消息传递计划。 已弃用此 API。 在以后的 API 版本中，通过 MessagingPlan 资源公开的属性将移动到（父）命名空间资源。 不支持对 API 版本 2017-04-01 执行此操作。|
|/namespaces/migrate/action|迁移命名空间操作|
|/namespaces/operationresults/read|获取命名空间操作的状态|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|获取命名空间诊断设置资源说明列表|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|获取命名空间诊断设置资源说明列表|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|获取命名空间日志资源说明列表|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|获取命名空间指标资源说明列表|
|/namespaces/queues/authorizationRules/action|更新队列的操作。 不支持对 API 版本 2017-04-01 执行此操作。 授权规则。 请使用 PUT 调用来更新授权规则。|
|/namespaces/queues/authorizationRules/delete|用于删除队列授权规则的操作|
|/namespaces/queues/authorizationRules/listkeys/action|获取队列的连接字符串|
|/namespaces/queues/authorizationRules/read| 获取队列授权规则列表|
|/namespaces/queues/authorizationRules/regenerateKeys/action|再生成资源的主密钥或辅助密钥|
|/namespaces/queues/authorizationRules/write|创建队列授权规则并更新其属性。 可以更新授权规则访问权限。|
|/namespaces/queues/Delete|用于删除队列资源的操作|
|/namespaces/queues/read|获取队列资源说明列表|
|/namespaces/queues/write|创建或更新队列属性。|
|/namespaces/read|获取命名空间资源说明列表|
|/namespaces/topics/authorizationRules/action|更新主题的操作。 不支持对 API 版本 2017-04-01 执行此操作。 授权规则。 请使用 PUT 调用来更新授权规则。|
|/namespaces/topics/authorizationRules/delete|用于删除主题授权规则的操作|
|/namespaces/topics/authorizationRules/listkeys/action|获取主题的连接字符串|
|/namespaces/topics/authorizationRules/read| 获取主题授权规则列表|
|/namespaces/topics/authorizationRules/regenerateKeys/action|再生成资源的主密钥或辅助密钥|
|/namespaces/topics/authorizationRules/write|创建主题授权规则并更新其属性。 可以更新授权规则访问权限。|
|/namespaces/topics/Delete|用于删除主题资源的操作|
|/namespaces/topics/read|获取主题资源说明列表|
|/namespaces/topics/subscriptions/Delete|用于删除 TopicSubscription 资源的操作|
|/namespaces/topics/subscriptions/read|获取 TopicSubscription 资源说明列表|
|/namespaces/topics/subscriptions/rules/Delete|用于删除规则资源的操作|
|/namespaces/topics/subscriptions/rules/read|获取规则资源说明列表|
|/namespaces/topics/subscriptions/rules/write|创建或更新规则属性。|
|/namespaces/topics/subscriptions/write|创建或更新 TopicSubscription 属性。|
|/namespaces/topics/write|创建或更新主题属性。|
|/namespaces/write|创建命名空间资源并更新其属性。 命名空间的标记和容量是可更新的属性。|
|/operations/read|获取操作|
|/register/action|注册 ServiceBus 提供程序的订阅，并启用 ServiceBus 资源的创建|
|/sku/read|获取 SKU 资源说明列表|
|/sku/regions/read|获取 SKU 区域资源说明列表|
|/unregister/action|注册 ServiceBus 提供程序的订阅，并启用 ServiceBus 资源的创建|

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

| Operation | 说明 |
|---|---|
|/clusters/applications/delete|删除任何应用程序|
|/clusters/applications/read|读取任何应用程序|
|/clusters/applications/services/delete|删除任何服务|
|/clusters/applications/services/partitions/read|读取任何分区|
|/clusters/applications/services/partitions/replicas/read|读取任何副本|
|/clusters/applications/services/read|读取任何服务|
|/clusters/applications/services/statuses/read|读取任何服务状态|
|/clusters/applications/services/write|创建或更新任何服务|
|/clusters/applications/write|创建或更新任何应用程序|
|/clusters/applicationTypes/delete|删除任何应用程序类型|
|/clusters/applicationTypes/read|读取任何应用程序类型|
|/clusters/applicationTypes/versions/delete|删除任何应用程序类型版本|
|/clusters/applicationTypes/versions/read|读取任何应用程序类型版本|
|/clusters/applicationTypes/versions/write|创建或更新任何应用程序类型版本|
|/clusters/applicationTypes/write|创建或更新任何应用程序类型|
|/clusters/delete|删除任何群集|
|/clusters/nodes/read|读取任何节点|
|/clusters/read|读取任何群集|
|/clusters/statuses/read|读取任何群集状态|
|/clusters/write|创建或更新任何群集|
|/locations/clusterVersions/read|读取任何群集版本|
|/locations/environments/clusterVersions/read|读取特定环境的任何群集版本|
|/locations/operationresults/read|读取任何操作结果|
|/locations/operations/read|按位置读取任何操作|
|/operations/read|读取任何可用操作|
|/register/action|注册任何操作|

## <a name="microsoftsolutions"></a>Microsoft.Solutions

| Operation | 说明 |
|---|---|
|/applicationDefinitions/delete|删除应用程序定义。|
|/applicationDefinitions/read|检索应用程序定义列表。|
|/applicationDefinitions/write|添加或修改应用程序定义。|
|/applications/delete|删除应用程序。|
|/applications/read|检索应用程序列表。|
|/applications/write|创建应用程序。|
|/locations/operationStatuses/read|读取资源的操作状态。|
|/register/action|注册到解决方案。|

## <a name="microsoftsql"></a>Microsoft.Sql

| Operation | 说明 |
|---|---|
|/checkNameAvailability/action|验证给定的服务器名称是否可用于在全球范围内对给定的订阅进行预配。|
|/locations/auditingSettingsAzureAsyncOperation/read|检索扩展服务器 blob 审核策略集操作的结果|
|/locations/auditingSettingsOperationResults/read|检索服务器 Blob 审核策略集操作的结果|
|/locations/capabilities/read|获取给定位置中的此订阅的功能|
|/locations/databaseAzureAsyncOperation/read|获取数据库操作的状态。|
|/locations/databaseOperationResults/read|获取数据库操作的状态。|
|/locations/deletedServerAsyncOperation/read|获取正在对已删除服务器进行的操作|
|/locations/deletedServerOperationResults/read|获取正在对已删除服务器进行的操作|
|/locations/deletedServers/read|返回已删除服务器的列表，或获取指定的已删除服务器的属性。|
|/locations/deletedServers/recover/action|恢复已删除的服务器|
|/locations/deleteVirtualNetworkOrSubnets/action|删除与虚拟网络或子网关联的虚拟网络规则|
|/locations/elasticPoolAzureAsyncOperation/read|获取弹性池异步操作的 Azure 异步操作|
|/locations/elasticPoolOperationResults/read|获取弹性池操作的结果。|
|/locations/extendedAuditingSettingsAzureAsyncOperation/read|检索扩展服务器 blob 审核策略集操作的结果|
|/locations/extendedAuditingSettingsOperationResults/read|检索扩展服务器 blob 审核策略集操作的结果|
|/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action|完成托管数据库还原操作|
|/locations/managedTransparentDataEncryptionAzureAsyncOperation/read|获取正在对托管数据库透明数据加密进行的操作|
|/locations/managedTransparentDataEncryptionOperationResults/read|获取正在对托管数据库透明数据加密进行的操作|
|/locations/read|获取给定订阅的可用位置|
|/locations/syncAgentOperationResults/read|检索同步代理资源操作的结果|
|/locations/syncDatabaseIds/read|检索特定区域和订阅的同步数据库 ID|
|/locations/syncGroupOperationResults/read|检索同步组资源操作的结果|
|/locations/syncMemberOperationResults/read|检索同步成员资源操作的结果|
|/locations/usages/read|获取此位置中的此订阅的使用指标的集合|
|/locations/virtualNetworkRulesAzureAsyncOperation/read|返回指定虚拟网络规则 Azure 异步操作的详细信息 |
|/locations/virtualNetworkRulesOperationResults/read|返回指定虚拟网络规则操作的详细信息 |
|/managedInstances/administrators/delete|删除托管实例的现有管理员。|
|/managedInstances/administrators/read|获取托管实例管理员的列表。|
|/managedInstances/administrators/write|使用指定参数创建或更新托管实例管理员。|
|/managedInstances/databases/delete|删除现有托管数据库|
|/managedInstances/databases/read|获取现有托管数据库|
|/managedInstances/databases/securityAlertPolicies/read|检索在给定托管数据库上配置的数据库威胁检测策略的详细信息|
|/managedInstances/databases/securityAlertPolicies/write|更改给定托管数据库的数据库威胁检测策略|
|/managedInstances/databases/securityEvents/read|检索托管数据库安全事件|
|/managedInstances/databases/transparentDataEncryption/read|检索给定托管数据库上的数据库透明数据加密的详细信息|
|/managedInstances/databases/transparentDataEncryption/write|更改给定托管数据库的数据库透明数据加密|
|/managedInstances/databases/write|创建新数据库或更新现有数据库。|
|/managedInstances/delete|删除现有托管实例。|
|/managedInstances/metricDefinitions/read|获取托管实例指标定义|
|/managedInstances/metrics/read|获取托管实例指标|
|/managedInstances/read|返回托管实例的列表，或获取指定托管实例的属性。|
|/managedInstances/securityAlertPolicies/read|检索在给定托管服务器上配置的托管服务器威胁检测策略的详细信息|
|/managedInstances/securityAlertPolicies/write|更改给定托管服务器的托管服务器威胁检测策略|
|/managedInstances/write|使用指定参数创建托管实例，或更新指定托管实例的属性或标记。|
|/operations/read|获取可用的 REST 操作|
|/register/action|注册 Microsoft SQL 数据库资源提供程序的订阅，并启用 Microsoft SQL 数据库的创建。|
|/servers/administratorOperationResults/read|获取正在对服务器管理员进行的操作|
|/servers/administrators/delete|删除服务器管理员|
|/servers/administrators/read|检索服务器管理员详细信息|
|/servers/administrators/write|创建或更新服务器管理员|
|/servers/advisors/read|返回可用于服务器的顾问列表|
|/servers/advisors/recommendedActions/read|返回服务器的指定顾问的建议操作列表|
|/servers/advisors/recommendedActions/write|对服务器应用建议的操作|
|/servers/advisors/write|在服务器级别更新顾问的自动执行状态。|
|/servers/auditingPolicies/read|检索在给定的服务器上配置的默认服务器表审核策略的详细信息|
|/servers/auditingPolicies/write|更改给定服务器的默认服务器表审核|
|/servers/auditingSettings/operationResults/read|检索服务器 Blob 审核策略集操作的结果|
|/servers/auditingSettings/read|检索在给定的服务器上配置的服务器 Blob 审核策略的详细信息|
|/servers/auditingSettings/write|更改给定服务器的服务器 Blob 审核|
|/servers/automaticTuning/read|返回服务器的自动微调设置|
|/servers/automaticTuning/write|更新服务器的自动微调设置并返回更新的设置|
|/servers/backupLongTermRetentionVaults/delete|删除现有备份存档保管库。|
|/servers/backupLongTermRetentionVaults/read|此操作用于获取备份长期保留保管库。 它返回有关已注册到此服务器的保管库的信息|
|/servers/backupLongTermRetentionVaults/write|此操作用于将备份长期保留保管库注册到服务器|
|/servers/communicationLinks/delete|删除现有服务器通信链接。|
|/servers/communicationLinks/read|返回指定服务器的通信链接列表。|
|/servers/communicationLinks/write|创建或更新服务器通信链接。|
|/servers/connectionPolicies/read|返回指定服务器的服务器连接策略列表。|
|/servers/connectionPolicies/write|创建或更新服务器连接策略。|
|/servers/databases/advisors/read|返回可用于数据库的顾问列表|
|/servers/databases/advisors/recommendedActions/read|返回数据库的指定顾问的建议操作列表|
|/servers/databases/advisors/recommendedActions/write|对数据库应用建议的操作|
|/servers/databases/advisors/write|在数据库级别更新顾问的自动执行状态。|
|/servers/databases/auditingPolicies/read|检索在给定的数据库上配置的表审核策略的详细信息|
|/servers/databases/auditingPolicies/write|更改给定数据库的表审核策略|
|/servers/databases/auditingSettings/read|检索在给定的数据库上配置的 Blob 审核策略的详细信息|
|/servers/databases/auditingSettings/write|更改给定数据库的 Blob 审核策略|
|/servers/databases/auditRecords/read|检索数据库 Blob 审核记录|
|/servers/databases/automaticTuning/read|返回数据库的自动微调设置|
|/servers/databases/automaticTuning/write|更新数据库的自动微调设置并返回更新的设置|
|/servers/databases/azureAsyncOperation/read|获取数据库操作的状态。|
|/servers/databases/backupLongTermRetentionPolicies/read|返回指定数据库的备份存档策略列表。|
|/servers/databases/backupLongTermRetentionPolicies/write|创建或更新数据库备份存档策略。|
|/servers/databases/connectionPolicies/read|检索在给定的数据库上配置的连接策略的详细信息|
|/servers/databases/connectionPolicies/write|更改给定数据库的连接策略|
|/servers/databases/dataMaskingPolicies/read|返回数据库数据掩码策略的列表。|
|/servers/databases/dataMaskingPolicies/rules/delete|删除给定数据库的数据掩码策略规则|
|/servers/databases/dataMaskingPolicies/rules/read|检索在给定数据库上配置的数据掩码策略规则的详细信息|
|/servers/databases/dataMaskingPolicies/rules/write|更改给定数据库的数据掩码策略规则|
|/servers/databases/dataMaskingPolicies/write|更改给定数据库的数据掩码策略|
|/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read|返回所选步骤 ID 的数据仓库查询的分布式查询步骤信息|
|/servers/databases/dataWarehouseQueries/read|返回所选查询 ID 的数据仓库分布查询信息|
|/servers/databases/dataWarehouseUserActivities/read|检索包含正在运行和已暂停查询的 SQL 数据仓库实例的用户活动|
|/servers/databases/delete|删除现有数据库。|
|/servers/databases/export/action|导出 Azure SQL 数据库|
|/servers/databases/extendedAuditingSettings/read|检索在给定的数据库上配置的扩展 blob 审核策略的详细信息|
|/servers/databases/extendedAuditingSettings/write|更改给定数据库的扩展 blob 审核策略|
|/servers/databases/extensions/read|获取数据库的扩展集合。|
|/servers/databases/extensions/write|更改给定数据库的扩展|
|/servers/databases/geoBackupPolicies/read|检索给定数据库的异地备份策略|
|/servers/databases/geoBackupPolicies/write|创建或更新数据库异地备份策略|
|/servers/databases/importExportOperationResults/read|获取正在进行的导入/导出操作|
|/servers/databases/metricDefinitions/read|返回可用于数据库的指标类型|
|/servers/databases/metrics/read|返回数据库的指标|
|/servers/databases/move/action|重命名 Azure SQL 数据库|
|/servers/databases/operationResults/read|获取数据库操作的状态。|
|/servers/databases/operations/cancel/action|取消尚未完成的 Azure SQL 数据库挂起异步操作。|
|/servers/databases/operations/read|返回对数据库执行的操作列表|
|/servers/databases/pause/action|暂停 Azure SQL 数据仓库数据库|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read|获取资源的诊断设置|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新资源的诊断设置|
|/servers/databases/providers/Microsoft.Insights/logDefinitions/read|获取数据库的可用日志|
|/servers/databases/providers/Microsoft.Insights/metricDefinitions/read|返回可用于数据库的指标类型|
|/servers/databases/queryStore/queryTexts/read|返回与指定参数对应的查询文本的集合。|
|/servers/databases/queryStore/read|返回数据库的查询存储设置的当前值。|
|/servers/databases/queryStore/write|更新数据库的 Query Store 设置|
|/servers/databases/read|返回数据库的列表，或获取指定数据库的属性。|
|/servers/databases/replicationLinks/delete|强制终止复制关系，这可能会丢失数据|
|/servers/databases/replicationLinks/failover/action|从主节点同步所有更改后执行故障转移，使此数据库成为复制关系的主节点，并使远程主节点成为辅助节点|
|/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action|立即故障转移（可能会丢失数据），使此数据库成为复制关系的主节点，并使远程主节点成为辅助节点|
|/servers/databases/replicationLinks/read|返回为特定数据库建立的复制链接的详细信息|
|/servers/databases/replicationLinks/unlink/action|强制终止或者在与合作伙伴同步后终止复制关系|
|/servers/databases/replicationLinks/updateReplicationMode/action|将链接的复制模式更新为同步或异步模式|
|/servers/databases/restorePoints/action|创建新的还原点|
|/servers/databases/restorePoints/read|返回数据库的还原点。|
|/servers/databases/resume/action|恢复 Azure SQL 数据仓库数据库|
|/servers/databases/schemas/read|检索数据库的架构列表|
|/servers/databases/schemas/tables/columns/read|检索表的列列表|
|/servers/databases/schemas/tables/columns/sensitivityLabels/delete|删除给定列的敏感度标签|
|/servers/databases/schemas/tables/columns/sensitivityLabels/read|获取给定列的敏感度标签|
|/servers/databases/schemas/tables/columns/sensitivityLabels/write|创建或更新给定列的敏感度标签|
|/servers/databases/schemas/tables/read|检索数据库的表列表|
|/servers/databases/schemas/tables/recommendedIndexes/read|检索数据库上的索引建议列表|
|/servers/databases/schemas/tables/recommendedIndexes/write|应用索引建议|
|/servers/databases/securityAlertPolicies/read|检索在给定的数据库上配置的威胁检测策略的详细信息|
|/servers/databases/securityAlertPolicies/write|更改给定数据库的威胁检测策略|
|/servers/databases/securityMetrics/read|获取数据库安全指标的集合|
|/servers/databases/sensitivityLabels/read|列出给定数据库的敏感度标签|
|/servers/databases/serviceTierAdvisors/read|返回有关根据查询执行统计信息扩展或缩减数据库的建议，以提高性能或降低成本|
|/servers/databases/syncGroups/cancelSync/action|取消同步组同步|
|/servers/databases/syncGroups/delete|删除现有同步组。|
|/servers/databases/syncGroups/hubSchemas/read|返回同步中心数据库架构的列表|
|/servers/databases/syncGroups/logs/read|返回同步组日志的列表|
|/servers/databases/syncGroups/read|返回同步组的列表，或获取指定同步组的属性。|
|/servers/databases/syncGroups/refreshHubSchema/action|刷新同步中心数据库架构|
|/servers/databases/syncGroups/refreshHubSchemaOperationResults/read|检索同步中心架构刷新操作的结果|
|/servers/databases/syncGroups/syncMembers/delete|删除现有同步成员。|
|/servers/databases/syncGroups/syncMembers/read|返回同步成员的列表，或获取指定同步成员的属性。|
|/servers/databases/syncGroups/syncMembers/refreshSchema/action|刷新同步成员架构|
|/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read|检索同步成员架构刷新操作的结果|
|/servers/databases/syncGroups/syncMembers/schemas/read|返回同步成员数据库架构的列表|
|/servers/databases/syncGroups/syncMembers/write|使用指定参数创建同步成员，或更新指定同步成员的属性。|
|/servers/databases/syncGroups/triggerSync/action|触发同步组同步|
|/servers/databases/syncGroups/write|使用指定参数创建同步组，或更新指定同步组的属性。|
|/servers/databases/topQueries/queryText/action|返回所选查询 ID 的 Transact-SQL 文本|
|/servers/databases/topQueries/read|返回所选查询在所选时间段内的聚合运行时统计信息|
|/servers/databases/topQueries/statistics/read|返回所选查询在所选时间段内的聚合运行时统计信息|
|/servers/databases/transparentDataEncryption/operationResults/read|获取正在对透明数据加密进行的操作|
|/servers/databases/transparentDataEncryption/read|检索给定数据库的透明数据加密安全功能的状态和详细信息|
|/servers/databases/transparentDataEncryption/write|更改透明数据加密状态|
|/servers/databases/upgradeDataWarehouse/action|升级 Azure SQL 数据仓库数据库|
|/servers/databases/usages/read|获取 Azure SQL 数据库使用情况信息|
|/servers/databases/vulnerabilityAssessments/delete|删除给定数据库的漏洞评估|
|/servers/databases/vulnerabilityAssessments/read|检索在给定数据库上配置的漏洞评估的详细信息|
|/servers/databases/vulnerabilityAssessments/rules/baselines/delete|删除给定数据库的漏洞评估规则基线|
|/servers/databases/vulnerabilityAssessments/rules/baselines/read|获取给定数据库的漏洞评估规则基线|
|/servers/databases/vulnerabilityAssessments/rules/baselines/write|更改给定数据库的漏洞评估规则基线|
|/servers/databases/vulnerabilityAssessments/scans/action|执行漏洞评估数据库扫描。|
|/servers/databases/vulnerabilityAssessments/scans/export/action|将现有扫描结果转换为可读取格式。 如果已存在，则没有任何反应|
|/servers/databases/vulnerabilityAssessments/scans/read|返回数据库漏洞评估扫描记录的列表，或获取指定扫描 ID 的扫描记录。|
|/servers/databases/vulnerabilityAssessments/write|更改给定数据库的漏洞评估|
|/servers/databases/vulnerabilityAssessmentScans/action|执行漏洞评估数据库扫描。|
|/servers/databases/vulnerabilityAssessmentScans/operationResults/read|检索数据库漏洞评估扫描执行操作的结果|
|/servers/databases/vulnerabilityAssessmentSettings/read|检索在给定数据库上配置的漏洞评估的详细信息|
|/servers/databases/vulnerabilityAssessmentSettings/write|更改给定数据库的漏洞评估|
|/servers/databases/write|使用指定的参数创建数据库，或更新指定数据库的属性或标记。|
|/servers/delete|删除现有服务器。|
|/servers/disasterRecoveryConfiguration/delete|删除给定服务器的现有灾难恢复配置|
|/servers/disasterRecoveryConfiguration/failover/action|故障转移 DisasterRecoveryConfiguration|
|/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action|强制故障转移 DisasterRecoveryConfiguration|
|/servers/disasterRecoveryConfiguration/read|获取包含此服务器的灾难恢复配置的集合|
|/servers/disasterRecoveryConfiguration/write|更改服务器灾难恢复配置|
|/servers/elasticPoolEstimates/read|返回已为此服务器创建的弹性池估计列表|
|/servers/elasticPoolEstimates/write|为提供的数据库列表创建新的弹性池估计|
|/servers/elasticPools/advisors/read|返回可用于弹性池的顾问列表|
|/servers/elasticPools/advisors/recommendedActions/read|返回弹性池的指定顾问的建议操作列表|
|/servers/elasticPools/advisors/recommendedActions/write|对弹性池应用建议的操作|
|/servers/elasticPools/advisors/write|在弹性池级别更新顾问的自动执行状态。|
|/servers/elasticPools/databases/read|获取弹性池的数据库列表|
|/servers/elasticPools/delete|删除现有弹性池|
|/servers/elasticPools/elasticPoolActivity/read|检索给定弹性数据库池的活动和详细信息|
|/servers/elasticPools/elasticPoolDatabaseActivity/read|检索属于弹性数据库池的给定数据库的活动和详细信息|
|/servers/elasticPools/metricDefinitions/read|返回可用于弹性数据库池的指标类型|
|/servers/elasticPools/metrics/read|返回弹性数据库池的指标|
|/servers/elasticPools/operations/cancel/action|取消尚未完成的 Azure SQL 弹性池挂起异步操作。|
|/servers/elasticPools/operations/read|返回对弹性池执行的操作列表|
|/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read|获取资源的诊断设置|
|/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新资源的诊断设置|
|/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read|返回可用于弹性数据库池的指标类型|
|/servers/elasticPools/read|检索给定服务器上弹性池的详细信息|
|/servers/elasticPools/skus/read|获取此弹性池可用的 SKU 的集合|
|/servers/elasticPools/write|创建新弹性池或更改现有弹性池的属性|
|/servers/encryptionProtector/read|返回服务器加密保护程序的列表，或获取指定服务器加密保护程序的属性。|
|/servers/encryptionProtector/write|更新指定服务器加密保护程序的属性。|
|/servers/extendedAuditingSettings/read|检索在给定服务器上配置的扩展服务器 blob 审核策略的详细信息|
|/servers/extendedAuditingSettings/write|更改给定服务器的扩展服务器 blob 审核|
|/servers/failoverGroups/delete|删除现有故障转移组。|
|/servers/failoverGroups/failover/action|在现有故障转移组中执行计划的故障转移。|
|/servers/failoverGroups/forceFailoverAllowDataLoss/action|在现有故障转移组中执行强制故障转移。|
|/servers/failoverGroups/read|返回故障转移组的列表，或获取指定故障转移组的属性。|
|/servers/failoverGroups/write|使用指定参数创建故障转移组，或更新指定故障转移组的属性或标记。|
|/servers/firewallRules/delete|删除现有服务器防火墙规则。|
|/servers/firewallRules/read|返回服务器防火墙规则的列表，或获取指定服务器防火墙规则的属性。|
|/servers/firewallRules/write|使用指定参数创建服务器防火墙规则、更新指定规则的属性、或使用新的服务器防火墙规则覆盖所有现有规则。|
|/servers/import/action|在服务器上创建新数据库，并部署 DacPac 包中的架构和数据|
|/servers/importExportOperationResults/read|获取正在进行的导入/导出操作|
|/servers/keys/delete|删除现有服务器密钥。|
|/servers/keys/read|返回服务器密钥的列表，或获取指定服务器密钥的属性。|
|/servers/keys/write|使用指定参数创建密钥，或更新指定服务器密钥的属性或标记。|
|/servers/operationResults/read|获取正在进行的服务器操作|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|返回服务器可用的指标类型|
|/servers/read|返回服务器列表，或获取指定服务器的属性。|
|/servers/recommendedElasticPools/databases/read|检索给定服务器的建议弹性数据库池的指标|
|/servers/recommendedElasticPools/read|检索针对弹性数据库池的建议，以便根据历史资源利用率降低成本或提高性能|
|/servers/recoverableDatabases/read|此操作用于实时数据库的灾难恢复，以便将数据库还原到最后一个已知正常的备份点。 它返回有关最后一个正常备份的信息，但实际上不会还原数据库。|
|/servers/restorableDroppedDatabases/read|获取已在给定服务器中删除但仍包含在保留策略中的数据库列表。|
|/servers/securityAlertPolicies/operationResults/read|检索服务器威胁检测策略写入操作的结果|
|/servers/securityAlertPolicies/read|检索在给定的服务器上配置的服务器威胁检测策略的详细信息|
|/servers/securityAlertPolicies/write|更改给定服务器的服务器威胁检测策略|
|/servers/serviceObjectives/read|检索可在给定服务器上实现的服务级别目标（也称为性能层|
|/servers/syncAgents/delete|删除现有同步代理。|
|/servers/syncAgents/generateKey/action|生成同步代理注册密钥|
|/servers/syncAgents/linkedDatabases/read|返回同步代理链接数据库的列表|
|/servers/syncAgents/read|返回同步代理的列表，或获取指定同步代理的属性。|
|/servers/syncAgents/write|使用指定参数创建同步代理，或更新指定同步代理的属性。|
|/servers/usages/read|返回服务器 DTU 配额，以及服务器中的所有数据库当前消耗的 DTU|
|/servers/virtualNetworkRules/delete|删除现有虚拟网络规则|
|/servers/virtualNetworkRules/read|返回虚拟网络规则列表，或获取指定虚拟网络规则的属性。|
|/servers/virtualNetworkRules/write|使用指定参数创建虚拟网络规则，或更新指定虚拟网络规则的属性或标记。|
|/servers/write|使用指定参数创建服务器，或更新指定服务器的属性或标记。|
|/unregister/action|取消注册 Microsoft SQL 数据库资源提供程序的订阅，并启用 Microsoft SQL 数据库的创建。|
|/virtualClusters/read|返回虚拟群集的列表，或获取指定虚拟群集的属性。|
|/virtualClusters/write|更新虚拟群集标记。|

## <a name="microsoftstorage"></a>Microsoft.Storage

| Operation | 说明 |
|---|---|
|/checknameavailability/read|检查帐户名称是否有效且未被使用。|
|/locations/deleteVirtualNetworkOrSubnets/action|向 Microsoft.Storage 通知正在删除虚拟网络或子网|
|/operations/read|轮询异步操作的状态。|
|/register/action|注册存储资源提供程序的订阅，并启用存储帐户的创建。|
|/skus/read|列出 Microsoft.Storage 支持的 SKU。|
|/storageAccounts/blobServices/containers/clearLegalHold/action|清除 blob 容器法定保留|
|/storageAccounts/blobServices/containers/delete|返回删除容器的结果|
|/storageAccounts/blobServices/containers/immutabilityPolicies/delete|删除 blob 容器不可变性策略|
|/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action|扩展 blob 容器不可变性策略|
|/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action|锁定 blob 容器不可变性策略|
|/storageAccounts/blobServices/containers/immutabilityPolicies/read|获取 blob 容器不可变性策略|
|/storageAccounts/blobServices/containers/immutabilityPolicies/write|放置 blob 容器不可变性策略|
|/storageAccounts/blobServices/containers/read|返回容器或容器列表|
|/storageAccounts/blobServices/containers/setLegalHold/action|设置 blob 容器法定保留|
|/storageAccounts/blobServices/containers/write|返回放置或租用 blob 容器的结果|
|/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read|获取资源的诊断设置。|
|/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新资源的诊断设置。|
|/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read|获取 Microsoft 存储指标定义列表。|
|/storageAccounts/blobServices/read|返回 blob 服务属性或统计信息|
|/storageAccounts/blobServices/write|返回放置 blob 服务属性的结果|
|/storageAccounts/delete|删除现有的存储帐户。|
|/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read|获取资源的诊断设置。|
|/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新资源的诊断设置。|
|/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read|获取 Microsoft 存储指标定义列表。|
|/storageAccounts/listAccountSas/action|返回指定存储帐户的帐户 SAS 令牌。|
|/storageAccounts/listkeys/action|返回指定存储帐户的访问密钥。|
|/storageAccounts/listServiceSas/action|返回指定存储帐户的服务 SAS 令牌。|
|/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read|获取资源的诊断设置。|
|/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新资源的诊断设置。|
|/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read|获取 Microsoft 存储指标定义列表。|
|/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新资源的诊断设置。|
|/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read|获取 Microsoft 存储指标定义列表。|
|/storageAccounts/queueServices/queues/delete|返回删除队列的结果|
|/storageAccounts/queueServices/queues/read|返回队列或队列列表。|
|/storageAccounts/queueServices/queues/write|返回写入队列的结果|
|/storageAccounts/queueServices/read|返回队列服务属性或统计信息。|
|/storageAccounts/queueServices/write|返回设置队列服务属性的结果|
|/storageAccounts/read|返回存储帐户的列表，或获取指定存储帐户的属性。|
|/storageAccounts/regeneratekey/action|再生成指定存储帐户的访问密钥。|
|/storageAccounts/services/diagnosticSettings/write|创建/更新存储帐户诊断设置。|
|/storageAccounts/storageAccounts/queueServices/providers/ Microsoft.Insights/diagnosticSettings/read|获取资源的诊断设置。|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read|获取资源的诊断设置。|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新资源的诊断设置。|
|/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read|获取 Microsoft 存储指标定义列表。|
|/storageAccounts/write|使用指定的参数创建存储帐户、更新指定存储帐户的属性或标记，或者为其添加自定义域。|
|/usages/read|返回指定订阅中的资源的限制和当前使用计数|

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

| Operation | 说明 |
|---|---|
|/storageSyncServices/delete|删除任何存储同步服务|
|/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read|获取存储同步服务的可用指标|
|/storageSyncServices/read|读取任何存储同步服务|
|/storageSyncServices/registeredServers/delete|删除任何已注册服务器|
|/storageSyncServices/registeredServers/read|读取任何已注册服务器|
|/storageSyncServices/registeredServers/write|创建或更新任何已注册服务器|
|/storageSyncServices/syncGroups/cloudEndpoints/delete|删除任何云终结点|
|/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read|异步备份调用的位置 API|
|/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action|备份后调用此操作|
|/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action|存储后调用此操作|
|/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action|备份前调用此操作|
|/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action|存储前调用此操作|
|/storageSyncServices/syncGroups/cloudEndpoints/read|读取任何云终结点|
|/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action|还原检测信号|
|/storageSyncServices/syncGroups/cloudEndpoints/write|创建或更新任何云终结点|
|/storageSyncServices/syncGroups/delete|删除任何同步组|
|/storageSyncServices/syncGroups/read|读取任何同步组|
|/storageSyncServices/syncGroups/serverEndpoints/delete|删除任何服务器终结点|
|/storageSyncServices/syncGroups/serverEndpoints/read|读取任何服务器终结点|
|/storageSyncServices/syncGroups/serverEndpoints/recallAction/action|调用此操作，将文件撤回到服务器|
|/storageSyncServices/syncGroups/serverEndpoints/write|创建或更新任何服务器终结点|
|/storageSyncServices/syncGroups/write|创建或更新任何同步组|
|/storageSyncServices/write|创建或更新任何存储同步服务|

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

| Operation | 说明 |
|---|---|
|/managers/accessControlRecords/delete|删除访问控制记录|
|/managers/accessControlRecords/read|列出或获取访问控制记录|
|/managers/accessControlRecords/write|创建或更新访问控制记录|
|/managers/alerts/read|列出或获取警报|
|/managers/bandwidthSettings/delete|删除现有的带宽设置（仅适用于 8000 系列）|
|/managers/bandwidthSettings/read|列出带宽设置（仅适用于 8000 系列）|
|/managers/bandwidthSettings/write|新建或更新带宽设置（仅适用于 8000 系列）|
|/Managers/certificates/write|“更新资源证书”操作更新资源/保管库凭据证书。|
|/managers/clearAlerts/action|清除与设备管理器关联的所有警报。|
|/managers/cloudApplianceConfigurations/read|列出云设备支持的配置|
|/managers/configureDevice/action|配置设备|
|/managers/delete|删除设备管理器|
|/Managers/delete|“删除保管库”操作删除“vault”类型的指定 Azure 资源|
|/managers/devices/alertSettings/read|列出或获取警报设置|
|/managers/devices/alertSettings/write|创建或更新警报设置|
|/managers/devices/backupPolicies/backup/action|执行手动备份以创建策略保护的所有卷的按需备份。|
|/managers/devices/backupPolicies/delete|删除现有的备份策略（仅适用于 8000 系列）|
|/managers/devices/backupPolicies/read|列出备份策略（仅适用于 8000 系列）|
|/managers/devices/backupPolicies/schedules/delete|删除现有的计划|
|/managers/devices/backupPolicies/schedules/read|列出计划|
|/managers/devices/backupPolicies/schedules/write|新建或更新计划|
|/managers/devices/backupPolicies/write|新建或更新备份策略（仅适用于 8000 系列）|
|/managers/devices/backups/delete|删除备份集|
|/managers/devices/backups/elements/clone/action|使用备份元素克隆共享或卷。|
|/managers/devices/backups/read|列出或获取备份集|
|/managers/devices/backups/restore/action|从备份集还原所有卷。|
|/managers/devices/backupScheduleGroups/delete|删除备份计划组|
|/managers/devices/backupScheduleGroups/read|列出或获取备份计划组|
|/managers/devices/backupScheduleGroups/write|创建或更新备份计划组|
|/managers/devices/chapSettings/delete|删除 Chap 设置|
|/managers/devices/chapSettings/read|列出或获取 Chap 设置|
|/managers/devices/chapSettings/write|创建或更新 Chap 设置|
|/managers/devices/deactivate/action|停用设备。|
|/managers/devices/delete|删除设备|
|/managers/devices/download/action|下载设备的更新。|
|/managers/devices/failover/action|设备故障转移。|
|/managers/devices/fileservers/backup/action|备份文件服务器。|
|/managers/devices/fileservers/delete|删除文件服务器|
|/managers/devices/fileservers/metrics/read|列出或获取指标|
|/managers/devices/fileservers/metricsDefinitions/read|列出或获取指标定义|
|/managers/devices/fileservers/read|列出或获取文件服务器|
|/managers/devices/fileservers/shares/delete|删除共享|
|/managers/devices/fileservers/shares/metrics/read|列出或获取指标|
|/managers/devices/fileservers/shares/metricsDefinitions/read|列出或获取指标定义|
|/managers/devices/fileservers/shares/read|列出或获取共享|
|/managers/devices/fileservers/shares/write|创建或更新共享|
|/managers/devices/fileservers/write|创建或更新文件服务器|
|/managers/devices/hardwareComponentGroups/changeControllerPowerState/action|更改硬件组件组的控制器电源状态|
|/managers/devices/hardwareComponentGroups/read|列出硬件组件组|
|/managers/devices/install/action|在设备上安装更新。|
|/managers/devices/installUpdates/action|在设备上安装更新|
|/managers/devices/iscsiservers/backup/action|备份 iSCSI 服务器。|
|/managers/devices/iscsiservers/delete|删除 iSCSI 服务器|
|/managers/devices/iscsiservers/disks/delete|删除磁盘|
|/managers/devices/iscsiservers/disks/metrics/read|列出或获取指标|
|/managers/devices/iscsiservers/disks/metricsDefinitions/read|列出或获取指标定义|
|/managers/devices/iscsiservers/disks/read|列出或获取磁盘|
|/managers/devices/iscsiservers/disks/write|创建或更新磁盘|
|/managers/devices/iscsiservers/metrics/read|列出或获取指标|
|/managers/devices/iscsiservers/metricsDefinitions/read|列出或获取指标定义|
|/managers/devices/iscsiservers/read|列出或获取 iSCSI 服务器|
|/managers/devices/iscsiservers/write|创建或更新 iSCSI 服务器|
|/managers/devices/jobs/cancel/action|取消正在运行的作业|
|/managers/devices/jobs/read|列出或获取作业|
|/managers/devices/listFailoverSets/action|列出现有设备的故障转移集。|
|/managers/devices/listFailoverTargets/action|列出设备的故障转移目标|
|/managers/devices/metrics/read|列出或获取指标|
|/managers/devices/metricsDefinitions/read|列出或获取指标定义|
|/managers/devices/migrationSourceConfigurations/confirmMigration/action|确认成功迁移并提交结果。|
|/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action|提取迁移的确认状态。|
|/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action|提取迁移估计作业的状态。|
|/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action|提取迁移的状态。|
|/managers/devices/migrationSourceConfigurations/import/action|导入要迁移的源配置|
|/managers/devices/migrationSourceConfigurations/startMigration/action|使用源配置开始迁移|
|/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action|启动作业来估计迁移过程的持续时间。|
|/managers/devices/networkSettings/read|列出或获取网络设置|
|/managers/devices/networkSettings/write|新建或更新网络设置|
|/managers/devices/publicEncryptionKey/action|列出设备管理器的加密公钥|
|/managers/devices/publishSupportPackage/action|发布设备的支持包供 Microsoft 支持人员进行故障排除。|
|/managers/devices/read|列出或获取设备|
|/managers/devices/scanForUpdates/action|扫描设备中的更新。|
|/managers/devices/securitySettings/read|列出安全设置|
|/managers/devices/securitySettings/syncRemoteManagementCertificate/action|同步设备的远程管理证书。|
|/managers/devices/securitySettings/update/action|更新安全设置。|
|/managers/devices/securitySettings/write|新建或更新安全设置|
|/managers/devices/sendTestAlertEmail/action|将测试警报电子邮件发送到配置的电子邮件收件人。|
|/managers/devices/timeSettings/read|列出或获取时间设置|
|/managers/devices/timeSettings/write|新建或更新时间设置|
|/managers/devices/updateSummary/read|列出或获取更新摘要|
|/managers/devices/volumeContainers/delete|删除现有的卷容器（仅适用于 8000 系列）|
|/managers/devices/volumeContainers/listEncryptionKeys/action|列出卷容器的加密密钥|
|/managers/devices/volumeContainers/metrics/read|列出指标|
|/managers/devices/volumeContainers/metricsDefinitions/read|列出指标定义|
|/managers/devices/volumeContainers/read|列出卷容器（仅适用于 8000 系列）|
|/managers/devices/volumeContainers/rolloverEncryptionKey/action|滚动更新卷容器的加密密钥|
|/managers/devices/volumeContainers/volumes/delete|删除现有的卷|
|/managers/devices/volumeContainers/volumes/metrics/read|列出指标|
|/managers/devices/volumeContainers/volumes/metricsDefinitions/read|列出指标定义|
|/managers/devices/volumeContainers/volumes/read|列出卷|
|/managers/devices/volumeContainers/volumes/write|新建或更新卷|
|/managers/devices/volumeContainers/write|新建或更新卷容器（仅适用于 8000 系列）|
|/managers/devices/write|创建或更新设备|
|/managers/encryptionSettings/read|列出或获取加密设置|
|/Managers/extendedInformation/delete|“获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象的扩展信息|
|/Managers/extendedInformation/read|“获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象的扩展信息|
|/Managers/extendedInformation/write|“获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象的扩展信息|
|/managers/getActivationKey/action|获取设备管理器的激活密钥。|
|/managers/getEncryptionKey/action|获取设备管理器的加密密钥。|
|/managers/listActivationKey/action|获取 StorSimple 设备管理器的激活密钥。|
|/managers/listPrivateEncryptionKey/action|获取 StorSimple 设备管理器的加密私钥。|
|/managers/listPublicEncryptionKey/action|列出 StorSimple 设备管理器的加密公钥。|
|/managers/metrics/read|列出或获取指标|
|/managers/metricsDefinitions/read|列出或获取指标定义|
|/managers/provisionCloudAppliance/action|创建新的云设备。|
|/managers/read|列出或获取设备管理器|
|/Managers/read|“获取保管库”操作获取表示“vault”类型的 Azure 资源的对象|
|/managers/regenarateRegistationCertificate/action|再生成设备管理器的注册证书。|
|/managers/regenerateActivationKey/action|再生成设备管理器的激活密钥。|
|/managers/storageAccountCredentials/delete|删除存储帐户凭据|
|/managers/storageAccountCredentials/listAccessKey/action|列出存储帐户凭据的访问密钥|
|/managers/storageAccountCredentials/read|列出或获取存储帐户凭据|
|/managers/storageAccountCredentials/write|创建或更新存储帐户凭据|
|/managers/storageDomains/delete|删除存储域|
|/managers/storageDomains/read|列出或获取存储域|
|/managers/storageDomains/write|创建或更新存储域|
|/managers/write|创建或更新设备管理器|
|/Managers/write|“创建保管库”操作创建“vault”类型的 Azure 资源|

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

| Operation | 说明 |
|---|---|
|/locations/quotas/Read|读取流分析订阅配额|
|/operations/Read|读取流分析操作|
|/Register/action|使用流分析资源提供程序注册订阅|
|/streamingjobs/Delete|删除流分析作业|
|/streamingjobs/functions/Delete|删除流分析作业函数|
|/streamingjobs/functions/operationresults/Read|读取流分析作业函数的操作结果|
|/streamingjobs/functions/Read|读取流分析作业函数|
|/streamingjobs/functions/RetrieveDefaultDefinition/action|检索流分析作业函数的默认定义|
|/streamingjobs/functions/Test/action|测试流分析作业函数|
|/streamingjobs/functions/Write|写入流分析作业函数|
|/streamingjobs/inputs/Delete|删除流分析作业输入|
|/streamingjobs/inputs/operationresults/Read|读取流分析作业输入的操作结果|
|/streamingjobs/inputs/Read|读取流分析作业输入|
|/streamingjobs/inputs/Sample/action|流分析作业输入采样|
|/streamingjobs/inputs/Test/action|测试流分析作业输入|
|/streamingjobs/inputs/Write|写入流分析作业输入|
|/streamingjobs/metricdefinitions/Read|读取指标定义|
|/streamingjobs/operationresults/Read|读取流分析作业的操作结果|
|/streamingjobs/outputs/Delete|删除流分析作业输出|
|/streamingjobs/outputs/operationresults/Read|读取流分析作业输出的操作结果|
|/streamingjobs/outputs/Read|读取流分析作业输出|
|/streamingjobs/outputs/Test/action|测试流分析作业输出|
|/streamingjobs/outputs/Write|写入流分析作业输出|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read|读取诊断设置。|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write|写入诊断设置。|
|/streamingjobs/providers/Microsoft.Insights/logDefinitions/read|获取 streamingjobs 的可用日志|
|/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read|获取 streamingjobs 的可用指标|
|/streamingjobs/Read|读取流分析作业|
|/streamingjobs/Start/action|启动流分析作业|
|/streamingjobs/Stop/action|停止流分析作业|
|/streamingjobs/transformations/Delete|删除流分析作业转换|
|/streamingjobs/transformations/Read|读取流分析作业转换|
|/streamingjobs/transformations/Write|写入流分析作业转换|
|/streamingjobs/Write|写入流分析作业|

## <a name="microsoftsubscription"></a>Microsoft.Subscription

| Operation | 说明 |
|---|---|
|/SubscriptionDefinitions/read|获取管理组中的 Azure 订阅定义。|
|/SubscriptionDefinitions/write|创建 Azure 订阅定义|

## <a name="microsoftsupport"></a>Microsoft.Support

| Operation | 说明 |
|---|---|
|/register/action|注册到支持资源提供程序|
|/supportTickets/read|获取支持票证详细信息（包括状态、严重性、联系详细信息和通信），或获取各个订阅中的支持票证列表。|
|/supportTickets/write|创建或更新支持票证。 可以针对技术、计费、配额或订阅管理相关的问题创建支持票证。 可以更新现有支持票证的严重性、联系详细信息和通信。|

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

| Operation | 说明 |
|---|---|
|/environments/accesspolicies/delete|删除访问策略。|
|/environments/accesspolicies/read|获取访问策略的属性。|
|/environments/accesspolicies/write|为环境创建新的访问策略，或更新现有访问策略。|
|/environments/delete|删除环境。|
|/environments/eventsources/delete|删除事件源。|
|/environments/eventsources/eventsources/providers/Microsoft.Insights/ diagnosticSettings/write|创建或更新资源的诊断设置|
|/environments/eventsources/providers/Microsoft.Insights/diagnosticSettings/read|获取资源的诊断设置|
|/environments/eventsources/providers/Microsoft.Insights/metricDefinitions/read|获取 eventsources 的可用指标|
|/environments/eventsources/read|获取事件源的属性。|
|/environments/eventsources/write|为环境创建新的事件源，或更新现有事件源。|
|/environments/providers/Microsoft.Insights/diagnosticSettings/read|获取资源的诊断设置|
|/environments/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新资源的诊断设置|
|/environments/providers/Microsoft.Insights/metricDefinitions/read|获取环境的可用指标|
|/environments/read|获取环境的属性。|
|/environments/referencedatasets/delete|删除参考数据集。|
|/environments/referencedatasets/read|获取参考数据集的属性。|
|/environments/referencedatasets/write|为环境创建新的参考数据集，或更新现有参考数据集。|
|/environments/status/read|获取环境的状态以及相关操作（如入口）的状态。|
|/environments/write|创建新环境，或更新现有环境。|
|/register/action|注册时序见解资源提供程序的订阅，并启用时序见解环境的创建。|

## <a name="microsoftweb"></a>microsoft.web

| Operation | 说明 |
|---|---|
|/apimanagementaccounts/apiacls/read|获取 API 管理帐户 Apiacls。|
|/apimanagementaccounts/apis/apiacls/delete|删除 API 管理帐户 API Apiacls。|
|/apimanagementaccounts/apis/apiacls/read|获取 API 管理帐户 API Apiacls。|
|/apimanagementaccounts/apis/apiacls/write|更新 API 管理帐户 API Apiacls。|
|/apimanagementaccounts/apis/connectionacls/read|获取 API 管理帐户 API Connectionacls。|
|/apimanagementaccounts/apis/connections/confirmconsentcode/action|确认同意代码 API 管理帐户 API 连接。|
|/apimanagementaccounts/apis/connections/connectionacls/delete|删除 API 管理帐户 API 连接 Connectionacls。|
|/apimanagementaccounts/apis/connections/connectionacls/read|获取 API 管理帐户 API 连接 Connectionacls。|
|/apimanagementaccounts/apis/connections/connectionacls/write|更新 API 管理帐户 API 连接 Connectionacls。|
|/apimanagementaccounts/apis/connections/delete|删除 API 管理帐户 API 连接。|
|/apimanagementaccounts/apis/connections/getconsentlinks/action|获取 API 管理帐户 API 连接的同意链接。|
|/apimanagementaccounts/apis/connections/listconnectionkeys/action|列出连接密钥 API 管理帐户 API 连接。|
|/apimanagementaccounts/apis/connections/listsecrets/action|列出机密 API 管理帐户 API 连接。|
|/apimanagementaccounts/apis/connections/read|获取 API 管理帐户 API 连接。|
|/apimanagementaccounts/apis/connections/write|更新 API 管理帐户 API 连接。|
|/apimanagementaccounts/apis/delete|删除 API 管理帐户 API。|
|/apimanagementaccounts/apis/localizeddefinitions/delete|删除 API 管理帐户 API 本地化定义。|
|/apimanagementaccounts/apis/localizeddefinitions/read|获取 API 管理帐户 API 本地化定义。|
|/apimanagementaccounts/apis/localizeddefinitions/write|更新 API 管理帐户 API 本地化定义。|
|/apimanagementaccounts/apis/read|获取 API 管理帐户 API。|
|/apimanagementaccounts/apis/write|更新 API 管理帐户 API。|
|/apimanagementaccounts/connectionacls/read|获取 API 管理帐户 Connectionacls。|
|/availablestacks/read|获取可用堆栈。|
|/billingmeters/read|获取账单计量的列表。|
|/certificates/Delete|删除现有证书。|
|/certificates/Read|获取证书列表。|
|/certificates/Write|添加新的或更新现有的证书。|
|/checknameavailability/read|检查资源名称是否可用。|
|/classicmobileservices/read|获取经典移动服务。|
|/connectionGateways/Delete|删除连接网关。|
|/connectionGateways/Join/Action|加入连接网关。|
|/connectiongateways/liststatus/action|列出状态连接网关。|
|/connectionGateways/ListStatus/Action|列出连接网关的状态。|
|/connectionGateways/Move/Action|移动连接网关。|
|/connectionGateways/Read|获取连接网关列表。|
|/connectionGateways/Write|创建或更新连接网关。|
|/connections/confirmconsentcode/action|确认连接许可代码。|
|/connections/Delete|删除连接。|
|/connections/Join/Action|加入连接。|
|/connections/listconsentlinks/action|列出连接的许可链接。|
|/connections/Move/Action|移动连接。|
|/connections/Read|获取连接列表。|
|/connections/Write|创建或更新连接。|
|/customApis/Delete|删除自定义 API。|
|/customApis/extractApiDefinitionFromWsdl/Action|从 WSDL 中提取 API 定义。|
|/customApis/Join/Action|加入自定义 API。|
|/customApis/listWsdlInterfaces/Action|列出自定义 API 的 WSDL 接口。|
|/customApis/Move/Action|移动自定义 API。|
|/customApis/Read|获取自定义 API 的列表。|
|/customApis/Write|创建或更新自定义 API。|
|/deploymentlocations/read|获取部署位置。|
|/geoRegions/Read|获取地理区域的列表。|
|/hostingenvironments/capacities/read|获取宿主环境容量。|
|/hostingEnvironments/Delete|删除应用服务环境|
|/hostingenvironments/diagnostics/read|获取宿主环境诊断。|
|/hostingenvironments/inboundnetworkdependenciesendpoints/read|获取所有入站依赖项的网络终结点。|
|/hostingenvironments/metricdefinitions/read|获取宿主环境的指标定义。|
|/hostingenvironments/multirolepools/metricdefinitions/read|获取宿主环境的多角色池指标定义。|
|/hostingenvironments/multirolepools/metrics/read|获取宿主环境的多角色池指标。|
|/hostingEnvironments/multiRolePools/providers/Microsoft.Insights/ metricDefinitions/Read|获取应用服务环境多角色的可用指标|
|/hostingEnvironments/multiRolePools/Read|获取应用服务环境中前端池的属性|
|/hostingenvironments/multirolepools/skus/read|获取宿主环境的多角色池 SKU。|
|/hostingenvironments/multirolepools/usages/read|获取宿主环境的多角色池使用情况。|
|/hostingEnvironments/multiRolePools/Write|在应用服务环境中创建新的或更新现有的前端池|
|/hostingenvironments/operations/read|获取宿主环境操作。|
|/hostingenvironments/outboundnetworkdependenciesendpoints/read|获取所有出站依赖项的网络终结点。|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/read|获取资源的诊断设置|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新资源的诊断设置|
|/hostingEnvironments/Read|获取应用服务环境的属性|
|/hostingEnvironments/reboot/Action|重新启动应用服务环境中的所有计算机|
|/hostingenvironments/resume/action|恢复宿主环境。|
|/hostingenvironments/serverfarms/read|获取宿主环境的应用服务计划。|
|/hostingenvironments/sites/read|获取宿主环境的 Web 应用。|
|/hostingenvironments/suspend/action|暂停宿主环境。|
|/hostingenvironments/usages/read|获取宿主环境使用情况。|
|/hostingenvironments/workerpools/metricdefinitions/read|获取宿主环境的辅助角色池指标定义。|
|/hostingenvironments/workerpools/metrics/read|获取宿主环境的辅助角色池指标。|
|/hostingEnvironments/workerPools/providers/Microsoft.Insights/metricDefinitions/Read|获取应用服务环境角色池的可用指标|
|/hostingEnvironments/workerPools/Read|获取应用服务环境中辅助角色池的属性|
|/hostingenvironments/workerpools/skus/read|获取宿主环境的辅助角色池 SKU。|
|/hostingenvironments/workerpools/usages/read|获取宿主环境的辅助角色池使用情况。|
|/hostingEnvironments/workerPools/Write|在应用服务环境中创建新的或更新现有的辅助角色池|
|/hostingEnvironments/Write|创建新的或更新现有的应用服务环境|
|/ishostingenvironmentnameavailable/read|检查宿主环境名称是否可用。|
|/ishostnameavailable/read|检查主机名是否可用。|
|/isusernameavailable/read|检查用户名是否可用。|
|/listSitesAssignedToHostName/Read|获取分配给主机名的站点名称。|
|/locations/apioperations/read|获取位置 API 操作。|
|/locations/connectiongatewayinstallations/read|获取位置连接网关安装。|
|/locations/extractapidefinitionfromwsdl/action|从位置的 WSDL 中提取 API 定义。|
|/locations/listwsdlinterfaces/action|列出位置的 WSDL 接口。|
|/locations/managedapis/apioperations/read|获取位置托管 API 操作。|
|/locations/managedapis/Join/Action|联接托管的 API。|
|/locations/managedapis/read|获取位置托管 API。|
|/operations/read|获取操作。|
|/publishingusers/read|获取发布用户。|
|/publishingusers/write|更新发布用户。|
|/recommendations/Read|获取订阅的建议列表。|
|/register/action|注册订阅的 Microsoft.Web 资源提供程序。|
|/resourcehealthmetadata/read|获取资源运行状况元数据。|
|/serverfarms/capabilities/read|获取应用服务计划功能。|
|/serverfarms/Delete|删除现有的应用服务计划|
|/serverfarms/firstpartyapps/settings/delete|删除应用服务计划第一方应用设置。|
|/serverfarms/firstpartyapps/settings/read|获取应用服务计划第一方应用设置。|
|/serverfarms/firstpartyapps/settings/write|更新应用服务计划第一方应用设置。|
|/serverfarms/hybridconnectionnamespaces/relays/delete|删除应用服务计划混合连接命名空间中继。|
|/serverfarms/hybridconnectionnamespaces/relays/read|获取应用服务计划混合连接命名空间中继。|
|/serverfarms/hybridconnectionnamespaces/relays/sites/read|获取应用服务计划混合连接命名空间中继 Web 应用。|
|/serverfarms/hybridconnectionplanlimits/read|获取应用服务计划混合连接计划限制。|
|/serverfarms/hybridconnectionrelays/read|获取应用服务计划混合连接中继。|
|/serverfarms/metricdefinitions/read|获取应用服务计划指标定义。|
|/serverfarms/metrics/read|获取应用服务计划指标。|
|/serverfarms/operationresults/read|获取应用服务计划操作结果。|
|/serverfarms/providers/Microsoft.Insights/diagnosticSettings/read|获取资源的诊断设置|
|/serverfarms/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新资源的诊断设置|
|/serverfarms/providers/Microsoft.Insights/metricDefinitions/Read|获取应用服务计划的可用指标|
|/serverfarms/Read|获取应用服务计划的属性|
|/serverfarms/restartSites/Action|重新启动应用服务计划中的所有 Web 应用|
|/serverfarms/sites/read|获取应用服务计划 Web 应用。|
|/serverfarms/skus/read|获取应用服务计划 SKU。|
|/serverfarms/usages/read|获取应用服务计划使用情况。|
|/serverfarms/virtualnetworkconnections/gateways/write|更新应用服务计划虚拟网络连接网关。|
|/serverfarms/virtualnetworkconnections/read|获取应用服务计划虚拟网络连接。|
|/serverfarms/virtualnetworkconnections/routes/delete|删除应用服务计划虚拟网络连接路由。|
|/serverfarms/virtualnetworkconnections/routes/read|获取应用服务计划虚拟网络连接路由。|
|/serverfarms/virtualnetworkconnections/routes/write|更新应用服务计划虚拟网络连接路由。|
|/serverfarms/workers/reboot/action|重新启动应用服务计划辅助角色。|
|/serverfarms/Write|创建新的或更新现有的应用服务计划。|
|/sites/analyzecustomhostname/read|分析自定义主机名。|
|/sites/applySlotConfig/Action|将目标槽中的 Web 应用槽配置应用到当前 Web 应用|
|/sites/backup/Action|创建新的 Web 应用备份|
|/sites/backup/read|获取 Web 应用备份。|
|/sites/backup/write|更新 Web 应用备份。|
|/sites/backups/delete|删除 Web 应用备份。|
|/sites/backups/list/action|列出 Web 应用备份。|
|/sites/backups/Read|获取 Web 应用的备份属性|
|/sites/backups/restore/action|还原 Web 应用备份。|
|/sites/config/delete|删除 Web 应用配置。|
|/sites/config/list/Action|列出 Web 应用的安全敏感设置，例如发布凭据、应用设置和连接字符串|
|/sites/config/Read|获取 Web 应用配置设置|
|/sites/config/Write|更新 Web 应用的配置设置|
|/sites/continuouswebjobs/delete|删除 Web 应用连续 Web 作业。|
|/sites/continuouswebjobs/read|获取 Web 应用连续 Web 作业。|
|/sites/continuouswebjobs/start/action|启动 Web 应用连续 Web 作业。|
|/sites/continuouswebjobs/stop/action|停止 Web 应用连续 Web 作业。|
|/sites/Delete|删除现有的 Web 应用|
|/sites/deployments/delete|删除 Web 应用部署。|
|/sites/deployments/log/read|获取 Web 应用部署日志。|
|/sites/deployments/read|获取 Web 应用部署。|
|/sites/deployments/write|更新 Web 应用部署。|
|/sites/diagnostics/analyses/execute/Action|运行 Web 应用诊断分析。|
|/sites/diagnostics/analyses/read|获取 Web 应用诊断分析。|
|/sites/diagnostics/aspnetcore/read|获取 ASP.NET Core 应用的 Web 应用诊断。|
|/sites/diagnostics/autoheal/read|获取 Web 应用诊断 Autoheal。|
|/sites/diagnostics/deployment/read|获取 Web 应用诊断部署。|
|/sites/diagnostics/deployments/read|获取 Web 应用诊断部署。|
|/sites/diagnostics/detectors/execute/Action|运行 Web 应用诊断检测程序。|
|/sites/diagnostics/detectors/read|获取 Web 应用诊断检测程序。|
|/sites/diagnostics/failedrequestsperuri/read|获取每个 Uri 的 Web 应用诊断失败的请求。|
|/sites/diagnostics/frebanalysis/read|获取 Web 应用诊断 FREB 分析。|
|/sites/diagnostics/loganalyzer/read|获取 Web 应用诊断日志分析器。|
|/sites/diagnostics/read|获取 Web 应用诊断类别。|
|/sites/diagnostics/runtimeavailability/read|获取 Web 应用诊断运行时可用性。|
|/sites/diagnostics/servicehealth/read|获取 Web 应用诊断服务运行状况。|
|/sites/diagnostics/sitecpuanalysis/read|获取 Web 应用诊断站点 CPU 分析。|
|/sites/diagnostics/sitecrashes/read|获取 Web 应用诊断站点故障。|
|/sites/diagnostics/sitelatency/read|获取 Web 应用诊断站点延迟。|
|/sites/diagnostics/sitememoryanalysis/read|获取 Web 应用诊断站点内存分析。|
|/sites/diagnostics/siterestartsettingupdate/read|获取 Web 应用诊断站点重启设置更新。|
|/sites/diagnostics/siterestartuserinitiated/read|获取 Web 应用诊断站点重启用户发起时间。|
|/sites/diagnostics/siteswap/read|获取 Web 应用诊断站点交换。|
|/sites/diagnostics/threadcount/read|获取 Web 应用诊断线程计数。|
|/sites/diagnostics/workeravailability/read|获取 Web 应用诊断 Workeravailability。|
|/sites/diagnostics/workerprocessrecycle/read|获取 Web 应用诊断工作进程回收。|
|/sites/domainownershipidentifiers/read|获取 Web 应用域所有权标识符。|
|/sites/domainownershipidentifiers/write|更新 Web 应用域所有权标识符。|
|/sites/functions/action|函数 Web 应用。|
|/sites/functions/delete|删除 Web 应用函数。|
|/sites/functions/listsecrets/action|列出机密 Web 应用函数。|
|/sites/functions/masterkey/read|获取 Web 应用函数主密钥。|
|/sites/functions/read|获取 Web 应用函数。|
|/sites/functions/token/read|获取 Web 应用函数令牌。|
|/sites/functions/write|更新 Web 应用函数。|
|/sites/hostnamebindings/delete|删除 Web 应用主机名绑定。|
|/sites/hostnamebindings/read|获取 Web 应用主机名绑定。|
|/sites/hostnamebindings/write|更新 Web 应用主机名绑定。|
|/sites/hybridconnection/delete|删除 Web 应用混合连接。|
|/sites/hybridconnection/read|获取 Web 应用混合连接。|
|/sites/hybridconnection/write|更新 Web 应用混合连接。|
|/sites/hybridconnectionnamespaces/relays/delete|删除 Web 应用混合连接命名空间中继。|
|/sites/hybridconnectionnamespaces/relays/listkeys/action|列出密钥 Web 应用混合连接命名空间中继。|
|/sites/hybridconnectionnamespaces/relays/read|获取 Web 应用混合连接命名空间中继。|
|/sites/hybridconnectionnamespaces/relays/write|更新 Web 应用混合连接命名空间中继。|
|/sites/hybridconnectionrelays/read|获取 Web 应用混合连接中继。|
|/sites/instances/deployments/delete|删除 Web 应用实例部署。|
|/sites/instances/deployments/read|获取 Web 应用实例部署。|
|/sites/instances/extensions/log/read|获取 Web 应用实例扩展日志。|
|/sites/instances/extensions/read|获取 Web 应用实例扩展。|
|/sites/instances/processes/delete|删除 Web 应用实例进程。|
|/sites/instances/processes/read|获取 Web 应用实例进程。|
|/sites/instances/read|获取 Web 应用实例。|
|/sites/listsyncfunctiontriggerstatus/action|列出同步函数触发器状态 Web 应用。|
|/sites/metricdefinitions/read|获取 Web 应用指标定义。|
|/sites/metrics/read|获取 Web 应用指标。|
|/sites/metricsdefinitions/read|获取 Web 应用指标定义。|
|/sites/migratemysql/action|迁移 MySql Web 应用。|
|/sites/migratemysql/read|获取 Web 应用迁移 MySql。|
|/sites/networktrace/action|网络跟踪 Web 应用。|
|/sites/newpassword/action|Newpassword Web 应用。|
|/sites/operationresults/read|获取 Web 应用操作结果。|
|/sites/operations/read|获取 Web 应用操作。|
|/sites/perfcounters/read|获取 Web 应用性能计数器。|
|/sites/premieraddons/delete|删除 Web 应用高级加载项。|
|/sites/premieraddons/read|获取 Web 应用高级加载项。|
|/sites/premieraddons/write|更新 Web 应用高级加载项。|
|/sites/processes/read|获取 Web 应用进程。|
|/sites/providers/Microsoft.Insights/diagnosticSettings/read|获取资源的诊断设置|
|/sites/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新资源的诊断设置|
|/sites/providers/Microsoft.Insights/metricDefinitions/Read|获取 Web 应用的可用指标|
|/sites/publiccertificates/delete|删除 Web 应用公共证书。|
|/sites/publiccertificates/read|获取 Web 应用公共证书。|
|/sites/publiccertificates/write|更新 Web 应用公共证书。|
|/sites/publish/Action|发布 Web 应用|
|/sites/publishxml/Action|获取 Web 应用的发布配置文件 xml|
|/sites/publishxml/read|获取 Web 应用发布 XML。|
|/sites/Read|获取 Web 应用的属性|
|/sites/recommendationhistory/read|获取 Web 应用建议历史记录。|
|/sites/recommendations/disable/action|禁用 Web 应用建议。|
|/sites/recommendations/Read|获取 Web 应用的建议列表。|
|/sites/recover/action|恢复 Web 应用。|
|/sites/resetSlotConfig/Action|重置 Web 应用配置|
|/sites/resourcehealthmetadata/read|获取 Web 应用资源运行状况元数据。|
|/sites/restart/Action|重新启动 Web 应用|
|/sites/restore/read|获取 Web 应用还原设置。|
|/sites/restore/write|还原 Web 应用。|
|/sites/siteextensions/delete|删除 Web 应用站点扩展。|
|/sites/siteextensions/read|获取 Web 应用站点扩展。|
|/sites/siteextensions/write|更新 Web 应用站点扩展。|
|/sites/slots/analyzecustomhostname/read|获取 Web 应用槽分析自定义主机名。|
|/sites/slots/applySlotConfig/Action|将目标槽中的 Web 应用槽配置应用到当前槽|
|/sites/slots/backup/Action|创建新的 Web 应用槽备份。|
|/sites/slots/backup/read|获取 Web 应用槽备份。|
|/sites/slots/backup/write|更新 Web 应用槽备份。|
|/sites/slots/backups/delete|删除 Web 应用槽备份。|
|/sites/slots/backups/list/action|列出 Web 应用槽备份。|
|/sites/slots/backups/Read|获取 Web 应用槽的备份属性|
|/sites/slots/backups/restore/action|还原 Web 应用槽备份。|
|/sites/slots/config/delete|删除 Web 应用槽配置。|
|/sites/slots/config/list/Action|列出 Web 应用槽的安全敏感设置，例如发布凭据、应用设置和连接字符串|
|/sites/slots/config/Read|获取 Web 应用槽的配置设置|
|/sites/slots/config/Write|更新 Web 应用槽的配置设置|
|/sites/slots/continuouswebjobs/delete|删除 Web 应用槽连续 Web 作业。|
|/sites/slots/continuouswebjobs/read|获取 Web 应用槽连续 Web 作业。|
|/sites/slots/continuouswebjobs/start/action|启动 Web 应用槽连续 Web 作业。|
|/sites/slots/continuouswebjobs/stop/action|停止 Web 应用槽连续 Web 作业。|
|/sites/slots/Delete|删除现有的 Web 应用槽|
|/sites/slots/deployments/delete|删除 Web 应用槽部署。|
|/sites/slots/deployments/log/read|获取 Web 应用槽部署日志。|
|/sites/slots/deployments/read|获取 Web 应用槽部署。|
|/sites/slots/deployments/write|更新 Web 应用槽部署。|
|/sites/slots/diagnostics/analyses/execute/Action|运行 Web 应用槽诊断分析。|
|/sites/slots/diagnostics/analyses/read|获取 Web 应用槽诊断分析。|
|/sites/slots/diagnostics/aspnetcore/read|获取 ASP.NET Core 应用的 Web 应用槽诊断。|
|/sites/slots/diagnostics/autoheal/read|获取 Web 应用槽诊断 Autoheal。|
|/sites/slots/diagnostics/deployment/read|获取 Web 应用槽诊断部署。|
|/sites/slots/diagnostics/deployments/read|获取 Web 应用槽诊断部署。|
|/sites/slots/diagnostics/detectors/execute/Action|运行 Web 应用槽诊断检测程序。|
|/sites/slots/diagnostics/detectors/read|获取 Web 应用槽诊断检测程序。|
|/sites/slots/diagnostics/frebanalysis/read|获取 Web 应用槽诊断 FREB 分析。|
|/sites/slots/diagnostics/loganalyzer/read|获取 Web 应用槽诊断日志分析器。|
|/sites/slots/diagnostics/read|获取 Web 应用槽诊断。|
|/sites/slots/diagnostics/runtimeavailability/read|获取 Web 应用槽诊断运行时可用性。|
|/sites/slots/diagnostics/servicehealth/read|获取 Web 应用槽诊断服务运行状况。|
|/sites/slots/diagnostics/sitecpuanalysis/read|获取 Web 应用槽诊断站点 CPU 分析。|
|/sites/slots/diagnostics/sitecrashes/read|获取 Web 应用槽诊断站点故障。|
|/sites/slots/diagnostics/sitelatency/read|获取 Web 应用槽诊断站点延迟。|
|/sites/slots/diagnostics/sitememoryanalysis/read|获取 Web 应用槽诊断站点内存分析。|
|/sites/slots/diagnostics/siterestartsettingupdate/read|获取 Web 应用槽诊断站点重启设置更新。|
|/sites/slots/diagnostics/siterestartuserinitiated/read|获取 Web 应用槽诊断站点重启用户发起时间。|
|/sites/slots/diagnostics/siteswap/read|获取 Web 应用槽诊断站点交换。|
|/sites/slots/diagnostics/threadcount/read|获取 Web 应用槽诊断线程计数。|
|/sites/slots/diagnostics/workeravailability/read|获取 Web 应用槽诊断 Workeravailability。|
|/sites/slots/diagnostics/workerprocessrecycle/read|获取 Web 应用槽诊断工作进程回收。|
|/sites/slots/domainownershipidentifiers/read|获取 Web 应用槽域所有权标识符。|
|/sites/slots/hostnamebindings/delete|删除 Web 应用槽主机名绑定。|
|/sites/slots/hostnamebindings/read|获取 Web 应用槽主机名绑定。|
|/sites/slots/hostnamebindings/write|更新 Web 应用槽主机名绑定。|
|/sites/slots/hybridconnection/delete|删除 Web 应用槽混合连接。|
|/sites/slots/hybridconnection/read|获取 Web 应用槽混合连接。|
|/sites/slots/hybridconnection/write|更新 Web 应用槽混合连接。|
|/sites/slots/hybridconnectionnamespaces/relays/delete|删除 Web 应用槽混合连接命名空间中继。|
|/sites/slots/hybridconnectionnamespaces/relays/write|更新 Web 应用槽混合连接命名空间中继。|
|/sites/slots/hybridconnectionrelays/read|获取 Web 应用槽混合连接中继。|
|/sites/slots/instances/deployments/read|获取 Web 应用槽实例部署。|
|/sites/slots/instances/processes/delete|删除 Web 应用槽实例进程。|
|/sites/slots/instances/processes/read|获取 Web 应用槽实例进程。|
|/sites/slots/instances/read|获取 Web 应用槽实例。|
|/sites/slots/metricdefinitions/read|获取 Web 应用槽指标定义。|
|/sites/slots/metrics/read|获取 Web 应用槽指标。|
|/sites/slots/migratemysql/read|获取 Web 应用槽迁移 MySql。|
|/sites/slots/networktrace/action|网络跟踪 Web 应用槽。|
|/sites/slots/newpassword/action|Newpassword Web 应用槽。|
|/sites/slots/operationresults/read|获取 Web 应用槽操作结果。|
|/sites/slots/operations/read|获取 Web 应用槽操作。|
|/sites/slots/perfcounters/read|获取 Web 应用槽性能计数器。|
|/sites/slots/phplogging/read|获取 Web 应用槽 Phplogging。|
|/sites/slots/premieraddons/delete|删除 Web 应用槽高级加载项。|
|/sites/slots/premieraddons/read|获取 Web 应用槽高级加载项。|
|/sites/slots/premieraddons/write|更新 Web 应用槽高级加载项。|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/read|获取资源的诊断设置|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/write|创建或更新资源的诊断设置|
|/sites/slots/providers/Microsoft.Insights/metricDefinitions/Read|获取 Web 应用槽的可用指标|
|/sites/slots/publiccertificates/read|获取 Web 应用槽公共证书。|
|/sites/slots/publiccertificates/write|创建或更新 Web 应用槽公共证书。|
|/sites/slots/publish/Action|发布 Web 应用槽|
|/sites/slots/publishxml/Action|获取 Web 应用槽的发布配置文件 xml|
|/sites/slots/Read|获取 Web 应用部署槽的属性|
|/sites/slots/resetSlotConfig/Action|重置 Web 应用槽配置|
|/sites/slots/resourcehealthmetadata/read|获取 Web 应用槽资源运行状况元数据。|
|/sites/slots/restart/Action|重新启动 Web 应用槽|
|/sites/slots/restore/read|获取 Web 应用槽还原设置。|
|/sites/slots/restore/write|还原 Web 应用槽。|
|/sites/slots/siteextensions/delete|删除 Web 应用槽站点扩展。|
|/sites/slots/siteextensions/read|获取 Web 应用槽站点扩展。|
|/sites/slots/siteextensions/write|更新 Web 应用槽站点扩展。|
|/sites/slots/slotsdiffs/Action|获取 Web 应用与槽之间的配置差异|
|/sites/slots/slotsswap/Action|交换 Web 应用部署槽|
|/sites/slots/snapshots/read|获取 Web 应用槽快照。|
|/sites/slots/sourcecontrols/Delete|删除 Web 应用槽的源代码管理配置设置|
|/sites/slots/sourcecontrols/Read|获取 Web 应用槽的源代码管理配置设置|
|/sites/slots/sourcecontrols/Write|更新 Web 应用槽的源代码管理配置设置|
|/sites/slots/start/Action|启动 Web 应用槽|
|/sites/slots/stop/Action|停止 Web 应用槽|
|/sites/slots/sync/action|同步 Web 应用槽。|
|/sites/slots/triggeredwebjobs/delete|删除 Web 应用槽触发的 Web 作业。|
|/sites/slots/triggeredwebjobs/read|获取 Web 应用槽触发的 Web 作业。|
|/sites/slots/triggeredwebjobs/run/action|运行 Web 应用槽触发的 Web 作业。|
|/sites/slots/usages/read|获取 Web 应用槽使用情况。|
|/sites/slots/virtualnetworkconnections/delete|删除 Web 应用槽虚拟网络连接。|
|/sites/slots/virtualnetworkconnections/gateways/write|更新 Web 应用槽虚拟网络连接网关。|
|/sites/slots/virtualnetworkconnections/read|获取 Web 应用槽虚拟网络连接。|
|/sites/slots/virtualnetworkconnections/write|更新 Web 应用槽虚拟网络连接。|
|/sites/slots/webjobs/read|获取 Web 应用槽 Web 作业。|
|/sites/slots/Write|创建新的或更新现有的 Web 应用槽|
|/sites/slotsdiffs/Action|获取 Web 应用与槽之间的配置差异|
|/sites/slotsswap/Action|交换 Web 应用部署槽|
|/sites/snapshots/read|获取 Web 应用快照。|
|/sites/sourcecontrols/Delete|删除 Web 应用的源代码管理配置设置|
|/sites/sourcecontrols/Read|获取 Web 应用的源代码管理配置设置|
|/sites/sourcecontrols/Write|更新 Web 应用的源代码管理配置设置|
|/sites/start/Action|启动 Web 应用|
|/sites/stop/Action|停止 Web 应用|
|/sites/sync/action|同步 Web 应用。|
|/sites/syncfunctiontriggers/action|Web 应用的同步函数触发器。|
|/sites/triggeredwebjobs/delete|删除 Web 应用触发的 Web 作业。|
|/sites/triggeredwebjobs/history/read|获取 Web 应用触发的 Web 作业历史记录。|
|/sites/triggeredwebjobs/read|获取 Web 应用触发的 Web 作业。|
|/sites/triggeredwebjobs/run/action|运行 Web 应用触发的 Web 作业。|
|/sites/usages/read|获取 Web 应用使用情况。|
|/sites/virtualnetworkconnections/delete|删除 Web 应用虚拟网络连接。|
|/sites/virtualnetworkconnections/gateways/read|获取 Web 应用虚拟网络连接网关。|
|/sites/virtualnetworkconnections/gateways/write|更新 Web 应用虚拟网络连接网关。|
|/sites/virtualnetworkconnections/read|获取 Web 应用虚拟网络连接。|
|/sites/virtualnetworkconnections/write|更新 Web 应用虚拟网络连接。|
|/sites/webjobs/read|获取 Web 应用 Web 作业。|
|/sites/Write|创建新的或更新现有的 Web 应用|
|/skus/read|获取 SKU。|
|/sourcecontrols/read|获取源代码管理。|
|/sourcecontrols/write|更新源代码管理。|
|/unregister/action|取消注册订阅的 Microsoft.Web 资源提供程序。|
|/validate/action|验证。|
|/verifyhostingenvironmentvnet/action|验证宿主环境 Vnet。|

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

| Operation | 说明 |
|---|---|
|/components/read|读取操作资源|
|/healthInstances/read|读取操作资源|
|/Operations/read|读取操作资源|
|/workloads/delete|删除工作负载资源|
|/workloads/read|读取工作负载资源|
|/workloads/write|写入工作负载资源|

## <a name="next-steps"></a>后续步骤

- 了解如何[创建自定义角色](role-based-access-control-custom-roles.md)。
- 查看[内置的 RBAC 角色](role-based-access-built-in-roles.md)。
- 了解如何[按用户](role-based-access-control-manage-assignments.md)或[按资源](role-based-access-control-configure.md)管理访问权限分配 
- 了解如何[查看活动日志以审核对资源的操作](~/articles/azure-resource-manager/resource-group-audit.md)
