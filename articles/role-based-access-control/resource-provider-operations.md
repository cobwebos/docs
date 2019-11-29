---
title: Azure 资源管理器资源提供程序操作 | Microsoft Docs
description: 列出了可对 Microsoft Azure 资源管理器资源提供程序使用的操作
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 092c3b4ac6ce163e9fcf8aaad9e74f398559e9e2
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546325"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Azure 资源管理器资源提供程序操作

本文档列出了可对每个 Azure 资源管理器资源提供程序使用的操作。 可以在[自定义角色](custom-roles.md)中使用这些操作，针对 Azure 中的资源提供精细的[基于角色的访问控制 (RBAC)](overview.md)。 操作字符串具有以下格式： `{Company}.{ProviderName}/{resourceType}/{action}`。 有关资源提供程序命名空间如何映射到 Azure 服务的列表，请参阅[将资源提供程序与服务匹配](../azure-resource-manager/azure-services-resource-providers.md)。

资源提供程序操作一直在不断发展。 若要获取最新操作，请使用 [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) 或 [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.AAD/domainServices/delete | 删除域服务 |
> | 行动 | Microsoft.AAD/domainServices/oucontainer/delete | 删除 Ou 容器 |
> | 行动 | Microsoft.AAD/domainServices/oucontainer/read | 读取 Ou 容器 |
> | 行动 | Microsoft.AAD/domainServices/oucontainer/write | 写入 Ou 容器 |
> | 行动 | Microsoft.AAD/domainServices/read | 读取域服务 |
> | 行动 | DomainServices/replicaSets/delete | 删除群集站点 |
> | 行动 | DomainServices/replicaSets/read | 读取群集站点 |
> | 行动 | DomainServices/replicaSets/write | 写入群集站点 |
> | 行动 | Microsoft.AAD/domainServices/write | 写入域服务 |
> | 行动 | Microsoft.AAD/locations/operationresults/read |  |
> | 行动 | Microsoft.AAD/Operations/read |  |
> | 行动 | Microsoft.AAD/register/action | 注册域服务 |
> | 行动 | Microsoft.AAD/unregister/action | 取消注册域服务 |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | microsoft.aadiam/diagnosticsettings/delete | 删除诊断设置 |
> | 行动 | microsoft.aadiam/diagnosticsettings/read | 读取诊断设置 |
> | 行动 | microsoft.aadiam/diagnosticsettings/write | 写入诊断设置 |
> | 行动 | microsoft.aadiam/diagnosticsettingscategories/read | 读取诊断设置类别 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Addons/operations/read | 获取受支持的 RP 操作。 |
> | 行动 | Microsoft.Addons/register/action | 向 Microsoft.Addons 注册指定的订阅 |
> | 行动 | Microsoft.Addons/supportProviders/listsupportplaninfo/action | 列出指定的订阅的当前支持计划信息。 |
> | 行动 | Microsoft.Addons/supportProviders/supportPlanTypes/delete | 删除指定的 Canonical 支持计划 |
> | 行动 | Microsoft.Addons/supportProviders/supportPlanTypes/read | 获取指定的 Canonical 支持计划状态。 |
> | 行动 | Microsoft.Addons/supportProviders/supportPlanTypes/write | 添加指定的 Canonical 支持计划类型。 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.ADHybridHealthService/addsservices/action | 为租户创建一个新林。 |
> | 行动 | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | 获取指定的服务名称的所有服务器。 |
> | 行动 | Microsoft.ADHybridHealthService/addsservices/alerts/read | 获取有关林的警报详细信息，例如警报 ID、警报引发日期、上次检测到的警报、警报说明、上次更新时间、警报级别、警报状态、警报故障排除链接，等等。 |
> | 行动 | Microsoft.ADHybridHealthService/addsservices/configuration/read | 获取林的服务配置。 示例 - 林名称、功能级别、域命名主机 FSMO 角色、架构主机 FSMO 角色，等等。 |
> | 行动 | Microsoft.ADHybridHealthService/addsservices/delete | 删除服务及其服务器和运行状况数据。 |
> | 行动 | Microsoft.ADHybridHealthService/addsservices/dimensions/read | 获取林的域和站点详细信息。 示例 - 运行状况、活动警报、已解决的警报、属性（如域功能级别、林、基础结构主机、PDC、RID 主机，等等）。  |
> | 行动 | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | 获取林的用户首选项设置。<br>示例 - MetricCounterName，例如 ldapsuccessfulbinds、ntlmauthentications、kerberosauthentications、addsinsightsagentprivatebytes、ldapsearches。<br>UI 图表的设置等等。 |
> | 行动 | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | 获取给定林的林摘要，例如林名称、此林下的域数目、站点数目和站点详细信息，等等。 |
> | 行动 | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | 获取给定服务的受支持指标列表。<br>例如，ADFS 服务的 Extranet 帐户锁定数、失败的请求总数、未完成的令牌请求数（代理）、令牌请求数/秒。<br>ADDomainService 的 NTLM 身份验证数/秒、LDAP 成功绑定数/秒、LDAP 绑定时间、LDAP 活动线程数、Kerberos 身份验证数/秒、ATQ 线程总数，等等。<br>ADSync 服务的运行配置文件延迟、已建立的 TCP 连接数、Insights 代理专用字节数、到 Azure AD 的导出统计信息。 |
> | 行动 | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | 对于给定的服务，此 API 获取指标信息。<br>例如，可以使用此 API 获取与以下项相关的信息：ADFederation 服务的 Extranet 帐户锁定数、失败的请求总数、未完成的令牌请求数（代理）、令牌请求数/秒。<br>ADDomain 服务的 NTLM 身份验证数/秒、LDAP 成功绑定数/秒、LDAP 绑定时间、LDAP 活动线程数、Kerberos 身份验证数/秒、ATQ 线程总数，等等。<br>同步服务的运行配置文件延迟、已建立的 TCP 连接数、Insights 代理专用字节数、到 Azure AD 的导出统计信息。 |
> | 行动 | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | 此 API 获取高级租户的所有已载入 ADDomainServices 的列表。 |
> | 行动 | Microsoft.ADHybridHealthService/addsservices/read | 获取指定服务名称的服务详细信息。 |
> | 行动 | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | 获取指定服务名称的所有服务器的复制详细信息。 |
> | 行动 | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | 获取域控制器的数目及其复制错误（如果有）。 |
> | 行动 | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | 获取给定林的完整域控制器列表以及复制详细信息。 |
> | 行动 | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | 向服务添加服务器实例。 |
> | 行动 | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | 在 ADDomainService 的服务器注册期间，会调用此 API 来获取正在载入的新服务器的凭据。 |
> | 行动 | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | 删除给定服务和租户的服务器。 |
> | 行动 | Microsoft.ADHybridHealthService/addsservices/write | 创建或更新租户的 ADDomainService 实例。 |
> | 行动 | Microsoft.ADHybridHealthService/configuration/action | 更新租户配置。 |
> | 行动 | Microsoft.ADHybridHealthService/configuration/read | 读取租户配置。 |
> | 行动 | Microsoft.ADHybridHealthService/configuration/write | 创建租户配置。 |
> | 行动 | Microsoft.ADHybridHealthService/logs/contents/read | 获取 blob 中存储的代理安装和注册日志的内容。 |
> | 行动 | Microsoft.ADHybridHealthService/logs/read | 获取租户的代理安装和注册日志。 |
> | 行动 | Microsoft.ADHybridHealthService/operations/read | 获取系统支持的操作的列表。 |
> | 行动 | Microsoft.ADHybridHealthService/register/action | 注册 ADHybrid 运行状况服务资源提供程序并启用 ADHybrid 运行状况服务资源的创建。 |
> | 行动 | Microsoft.ADHybridHealthService/reports/availabledeployments/read | 获取 DevOps 为客户事件提供支持时使用的可用区域的列表。 |
> | 行动 | Microsoft.ADHybridHealthService/reports/badpassword/read | 获取 Active Directory 联合身份验证服务中的所有用户的错误密码尝试列表。 |
> | 行动 | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | 获取包含新排队的用于以下用途的报告作业的状态和最终结果的 Blob SAS URI：显示给定租户中每 UserId 每 IPAddress 的错误用户名/密码尝试频率。 |
> | 行动 | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | 获取 DevOps 已许可租户的列表。 通常用于客户支持。 |
> | 行动 | Microsoft.ADHybridHealthService/reports/isdevops/read | 获取一个值，该值指示租户是否为 DevOps 许可的租户。 |
> | 行动 | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | 更新所选 DevOps 租户的 userid(objectid)。 |
> | 行动 | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | 获取给定租户的所选部署。 |
> | 行动 | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | 对于给定的租户 id，获取租户存储位置。 |
> | 行动 | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | 获取将从中访问数据的地理位置。 |
> | 行动 | Microsoft.ADHybridHealthService/services/action | 更新租户中的服务实例。 |
> | 行动 | Microsoft.ADHybridHealthService/services/alerts/read | 读取服务的警报。 |
> | 行动 | Microsoft.ADHybridHealthService/services/alerts/read | 读取服务的警报。 |
> | 行动 | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | 对于给定的功能名称，验证某项服务是否具有使用该功能所需的所有项。 |
> | 行动 | Microsoft.ADHybridHealthService/services/delete | 删除租户中的服务实例。 |
> | 行动 | Microsoft.ADHybridHealthService/services/exporterrors/read | 获取给定同步服务的导出错误。 |
> | 行动 | Microsoft.ADHybridHealthService/services/exportstatus/read | 获取给定服务的导出状态。 |
> | 行动 | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | 获取给定服务和服务器的警报反馈。 |
> | 行动 | Microsoft.ADHybridHealthService/services/metricmetadata/read | 获取给定服务的受支持指标列表。<br>例如，ADFS 服务的 Extranet 帐户锁定数、失败的请求总数、未完成的令牌请求数（代理）、令牌请求数/秒。<br>ADDomainService 的 NTLM 身份验证数/秒、LDAP 成功绑定数/秒、LDAP 绑定时间、LDAP 活动线程数、Kerberos 身份验证数/秒、ATQ 线程总数，等等。<br>ADSync 服务的运行配置文件延迟、已建立的 TCP 连接数、Insights 代理专用字节数、到 Azure AD 的导出统计信息。 |
> | 行动 | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | 对于给定的服务，此 API 获取给定服务的指标的平均值。<br>例如，可以使用此 API 获取与以下项相关的信息：ADFederation 服务的 Extranet 帐户锁定数、失败的请求总数、未完成的令牌请求数（代理）、令牌请求数/秒。<br>ADDomain 服务的 NTLM 身份验证数/秒、LDAP 成功绑定数/秒、LDAP 绑定时间、LDAP 活动线程数、Kerberos 身份验证数/秒、ATQ 线程总数，等等。<br>同步服务的运行配置文件延迟、已建立的 TCP 连接数、Insights 代理专用字节数、到 Azure AD 的导出统计信息。 |
> | 行动 | Microsoft.ADHybridHealthService/services/metrics/groups/read | 对于给定的服务，此 API 获取指标信息。<br>例如，可以使用此 API 获取与以下项相关的信息：ADFederation 服务的 Extranet 帐户锁定数、失败的请求总数、未完成的令牌请求数（代理）、令牌请求数/秒。<br>ADDomain 服务的 NTLM 身份验证数/秒、LDAP 成功绑定数/秒、LDAP 绑定时间、LDAP 活动线程数、Kerberos 身份验证数/秒、ATQ 线程总数，等等。<br>同步服务的运行配置文件延迟、已建立的 TCP 连接数、Insights 代理专用字节数、到 Azure AD 的导出统计信息。 |
> | 行动 | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | 对于给定的服务，此 API 获取给定服务的指标的聚合视图。<br>例如，可以使用此 API 获取与以下项相关的信息：ADFederation 服务的 Extranet 帐户锁定数、失败的请求总数、未完成的令牌请求数（代理）、令牌请求数/秒。<br>ADDomain 服务的 NTLM 身份验证数/秒、LDAP 成功绑定数/秒、LDAP 绑定时间、LDAP 活动线程数、Kerberos 身份验证数/秒、ATQ 线程总数，等等。<br>同步服务的运行配置文件延迟、已建立的 TCP 连接数、Insights 代理专用字节数、到 Azure AD 的导出统计信息。 |
> | 行动 | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | 添加或更新服务的监视配置。 |
> | 行动 | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | 获取给定服务的监视配置。 |
> | 行动 | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | 添加或更新服务的监视配置。 |
> | 行动 | Microsoft.ADHybridHealthService/services/premiumcheck/read | 此 API 获取高级租户的所有已载入服务的列表。 |
> | 行动 | Microsoft.ADHybridHealthService/services/read | 读取租户中的服务实例。 |
> | 行动 | ADHybridHealthService/services/reports/blobUris/read | 获取过去7天的所有有风险的 IP 报表 Uri。 |
> | 行动 | Microsoft.ADHybridHealthService/services/reports/details/read | 获取包含过去 7 天内发生的密码不正确错误最多的前 50 位用户的报告 |
> | 行动 | ADHybridHealthService/services/reports/generateBlobUri/action | 生成有风险的 IP 报表并返回指向它的 URI。 |
> | 行动 | Microsoft.ADHybridHealthService/services/servicemembers/action | 在服务中创建服务器实例。 |
> | 行动 | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | 读取服务器的警报。 |
> | 行动 | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | 在服务器注册期间，会调用此 API 来获取正在载入的新服务器的凭据。 |
> | 行动 | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | 对于给定的服务器，此 API 获取服务器正在上传的数据类型的列表，以及每次上传的最新时间。 |
> | 行动 | Microsoft.ADHybridHealthService/services/servicemembers/delete | 删除服务中的服务器实例。 |
> | 行动 | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | 获取给定同步服务的同步导出错误详细信息。 |
> | 行动 | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | 对于给定的服务，此 API 获取指标信息。<br>例如，可以使用此 API 获取与以下项相关的信息：ADFederation 服务的 Extranet 帐户锁定数、失败的请求总数、未完成的令牌请求数（代理）、令牌请求数/秒。<br>ADDomain 服务的 NTLM 身份验证数/秒、LDAP 成功绑定数/秒、LDAP 绑定时间、LDAP 活动线程数、Kerberos 身份验证数/秒、ATQ 线程总数，等等。<br>同步服务的运行配置文件延迟、已建立的 TCP 连接数、Insights 代理专用字节数、到 Azure AD 的导出统计信息。 |
> | 行动 | ADHybridHealthService/services/servicemembers/指标/读取 | 获取给定服务和服务成员的连接器和运行配置文件名称的列表。 |
> | 行动 | Microsoft.ADHybridHealthService/services/servicemembers/read | 读取服务中的服务器实例。 |
> | 行动 | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | 获取给定租户的服务配置。 |
> | 行动 | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | 获取给定租户的功能允许列表状态。 |
> | 行动 | Microsoft.ADHybridHealthService/services/write | 在租户中创建服务实例。 |
> | 行动 | Microsoft.ADHybridHealthService/unregister/action | 取消注册 ADHybrid 运行状况服务资源提供程序的订阅。 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Advisor/configurations/read | 获取配置 |
> | 行动 | Microsoft.Advisor/configurations/write | 创建/更新配置 |
> | 行动 | Microsoft.Advisor/generateRecommendations/action | 生成建议 |
> | 行动 | Microsoft.Advisor/generateRecommendations/read | 获取“生成建议”状态 |
> | 行动 | Microsoft Advisor/元数据/读取 | 获取元数据 |
> | 行动 | Microsoft.Advisor/operations/read | 获取 Microsoft 顾问的操作 |
> | 行动 | Microsoft.Advisor/recommendations/available/action | Microsoft 顾问中提供了新建议 |
> | 行动 | Microsoft.Advisor/recommendations/read | 读取建议 |
> | 行动 | Microsoft.Advisor/recommendations/suppressions/delete | 删除禁止显示 |
> | 行动 | Microsoft.Advisor/recommendations/suppressions/read | 获取禁止显示 |
> | 行动 | Microsoft.Advisor/recommendations/suppressions/write | 创建/更新禁止显示 |
> | 行动 | Microsoft.Advisor/register/action | 注册 Microsoft 顾问的订阅 |
> | 行动 | Microsoft.Advisor/suppressions/delete | 删除禁止显示 |
> | 行动 | Microsoft.Advisor/suppressions/read | 获取禁止显示 |
> | 行动 | Microsoft.Advisor/suppressions/write | 创建/更新禁止显示 |
> | 行动 | Microsoft.Advisor/unregister/action | 取消注册 Microsoft 顾问的订阅 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.AlertsManagement/actionRules/delete | 删除给定订阅中的操作规则。 |
> | 行动 | Microsoft.AlertsManagement/actionRules/read | 获取输入筛选器的所有操作规则。 |
> | 行动 | Microsoft.AlertsManagement/actionRules/write | 创建或更新给定订阅中的操作规则 |
> | 行动 | Microsoft.AlertsManagement/alerts/changestate/action | 更改警报状态。 |
> | 行动 | Microsoft.AlertsManagement/alerts/diagnostics/read | 获取警报的所有诊断 |
> | 行动 | Microsoft.AlertsManagement/alerts/history/read | 获取警报的历史记录 |
> | 行动 | Microsoft.AlertsManagement/alerts/read | 获取输入筛选器的所有警报。 |
> | 行动 | Microsoft.AlertsManagement/alertsList/read | 获取跨订阅的输入筛选器的所有警报 |
> | 行动 | AlertsManagement/alertsMetaData/read | 获取输入参数的元数据警报。 |
> | 行动 | Microsoft.AlertsManagement/alertsSummary/read | 获取警报的摘要 |
> | 行动 | Microsoft.AlertsManagement/alertsSummaryList/read | 获取跨订阅的警报的摘要 |
> | 行动 | Microsoft.AlertsManagement/Operations/read | 读取提供的操作 |
> | 行动 | Microsoft.AlertsManagement/register/action | 注册 Microsoft 警报管理的订阅 |
> | 行动 | AlertsManagement/smartDetectorAlertRules/delete | 删除给定订阅中的智能检测警报规则 |
> | 行动 | AlertsManagement/smartDetectorAlertRules/read | 获取输入筛选器的所有智能检测程序警报规则 |
> | 行动 | AlertsManagement/smartDetectorAlertRules/write | 在给定的订阅中创建或更新智能检测预警规则 |
> | 行动 | Microsoft.AlertsManagement/smartGroups/changestate/action | 更改智能组的状态 |
> | 行动 | Microsoft.AlertsManagement/smartGroups/history/read | 获取智能组的历史记录 |
> | 行动 | Microsoft.AlertsManagement/smartGroups/read | 获取输入筛选器的所有智能组 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.AnalysisServices/locations/checkNameAvailability/action | 检查给定的 Analysis Server 名称是否有效且未被使用。 |
> | 行动 | Microsoft.AnalysisServices/locations/operationresults/read | 检索指定操作结果的信息。 |
> | 行动 | Microsoft.AnalysisServices/locations/operationstatuses/read | 检索指定操作状态的信息。 |
> | 行动 | Microsoft.AnalysisServices/operations/read | 检索操作的信息 |
> | 行动 | Microsoft.AnalysisServices/register/action | 注册 Analysis Services 资源提供程序。 |
> | 行动 | Microsoft.AnalysisServices/servers/delete | 删除 Analysis Server。 |
> | 行动 | Microsoft.AnalysisServices/servers/listGatewayStatus/action | 列出与服务器关联的网关的状态。 |
> | 行动 | Microsoft.AnalysisServices/servers/read | 检索指定 Analysis Server 的信息。 |
> | 行动 | Microsoft.AnalysisServices/servers/resume/action | 恢复 Analysis Server。 |
> | 行动 | Microsoft.AnalysisServices/servers/skus/read | 检索服务器的可用 SKU 信息 |
> | 行动 | Microsoft.AnalysisServices/servers/suspend/action | 暂停 Analysis Server。 |
> | 行动 | Microsoft.AnalysisServices/servers/write | 创建或更新指定的 Analysis Server。 |
> | 行动 | Microsoft.AnalysisServices/skus/read | 检索 SKU 的信息 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.ApiManagement/checkNameAvailability/read | 检查提供的服务名称是否可用 |
> | 行动 | Microsoft.ApiManagement/operations/read | 读取适用于 Microsoft.ApiManagement 资源的所有 API 操作 |
> | 行动 | Microsoft.ApiManagement/register/action | 注册 Microsoft.ApiManagement 资源提供程序的订阅 |
> | 行动 | Microsoft.ApiManagement/reports/read | 获取按时间段、地理区域、开发者、产品、API、操作、订阅和 byRequest 聚合的报表。 |
> | 行动 | Microsoft.ApiManagement/service/apis/delete | 删除 API 管理服务实例的指定 API。 |
> | 行动 | Microsoft.ApiManagement/service/apis/diagnostics/delete | 从 API 中删除指定的诊断。 |
> | 行动 | Microsoft.ApiManagement/service/apis/diagnostics/read | 列出 API 的所有诊断。 或获取由其标识符指定的 API 的诊断详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/apis/diagnostics/write | 为 API 创建新的或更新现有的诊断。 或更新由其标识符指定的 API 的诊断详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/apis/issues/attachments/delete | 从问题中删除指定的注释。 |
> | 行动 | Microsoft.ApiManagement/service/apis/issues/attachments/read | 列出与指定的 API 关联的问题的所有附件。 或获取由其标识符指定的 API 的问题附件的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/apis/issues/attachments/write | 为 API 中的问题创建新的附件，或更新现有的附件。 |
> | 行动 | Microsoft.ApiManagement/service/apis/issues/comments/delete | 从问题中删除指定的注释。 |
> | 行动 | Microsoft.ApiManagement/service/apis/issues/comments/read | 列出与指定的 API 关联的问题的所有注释。 或获取由其标识符指定的 API 的问题注释的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/apis/issues/comments/write | 为 API 中的问题创建新注释或更新现有注释。 |
> | 行动 | Microsoft.ApiManagement/service/apis/issues/delete | 从 API 中删除指定的问题。 |
> | 行动 | Microsoft.ApiManagement/service/apis/issues/read | 列出与指定的 API 相关的所有问题。 或获取由其标识符指定的 API 的问题的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/apis/issues/write | 为 API 创建新问题或更新现有问题。 或更新 API 的现有问题。 |
> | 行动 | Microsoft.ApiManagement/service/apis/operations/delete | 删除 API 中的指定操作。 |
> | 行动 | Microsoft.ApiManagement/service/apis/operations/policies/delete | 在 Api 操作中删除策略配置。 |
> | 行动 | Microsoft.ApiManagement/service/apis/operations/policies/read | 获取 API 操作级别的策略配置列表。 或获取 API 操作级别的策略配置。 |
> | 行动 | Microsoft.ApiManagement/service/apis/operations/policies/write | 创建或更新 API 操作级别的策略配置。 |
> | 行动 | Microsoft.ApiManagement/service/apis/operations/policy/delete | 在操作级别删除策略配置 |
> | 行动 | Microsoft.ApiManagement/service/apis/operations/policy/read | 在操作级别获取策略配置 |
> | 行动 | Microsoft.ApiManagement/service/apis/operations/policy/write | 在操作级别创建策略配置 |
> | 行动 | Microsoft.ApiManagement/service/apis/operations/read | 列出指定 API 的操作的集合。 或获取由其标识符指定的 API 操作的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/apis/operations/tags/delete | 从操作中分离标记。 |
> | 行动 | Microsoft.ApiManagement/service/apis/operations/tags/read | 列出与操作关联的所有标记。 或获取与操作关联的标记。 |
> | 行动 | Microsoft.ApiManagement/service/apis/operations/tags/write | 为操作分配标记。 |
> | 行动 | Microsoft.ApiManagement/service/apis/operations/write | 在 API 中创建新操作或更新现有操作。 或更新其标识符所指定的 API 中的操作的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/apis/operationsByTags/read | 列出与标记相关联的操作的集合。 |
> | 行动 | Microsoft.ApiManagement/service/apis/policies/delete | 删除 Api 中的策略配置。 |
> | 行动 | Microsoft.ApiManagement/service/apis/policies/read | 获取 API 级别的策略配置。 或获取 API 级别的策略配置。 |
> | 行动 | Microsoft.ApiManagement/service/apis/policies/write | 创建或更新 API 的策略配置。 |
> | 行动 | Microsoft.ApiManagement/service/apis/policy/delete | 在 API 级别删除策略配置 |
> | 行动 | Microsoft.ApiManagement/service/apis/policy/read | 在 API 级别获取策略配置 |
> | 行动 | Microsoft.ApiManagement/service/apis/policy/write | 在 API 级别创建策略配置 |
> | 行动 | Microsoft.ApiManagement/service/apis/products/read | 列出 API 所属的所有产品。 |
> | 行动 | Microsoft.ApiManagement/service/apis/read | 列出 API 管理服务实例的所有 Api。 或获取由其标识符指定的 API 的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/apis/releases/delete | 删除 API 的所有版本或删除 API 中指定的版本。 |
> | 行动 | Microsoft.ApiManagement/service/apis/releases/read | 列出 API 的所有版本。<br>将 API 版本设为当前版本时，将创建 API 版本。<br>版本还用于回滚到以前的修订版本。<br>将对结果进行分页并可由 $top 和 $skip 参数进行约束。<br>或返回 API 版本的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/apis/releases/write | 创建 API 的新版本。 或更新由其标识符指定的 API 版本的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/apis/revisions/delete | 删除 API 的所有修订 |
> | 行动 | Microsoft.ApiManagement/service/apis/revisions/read | 列出 API 的所有修订版本。 |
> | 行动 | Microsoft.ApiManagement/service/apis/schemas/delete | 删除 Api 的架构配置。 |
> | 行动 | Microsoft.ApiManagement/service/apis/schemas/read | 在 API 级别获取架构配置。 或获取 API 级别的架构配置。 |
> | 行动 | Microsoft.ApiManagement/service/apis/schemas/write | 创建或更新 API 的架构配置。 |
> | 行动 | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | 删除 Api 的标记说明。 |
> | 行动 | Microsoft.ApiManagement/service/apis/tagDescriptions/read | 列出 API 范围内的所有标记说明。 类似于 tagDescription 的模型是在 API 级别上定义的，但可以在 API 范围内将标记分配给操作或获取标记说明 |
> | 行动 | Microsoft.ApiManagement/service/apis/tagDescriptions/write | 在 Api 范围中创建/更新标记说明。 |
> | 行动 | Microsoft.ApiManagement/service/apis/tags/delete | 从 Api 分离标记。 |
> | 行动 | Microsoft.ApiManagement/service/apis/tags/read | 列出与 API 关联的所有标记。 或获取与 API 关联的标记。 |
> | 行动 | Microsoft.ApiManagement/service/apis/tags/write | 将标记分配给 Api。 |
> | 行动 | Microsoft.ApiManagement/service/apis/write | 创建新的或更新 API 管理服务实例的现有指定 API。 或更新 API 管理服务实例的指定 API。 |
> | 行动 | Microsoft.ApiManagement/service/apisByTags/read | 列出与标记相关联的 api 集合。 |
> | 行动 | Microsoft.ApiManagement/service/apiVersionSets/delete | 删除特定的 Api 版本集。 |
> | 行动 | Microsoft.ApiManagement/service/apiVersionSets/read | 列出指定服务实例中的 API 版本集的集合。 或获取由其标识符指定的 Api 版本集的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/apiVersionSets/versions/read | 获取版本实体的列表 |
> | 行动 | Microsoft.ApiManagement/service/apiVersionSets/write | 创建或更新 Api 版本集。 或更新由其标识符指定的 Api VersionSet 的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | 更新虚拟网络中运行的 Microsoft.ApiManagement 资源，以提取更新的网络设置。 |
> | 行动 | Microsoft.ApiManagement/service/authorizationServers/delete | 删除特定的授权服务器实例。 |
> | 行动 | Microsoft.ApiManagement/service/authorizationServers/read | 列出在服务实例中定义的授权服务器的集合。 或获取由其标识符指定的授权服务器的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/authorizationServers/write | 创建新的授权服务器，或更新现有授权服务器。 或更新由其标识符指定的授权服务器的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/backends/delete | 删除指定的后端。 |
> | 行动 | Microsoft.ApiManagement/service/backends/read | 列出指定服务实例中的后端的集合。 或获取由其标识符指定的后端的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/backends/reconnect/action | 通知 APIM 代理在指定的超时时间后创建到后端的新连接。 如果未指定超时，则使用2分钟的超时值。 |
> | 行动 | Microsoft.ApiManagement/service/backends/write | 创建或更新后端。 或更新现有后端。 |
> | 行动 | Microsoft.ApiManagement/service/backup/action | 将 API 管理服务备份到用户提供的存储帐户中的指定容器 |
> | 行动 | ApiManagement/服务/缓存/删除 | 删除特定的缓存。 |
> | 行动 | ApiManagement/服务/缓存/读取 | 列出指定服务实例中的所有外部缓存的集合。 或获取由其标识符指定的缓存的详细信息。 |
> | 行动 | ApiManagement/服务/缓存/写入 | 创建或更新要在 Api 管理实例中使用的外部缓存。 或更新由其标识符指定的缓存的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/certificates/delete | 删除特定证书。 |
> | 行动 | Microsoft.ApiManagement/service/certificates/read | 列出指定服务实例中所有证书的集合。 或获取由标识符指定的证书的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/certificates/write | 创建或更新用于对后端进行身份验证的证书。 |
> | 行动 | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | 删除指定的内容项。 |
> | 行动 | Microsoft.ApiManagement/service/contentTypes/contentItems/read | 返回内容项的列表或返回内容项详细信息 |
> | 行动 | Microsoft.ApiManagement/service/contentTypes/contentItems/write | 创建新内容项，或更新指定的内容项 |
> | 行动 | Microsoft.ApiManagement/service/contentTypes/read | 返回内容类型的列表 |
> | 行动 | Microsoft.ApiManagement/service/delete | 删除 API 管理服务实例 |
> | 行动 | Microsoft.ApiManagement/service/diagnostics/delete | 删除指定的诊断。 |
> | 行动 | Microsoft.ApiManagement/service/diagnostics/read | 列出 API 管理服务实例的所有诊断。 或获取由标识符指定的诊断的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/diagnostics/write | 创建新诊断或更新现有诊断。 或更新由其标识符指定的诊断的详细信息。 |
> | 行动 | ApiManagement/服务/网关/操作 | 检索网关配置。 或更新网关检测信号。 |
> | 行动 | ApiManagement/服务/网关/删除 | 删除特定的网关。 |
> | 行动 | ApiManagement/服务/网关/密钥/操作 | 检索网关密钥。 |
> | 行动 | ApiManagement/服务/网关/读取 | 列出注册到服务实例的网关集合。 或获取由其标识符指定的网关的详细信息。 |
> | 行动 | ApiManagement/服务/网关/regeneratePrimaryKey/action | 重新生成用它创建的任何令牌 invalidationg 的主网关密钥。 |
> | 行动 | ApiManagement/服务/网关/regenerateSecondaryKey/action | 重新生成用它创建的任何令牌 invalidationg 的辅助网关密钥。 |
> | 行动 | ApiManagement/服务/网关/令牌/操作 | 获取网关的共享访问授权令牌。 |
> | 行动 | ApiManagement/服务/网关/写入 | 创建或更新要用于 Api 管理实例的网关。 或更新由其标识符指定的网关的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/getssotoken/action | 获取可用于以管理员身份登录到 API 管理服务旧版门户的 SSO 令牌 |
> | 行动 | Microsoft.ApiManagement/service/groups/delete | 删除 API 管理服务实例的特定组。 |
> | 行动 | Microsoft.ApiManagement/service/groups/read | 列出在服务实例中定义的组的集合。 或获取由标识符指定的组的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/groups/users/delete | 从现有组中删除现有用户。 |
> | 行动 | Microsoft.ApiManagement/service/groups/users/read | 列出与组关联的用户实体的集合。 |
> | 行动 | Microsoft.ApiManagement/service/groups/users/write | 将现有用户添加到现有组 |
> | 行动 | Microsoft.ApiManagement/service/groups/write | 创建或更新组。 或更新由标识符指定的组的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/identityProviders/delete | 删除指定的标识提供程序配置。 |
> | 行动 | Microsoft.ApiManagement/service/identityProviders/read | 列出在指定的服务实例中配置的标识提供程序的集合。 或获取在指定的服务实例中配置的标识提供程序的配置详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/identityProviders/write | 创建或更新 IdentityProvider 配置。 或更新现有的 IdentityProvider 配置。 |
> | 行动 | ApiManagement/服务/问题/读取 | 列出指定服务实例中的问题的集合。 或获取 API 管理问题的详细信息 |
> | 行动 | Microsoft.ApiManagement/service/locations/networkstatus/read | 获取位置中服务所依赖的资源的网络访问状态。 |
> | 行动 | Microsoft.ApiManagement/service/loggers/delete | 删除指定的记录器。 |
> | 行动 | Microsoft.ApiManagement/service/loggers/read | 列出指定服务实例中的记录器的集合。 或获取由标识符指定的记录器的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/loggers/write | 创建或更新记录器。 或更新现有的记录器。 |
> | 行动 | Microsoft.ApiManagement/service/managedeployments/action | 更改 API 管理服务的 SKU/单位，以及添加/删除其区域部署 |
> | 行动 | Microsoft.ApiManagement/service/networkstatus/read | 获取服务所依赖的资源的网络访问状态。 |
> | 行动 | Microsoft.ApiManagement/service/notifications/action | 向指定用户发送通知 |
> | 行动 | Microsoft.ApiManagement/service/notifications/read | 列出在服务实例中定义的属性的集合。 或获取由标识符指定的通知的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | 从通知列表中删除电子邮件。 |
> | 行动 | Microsoft.ApiManagement/service/notifications/recipientEmails/read | 获取订阅通知的通知收件人电子邮件的列表。 |
> | 行动 | Microsoft.ApiManagement/service/notifications/recipientEmails/write | 将电子邮件地址添加到通知的收件人列表。 |
> | 行动 | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | 从通知列表中删除 API 管理用户。 |
> | 行动 | Microsoft.ApiManagement/service/notifications/recipientUsers/read | 获取订阅通知的通知收件人用户的列表。 |
> | 行动 | Microsoft.ApiManagement/service/notifications/recipientUsers/write | 将 API 管理用户添加到通知的收件人列表。 |
> | 行动 | Microsoft.ApiManagement/service/notifications/write | 创建或更新 API 管理发布者通知。 |
> | 行动 | Microsoft.ApiManagement/service/openidConnectProviders/delete | 删除 API 管理服务实例的特定 OpenID Connect 提供程序。 |
> | 行动 | Microsoft.ApiManagement/service/openidConnectProviders/read | 所有 OpenId Connect 提供程序的列表。 或获取特定 OpenID Connect 提供程序。 |
> | 行动 | Microsoft.ApiManagement/service/openidConnectProviders/write | 创建或更新 OpenID Connect 提供程序。 或更新特定 OpenID Connect 提供程序。 |
> | 行动 | Microsoft.ApiManagement/service/operationresults/read | 获取长时间运行的操作的当前状态 |
> | 行动 | Microsoft.ApiManagement/service/policies/delete | 删除 Api 管理服务的全局策略配置。 |
> | 行动 | Microsoft.ApiManagement/service/policies/read | 列出 Api 管理服务的所有全局策略定义。 或获取 Api 管理服务的全局策略定义。 |
> | 行动 | Microsoft.ApiManagement/service/policies/write | 创建或更新 Api 管理服务的全局策略配置。 |
> | 行动 | ApiManagement/服务/策略/删除 | 在租户级别删除策略配置 |
> | 行动 | ApiManagement/服务/策略/读取 | 在租户级别获取策略配置 |
> | 行动 | ApiManagement/服务/策略/写入 | 在租户级别创建策略配置 |
> | 行动 | ApiManagement/service/policyDescriptions/read | 列出所有策略说明。 |
> | 行动 | Microsoft.ApiManagement/service/policySnippets/read | 列出所有策略代码片段。 |
> | 行动 | Microsoft.ApiManagement/service/portalsettings/read | 获取门户的登录设置，或获取门户的注册设置或获取门户的委派设置。 |
> | 行动 | Microsoft.ApiManagement/service/portalsettings/write | 更新登录设置。 或创建或更新登录设置。 或更新注册设置或更新注册设置或更新委派设置。 或创建或更新委派设置。 |
> | 行动 | Microsoft.ApiManagement/service/products/apis/delete | 从指定的产品中删除指定的 API。 |
> | 行动 | Microsoft.ApiManagement/service/products/apis/read | 列出与产品关联的 Api 的集合。 |
> | 行动 | Microsoft.ApiManagement/service/products/apis/write | 将 API 添加到指定的产品。 |
> | 行动 | Microsoft.ApiManagement/service/products/delete | 删除产品。 |
> | 行动 | Microsoft.ApiManagement/service/products/groups/delete | 删除指定的组与产品之间的关联。 |
> | 行动 | Microsoft.ApiManagement/service/products/groups/read | 列出与指定产品关联的开发人员组的集合。 |
> | 行动 | Microsoft.ApiManagement/service/products/groups/write | 添加指定的开发人员组与指定产品之间的关联。 |
> | 行动 | Microsoft.ApiManagement/service/products/policies/delete | 删除产品中的策略配置。 |
> | 行动 | Microsoft.ApiManagement/service/products/policies/read | 获取产品级别的策略配置。 或获取产品级别的策略配置。 |
> | 行动 | Microsoft.ApiManagement/service/products/policies/write | 创建或更新产品的策略配置。 |
> | 行动 | Microsoft.ApiManagement/service/products/policy/delete | 在产品级别删除策略配置 |
> | 行动 | Microsoft.ApiManagement/service/products/policy/read | 获取产品级别的策略配置 |
> | 行动 | Microsoft.ApiManagement/service/products/policy/write | 在产品级别创建策略配置 |
> | 行动 | Microsoft.ApiManagement/service/products/read | 列出指定服务实例中的产品集合。 或获取由其标识符指定的产品的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/products/subscriptions/read | 列出对指定产品的订阅的集合。 |
> | 行动 | Microsoft.ApiManagement/service/products/tags/delete | 将标记与产品分离。 |
> | 行动 | Microsoft.ApiManagement/service/products/tags/read | 列出与产品关联的所有标记。 或获取与产品关联的标记。 |
> | 行动 | Microsoft.ApiManagement/service/products/tags/write | 将标记分配给产品。 |
> | 行动 | Microsoft.ApiManagement/service/products/write | 创建或更新产品。 或更新现有产品详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/productsByTags/read | 列出与标记相关联的产品集合。 |
> | 行动 | Microsoft.ApiManagement/service/properties/delete | 删除 API 管理服务实例中的特定属性。 |
> | 行动 | Microsoft.ApiManagement/service/properties/read | 列出在服务实例中定义的属性的集合。 或获取由标识符指定的属性的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/properties/write | 创建或更新属性。 或更新特定属性。 |
> | 行动 | Microsoft.ApiManagement/service/quotas/periods/read | 获取某时间段的配额计数器值 |
> | 行动 | Microsoft.ApiManagement/service/quotas/periods/write | 设置配额计数器当前值 |
> | 行动 | Microsoft.ApiManagement/service/quotas/read | 获取配额值 |
> | 行动 | Microsoft.ApiManagement/service/quotas/write | 设置配额计数器当前值 |
> | 行动 | Microsoft.ApiManagement/service/read | 读取 API 管理服务实例的元数据 |
> | 行动 | ApiManagement/服务/区域/读取 | 列出存在该服务的所有 azure 区域。 |
> | 行动 | Microsoft.ApiManagement/service/reports/read | 获取按时间段聚合的报表，或获取按地理区域聚合的报表，或获取按开发者聚合的报表。<br>或获取按产品聚合的报表。<br>或获取按 API 聚合的报表，或获取按操作聚合的报表，或获取按订阅聚合的报表。<br>或获取报告数据的请求 |
> | 行动 | Microsoft.ApiManagement/service/restore/action | 从用户提供的存储帐户中的指定容器还原 API 管理服务 |
> | 行动 | Microsoft.ApiManagement/service/subscriptions/delete | 删除指定的订阅。 |
> | 行动 | Microsoft.ApiManagement/service/subscriptions/read | 列出 API 管理服务实例的所有订阅。 或获取指定的订阅实体。 |
> | 行动 | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | 重新生成 API 管理服务实例的现有订阅的主密钥。 |
> | 行动 | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | 重新生成 API 管理服务实例的现有订阅的辅助密钥。 |
> | 行动 | Microsoft.ApiManagement/service/subscriptions/write | 创建或更新指定用户对指定产品的订阅。 或更新由其标识符指定的订阅的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/tagResources/read | 列出与标记相关联的资源的集合。 |
> | 行动 | Microsoft.ApiManagement/service/tags/delete | 删除 API 管理服务实例的特定标记。 |
> | 行动 | Microsoft.ApiManagement/service/tags/read | 列出在服务实例中定义的标记的集合。 或获取由标识符指定的标记的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/tags/write | 创建标记。 或更新由标识符指定的标记的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/templates/delete | 重置默认 API 管理电子邮件模板 |
> | 行动 | Microsoft.ApiManagement/service/templates/read | 获取所有电子邮件模板，或获取 API 管理电子邮件模板详细信息 |
> | 行动 | Microsoft.ApiManagement/service/templates/write | 创建或更新 API 管理电子邮件模板或更新 API 管理电子邮件模板 |
> | 行动 | Microsoft.ApiManagement/service/tenant/delete | 删除租户的策略配置 |
> | 行动 | Microsoft.ApiManagement/service/tenant/deploy/action | 运行部署任务，以便将指定的 git 分支中的更改应用到数据库中的配置。 |
> | 行动 | Microsoft.ApiManagement/service/tenant/operationResults/read | 获取操作结果的列表，或获取特定操作的结果 |
> | 行动 | Microsoft.ApiManagement/service/tenant/read | 获取 Api 管理服务的全局策略定义。 或获取租户访问信息详细信息 |
> | 行动 | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | 再生成主访问密钥 |
> | 行动 | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | 再生成辅助访问密钥 |
> | 行动 | Microsoft.ApiManagement/service/tenant/save/action | 创建包含配置快照的、目标为储存库中的指定的分支的提交内容 |
> | 行动 | Microsoft.ApiManagement/service/tenant/syncState/read | 获取上次 git 同步的状态 |
> | 行动 | Microsoft.ApiManagement/service/tenant/validate/action | 验证指定的 git 分支中的更改 |
> | 行动 | Microsoft.ApiManagement/service/tenant/write | 设置租户的策略配置或更新租户访问详细信息 |
> | 行动 | Microsoft.ApiManagement/service/updatecertificate/action | 上传 API 管理服务的 SSL 证书 |
> | 行动 | Microsoft.ApiManagement/service/updatehostname/action | 设置、更新或删除 API 管理服务的自定义域名 |
> | 行动 | Microsoft.ApiManagement/service/users/action | 注册新用户 |
> | 行动 | Microsoft.ApiManagement/service/users/confirmations/send/action | 发送确认 |
> | 行动 | Microsoft.ApiManagement/service/users/delete | 删除特定的用户。 |
> | 行动 | Microsoft.ApiManagement/service/users/generateSsoUrl/action | 检索一个重定向 URL，其中包含用于将给定用户登录到开发人员门户的身份验证令牌。 |
> | 行动 | Microsoft.ApiManagement/service/users/groups/read | 列出所有用户组。 |
> | 行动 | ApiManagement/服务/用户/标识/读取 | 所有用户标识的列表。 |
> | 行动 | Microsoft.ApiManagement/service/users/keys/read | 获取与用户关联的密钥 |
> | 行动 | Microsoft.ApiManagement/service/users/read | 列出指定服务实例中已注册用户的集合。 或获取由标识符指定的用户的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/users/subscriptions/read | 列出指定用户的订阅的集合。 |
> | 行动 | Microsoft.ApiManagement/service/users/token/action | 获取用户的共享访问授权标记。 |
> | 行动 | Microsoft.ApiManagement/service/users/write | 创建或更新用户。 或更新由标识符指定的用户的详细信息。 |
> | 行动 | Microsoft.ApiManagement/service/write | 创建 API 管理服务的新实例 |
> | 行动 | Microsoft.ApiManagement/unregister/action | 取消注册 Microsoft.ApiManagement 资源提供程序的订阅 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Authorization/classicAdministrators/delete | 从订阅中删除管理员。 |
> | 行动 | Microsoft.Authorization/classicAdministrators/operationstatuses/read | 获取订阅的管理员操作状态。 |
> | 行动 | Microsoft.Authorization/classicAdministrators/read | 读取订阅的管理员。 |
> | 行动 | Microsoft.Authorization/classicAdministrators/write | 在订阅中添加或修改管理员。 |
> | 行动 | Microsoft.Authorization/denyAssignments/delete | 在指定范围处删除拒绝分配。 |
> | 行动 | Microsoft.Authorization/denyAssignments/read | 获取拒绝分配的相关信息。 |
> | 行动 | Microsoft.Authorization/denyAssignments/write | 在指定范围处创建拒绝分配。 |
> | 行动 | Microsoft.Authorization/elevateAccess/action | 向调用方授予租户范围的“用户访问管理员”访问权限 |
> | 行动 | Microsoft.Authorization/locks/delete | 删除指定范围的锁。 |
> | 行动 | Microsoft.Authorization/locks/read | 获取指定范围的锁。 |
> | 行动 | Microsoft.Authorization/locks/write | 添加指定范围的锁。 |
> | 行动 | Microsoft.Authorization/operations/read | 获取操作列表 |
> | 行动 | Microsoft.Authorization/permissions/read | 列出调用方在给定范围拥有的所有权限。 |
> | 行动 | Microsoft. 授权/策略/审核/操作 | 使用 "审核" 效果评估 Azure 策略时采取的操作 |
> | 行动 | Microsoft. Authorization/策略/auditIfNotExists/action | 使用 "auditIfNotExists" 效果评估 Azure 策略时采取的操作 |
> | 行动 | Microsoft. 授权/策略/拒绝/操作 | 使用 "拒绝" 效果评估 Azure 策略时采取的操作 |
> | 行动 | Microsoft. Authorization/策略/deployIfNotExists/action | 使用 "deployIfNotExists" 效果评估 Azure 策略时采取的操作 |
> | 行动 | Microsoft.Authorization/policyAssignments/delete | 删除指定范围的策略分配。 |
> | 行动 | Microsoft.Authorization/policyAssignments/read | 获取有关策略分配的信息。 |
> | 行动 | Microsoft.Authorization/policyAssignments/write | 创建指定范围的策略分配。 |
> | 行动 | Microsoft.Authorization/policyDefinitions/delete | 删除策略定义。 |
> | 行动 | Microsoft.Authorization/policyDefinitions/read | 获取有关策略定义的信息。 |
> | 行动 | Microsoft.Authorization/policyDefinitions/write | 创建自定义策略定义。 |
> | 行动 | Microsoft.Authorization/policySetDefinitions/delete | 删除策略集定义。 |
> | 行动 | Microsoft.Authorization/policySetDefinitions/read | 获取有关策略集定义的信息。 |
> | 行动 | Microsoft.Authorization/policySetDefinitions/write | 创建自定义策略集定义。 |
> | 行动 | Microsoft.Authorization/providerOperations/read | 获取可在角色定义中使用的所有资源提供程序的操作。 |
> | 行动 | Microsoft.Authorization/roleAssignments/delete | 删除指定范围的角色分配。 |
> | 行动 | Microsoft.Authorization/roleAssignments/read | 获取有关角色分配的信息。 |
> | 行动 | Microsoft.Authorization/roleAssignments/write | 创建指定范围的角色分配。 |
> | 行动 | Microsoft.Authorization/roleDefinitions/delete | 删除指定的自定义角色定义。 |
> | 行动 | Microsoft.Authorization/roleDefinitions/read | 获取有关角色定义的信息。 |
> | 行动 | Microsoft.Authorization/roleDefinitions/write | 使用指定的权限和可分配的范围创建或更新自定义角色定义。 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | 读取 Azure Automation DSC 的注册信息 |
> | 行动 | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | 写入重新生成 Azure Automation DSC 密钥的请求 |
> | 行动 | Microsoft.Automation/automationAccounts/certificates/delete | 删除 Azure 自动化证书资产 |
> | 行动 | Microsoft.Automation/automationAccounts/certificates/getCount/action | 读取证书的计数 |
> | 行动 | Microsoft.Automation/automationAccounts/certificates/read | 获取 Azure 自动化证书资产 |
> | 行动 | Microsoft.Automation/automationAccounts/certificates/write | 创建或更新 Azure 自动化证书资产 |
> | 行动 | Microsoft.Automation/automationAccounts/compilationjobs/read | 读取 Azure Automation DSC 的编译 |
> | 行动 | Microsoft.Automation/automationAccounts/compilationjobs/read | 读取 Azure Automation DSC 的编译 |
> | 行动 | Microsoft.Automation/automationAccounts/compilationjobs/write | 写入 Azure Automation DSC 的编译 |
> | 行动 | Microsoft.Automation/automationAccounts/compilationjobs/write | 写入 Azure Automation DSC 的编译 |
> | 行动 | Microsoft.Automation/automationAccounts/configurations/content/read | 读取配置媒体内容 |
> | 行动 | Microsoft.Automation/automationAccounts/configurations/delete | 删除 Azure Automation DSC 的内容 |
> | 行动 | Microsoft.Automation/automationAccounts/configurations/getCount/action | 读取 Azure Automation DSC 的内容计数 |
> | 行动 | Microsoft.Automation/automationAccounts/configurations/read | 获取 Azure Automation DSC 的内容 |
> | 行动 | Microsoft.Automation/automationAccounts/configurations/write | 写入 Azure Automation DSC 的内容 |
> | 行动 | Microsoft.Automation/automationAccounts/connections/delete | 删除 Azure 自动化连接资产 |
> | 行动 | Microsoft.Automation/automationAccounts/connections/getCount/action | 读取连接的计数 |
> | 行动 | Microsoft.Automation/automationAccounts/connections/read | 获取 Azure 自动化连接资产 |
> | 行动 | Microsoft.Automation/automationAccounts/connections/write | 创建或更新 Azure 自动化连接资产 |
> | 行动 | Microsoft.Automation/automationAccounts/connectionTypes/delete | 删除 Azure 自动化连接类型资产 |
> | 行动 | Microsoft.Automation/automationAccounts/connectionTypes/read | 获取 Azure 自动化连接类型资产 |
> | 行动 | Microsoft.Automation/automationAccounts/connectionTypes/write | 创建 Azure 自动化连接类型资产 |
> | 行动 | Microsoft.Automation/automationAccounts/credentials/delete | 删除 Azure 自动化凭据资产 |
> | 行动 | Microsoft.Automation/automationAccounts/credentials/getCount/action | 读取凭据的计数 |
> | 行动 | Microsoft.Automation/automationAccounts/credentials/read | 获取 Azure 自动化凭据资产 |
> | 行动 | Microsoft.Automation/automationAccounts/credentials/write | 创建或更新 Azure 自动化凭据资产 |
> | 行动 | Microsoft.Automation/automationAccounts/delete | 删除 Azure 自动化帐户 |
> | 行动 | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | 删除混合 Runbook 辅助角色资源 |
> | 行动 | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | 读取混合 Runbook 辅助角色资源 |
> | 行动 | Microsoft.Automation/automationAccounts/jobs/output/read | 获取作业的输出 |
> | 行动 | Microsoft.Automation/automationAccounts/jobs/read | 获取 Azure 自动化作业 |
> | 行动 | Microsoft.Automation/automationAccounts/jobs/resume/action | 恢复 Azure 自动化作业 |
> | 行动 | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | 执行作业时获取 Azure 自动化 Runbook 的内容 |
> | 行动 | Microsoft.Automation/automationAccounts/jobs/stop/action | 停止 Azure 自动化作业 |
> | 行动 | Microsoft.Automation/automationAccounts/jobs/streams/read | 获取 Azure 自动化作业流 |
> | 行动 | Microsoft.Automation/automationAccounts/jobs/streams/read | 获取 Azure 自动化作业流 |
> | 行动 | Microsoft.Automation/automationAccounts/jobs/suspend/action | 暂停 Azure 自动化作业 |
> | 行动 | Microsoft.Automation/automationAccounts/jobs/write | 创建 Azure 自动化作业 |
> | 行动 | Microsoft.Automation/automationAccounts/jobSchedules/delete | 删除 Azure 自动化作业计划 |
> | 行动 | Microsoft.Automation/automationAccounts/jobSchedules/read | 获取 Azure 自动化作业计划 |
> | 行动 | Microsoft.Automation/automationAccounts/jobSchedules/write | 创建 Azure 自动化作业计划 |
> | 行动 | Microsoft.Automation/automationAccounts/linkedWorkspace/read | 获取链接到自动化帐户的工作区 |
> | 行动 | Microsoft.Automation/automationAccounts/listKeys/action | 读取自动化帐户的键 |
> | 行动 | Microsoft.Automation/automationAccounts/modules/activities/read | 获取 Azure 自动化活动 |
> | 行动 | Microsoft.Automation/automationAccounts/modules/delete | 删除 Azure 自动化 Powershell 模块 |
> | 行动 | Microsoft.Automation/automationAccounts/modules/getCount/action | 获取自动化帐户中的 Powershell 模块的计数 |
> | 行动 | Microsoft.Automation/automationAccounts/modules/read | 获取 Azure 自动化 Powershell 模块 |
> | 行动 | Microsoft.Automation/automationAccounts/modules/write | 创建或更新 Azure 自动化 Powershell 模块 |
> | 行动 | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | 删除 Azure Automation DSC 的节点配置 |
> | 行动 | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | 读取 Azure Automation DSC 的节点配置内容 |
> | 行动 | Microsoft.Automation/automationAccounts/nodeConfigurations/read | 读取 Azure Automation DSC 的节点配置 |
> | 行动 | Microsoft.Automation/automationAccounts/nodeConfigurations/write | 写入 Azure Automation DSC 的节点配置 |
> | 行动 | Microsoft.Automation/automationAccounts/nodecounts/read | 读取指定类型的节点计数摘要 |
> | 行动 | Microsoft.Automation/automationAccounts/nodes/delete | 删除 Azure Automation DSC 节点 |
> | 行动 | Microsoft.Automation/automationAccounts/nodes/read | 读取 Azure Automation DSC 节点 |
> | 行动 | Microsoft.Automation/automationAccounts/nodes/reports/content/read | 读取 Azure Automation DSC 报表内容 |
> | 行动 | Microsoft.Automation/automationAccounts/nodes/reports/read | 读取 Azure Automation DSC 报表 |
> | 行动 | Microsoft.Automation/automationAccounts/nodes/write | 创建或更新 Azure Automation DSC 节点 |
> | 行动 | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | 获取 Azure 自动化 TypeField |
> | 行动 | Microsoft.Automation/automationAccounts/python2Packages/delete | 删除 Azure 自动化 Python 2 包 |
> | 行动 | Microsoft.Automation/automationAccounts/python2Packages/read | 获取 Azure 自动化 Python 2 包 |
> | 行动 | Microsoft.Automation/automationAccounts/python2Packages/write | 创建或更新 Azure 自动化 Python 2 包 |
> | 行动 | Microsoft.Automation/automationAccounts/python3Packages/delete | 删除 Azure 自动化 Python 3 包 |
> | 行动 | Microsoft.Automation/automationAccounts/python3Packages/read | 获取 Azure 自动化 Python 3 包 |
> | 行动 | Microsoft.Automation/automationAccounts/python3Packages/write | 创建或更新 Azure 自动化 Python 3 包 |
> | 行动 | Microsoft.Automation/automationAccounts/read | 获取 Azure 自动化帐户。 |
> | 行动 | Microsoft.Automation/automationAccounts/runbooks/content/read | 获取 Azure 自动化 Runbook 的内容 |
> | 行动 | Microsoft.Automation/automationAccounts/runbooks/delete | 删除 Azure 自动化 Runbook |
> | 行动 | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | 创建 Azure 自动化 Runbook 草稿的内容 |
> | 行动 | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | 获取 Azure 自动化 Runbook 草稿操作结果 |
> | 行动 | Microsoft.Automation/automationAccounts/runbooks/draft/read | 获取 Azure 自动化 Runbook 草稿 |
> | 行动 | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | 获取 Azure 自动化 Runbook 草稿测试作业 |
> | 行动 | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | 恢复 Azure 自动化 Runbook 草稿测试作业 |
> | 行动 | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | 停止 Azure 自动化 Runbook 草稿测试作业 |
> | 行动 | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | 暂停 Azure 自动化 Runbook 草稿测试作业 |
> | 行动 | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | 创建 Azure 自动化 Runbook 草稿测试作业 |
> | 行动 | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | 撤消对 Azure 自动化 Runbook 草稿的编辑 |
> | 行动 | Microsoft.Automation/automationAccounts/runbooks/getCount/action | 获取 Azure 自动化 Runbook 的计数 |
> | 行动 | Microsoft.Automation/automationAccounts/runbooks/publish/action | 发布 Azure 自动化 Runbook 草稿 |
> | 行动 | Microsoft.Automation/automationAccounts/runbooks/read | 获取 Azure 自动化 Runbook |
> | 行动 | Microsoft.Automation/automationAccounts/runbooks/write | 创建或更新 Azure 自动化 Runbook |
> | 行动 | Microsoft.Automation/automationAccounts/schedules/delete | 删除 Azure 自动化计划资产 |
> | 行动 | Microsoft.Automation/automationAccounts/schedules/getCount/action | 获取 Azure 自动化计划的计数 |
> | 行动 | Microsoft.Automation/automationAccounts/schedules/read | 获取 Azure 自动化计划资产 |
> | 行动 | Microsoft.Automation/automationAccounts/schedules/write | 创建或更新 Azure 自动化计划资产 |
> | 行动 | AutomationAccounts/softwareUpdateConfigurationMachineRuns/read | 获取 Azure 自动化软件更新配置计算机运行 |
> | 行动 | AutomationAccounts/softwareUpdateConfigurationRuns/read | 获取 Azure 自动化软件更新配置运行 |
> | 行动 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | 删除 Azure 自动化软件更新配置 |
> | 行动 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | 删除 Azure 自动化软件更新配置 |
> | 行动 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | 获取 Azure 自动化软件更新配置 |
> | 行动 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | 获取 Azure 自动化软件更新配置 |
> | 行动 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | 创建或更新 Azure 自动化软件更新配置 |
> | 行动 | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | 创建或更新 Azure 自动化软件更新配置 |
> | 行动 | Microsoft.Automation/automationAccounts/statistics/read | 获取 Azure 自动化统计信息 |
> | 行动 | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | 获取 Azure 自动化更新部署计算机 |
> | 行动 | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | 获取 Azure 自动化更新管理修补程序作业 |
> | 行动 | Microsoft.Automation/automationAccounts/usages/read | 获取 Azure 自动化使用情况 |
> | 行动 | Microsoft.Automation/automationAccounts/variables/delete | 删除 Azure 自动化变量资产 |
> | 行动 | Microsoft.Automation/automationAccounts/variables/read | 读取 Azure 自动化变量资产 |
> | 行动 | Microsoft.Automation/automationAccounts/variables/write | 创建或更新 Azure 自动化变量资产 |
> | 行动 | Microsoft.Automation/automationAccounts/watchers/delete | 删除 Azure 自动化观察程序作业 |
> | 行动 | Microsoft.Automation/automationAccounts/watchers/read | 获取 Azure 自动化观察程序作业 |
> | 行动 | Microsoft.Automation/automationAccounts/watchers/start/action | 启动 Azure 自动化观察程序作业 |
> | 行动 | Microsoft.Automation/automationAccounts/watchers/stop/action | 停止 Azure 自动化观察程序作业 |
> | 行动 | Microsoft.Automation/automationAccounts/watchers/streams/read | 获取 Azure 自动化观察程序作业流 |
> | 行动 | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | 删除 Azure 自动化观察程序作业操作 |
> | 行动 | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | 获取 Azure 自动化观察程序作业操作 |
> | 行动 | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | 创建 Azure 自动化观察程序作业操作 |
> | 行动 | Microsoft.Automation/automationAccounts/watchers/write | 创建 Azure 自动化观察程序作业 |
> | 行动 | Microsoft.Automation/automationAccounts/webhooks/action | 生成 Azure 自动化 Webhook 的 URI |
> | 行动 | Microsoft.Automation/automationAccounts/webhooks/delete | 删除 Azure 自动化 Webhook  |
> | 行动 | Microsoft.Automation/automationAccounts/webhooks/read | 读取 Azure 自动化 Webhook |
> | 行动 | Microsoft.Automation/automationAccounts/webhooks/write | 创建或更新 Azure 自动化 Webhook |
> | 行动 | Microsoft.Automation/automationAccounts/write | 创建或更新 Azure 自动化帐户 |
> | 行动 | Microsoft.Automation/operations/read | 获取可对 Azure 自动化资源使用的操作 |
> | 行动 | Microsoft.Automation/register/action | 将订阅注册到 Azure Automation |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.AzureActiveDirectory/b2cDirectories/delete | 删除 B2C 目录资源 |
> | 行动 | Microsoft.AzureActiveDirectory/b2cDirectories/read | 查看 B2C 目录资源 |
> | 行动 | Microsoft.AzureActiveDirectory/b2cDirectories/write | 创建或更新 B2C 目录资源 |
> | 行动 | AzureActiveDirectory/b2ctenants/read | 列出用户所属的所有 B2C 租户 |
> | 行动 | Microsoft.AzureActiveDirectory/operations/read | 读取适用于 Microsoft.AzureActiveDirectory 资源提供程序的所有 API 操作 |
> | 行动 | Microsoft.AzureActiveDirectory/register/action | 注册 Microsoft.AzureActiveDirectory 资源提供程序的订阅 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.AzureStack/Operations/read | 获取资源提供程序操作的属性 |
> | 行动 | Microsoft.AzureStack/register/action | 使用 Microsoft.AzureStack 资源提供程序注册订阅 |
> | 行动 | Microsoft.AzureStack/registrations/customerSubscriptions/delete | 删除 Azure Stack 客户订阅 |
> | 行动 | Microsoft.AzureStack/registrations/customerSubscriptions/read | 获取 Azure Stack 客户订阅的属性 |
> | 行动 | Microsoft.AzureStack/registrations/customerSubscriptions/write | 创建或更新 Azure Stack 客户订阅 |
> | 行动 | Microsoft.AzureStack/registrations/delete | 删除 Azure Stack 注册 |
> | 行动 | Microsoft.AzureStack/registrations/getActivationKey/action | 获取最新的 Azure Stack 激活密钥 |
> | 行动 | Microsoft.AzureStack/registrations/products/listDetails/action | 检索 Azure Stack 市场产品的扩展详细信息 |
> | 行动 | Microsoft.AzureStack/registrations/products/read | 获取 Azure Stack 市场产品的属性 |
> | 行动 | Test-azurestack/注册/产品/uploadProductLog/操作 | 记录 Azure Stack Marketplace 产品操作状态和时间戳 |
> | 行动 | Microsoft.AzureStack/registrations/read | 获取 Azure Stack 注册的属性 |
> | 行动 | Microsoft.AzureStack/registrations/write | 创建或更新 Azure Stack 注册 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Batch/batchAccounts/applications/delete | 删除应用程序 |
> | 行动 | Microsoft.Batch/batchAccounts/applications/read | 列出应用程序，或获取应用程序的属性 |
> | 行动 | Microsoft.Batch/batchAccounts/applications/versions/activate/action | 激活应用程序包 |
> | 行动 | Microsoft.Batch/batchAccounts/applications/versions/delete | 删除应用程序包 |
> | 行动 | Microsoft.Batch/batchAccounts/applications/versions/read | 获取应用程序包的属性 |
> | 行动 | Microsoft.Batch/batchAccounts/applications/versions/write | 创建新的应用程序包，或更新现有的应用程序包 |
> | 行动 | Microsoft.Batch/batchAccounts/applications/write | 创建新的应用程序，或更新现有的应用程序 |
> | 行动 | Microsoft.Batch/batchAccounts/certificateOperationResults/read | 获取 Batch 帐户上长时间运行的证书操作的结果 |
> | 行动 | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | 取消对 Batch 帐户上证书删除失败的操作 |
> | 行动 | Microsoft.Batch/batchAccounts/certificates/delete | 从 Batch 帐户中删除证书 |
> | 行动 | Microsoft.Batch/batchAccounts/certificates/read | 列出 Batch 帐户上的证书或获取证书的属性 |
> | 行动 | Microsoft.Batch/batchAccounts/certificates/write | 在 Batch 帐户上创建新的证书或更新现有证书 |
> | 行动 | Microsoft.Batch/batchAccounts/delete | 删除批处理帐户 |
> | DataAction | BatchAccounts/作业/删除 | 从 Batch 帐户中删除作业 |
> | DataAction | BatchAccounts/作业/读取 | 列出 Batch 帐户上的作业或获取作业的属性 |
> | DataAction | BatchAccounts/作业/写入 | 在 Batch 帐户上创建新作业，或更新现有作业 |
> | DataAction | BatchAccounts/jobSchedules/delete | 从 Batch 帐户中删除作业计划 |
> | DataAction | BatchAccounts/jobSchedules/read | 列出批处理帐户的作业计划或获取作业计划的属性 |
> | DataAction | BatchAccounts/jobSchedules/write | 在 Batch 帐户上创建新的作业计划或更新现有作业计划 |
> | 行动 | Microsoft.Batch/batchAccounts/listkeys/action | 列出批处理帐户的访问密钥 |
> | 行动 | Microsoft.Batch/batchAccounts/operationResults/read | 获取长时间运行的 Batch 帐户操作的结果 |
> | 行动 | Microsoft.Batch/batchAccounts/poolOperationResults/read | 获取 Batch 帐户上长时间运行的池操作的结果 |
> | 行动 | Microsoft.Batch/batchAccounts/pools/delete | 从 Batch 帐户中删除池 |
> | 行动 | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | 禁用 Batch 帐户池的自动缩放 |
> | 行动 | Microsoft.Batch/batchAccounts/pools/read | 列出 Batch 帐户上的池，或获取池的属性 |
> | 行动 | Microsoft.Batch/batchAccounts/pools/stopResize/action | 停止正在对 Batch 帐户池进行的重设大小操作 |
> | 行动 | Microsoft.Batch/batchAccounts/pools/write | 在 Batch 帐户上创建新池，或更新现有池 |
> | 行动 | Microsoft.Batch/batchAccounts/read | 列出批处理帐户，或获取批处理帐户的属性 |
> | 行动 | Microsoft.Batch/batchAccounts/regeneratekeys/action | 再生成批处理帐户的访问密钥 |
> | 行动 | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | 同步针对批处理帐户配置的自动存储帐户的访问密钥 |
> | 行动 | Microsoft.Batch/batchAccounts/write | 创建新的批处理帐户，或更新现有的批处理帐户 |
> | 行动 | Microsoft.Batch/locations/accountOperationResults/read | 获取长时间运行的 Batch 帐户操作的结果 |
> | 行动 | Microsoft.Batch/locations/checkNameAvailability/action | 检查帐户名称是否有效且未被使用。 |
> | 行动 | Microsoft.Batch/locations/quotas/read | 获取指定 Azure 区域中指定订阅的批处理配额 |
> | 行动 | Microsoft.Batch/operations/read | 列出适用于 Microsoft.Batch 资源提供程序的操作 |
> | 行动 | Microsoft.Batch/register/action | 注册批处理资源提供程序的订阅，并启用批处理帐户的创建 |
> | 行动 | Microsoft.Batch/unregister/action | 取消注册 Batch 资源提供程序的订阅，阻止创建 Batch 帐户 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | BillingAccounts/协议/读取 |  |
> | 行动 | BillingAccounts/billingPermissions/read |  |
> | 行动 | BillingAccounts/billingProfiles/billingPermissions/read |  |
> | 行动 | BillingAccounts/billingProfiles/customers/read |  |
> | 行动 | BillingAccounts/billingProfiles/发票/价目表/下载/操作 |  |
> | 行动 | BillingAccounts/billingProfiles/invoiceSections/billingPermissions/read |  |
> | 行动 | BillingAccounts/billingProfiles/invoiceSections/billingSubscriptions/move/action |  |
> | 行动 | BillingAccounts/billingProfiles/invoiceSections/billingSubscriptions/传输/操作 |  |
> | 行动 | BillingAccounts/billingProfiles/invoiceSections/billingSubscriptions/validateMoveEligibility/action |  |
> | 行动 | BillingAccounts/billingProfiles/invoiceSections/products/移动/操作 |  |
> | 行动 | BillingAccounts/billingProfiles/invoiceSections/products/转让/操作 |  |
> | 行动 | BillingAccounts/billingProfiles/invoiceSections/products/validateMoveEligibility/action |  |
> | 行动 | BillingAccounts/billingProfiles/invoiceSections/read |  |
> | 行动 | BillingAccounts/billingProfiles/invoiceSections/write |  |
> | 行动 | BillingAccounts/billingProfiles/价目表/下载/操作 |  |
> | 行动 | BillingAccounts/billingProfiles/read |  |
> | 行动 | BillingAccounts/billingProfiles/write |  |
> | 行动 | BillingAccounts/billingProfiles/write |  |
> | 行动 | BillingAccounts/billingSubscriptions/read |  |
> | 行动 | BillingAccounts/customers/billingPermissions/read |  |
> | 行动 | BillingAccounts/客户/读取 |  |
> | 行动 | Microsoft.Billing/billingAccounts/departments/read |  |
> | 行动 | BillingAccounts/enrollmentAccounts/billingPermissions/read |  |
> | 行动 | Microsoft.Billing/billingAccounts/enrollmentAccounts/read |  |
> | 行动 | BillingAccounts/enrollmentDepartments/billingPermissions/read |  |
> | 行动 | BillingAccounts/listInvoiceSectionsWithCreateSubscriptionPermission/action |  |
> | 行动 | BillingAccounts/产品/读取 |  |
> | 行动 | Microsoft.Billing/billingAccounts/read |  |
> | 行动 | BillingAccounts/写入 |  |
> | 行动 | Microsoft.Billing/departments/read |  |
> | 行动 | Microsoft.Billing/register/action |  |
> | 行动 | ValidateAddress/操作 |  |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.BingMaps/mapApis/Delete | 删除操作 |
> | 行动 | Microsoft.BingMaps/mapApis/listSecrets/action | 列出机密 |
> | 行动 | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | 读取资源的单一登录授权令牌 |
> | 行动 | Microsoft.BingMaps/mapApis/Read | 读取操作 |
> | 行动 | Microsoft.BingMaps/mapApis/regenerateKey/action | 再生成密钥 |
> | 行动 | Microsoft.BingMaps/mapApis/Write | 写入操作 |
> | 行动 | Microsoft.BingMaps/Operations/read | 操作说明。 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | 区块链/blockchainMembers/delete | 删除现有的区块链成员。 |
> | 行动 | 区块链/blockchainMembers/listApiKeys/action | 获取或列出现有的区块链成员 Api 密钥。 |
> | 行动 | 区块链/blockchainMembers/read | 获取或列出现有的区块链成员。 |
> | DataAction | 区块链/blockchainMembers/transactionNodes/connect/action | 连接到区块链成员事务节点。 |
> | 行动 | 区块链/blockchainMembers/transactionNodes/delete | 删除现有的区块链成员事务节点。 |
> | 行动 | 区块链/blockchainMembers/transactionNodes/listApiKeys/action | 获取或列出现有的区块链成员事务节点 Api 密钥。 |
> | 行动 | 区块链/blockchainMembers/transactionNodes/read | 获取或列出现有的区块链成员事务节点。 |
> | 行动 | 区块链/blockchainMembers/transactionNodes/write | 创建或更新区块链成员事务节点。 |
> | 行动 | 区块链/blockchainMembers/write | 创建或更新区块链成员。 |
> | 行动 | 区块链/位置/blockchainMemberOperationResults/读取 | 获取区块链成员的操作结果。 |
> | 行动 | 区块链/位置/checkNameAvailability/action | 检查资源名称是否有效且未被使用。 |
> | 行动 | 区块链/操作/读取 | 列出 Microsoft 区块链资源提供程序中的所有操作。 |
> | 行动 | 区块链/注册/操作 | 注册区块链资源提供程序的订阅。 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Blueprint/blueprintAssignments/assignmentOperations/read | 读取任何蓝图项目 |
> | 行动 | Microsoft.Blueprint/blueprintAssignments/delete | 删除任何蓝图项目 |
> | 行动 | Microsoft.Blueprint/blueprintAssignments/read | 读取任何蓝图项目 |
> | 行动 | BlueprintAssignments/whoisblueprint/action | 获取 Azure 蓝图服务主体对象 Id。 |
> | 行动 | Microsoft.Blueprint/blueprintAssignments/write | 创建或更新任何蓝图项目 |
> | 行动 | Microsoft.Blueprint/blueprints/artifacts/delete | 删除任何蓝图项目 |
> | 行动 | Microsoft.Blueprint/blueprints/artifacts/read | 读取任何蓝图项目 |
> | 行动 | Microsoft.Blueprint/blueprints/artifacts/write | 创建或更新任何蓝图项目 |
> | 行动 | Microsoft.Blueprint/blueprints/delete | 删除任何蓝图 |
> | 行动 | Microsoft.Blueprint/blueprints/read | 读取任何蓝图 |
> | 行动 | Microsoft.Blueprint/blueprints/versions/artifacts/read | 读取任何蓝图项目 |
> | 行动 | Microsoft.Blueprint/blueprints/versions/delete | 删除任何蓝图 |
> | 行动 | Microsoft.Blueprint/blueprints/versions/read | 读取任何蓝图 |
> | 行动 | Microsoft.Blueprint/blueprints/versions/write | 创建或更新任何蓝图 |
> | 行动 | Microsoft.Blueprint/blueprints/write | 创建或更新任何蓝图 |
> | 行动 | Microsoft.Blueprint/register/action | 注册 Azure 蓝图资源提供程序 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.BotService/botServices/channels/delete | 删除机器人服务 |
> | 行动 | Microsoft.BotService/botServices/channels/read | 读取机器人服务 |
> | 行动 | Microsoft.BotService/botServices/channels/write | 写入机器人服务 |
> | 行动 | Microsoft.BotService/botServices/connections/delete | 删除机器人服务 |
> | 行动 | Microsoft.BotService/botServices/connections/read | 读取机器人服务 |
> | 行动 | Microsoft.BotService/botServices/connections/write | 写入机器人服务 |
> | 行动 | Microsoft.BotService/botServices/delete | 删除机器人服务 |
> | 行动 | Microsoft.BotService/botServices/read | 读取机器人服务 |
> | 行动 | Microsoft.BotService/botServices/write | 写入机器人服务 |
> | 行动 | Microsoft.BotService/locations/operationresults/read | 读取异步操作的状态 |
> | 行动 | Microsoft.BotService/Operations/read | 读取所有资源类型的操作 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Cache/checknameavailability/action | 检查名称是否可用于新的 Redis 缓存 |
> | 行动 | Microsoft.Cache/locations/operationresults/read | 获取之前将“Location”标头返回给客户端的长时间运行的操作结果 |
> | 行动 | Microsoft.Cache/operations/read | 列出“Microsoft.Cache”提供程序支持的操作。 |
> | 行动 | Microsoft.Cache/redis/delete | 删除整个 Redis 缓存 |
> | 行动 | Microsoft.Cache/redis/export/action | 将 Redis 数据以指定的格式导出到带前缀的存储 Blob |
> | 行动 | Microsoft.Cache/redis/firewallRules/delete | 删除 Redis 缓存的 IP 防火墙规则 |
> | 行动 | Microsoft.Cache/redis/firewallRules/read | 获取 Redis 缓存的 IP 防火墙规则 |
> | 行动 | Microsoft.Cache/redis/firewallRules/write | 编辑 Redis 缓存的 IP 防火墙规则 |
> | 行动 | Microsoft.Cache/redis/forceReboot/action | 强制重新启动缓存实例（可能会发生数据丢失）。 |
> | 行动 | Microsoft.Cache/redis/import/action | 将多个 Blob 中指定格式的数据导入 Redis |
> | 行动 | Microsoft.Cache/redis/linkedservers/delete | 从 Redis 缓存中删除链接服务器 |
> | 行动 | Microsoft.Cache/redis/linkedservers/read | 获取与 Redis 缓存关联的链接服务器。 |
> | 行动 | Microsoft.Cache/redis/linkedservers/write | 将链接服务器添加到 Redis 缓存 |
> | 行动 | Microsoft.Cache/redis/listKeys/action | 在管理门户中查看 Redis 缓存访问密钥的值 |
> | 行动 | Microsoft.Cache/redis/listUpgradeNotifications/read | 列出缓存租户的最新升级通知。 |
> | 行动 | Microsoft.Cache/redis/metricDefinitions/read | 获取 Redis 缓存的可用指标 |
> | 行动 | Microsoft.Cache/redis/patchSchedules/delete | 删除 Redis 缓存的修补计划 |
> | 行动 | Microsoft.Cache/redis/patchSchedules/read | 获取 Redis 缓存的修补计划 |
> | 行动 | Microsoft.Cache/redis/patchSchedules/write | 修改 Redis 缓存的修补计划 |
> | 行动 | Microsoft.Cache/redis/read | 在管理门户中查看 Redis 缓存的设置和配置 |
> | 行动 | Microsoft.Cache/redis/regenerateKey/action | 在管理门户中更改 Redis 缓存访问密钥的值 |
> | 行动 | Microsoft.Cache/redis/start/action | 启动缓存实例。 |
> | 行动 | Microsoft.Cache/redis/stop/action | 停止缓存实例。 |
> | 行动 | Microsoft.Cache/redis/write | 在管理门户中修改 Redis 缓存的设置和配置 |
> | 行动 | Microsoft.Cache/register/action | 将“Microsoft.Cache”资源提供程序注册到订阅 |
> | 行动 | Microsoft.Cache/unregister/action | 从订阅中取消注册“Microsoft.Cache”资源提供程序 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Capacity/appliedreservations/read | 读取所有预订 |
> | 行动 | Calculateexchange/操作 | 计算新购买的汇率和价格，并返回策略错误。 |
> | 行动 | Microsoft.Capacity/calculateprice/action | 计算任何预留价格 |
> | 行动 | Microsoft.Capacity/catalogs/read | 读取预留目录 |
> | 行动 | Microsoft.Capacity/checkoffers/action | 检查任何订阅套餐 |
> | 行动 | Microsoft.Capacity/checkscopes/action | 检查任何订阅 |
> | 行动 | Microsoft.Capacity/commercialreservationorders/read | 获取在任何租户中创建的预留订单 |
> | 行动 | Microsoft. 容量/交换/操作 | 交换任何预订 |
> | 行动 | Microsoft.Capacity/operations/read | 读取任何操作 |
> | 行动 | Microsoft.Capacity/register/action | 注册容量资源提供程序，并启用容量资源的创建。 |
> | 行动 | Microsoft.Capacity/reservationorders/action | 更新任何预订 |
> | 行动 | Microsoft.Capacity/reservationorders/availablescopes/action | 查找任何可用范围 |
> | 行动 | Reservationorders/calculaterefund/action | 计算新购买的退款金额和价格并返回策略错误。 |
> | 行动 | Microsoft.Capacity/reservationorders/delete | 删除任何预订 |
> | 行动 | Microsoft.Capacity/reservationorders/merge/action | 合并任何预留 |
> | 行动 | Microsoft.Capacity/reservationorders/read | 读取所有预订 |
> | 行动 | Microsoft.Capacity/reservationorders/reservations/action | 更新任何预订 |
> | 行动 | Reservationorders/availablescopes/action | 查找任何可用范围 |
> | 行动 | Microsoft.Capacity/reservationorders/reservations/delete | 删除任何预订 |
> | 行动 | Microsoft.Capacity/reservationorders/reservations/read | 读取所有预订 |
> | 行动 | Microsoft.Capacity/reservationorders/reservations/revisions/read | 读取所有预订 |
> | 行动 | Microsoft.Capacity/reservationorders/reservations/write | 创建任何预订 |
> | 行动 | Microsoft.Capacity/reservationorders/return/action | 返回任何预留 |
> | 行动 | Microsoft.Capacity/reservationorders/split/action | 拆分任何预留 |
> | 行动 | Microsoft.Capacity/reservationorders/swap/action | 交换任何预留 |
> | 行动 | Microsoft.Capacity/reservationorders/write | 创建任何预订 |
> | 行动 | Microsoft.Capacity/tenants/register/action | 注册任何租户 |
> | 行动 | Microsoft.Capacity/unregister/action | 取消注册任何租户 |
> | 行动 | Microsoft.Capacity/validatereservationorder/action | 验证任何预留 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Cdn/CheckNameAvailability/action |  |
> | 行动 | Microsoft.Cdn/CheckResourceUsage/action |  |
> | 行动 | Microsoft.Cdn/edgenodes/delete |  |
> | 行动 | Microsoft.Cdn/edgenodes/read |  |
> | 行动 | Microsoft.Cdn/edgenodes/write |  |
> | 行动 | Microsoft.Cdn/operationresults/delete |  |
> | 行动 | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | 行动 | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | 行动 | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | 行动 | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | 行动 | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | 行动 | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | 行动 | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | 行动 | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | 行动 | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | 行动 | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | 行动 | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | 行动 | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | 行动 | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | 行动 | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | 行动 | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | 行动 | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | 行动 | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | 行动 | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | 行动 | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | 行动 | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | 行动 | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | 行动 | Microsoft.Cdn/operationresults/profileresults/read |  |
> | 行动 | Microsoft.Cdn/operationresults/profileresults/write |  |
> | 行动 | Microsoft.Cdn/operationresults/read |  |
> | 行动 | Microsoft.Cdn/operationresults/write |  |
> | 行动 | Microsoft.Cdn/operations/read |  |
> | 行动 | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | 行动 | Microsoft.Cdn/profiles/delete |  |
> | 行动 | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | 行动 | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | 行动 | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | 行动 | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | 行动 | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | 行动 | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | 行动 | Microsoft.Cdn/profiles/endpoints/delete |  |
> | 行动 | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | 行动 | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | 行动 | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | 行动 | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | 行动 | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | 行动 | Microsoft.Cdn/profiles/endpoints/read |  |
> | 行动 | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | 行动 | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | 行动 | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | 行动 | Microsoft.Cdn/profiles/endpoints/write |  |
> | 行动 | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | 行动 | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | 行动 | Microsoft.Cdn/profiles/read |  |
> | 行动 | Microsoft.Cdn/profiles/write |  |
> | 行动 | Microsoft.Cdn/register/action | 注册 CDN 资源提供程序的订阅，并启用 CDN 配置文件的创建。 |
> | 行动 | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | 删除现有证书 |
> | 行动 | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | 获取证书列表 |
> | 行动 | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | 添加新的证书，或更新现有的证书 |
> | 行动 | Microsoft.CertificateRegistration/certificateOrders/Delete | 删除现有的 AppServiceCertificate |
> | 行动 | Microsoft.CertificateRegistration/certificateOrders/Read | 获取 CertificateOrders 列表 |
> | 行动 | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | 重新颁发现有的 certificateorder |
> | 行动 | Microsoft.CertificateRegistration/certificateOrders/renew/Action | 续订现有的 certificateorder |
> | 行动 | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | 重新发送证书电子邮件 |
> | 行动 | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | 将请求电子邮件重新发送到另一个电子邮件地址 |
> | 行动 | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | 检索颁发的应用服务证书的站点封印 |
> | 行动 | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | 检索证书操作的列表 |
> | 行动 | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | 检索证书电子邮件历史记录 |
> | 行动 | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | 验证域所有权 |
> | 行动 | Microsoft.CertificateRegistration/certificateOrders/Write | 添加新的或更新现有的 certificateOrder |
> | 行动 | Microsoft.CertificateRegistration/operations/Read | 列出应用服务证书注册的所有操作 |
> | 行动 | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | 为服务应用主体预配服务主体 |
> | 行动 | Microsoft.CertificateRegistration/register/action | 注册订阅的 Microsoft 证书资源提供程序 |
> | 行动 | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | 验证证书购买对象但不提交该对象 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.ClassicCompute/capabilities/read | 显示功能 |
> | 行动 | Microsoft.ClassicCompute/checkDomainNameAvailability/action | 检查给定域名的可用性。 |
> | 行动 | Microsoft.ClassicCompute/checkDomainNameAvailability/read | 获取一个给定域名的可用性。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/active/write | 设置活动域名。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/availabilitySets/read | 显示资源的可用性集。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/capabilities/read | 显示域名功能 |
> | 行动 | Microsoft.ClassicCompute/domainNames/delete | 删除资源的域名。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/deploymentslots/read | 显示部署槽。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | 获取域名部署槽位中的角色 |
> | 行动 | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | 获取域名部署槽位中角色的角色实例 |
> | 行动 | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | 获取部署槽位状态。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | 添加部署槽位状态。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | 获取域名中部署槽位的升级域 |
> | 行动 | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | 更新域名中部署槽位的升级域 |
> | 行动 | Microsoft.ClassicCompute/domainNames/deploymentslots/write | 创建或更新部署。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/extensions/delete | 删除域名扩展。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | 读取域名扩展的操作状态。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/extensions/read | 返回域名扩展。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/extensions/write | 添加域名扩展。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | 删除新的内部负载均衡。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | 读取域名内部负载均衡器的操作状态。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | 获取内部负载均衡器。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | 创建新的内部负载均衡。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | 读取域名负载均衡的终结点集的操作状态。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | 获取负载均衡的终结点集。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | 添加负载均衡的终结点集。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/operationstatuses/read | 获取域名的操作状态。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/operationStatuses/read | 读取域名扩展的操作状态。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/read | 返回资源的域名。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | 删除使用的服务证书。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | 读取域名服务证书的操作状态。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | 返回使用的服务证书。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | 添加或修改使用的服务证书。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | 中止部署槽位的迁移。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | 提交部署槽位的迁移。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/delete | 删除给定的部署槽。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | 读取域名槽的操作状态。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | 准备部署槽位的迁移。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/read | 显示部署槽。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | 删除部署槽角色的扩展引用。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | 读取域名槽角色扩展引用的操作状态。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | 返回部署槽角色的扩展引用。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | 添加或修改部署槽角色的扩展引用。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | 获取域名的角色指标定义。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | 获取域名的角色指标。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | 获取域名槽位角色的操作状态。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | 获取诊断设置。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | 添加或修改诊断设置。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | 获取指标定义。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/roles/read | 获取部署槽的角色。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | 下载域名槽位角色上角色实例的远程桌面连接文件。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | 获取域名槽位角色上角色实例的操作状态。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | 获取角色实例。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | 重新生成角色实例。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | 重置角色实例的映像。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | 重新启动角色实例。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | 获取部署槽位的角色 SKU。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/roles/write | 添加部署槽位的角色。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/start/action | 启动部署槽。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/state/start/write | 将部署槽状态更改为已停止。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | 将部署槽状态更改为已启动。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/stop/action | 暂停部署槽。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | 逐步升级域。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | 验证部署槽位的迁移。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/slots/write | 创建或更新部署。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/swap/action | 将过渡槽交换到生产槽。 |
> | 行动 | Microsoft.ClassicCompute/domainNames/write | 添加或修改资源的域名。 |
> | 行动 | Microsoft.ClassicCompute/moveSubscriptionResources/action | 将所有经典资源移到不同的订阅。 |
> | 行动 | Microsoft.ClassicCompute/operatingSystemFamilies/read | 列出了 Microsoft Azure 中可用的来宾操作系统系列，还列出了每个系列可用的操作系统版本。 |
> | 行动 | Microsoft.ClassicCompute/operatingSystems/read | 列出 Microsoft Azure 中当前可用的来宾操作系统版本。 |
> | 行动 | Microsoft.ClassicCompute/operations/read | 获取操作列表。 |
> | 行动 | Microsoft.ClassicCompute/operationStatuses/read | 读取资源的操作状态。 |
> | 行动 | Microsoft.ClassicCompute/quotas/read | 获取订阅的配额。 |
> | 行动 | Microsoft.ClassicCompute/register/action | 注册到经典计算 |
> | 行动 | Microsoft.ClassicCompute/resourceTypes/skus/read | 获取受支持资源类型的 SKU 列表。 |
> | 行动 | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | 验证订阅是否可用于经典移动操作。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | 删除与虚拟机关联的网络安全组。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | 读取与网络安全组关联的虚拟机的操作状态。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | 获取与虚拟机关联的网络安全组。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | 添加与虚拟机关联的网络安全组。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | 获取可能的异步操作 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | 将数据磁盘附加到虚拟机。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/capture/action | 捕获虚拟机。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/delete | 删除虚拟机。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | 从虚拟机中分离数据磁盘。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | 获取虚拟机诊断设置。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/disks/read | 检索数据磁盘的列表 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | 下载虚拟机的 RDP 文件。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | 读取虚拟机扩展的操作状态。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/extensions/read | 获取虚拟机扩展。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/extensions/write | 放置虚拟机扩展。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | 获取虚拟机指标定义。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/metrics/read | 获取指标。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | 删除与网络接口关联的网络安全组。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | 读取与网络安全组关联的虚拟机的操作状态。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | 获取与网络接口关联的网络安全组。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | 添加与网络接口关联的网络安全组。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | 读取虚拟机的操作状态。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | 在虚拟机上执行维护。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | 获取诊断设置。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | 添加或修改诊断设置。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | 获取指标定义。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/read | 检索虚拟机列表。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/redeploy/action | 重新部署虚拟机。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/restart/action | 重新启动虚拟机。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/shutdown/action | 关闭虚拟机。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/start/action | 启动虚拟机。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/stop/action | 停止虚拟机。 |
> | 行动 | Microsoft.ClassicCompute/virtualMachines/write | 添加或修改虚拟机。 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | 获取快速路由交叉连接操作状态。 |
> | 行动 | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | 删除快速路由交叉连接对等互连。 |
> | 行动 | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | 获取快速路由交叉连接对等互连操作状态。 |
> | 行动 | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | 获取快速路由交叉连接对等互连。 |
> | 行动 | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | 添加快速路由交叉连接对等互连。 |
> | 行动 | Microsoft.ClassicNetwork/expressroutecrossconnections/read | 获取快速路由交叉连接。 |
> | 行动 | Microsoft.ClassicNetwork/expressroutecrossconnections/write | 添加快速路由交叉连接。 |
> | 行动 | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | 检索受支持设备的列表。 |
> | 行动 | Microsoft.ClassicNetwork/networkSecurityGroups/delete | 删除网络安全组。 |
> | 行动 | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | 读取网络安全组的操作状态。 |
> | 行动 | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | 获取网络安全组诊断设置 |
> | 行动 | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新网络安全组诊断设置，此操作由见解资源提供程序进行补充。 |
> | 行动 | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | 获取网络安全组的事件 |
> | 行动 | Microsoft.ClassicNetwork/networkSecurityGroups/read | 获取网络安全组。 |
> | 行动 | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | 删除安全规则。 |
> | 行动 | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | 读取网络安全组安全规则的操作状态。 |
> | 行动 | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | 获取安全规则。 |
> | 行动 | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | 添加或更新安全规则。 |
> | 行动 | Microsoft.ClassicNetwork/networkSecurityGroups/write | 添加新的网络安全组。 |
> | 行动 | Microsoft.ClassicNetwork/operations/read | 获取经典网络操作。 |
> | 行动 | Microsoft.ClassicNetwork/quotas/read | 获取订阅的配额。 |
> | 行动 | Microsoft.ClassicNetwork/register/action | 注册到经典网络 |
> | 行动 | Microsoft.ClassicNetwork/reservedIps/delete | 删除保留 IP。 |
> | 行动 | Microsoft.ClassicNetwork/reservedIps/join/action | 联接保留 IP |
> | 行动 | Microsoft.ClassicNetwork/reservedIps/link/action | 链接保留 IP |
> | 行动 | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | 读取保留 IP 的操作状态。 |
> | 行动 | Microsoft.ClassicNetwork/reservedIps/read | 获取保留 IP |
> | 行动 | Microsoft.ClassicNetwork/reservedIps/write | 添加新的保留 IP |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | 中止虚拟网络的迁移 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | 显示功能 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | 检查虚拟网络中给定 IP 地址的可用性。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | 提交虚拟网络的迁移 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/delete | 删除虚拟网络。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | 取消吊销客户端证书。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | 读取已吊销的客户端证书。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | 吊销客户端证书。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | 删除虚拟网络网关客户端证书。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | 按指纹下载证书。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | 列出虚拟网络网关证书包。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | 查找客户端根证书。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | 上传新的客户端根证书。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | 建立站点到站点网关连接。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | 断开站点到站点网关连接。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | 检索连接列表。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | 测试站点到站点网关连接。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | 删除虚拟网络网关。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | 下载设备配置脚本。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | 下载网关诊断。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | 检索线路服务密钥。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | 列出虚拟网络网关包。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | 读取虚拟网络网关的操作状态。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | 获取虚拟网络网关包。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | 获取虚拟网络网关。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | 启动虚拟网关的诊断。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | 停止虚拟网关的诊断。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | 添加虚拟网络网关。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/join/action | 加入虚拟网络。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | 读取虚拟网络的操作状态。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/peer/action | 在两个不同的虚拟网络之间建立对等互连。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | 准备虚拟网络的迁移 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/read | 获取虚拟网络。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | 删除远程虚拟网络对等互连代理。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | 获取远程虚拟网络对等互连代理。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | 添加或更新远程虚拟网络对等互连代理。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | 删除与子网关联的网络安全组。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | 读取与网络安全组关联的虚拟网络子网的操作状态。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | 获取与子网关联的网络安全组。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | 添加与子网关联的网络安全组。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/validateMigration/action | 验证虚拟网络的迁移 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | 获取虚拟网络对等互连。 |
> | 行动 | Microsoft.ClassicNetwork/virtualNetworks/write | 添加新的虚拟网络。 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.ClassicStorage/capabilities/read | 显示功能 |
> | 行动 | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | 检查存储帐户的可用性。 |
> | 行动 | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | 获取存储帐户的可用性。 |
> | 行动 | Microsoft.ClassicStorage/disks/read | 返回存储帐户磁盘。 |
> | 行动 | Microsoft.ClassicStorage/images/operationstatuses/read | 获取映像操作状态。 |
> | 行动 | Microsoft.ClassicStorage/images/read | 返回映像。 |
> | 行动 | Microsoft.ClassicStorage/operations/read | 获取经典存储操作 |
> | 行动 | Microsoft.ClassicStorage/osImages/read | 返回操作系统映像。 |
> | 行动 | Microsoft.ClassicStorage/osPlatformImages/read | 获取操作系统平台映像。 |
> | 行动 | Microsoft.ClassicStorage/publicImages/read | 获取公共虚拟机映像。 |
> | 行动 | Microsoft.ClassicStorage/quotas/read | 获取订阅的配额。 |
> | 行动 | Microsoft.ClassicStorage/register/action | 注册到经典存储 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | 中止存储帐户的迁移。 |
> | 行动 | ClassicStorage/storageAccounts/blobServices/providers//diagnosticSettings/read | 获取诊断设置。 |
> | 行动 | ClassicStorage/storageAccounts/blobServices/providers//diagnosticSettings/write | 添加或修改诊断设置。 |
> | 行动 | ClassicStorage/storageAccounts/blobServices/providers//metricDefinitions/read | 获取指标定义。 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | 提交存储帐户的迁移。 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/delete | 删除存储帐户。 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/disks/delete | 删除给定的存储帐户磁盘。 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | 读取资源的操作状态。 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/disks/read | 返回存储帐户磁盘。 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/disks/write | 添加存储帐户磁盘。 |
> | 行动 | ClassicStorage/storageAccounts/fileServices/providers//diagnosticSettings/read | 获取诊断设置。 |
> | 行动 | ClassicStorage/storageAccounts/fileServices/providers//diagnosticSettings/write | 添加或修改诊断设置。 |
> | 行动 | ClassicStorage/storageAccounts/fileServices/providers//metricDefinitions/read | 获取指标定义。 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/images/delete | 删除给定的存储帐户映像。 （已弃用。 请使用“Microsoft.ClassicStorage/storageAccounts/vmImages”） |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | 返回存储帐户映像操作状态。 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/images/read | 返回存储帐户映像。 （已弃用。 请使用“Microsoft.ClassicStorage/storageAccounts/vmImages”） |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/listKeys/action | 列出存储帐户的访问密钥。 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | 读取资源的操作状态。 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/osImages/delete | 删除给定的存储帐户操作系统映像。 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/osImages/read | 返回存储帐户操作系统映像。 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/osImages/write | 添加给定的存储帐户操作系统映像。 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | 准备存储帐户的迁移。 |
> | 行动 | ClassicStorage/storageAccounts/providers/diagnosticSettings/read | 获取诊断设置。 |
> | 行动 | ClassicStorage/storageAccounts/providers/diagnosticSettings/write | 添加或修改诊断设置。 |
> | 行动 | ClassicStorage/storageAccounts/providers/metricDefinitions/read | 获取指标定义。 |
> | 行动 | ClassicStorage/storageAccounts/queueServices/providers//diagnosticSettings/read | 获取诊断设置。 |
> | 行动 | ClassicStorage/storageAccounts/queueServices/providers//diagnosticSettings/write | 添加或修改诊断设置。 |
> | 行动 | ClassicStorage/storageAccounts/queueServices/providers//metricDefinitions/read | 获取指标定义。 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/read | 返回包含给定帐户的存储帐户。 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | 再生成存储帐户的现有访问密钥。 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | 获取诊断设置。 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | 添加或修改诊断设置。 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | 获取指标定义。 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | 获取指标。 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/services/read | 获取可用服务。 |
> | 行动 | ClassicStorage/storageAccounts/tableServices/providers//diagnosticSettings/read | 获取诊断设置。 |
> | 行动 | ClassicStorage/storageAccounts/tableServices/providers//diagnosticSettings/write | 添加或修改诊断设置。 |
> | 行动 | ClassicStorage/storageAccounts/tableServices/providers//metricDefinitions/read | 获取指标定义。 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | 验证存储帐户的迁移。 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | 删除给定的虚拟机映像。 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | 获取给定的虚拟机映像操作状态。 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/vmImages/read | 返回虚拟机映像。 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/vmImages/write | 添加给定的虚拟机映像。 |
> | 行动 | Microsoft.ClassicStorage/storageAccounts/write | 添加新的存储帐户。 |
> | 行动 | Microsoft.ClassicStorage/vmImages/read | 列出虚拟机映像。 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | DataAction | Cognitiveservices account/accounts/自动建议/搜索/操作 | 此操作提供给定查询或部分查询的建议。 |
> | DataAction | Cognitiveservices account/accounts/ComputerVision/分析/操作 | 此操作基于图像内容提取一组丰富的视觉功能。  |
> | DataAction | Cognitiveservices account/accounts/ComputerVision/areaofinterest/action | 此操作将返回图像最重要区域周围的边界框。 |
> | DataAction | Cognitiveservices account/accounts/ComputerVision/说明/操作 | 此操作使用完整的句子以用户可读语言生成图像说明。<br> 描述基于内容标记的集合，该集合也由操作返回。<br>可以为每个映像生成多个说明。<br> 描述按其置信度分数排序。<br>所有说明均为英语。 |
> | DataAction | Cognitiveservices account/accounts/ComputerVision/检测/操作 | 此操作对指定的映像执行对象检测。  |
> | DataAction | Cognitiveservices account/accounts/ComputerVision/generatethumbnail/action | 此操作将生成具有用户指定的宽度和高度的缩略图。<br> 默认情况下，该服务会分析图像、标识感兴趣的区域（ROI），并根据 ROI 生成智能裁剪坐标。<br> 当指定的纵横比不同于输入图像的纵横比时，智能裁剪有助于 |
> | DataAction | Cognitiveservices account/accounts/ComputerVision/模型/分析/操作 | 此操作通过应用特定于域的模型来识别图像内的内容。<br> 计算机视觉 API 支持的特定于域的模型的列表可以使用/models GET 请求进行检索。<br> 目前，API 提供下列特定于域的模型：名人、特征点。 |
> | DataAction | Cognitiveservices account/accounts/ComputerVision/模型/读取 | 此操作返回计算机视觉 API 支持的特定于域的模型的列表。  目前，API 支持以下特定于域的模型：名人识别器、路标识别器。 |
> | DataAction | Cognitiveservices account/accounts/ComputerVision/ocr/动作 | 光学字符识别（OCR）检测图像中的文本，并将识别到的字符提取到机器可用的字符流中。    |
> | DataAction | Cognitiveservices account/accounts/ComputerVision/recognizetext/action | 使用此接口可获取识别文本操作的结果。 如果使用识别文本接口，则响应将包含一个名为 "操作-位置" 的字段。 "操作位置" 字段包含您必须用于 Get 识别文本操作结果操作的 URL。 |
> | DataAction | Cognitiveservices account/accounts/ComputerVision/标记/操作 | 此操作生成与提供的图像内容相关的单词或标记的列表。<br>计算机视觉 API 可以基于对象、生活、风景或图像中找到的操作返回标记。<br>与类别不同，标记不会按照分层分类系统进行组织，而是对应于图像内容。<br>标记可能包含用于避免多义性或提供上下文的提示，例如标记 "cello" 可能伴随提示 "乐器"。<br>所有标记均为英语。 |
> | DataAction | Cognitiveservices account/accounts/ComputerVision/textoperations/read | 此接口用于获取识别文本操作结果。 应从识别文本接口返回的<b>"操作位置"</b>字段中检索此接口的 URL。 |
> | 行动 | Microsoft.CognitiveServices/accounts/delete | 删除 API 帐户 |
> | DataAction | Cognitiveservices account/accounts/EntitySearch/搜索/操作 | 获取给定查询的实体和位置结果。 |
> | DataAction | Cognitiveservices account/accounts/面部/检测/操作 | 检测图像中的人脸、返回人脸矩形，还可以选择使用 faceIds、特征点和特性。 |
> | DataAction | Cognitiveservices account/accounts/面部/facelists/delete | 删除指定的人脸列表。 还将删除人脸列表中相关的人脸图像。 |
> | DataAction | Cognitiveservices account/accounts/面部/facelists/persistedfaces/delete | 通过指定的 faceListId 和 persisitedFaceId 删除人脸列表中的人脸。 还将删除相关的人脸图像。 |
> | DataAction | Cognitiveservices account/accounts/面部/facelists/persistedfaces/write | 将人脸添加到指定的人脸列表，最多1000面。 |
> | DataAction | Cognitiveservices account/accounts/面部/facelists/read | 在人脸列表中检索人脸列表的 faceListId、姓名、userData 和面部。 列表人脸列表的名称和 faceListId。 |
> | DataAction | Cognitiveservices account/accounts/面部/facelists/write | 使用用户指定的 faceListId、名称和可选的 userData 创建空的人脸列表。 最多支持64人脸列表，其中包含名称和 userData 的更新信息。 |
> | DataAction | Cognitiveservices account/accounts/面部/findsimilars/action | 给定的查询人脸的 faceId，从 faceId 阵列、人脸列表或大脸列表中搜索看起来类似的人脸。 faceId |
> | DataAction | Cognitiveservices account/accounts/面部/组/操作 | 根据面部相似性将候选面划分为若干组。 |
> | DataAction | Cognitiveservices account/accounts/面部/识别/操作 | 一对多标识，查找来自人员组或大型人员组的特定查询人脸最近的匹配项。 |
> | DataAction | Cognitiveservices account/accounts/面部/largefacelists/delete | 删除指定的大图标列表。 将删除大人脸列表中相关的人脸图像。 |
> | DataAction | Cognitiveservices account/accounts/面部/largefacelists/persistedfaces/delete | 通过指定的 largeFaceListId 和 persisitedFaceId 删除大人脸列表中的人脸。 还将删除相关的人脸图像。 |
> | DataAction | Cognitiveservices account/accounts/面部/largefacelists/persistedfaces/read | 使用 largeFaceListId 和 persistedFaceId 检索大人脸列表中的持久面。 在指定的大图标列表中列出人脸的 persistedFaceId 和 userData。 |
> | DataAction | Cognitiveservices account/accounts/面部/largefacelists/persistedfaces/write | 将人脸添加到指定的大表面列表，最多1000000面。 通过 persistedFaceId 在大人脸列表中更新指定的人脸字段。 |
> | DataAction | Cognitiveservices account/accounts/面部/largefacelists/read | 检索大人脸列表的 largeFaceListId，name，userData。 列出 largeFaceListId、名称和 userData 的大脸列表信息。 |
> | DataAction | Cognitiveservices account/accounts/面部/largefacelists/定型/操作 | 提交大的人脸列表培训任务。 训练是只有经过训练的大型人脸列表才能使用的关键步骤。 |
> | DataAction | Cognitiveservices account/accounts/面部/largefacelists/培训/读 | 若要查看大脸列表培训状态是否已完成或仍在进行。 LargeFaceList 定型是一种异步操作 |
> | DataAction | Cognitiveservices account/accounts/面部/largefacelists/write | 使用用户指定的 largeFaceListId、名称和可选的 userData 创建一个空的大脸列表。 更新大图标列表的信息，包括名称和 userData。 |
> | DataAction | Cognitiveservices account/accounts/面部/largepersongroups/delete | 删除具有指定 personGroupId 的现有大人员组。 此大人员组中的持久化数据将被删除。 |
> | DataAction | Cognitiveservices account/accounts/面部/largepersongroups/人员/操作 | 在指定的大型人员组中创建新人员。 若要为此人添加人脸，请调用 |
> | DataAction | Cognitiveservices account/accounts/面部/largepersongroups/人员/删除 | 从大人员组中删除现有用户。 将删除 person 条目中所有存储的人员数据和人脸图像。 |
> | DataAction | Cognitiveservices account/accounts/面部/largepersongroups/人员/persistedfaces/delete | 删除大型人员组中的人脸。 与此面部输入相关的面部数据和图像也将被删除。 |
> | DataAction | Cognitiveservices account/accounts/面部/largepersongroups/人员/persistedfaces/read | 检索人员脸信息。 持久化人员脸由其 largePersonGroupId、personId 和 persistedFaceId 来指定。 |
> | DataAction | Cognitiveservices account/accounts/面部/largepersongroups/人员/persistedfaces/write | 将人脸图像添加到大个人组，以进行人脸标识或验证。 若要处理用于更新人员的人脸的用户的图像，请使用此字段。 |
> | DataAction | Cognitiveservices account/accounts/面部/largepersongroups/人员/阅读 | 检索人员的姓名和用户，并使用 faceIds 来表示注册的人脸图像。 列出指定大人员组中的所有人员信息，包括 personId、name、userData 和 persistedFaceIds。 |
> | DataAction | Cognitiveservices account/accounts/面部/largepersongroups/人员/写入 | 更新人员的名称或用户名称。 |
> | DataAction | Cognitiveservices account/accounts/面部/largepersongroups/read | 检索大人员组的信息，包括其名称和用户信息。 此 API 将返回大人员组信息列表，其中列出了所有现有的大型人员组的 largePesonGroupId、名称和 userData。 |
> | DataAction | Cognitiveservices account/accounts/面部/largepersongroups/定型/操作 | 提交大人员组定型任务。 训练是只有经过训练的大型人员组才能使用的关键步骤。 |
> | DataAction | Cognitiveservices account/accounts/面部/largepersongroups/培训/读 | 检查大型人员组定型状态已完成或仍在进行。 LargePersonGroup 定型是一种异步操作 |
> | DataAction | Cognitiveservices account/accounts/面部/largepersongroups/write | 使用用户指定的 largePersonGroupId、名称和可选的 userData 创建新的大型 person 组。 更新现有大人员组的名称和 userData。 如果属性不在请求正文中，属性将保持不变。 |
> | DataAction | Cognitiveservices account/accounts/面部/persongroup 现/delete | 删除具有指定 personGroupId 的现有人员组。 此人员组中的持久化数据将被删除。 |
> | DataAction | Cognitiveservices account/accounts/面部/persongroup 现/人员/操作 | 在指定的用户组中创建新人员。 若要为此人添加人脸，请调用 |
> | DataAction | Cognitiveservices account/accounts/面部/persongroup 现/人员/删除 | 从用户组中删除现有用户。 将删除 person 条目中所有存储的人员数据和人脸图像。 |
> | DataAction | Cognitiveservices account/accounts/面部/persongroup 现/人员/persistedfaces/delete | 删除人员组中的人脸。 与此面部输入相关的面部数据和图像也将被删除。 |
> | DataAction | Cognitiveservices account/accounts/面部/persongroup 现/人员/persistedfaces/read | 检索人员脸信息。 持久化人员脸由其 personGroupId、personId 和 persistedFaceId 来指定。 |
> | DataAction | Cognitiveservices account/accounts/面部/persongroup 现/人员/persistedfaces/write | 将人脸图像添加到人员组，以进行人脸标识或验证。 若要处理多个更新的映像，请使用人脸的 userData 字段。 |
> | DataAction | Cognitiveservices account/accounts/面部/persongroup 现/人员/阅读 | 检索人员的姓名和用户，并使用 faceIds 来表示注册的人脸图像。 列出指定人员组中的所有人员信息，包括已注册的 personId、name、userData 和 persistedFaceIds。 |
> | DataAction | Cognitiveservices account/accounts/面部/persongroup 现/人员/写入 | 更新人员的名称或用户名称。 |
> | DataAction | Cognitiveservices account/accounts/面部/persongroup 现/read | 检索人员组名称和 userData。 若要在此 Person group 下获取人员信息，请使用列表人员组的 pesonGroupId、name 和 userData。 |
> | DataAction | Cognitiveservices account/accounts/面部/persongroup 现/定型/操作 | 提交人员组定型任务。 训练是只有训练有素的人员组才能使用的关键步骤。 |
> | DataAction | Cognitiveservices account/accounts/面部/persongroup 现/培训/读 | 检查人员组定型状态已完成或仍在进行。 Person group 定型是触发的异步操作 |
> | DataAction | Cognitiveservices account/accounts/面部/persongroup 现/write | 使用指定的 personGroupId、名称和用户提供的 userData 创建新的 person 组。 更新现有人员组的名称和 userData。 如果属性不在请求正文中，属性将保持不变。 |
> | DataAction | Cognitiveservices account/帐户/人脸/验证/操作 | 验证两人脸是否属于同一个人，或是否属于某个人。 |
> | DataAction | Cognitiveservices account/accounts/ImageSearch/详细信息/操作 | 返回有关图像（如包含图像的网页）的见解。 |
> | DataAction | Cognitiveservices account/accounts/ImageSearch/搜索/操作 | 获取给定查询的相关图像。 |
> | DataAction | Cognitiveservices account/accounts/ImageSearch/趋势分析/操作 | 获取当前趋势图像。 |
> | DataAction | Cognitiveservices account/accounts/ImmersiveReader/getcontentmodelforreader/action | 创建沉浸式读卡器会话 |
> | DataAction | Cognitiveservices account/accounts/InkRecognizer/识别/操作 | 给定一组笔划数据会分析内容并生成可识别的实体列表，其中包括识别的文本。 |
> | 行动 | Microsoft.CognitiveServices/accounts/listKeys/action | 列出密钥 |
> | DataAction | Cognitiveservices account/accounts/LUIS/预测/操作 | 获取给定查询的已发布终结点预测。 |
> | DataAction | Cognitiveservices account/accounts/NewsSearch/categorysearch/action | 返回所提供类别的新闻。 |
> | DataAction | Cognitiveservices account/accounts/NewsSearch/搜索/操作 | 获取与给定查询相关的新闻文章。 |
> | DataAction | Cognitiveservices account/accounts/NewsSearch/trendingtopics/action | 获取 Bing 标识的趋势主题。 这些主题与必应主页底部的横幅中显示的主题相同。 |
> | 行动 | Microsoft.CognitiveServices/accounts/read | 读取 API 帐户。 |
> | 行动 | Microsoft.CognitiveServices/accounts/regenerateKey/action | 再生成密钥 |
> | 行动 | Microsoft.CognitiveServices/accounts/skus/read | 读取现有资源的可用 SKU。 |
> | DataAction | Cognitiveservices account/accounts/拼写检查/拼写检查/action | 通过 GET 或 POST 获取拼写检查查询的结果。 |
> | DataAction | Cognitiveservices account/accounts/TextAnalytics/实体/操作 | API 将返回给定文档中已知实体和常规命名实体（\"人员\"、\"位置\"、\"组织\" 等）的列表。 |
> | DataAction | Cognitiveservices account/accounts/TextAnalytics/keyphrases/action | API 会返回一个字符串列表，指示输入文本中的关键要点。 |
> | DataAction | Cognitiveservices account/accounts/TextAnalytics/语言/操作 | API 会返回检测的语言和数字评分（介于 0 到 1 之间）。 评分接近 1 表示 100% 确定识别的语言是正确的。 共支持 120 种语言。 |
> | DataAction | Cognitiveservices account/accounts/TextAnalytics/情绪/action | API 会返回介于 0 到 1 之间的数字评分。<br>接近 1 的分数表示积极情绪，而接近 0 的分数表示消极情绪。<br>分数为0.5，表示缺少情绪（例如<br>一个仿真陈述。 |
> | 行动 | Microsoft.CognitiveServices/accounts/usages/read | 获取现有资源的配额用量。 |
> | DataAction | Cognitiveservices account/accounts/VideoSearch/详细信息/操作 | 获取有关视频的见解，如相关视频。 |
> | DataAction | Cognitiveservices account/accounts/VideoSearch/搜索/操作 | 获取给定查询的相关视频。 |
> | DataAction | Cognitiveservices account/accounts/VideoSearch/趋势分析/操作 | 获取当前趋势视频。 |
> | DataAction | Cognitiveservices account/accounts/VisualSearch/搜索/操作 | 返回与提供的图像相关的标记列表 |
> | DataAction | Cognitiveservices account/accounts/WebSearch/搜索/操作 | 获取给定查询的 web、图像、新闻、& 视频结果。 |
> | 行动 | Microsoft.CognitiveServices/accounts/write | 写入 API 帐户。 |
> | 行动 | Cognitiveservices account/checkDomainAvailability/action | 读取订阅的可用 Sku。 |
> | 行动 | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | 读取订阅的可用 Sku。 |
> | 行动 | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | 读取订阅的可用 Sku。 |
> | 行动 | Cognitiveservices account/位置/deleteVirtualNetworkOrSubnets/action | 删除 VirtualNetworks 或子网的 Microsoft 通知。 |
> | 行动 | Microsoft.CognitiveServices/Operations/read | 读取所有可用操作 |
> | 行动 | Microsoft.CognitiveServices/register/action | 注册认知服务的订阅 |
> | 行动 | Microsoft.CognitiveServices/register/action | 注册认知服务的订阅 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Commerce/RateCard/read | 返回给定订阅的套餐数据、资源/计量元数据和费率。 |
> | 行动 | Microsoft.Commerce/UsageAggregates/read | 检索订阅的 Microsoft Azure 消耗量。 结果包含特定时间范围内的聚合用量数据、订阅和资源相关信息。 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Compute/availabilitySets/delete | 删除可用性集 |
> | 行动 | Microsoft.Compute/availabilitySets/read | 获取可用性集的属性 |
> | 行动 | Microsoft.Compute/availabilitySets/vmSizes/read | 列出可在可用性集中创建或更新的虚拟机大小 |
> | 行动 | Microsoft.Compute/availabilitySets/write | 创建新的可用性集，或更新现有的可用性集 |
> | 行动 | DiskEncryptionSets/删除 | 删除磁盘加密集 |
> | 行动 | DiskEncryptionSets/read | 获取磁盘加密集的属性 |
> | 行动 | DiskEncryptionSets/write | 创建新的磁盘加密集或更新现有的磁盘加密集 |
> | 行动 | Microsoft.Compute/disks/beginGetAccess/action | 获取用于 Blob 访问的磁盘 SAS URI |
> | 行动 | Microsoft.Compute/disks/delete | 删除磁盘 |
> | 行动 | Microsoft.Compute/disks/endGetAccess/action | 吊销磁盘的 SAS URI |
> | 行动 | Microsoft.Compute/disks/read | 获取磁盘的属性 |
> | 行动 | Microsoft.Compute/disks/write | 创建新的磁盘，或更新现有的磁盘 |
> | 行动 | Microsoft.Compute/galleries/delete | 删除库 |
> | 行动 | Microsoft.Compute/galleries/images/delete | 删除库映像 |
> | 行动 | Microsoft.Compute/galleries/images/read | 获取库映像的属性 |
> | 行动 | Microsoft.Compute/galleries/images/versions/delete | 删除库映像版本 |
> | 行动 | Microsoft.Compute/galleries/images/versions/read | 获取库映像版本的属性 |
> | 行动 | Microsoft.Compute/galleries/images/versions/write | 创建新的库映像版本或更新现有版本 |
> | 行动 | Microsoft.Compute/galleries/images/write | 创建新的库映像或更新现有库映像 |
> | 行动 | Microsoft.Compute/galleries/read | 获取库的属性 |
> | 行动 | Microsoft.Compute/galleries/write | 创建新的库或更新现有库 |
> | 行动 | HostGroups/删除 | 删除主机组 |
> | 行动 | HostGroups/主机/删除 | 删除主机 |
> | 行动 | HostGroups/主机/读取 | 获取主机的属性 |
> | 行动 | HostGroups/主机/写入 | 创建新主机或更新现有主机 |
> | 行动 | HostGroups/read | 获取主机组的属性 |
> | 行动 | HostGroups/write | 创建新的主机组或更新现有主机组 |
> | 行动 | Microsoft.Compute/images/delete | 删除映像 |
> | 行动 | Microsoft.Compute/images/read | 获取映像的属性 |
> | 行动 | Microsoft.Compute/images/write | 创建新的映像，或更新现有的映像 |
> | 行动 | Microsoft.Compute/locations/capsOperations/read | 获取异步大写操作的状态 |
> | 行动 | Microsoft.Compute/locations/diskOperations/read | 获取异步磁盘操作的状态 |
> | 行动 | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | 创建日志来按时间间隔显示请求总数，以帮助限制诊断。 |
> | 行动 | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | 创建日志以显示按 ResourceName、OperationName 或应用的限制策略分组的限制请求数聚合。 |
> | 行动 | Microsoft.Compute/locations/operations/read | 获取异步操作的状态 |
> | 行动 | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | 获取平台映像产品/服务的属性 |
> | 行动 | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | 获取平台映像 SKU 的属性 |
> | 行动 | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | 获取平台映像版本的属性 |
> | 行动 | Microsoft.Compute/locations/publishers/artifacttypes/types/read | 获取 VMExtension 类型的属性 |
> | 行动 | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | 获取 VMExtension 版本的属性 |
> | 行动 | Microsoft.Compute/locations/publishers/read | 获取发布服务器的属性 |
> | 行动 | Microsoft.Compute/locations/runCommands/read | 列出位置中的可用运行命令 |
> | 行动 | Microsoft.Compute/locations/usages/read | 获取某个位置中订阅的计算资源的服务限制和当前用量 |
> | 行动 | Microsoft.Compute/locations/vmSizes/read | 列出某个位置的可用虚拟机大小 |
> | 行动 | Microsoft.Compute/operations/read | 列出可对 Microsoft.Compute 资源提供程序使用的操作 |
> | 行动 | ProximityPlacementGroups/删除 | 删除邻近位置组 |
> | 行动 | ProximityPlacementGroups/read | 获取邻近位置组的属性 |
> | 行动 | ProximityPlacementGroups/write | 创建新的邻近位置组或更新现有的邻近位置组 |
> | 行动 | Microsoft.Compute/register/action | 将订阅注册到 Microsoft.Compute 资源提供程序 |
> | 行动 | Microsoft.Compute/restorePointCollections/delete | 删除恢复点集合以及包含的还原点 |
> | 行动 | Microsoft.Compute/restorePointCollections/read | 获取还原点集合的属性 |
> | 行动 | Microsoft.Compute/restorePointCollections/restorePoints/delete | 删除还原点 |
> | 行动 | Microsoft.Compute/restorePointCollections/restorePoints/read | 获取还原点的属性 |
> | 行动 | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | 获取还原点的属性以及 Blob SAS URI |
> | 行动 | Microsoft.Compute/restorePointCollections/restorePoints/write | 创建新的还原点 |
> | 行动 | Microsoft.Compute/restorePointCollections/write | 创建新的或更新现有的还原点集合 |
> | 行动 | Microsoft.Compute/sharedVMImages/delete | 删除 SharedVMImage |
> | 行动 | Microsoft.Compute/sharedVMImages/read | 获取 SharedVMImage 的属性 |
> | 行动 | Microsoft.Compute/sharedVMImages/versions/delete | 删除 SharedVMImageVersion |
> | 行动 | Microsoft.Compute/sharedVMImages/versions/read | 获取 SharedVMImageVersion 的属性 |
> | 行动 | Microsoft.Compute/sharedVMImages/versions/replicate/action | 将 SharedVMImageVersion 复制到目标区域 |
> | 行动 | Microsoft.Compute/sharedVMImages/versions/write | 创建新的 SharedVMImageVersion 或更新现有的 SharedVMImageVersion |
> | 行动 | Microsoft.Compute/sharedVMImages/write | 创建新的 SharedVMImage 或更新现有的 SharedVMImage |
> | 行动 | Microsoft.Compute/skus/read | 获取订阅可用的 Microsoft.Compute SKU 列表 |
> | 行动 | Microsoft.Compute/snapshots/beginGetAccess/action | 获取用于 blob 访问的快照 SAS URI |
> | 行动 | Microsoft.Compute/snapshots/delete | 删除快照 |
> | 行动 | Microsoft.Compute/snapshots/endGetAccess/action | 撤销快照的 SAS URI |
> | 行动 | Microsoft.Compute/snapshots/read | 获取快照的属性 |
> | 行动 | Microsoft.Compute/snapshots/write | 创建新的快照，或更新现有的快照 |
> | 行动 | Microsoft. 计算/注销/操作 | 取消注册与 Microsoft. 计算资源提供程序的订阅 |
> | 行动 | Microsoft.Compute/virtualMachines/capture/action | 通过复制虚拟硬盘捕获虚拟机，并生成可用于创建类似虚拟机的模板 |
> | 行动 | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | 将虚拟机的基于 Blob 的磁盘转换为托管磁盘 |
> | 行动 | Microsoft.Compute/virtualMachines/deallocate/action | 关闭虚拟机并释放计算资源 |
> | 行动 | Microsoft.Compute/virtualMachines/delete | 删除虚拟机 |
> | 行动 | Microsoft.Compute/virtualMachines/extensions/delete | 删除虚拟机扩展 |
> | 行动 | Microsoft.Compute/virtualMachines/extensions/read | 获取虚拟机扩展的属性 |
> | 行动 | Microsoft.Compute/virtualMachines/extensions/write | 创建新的或更新现有的虚拟机扩展 |
> | 行动 | Microsoft.Compute/virtualMachines/generalize/action | 将虚拟机状态设置为通用化，并准备要捕获的虚拟机 |
> | 行动 | Microsoft.Compute/virtualMachines/instanceView/read | 获取虚拟机的详细运行时状态及其资源 |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | 以普通用户身份登录虚拟机 |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | 以 Windows 管理员身份或 Linux 根用户权限登录虚拟机 |
> | 行动 | Microsoft.Compute/virtualMachines/performMaintenance/action | 在 VM 上执行维护操作。 |
> | 行动 | Microsoft.Compute/virtualMachines/powerOff/action | 关闭虚拟机。 请注意，该虚拟机会继续产生费用。 |
> | 行动 | Microsoft.Compute/virtualMachines/read | 获取虚拟机的属性 |
> | 行动 | Microsoft.Compute/virtualMachines/redeploy/action | 重新部署虚拟机 |
> | 行动 | Microsoft.Compute/virtualMachines/reimage/action | 对使用差异磁盘的虚拟机重置映像。 |
> | 行动 | Microsoft.Compute/virtualMachines/restart/action | 重新启动虚拟机 |
> | 行动 | Microsoft.Compute/virtualMachines/runCommand/action | 执行虚拟机上的预定义脚本 |
> | 行动 | Microsoft.Compute/virtualMachines/start/action | 启动虚拟机 |
> | 行动 | Microsoft.Compute/virtualMachines/vmSizes/read | 列出可将虚拟机更新到的大小 |
> | 行动 | Microsoft.Compute/virtualMachines/write | 创建新的虚拟机，或更新现有的虚拟机 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | 关闭并释放虚拟机规模集实例的计算资源  |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/delete | 删除虚拟机规模集 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/delete/action | 删除虚拟机规模集的实例 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | 删除虚拟机规模集扩展 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/extensions/read | 获取虚拟机规模集扩展的属性 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/extensions/write | 创建新的虚拟机规模集扩展或更新现有虚拟机规模集扩展 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | 手动浏览服务结构虚拟机规模集的平台更新域，完成卡住的挂起更新 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | 获取虚拟机规模集的实例视图 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | 手动将实例更新到虚拟机规模集的最新模型 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | 获取虚拟机规模集的所有网络接口的属性 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | 启动滚动升级，将所有虚拟机规模集实例移动到最新可用的平台映像操作系统版本。 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | 获取虚拟机规模集的 OS 升级历史记录 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | 在虚拟机规模集的实例上执行计划内维护 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | 关闭虚拟机规模集的实例 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | 获取虚拟机规模集的所有公用 IP 地址的属性 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/read | 获取虚拟机规模集的属性 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | 重新部署虚拟机规模集的实例 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/reimage/action | 对虚拟机规模集的实例进行映像重置 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | 重置虚拟机规模集实例的所有磁盘（OS 磁盘和数据磁盘）的映像  |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/restart/action | 重新启动虚拟机规模集的实例 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | 取消虚拟机规模集的滚动升级 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | 获取虚拟机规模集的最新滚动升级状态 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/scale/action | 验证现有虚拟机规模集是否可以缩小/扩大到指定的实例计数 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/skus/read | 列出现有虚拟机规模集的有效 SKU |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/start/action | 启动虚拟机规模集的实例 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | 关闭并释放 VM 规模集中虚拟机的计算资源。 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | 删除 VM 规模集中的特定虚拟机。 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | 检索 VM 规模集中虚拟机的实例视图。 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | 获取使用虚拟机规模集创建的公共 IP 地址的属性。 虚拟机规模集可为每个 ipconfiguration（专用 IP）最多创建一个公用 IP |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | 获取使用虚拟机规模集创建的网络接口的一个或所有 IP 配置的属性。 IP 配置表示专用 IP |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | 获取使用虚拟机规模集创建的虚拟机的一个或所有网络接口的属性 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | 在虚拟机规模集的虚拟机实例上执行计划内维护 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | 关闭 VM 规模集中的虚拟机实例。 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | 检索 VM 规模集中虚拟机的属性 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | 重新部署虚拟机规模集中的虚拟机实例 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | 对虚拟机规模集中的虚拟机实例进行映像重置。 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | 重置虚拟机规模集中虚拟机实例的所有磁盘（OS 磁盘和数据磁盘）的映像。 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | 重新启动 VM 规模集中的虚拟机实例。 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | 在虚拟机规模集的虚拟机实例上执行预定义的脚本。 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | 启动 VM 规模集中的虚拟机实例。 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | 更新 VM 规模集中虚拟机的属性 |
> | 行动 | VirtualMachineScaleSets/vmSizes/read | 列出用于创建或更新虚拟机规模集中的虚拟机的可用大小 |
> | 行动 | Microsoft.Compute/virtualMachineScaleSets/write | 创建新的或更新现有的虚拟机规模集 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Consumption/balances/read | 列出管理组的计费周期的使用情况摘要。 |
> | 行动 | Microsoft.Consumption/budgets/delete | 按订阅或管理组删除预算。 |
> | 行动 | Microsoft.Consumption/budgets/read | 按订阅或管理组列出预算。 |
> | 行动 | Microsoft.Consumption/budgets/write | 按订阅或管理组创建和更新预算。 |
> | 行动 | Microsoft.Consumption/charges/read | 列出费用 |
> | 行动 | Microsoft.Consumption/credits/read | 列出信用额度 |
> | 行动 | Microsoft.Consumption/events/read | 列出事件 |
> | 行动 | Microsoft.Consumption/forecasts/read | 列出预测 |
> | 行动 | Microsoft.Consumption/lots/read | 列出批次 |
> | 行动 | Microsoft.Consumption/marketplaces/read | 列出 EA 和 WebDirect 订阅的市场资源使用情况。 |
> | 行动 | Microsoft.Consumption/operationresults/read | 列出 operationresults |
> | 行动 | Microsoft.Consumption/operations/read | 列出 Microsoft.Consumption 资源提供程序支持的所有操作。 |
> | 行动 | Microsoft.Consumption/operationstatus/read | 列出 operationstatus |
> | 行动 | Microsoft.Consumption/pricesheets/read | 列出订阅或管理组的 Pricesheets 数据。 |
> | 行动 | Microsoft.Consumption/register/action | 注册到消耗 RP |
> | 行动 | Microsoft.Consumption/reservationDetails/read | 按预订订单或管理组列出保留实例的使用情况详细信息。 详细信息数据为每天每个实例级别。 |
> | 行动 | Microsoft.Consumption/reservationRecommendations/read | 列出某个订阅的预留实例的单个或共享建议。 |
> | 行动 | Microsoft.Consumption/reservationSummaries/read | 按预订订单或管理组列出保留实例的使用情况详细信息。 摘要数据为每月或每天级别。 |
> | 行动 | Microsoft.Consumption/reservationTransactions/read | 按管理组列出预留实例的事务历史记录。 |
> | 行动 | Microsoft.Consumption/tags/read | 列出 EA 和订阅的标记。 |
> | 行动 | Microsoft. 消耗/租户/读取 | 列出租户 |
> | 行动 | Microsoft.Consumption/tenants/register/action | 按租户注册 Microsoft.Consumption 的作用域的操作。 |
> | 行动 | Microsoft.Consumption/terms/read | 列出订阅或管理组的条款。 |
> | 行动 | Microsoft.Consumption/usageDetails/read | 列出 EA 和 WebDirect 订阅的范围的使用情况详细信息。 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.ContainerInstance/containerGroups/containers/exec/action | 执行到特定容器中。 |
> | 行动 | Microsoft.ContainerInstance/containerGroups/containers/logs/read | 获取特定容器的日志。 |
> | 行动 | Microsoft.ContainerInstance/containerGroups/delete | 删除特定容器组。 |
> | 行动 | ContainerInstance/containerGroups/operationResults/read | 获取异步操作结果 |
> | 行动 | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | 获取容器组的诊断设置。 |
> | 行动 | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新容器组的诊断设置。 |
> | 行动 | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | 获取容器组的可用指标。 |
> | 行动 | Microsoft.ContainerInstance/containerGroups/read | 获取所有容器组。 |
> | 行动 | Microsoft.ContainerInstance/containerGroups/restart/action | 重启特定容器组。 |
> | 行动 | Microsoft.ContainerInstance/containerGroups/start/action | 启动特定容器组。 |
> | 行动 | Microsoft.ContainerInstance/containerGroups/stop/action | 停止特定容器组。 计算资源将解除分配且计费将停止。 |
> | 行动 | Microsoft.ContainerInstance/containerGroups/write | 创建或更新特定容器组。 |
> | 行动 | Microsoft.ContainerInstance/locations/cachedImages/read | 获取某个区域中订阅的缓存映像。 |
> | 行动 | Microsoft.ContainerInstance/locations/capabilities/read | 获取区域的功能。 |
> | 行动 | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | 通知 Microsoft.ContainerInstance 正在删除虚拟网络或子网。 |
> | 行动 | ContainerInstance/位置/操作/读取 | 列出 Azure 容器实例服务的操作。 |
> | 行动 | Microsoft.ContainerInstance/locations/usages/read | 获取特定区域的使用情况。 |
> | 行动 | ContainerInstance/操作/读取 | 列出 Azure 容器实例服务的操作。 |
> | 行动 | Microsoft.ContainerInstance/register/action | 为容器实例资源提供程序注册订阅并启用容器组的创建。 |
> | 行动 | ContainerInstance/serviceassociationlinks/delete | 删除由 azure 容器实例资源提供程序在子网上创建的服务关联链接。 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.ContainerRegistry/checkNameAvailability/read | 检查容器注册表名称是否可用。 |
> | 行动 | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | 向 Microsoft.ContainerRegistry 通知正在删除虚拟网络或子网 |
> | 行动 | Microsoft.ContainerRegistry/locations/operationResults/read | 获取异步操作结果 |
> | 行动 | Microsoft.ContainerRegistry/operations/read | 列出所有可用的 Azure 容器注册表 REST API 操作 |
> | 行动 | Microsoft.ContainerRegistry/register/action | 注册容器注册表资源提供程序的订阅，并启用容器注册表的创建 |
> | 行动 | Microsoft.containerregistry/注册表/项目/删除 | 删除容器注册表中的项目。 |
> | 行动 | Microsoft.ContainerRegistry/registries/builds/cancel/action | 取消现有生成。 |
> | 行动 | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | 获取链接以下载生成日志。 |
> | 行动 | Microsoft.ContainerRegistry/registries/builds/read | 获取指定生成的属性，或列出指定容器注册表的所有生成。 |
> | 行动 | Microsoft.ContainerRegistry/registries/builds/write | 使用指定参数更新容器注册表的生成。 |
> | 行动 | Microsoft.ContainerRegistry/registries/buildTasks/delete | 从容器注册表中删除生成任务。 |
> | 行动 | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | 列出生成任务的源代码管理属性。 |
> | 行动 | Microsoft.ContainerRegistry/registries/buildTasks/read | 获取指定生成任务的属性，或列出指定容器注册表的所有生成任务。 |
> | 行动 | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | 从生成任务中删除生成步骤。 |
> | 行动 | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | 列出生成步骤的生成参数，包括机密参数。 |
> | 行动 | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | 获取指定生成步骤的属性，或列出指定生成任务的所有生成步骤。 |
> | 行动 | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | 使用指定的参数创建或更新生成任务的生成步骤。 |
> | 行动 | Microsoft.ContainerRegistry/registries/buildTasks/write | 使用指定参数创建或更新容器注册表的生成任务。 |
> | 行动 | Microsoft.ContainerRegistry/registries/delete | 删除容器注册表。 |
> | 行动 | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | 从容器注册表中删除事件网格筛选器。 |
> | 行动 | Microsoft.ContainerRegistry/registries/eventGridFilters/read | 获取指定事件网格筛选器的属性，或列出指定容器注册表的所有事件网格筛选器。 |
> | 行动 | Microsoft.ContainerRegistry/registries/eventGridFilters/write | 使用指定参数创建或更新容器注册表的事件网格筛选器。 |
> | 行动 | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | 获取上传位置，以便用户能够上传源。 |
> | 行动 | Microsoft.ContainerRegistry/registries/importImage/action | 使用指定的参数将映像导入到容器注册表中。 |
> | 行动 | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | 获取容器注册表的源上传 URL 位置。 |
> | 行动 | Microsoft.ContainerRegistry/registries/listCredentials/action | 列出指定容器注册表的登录凭据。 |
> | 行动 | Microsoft.ContainerRegistry/registries/listPolicies/read | 列出指定容器注册表的策略 |
> | 行动 | Microsoft.ContainerRegistry/registries/listUsages/read | 列出指定容器注册表的配额使用情况。 |
> | 行动 | Microsoft.containerregistry/注册表/元数据/读取 | 获取容器注册表的特定存储库的元数据 |
> | 行动 | Microsoft.containerregistry/注册表/元数据/写入 | 更新容器注册表的存储库的元数据 |
> | 行动 | Microsoft.ContainerRegistry/registries/operationStatuses/read | 获取注册表异步操作状态 |
> | 行动 | Microsoft.ContainerRegistry/registries/pull/read | 从容器注册表中拉取或获取映像。 |
> | 行动 | Microsoft.ContainerRegistry/registries/push/write | 将映像推送或写入容器注册表。 |
> | 行动 | Microsoft.ContainerRegistry/registries/quarantineRead/read | 从容器注册表中拉取或获取已隔离的映像 |
> | 行动 | Microsoft.ContainerRegistry/registries/quarantineWrite/write | 写入/修改已隔离映像的隔离状态 |
> | 行动 | Microsoft.ContainerRegistry/registries/queueBuild/action | 根据请求参数创建新生成，并将其添加到生成队列。 |
> | 行动 | Microsoft.ContainerRegistry/registries/read | 获取指定容器注册表的属性，或列出指定资源组或订阅下的所有容器注册表。 |
> | 行动 | Microsoft.ContainerRegistry/registries/regenerateCredential/action | 为指定容器注册表重新生成一个登录凭据。 |
> | 行动 | Microsoft.ContainerRegistry/registries/replications/delete | 从容器注册表中删除复制。 |
> | 行动 | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | 获取复制步操作状态 |
> | 行动 | Microsoft.ContainerRegistry/registries/replications/read | 获取指定复制的属性，或列出指定容器注册表的所有复制。 |
> | 行动 | Microsoft.ContainerRegistry/registries/replications/write | 使用指定参数创建或更新容器注册表的复制。 |
> | 行动 | Microsoft.ContainerRegistry/registries/runs/cancel/action | 取消现有的运行。 |
> | 行动 | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | 获取运行的日志 SAS URL。 |
> | 行动 | Microsoft.ContainerRegistry/registries/runs/read | 获取针对容器注册表的运行的属性，或列出运行。 |
> | 行动 | Microsoft.ContainerRegistry/registries/runs/write | 更新运行。 |
> | 行动 | Microsoft.ContainerRegistry/registries/scheduleRun/action | 计划针对容器注册表的运行。 |
> | 行动 | Microsoft.ContainerRegistry/registries/sign/write | 推送/拉取容器注册表的内容信任元数据。 |
> | 行动 | Microsoft.ContainerRegistry/registries/tasks/delete | 删除容器注册表的任务。 |
> | 行动 | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | 列出容器注册表的任务的所有详细信息。 |
> | 行动 | Microsoft.ContainerRegistry/registries/tasks/read | 获取容器注册表的任务，或列出所有任务。 |
> | 行动 | Microsoft.ContainerRegistry/registries/tasks/write | 创建或更新容器注册表的任务。 |
> | 行动 | Microsoft.ContainerRegistry/registries/updatePolicies/write | 更新指定容器注册表的策略 |
> | 行动 | Microsoft.ContainerRegistry/registries/webhooks/delete | 从容器注册表中删除 Webhook。 |
> | 行动 | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | 获取服务 URI 的配置和 Webhook 的自定义标头。 |
> | 行动 | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | 列出指定 Webhook 的最新事件。 |
> | 行动 | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | 获取 Webhook 异步操作状态 |
> | 行动 | Microsoft.ContainerRegistry/registries/webhooks/ping/action | 触发一个将发送到 Webhook 的 ping 事件。 |
> | 行动 | Microsoft.ContainerRegistry/registries/webhooks/read | 获取指定 Webhook 的属性，或列出指定容器注册表的所有 Webhook。 |
> | 行动 | Microsoft.ContainerRegistry/registries/webhooks/write | 使用指定参数创建或更新容器注册表的 Webhook。 |
> | 行动 | Microsoft.ContainerRegistry/registries/write | 使用指定参数创建或更新容器注册表。 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.ContainerService/containerServices/delete | 删除容器服务 |
> | 行动 | Microsoft.ContainerService/containerServices/read | 获取容器服务 |
> | 行动 | Microsoft.ContainerService/containerServices/write | 创建新的或更新现有的容器服务 |
> | 行动 | Microsoft.ContainerService/locations/operationresults/read | 获取异步操作结果的状态 |
> | 行动 | Microsoft.ContainerService/locations/operations/read | 获取异步操作的状态 |
> | 行动 | Microsoft.ContainerService/locations/orchestrators/read | 列出支持的业务流程协调程序 |
> | 行动 | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | 使用列表凭据按角色名称获取托管的群集访问配置文件 |
> | 行动 | Microsoft.ContainerService/managedClusters/accessProfiles/read | 按角色名称获取托管的群集访问配置文件 |
> | 行动 | ContainerService/managedClusters/agentPools/delete | 删除代理池 |
> | 行动 | ContainerService/managedClusters/agentPools/read | 获取代理池 |
> | 行动 | ContainerService/managedClusters/agentPools/upgradeProfiles/read | 获取代理池的升级配置文件 |
> | 行动 | ContainerService/managedClusters/agentPools/write | 创建新的或更新现有的代理池 |
> | 行动 | Microsoft.ContainerService/managedClusters/delete | 删除托管的群集 |
> | 行动 | ContainerService/managedClusters/检测程序/read | 获取托管群集探测器 |
> | 行动 | ContainerService/managedClusters/diagnosticsState/read | 获取群集的诊断状态 |
> | 行动 | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | 列出托管群集的 clusterAdmin 凭据 |
> | 行动 | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | 列出托管群集的 clusterUser 凭据 |
> | 行动 | ContainerService/managedClusters/privateEndpointConnectionsApproval/action | 确定是否允许用户批准专用终结点连接 |
> | 行动 | Microsoft.ContainerService/managedClusters/read | 获取托管的群集 |
> | 行动 | Microsoft.ContainerService/managedClusters/resetAADProfile/action | 重置托管群集的 AAD 配置文件 |
> | 行动 | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | 重置托管群集的服务主体配置文件 |
> | 行动 | ContainerService/managedClusters/upgradeProfiles/read | 获取群集的升级配置文件 |
> | 行动 | Microsoft.ContainerService/managedClusters/write | 创建新的或更新现有的托管的群集 |
> | 行动 | Microsoft.ContainerService/openShiftClusters/delete | 删除开放式班次群集 |
> | 行动 | Microsoft.ContainerService/openShiftClusters/read | 获取打开的 Shift 群集 |
> | 行动 | Microsoft.ContainerService/openShiftClusters/write | 创建新的或更新现有的 Open Shift 群集 |
> | 行动 | Microsoft.ContainerService/openShiftManagedClusters/delete | 删除开放式班次托管群集 |
> | 行动 | Microsoft.ContainerService/openShiftManagedClusters/read | 获取开放式 Shift 托管群集 |
> | 行动 | Microsoft.ContainerService/openShiftManagedClusters/write | 创建新的或更新现有的 Open Shift 托管群集 |
> | 行动 | Microsoft.ContainerService/operations/read | 列出可对 Microsoft.ContainerService 资源提供程序执行的操作 |
> | 行动 | Microsoft.ContainerService/register/action | 将订阅注册到 Microsoft.ContainerService 资源提供程序 |
> | 行动 | Microsoft.ContainerService/unregister/action | 取消注册到 Microsoft.ContainerService 资源提供程序的订阅 |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.ContentModerator/applications/delete | 删除操作 |
> | 行动 | Microsoft.ContentModerator/applications/listSecrets/action | 列出机密 |
> | 行动 | Microsoft.ContentModerator/applications/listSingleSignOnToken/action | 读取单一登录令牌 |
> | 行动 | Microsoft.ContentModerator/applications/read | 读取操作 |
> | 行动 | Microsoft.ContentModerator/applications/write | 写入操作 |
> | 行动 | Microsoft.ContentModerator/applications/write | 写入操作 |
> | 行动 | Microsoft.ContentModerator/listCommunicationPreference/action | 列出通信首选项 |
> | 行动 | Microsoft.ContentModerator/operations/read | 读取操作 |
> | 行动 | Microsoft.ContentModerator/updateCommunicationPreference/action | 更新通信首选项 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | CostManagement/cloudConnectors/delete | 删除指定的 cloudConnector。 |
> | 行动 | CostManagement/cloudConnectors/read | 列出已通过身份验证的用户的 cloudConnectors。 |
> | 行动 | CostManagement/cloudConnectors/write | 创建或更新指定的 cloudConnector。 |
> | 行动 | Microsoft.CostManagement/dimensions/read | 按范围列出所有受支持的维度。 |
> | 行动 | Microsoft.CostManagement/exports/action | 运行指定的导出。 |
> | 行动 | Microsoft.CostManagement/exports/delete | 删除指定的导出。 |
> | 行动 | Microsoft.CostManagement/exports/read | 按范围列出导出。 |
> | 行动 | CostManagement/出口/运行/操作 | 运行导出。 |
> | 行动 | Microsoft.CostManagement/exports/write | 创建或更新指定的导出。 |
> | 行动 | CostManagement/externalBillingAccounts/externalSubscriptions/read | 列出已通过身份验证的用户的 externalBillingAccount 中的 externalSubscriptions。 |
> | 行动 | CostManagement/externalBillingAccounts/read | 列出已通过身份验证的用户的 externalBillingAccounts。 |
> | 行动 | CostManagement/externalSubscriptions/read | 列出已通过身份验证的用户的 externalSubscriptions。 |
> | 行动 | CostManagement/externalSubscriptions/write | 更新 externalSubscription 的关联管理组 |
> | 行动 | Microsoft.CostManagement/query/action | 按范围查询使用情况数据。 |
> | 行动 | Microsoft.CostManagement/query/read | 按范围查询使用情况数据。 |
> | 行动 | CostManagement/注册/操作 | 为订阅的 CostManagement 范围注册操作。 |
> | 行动 | Microsoft.CostManagement/reports/action | 按范围计划使用情况数据的报告。 |
> | 行动 | Microsoft.CostManagement/reports/read | 按范围计划使用情况数据的报告。 |
> | 行动 | CostManagement/租户/注册/操作 | 为租户的 CostManagement 范围注册操作。 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.DataBox/jobs/bookShipmentPickUp/action | 允许为退件预订提货。 |
> | 行动 | Microsoft.DataBox/jobs/cancel/action | 取消正在进行的订单。 |
> | 行动 | Microsoft.DataBox/jobs/delete | 删除订单 |
> | 行动 | Microsoft.DataBox/jobs/listCredentials/action | 列出与订单相关的未加密凭据。 |
> | 行动 | Microsoft.DataBox/jobs/read | 列出或获取订单 |
> | 行动 | Microsoft.DataBox/jobs/write | 创建或更新订单 |
> | 行动 | Microsoft.DataBox/locations/availableSkus/action | 此方法返回可用 SKU 列表。 |
> | 行动 | DataBox/位置/availableSkus/读取 | 列出或获取可用的 Sku |
> | 行动 | Microsoft.DataBox/locations/operationResults/read | 列出或获取操作结果 |
> | 行动 | DataBox/位置/regionConfiguration/action | 此方法返回区域的配置。 |
> | 行动 | Microsoft.DataBox/locations/validateAddress/action | 验证送货地址，并提供备用地址（如有）。 |
> | 行动 | DataBox/位置/validateInputs/action | 此方法进行所有类型的验证。 |
> | 行动 | DataBox/操作/读取 | 列出或获取操作 |
> | 行动 | Microsoft.DataBox/register/action | 注册提供程序 Microsoft.Databox |
> | 行动 | DataBox/订阅/resourceGroups/moveResources/action | 此方法执行资源移动。 |
> | 行动 | DataBox/订阅/resourceGroups/validateMoveResources/action | 此方法验证是否允许移动资源。 |
> | 行动 | DataBox/注销/操作 | 取消注册提供商 Databox |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | 列出或获取警报 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | 列出或获取警报 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | 删除带宽计划 |
> | 行动 | DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/read | 列出或获取操作结果 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | 列出或获取带宽计划 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | 列出或获取带宽计划 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | 创建或更新带宽计划 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | 删除 Data Box Edge 设备 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | 在设备中下载更新 |
> | 行动 | DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action | 检索资源扩展信息 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | 在设备上安装更新 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | 列出或获取作业 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | 列出或获取设备网络设置 |
> | 行动 | DataBoxEdge/dataBoxEdgeDevices/节点/读取 | 列出或获取节点 |
> | 行动 | DataBoxEdge/dataBoxEdgeDevices/operationResults/read | 列出或获取操作结果 |
> | 行动 | DataBoxEdge/dataBoxEdgeDevices/operationsStatus/read | 列出或获取操作状态 |
> | 行动 | DataBoxEdge/dataBoxEdgeDevices/orders/delete | 删除订单 |
> | 行动 | DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/read | 列出或获取操作结果 |
> | 行动 | DataBoxEdge/dataBoxEdgeDevices/orders/read | 列出或获取订单 |
> | 行动 | DataBoxEdge/dataBoxEdgeDevices/orders/read | 列出或获取订单 |
> | 行动 | DataBoxEdge/dataBoxEdgeDevices/orders/write | 创建或更新订单 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | 列出或获取 Data Box Edge 设备 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | 列出或获取 Data Box Edge 设备 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | 列出或获取 Data Box Edge 设备 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | 删除角色 |
> | 行动 | DataBoxEdge/dataBoxEdgeDevices/role/operationResults/read | 列出或获取操作结果 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | 列出或获取角色 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | 列出或获取角色 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | 创建或更新角色 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | 扫描更新 |
> | 行动 | DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/read | 列出或获取操作结果 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | 更新安全设置 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | 删除共享 |
> | 行动 | DataBoxEdge/dataBoxEdgeDevices/共享/operationResults/read | 列出或获取操作结果 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | 列出或获取共享 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | 列出或获取共享 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | 刷新与云中数据的共享元数据 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | 创建或更新共享 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | 删除存储帐户凭据 |
> | 行动 | DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | 列出或获取操作结果 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | 列出或获取存储帐户凭据 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | 列出或获取存储帐户凭据 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | 创建或更新存储帐户凭据 |
> | 行动 | DataBoxEdge/dataBoxEdgeDevices/trigger/delete | 删除触发器 |
> | 行动 | DataBoxEdge/dataBoxEdgeDevices/trigger/operationResults/read | 列出或获取操作结果 |
> | 行动 | DataBoxEdge/dataBoxEdgeDevices/trigger/read | 列出或获取触发器 |
> | 行动 | DataBoxEdge/dataBoxEdgeDevices/trigger/read | 列出或获取触发器 |
> | 行动 | DataBoxEdge/dataBoxEdgeDevices/trigger/write | 创建或更新触发器 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | 列出或获取更新摘要 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | 上传用于注册设备的证书 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | 删除共享用户 |
> | 行动 | DataBoxEdge/dataBoxEdgeDevices/users/operationResults/read | 列出或获取操作结果 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | 列出或获取共享用户 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | 列出或获取共享用户 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | 创建或更新共享用户 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | 创建或更新 Data Box Edge 设备 |
> | 行动 | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | 创建或更新 Data Box Edge 设备 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Databricks/位置/getNetworkPolicies/action | 根据 NRP 使用的位置获取子网的网络意向策略 |
> | 行动 | Microsoft.Databricks/register/action | 注册到 Databricks。 |
> | 行动 | Microsoft.Databricks/workspaces/delete | 删除 Databricks 工作区。 |
> | 行动 | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | 设置 Databricks 工作区的可用诊断设置 |
> | 行动 | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | 添加或修改诊断设置。 |
> | 行动 | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/logDefinitions/read | 获取 Databricks 工作区的可用日志定义 |
> | 行动 | Microsoft.Databricks/workspaces/read | 检索 Databricks 工作区列表。 |
> | 行动 | Microsoft.Databricks/workspaces/write | 创建 Databricks 工作区。 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.DataCatalog/catalogs/delete | 删除数据目录资源提供程序的目录资源。 |
> | 行动 | Microsoft.DataCatalog/catalogs/read | 读取数据目录资源提供程序的目录资源。 |
> | 行动 | Microsoft.DataCatalog/catalogs/write | 写入数据目录资源提供程序的目录资源。 |
> | 行动 | Microsoft.datacatalog/checkNameAvailability/read | 检查数据目录资源提供程序的目录名称可用性。 |
> | 行动 | Microsoft.datacatalog/datacatalogs/delete | 删除数据目录资源提供程序的 Microsoft.datacatalog 资源。 |
> | 行动 | Microsoft.datacatalog/datacatalogs/read | 读取数据目录资源提供程序的 Microsoft.datacatalog 资源。 |
> | 行动 | Microsoft.datacatalog/datacatalogs/write | 写入数据目录资源提供程序的 Microsoft.datacatalog 资源。 |
> | 行动 | Microsoft.DataCatalog/operations/read | 读取数据目录资源提供程序中的所有可用操作。 |
> | 行动 | Microsoft.DataCatalog/register/action | 为数据目录资源提供程序注册订阅 |
> | 行动 | Microsoft.DataCatalog/unregister/action | 取消注册数据目录资源提供程序的订阅 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | 检查数据工厂名称是否可用。 |
> | 行动 | Microsoft.DataFactory/datafactories/activitywindows/read | 使用指定的参数读取数据工厂中的活动时段。 |
> | 行动 | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | 使用指定的参数读取管道活动中的活动时段。 |
> | 行动 | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | 使用指定的参数读取管道的活动时段。 |
> | 行动 | Microsoft.DataFactory/datafactories/datapipelines/delete | 删除任何管道。 |
> | 行动 | Microsoft.DataFactory/datafactories/datapipelines/pause/action | 暂停任何管道。 |
> | 行动 | Microsoft.DataFactory/datafactories/datapipelines/read | 读取任何管道。 |
> | 行动 | Microsoft.DataFactory/datafactories/datapipelines/resume/action | 恢复任何管道。 |
> | 行动 | Microsoft.DataFactory/datafactories/datapipelines/update/action | 更新任何管道。 |
> | 行动 | Microsoft.DataFactory/datafactories/datapipelines/write | 创建或更新任何管道。 |
> | 行动 | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | 使用指定的参数读取数据集的活动时段。 |
> | 行动 | Microsoft.DataFactory/datafactories/datasets/delete | 删除任何数据集。 |
> | 行动 | Microsoft.DataFactory/datafactories/datasets/read | 读取任何数据集。 |
> | 行动 | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | 从给定的开始时间读取针对给定数据集的数据切片运行。 |
> | 行动 | Microsoft.DataFactory/datafactories/datasets/slices/read | 获取给定期间中的数据切片。 |
> | 行动 | Microsoft.DataFactory/datafactories/datasets/slices/write | 更新数据切片的状态。 |
> | 行动 | Microsoft.DataFactory/datafactories/datasets/write | 创建或更新任何数据集。 |
> | 行动 | Microsoft.DataFactory/datafactories/delete | 删除数据工厂。 |
> | 行动 | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | 读取任何网关的连接信息。 |
> | 行动 | Microsoft.DataFactory/datafactories/gateways/delete | 删除任何网关。 |
> | 行动 | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | 列出任何网关的身份验证密钥。 |
> | 行动 | Microsoft.DataFactory/datafactories/gateways/read | 读取任何网关。 |
> | 行动 | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | 重新生成任何网关的身份验证密钥。 |
> | 行动 | Microsoft.DataFactory/datafactories/gateways/write | 创建或更新任何网关。 |
> | 行动 | Microsoft.DataFactory/datafactories/linkedServices/delete | 删除任何链接的服务。 |
> | 行动 | Microsoft.DataFactory/datafactories/linkedServices/read | 读取任何链接的服务。 |
> | 行动 | Microsoft.DataFactory/datafactories/linkedServices/write | 创建或更新任何链接的服务。 |
> | 行动 | Microsoft.DataFactory/datafactories/read | 读取数据工厂。 |
> | 行动 | Microsoft.DataFactory/datafactories/runs/loginfo/read | 将 SAS URI 读取到包含日志的 blob 容器。 |
> | 行动 | Microsoft.DataFactory/datafactories/tables/delete | 删除任何数据集。 |
> | 行动 | Microsoft.DataFactory/datafactories/tables/read | 读取任何数据集。 |
> | 行动 | Microsoft.DataFactory/datafactories/tables/write | 创建或更新任何数据集。 |
> | 行动 | Microsoft.DataFactory/datafactories/write | 创建或更新数据工厂。 |
> | 行动 | Microsoft.DataFactory/factories/cancelpipelinerun/action | 取消由运行 ID 指定的管道运行。 |
> | 行动 | DataFactory/工厂/cancelSandboxPipelineRun/action | 取消管道的调试运行。 |
> | 行动 | DataFactory/工厂/createdataflowdebugsession/action | 创建数据流调试会话。 |
> | 行动 | DataFactory/工厂/数据流/delete | 删除数据流。 |
> | 行动 | DataFactory/工厂/数据流/read | 读取数据流。 |
> | 行动 | DataFactory/工厂/数据流/write | 创建或更新数据流 |
> | 行动 | Microsoft.DataFactory/factories/datasets/delete | 删除任何数据集。 |
> | 行动 | Microsoft.DataFactory/factories/datasets/read | 读取任何数据集。 |
> | 行动 | Microsoft.DataFactory/factories/datasets/write | 创建或更新任何数据集。 |
> | 行动 | DataFactory/工厂/debugpipelineruns/cancel/action | 取消管道的调试运行。 |
> | 行动 | Microsoft.DataFactory/factories/delete | 删除数据工厂。 |
> | 行动 | DataFactory/工厂/deletedataflowdebugsession/action | 删除数据流调试会话。 |
> | 行动 | DataFactory/工厂/getDataPlaneAccess/action | 获取对 ADF DataPlane 服务的访问权限。 |
> | 行动 | DataFactory/工厂/getDataPlaneAccess/read | 读取对 ADF DataPlane 服务的访问权限。 |
> | 行动 | DataFactory/工厂/getFeatureValue/action | 获取特定位置的公开控件功能值。 |
> | 行动 | DataFactory/工厂/getFeatureValue/read | 读取特定位置的公开控件功能值。 |
> | 行动 | Microsoft.DataFactory/factories/getGitHubAccessToken/action | 获取 GitHub 访问令牌。 |
> | 行动 | Microsoft.DataFactory/factories/integrationruntimes/delete | 删除任何集成运行时。 |
> | 行动 | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | 读取集成运行时连接信息。 |
> | 行动 | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | 获取指定的 Integration Runtime 的 SSIS Integration Runtime 元数据。 |
> | 行动 | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | 读取集成运行时状态。 |
> | 行动 | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | 在指定的共享 Integration Runtime 上创建链接的 Integration Runtime 引用。 |
> | 行动 | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | 列出任何集成运行时的身份验证密钥。 |
> | 行动 | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | 获取任何集成运行时的监视数据。 |
> | 行动 | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | 删除指定的集成运行时的节点。 |
> | 行动 | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | 返回集成运行时的指定节点的 IP 地址。 |
> | 行动 | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | 读取指定的集成运行时的节点。 |
> | 行动 | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | 更新自承载集成运行时节点。 |
> | 行动 | Microsoft.DataFactory/factories/integrationruntimes/read | 读取任何集成运行时。 |
> | 行动 | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | 刷新指定的 Integration Runtime 的 SSIS Integration Runtime 元数据。 |
> | 行动 | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | 重新生成指定的集成运行时的身份验证密钥。 |
> | 行动 | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | 从指定的集成运行时中删除链接的集成运行时引用。 |
> | 行动 | Microsoft.DataFactory/factories/integrationruntimes/start/action | 启动任何集成运行时。 |
> | 行动 | Microsoft.DataFactory/factories/integrationruntimes/stop/action | 停止任何集成运行时。 |
> | 行动 | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | 同步指定的集成运行时的凭据。 |
> | 行动 | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | 升级指定的集成运行时。 |
> | 行动 | Microsoft.DataFactory/factories/integrationruntimes/write | 创建或更新任何集成运行时。 |
> | 行动 | Microsoft.DataFactory/factories/linkedServices/delete | 删除链接的服务。 |
> | 行动 | Microsoft.DataFactory/factories/linkedServices/read | 读取链接的服务。 |
> | 行动 | Microsoft.DataFactory/factories/linkedServices/write | 创建或更新链接的服务 |
> | 行动 | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | 读取指定的管道运行 ID 的活动运行。 |
> | 行动 | Microsoft.DataFactory/factories/pipelineruns/cancel/action | 取消由运行 ID 指定的管道运行。 |
> | 行动 | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | 查询指定的管道运行 ID 的活动运行。 |
> | 行动 | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | 读取指定管道运行 ID 的查询活动运行的结果。 |
> | 行动 | Microsoft.DataFactory/factories/pipelineruns/read | 读取管道运行。 |
> | 行动 | Microsoft.DataFactory/factories/pipelines/createrun/action | 为管道创建运行。 |
> | 行动 | Microsoft.DataFactory/factories/pipelines/delete | 删除管道。 |
> | 行动 | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | 获取活动运行的进程。 |
> | 行动 | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | 读取管道运行。 |
> | 行动 | Microsoft.DataFactory/factories/pipelines/read | 读取管道。 |
> | 行动 | DataFactory/工厂/管道/沙盒/操作 | 为管道创建调试运行环境。 |
> | 行动 | DataFactory/工厂/管道/沙盒/创建/操作 | 为管道创建调试运行环境。 |
> | 行动 | DataFactory/工厂/管道/沙盒/运行/操作 | 为管道创建调试运行。 |
> | 行动 | Microsoft.DataFactory/factories/pipelines/write | 创建或更新管道 |
> | 行动 | Microsoft.DataFactory/factories/querydebugpipelineruns/action | 查询调试管道运行。 |
> | 行动 | Microsoft.DataFactory/factories/querypipelineruns/action | 查询管道运行。 |
> | 行动 | Microsoft.DataFactory/factories/querypipelineruns/read | 读取查询管道运行的结果。 |
> | 行动 | Microsoft.DataFactory/factories/querytriggerruns/action | 查询触发器运行。 |
> | 行动 | Microsoft.DataFactory/factories/querytriggerruns/read | 读取触发器运行的结果。 |
> | 行动 | Microsoft.DataFactory/factories/read | 读取数据工厂。 |
> | 行动 | DataFactory/工厂/sandboxpipelineruns/sandboxActivityRuns/read | 获取活动的调试运行信息。 |
> | 行动 | DataFactory/工厂/startdataflowdebugsession/action | 启动数据流调试会话。 |
> | 行动 | DataFactory/工厂/submitDataFlowForPreview/action | 提交数据流以便使用调试会话获取数据预览。 |
> | 行动 | Microsoft.DataFactory/factories/triggerruns/read | 读取触发器运行。 |
> | 行动 | Microsoft.DataFactory/factories/triggers/delete | 删除任何触发器。 |
> | 行动 | Microsoft.DataFactory/factories/triggers/read | 读取任何触发器。 |
> | 行动 | Microsoft.DataFactory/factories/triggers/start/action | 启动任何触发器。 |
> | 行动 | Microsoft.DataFactory/factories/triggers/stop/action | 停止任何触发器。 |
> | 行动 | Microsoft.DataFactory/factories/triggers/triggerruns/read | 读取触发器运行。 |
> | 行动 | Microsoft.DataFactory/factories/triggers/write | 创建或更新任何触发器。 |
> | 行动 | Microsoft.DataFactory/factories/write | 创建或更新数据工厂 |
> | 行动 | Microsoft.DataFactory/locations/configureFactoryRepo/action | 配置工厂的存储库。 |
> | 行动 | Microsoft.DataFactory/locations/getFeatureValue/action | 获取特定位置的公开控件功能值。 |
> | 行动 | Microsoft.DataFactory/locations/getFeatureValue/read | 读取特定位置的公开控件功能值。 |
> | 行动 | Microsoft.DataFactory/operations/read | 读取 Microsoft 数据工厂提供程序中的所有操作。 |
> | 行动 | Microsoft.DataFactory/register/action | 注册数据工厂资源提供程序的订阅。 |
> | 行动 | Microsoft.DataFactory/unregister/action | 取消注册数据工厂资源提供程序的订阅。 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | 删除计算策略。 |
> | 行动 | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | 获取有关计算策略的信息。 |
> | 行动 | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | 创建或更新计算策略。 |
> | 行动 | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | 从 DataLakeAnalytics 帐户取消链接 DataLakeStore 帐户。 |
> | 行动 | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | 获取有关 DataLakeAnalytics 帐户的链接 DataLakeStore 帐户的信息。 |
> | 行动 | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | 获取或更新 DataLakeAnalytics 帐户的链接 DataLakeStore 帐户。 |
> | 行动 | Microsoft.DataLakeAnalytics/accounts/delete | 删除 DataLakeAnalytics 帐户。 |
> | 行动 | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | 删除防火墙规则。 |
> | 行动 | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | 获取有关防火墙规则的信息。 |
> | 行动 | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | 创建或更新防火墙规则。 |
> | 行动 | Microsoft.DataLakeAnalytics/accounts/operationResults/read | 获取 DataLakeAnalytics 帐户操作的结果。 |
> | 行动 | Microsoft.DataLakeAnalytics/accounts/read | 获取有关现有 DataLakeAnalytics 帐户的信息。 |
> | 行动 | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | 列出 DataLakeAnalytics 帐户的链接存储帐户的存储容器的 SAS 令牌。 |
> | 行动 | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | 获取 DataLakeAnalytics 帐户的链接存储帐户的容器。 |
> | 行动 | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | 从 DataLakeAnalytics 帐户取消链接存储帐户。 |
> | 行动 | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | 获取有关 DataLakeAnalytics 帐户的链接存储帐户的信息。 |
> | 行动 | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | 创建或更新 DataLakeAnalytics 帐户的链接存储帐户。 |
> | 行动 | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | 授权取消由其他用户提交的作业。 |
> | 行动 | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | 在 DataLakeAnalytics 帐户中传输 SystemMaxAnalyticsUnits。 |
> | 行动 | DataLakeAnalytics/accounts/virtualNetworkRules/delete | 删除虚拟网络规则。 |
> | 行动 | DataLakeAnalytics/accounts/virtualNetworkRules/read | 获取有关虚拟网络规则的信息。 |
> | 行动 | DataLakeAnalytics/accounts/virtualNetworkRules/write | 创建或更新虚拟网络规则。 |
> | 行动 | Microsoft.DataLakeAnalytics/accounts/write | 创建或更新 DataLakeAnalytics 帐户。 |
> | 行动 | Microsoft.DataLakeAnalytics/locations/capability/read | 获取有关使用 DataLakeAnalytics 的订阅的功能信息。 |
> | 行动 | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | 检查 DataLakeAnalytics 帐户名称的可用性。 |
> | 行动 | Microsoft.DataLakeAnalytics/locations/operationResults/read | 获取 DataLakeAnalytics 帐户操作的结果。 |
> | 行动 | Microsoft.DataLakeAnalytics/locations/usages/read | 获取有关 DataLakeAnalytics 使用情况的订阅的配额使用信息。 |
> | 行动 | Microsoft.DataLakeAnalytics/operations/read | 获取 DataLakeAnalytics 的可用操作。 |
> | 行动 | Microsoft.DataLakeAnalytics/register/action | 将订阅注册到 DataLakeAnalytics。 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.DataLakeStore/accounts/delete | 删除 DataLakeStore 帐户。 |
> | 行动 | Microsoft.DataLakeStore/accounts/enableKeyVault/action | 为 DataLakeStore 帐户启用 KeyVault。 |
> | 行动 | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | 删除 EventGrid 筛选器。 |
> | 行动 | Microsoft.DataLakeStore/accounts/eventGridFilters/read | 获取 EventGrid 筛选器。 |
> | 行动 | Microsoft.DataLakeStore/accounts/eventGridFilters/write | 创建或更新 EventGrid 筛选器。 |
> | 行动 | Microsoft.DataLakeStore/accounts/firewallRules/delete | 删除防火墙规则。 |
> | 行动 | Microsoft.DataLakeStore/accounts/firewallRules/read | 获取有关防火墙规则的信息。 |
> | 行动 | Microsoft.DataLakeStore/accounts/firewallRules/write | 创建或更新防火墙规则。 |
> | 行动 | Microsoft.DataLakeStore/accounts/operationResults/read | 获取 DataLakeStore 帐户操作的结果。 |
> | 行动 | Microsoft.DataLakeStore/accounts/read | 获取有关现有 DataLakeStore 帐户的信息。 |
> | 行动 | DataLakeStore/帐户/共享/删除 | 删除共享。 |
> | 行动 | DataLakeStore/帐户/共享/读取 | 获取有关共享的信息。 |
> | 行动 | DataLakeStore/帐户/共享/写入 | 创建或更新共享。 |
> | 行动 | Microsoft.DataLakeStore/accounts/Superuser/action | 使用 Microsoft.Authorization/roleAssignments/write 授权后，对 Data Lake Store 上的超级用户授权。 |
> | 行动 | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | 删除受信任的标识提供者。 |
> | 行动 | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | 获取有关受信任标识提供者的信息。 |
> | 行动 | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | 创建或更新受信任的标识提供者。 |
> | 行动 | DataLakeStore/accounts/virtualNetworkRules/delete | 删除虚拟网络规则。 |
> | 行动 | DataLakeStore/accounts/virtualNetworkRules/read | 获取有关虚拟网络规则的信息。 |
> | 行动 | DataLakeStore/accounts/virtualNetworkRules/write | 创建或更新虚拟网络规则。 |
> | 行动 | Microsoft.DataLakeStore/accounts/write | 创建或更新 DataLakeStore 帐户。 |
> | 行动 | Microsoft.DataLakeStore/locations/capability/read | 获取有关使用 DataLakeStore 的订阅的功能信息。 |
> | 行动 | Microsoft.DataLakeStore/locations/checkNameAvailability/action | 检查 DataLakeStore 帐户名称的可用性。 |
> | 行动 | Microsoft.DataLakeStore/locations/operationResults/read | 获取 DataLakeStore 帐户操作的结果。 |
> | 行动 | Microsoft.DataLakeStore/locations/usages/read | 获取有关 DataLakeStore 使用情况的订阅的配额使用信息。 |
> | 行动 | Microsoft.DataLakeStore/operations/read | 获取 DataLakeStore 的可用操作。 |
> | 行动 | Microsoft.DataLakeStore/register/action | 将订阅注册到 DataLakeStore。 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.DataMigration/locations/operationResults/read | 获取与“202 已接受”响应相关的长时间运行操作的状态 |
> | 行动 | Microsoft.DataMigration/locations/operationStatuses/read | 获取与“202 已接受”响应相关的长时间运行操作的状态 |
> | 行动 | Microsoft.DataMigration/register/action | 向 Azure 数据库迁移服务提供商注册订阅 |
> | 行动 | Microsoft.datamigration/services/addWorker/action | 将 DMS 工作线程添加到服务的可用 wds 辅助角色 |
> | 行动 | Microsoft.DataMigration/services/checkStatus/action | 检查服务是否已部署并正在运行 |
> | 行动 | Microsoft.datamigration/services/configureWorker/action | 将 DMS 辅助角色配置给服务的可用 wds 辅助角色 |
> | 行动 | Microsoft.DataMigration/services/delete | 删除资源及其所有子级 |
> | 行动 | Microsoft.DataMigration/services/projects/accessArtifacts/action | 生成一个可用于 GET 或 PUT 项目的 URL |
> | 行动 | Microsoft.DataMigration/services/projects/delete | 删除资源及其所有子级 |
> | 行动 | Microsoft.DataMigration/services/projects/files/delete | 删除资源及其所有子级 |
> | 行动 | Microsoft.DataMigration/services/projects/files/read | 读取有关资源的信息 |
> | 行动 | Microsoft.DataMigration/services/projects/files/read/action | 获取可用于读取文件内容的 URL |
> | 行动 | Microsoft.DataMigration/services/projects/files/readWrite/action | 获取可用于读取或写入文件内容的 URL |
> | 行动 | Microsoft.DataMigration/services/projects/files/write | 创建或更新资源及其属性 |
> | 行动 | Microsoft.DataMigration/services/projects/read | 读取有关资源的信息 |
> | 行动 | Microsoft.DataMigration/services/projects/tasks/cancel/action | 如果任务当前正在运行，则将其取消 |
> | 行动 | Microsoft.DataMigration/services/projects/tasks/delete | 删除资源及其所有子级 |
> | 行动 | Microsoft.DataMigration/services/projects/tasks/read | 读取有关资源的信息 |
> | 行动 | Microsoft.DataMigration/services/projects/tasks/write | 运行 Azure 数据库迁移服务任务 |
> | 行动 | Microsoft.DataMigration/services/projects/write | 运行 Azure 数据库迁移服务任务 |
> | 行动 | Microsoft.DataMigration/services/read | 读取有关资源的信息 |
> | 行动 | Microsoft.datamigration/services/removeWorker/action | 删除服务的可用 wds 辅助角色的 DMS 辅助角色 |
> | 行动 | Microsoft.datamigration/services/serviceTasks/cancel/action | 如果任务当前正在运行，则将其取消 |
> | 行动 | Microsoft.datamigration/services/serviceTasks/delete | 删除资源及其所有子级 |
> | 行动 | Microsoft.datamigration/services/serviceTasks/read | 读取有关资源的信息 |
> | 行动 | Microsoft.datamigration/services/serviceTasks/write | 运行 Azure 数据库迁移服务任务 |
> | 行动 | Microsoft.DataMigration/services/slots/delete | 删除资源及其所有子级 |
> | 行动 | Microsoft.DataMigration/services/slots/read | 读取有关资源的信息 |
> | 行动 | Microsoft.DataMigration/services/slots/write | 创建或更新资源及其属性 |
> | 行动 | Microsoft.DataMigration/services/start/action | 启动 DMS 服务，使其可以再次处理迁移 |
> | 行动 | Microsoft.DataMigration/services/stop/action | 停止 DMS 服务，将成本降至最低 |
> | 行动 | Microsoft.datamigration/services/updateAgentConfig/action | 用提供的值更新 DMS 代理配置。 |
> | 行动 | Microsoft.DataMigration/services/write | 创建或更新资源及其属性 |
> | 行动 | Microsoft.DataMigration/skus/read | 获取 DMS 资源支持的 SKU 列表。 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | DBforMariaDB/checkNameAvailability/action | 验证给定的服务器名称是否可用于在全球范围内对给定的订阅进行预配。 |
> | 行动 | DBforMariaDB/位置/azureAsyncOperation/读取 | 返回 MariaDB 服务器操作结果 |
> | 行动 | DBforMariaDB/位置/operationResults/读取 | 返回基于 ResourceGroup 的 MariaDB 服务器操作结果 |
> | 行动 | DBforMariaDB/位置/operationResults/读取 | 返回 MariaDB 服务器操作结果 |
> | 行动 | Microsoft.DBforMariaDB/locations/performanceTiers/read | 返回可用的性能层列表。 |
> | 行动 | DBforMariaDB/位置/securityAlertPoliciesAzureAsyncOperation/读取 | 返回服务器威胁检测操作结果的列表。 |
> | 行动 | DBforMariaDB/位置/securityAlertPoliciesOperationResults/读取 | 返回服务器威胁检测操作结果的列表。 |
> | 行动 | DBforMariaDB/操作/读取 | 返回 MariaDB 操作的列表。 |
> | 行动 | Microsoft.DBforMariaDB/performanceTiers/read | 返回可用的性能层列表。 |
> | 行动 | DBforMariaDB/注册/操作 | 注册 MariaDB 资源提供程序 |
> | 行动 | DBforMariaDB/服务器/管理员/删除 | 删除 MariaDB 服务器的现有管理员。 |
> | 行动 | DBforMariaDB/服务器/管理员/读取 | 获取 MariaDB 服务器管理员的列表。 |
> | 行动 | DBforMariaDB/服务器/管理员/写入 | 用指定的参数创建或更新 MariaDB 服务器管理员。 |
> | 行动 | DBforMariaDB/服务器/顾问/createRecommendedActionSession/action | 创建新的建议操作会话 |
> | 行动 | DBforMariaDB/服务器/顾问/读取 | 返回顾问列表 |
> | 行动 | DBforMariaDB/服务器/顾问/读取 | 返回顾问 |
> | 行动 | DBforMariaDB/服务器/顾问/recommendedActions/read | 返回建议的操作列表 |
> | 行动 | DBforMariaDB/服务器/顾问/recommendedActions/read | 返回建议的操作列表 |
> | 行动 | DBforMariaDB/服务器/顾问/recommendedActions/read | 返回建议的操作 |
> | 行动 | Microsoft.DBforMariaDB/servers/configurations/read | 返回服务器的配置列表，或获取指定配置的属性。 |
> | 行动 | Microsoft.DBforMariaDB/servers/configurations/write | 更新指定配置的值 |
> | 行动 | DBforMariaDB/服务器/数据库/删除 | 删除现有的 MariaDB 数据库。 |
> | 行动 | DBforMariaDB/服务器/数据库/读取 | 返回 MariaDB 数据库的列表，或获取指定数据库的属性。 |
> | 行动 | DBforMariaDB/服务器/数据库/写入 | 使用指定的参数创建 MariaDB 数据库，或更新指定数据库的属性。 |
> | 行动 | Microsoft.DBforMariaDB/servers/delete | 删除现有服务器。 |
> | 行动 | Microsoft.DBforMariaDB/servers/firewallRules/delete | 删除现有防火墙规则。 |
> | 行动 | Microsoft.DBforMariaDB/servers/firewallRules/read | 返回服务器的防火墙规则列表，或获取指定防火墙规则的属性。 |
> | 行动 | Microsoft.DBforMariaDB/servers/firewallRules/write | 使用指定参数创建防火墙规则，或更新现有规则。 |
> | 行动 | DBforMariaDB/服务器/日志日志/读取 | 返回 MariaDB 日志日志的列表。 |
> | 行动 | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | 行动 | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | 行动 | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | 获取 MariaDB 服务器的可用日志 |
> | 行动 | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | 返回可用于数据库的指标类型 |
> | 行动 | DBforMariaDB/servers/queryTexts/action | 返回查询列表的文本 |
> | 行动 | DBforMariaDB/servers/queryTexts/action | 返回查询的文本 |
> | 行动 | Microsoft.DBforMariaDB/servers/read | 返回服务器列表，或获取指定服务器的属性。 |
> | 行动 | Microsoft.DBforMariaDB/servers/recoverableServers/read | 返回可恢复的 MariaDB 服务器信息 |
> | 行动 | DBforMariaDB/服务器/副本/读取 | 获取 MariaDB 服务器的读取副本 |
> | 行动 | DBforMariaDB/服务器/重新启动/操作 | 重新启动特定的服务器。 |
> | 行动 | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | 检索在给定的服务器上配置的服务器威胁检测策略的详细信息 |
> | 行动 | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | 更改给定服务器的服务器威胁检测策略 |
> | 行动 | DBforMariaDB/servers/topQueryStatistics/read | 返回热门查询的查询统计信息列表。 |
> | 行动 | DBforMariaDB/servers/topQueryStatistics/read | 返回查询统计信息 |
> | 行动 | Microsoft.DBforMariaDB/servers/updateConfigurations/action | 更新指定服务器的配置 |
> | 行动 | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | 删除现有虚拟网络规则 |
> | 行动 | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | 返回虚拟网络规则列表，或获取指定虚拟网络规则的属性。 |
> | 行动 | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | 使用指定参数创建虚拟网络规则，或更新指定虚拟网络规则的属性或标记。 |
> | 行动 | DBforMariaDB/servers/waitStatistics/read | 返回实例的等待统计信息 |
> | 行动 | DBforMariaDB/servers/waitStatistics/read | 返回等待统计信息 |
> | 行动 | Microsoft.DBforMariaDB/servers/write | 使用指定参数创建服务器，或更新指定服务器的属性或标记。 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | DBforMySQL/checkNameAvailability/action | 验证给定的服务器名称是否可用于在全球范围内对给定的订阅进行预配。 |
> | 行动 | DBforMySQL/位置/azureAsyncOperation/读取 | 返回 MySQL 服务器操作结果 |
> | 行动 | DBforMySQL/位置/operationResults/读取 | 返回基于 ResourceGroup 的 MySQL 服务器操作结果 |
> | 行动 | DBforMySQL/位置/operationResults/读取 | 返回 MySQL 服务器操作结果 |
> | 行动 | Microsoft.DBforMySQL/locations/performanceTiers/read | 返回可用的性能层列表。 |
> | 行动 | DBforMySQL/位置/securityAlertPoliciesAzureAsyncOperation/读取 | 返回服务器威胁检测操作结果的列表。 |
> | 行动 | DBforMySQL/位置/securityAlertPoliciesOperationResults/读取 | 返回服务器威胁检测操作结果的列表。 |
> | 行动 | DBforMySQL/操作/读取 | 返回 MySQL 操作的列表。 |
> | 行动 | Microsoft.DBforMySQL/performanceTiers/read | 返回可用的性能层列表。 |
> | 行动 | DBforMySQL/注册/操作 | 注册 MySQL 资源提供程序 |
> | 行动 | DBforMySQL/服务器/管理员/删除 | 删除 MySQL server 的现有管理员。 |
> | 行动 | DBforMySQL/服务器/管理员/读取 | 获取 MySQL 服务器管理员的列表。 |
> | 行动 | DBforMySQL/服务器/管理员/写入 | 用指定的参数创建或更新 MySQL server 管理员。 |
> | 行动 | DBforMySQL/服务器/顾问/createRecommendedActionSession/action | 创建新的建议操作会话 |
> | 行动 | DBforMySQL/服务器/顾问/读取 | 返回顾问列表 |
> | 行动 | DBforMySQL/服务器/顾问/读取 | 返回顾问 |
> | 行动 | DBforMySQL/服务器/顾问/recommendedActions/read | 返回建议的操作列表 |
> | 行动 | DBforMySQL/服务器/顾问/recommendedActions/read | 返回建议的操作列表 |
> | 行动 | DBforMySQL/服务器/顾问/recommendedActions/read | 返回建议的操作 |
> | 行动 | Microsoft.DBforMySQL/servers/configurations/read | 返回服务器的配置列表，或获取指定配置的属性。 |
> | 行动 | Microsoft.DBforMySQL/servers/configurations/write | 更新指定配置的值 |
> | 行动 | DBforMySQL/服务器/数据库/删除 | 删除现有 MySQL 数据库。 |
> | 行动 | DBforMySQL/服务器/数据库/读取 | 返回 MySQL 数据库的列表，或获取指定数据库的属性。 |
> | 行动 | DBforMySQL/服务器/数据库/写入 | 使用指定的参数创建 MySQL 数据库，或更新指定数据库的属性。 |
> | 行动 | Microsoft.DBforMySQL/servers/delete | 删除现有服务器。 |
> | 行动 | Microsoft.DBforMySQL/servers/firewallRules/delete | 删除现有防火墙规则。 |
> | 行动 | Microsoft.DBforMySQL/servers/firewallRules/read | 返回服务器的防火墙规则列表，或获取指定防火墙规则的属性。 |
> | 行动 | Microsoft.DBforMySQL/servers/firewallRules/write | 使用指定参数创建防火墙规则，或更新现有规则。 |
> | 行动 | DBforMySQL/服务器/日志日志/读取 | 返回 PostgreSQL 日志日志的列表。 |
> | 行动 | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | 行动 | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | 行动 | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | 获取 MySQL 服务器的可用日志 |
> | 行动 | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | 返回可用于数据库的指标类型 |
> | 行动 | DBforMySQL/servers/queryTexts/action | 返回查询列表的文本 |
> | 行动 | DBforMySQL/servers/queryTexts/action | 返回查询的文本 |
> | 行动 | Microsoft.DBforMySQL/servers/read | 返回服务器列表，或获取指定服务器的属性。 |
> | 行动 | Microsoft.DBforMySQL/servers/recoverableServers/read | 返回可恢复的 MySQL 服务器信息 |
> | 行动 | DBforMySQL/服务器/副本/读取 | 获取 MySQL 服务器的读取副本 |
> | 行动 | DBforMySQL/服务器/重新启动/操作 | 重新启动特定的服务器。 |
> | 行动 | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | 检索在给定的服务器上配置的服务器威胁检测策略的详细信息 |
> | 行动 | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | 更改给定服务器的服务器威胁检测策略 |
> | 行动 | DBforMySQL/servers/topQueryStatistics/read | 返回热门查询的查询统计信息列表。 |
> | 行动 | DBforMySQL/servers/topQueryStatistics/read | 返回查询统计信息 |
> | 行动 | Microsoft.DBforMySQL/servers/updateConfigurations/action | 更新指定服务器的配置 |
> | 行动 | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | 删除现有虚拟网络规则 |
> | 行动 | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | 返回虚拟网络规则列表，或获取指定虚拟网络规则的属性。 |
> | 行动 | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | 使用指定参数创建虚拟网络规则，或更新指定虚拟网络规则的属性或标记。 |
> | 行动 | DBforMySQL/servers/waitStatistics/read | 返回实例的等待统计信息 |
> | 行动 | DBforMySQL/servers/waitStatistics/read | 返回等待统计信息 |
> | 行动 | Microsoft.DBforMySQL/servers/write | 使用指定参数创建服务器，或更新指定服务器的属性或标记。 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | DBforPostgreSQL/checkNameAvailability/action | 验证给定的服务器名称是否可用于在全球范围内对给定的订阅进行预配。 |
> | 行动 | DBforPostgreSQL/位置/azureAsyncOperation/读取 | 返回 PostgreSQL 服务器操作结果 |
> | 行动 | DBforPostgreSQL/位置/operationResults/读取 | 返回基于 ResourceGroup 的 PostgreSQL 服务器操作结果 |
> | 行动 | DBforPostgreSQL/位置/operationResults/读取 | 返回 PostgreSQL 服务器操作结果 |
> | 行动 | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | 返回可用的性能层列表。 |
> | 行动 | DBforPostgreSQL/位置/privateEndpointConnectionAzureAsyncOperation/读取 | 获取私有终结点连接操作的结果 |
> | 行动 | DBforPostgreSQL/位置/privateEndpointConnectionOperationResults/读取 | 获取私有终结点连接操作的结果 |
> | 行动 | DBforPostgreSQL/位置/privateEndpointConnectionProxyAzureAsyncOperation/读取 | 获取私有终结点连接代理操作的结果 |
> | 行动 | DBforPostgreSQL/位置/privateEndpointConnectionProxyOperationResults/读取 | 获取私有终结点连接代理操作的结果 |
> | 行动 | DBforPostgreSQL/位置/securityAlertPoliciesAzureAsyncOperation/读取 | 返回服务器威胁检测操作结果的列表。 |
> | 行动 | DBforPostgreSQL/位置/securityAlertPoliciesOperationResults/读取 | 返回服务器威胁检测操作结果的列表。 |
> | 行动 | DBforPostgreSQL/操作/读取 | 返回 PostgreSQL 操作的列表。 |
> | 行动 | Microsoft.DBforPostgreSQL/performanceTiers/read | 返回可用的性能层列表。 |
> | 行动 | DBforPostgreSQL/注册/操作 | 注册 PostgreSQL 资源提供程序 |
> | 行动 | DBforPostgreSQL/服务器/管理员/删除 | 删除 PostgreSQL 服务器的现有管理员。 |
> | 行动 | DBforPostgreSQL/服务器/管理员/读取 | 获取 PostgreSQL 服务器管理员的列表。 |
> | 行动 | DBforPostgreSQL/服务器/管理员/写入 | 用指定的参数创建或更新 PostgreSQL 服务器管理员。 |
> | 行动 | Microsoft.DBforPostgreSQL/servers/advisors/read | 返回顾问列表 |
> | 行动 | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | 返回建议的操作列表 |
> | 行动 | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | 做出建议 |
> | 行动 | Microsoft.DBforPostgreSQL/servers/configurations/read | 返回服务器的配置列表，或获取指定配置的属性。 |
> | 行动 | Microsoft.DBforPostgreSQL/servers/configurations/write | 更新指定配置的值 |
> | 行动 | DBforPostgreSQL/服务器/数据库/删除 | 删除现有的 PostgreSQL 数据库。 |
> | 行动 | DBforPostgreSQL/服务器/数据库/读取 | 返回 PostgreSQL 数据库的列表，或获取指定数据库的属性。 |
> | 行动 | DBforPostgreSQL/服务器/数据库/写入 | 使用指定的参数创建 PostgreSQL 数据库，或更新指定数据库的属性。 |
> | 行动 | Microsoft.DBforPostgreSQL/servers/delete | 删除现有服务器。 |
> | 行动 | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | 删除现有防火墙规则。 |
> | 行动 | Microsoft.DBforPostgreSQL/servers/firewallRules/read | 返回服务器的防火墙规则列表，或获取指定防火墙规则的属性。 |
> | 行动 | Microsoft.DBforPostgreSQL/servers/firewallRules/write | 使用指定参数创建防火墙规则，或更新现有规则。 |
> | 行动 | DBforPostgreSQL/服务器/日志日志/读取 | 返回 PostgreSQL 日志日志的列表。 |
> | 行动 | DBforPostgreSQL/servers/privateEndpointConnectionProxies/delete | 删除现有的专用终结点连接代理 |
> | 行动 | DBforPostgreSQL/servers/privateEndpointConnectionProxies/read | 返回专用终结点连接代理列表，或获取指定的专用终结点连接代理的属性。 |
> | 行动 | DBforPostgreSQL/servers/privateEndpointConnectionProxies/validate/action | 验证专用终结点连接从 NRP 端创建调用 |
> | 行动 | DBforPostgreSQL/servers/privateEndpointConnectionProxies/write | 使用指定的参数创建专用终结点连接代理，或更新指定专用终结点连接代理的属性或标记。 |
> | 行动 | DBforPostgreSQL/servers/privateEndpointConnections/delete | 删除现有的专用终结点连接 |
> | 行动 | DBforPostgreSQL/servers/privateEndpointConnections/read | 返回专用终结点连接的列表，或获取指定的专用终结点连接的属性。 |
> | 行动 | DBforPostgreSQL/servers/privateEndpointConnections/write | 批准或拒绝现有的专用终结点连接 |
> | 行动 | DBforPostgreSQL/servers/privateLinkResources/read | 获取相应 PostgreSQL 服务器的专用链接资源 |
> | 行动 | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | 行动 | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | 行动 | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | 获取 Postgres 服务器的可用日志 |
> | 行动 | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | 返回可用于数据库的指标类型 |
> | 行动 | Microsoft.DBforPostgreSQL/servers/queryTexts/action | 返回查询的文本 |
> | 行动 | DBforPostgreSQL/servers/queryTexts/read | 返回查询列表的文本 |
> | 行动 | Microsoft.DBforPostgreSQL/servers/read | 返回服务器列表，或获取指定服务器的属性。 |
> | 行动 | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | 返回可恢复的 PostgreSQL 服务器信息 |
> | 行动 | DBforPostgreSQL/服务器/副本/读取 | 获取 PostgreSQL 服务器的读取副本 |
> | 行动 | DBforPostgreSQL/服务器/重新启动/操作 | 重新启动特定的服务器。 |
> | 行动 | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | 检索在给定的服务器上配置的服务器威胁检测策略的详细信息 |
> | 行动 | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | 更改给定服务器的服务器威胁检测策略 |
> | 行动 | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | 返回热门查询的查询统计信息列表。 |
> | 行动 | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | 更新指定服务器的配置 |
> | 行动 | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | 删除现有虚拟网络规则 |
> | 行动 | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | 返回虚拟网络规则列表，或获取指定虚拟网络规则的属性。 |
> | 行动 | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | 使用指定参数创建虚拟网络规则，或更新指定虚拟网络规则的属性或标记。 |
> | 行动 | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | 返回实例的等待统计信息 |
> | 行动 | Microsoft.DBforPostgreSQL/servers/write | 使用指定参数创建服务器，或更新指定服务器的属性或标记。 |
> | 行动 | DBforPostgreSQL/serversv2/配置/读取 | 返回服务器的配置列表，或获取指定配置的属性。 |
> | 行动 | DBforPostgreSQL/serversv2/配置/写入 | 更新指定配置的值 |
> | 行动 | DBforPostgreSQL/serversv2/delete | 删除现有服务器。 |
> | 行动 | DBforPostgreSQL/serversv2/firewallRules/delete | 删除现有防火墙规则。 |
> | 行动 | DBforPostgreSQL/serversv2/firewallRules/read | 返回服务器的防火墙规则列表，或获取指定防火墙规则的属性。 |
> | 行动 | DBforPostgreSQL/serversv2/firewallRules/write | 使用指定参数创建防火墙规则，或更新现有规则。 |
> | 行动 | DBforPostgreSQL/serversv2/providers/diagnosticSettings/read | 获取资源的诊断设置 |
> | 行动 | DBforPostgreSQL/serversv2/providers/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | 行动 | DBforPostgreSQL/serversv2/providers/logDefinitions/read | 获取 Postgres 服务器的可用日志 |
> | 行动 | DBforPostgreSQL/serversv2/providers/metricDefinitions/read | 返回可用于数据库的指标类型 |
> | 行动 | DBforPostgreSQL/serversv2/read | 返回服务器列表，或获取指定服务器的属性。 |
> | 行动 | DBforPostgreSQL/serversv2/updateConfigurations/action | 更新指定服务器的配置 |
> | 行动 | DBforPostgreSQL/serversv2/write | 使用指定参数创建服务器，或更新指定服务器的属性或标记。 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft. 设备/帐户/diagnosticSettings/读取 | 获取资源的诊断设置 |
> | 行动 | Microsoft. 设备/帐户/diagnosticSettings/写入 | 创建或更新资源的诊断设置 |
> | 行动 | Microsoft. 设备/帐户/logDefinitions/读取 | 获取 IotHub 服务的可用日志定义 |
> | 行动 | Microsoft. 设备/帐户/metricDefinitions/读取 | 获取 IotHub 服务的可用指标 |
> | 行动 | Microsoft.Devices/checkNameAvailability/Action | 检查 IotHub 名称是否可用 |
> | 行动 | Microsoft.Devices/checkNameAvailability/Action | 检查 IotHub 名称是否可用 |
> | 行动 | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | 检查预配服务名称是否可用 |
> | 行动 | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | 检查预配服务名称是否可用 |
> | 行动 | DigitalTwins/删除 | 删除现有的数字孪生帐户及其所有子项 |
> | 行动 | DigitalTwins/operationresults/Read | 获取针对数字孪生帐户的操作状态 |
> | 行动 | DigitalTwins/Read | 获取与订阅关联的数字孪生帐户的列表 |
> | 行动 | DigitalTwins/sku/读取 | 获取用于数字孪生帐户的有效 Sku 列表 |
> | 行动 | DigitalTwins/写入 | 创建新的 Digitial 孪生帐户 |
> | 行动 | Microsoft.Devices/ElasticPools/diagnosticSettings/read | 获取资源的诊断设置 |
> | 行动 | Microsoft.Devices/ElasticPools/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | 行动 | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | 删除证书 |
> | 行动 | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | 生成验证码 |
> | 行动 | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | 获取证书 |
> | 行动 | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | 验证证书资源 |
> | 行动 | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | 创建或更新证书 |
> | 行动 | Microsoft.Devices/elasticPools/iotHubTenants/Delete | 删除 IotHub 租户资源 |
> | 行动 | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | 获取资源的诊断设置 |
> | 行动 | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | 行动 | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | 删除 EventHub 使用者组 |
> | 行动 | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | 获取 EventHub 使用者组 |
> | 行动 | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | 创建 EventHub 使用者组 |
> | 行动 | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | 导出设备 |
> | 行动 | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | 获取 IotHub 租户统计资源 |
> | 行动 | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | 导入设备 |
> | 行动 | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | 获取 IotHub 租户密钥 |
> | 行动 | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | 获取在给定 IotHub 上提交的作业详细信息 |
> | 行动 | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | 获取 IotHub 租户密钥 |
> | 行动 | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | 获取 IotHub 服务的可用日志定义 |
> | 行动 | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | 获取 IotHub 服务的可用指标 |
> | 行动 | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | 获取配额指标 |
> | 行动 | Microsoft.Devices/elasticPools/iotHubTenants/Read | 获取 IotHub 租户资源 |
> | 行动 | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | 针对所有现有路由测试消息 |
> | 行动 | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | 针对提供的测试路由测试消息 |
> | 行动 | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | 获取 IotHub 的所有路由终结点的运行状况 |
> | 行动 | Microsoft.Devices/elasticPools/iotHubTenants/Write | 创建或更新 IotHub 租户资源 |
> | 行动 | Microsoft.Devices/ElasticPools/metricDefinitions/read | 获取 IotHub 服务的可用指标 |
> | 行动 | Microsoft.Devices/iotHubs/certificates/Delete | 删除证书 |
> | 行动 | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | 生成验证码 |
> | 行动 | Microsoft.Devices/iotHubs/certificates/Read | 获取证书 |
> | 行动 | Microsoft.Devices/iotHubs/certificates/verify/Action | 验证证书资源 |
> | 行动 | Microsoft.Devices/iotHubs/certificates/Write | 创建或更新证书 |
> | 行动 | Microsoft.Devices/iotHubs/Delete | 删除 IotHub 资源 |
> | 行动 | Microsoft.Devices/IotHubs/diagnosticSettings/read | 获取资源的诊断设置 |
> | 行动 | Microsoft.Devices/IotHubs/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | 行动 | Microsoft.Devices/iotHubs/eventGridFilters/Delete | 删除事件网格筛选器 |
> | 行动 | Microsoft.Devices/iotHubs/eventGridFilters/Read | 获取事件网格筛选器 |
> | 行动 | Microsoft.Devices/iotHubs/eventGridFilters/Write | 创建新的或更新现有的事件网格筛选器 |
> | 行动 | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | 删除 EventHub 使用者组 |
> | 行动 | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | 获取 EventHub 使用者组 |
> | 行动 | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | 创建 EventHub 使用者组 |
> | 行动 | Microsoft.Devices/iotHubs/exportDevices/Action | 导出设备 |
> | 行动 | Microsoft.Devices/iotHubs/importDevices/Action | 导入设备 |
> | 行动 | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | 获取给定名称的 IotHub 密钥 |
> | 行动 | Microsoft.Devices/iotHubs/iotHubStats/Read | 获取 IotHub 统计信息 |
> | 行动 | Microsoft.Devices/iotHubs/jobs/Read | 获取在给定 IotHub 上提交的作业详细信息 |
> | 行动 | Microsoft.Devices/iotHubs/listkeys/Action | 获取所有 IotHub 密钥 |
> | 行动 | Microsoft.Devices/IotHubs/logDefinitions/read | 获取 IotHub 服务的可用日志定义 |
> | 行动 | Microsoft.Devices/IotHubs/metricDefinitions/read | 获取 IotHub 服务的可用指标 |
> | 行动 | Microsoft.Devices/iotHubs/operationresults/Read | 获取操作结果（已过时的 API） |
> | 行动 | Microsoft.Devices/iotHubs/quotaMetrics/Read | 获取配额指标 |
> | 行动 | Microsoft.Devices/iotHubs/Read | 获取 IotHub 资源 |
> | 行动 | Microsoft.Devices/iotHubs/routing/$testall/Action | 针对所有现有路由测试消息 |
> | 行动 | Microsoft.Devices/iotHubs/routing/$testnew/Action | 针对提供的测试路由测试消息 |
> | 行动 | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | 获取 IotHub 的所有路由终结点的运行状况 |
> | 行动 | Microsoft.Devices/iotHubs/skus/Read | 获取有效的 IotHub SKU |
> | 行动 | Microsoft.Devices/iotHubs/Write | 创建或更新 IotHub 资源 |
> | 行动 | Microsoft.Devices/locations/operationresults/Read | 获取基于位置的操作结果 |
> | 行动 | Microsoft.Devices/operationresults/Read | 获取操作结果 |
> | 行动 | Microsoft.Devices/operations/Read | 获取所有 ResourceProvider 操作 |
> | 行动 | Microsoft.Devices/provisioningServices/certificates/Delete | 删除证书 |
> | 行动 | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | 生成验证码 |
> | 行动 | Microsoft.Devices/provisioningServices/certificates/Read | 获取证书 |
> | 行动 | Microsoft.Devices/provisioningServices/certificates/verify/Action | 验证证书资源 |
> | 行动 | Microsoft.Devices/provisioningServices/certificates/Write | 创建或更新证书 |
> | 行动 | Microsoft.Devices/provisioningServices/Delete | 删除 IotDps 资源 |
> | 行动 | Microsoft.Devices/provisioningServices/diagnosticSettings/read | 获取资源的诊断设置 |
> | 行动 | Microsoft.Devices/provisioningServices/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | 行动 | Microsoft.Devices/provisioningServices/keys/listkeys/Action | 获取 IotDps 密钥的密钥名称 |
> | 行动 | Microsoft.Devices/provisioningServices/listkeys/Action | 获取所有 IotDps 密钥 |
> | 行动 | Microsoft.Devices/provisioningServices/logDefinitions/read | 获取预配服务的可用日志定义 |
> | 行动 | Microsoft.Devices/provisioningServices/metricDefinitions/read | 获取预配服务的可用指标 |
> | 行动 | Microsoft.Devices/provisioningServices/operationresults/Read | 获取 DPS 操作结果 |
> | 行动 | Microsoft.Devices/provisioningServices/Read | 获取 IotDps 资源 |
> | 行动 | Microsoft.Devices/provisioningServices/skus/Read | 获取有效的 IotDps SKU |
> | 行动 | Microsoft.Devices/provisioningServices/Write | 创建 IotDps 资源 |
> | 行动 | Microsoft.Devices/register/action | 注册 IotHub 资源提供程序的订阅，并启用 IotHub 资源的创建 |
> | 行动 | Microsoft.Devices/usages/Read | 获取此提供程序的订阅用量详细信息。 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.DevSpaces/controllers/delete | 删除 Azure Dev Spaces 控制器和数据平面服务 |
> | 行动 | Microsoft.DevSpaces/controllers/listConnectionDetails/action | 列出 Azure Dev Spaces 控制器基础结构的连接详细信息 |
> | 行动 | Microsoft.DevSpaces/controllers/read | 读取 Azure Dev Spaces 控制器属性 |
> | 行动 | Microsoft.DevSpaces/controllers/write | 创建或更新 Azure Dev Spaces 控制器属性 |
> | 行动 | DevSpaces/位置/checkContainerHostMapping/action | 检查容器主机的现有控制器映射 |
> | 行动 | DevSpaces/位置/operationresults/读取 | 读取异步操作的状态 |
> | 行动 | Microsoft.DevSpaces/register/action | 将 Microsoft Dev Spaces 资源提供程序注册到订阅 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.DevTestLab/labCenters/delete | 删除实验室中心。 |
> | 行动 | Microsoft.DevTestLab/labCenters/read | 读取实验室中心。 |
> | 行动 | Microsoft.DevTestLab/labCenters/write | 添加或修改实验室中心。 |
> | 行动 | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | 读取 Azure Resource Manager 模板。 |
> | 行动 | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | 为给定的项目生成 ARM 模板，将所需的文件上传到存储帐户，然后验证生成的项目。 |
> | 行动 | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | 读取项目。 |
> | 行动 | Microsoft.DevTestLab/labs/artifactSources/delete | 删除项目源。 |
> | 行动 | Microsoft.DevTestLab/labs/artifactSources/read | 读取项目源。 |
> | 行动 | Microsoft.DevTestLab/labs/artifactSources/write | 添加或修改项目源。 |
> | 行动 | Microsoft.DevTestLab/labs/ClaimAnyVm/action | 在实验室中认领随机可认领虚拟机。 |
> | 行动 | Microsoft.DevTestLab/labs/costs/read | 读取成本。 |
> | 行动 | Microsoft.DevTestLab/labs/costs/write | 添加或修改成本。 |
> | 行动 | Microsoft.DevTestLab/labs/CreateEnvironment/action | 在实验室中创建虚拟机。 |
> | 行动 | Microsoft.DevTestLab/labs/customImages/delete | 删除自定义映像。 |
> | 行动 | Microsoft.DevTestLab/labs/customImages/read | 读取自定义映像。 |
> | 行动 | Microsoft.DevTestLab/labs/customImages/write | 添加或修改自定义映像。 |
> | 行动 | Microsoft.DevTestLab/labs/delete | 删除实验室。 |
> | 行动 | Microsoft.devtestlab/实验室/EnsureCurrentUserProfile/action | 确保当前用户在实验室中有有效的配置文件。 |
> | 行动 | Microsoft.DevTestLab/labs/ExportResourceUsage/action | 将实验室资源使用情况导出到存储帐户 |
> | 行动 | Microsoft.DevTestLab/labs/formulas/delete | 删除公式。 |
> | 行动 | Microsoft.DevTestLab/labs/formulas/read | 读取公式。 |
> | 行动 | Microsoft.DevTestLab/labs/formulas/write | 添加或修改公式。 |
> | 行动 | Microsoft.DevTestLab/labs/galleryImages/read | 读取库映像。 |
> | 行动 | Microsoft.DevTestLab/labs/GenerateUploadUri/action | 生成用于将自定义磁盘映像上传到实验室的 URI。 |
> | 行动 | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | 将虚拟机导入到不同实验室。 |
> | 行动 | Microsoft.DevTestLab/labs/ListVhds/action | 列出可用于创建自定义映像的磁盘映像。 |
> | 行动 | Microsoft.DevTestLab/labs/notificationChannels/delete | 删除通知通道。 |
> | 行动 | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | 将通知发送到提供的通道。 |
> | 行动 | Microsoft.DevTestLab/labs/notificationChannels/read | 读取通知通道。 |
> | 行动 | Microsoft.DevTestLab/labs/notificationChannels/write | 添加或修改通知通道。 |
> | 行动 | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | 评估实验室策略。 |
> | 行动 | Microsoft.DevTestLab/labs/policySets/policies/delete | 删除策略。 |
> | 行动 | Microsoft.DevTestLab/labs/policySets/policies/read | 读取策略。 |
> | 行动 | Microsoft.DevTestLab/labs/policySets/policies/write | 添加或修改策略。 |
> | 行动 | Microsoft.DevTestLab/labs/policySets/read | 读取策略集。 |
> | 行动 | Microsoft.DevTestLab/labs/read | 读取实验室。 |
> | 行动 | Microsoft.DevTestLab/labs/schedules/delete | 删除计划。 |
> | 行动 | Microsoft.DevTestLab/labs/schedules/Execute/action | 执行计划。 |
> | 行动 | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | 列出所有适用的计划 |
> | 行动 | Microsoft.DevTestLab/labs/schedules/read | 读取计划。 |
> | 行动 | Microsoft.DevTestLab/labs/schedules/write | 添加或修改计划。 |
> | 行动 | Microsoft.DevTestLab/labs/serviceRunners/delete | 删除服务运行程序。 |
> | 行动 | Microsoft.DevTestLab/labs/serviceRunners/read | 读取服务运行程序。 |
> | 行动 | Microsoft.DevTestLab/labs/serviceRunners/write | 添加或修改服务运行程序。 |
> | 行动 | Microsoft.devtestlab/实验室/sharedGalleries/delete | 删除共享的库。 |
> | 行动 | Microsoft.devtestlab/labs/sharedGalleries/read | 读取共享的库。 |
> | 行动 | Microsoft.devtestlab/labs/sharedGalleries/sharedImages/delete | 删除共享图像。 |
> | 行动 | Microsoft.devtestlab/labs/sharedGalleries/sharedImages/read | 读取共享图像。 |
> | 行动 | Microsoft.devtestlab/labs/sharedGalleries/sharedImages/write | 添加或修改共享映像。 |
> | 行动 | Microsoft.devtestlab/labs/sharedGalleries/write | 添加或修改共享库。 |
> | 行动 | Microsoft.DevTestLab/labs/users/delete | 删除用户配置文件。 |
> | 行动 | Microsoft.DevTestLab/labs/users/disks/Attach/action | 在虚拟机上附加和创建磁盘的租约。 |
> | 行动 | Microsoft.DevTestLab/labs/users/disks/delete | 删除磁盘。 |
> | 行动 | Microsoft.DevTestLab/labs/users/disks/Detach/action | 分离并破坏已附加到虚拟机的磁盘的租约。 |
> | 行动 | Microsoft.DevTestLab/labs/users/disks/read | 读取磁盘。 |
> | 行动 | Microsoft.DevTestLab/labs/users/disks/write | 添加或修改磁盘。 |
> | 行动 | Microsoft.DevTestLab/labs/users/environments/delete | 删除环境。 |
> | 行动 | Microsoft.DevTestLab/labs/users/environments/read | 读取环境。 |
> | 行动 | Microsoft.DevTestLab/labs/users/environments/write | 添加或修改环境。 |
> | 行动 | Microsoft.DevTestLab/labs/users/read | 读取用户配置文件。 |
> | 行动 | Microsoft.DevTestLab/labs/users/secrets/delete | 删除机密。 |
> | 行动 | Microsoft.DevTestLab/labs/users/secrets/read | 读取机密。 |
> | 行动 | Microsoft.DevTestLab/labs/users/secrets/write | 添加或修改机密。 |
> | 行动 | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | 删除 Service Fabric。 |
> | 行动 | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | 列出适用的启动/停止计划（如果有）。 |
> | 行动 | Microsoft.DevTestLab/labs/users/serviceFabrics/read | 读取 Service Fabric。 |
> | 行动 | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | 删除计划。 |
> | 行动 | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | 执行计划。 |
> | 行动 | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | 读取计划。 |
> | 行动 | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | 添加或修改计划。 |
> | 行动 | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | 启动 Service Fabric。 |
> | 行动 | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | 停止 Service Fabric |
> | 行动 | Microsoft.DevTestLab/labs/users/serviceFabrics/write | 添加或修改 Service Fabric。 |
> | 行动 | Microsoft.DevTestLab/labs/users/write | 添加或修改用户配置文件。 |
> | 行动 | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | 将新的或现有的数据磁盘附加到虚拟机。 |
> | 行动 | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | 将项目应用到虚拟机。 |
> | 行动 | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | 获得现有虚拟机的所有权 |
> | 行动 | Microsoft.DevTestLab/labs/virtualMachines/delete | 删除虚拟机。 |
> | 行动 | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | 从虚拟机中分离指定的磁盘。 |
> | 行动 | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | 获取一个字符串，该字符串表示虚拟机的 RDP 文件内容 |
> | 行动 | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | 列出适用的启动/停止计划（如果有）。 |
> | 行动 | Microsoft.DevTestLab/labs/virtualMachines/read | 读取虚拟机。 |
> | 行动 | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | 重新部署虚拟机 |
> | 行动 | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | 调整虚拟机大小。 |
> | 行动 | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | 重新启动虚拟机。 |
> | 行动 | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | 删除计划。 |
> | 行动 | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | 执行计划。 |
> | 行动 | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | 读取计划。 |
> | 行动 | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | 添加或修改计划。 |
> | 行动 | Microsoft.DevTestLab/labs/virtualMachines/Start/action | 启动虚拟机。 |
> | 行动 | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | 停止虚拟机 |
> | 行动 | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | 转移附加到虚拟机的所有数据磁盘，该虚拟机将规当前用户所有。 |
> | 行动 | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | 释放现有虚拟机的所有权 |
> | 行动 | Microsoft.DevTestLab/labs/virtualMachines/write | 添加或修改虚拟机。 |
> | 行动 | Microsoft.devtestlab/labs/virtualNetworks/bastionHosts/delete | 删除 bastionhosts。 |
> | 行动 | Microsoft.devtestlab/labs/virtualNetworks/bastionHosts/read | 读取 bastionhosts。 |
> | 行动 | Microsoft.devtestlab/labs/virtualNetworks/bastionHosts/write | 添加或修改 bastionhosts。 |
> | 行动 | Microsoft.DevTestLab/labs/virtualNetworks/delete | 创建虚拟网络。 |
> | 行动 | Microsoft.DevTestLab/labs/virtualNetworks/read | 读取虚拟网络。 |
> | 行动 | Microsoft.DevTestLab/labs/virtualNetworks/write | 添加或修改虚拟网络。 |
> | 行动 | Microsoft.DevTestLab/labs/vmPools/delete | 删除虚拟机池。 |
> | 行动 | Microsoft.DevTestLab/labs/vmPools/read | 读取虚拟机池。 |
> | 行动 | Microsoft.DevTestLab/labs/vmPools/write | 添加或修改虚拟机池。 |
> | 行动 | Microsoft.DevTestLab/labs/write | 添加或修改实验室。 |
> | 行动 | Microsoft.DevTestLab/locations/operations/read | 读取操作。 |
> | 行动 | Microsoft.DevTestLab/register/action | 注册订阅 |
> | 行动 | Microsoft.DevTestLab/schedules/delete | 删除计划。 |
> | 行动 | Microsoft.DevTestLab/schedules/Execute/action | 执行计划。 |
> | 行动 | Microsoft.DevTestLab/schedules/read | 读取计划。 |
> | 行动 | Microsoft.DevTestLab/schedules/Retarget/action | 更新计划的目标资源 ID。 |
> | 行动 | Microsoft.DevTestLab/schedules/write | 添加或修改计划。 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.DocumentDB/databaseAccountNames/read | 检查名称可用性。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/集合/删除 | 删除集合。 仅适用于 API 类型： "mongodb"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/集合/operationResults/读取 | 读取异步操作的状态。 仅适用于 API 类型： "mongodb"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/集合/读取 | 读取集合或列出所有集合。 仅适用于 API 类型： "mongodb"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/集合/设置/operationResults/读取 | 读取异步操作的状态。 仅适用于 API 类型： "mongodb"。 仅适用于设置类型： "吞吐量"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/集合/设置/读取 | 读取集合吞吐量。 仅适用于 API 类型： "mongodb"。 仅适用于设置类型： "吞吐量"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/集合/设置/写入 | 更新集合吞吐量。 仅适用于 API 类型： "mongodb"。 仅适用于设置类型： "吞吐量"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/集合/写入 | 创建或更新集合。 仅适用于 API 类型： "mongodb"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/容器/删除 | 删除容器。 仅适用于 API 类型： "sql"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/容器/operationResults/读取 | 读取异步操作的状态。 仅适用于 API 类型： "sql"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/容器/读取 | 读取容器或列出所有容器。 仅适用于 API 类型： "sql"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/容器/设置/operationResults/读取 | 读取异步操作的状态。 仅适用于 API 类型： "sql"。 仅适用于设置类型： "吞吐量"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/容器/设置/读取 | 读取容器吞吐量。 仅适用于 API 类型： "sql"。 仅适用于设置类型： "吞吐量"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/容器/设置/写入 | 更新容器吞吐量。 仅适用于 API 类型： "sql"。 仅适用于设置类型： "吞吐量"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/容器/写入 | 创建或更新容器。 仅适用于 API 类型： "sql"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/删除 | 删除数据库。 仅适用于 API 类型： "sql"、"mongodb"、"gremlin'"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/图形/删除 | 删除图形。 仅适用于 API 类型： ' gremlin'。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/图形/operationResults/读取 | 读取异步操作的状态。 仅适用于 API 类型： ' gremlin'。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/图形/读取 | 读取关系图或列表所有关系图。 仅适用于 API 类型： ' gremlin'。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/图形/设置/operationResults/读取 | 读取异步操作的状态。 仅适用于 API 类型： ' gremlin'。 仅适用于设置类型： "吞吐量"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/图形/设置/读取 | 读取图形吞吐量。 仅适用于 API 类型： ' gremlin'。 仅适用于设置类型： "吞吐量"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/图形/设置/写入 | 更新图形吞吐量。 仅适用于 API 类型： ' gremlin'。 仅适用于设置类型： "吞吐量"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/图形/写入 | 创建或更新关系图。 仅适用于 API 类型： ' gremlin'。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/operationResults/read | 读取异步操作的状态。 仅适用于 API 类型： "sql"、"mongodb"、"gremlin'"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/读取 | 读取数据库或列出所有数据库。 仅适用于 API 类型： "sql"、"mongodb"、"gremlin'"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/设置/operationResults/读取 | 读取异步操作的状态。 仅适用于 API 类型： "sql"、"mongodb"、"gremlin'"。 仅适用于设置类型： "吞吐量"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/设置/读取 | 读取数据库吞吐量。 仅适用于 API 类型： "sql"、"mongodb"、"gremlin'"。 仅适用于设置类型： "吞吐量"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/设置/写入 | 更新数据库吞吐量。 仅适用于 API 类型： "sql"、"mongodb"、"gremlin'"。 仅适用于设置类型： "吞吐量"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/数据库/写入 | 创建数据库。 仅适用于 API 类型： "sql"、"mongodb"、"gremlin'"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/大量/delete | 删除密钥空间。 仅适用于 API 类型： "cassandra"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/大量/operationResults/read | 读取异步操作的状态。 仅适用于 API 类型： "cassandra"。 |
> | 行动 | DatabaseAccounts/api/大量/read | 读取密钥空间或列出所有大量。 仅适用于 API 类型： "cassandra"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/大量/settings/operationResults/read | 读取异步操作的状态。 仅适用于 API 类型： "cassandra"。 仅适用于设置类型： "吞吐量"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/大量/settings/read | 读取密钥空间吞吐量。 仅适用于 API 类型： "cassandra"。 仅适用于设置类型： "吞吐量"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/大量/settings/write | 更新密钥空间吞吐量。 仅适用于 API 类型： "cassandra"。 仅适用于设置类型： "吞吐量"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/大量/tables/delete | 删除表。 仅适用于 API 类型： "cassandra"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/大量/tables/operationResults/read | 读取异步操作的状态。 仅适用于 API 类型： "cassandra"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/大量/tables/read | 读取表或列出所有表。 仅适用于 API 类型： "cassandra"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/大量/tables/settings/operationResults/read | 读取异步操作的状态。 仅适用于 API 类型： "cassandra"。 仅适用于设置类型： "吞吐量"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/大量/tables/settings/read | 读取表吞吐量。 仅适用于 API 类型： "cassandra"。 仅适用于设置类型： "吞吐量"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/大量/tables/settings/write | 更新表吞吐量。 仅适用于 API 类型： "cassandra"。 仅适用于设置类型： "吞吐量"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/大量/tables/write | 创建或更新表。 仅适用于 API 类型： "cassandra"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/大量/write | 创建密钥空间。 仅适用于 API 类型： "cassandra"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/表/删除 | 删除表。 仅适用于 API 类型： "表"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/tables/operationResults/read | 读取异步操作的状态。 仅适用于 API 类型： "表"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/表/读取 | 读取表或列出所有表。 仅适用于 API 类型： "表"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/tables/settings/operationResults/read | 读取异步操作的状态。 仅适用于 API 类型： "表"。 仅适用于设置类型： "吞吐量"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/表/设置/读取 | 读取表吞吐量。 仅适用于 API 类型： "表"。 仅适用于设置类型： "吞吐量"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/表/设置/写入 | 更新表吞吐量。 仅适用于 API 类型： "表"。 仅适用于设置类型： "吞吐量"。 |
> | 行动 | Microsoft DocumentDB/databaseAccounts/api/表/写入 | 创建或更新表。 仅适用于 API 类型： "表"。 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/backup/action | 提交配置备份的请求 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | 更改数据库帐户的资源组 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | 读取集合指标定义。 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | 读取集合指标。 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | 读取数据库帐户分区密钥级别指标 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | 读取数据库帐户分区级别指标 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | 读取集合中的数据库帐户分区 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | 读取数据库帐户分区级别使用情况 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | 读取集合使用情况。 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | 读取数据库指标定义 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | 读取数据库指标。 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | 读取数据库使用情况。 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/delete | 删除数据库帐户。 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | 更改数据库帐户的区域故障转移优先级。 用于执行手动故障转移操作 |
> | 行动 | DatabaseAccounts/getBackupPolicy/action | 获取数据库帐户的备份策略 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | 获取数据库帐户的连接字符串 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/listKeys/action | 列出数据库帐户的密钥 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | 读取数据库帐户指标定义。 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/metrics/read | 读取数据库帐户指标。 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | 使数据库帐户的区域脱机。  |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | 使数据库帐户的区域联机。 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/operationResults/read | 读取异步操作的状态 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | 读取延迟指标 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/percentile/read | 读取复制延迟的百分位数 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | 读取特定源和目标区域的延迟指标 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | 读取特定目标区域的延迟指标 |
> | 行动 | DatabaseAccounts/privateEndpointConnectionProxies/delete | 删除数据库帐户的专用终结点连接代理 |
> | 行动 | DatabaseAccounts/privateEndpointConnectionProxies/read | 读取数据库帐户的专用终结点连接代理 |
> | 行动 | DatabaseAccounts/privateEndpointConnectionProxies/validate/action | 验证数据库帐户的专用终结点连接代理 |
> | 行动 | DatabaseAccounts/privateEndpointConnectionProxies/write | 创建或更新数据库帐户的专用终结点连接代理 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/read | 读取数据库帐户。 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | 读取数据库帐户只读密钥。 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | 读取数据库帐户只读密钥。 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | 轮替数据库帐户的密钥 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | 读取区域集合指标。 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | 读取区域数据库帐户分区密钥级别指标 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | 读取区域数据库帐户分区级别指标 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | 读取集合中的区域性数据库帐户分区 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | 读取区域和数据库帐户指标。 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/restore/action | 提交还原请求 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/usages/read | 读取数据库帐户使用情况。 |
> | 行动 | Microsoft.DocumentDB/databaseAccounts/write | 更新数据库帐户。 |
> | 行动 | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | 向 Microsoft.DocumentDB 通知正在删除虚拟网络或子网 |
> | 行动 | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | 读取 deleteVirtualNetworkOrSubnets 异步操作的状态 |
> | 行动 | Microsoft DocumentDB/位置/operationsStatus/读取 | 读取异步操作的状态 |
> | 行动 | Microsoft.DocumentDB/operationResults/read | 读取异步操作的状态 |
> | 行动 | Microsoft.DocumentDB/operations/read | 读取适用于 Microsoft DocumentDB 的操作  |
> | 行动 | Microsoft.DocumentDB/register/action |  注册订阅的 Microsoft DocumentDB 资源提供程序 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.DomainRegistration/checkDomainAvailability/Action | 检查是否可购买某个域 |
> | 行动 | Microsoft.DomainRegistration/domains/Delete | 删除现有域。 |
> | 行动 | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | 删除所有权标识符 |
> | 行动 | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | 列出所有权标识符 |
> | 行动 | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | 获取所有权标识符 |
> | 行动 | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | 创建或更新标识符 |
> | 行动 | Microsoft.DomainRegistration/domains/operationresults/Read | 获取域操作 |
> | 行动 | Microsoft.DomainRegistration/domains/Read | 获取域的列表 |
> | 行动 | Microsoft.DomainRegistration/domains/Read | 获取域 |
> | 行动 | Microsoft.DomainRegistration/domains/renew/Action | 续订现有域。 |
> | 行动 | Microsoft.DomainRegistration/domains/Write | 添加新域，或更新现有域 |
> | 行动 | Microsoft.DomainRegistration/generateSsoRequest/Action | 生成登录域控制中心的请求。 |
> | 行动 | Microsoft.DomainRegistration/listDomainRecommendations/Action | 根据关键字检索列表域建议 |
> | 行动 | Microsoft.DomainRegistration/operations/Read | 列出应用服务域注册的所有操作 |
> | 行动 | Microsoft.DomainRegistration/register/action | 注册订阅的 Microsoft 域资源提供程序 |
> | 行动 | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | 列出协议操作 |
> | 行动 | Microsoft.DomainRegistration/topLevelDomains/Read | 获取顶级域 |
> | 行动 | Microsoft.DomainRegistration/topLevelDomains/Read | 获取顶级域 |
> | 行动 | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | 验证域购买对象但不提交该对象 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.EventGrid/domains/delete | 删除域 |
> | 行动 | Microsoft.EventGrid/domains/listKeys/action | 列出域的密钥 |
> | 行动 | Microsoft.EventGrid/domains/providers/Microsoft.Insights/metricDefinitions/read | 获取域的可用指标 |
> | 行动 | Microsoft.EventGrid/domains/read | 读取域 |
> | 行动 | Microsoft.EventGrid/domains/regenerateKey/action | 重新生成域的密钥 |
> | 行动 | EventGrid/域/主题/删除 | 删除域主题 |
> | 行动 | Microsoft.EventGrid/domains/topics/read | 读取域主题 |
> | 行动 | EventGrid/域/主题/写入 | 创建或更新域主题 |
> | 行动 | Microsoft.EventGrid/domains/write | 创建或更新域 |
> | 行动 | Microsoft.EventGrid/eventSubscriptions/delete | 删除 eventSubscription |
> | 行动 | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | 获取事件订阅的完整 URL |
> | 行动 | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | 获取事件订阅的诊断设置 |
> | 行动 | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新事件订阅的诊断设置 |
> | 行动 | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | 获取 eventSubscriptions 的可用指标 |
> | 行动 | Microsoft.EventGrid/eventSubscriptions/read | 读取 eventSubscription |
> | 行动 | Microsoft.EventGrid/eventSubscriptions/write | 创建或更新 eventSubscription |
> | 行动 | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | 获取主题的诊断设置 |
> | 行动 | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新主题的诊断设置 |
> | 行动 | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | 获取主题的可用指标 |
> | 行动 | Microsoft.EventGrid/extensionTopics/read | 读取 extensionTopic。 |
> | 行动 | Microsoft.EventGrid/locations/eventSubscriptions/read | 列出区域事件订阅 |
> | 行动 | Microsoft.EventGrid/locations/operationResults/read | 读取区域操作的结果 |
> | 行动 | Microsoft.EventGrid/locations/operationsStatus/read | 读取区域操作的状态 |
> | 行动 | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | 按主题类型列出区域事件订阅 |
> | 行动 | Microsoft.EventGrid/operationResults/read | 读取操作的结果 |
> | 行动 | Microsoft.EventGrid/operations/read | 列出 EventGrid 操作。 |
> | 行动 | Microsoft.EventGrid/operationsStatus/read | 读取操作的状态 |
> | 行动 | Microsoft.EventGrid/register/action | 注册事件网格资源提供程序的订阅。 |
> | 行动 | Microsoft.EventGrid/topics/delete | 删除主题 |
> | 行动 | Microsoft.EventGrid/topics/listKeys/action | 列出主题的密钥 |
> | 行动 | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | 获取主题的诊断设置 |
> | 行动 | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新主题的诊断设置 |
> | 行动 | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | 获取主题的可用指标 |
> | 行动 | Microsoft.EventGrid/topics/read | 读取主题 |
> | 行动 | Microsoft.EventGrid/topics/regenerateKey/action | 重新生成主题的密钥 |
> | 行动 | Microsoft.EventGrid/topics/write | 创建或更新主题 |
> | 行动 | Microsoft.EventGrid/topictypes/eventSubscriptions/read | 按主题类型列出全局事件订阅 |
> | 行动 | Microsoft.EventGrid/topictypes/eventtypes/read | 读取主题类型支持的事件类型 |
> | 行动 | Microsoft.EventGrid/topictypes/read | 读取主题类型 |
> | 行动 | Microsoft.EventGrid/unregister/action | 取消注册事件网格资源提供程序的订阅。 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | AvailableClusterRegions/read | 按 Azure 区域列出可用预配群集的读取操作。 |
> | 行动 | Microsoft.EventHub/checkNameAvailability/action | 检查给定订阅下的命名空间可用性。 |
> | 行动 | Microsoft.EventHub/checkNamespaceAvailability/action | 检查给定订阅下的命名空间可用性。 已弃用此 API，请改用 CheckNameAvailability。 |
> | 行动 | Node.js/群集/删除 | 删除现有群集资源。 |
> | 行动 | Node.js/群集/命名空间/读取 | 列出群集中的命名空间的命名空间 ARM Id。 |
> | 行动 | Node.js/群集/operationresults/读取 | 获取异步群集操作的状态。 |
> | 行动 | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | 获取群集指标资源说明列表 |
> | 行动 | Microsoft.EventHub/clusters/read | 获取群集资源说明 |
> | 行动 | Microsoft.EventHub/clusters/write | 创建或修改现有群集资源。 |
> | 行动 | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | 在事件中心资源提供程序中删除指定 VNet 的 VNet 规则 |
> | 行动 | Microsoft.EventHub/namespaces/authorizationRules/action | 更新命名空间授权规则。 已弃用此 API。 请改为使用 PUT 调用来更新命名空间授权规则。 不支持对 API 版本 2017-04-01 执行此操作。 |
> | 行动 | Microsoft.EventHub/namespaces/authorizationRules/delete | 删除命名空间授权规则。 无法删除默认的命名空间授权规则。  |
> | 行动 | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | 获取命名空间的连接字符串 |
> | 行动 | Microsoft.EventHub/namespaces/authorizationRules/read | 获取命名空间授权规则说明列表。 |
> | 行动 | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | 再生成资源的主密钥或辅助密钥 |
> | 行动 | Microsoft.EventHub/namespaces/authorizationRules/write | 创建命名空间级授权规则并更新其属性。 可以更新授权规则访问权限、主密钥和辅助密钥。 |
> | 行动 | Microsoft.EventHub/namespaces/Delete | 删除命名空间资源 |
> | 行动 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | 获取灾难恢复主命名空间的授权规则密钥 |
> | 行动 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | 获取灾难恢复主命名空间的授权规则 |
> | 行动 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | 禁用灾难恢复，并停止从主命名空间向辅助命名空间复制更改。 |
> | 行动 | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 检查给定订阅下命名空间别名的可用性。 |
> | 行动 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | 删除与命名空间关联的灾难恢复配置。 只能通过主命名空间调用此操作。 |
> | 行动 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | 调用异地灾难恢复故障转移并将命名空间别名重新配置为指向辅助命名空间。 |
> | 行动 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | 获取与命名空间关联的灾难恢复配置。 |
> | 行动 | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | 创建或更新与命名空间关联的灾难恢复配置。 |
> | 行动 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | 更新 EventHub 的操作。 不支持对 API 版本 2017-04-01 执行此操作。 授权规则。 请使用 PUT 调用来更新授权规则。 |
> | 行动 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | 用于删除 EventHub 授权规则的操作 |
> | 行动 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | 获取 EventHub 的连接字符串 |
> | 行动 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  获取 EventHub 授权规则列表 |
> | 行动 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | 再生成资源的主密钥或辅助密钥 |
> | 行动 | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | 创建 EventHub 授权规则并更新其属性。 可以更新授权规则访问权限。 |
> | 行动 | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | 用于删除 ConsumerGroup 资源的操作 |
> | 行动 | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | 获取 ConsumerGroup 资源说明列表 |
> | 行动 | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | 创建或更新 ConsumerGroup 属性。 |
> | 行动 | Microsoft.EventHub/namespaces/eventhubs/Delete | 用于删除 EventHub 资源的操作 |
> | 行动 | Microsoft.EventHub/namespaces/eventhubs/read | 获取 EventHub 资源说明列表 |
> | 行动 | Microsoft.EventHub/namespaces/eventhubs/write | 创建或更新 EventHub 属性。 |
> | 行动 | Microsoft.EventHub/namespaces/ipFilterRules/delete | 删除 IP 筛选器资源 |
> | 行动 | Microsoft.EventHub/namespaces/ipFilterRules/read | 获取 IP 筛选器资源 |
> | 行动 | Microsoft.EventHub/namespaces/ipFilterRules/write | 创建 IP 筛选器资源 |
> | DataAction | Node.js/命名空间/消息/接收/操作 | 接收消息 |
> | DataAction | Node.js/命名空间/消息/发送/操作 | 发送消息 |
> | 行动 | Microsoft.EventHub/namespaces/messagingPlan/read | 获取命名空间的消息传递计划。<br>已弃用此 API。<br>在以后的 API 版本中，通过 MessagingPlan 资源公开的属性将移动到（父）命名空间资源。<br>不支持对 API 版本 2017-04-01 执行此操作。 |
> | 行动 | Microsoft.EventHub/namespaces/messagingPlan/write | 更新命名空间的消息传递计划。<br>已弃用此 API。<br>在以后的 API 版本中，通过 MessagingPlan 资源公开的属性将移动到（父）命名空间资源。<br>不支持对 API 版本 2017-04-01 执行此操作。 |
> | 行动 | Networkruleset/命名空间/删除 | 删除 VNET 规则资源 |
> | 行动 | Node.js/命名空间/networkruleset/读取 | 获取 NetworkRuleSet 资源 |
> | 行动 | Node.js/命名空间/networkruleset/写入 | 创建 VNET 规则资源 |
> | 行动 | Networkrulesets/命名空间/删除 | 删除 VNET 规则资源 |
> | 行动 | Node.js/命名空间/networkrulesets/读取 | 获取 NetworkRuleSet 资源 |
> | 行动 | Node.js/命名空间/networkrulesets/写入 | 创建 VNET 规则资源 |
> | 行动 | Microsoft.EventHub/namespaces/operationresults/read | 获取命名空间操作的状态 |
> | 行动 | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | 获取命名空间诊断设置资源说明列表 |
> | 行动 | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | 获取命名空间诊断设置资源说明列表 |
> | 行动 | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | 获取命名空间日志资源说明列表 |
> | 行动 | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 获取命名空间指标资源说明列表 |
> | 行动 | Microsoft.EventHub/namespaces/read | 获取命名空间资源说明列表 |
> | 行动 | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | 删除 ACS 命名空间 |
> | 行动 | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | 删除 VNET 规则资源 |
> | 行动 | Microsoft.EventHub/namespaces/virtualNetworkRules/read | 获取 VNET 规则资源 |
> | 行动 | Microsoft.EventHub/namespaces/virtualNetworkRules/write | 创建 VNET 规则资源 |
> | 行动 | Microsoft.EventHub/namespaces/write | 创建命名空间资源并更新其属性。 命名空间的标记和容量是可更新的属性。 |
> | 行动 | Microsoft.EventHub/operations/read | 获取操作 |
> | 行动 | Microsoft.EventHub/register/action | 注册 EventHub 资源提供程序的订阅，并启用 EventHub 资源的创建 |
> | 行动 | Microsoft.EventHub/sku/read | 获取 SKU 资源说明列表 |
> | 行动 | Microsoft.EventHub/sku/regions/read | 获取 SKU 区域资源说明列表 |
> | 行动 | Microsoft.EventHub/unregister/action | 注册 EventHub 资源提供程序 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Features/features/read | 获取订阅的功能。 |
> | 行动 | Microsoft.Features/operations/read | 获取操作列表。 |
> | 行动 | Microsoft.Features/providers/features/read | 获取给定资源提供程序中某个订阅的功能。 |
> | 行动 | Microsoft.Features/providers/features/register/action | 在给定的资源提供程序中注册某个订阅的功能。 |
> | 行动 | Microsoft.Features/providers/features/unregister/action | 取消注册给定资源提供程序中的订阅的功能。 |
> | 行动 | Microsoft.Features/register/action | 注册某个订阅的功能。 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | GuestConfiguration/guestConfigurationAssignments/delete | 删除来宾配置分配。 |
> | 行动 | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | 获取来宾配置分配。 |
> | 行动 | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | 获取来宾配置分配报告。 |
> | 行动 | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | 创建新的来宾配置分配。 |
> | 行动 | GuestConfiguration/操作/读取 | 获取 GuestConfiguration 资源提供程序的操作 |
> | 行动 | GuestConfiguration/注册/操作 | 注册 GuestConfiguration 资源提供程序的订阅。 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.HDInsight/clusters/applications/delete | 删除 HDInsight 群集的应用程序 |
> | 行动 | Microsoft.HDInsight/clusters/applications/read | 获取 HDInsight 群集的应用程序 |
> | 行动 | Microsoft.HDInsight/clusters/applications/write | 创建或更新 HDInsight 群集的应用程序 |
> | 行动 | Microsoft.HDInsight/clusters/changerdpsetting/action | 更改 HDInsight 群集的 RDP 设置 |
> | 行动 | Microsoft.HDInsight/clusters/configurations/action | 更新 HDInsight 群集配置 |
> | 行动 | Microsoft.HDInsight/clusters/configurations/action | 获取 HDInsight 群集配置 |
> | 行动 | Microsoft.HDInsight/clusters/configurations/read | 获取 HDInsight 群集配置 |
> | 行动 | Microsoft.HDInsight/clusters/delete | 删除 HDInsight 群集 |
> | 行动 | Microsoft HDInsight/群集/扩展/删除 | 删除 HDInsight 群集的群集扩展 |
> | 行动 | Microsoft HDInsight/群集/扩展/读取 | 获取 HDInsight 群集的群集扩展 |
> | 行动 | Microsoft HDInsight/群集/扩展/写入 | 为 HDInsight 群集创建群集扩展 |
> | 行动 | Microsoft HDInsight/群集/getGatewaySettings/操作 | 获取 HDInsight 群集的网关设置 |
> | 行动 | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源 HDInsight 群集的诊断设置 |
> | 行动 | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源 HDInsight 群集的诊断设置 |
> | 行动 | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | 获取 HDInsight 群集的可用指标 |
> | 行动 | Microsoft.HDInsight/clusters/read | 获取有关 HDInsight 群集的详细信息 |
> | 行动 | Microsoft.HDInsight/clusters/roles/resize/action | 调整 HDInsight 群集的大小 |
> | 行动 | Microsoft HDInsight/群集/updateGatewaySettings/操作 | 更新 HDInsight 群集的网关设置 |
> | 行动 | Microsoft.HDInsight/clusters/write | 创建或更新 HDInsight 群集 |
> | 行动 | Microsoft.HDInsight/locations/capabilities/read | 获取订阅功能 |
> | 行动 | Microsoft.HDInsight/locations/checkNameAvailability/read | 检查名称可用性 |
> | 行动 | Microsoft HDInsight/注册/操作 | 为订阅注册 HDInsight 资源提供程序 |
> | 行动 | Microsoft HDInsight/注销/操作 | 为订阅注销 HDInsight 资源提供程序 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.ImportExport/jobs/delete | 删除现有的作业。 |
> | 行动 | Microsoft.ImportExport/jobs/listBitLockerKeys/action | 获取指定作业的 BitLocker 密钥。 |
> | 行动 | Microsoft.ImportExport/jobs/read | 获取指定作业的属性，或返回作业列表。 |
> | 行动 | Microsoft.ImportExport/jobs/write | 使用指定的参数创建作业，或更新指定作业的属性或标记。 |
> | 行动 | Microsoft.ImportExport/locations/read | 获取指定位置的属性，或返回位置列表。 |
> | 行动 | ImportExport/操作/读取 | 获取资源提供程序支持的操作。 |
> | 行动 | Microsoft.ImportExport/register/action | 注册导入/导出资源提供程序的订阅，并启用导入/导出作业的创建。 |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Insights/ActionGroups/Delete | 删除操作组 |
> | 行动 | Microsoft.Insights/ActionGroups/Read | 读取操作组 |
> | 行动 | Microsoft.Insights/ActionGroups/Write | 创建或更新操作组 |
> | 行动 | Microsoft.Insights/ActivityLogAlerts/Activated/Action | 活动日志警报已激活 |
> | 行动 | Microsoft.Insights/ActivityLogAlerts/Delete | 删除活动日志警报 |
> | 行动 | Microsoft.Insights/ActivityLogAlerts/Read | 读取活动日志警报 |
> | 行动 | Microsoft.Insights/ActivityLogAlerts/Write | 创建或更新活动日志警报 |
> | 行动 | Microsoft.Insights/AlertRules/Activated/Action | 经典指标警报已激活 |
> | 行动 | Microsoft.Insights/AlertRules/Delete | 删除经典指标警报 |
> | 行动 | Microsoft.Insights/AlertRules/Incidents/Read | 读取经典指标警报事件 |
> | 行动 | Microsoft.Insights/AlertRules/Read | 读取经典指标警报 |
> | 行动 | Microsoft.Insights/AlertRules/Resolved/Action | 经典指标警报已解决 |
> | 行动 | Microsoft.Insights/AlertRules/Throttled/Action | 经典指标预警规则已中止 |
> | 行动 | Microsoft.Insights/AlertRules/Write | 创建或更新经典指标警报 |
> | 行动 | Microsoft.Insights/AutoscaleSettings/Delete | 删除自动缩放设置 |
> | 行动 | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | 读取资源诊断设置 |
> | 行动 | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Write | 创建或更新资源诊断设置 |
> | 行动 | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | 读取日志定义 |
> | 行动 | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | 读取指标定义 |
> | 行动 | Microsoft.Insights/AutoscaleSettings/Read | 读取自动缩放设置 |
> | 行动 | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | 自动缩放缩减已启动 |
> | 行动 | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | 自动缩放缩减已完成 |
> | 行动 | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | 自动缩放纵向扩展已启动 |
> | 行动 | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | 自动缩放纵向扩展已完成 |
> | 行动 | Microsoft.Insights/AutoscaleSettings/Write | 创建或更新自动缩放设置 |
> | 行动 | Microsoft Insights/基线/读取 | 读取指标基线（预览） |
> | 行动 | Microsoft Insights/CalculateBaseline/Read | 计算指标值（预览版）的基线 |
> | 行动 | Microsoft.Insights/Components/AnalyticsItems/Delete | 添加 Application Insights 分析项 |
> | 行动 | Microsoft.Insights/Components/AnalyticsItems/Read | 读取 Application Insights 分析项 |
> | 行动 | Microsoft.Insights/Components/AnalyticsItems/Write | 写入 Application Insights 分析项 |
> | 行动 | Microsoft.Insights/Components/AnalyticsTables/Action | Application Insights 分析表操作 |
> | 行动 | Microsoft.Insights/Components/AnalyticsTables/Delete | 删除 Application Insights 分析表架构 |
> | 行动 | Microsoft.Insights/Components/AnalyticsTables/Read | 读取 Application Insights 分析表架构 |
> | 行动 | Microsoft.Insights/Components/AnalyticsTables/Write | 写入 Application Insights 分析表架构 |
> | 行动 | Microsoft.Insights/Components/Annotations/Delete | 删除 Application Insights 注释 |
> | 行动 | Microsoft.Insights/Components/Annotations/Read | 读取 Application Insights 注释 |
> | 行动 | Microsoft.Insights/Components/Annotations/Write | 写入 Application Insights 注释 |
> | 行动 | Microsoft.Insights/Components/Api/Read | 读取 Application Insights 组件数据 API |
> | 行动 | Microsoft.Insights/Components/ApiKeys/Action | 生成 Application Insights API 密钥 |
> | 行动 | Microsoft.Insights/Components/ApiKeys/Delete | 删除 Application Insights API 密钥 |
> | 行动 | Microsoft.Insights/Components/ApiKeys/Read | 读取 Application Insights API 密钥 |
> | 行动 | Microsoft.Insights/Components/BillingPlanForComponent/Read | 读取 Application Insights 组件的计费计划 |
> | 行动 | Microsoft.Insights/Components/CurrentBillingFeatures/Read | 读取 Application Insights 组件的当前计费计划 |
> | 行动 | Microsoft.Insights/Components/CurrentBillingFeatures/Write | 写入 Application Insights 组件的当前计费计划 |
> | 行动 | Microsoft Insights/Components/DailyCapReached/Action | 已达到 Application Insights 组件的每日上限 |
> | 行动 | Microsoft Insights/Components/DailyCapWarningThresholdReached/Action | 已达到 Application Insights 组件的每日上限警告阈值 |
> | 行动 | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | 读取 Application Insights 默认 ALM 集成配置 |
> | 行动 | Microsoft.Insights/Components/Delete | 删除 Application Insights 组件配置 |
> | 行动 | Microsoft.Insights/Components/Events/Read | 使用 OData 查询格式从 Application Insights 获取日志 |
> | 行动 | Microsoft.Insights/Components/ExportConfiguration/Action | Application Insights 导出设置操作 |
> | 行动 | Microsoft.Insights/Components/ExportConfiguration/Delete | 删除 Application Insights 导出设置 |
> | 行动 | Microsoft.Insights/Components/ExportConfiguration/Read | 读取 Application Insights 导出设置 |
> | 行动 | Microsoft.Insights/Components/ExportConfiguration/Write | 写入 Application Insights 导出设置 |
> | 行动 | Microsoft.Insights/Components/ExtendQueries/Read | 读取 Application Insights 组件扩展查询结果 |
> | 行动 | Microsoft.Insights/Components/Favorites/Delete | 删除 Application Insights 收藏夹 |
> | 行动 | Microsoft.Insights/Components/Favorites/Read | 读取 Application Insights 收藏夹 |
> | 行动 | Microsoft.Insights/Components/Favorites/Write | 写入 Application Insights 收藏夹 |
> | 行动 | Microsoft.Insights/Components/FeatureCapabilities/Read | 读取 Application Insights 组件功能 |
> | 行动 | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | 读取 Application Insights 组件可用计费功能 |
> | 行动 | Microsoft.Insights/Components/GetToken/Read | 读取 Application Insights 组件令牌 |
> | 行动 | Microsoft.Insights/Components/MetricDefinitions/Read | 读取 Application Insights 组件指标定义 |
> | 行动 | Microsoft.Insights/Components/Metrics/Read | 读取 Application Insights 组件指标 |
> | 行动 | Microsoft.Insights/Components/Move/Action | 将 Application Insights 组件移动到另一个资源组或订阅 |
> | 行动 | Microsoft.Insights/Components/MyAnalyticsItems/Delete | 删除 Application Insights 个人分析项 |
> | 行动 | Microsoft.Insights/Components/MyAnalyticsItems/Read | 读取 Application Insights 个人分析项 |
> | 行动 | Microsoft.Insights/Components/MyAnalyticsItems/Write | 写入 Application Insights 个人分析项 |
> | 行动 | Microsoft.Insights/Components/MyFavorites/Read | 读取 Application Insights 个人收藏夹 |
> | 行动 | Microsoft.Insights/Components/Operations/Read | 在 Application Insights 中获取长时间运行的操作的状态 |
> | 行动 | Microsoft.Insights/Components/PricingPlans/Read | 读取 Application Insights 组件定价计划 |
> | 行动 | Microsoft.Insights/Components/PricingPlans/Write | 写入 Application Insights 组件定价计划 |
> | 行动 | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | 读取 Application Insights 主动检测配置 |
> | 行动 | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | 写入 Application Insights 主动检测配置 |
> | 行动 | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | 读取指标定义 |
> | 行动 | Microsoft.Insights/Components/Purge/Action | 从 Application Insights 清除数据 |
> | 行动 | Microsoft.Insights/Components/Query/Read | 对 Application Insights 日志运行查询 |
> | 行动 | Microsoft.Insights/Components/QuotaStatus/Read | 读取 Application Insights 组件配额状态 |
> | 行动 | Microsoft.Insights/Components/Read | 读取 Application Insights 组件配置 |
> | 行动 | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | 读取 Application Insights webtest 位置 |
> | 行动 | Microsoft.Insights/Components/Webtests/Read | 读取 webtest 配置 |
> | 行动 | Microsoft.Insights/Components/WorkItemConfigs/Delete | 删除 Application Insights ALM 集成配置 |
> | 行动 | Microsoft.Insights/Components/WorkItemConfigs/Read | 读取 Application Insights ALM 集成配置 |
> | 行动 | Microsoft.Insights/Components/WorkItemConfigs/Write | 写入 Application Insights ALM 集成配置 |
> | 行动 | Microsoft.Insights/Components/Write | 写入到 Application Insights 组件配置 |
> | 行动 | Microsoft.Insights/DiagnosticSettings/Delete | 删除资源诊断设置 |
> | 行动 | Microsoft.Insights/DiagnosticSettings/Read | 读取资源诊断设置 |
> | 行动 | Microsoft.Insights/DiagnosticSettings/Write | 创建或更新资源诊断设置 |
> | 行动 | Microsoft.Insights/EventCategories/Read | 读取可用的活动日志事件类别 |
> | 行动 | Microsoft.Insights/eventtypes/digestevents/Read | 读取管理事件类型摘要 |
> | 行动 | Microsoft.Insights/eventtypes/values/Read | 读取活动日志事件 |
> | 行动 | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | 删除网络流日志诊断设置 |
> | 行动 | Microsoft.Insights/ExtendedDiagnosticSettings/Read | 读取网络流日志诊断设置 |
> | 行动 | Microsoft.Insights/ExtendedDiagnosticSettings/Write | 创建或更新网络流日志诊断设置 |
> | 行动 | Microsoft.Insights/ListMigrationDate/Action | 返回订阅迁移日期 |
> | 行动 | Microsoft.Insights/ListMigrationDate/Read | 返回订阅迁移日期 |
> | 行动 | Microsoft.Insights/LogDefinitions/Read | 读取日志定义 |
> | 行动 | Microsoft.Insights/LogProfiles/Delete | 删除活动日志的日志配置文件 |
> | 行动 | Microsoft.Insights/LogProfiles/Read | 读取活动日志的日志配置文件 |
> | 行动 | Microsoft.Insights/LogProfiles/Write | 创建或更新活动日志的日志配置文件 |
> | 行动 | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | 从 ADAssessmentRecommendation 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/ADReplicationResult/Read | 从 ADReplicationResult 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | 从 ADSecurityAssessmentRecommendation 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/Alert/Read | 从 Alert 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/AlertHistory/Read | 从 AlertHistory 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/ApplicationInsights/Read | 从 ApplicationInsights 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/AzureActivity/Read | 从 AzureActivity 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/AzureMetrics/Read | 从 AzureMetrics 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/BoundPort/Read | 从 BoundPort 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/CommonSecurityLog/Read | 从 CommonSecurityLog 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/ComputerGroup/Read | 从 ComputerGroup 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/ConfigurationChange/Read | 从 ConfigurationChange 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/ConfigurationData/Read | 从 ConfigurationData 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/ContainerImageInventory/Read | 从 ContainerImageInventory 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/ContainerInventory/Read | 从 ContainerInventory 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/ContainerLog/Read | 从 ContainerLog 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/ContainerServiceLog/Read | 从 ContainerServiceLog 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/DeviceAppCrash/Read | 从 DeviceAppCrash 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/DeviceAppLaunch/Read | 从 DeviceAppLaunch 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/DeviceCalendar/Read | 从 DeviceCalendar 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/DeviceCleanup/Read | 从 DeviceCleanup 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/DeviceConnectSession/Read | 从 DeviceConnectSession 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/DeviceEtw/Read | 从 DeviceEtw 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | 从 DeviceHardwareHealth 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/DeviceHealth/Read | 从 DeviceHealth 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/DeviceHeartbeat/Read | 从 DeviceHeartbeat 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | 从 DeviceSkypeHeartbeat 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | 从 DeviceSkypeSignIn 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/DeviceSleepState/Read | 从 DeviceSleepState 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/DHAppFailure/Read | 从 DHAppFailure 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/DHAppReliability/Read | 从 DHAppReliability 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/DHDriverReliability/Read | 从 DHDriverReliability 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/DHLogonFailures/Read | 从 DHLogonFailures 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/DHLogonMetrics/Read | 从 DHLogonMetrics 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/DHOSCrashData/Read | 从 DHOSCrashData 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/DHOSReliability/Read | 从 DHOSReliability 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/DHWipAppLearning/Read | 从 DHWipAppLearning 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/DnsEvents/Read | 从 DnsEvents 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/DnsInventory/Read | 从 DnsInventory 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/ETWEvent/Read | 从 ETWEvent 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/Event/Read | 从 Event 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | 从 ExchangeAssessmentRecommendation 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | 从 ExchangeOnlineAssessmentRecommendation 表中读取数据 |
> | 行动 | Microsoft.Insights/Logs/Heartbeat/Read | 从 Heartbeat 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | 从 IISAssessmentRecommendation 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/InboundConnection/Read | 从 InboundConnection 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/KubeNodeInventory/Read | 从 KubeNodeInventory 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/KubePodInventory/Read | 从 KubePodInventory 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/LinuxAuditLog/Read | 从 LinuxAuditLog 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAApplication/Read | 从 MAApplication 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAApplicationHealth/Read | 从 MAApplicationHealth 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | 从 MAApplicationHealthAlternativeVersions 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAApplicationHealthIssues/Read | 从 MAApplicationHealthIssues 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAApplicationInstance/Read | 从 MAApplicationInstance 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | 从 MAApplicationInstanceReadiness 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAApplicationReadiness/Read | 从 MAApplicationReadiness 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MADeploymentPlan/Read | 从 MADeploymentPlan 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MADevice/Read | 从 MADevice 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MADevicePnPHealth/Read | 从 MADevicePnPHealth 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | 从 MADevicePnPHealthAlternativeVersions 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | 从 MADevicePnPHealthIssues 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MADeviceReadiness/Read | 从 MADeviceReadiness 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | 从 MADriverInstanceReadiness 表中读取数据 |
> | 行动 | Microsoft.Insights/Logs/MADriverReadiness/Read | 从 MADriverReadiness 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAOfficeAddin/Read | 从 MAOfficeAddin 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | 从 MAOfficeAddinHealth 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | 从 MAOfficeAddinHealthIssues 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAOfficeAddinInstance/Read | 从 MAOfficeAddinInstance 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | 从 MAOfficeAddinInstanceReadiness 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAOfficeAddinReadiness/Read | 从 MAOfficeAddinReadiness 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAOfficeApp/Read | 从 MAOfficeApp 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | 从 MAOfficeAppHealth 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAOfficeAppInstance/Read | 从 MAOfficeAppInstance 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | 从 MAOfficeAppReadiness 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | 从 MAOfficeBuildInfo 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAOfficeCurrencyAssessment/Read | 从 MAOfficeCurrencyAssessment 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | 从 MAOfficeCurrencyAssessmentDailyCounts 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | 从 MAOfficeDeploymentStatus 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | 从 MAOfficeMacroHealth 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAOfficeMacroHealthIssues/Read | 从 MAOfficeMacroHealthIssues 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | 从 MAOfficeMacroIssueInstanceReadiness 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAOfficeMacroIssueReadiness/Read | 从 MAOfficeMacroIssueReadiness 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAOfficeMacroSummary/Read | 从 MAOfficeMacroSummary 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAOfficeSuite/Read | 从 MAOfficeSuite 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | 从 MAOfficeSuiteInstance 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | 从 MAProposedPilotDevices 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | 从 MAWindowsBuildInfo 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | 从 MAWindowsCurrencyAssessment 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | 从 MAWindowsCurrencyAssessmentDailyCounts 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | 从 MAWindowsDeploymentStatus 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | 从 MAWindowsSysReqInstanceReadiness 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/NetworkMonitoring/Read | 从 NetworkMonitoring 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/OfficeActivity/Read | 从 OfficeActivity 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/Operation/Read | 从 Operation 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/OutboundConnection/Read | 从 OutboundConnection 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/Perf/Read | 从 Perf 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/ProtectionStatus/Read | 从 ProtectionStatus 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/Read | 从所有日志中读取数据 |
> | 行动 | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | 从 ReservedAzureCommonFields 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/ReservedCommonFields/Read | 从 ReservedCommonFields 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | 从 SCCMAssessmentRecommendation 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/SCOMAssessmentRecommendation/Read | 从 SCOMAssessmentRecommendation 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/SecurityAlert/Read | 从 SecurityAlert 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/SecurityBaseline/Read | 从 SecurityBaseline 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | 从 SecurityBaselineSummary 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/SecurityDetection/Read | 从 SecurityDetection 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/SecurityEvent/Read | 从 SecurityEvent 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | 从 ServiceFabricOperationalEvent 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | 从 ServiceFabricReliableActorEvent 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | 从 ServiceFabricReliableServiceEvent 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | 从 SfBAssessmentRecommendation 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | 从 SfBOnlineAssessmentRecommendation 表中读取数据 |
> | 行动 | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | 从 SharePointOnlineAssessmentRecommendation 表中读取数据 |
> | 行动 | Microsoft.Insights/Logs/SPAssessmentRecommendation/Read | 从 SPAssessmentRecommendation 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | 从 SQLAssessmentRecommendation 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/SQLQueryPerformance/Read | 从 SQLQueryPerformance 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/Syslog/Read | 从 Syslog 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/SysmonEvent/Read | 从 SysmonEvent 表读取数据 |
> | 行动 | Microsoft Insights/日志/表。自定义/读取 | 从任何自定义日志读取数据 |
> | 行动 | Microsoft.Insights/Logs/UAApp/Read | 从 UAApp 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/UAComputer/Read | 从 UAComputer 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/UAComputerRank/Read | 从 UAComputerRank 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/UADriver/Read | 从 UADriver 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/UADriverProblemCodes/Read | 从 UADriverProblemCodes 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/UAFeedback/Read | 从 UAFeedback 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/UAHardwareSecurity/Read | 从 UAHardwareSecurity 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/UAIESiteDiscovery/Read | 从 UAIESiteDiscovery 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/UAOfficeAddIn/Read | 从 UAOfficeAddIn 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/UAProposedActionPlan/Read | 从 UAProposedActionPlan 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/UASysReqIssue/Read | 从 UASysReqIssue 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/UAUpgradedComputer/Read | 从 UAUpgradedComputer 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/Update/Read | 从 Update 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/UpdateRunProgress/Read | 从 UpdateRunProgress 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/UpdateSummary/Read | 从 UpdateSummary 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/Usage/Read | 从 Usage 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/W3CIISLog/Read | 从 W3CIISLog 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | 从 WaaSDeploymentStatus 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | 从 WaaSInsiderStatus 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | 从 WaaSUpdateStatus 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/WDAVStatus/Read | 从 WDAVStatus 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/WDAVThreat/Read | 从 WDAVThreat 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | 从 WindowsClientAssessmentRecommendation 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/WindowsFirewall/Read | 从 WindowsFirewall 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | 从 WindowsServerAssessmentRecommendation 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/WireData/Read | 从 WireData 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/WUDOAggregatedStatus/Read | 从 WUDOAggregatedStatus 表读取数据 |
> | 行动 | Microsoft.Insights/Logs/WUDOStatus/Read | 从 WUDOStatus 表读取数据 |
> | 行动 | Microsoft.Insights/MetricAlerts/Delete | 删除指标警报 |
> | 行动 | Microsoft.Insights/MetricAlerts/Read | 读取指标警报 |
> | 行动 | Microsoft.Insights/MetricAlerts/Status/Read | 读取指标警报状态 |
> | 行动 | Microsoft.Insights/MetricAlerts/Write | 创建或更新指标警报 |
> | 行动 | Microsoft Insights/MetricBaselines/Read | 读取指标基线 |
> | 行动 | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | 读取指标定义 |
> | 行动 | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | 读取指标定义 |
> | 行动 | Microsoft.Insights/MetricDefinitions/Read | 读取指标定义 |
> | 行动 | Microsoft Insights/Metricnamespaces/Read | 读取指标命名空间 |
> | 行动 | Microsoft.Insights/Metrics/Action | 指标操作 |
> | 行动 | Microsoft Insights/指标/Microsoft Insights/读取 | 添加指标 |
> | 行动 | Microsoft.Insights/Metrics/providers/Metrics/Read | 添加指标 |
> | 行动 | Microsoft.Insights/Metrics/Read | 添加指标 |
> | DataAction | Microsoft.Insights/Metrics/Write | 写入指标 |
> | 行动 | Microsoft.Insights/MigrateToNewpricingModel/Action | 将订阅迁移到新的定价模型 |
> | 行动 | Microsoft.Insights/Operations/Read | 读取操作 |
> | 行动 | Microsoft.Insights/Register/Action | 注册 Microsoft Insights 提供程序 |
> | 行动 | Microsoft.Insights/RollbackToLegacyPricingModel/Action | 将订阅回滚到旧定价模型 |
> | 行动 | Microsoft.Insights/ScheduledQueryRules/Delete | 删除计划的查询规则 |
> | 行动 | Microsoft.Insights/ScheduledQueryRules/Read | 读取计划的查询规则 |
> | 行动 | Microsoft.Insights/ScheduledQueryRules/Write | 写入计划的查询规则 |
> | 行动 | Microsoft.Insights/Tenants/Register/Action | 初始化 Microsoft Insights 提供程序 |
> | 行动 | Microsoft.Insights/Unregister/Action | 注册 Microsoft Insights 提供程序 |
> | 行动 | Microsoft.Insights/Webtests/Delete | 删除 webtest 配置 |
> | 行动 | Microsoft.Insights/Webtests/GetToken/Read | 读取 webtest 令牌 |
> | 行动 | Microsoft.Insights/Webtests/MetricDefinitions/Read | 读取 webtest 指标定义 |
> | 行动 | Microsoft.Insights/Webtests/Metrics/Read | 读取 webtest 指标 |
> | 行动 | Microsoft.Insights/Webtests/Read | 读取 webtest 配置 |
> | 行动 | Microsoft.Insights/Webtests/Write | 写入到 webtest 配置 |
> | 行动 | Microsoft Insights/工作簿/删除 | 删除工作簿 |
> | 行动 | Microsoft Insights/工作簿/读取 | 读取工作簿 |
> | 行动 | Microsoft Insights/工作簿/写入 | 创建或更新工作簿 |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Intune/diagnosticsettings/delete | 删除诊断设置 |
> | 行动 | Microsoft.Intune/diagnosticsettings/read | 读取诊断设置 |
> | 行动 | Microsoft.Intune/diagnosticsettings/write | 写入诊断设置 |
> | 行动 | Microsoft.Intune/diagnosticsettingscategories/read | 读取诊断设置类别 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | IoTCentral/appTemplates/action | 获取 Azure 上的所有可用应用程序模板 IoT Central |
> | 行动 | Microsoft.IoTCentral/checkNameAvailability/action | 检查 IoT Central 应用程序名称是否可用 |
> | 行动 | Microsoft.IoTCentral/checkSubdomainAvailability/action | 检查 IoT Central 应用程序子域是否可用 |
> | 行动 | Microsoft.IoTCentral/IoTApps/delete | 删除 IoT Central 应用程序 |
> | 行动 | Microsoft.IoTCentral/IoTApps/read | 获取单个 IoT Central 应用程序 |
> | 行动 | Microsoft.IoTCentral/IoTApps/write | 创建或更新 IoT Central 应用程序 |
> | 行动 | Microsoft.IoTCentral/operations/read | 获取可针对 IoT Central 应用程序执行的所有操作 |
> | 行动 | Microsoft.IoTCentral/register/action | 注册 Azure IoT Central 资源提供程序的订阅 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.IoTSpaces/Graph/delete | 删除 Microsoft.IoTSpaces Graph 资源 |
> | 行动 | Microsoft.IoTSpaces/Graph/read | 获取 Microsoft.IoTSpaces Graph 资源 |
> | 行动 | Microsoft.IoTSpaces/Graph/write | 创建 Microsoft.IoTSpaces Graph 资源 |
> | 行动 | Microsoft.IoTSpaces/register/action | 注册 Microsoft.IoTSpaces Graph 资源提供程序的订阅，以便能够创建资源 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.KeyVault/checkNameAvailability/read | 检查 Key Vault 名称是否有效且未被使用 |
> | 行动 | Microsoft.KeyVault/deletedVaults/read | 查看软删除的 Key Vault 的属性 |
> | 行动 | Microsoft.KeyVault/hsmPools/delete | 删除 HSM 池 |
> | 行动 | Microsoft.KeyVault/hsmPools/joinVault/action | 将密钥保管库加入 HSM 池 |
> | 行动 | Microsoft.KeyVault/hsmPools/read | 查看 HSM 池的属性 |
> | 行动 | Microsoft.KeyVault/hsmPools/write | 创建新 HSM 池或更新现有 HSM 池的属性 |
> | 行动 | Microsoft.KeyVault/locations/deletedVaults/purge/action | 清除软删除的 Key Vault |
> | 行动 | Microsoft.KeyVault/locations/deletedVaults/read | 查看软删除的 Key Vault 的属性 |
> | 行动 | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | 通知 Microsoft.KeyVault 正在删除虚拟网络或子网 |
> | 行动 | Microsoft.KeyVault/locations/operationResults/read | 检查长时间运行的操作的结果 |
> | 行动 | Microsoft.KeyVault/operations/read | 列出可对 Microsoft.KeyVault 资源提供程序执行的操作 |
> | 行动 | Microsoft.KeyVault/register/action | 注册订阅 |
> | 行动 | Microsoft.KeyVault/unregister/action | 取消注册订阅 |
> | 行动 | Microsoft.KeyVault/vaults/accessPolicies/write | 通过合并或替换操作更新现有的访问策略，或者将新的访问策略添加到保管库。 |
> | 行动 | Microsoft.KeyVault/vaults/delete | 删除 Key Vault |
> | 行动 | Microsoft.KeyVault/vaults/deploy/action | 部署 Azure 资源时启用对 Key Vault 中的机密的访问 |
> | 行动 | KeyVault/保管库/eventGridFilters/delete | 通知 KeyVault 正在删除 Key Vault 的 EventGrid 订阅 |
> | 行动 | KeyVault/保管库/eventGridFilters/read | 通知 KeyVault 正在查看 Key Vault 的 EventGrid 订阅 |
> | 行动 | KeyVault/保管库/eventGridFilters/write | 通知 KeyVault 正在创建新的 EventGrid 订阅 Key Vault |
> | 行动 | Microsoft.KeyVault/vaults/read | 查看 Key Vault 的属性 |
> | 行动 | Microsoft.KeyVault/vaults/secrets/read | 查看机密的属性，但不查看其值。 |
> | 行动 | Microsoft.KeyVault/vaults/secrets/write | 创建新的机密，或更新现有机密的值。 |
> | 行动 | Microsoft.KeyVault/vaults/write | 创建新的 Key Vault，或更新现有 Key Vault 的属性 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Kusto/群集/激活/操作 | 启动群集。 |
> | 行动 | Kusto/集群/AttachedDatabaseConfigurations/delete | 删除附加的数据库配置 resourceCopy。 |
> | 行动 | Kusto/集群/AttachedDatabaseConfigurations/read | 读取附加的数据库配置 resourceCopy。 |
> | 行动 | Kusto/群集/AttachedDatabaseConfigurations/write | 写入附加的数据库配置 resourceCopy。 |
> | 行动 | Kusto/集群/CheckNameAvailability/action | 检查群集名称的可用性。 |
> | 行动 | Kusto/群集/数据库/AddPrincipals/action | 添加数据库主体。 |
> | 行动 | Kusto/群集/数据库/CheckNameAvailability/action | 检查给定类型的名称可用性。 |
> | 行动 | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | 删除数据连接 resourceCopy。 |
> | 行动 | Microsoft.Kusto/Clusters/Databases/DataConnections/read | 读取数据连接 resourceCopy。 |
> | 行动 | Microsoft.Kusto/Clusters/Databases/DataConnections/write | 写入数据连接 resourceCopy。 |
> | 行动 | Kusto/群集/数据库/DataConnectionValidation/action | 验证数据库数据连接。 |
> | 行动 | Microsoft.Kusto/Clusters/Databases/delete | 删除数据库 resourceCopy。 |
> | 行动 | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | 删除事件中心连接 resourceCopy。 |
> | 行动 | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | 读取事件中心连接 resourceCopy。 |
> | 行动 | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | 写入事件中心连接 resourceCopy。 |
> | 行动 | Kusto/群集/数据库/EventHubConnectionValidation/action | 验证数据库事件中心连接。 |
> | 行动 | Kusto/群集/数据库/ListPrincipals/action | 列出数据库主体。 |
> | 行动 | Microsoft.Kusto/Clusters/Databases/read | 读取数据库 resourceCopy。 |
> | 行动 | Kusto/群集/数据库/RemovePrincipals/action | 删除数据库主体。 |
> | 行动 | Microsoft.Kusto/Clusters/Databases/write | 写入数据库 resourceCopy。 |
> | 行动 | Kusto/群集/停用/操作 | 停止群集。 |
> | 行动 | Microsoft.Kusto/Clusters/delete | 删除群集 resourceCopy。 |
> | 行动 | Microsoft.Kusto/Clusters/read | 读取群集 resourceCopy。 |
> | 行动 | Kusto/群集/Sku/读取 | 读取群集 SKU resourceCopy。 |
> | 行动 | Kusto/群集/启动/操作 | 启动群集。 |
> | 行动 | Kusto/群集/停止/操作 | 停止群集。 |
> | 行动 | Microsoft.Kusto/Clusters/write | 写入群集 resourceCopy。 |
> | 行动 | Kusto/DetachFollowerDatabases/action | 分离用的数据库。 |
> | 行动 | Kusto/ListFollowerDatabases/action | 列出了该从中的数据库。 |
> | 行动 | Kusto/位置/CheckNameAvailability/action | 检查 resourceCopy 名称可用性。 |
> | 行动 | Microsoft.Kusto/locations/operationresults/read | 读取操作 resourceCopys |
> | 行动 | Microsoft.Kusto/Operations/read | 读取操作 resourceCopys |
> | 行动 | Kusto/注册/操作 | 订阅注册操作 |
> | 行动 | Kusto/注册/操作 | 向 Kusto 资源提供程序注册订阅。 |
> | 行动 | Kusto/Sku/读取 | 读取 SKU resourceCopy。 |
> | 行动 | Kusto/注销/操作 | 取消注册 Kusto 资源提供程序的订阅。 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.LabServices/labAccounts/CreateLab/action | 在实验室帐户中创建实验室。 |
> | 行动 | Microsoft.LabServices/labAccounts/delete | 删除实验室帐户。 |
> | 行动 | Microsoft.LabServices/labAccounts/galleryImages/delete | 删除库映像。 |
> | 行动 | Microsoft.LabServices/labAccounts/galleryImages/read | 读取库映像。 |
> | 行动 | Microsoft.LabServices/labAccounts/galleryImages/write | 添加或修改库映像。 |
> | 行动 | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | 获取实验室帐户下配置的每个大小类别的区域可用性信息 |
> | 行动 | Microsoft.LabServices/labAccounts/labs/AddUsers/action | 将用户添加到实验室 |
> | 行动 | Microsoft.LabServices/labAccounts/labs/delete | 删除实验室。 |
> | 行动 | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | 删除环境设置。 |
> | 行动 | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | 删除环境。 |
> | 行动 | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | 读取环境。 |
> | 行动 | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | 在环境中重置用户密码 |
> | 行动 | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | 通过启动环境内的所有资源来启动环境。 |
> | 行动 | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | 通过停止环境内的所有资源来停止环境。 |
> | 行动 | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | 添加或修改环境。 |
> | 行动 | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | 基于实验室/环境设置的当前状态设置/取消设置环境设置的所需资源。 |
> | 行动 | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | 读取环境设置。 |
> | 行动 | LabServices/labAccounts/labs/Cloudtask.environmentsettings/ResetPassword/action | 重置模板虚拟机上的密码。 |
> | 行动 | LabServices/labAccounts/labs/Cloudtask.environmentsettings/SaveImage/action | 将当前模板映像保存到实验室帐户中的共享库 |
> | 行动 | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | 删除计划。 |
> | 行动 | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | 读取计划。 |
> | 行动 | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | 添加或修改计划。 |
> | 行动 | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | 通过启动模板内的所有资源来启动模板。 |
> | 行动 | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | 通过停止模板内的所有资源来停止模板。 |
> | 行动 | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | 添加或修改环境设置。 |
> | 行动 | Microsoft.LabServices/labAccounts/labs/read | 读取实验室。 |
> | 行动 | Microsoft.LabServices/labAccounts/labs/SendEmail/action | 发送包含实验室注册链接的电子邮件 |
> | 行动 | Microsoft.LabServices/labAccounts/labs/users/delete | 删除用户。 |
> | 行动 | Microsoft.LabServices/labAccounts/labs/users/read | 读取用户。 |
> | 行动 | Microsoft.LabServices/labAccounts/labs/users/write | 添加或修改用户。 |
> | 行动 | Microsoft.LabServices/labAccounts/labs/write | 添加或修改实验室。 |
> | 行动 | Microsoft.LabServices/labAccounts/read | 读取实验室帐户。 |
> | 行动 | Microsoft.LabServices/labAccounts/sharedGalleries/delete | 删除 sharedgalleries。 |
> | 行动 | Microsoft.LabServices/labAccounts/sharedGalleries/read | 读取 sharedgalleries。 |
> | 行动 | Microsoft.LabServices/labAccounts/sharedGalleries/write | 添加或修改 sharedgalleries。 |
> | 行动 | Microsoft.LabServices/labAccounts/sharedImages/delete | 删除 sharedimages。 |
> | 行动 | Microsoft.LabServices/labAccounts/sharedImages/read | 读取 sharedimages。 |
> | 行动 | Microsoft.LabServices/labAccounts/sharedImages/write | 添加或修改 sharedimages。 |
> | 行动 | Microsoft.LabServices/labAccounts/write | 添加或修改实验室帐户。 |
> | 行动 | Microsoft.LabServices/locations/operations/read | 读取操作。 |
> | 行动 | Microsoft.LabServices/register/action | 注册订阅 |
> | 行动 | Microsoft.LabServices/users/GetOperationBatchStatus/action | 获取批处理操作状态 |
> | 行动 | Microsoft.LabServices/users/GetOperationStatus/action | 获取长时间运行操作的状态 |
> | 行动 | Microsoft.LabServices/users/GetPersonalPreferences/action | 获取用户的个人首选项 |
> | 行动 | LabServices/users/ListAllEnvironments/action | 列出用户的所有环境 |
> | 行动 | Microsoft.LabServices/users/Register/action | 将用户注册到托管的实验室 |
> | 行动 | Microsoft.LabServices/users/ResetPassword/action | 在环境中重置用户密码 |
> | 行动 | Microsoft.LabServices/users/StartEnvironment/action | 通过启动环境内的所有资源来启动环境。 |
> | 行动 | Microsoft.LabServices/users/StopEnvironment/action | 通过停止环境内的所有资源来停止环境。 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Logic/integrationAccounts/agreements/delete | 删除集成帐户中的协议。 |
> | 行动 | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | 获取集成帐户中的协议内容的回调 URL。 |
> | 行动 | Microsoft.Logic/integrationAccounts/agreements/read | 读取集成帐户中的协议。 |
> | 行动 | Microsoft.Logic/integrationAccounts/agreements/write | 创建或更新集成帐户中的协议。 |
> | 行动 | Microsoft.Logic/integrationAccounts/assemblies/delete | 删除集成帐户中的程序集。 |
> | 行动 | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | 获取集成帐户中的程序集内容的回调 URL。 |
> | 行动 | Microsoft.Logic/integrationAccounts/assemblies/read | 读取集成帐户中的程序集。 |
> | 行动 | Microsoft.Logic/integrationAccounts/assemblies/write | 创建或更新集成帐户中的程序集。 |
> | 行动 | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | 删除集成帐户中的批配置。 |
> | 行动 | Microsoft.Logic/integrationAccounts/batchConfigurations/read | 读取集成帐户中的批配置。 |
> | 行动 | Microsoft.Logic/integrationAccounts/batchConfigurations/write | 创建或更新集成帐户中的批配置。 |
> | 行动 | Microsoft.Logic/integrationAccounts/certificates/delete | 删除集成帐户中的证书。 |
> | 行动 | Microsoft.Logic/integrationAccounts/certificates/read | 读取集成帐户中的证书。 |
> | 行动 | Microsoft.Logic/integrationAccounts/certificates/write | 创建或更新集成帐户中的证书。 |
> | 行动 | Microsoft.Logic/integrationAccounts/delete | 删除集成帐户。 |
> | 行动 | Microsoft.Logic/integrationAccounts/join/action | 加入集成帐户。 |
> | 行动 | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | 获取集成帐户的回调 URL。 |
> | 行动 | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | 获取密钥保管库中的密钥。 |
> | 行动 | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | 记录集成帐户中的跟踪事件。 |
> | 行动 | Microsoft.Logic/integrationAccounts/maps/delete | 删除集成帐户中的映射。 |
> | 行动 | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | 获取集成帐户中的映射内容的回调 URL。 |
> | 行动 | Microsoft.Logic/integrationAccounts/maps/read | 读取集成帐户中的映射。 |
> | 行动 | Microsoft.Logic/integrationAccounts/maps/write | 创建或更新集成帐户中的映射。 |
> | 行动 | Microsoft.Logic/integrationAccounts/partners/delete | 删除集成帐户中的合作伙伴。 |
> | 行动 | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | 获取集成帐户中的合作伙伴内容的回调 URL。 |
> | 行动 | Microsoft.Logic/integrationAccounts/partners/read | 读取集成帐户中的合作伙伴。 |
> | 行动 | Microsoft.Logic/integrationAccounts/partners/write | 创建或更新集成帐户中的合作伙伴。 |
> | 行动 | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | 读取集成帐户日志定义。 |
> | 行动 | Microsoft.Logic/integrationAccounts/read | 读取集成帐户。 |
> | 行动 | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | 再生成访问密钥机密。 |
> | 行动 | Microsoft.Logic/integrationAccounts/schemas/delete | 删除集成帐户中的架构。 |
> | 行动 | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | 获取集成帐户中的架构内容的回调 URL。 |
> | 行动 | Microsoft.Logic/integrationAccounts/schemas/read | 读取集成帐户中的架构。 |
> | 行动 | Microsoft.Logic/integrationAccounts/schemas/write | 创建或更新集成帐户中的架构。 |
> | 行动 | Microsoft.Logic/integrationAccounts/sessions/delete | 删除集成帐户中的会话。 |
> | 行动 | Microsoft.Logic/integrationAccounts/sessions/read | 读取集成帐户中的批配置。 |
> | 行动 | Microsoft.Logic/integrationAccounts/sessions/write | 创建或更新集成帐户中的会话。 |
> | 行动 | Microsoft.Logic/integrationAccounts/write | 创建或更新集成帐户。 |
> | 行动 | Microsoft.Logic/integrationServiceEnvironments/delete | 删除集成服务环境。 |
> | 行动 | Microsoft.Logic/integrationServiceEnvironments/join/action | 加入集成服务环境。 |
> | 行动 | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | 读取集成服务环境托管 API 操作。 |
> | 行动 | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | 读取集成服务环境托管 API。 |
> | 行动 | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | 读取集成服务环境操作状态。 |
> | 行动 | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | 读取集成服务环境指标定义。 |
> | 行动 | Microsoft.Logic/integrationServiceEnvironments/read | 读取集成服务环境。 |
> | 行动 | Microsoft.Logic/integrationServiceEnvironments/write | 创建或更新集成服务环境。 |
> | 行动 | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | 获取工作流建议操作组。 |
> | 行动 | Microsoft.Logic/locations/workflows/validate/action | 验证工作流。 |
> | 行动 | Microsoft.Logic/operations/read | 获取操作。 |
> | 行动 | Microsoft.Logic/register/action | 为给定的订阅注册 Microsoft.Logic 资源提供程序。 |
> | 行动 | Microsoft.Logic/workflows/accessKeys/delete | 删除访问密钥。 |
> | 行动 | Microsoft.Logic/workflows/accessKeys/list/action | 列出访问密钥机密。 |
> | 行动 | Microsoft.Logic/workflows/accessKeys/read | 读取访问密钥。 |
> | 行动 | Microsoft.Logic/workflows/accessKeys/regenerate/action | 再生成访问密钥机密。 |
> | 行动 | Microsoft.Logic/workflows/accessKeys/write | 创建或更新访问密钥。 |
> | 行动 | Microsoft.Logic/workflows/delete | 删除工作流。 |
> | 行动 | Microsoft.Logic/workflows/disable/action | 禁用工作流。 |
> | 行动 | Microsoft.Logic/workflows/enable/action | 启用工作流。 |
> | 行动 | Microsoft.Logic/workflows/listCallbackUrl/action | 获取工作流的回调 URL。 |
> | 行动 | Microsoft.Logic/workflows/listSwagger/action | 获取工作流 Swagger 定义。 |
> | 行动 | Microsoft.Logic/workflows/move/action | 将工作流从其现有订阅 ID、资源组和/或名称移到其他订阅 ID、资源组和/或名称。 |
> | 行动 | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | 读取工作流诊断设置。 |
> | 行动 | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新工作流诊断设置。 |
> | 行动 | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | 读取工作流日志定义。 |
> | 行动 | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | 读取工作流指标定义。 |
> | 行动 | Microsoft.Logic/workflows/read | 读取工作流。 |
> | 行动 | Microsoft.Logic/workflows/regenerateAccessKey/action | 再生成访问密钥机密。 |
> | 行动 | Microsoft.Logic/workflows/run/action | 启动工作流的运行。 |
> | 行动 | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | 获取工作流运行操作表达式跟踪。 |
> | 行动 | Microsoft.Logic/workflows/runs/actions/read | 读取工作流运行操作。 |
> | 行动 | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | 获取工作流运行操作重复表达式跟踪。 |
> | 行动 | Microsoft.Logic/workflows/runs/actions/repetitions/read | 读取工作流运行操作重复。 |
> | 行动 | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | 读取工作流运行重复操作请求历史记录。 |
> | 行动 | Microsoft.Logic/workflows/runs/actions/requestHistories/read | 读取工作流运行操作请求历史记录。 |
> | 行动 | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | 读取工作流运行操作范围重复。 |
> | 行动 | Microsoft.Logic/workflows/runs/cancel/action | 取消工作流的运行。 |
> | 行动 | Microsoft. 逻辑/工作流/运行/删除 | 删除工作流的运行。 |
> | 行动 | Microsoft.Logic/workflows/runs/operations/read | 读取工作流运行操作状态。 |
> | 行动 | Microsoft.Logic/workflows/runs/read | 读取工作流运行。 |
> | 行动 | Microsoft.Logic/workflows/suspend/action | 暂停工作流。 |
> | 行动 | Microsoft.Logic/workflows/triggers/histories/read | 读取触发器历史记录。 |
> | 行动 | Microsoft.Logic/workflows/triggers/histories/resubmit/action | 重新提交工作流触发器。 |
> | 行动 | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | 获取触发器的回调 URL。 |
> | 行动 | Microsoft.Logic/workflows/triggers/read | 读取触发器。 |
> | 行动 | Microsoft.Logic/workflows/triggers/reset/action | 重置触发器。 |
> | 行动 | Microsoft.Logic/workflows/triggers/run/action | 执行触发器。 |
> | 行动 | Microsoft.Logic/workflows/triggers/setState/action | 设置触发器状态。 |
> | 行动 | Microsoft.Logic/workflows/validate/action | 验证工作流。 |
> | 行动 | Microsoft.Logic/workflows/versions/read | 读取工作流版本。 |
> | 行动 | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | 获取触发器的回调 URL。 |
> | 行动 | Microsoft.Logic/workflows/write | 创建或更新工作流。 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | 移动任何机器学习承诺计划关联 |
> | 行动 | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | 读取任何机器学习承诺计划关联 |
> | 行动 | Microsoft.MachineLearning/commitmentPlans/delete | 删除任何机器学习承诺计划 |
> | 行动 | Microsoft.MachineLearning/commitmentPlans/join/action | 加入任何机器学习承诺计划 |
> | 行动 | Microsoft.MachineLearning/commitmentPlans/read | 读取任何机器学习承诺计划 |
> | 行动 | Microsoft.MachineLearning/commitmentPlans/write | 创建或更新任何机器学习承诺计划 |
> | 行动 | Microsoft.MachineLearning/locations/operationresults/read | 获取机器学习操作的结果 |
> | 行动 | Microsoft.MachineLearning/locations/operationsstatus/read | 获取正在进行的机器学习操作的状态 |
> | 行动 | Microsoft.MachineLearning/operations/read | 获取机器学习操作 |
> | 行动 | Microsoft.MachineLearning/register/action | 注册机器学习 Web 服务资源提供程序的订阅，并启用 Web 服务的创建。 |
> | 行动 | Microsoft.MachineLearning/skus/read | 获取机器学习承诺计划 SKU |
> | 行动 | Microsoft.MachineLearning/webServices/action | 为受支持的区域创建区域 Web 服务属性 |
> | 行动 | Microsoft.MachineLearning/webServices/delete | 删除任何机器学习 Web 服务 |
> | 行动 | Microsoft.MachineLearning/webServices/listkeys/read | 获取机器学习 Web 服务的密钥 |
> | 行动 | Microsoft.MachineLearning/webServices/read | 读取任何机器学习 Web 服务 |
> | 行动 | Microsoft.MachineLearning/webServices/write | 创建或更新任何机器学习 Web 服务 |
> | 行动 | Microsoft.MachineLearning/Workspaces/delete | 删除任何机器学习工作区 |
> | 行动 | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | 列出机器学习工作区的密钥 |
> | 行动 | Microsoft.MachineLearning/Workspaces/read | 读取任何机器学习工作区 |
> | 行动 | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | 重新同步针对机器学习工作区配置的存储帐户密钥 |
> | 行动 | Microsoft.MachineLearning/Workspaces/write | 创建或更新任何机器学习工作区 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | MachineLearningServices/位置/computeoperationsstatus/读取 | 获取特定计算操作的状态 |
> | 行动 | Microsoft.MachineLearningServices/locations/usages/read | 某个订阅中 aml 计算资源的使用情况报告 |
> | 行动 | Microsoft.MachineLearningServices/locations/vmsizes/read | 获取支持的 VM 大小 |
> | 行动 | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | 获取特定工作区操作的状态 |
> | 行动 | Microsoft.MachineLearningServices/register/action | 注册机器学习服务资源提供程序的订阅。 |
> | 行动 | Microsoft.MachineLearningServices/workspaces/computes/delete | 删除机器学习服务工作区中的计算资源 |
> | 行动 | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | 列出机器学习服务工作区中的计算资源的机密 |
> | 行动 | Microsoft.MachineLearningServices/workspaces/computes/listNodes/action | 列出机器学习服务工作区中的计算资源的节点 |
> | 行动 | Microsoft.MachineLearningServices/workspaces/computes/read | 获取机器学习服务工作区中的计算资源 |
> | 行动 | Microsoft.MachineLearningServices/workspaces/computes/write | 在机器学习服务工作区中创建或更新计算资源 |
> | 行动 | Microsoft.MachineLearningServices/workspaces/delete | 删除机器学习服务工作区 |
> | DataAction | MachineLearningServices/工作区/试验/删除 | 删除机器学习服务工作区中的试验 |
> | DataAction | MachineLearningServices/工作区/试验/读取 | 获取机器学习服务工作区中的试验 |
> | DataAction | MachineLearningServices/工作区/试验/写入 | 在机器学习服务工作区中创建或更新试验 |
> | 行动 | Microsoft.MachineLearningServices/workspaces/listKeys/action | 列出机器学习服务工作区的机密 |
> | 行动 | Microsoft.MachineLearningServices/workspaces/read | 获取机器学习服务工作区 |
> | 行动 | Microsoft.MachineLearningServices/workspaces/write | 创建或更新机器学习服务工作区 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | 对 microsoft.managedidentity/标识/读取 | 获取现有系统分配的标识 |
> | 行动 | Microsoft.ManagedIdentity/register/action | 注册托管标识资源提供程序的订阅 |
> | 行动 | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | RBAC 操作用于将现有用户分配标识分配给资源 |
> | 行动 | Microsoft.ManagedIdentity/userAssignedIdentities/delete | 删除现有用户分配标识 |
> | 行动 | Microsoft.ManagedIdentity/userAssignedIdentities/read | 获取现有用户分配标识 |
> | 行动 | Microsoft.ManagedIdentity/userAssignedIdentities/write | 创建新的用户分配标识或更新与现有用户分配标识关联的标记 |

## <a name="microsoftmanagedservices"></a>ManagedServices

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | ManagedServices/marketplaceRegistrationDefinitions/read | 检索托管服务注册定义的列表。 |
> | 行动 | ManagedServices/操作/读取 | 检索托管服务操作的列表。 |
> | 行动 | ManagedServices/operationStatuses/read | 读取资源的操作状态。 |
> | 行动 | ManagedServices/注册/操作 | 注册到托管服务。 |
> | 行动 | ManagedServices/registrationAssignments/delete | 删除托管服务注册分配。 |
> | 行动 | ManagedServices/registrationAssignments/read | 检索托管服务注册分配的列表。 |
> | 行动 | ManagedServices/registrationAssignments/write | 添加或修改托管服务注册分配。 |
> | 行动 | ManagedServices/registrationDefinitions/delete | 删除托管服务注册定义。 |
> | 行动 | ManagedServices/registrationDefinitions/read | 检索托管服务注册定义的列表。 |
> | 行动 | ManagedServices/registrationDefinitions/write | 添加或修改托管服务注册定义。 |
> | 行动 | ManagedServices/注销/操作 | 从托管服务中取消注册。 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Management/checkNameAvailability/action | 检查指定的管理组名称是否有效且唯一。 |
> | 行动 | Microsoft.Management/getEntities/action | 列出已通过身份验证的用户的所有实体（管理组、订阅等）。 |
> | 行动 | Microsoft.Management/managementGroups/delete | 删除管理组。 |
> | 行动 | Microsoft.Management/managementGroups/read | 列出已通过身份验证的用户的管理组。 |
> | 行动 | Microsoft.Management/managementGroups/subscriptions/delete | 从管理组取消关联订阅。 |
> | 行动 | Microsoft.Management/managementGroups/subscriptions/write | 将现有订阅与管理组关联。 |
> | 行动 | Microsoft.Management/managementGroups/write | 创建或更新管理组。 |
> | 行动 | Microsoft.Management/register/action | 向 Microsoft.Management 注册指定的订阅 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | DataAction | Microsoft.Maps/accounts/data/read | 授予对映射帐户的数据读权限。 |
> | 行动 | Microsoft.Maps/accounts/delete | 删除 Maps 帐户。 |
> | 行动 | Microsoft.Maps/accounts/eventGridFilters/delete | 删除事件网格筛选器 |
> | 行动 | Microsoft.Maps/accounts/eventGridFilters/read | 获取事件网格筛选器 |
> | 行动 | Microsoft.Maps/accounts/eventGridFilters/write | 创建或更新事件网格筛选器 |
> | 行动 | Microsoft.Maps/accounts/listKeys/action | 列出 Maps 帐户密钥 |
> | 行动 | Microsoft.Maps/accounts/read | 获取 Maps 帐户。 |
> | 行动 | Microsoft.Maps/accounts/regenerateKey/action | 生成新的 Maps 帐户主要或辅助密钥 |
> | 行动 | Microsoft.Maps/accounts/write | 创建或更新 Maps 帐户。 |
> | 行动 | Microsoft Maps/操作/读取 | 读取提供程序操作 |
> | 行动 | Microsoft.Maps/register/action | 注册提供程序 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | 返回一个协议。 |
> | 行动 | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | 接受已签名的协议。 |
> | 行动 | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | 将映像导入到最终用户的 ACR。 |
> | 行动 | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | 返回配置。 |
> | 行动 | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | 保存配置。 |
> | 行动 | Microsoft.Marketplace/register/action | 注册订阅中的 Microsoft.Marketplace 资源提供程序。 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.MarketplaceApps/ClassicDevServices/delete | 对经典开发人员服务资源执行 DELETE 操作。 |
> | 行动 | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | 获取经典开发人员服务资源管理密钥。 |
> | 行动 | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | 获取经典开发人员服务的单一登录 URL。 |
> | 行动 | Microsoft.MarketplaceApps/ClassicDevServices/read | 对经典开发人员服务执行 GET 操作。 |
> | 行动 | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | 生成经典开发人员服务资源管理密钥。 |
> | 行动 | Microsoft.MarketplaceApps/Operations/read | 读取所有资源类型的操作。 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | 取消给定市场项的协议 |
> | 行动 | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | 返回给定市场项的协议 |
> | 行动 | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | 为给定市场项的协议签名 |
> | 行动 | Microsoft.MarketplaceOrdering/agreements/read | 返回给定订阅下的所有协议 |
> | 行动 | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | 获取给定市场虚拟机项的协议 |
> | 行动 | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | 签订或取消给定市场虚拟机项的协议 |
> | 行动 | MarketplaceOrdering/操作/读取 | 列出 API 中所有可能的操作 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Media/checknameavailability/action | 检查媒体服务帐户名称是否可用 |
> | 行动 | Microsoft.Media/locations/checkNameAvailability/action | 检查媒体服务帐户名称是否可用 |
> | 行动 | Microsoft.Media/mediaservices/accountfilters/delete | 删除任何帐户筛选器 |
> | 行动 | Microsoft.Media/mediaservices/accountfilters/read | 读取任何帐户筛选器 |
> | 行动 | Microsoft.Media/mediaservices/accountfilters/write | 创建或更新任何帐户筛选器 |
> | 行动 | Microsoft.Media/mediaservices/assets/assetfilters/delete | 删除任何资产筛选器 |
> | 行动 | Microsoft.Media/mediaservices/assets/assetfilters/read | 读取任何资产筛选器 |
> | 行动 | Microsoft.Media/mediaservices/assets/assetfilters/write | 创建或更新任何资产筛选器 |
> | 行动 | Microsoft.Media/mediaservices/assets/delete | 删除任何资产 |
> | 行动 | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | 获取资产加密密钥 |
> | 行动 | Microsoft.Media/mediaservices/assets/listContainerSas/action | 列出资产容器 SAS URL |
> | 行动 | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | 列出资产的流式处理定位符 |
> | 行动 | Microsoft.Media/mediaservices/assets/read | 读取任何资产 |
> | 行动 | Microsoft.Media/mediaservices/assets/write | 创建或更新任何资产 |
> | 行动 | Microsoft.Media/mediaservices/contentKeyPolicies/delete | 删除任何内容密钥策略 |
> | 行动 | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | 获取包含机密的策略属性 |
> | 行动 | Microsoft.Media/mediaservices/contentKeyPolicies/read | 读取任何内容密钥策略 |
> | 行动 | Microsoft.Media/mediaservices/contentKeyPolicies/write | 创建或更新任何内容密钥策略 |
> | 行动 | Microsoft.Media/mediaservices/delete | 删除任何媒体服务帐户 |
> | 行动 | Microsoft.Media/mediaservices/eventGridFilters/delete | 删除任何事件网格筛选器 |
> | 行动 | Microsoft.Media/mediaservices/eventGridFilters/read | 读取任何事件网格筛选器 |
> | 行动 | Microsoft.Media/mediaservices/eventGridFilters/write | 创建或更新任何事件网格筛选器 |
> | 行动 | Microsoft.Media/mediaservices/liveEventOperations/read | 读取任何直播活动操作 |
> | 行动 | Microsoft.Media/mediaservices/liveEvents/delete | 删除任何直播活动 |
> | 行动 | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | 删除任何直播输出 |
> | 行动 | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | 读取任何直播输出 |
> | 行动 | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | 创建或更新任何直播输出 |
> | 行动 | Microsoft.Media/mediaservices/liveEvents/read | 读取任何直播活动 |
> | 行动 | Microsoft.Media/mediaservices/liveEvents/reset/action | 重置任何直播活动操作 |
> | 行动 | Microsoft.Media/mediaservices/liveEvents/start/action | 启动任何直播活动操作 |
> | 行动 | Microsoft.Media/mediaservices/liveEvents/stop/action | 停止任何直播活动操作 |
> | 行动 | Microsoft.Media/mediaservices/liveEvents/write | 创建或更新任何直播活动 |
> | 行动 | Microsoft.Media/mediaservices/liveOutputOperations/read | 读取任何直播输出操作 |
> | 行动 | Microsoft.Media/mediaservices/read | 读取任何媒体服务帐户 |
> | 行动 | Microsoft.Media/mediaservices/streamingEndpointOperations/read | 读取任何流式处理终结点操作 |
> | 行动 | Microsoft.Media/mediaservices/streamingEndpoints/delete | 删除任何流式处理终结点 |
> | 行动 | Microsoft.Media/mediaservices/streamingEndpoints/read | 读取任何流式处理终结点 |
> | 行动 | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | 缩放任何流式处理终结点操作 |
> | 行动 | Microsoft.Media/mediaservices/streamingEndpoints/start/action | 启动任何流式处理终结点操作 |
> | 行动 | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | 停止任何流式处理终结点操作 |
> | 行动 | Microsoft.Media/mediaservices/streamingEndpoints/write | 创建或更新任何流式处理终结点 |
> | 行动 | Microsoft.Media/mediaservices/streamingLocators/delete | 删除任何流式处理定位符 |
> | 行动 | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | 列出内容密钥 |
> | 行动 | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | 列出路径 |
> | 行动 | Microsoft.Media/mediaservices/streamingLocators/read | 读取任何流式处理定位符 |
> | 行动 | Microsoft.Media/mediaservices/streamingLocators/write | 创建或更新任何流式处理定位符 |
> | 行动 | Microsoft.Media/mediaservices/streamingPolicies/delete | 删除任何流式处理策略 |
> | 行动 | Microsoft.Media/mediaservices/streamingPolicies/read | 读取任何流式处理策略 |
> | 行动 | Microsoft.Media/mediaservices/streamingPolicies/write | 创建或更新任何流式处理策略 |
> | 行动 | Microsoft.Media/mediaservices/syncStorageKeys/action | 同步附加的 Azure 存储帐户的存储密钥 |
> | 行动 | Microsoft.Media/mediaservices/transforms/delete | 删除任何转换 |
> | 行动 | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | 取消作业 |
> | 行动 | Microsoft.Media/mediaservices/transforms/jobs/delete | 删除任何作业 |
> | 行动 | Microsoft.Media/mediaservices/transforms/jobs/read | 读取任何作业 |
> | 行动 | Microsoft.Media/mediaservices/transforms/jobs/write | 创建或更新任何作业 |
> | 行动 | Microsoft.Media/mediaservices/transforms/read | 读取任何转换 |
> | 行动 | Microsoft.Media/mediaservices/transforms/write | 创建或更新任何转换 |
> | 行动 | Microsoft.Media/mediaservices/write | 创建或更新任何媒体服务帐户 |
> | 行动 | Microsoft.Media/operations/read | 获取可用操作 |
> | 行动 | Microsoft.Media/register/action | 注册媒体服务资源提供程序的订阅，并启用媒体服务帐户的创建 |
> | 行动 | Microsoft.Media/unregister/action | 取消注册媒体服务资源提供程序的订阅 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Assessmentprojects/assessmentOptions/read | 获取给定位置中提供的评估选项 |
> | 行动 | Assessmentprojects/评估/读取 | 列出项目内的评估 |
> | 行动 | Assessmentprojects/删除 | 删除评估项目 |
> | 行动 | Microsoft. 迁移/assessmentprojects/组/评估/assessedmachines/读取 | 获取已评估计算机的属性 |
> | 行动 | Microsoft. 迁移/assessmentprojects/组/评估/删除 | 删除评估 |
> | 行动 | Microsoft. 迁移/assessmentprojects/组/评估/downloadurl/操作 | 下载评估报告的 URL |
> | 行动 | Microsoft. 迁移/assessmentprojects/组/评估/读取 | 获取评估的属性 |
> | 行动 | Microsoft. 迁移/assessmentprojects/组/评估/写入 | 创建新评估或更新现有评估 |
> | 行动 | Assessmentprojects/组/删除 | 删除组 |
> | 行动 | Microsoft. 迁移/assessmentprojects/组/读取 | 获取组的属性 |
> | 行动 | Assessmentprojects/groups/updateMachines/action | 通过添加或删除计算机更新组 |
> | 行动 | Microsoft. 迁移/assessmentprojects/组/写入 | 创建新组或更新现有组 |
> | 行动 | Assessmentprojects/hypervcollectors/delete | 删除 HyperV 收集器 |
> | 行动 | Assessmentprojects/hypervcollectors/read | 获取 HyperV 收集器的属性 |
> | 行动 | Assessmentprojects/hypervcollectors/write | 创建新的 HyperV 收集器或更新现有的 HyperV 收集器 |
> | 行动 | Assessmentprojects/计算机/读取 | 获取计算机的属性 |
> | 行动 | Microsoft. 迁移/assessmentprojects/读取 | 获取评估项目的属性 |
> | 行动 | Assessmentprojects/vmwarecollectors/delete | 删除 VMware 收集器 |
> | 行动 | Assessmentprojects/vmwarecollectors/read | 获取 VMware 收集器的属性 |
> | 行动 | Assessmentprojects/vmwarecollectors/write | 创建新的 VMware 收集器或更新现有的 VMware 收集器 |
> | 行动 | Assessmentprojects/写入 | 创建新的评估项目，或更新现有评估项目 |
> | 行动 | Microsoft.Migrate/locations/assessmentOptions/read | 获取给定位置中提供的评估选项 |
> | 行动 | Microsoft.Migrate/locations/checknameavailability/action | 在给定位置检查给定订阅的资源名称的可用性 |
> | 行动 | Migrateprojects/DatabaseInstances/read | 获取数据库实例的属性 |
> | 行动 | Migrateprojects/数据库/读取 | 获取数据库的属性 |
> | 行动 | Migrateprojects/删除 | 删除迁移项目 |
> | 行动 | Migrateprojects/计算机/读取 | 获取计算机的属性 |
> | 行动 | Migrateprojects/MigrateEvents/Delete | 删除迁移事件 |
> | 行动 | Migrateprojects/MigrateEvents/read | 获取迁移事件的属性。 |
> | 行动 | Microsoft.Migrate/migrateprojects/read | 获取迁移项目的属性 |
> | 行动 | Migrateprojects/RefreshSummary/action | 刷新迁移项目摘要 |
> | 行动 | Migrateprojects/registerTool/action | 将工具注册到迁移项目 |
> | 行动 | Migrateprojects/cleanupData/action | 清理迁移项目解决方案数据 |
> | 行动 | Migrateprojects/解决方案/删除 | 删除迁移项目解决方案 |
> | 行动 | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | 获取迁移项目解决方案配置 |
> | 行动 | Microsoft.Migrate/migrateprojects/solutions/read | 获取迁移项目解决方案的属性 |
> | 行动 | Microsoft. 迁移/migrateprojects/解决方案/写入 | 创建新的 "迁移项目" 解决方案或更新现有的 "迁移项目" 解决方案 |
> | 行动 | Migrateprojects/写入 | 创建新的迁移项目，或更新现有的迁移项目 |
> | 行动 | Microsoft.Migrate/Operations/read | 列出适用于 Microsoft.Migrate 资源提供程序的操作 |
> | 行动 | Microsoft.Migrate/projects/assessments/read | 列出项目内的评估 |
> | 行动 | Microsoft.Migrate/projects/delete | 删除项目 |
> | 行动 | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | 获取已评估计算机的属性 |
> | 行动 | Microsoft.Migrate/projects/groups/assessments/delete | 删除评估 |
> | 行动 | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | 下载评估报告的 URL |
> | 行动 | Microsoft.Migrate/projects/groups/assessments/read | 获取评估的属性 |
> | 行动 | Microsoft.Migrate/projects/groups/assessments/write | 创建新评估或更新现有评估 |
> | 行动 | Microsoft.Migrate/projects/groups/delete | 删除组 |
> | 行动 | Microsoft.Migrate/projects/groups/read | 获取组的属性 |
> | 行动 | Microsoft.Migrate/projects/groups/write | 创建新组或更新现有组 |
> | 行动 | Microsoft.Migrate/projects/keys/action | 获取项目的共享密钥 |
> | 行动 | Microsoft.Migrate/projects/machines/read | 获取计算机的属性 |
> | 行动 | Microsoft.Migrate/projects/read | 获取项目的属性 |
> | 行动 | Microsoft.Migrate/projects/write | 创建新项目或更新现有项目 |
> | 行动 | Microsoft.Migrate/register/action | 使用 Microsoft.Migrate 资源提供程序注册订阅 |

## <a name="microsoftmixedreality"></a>MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | MixedReality/注册/操作 | 注册混合现实资源提供程序的订阅。 |
> | DataAction | MixedReality/SpatialAnchorsAccounts/create/action | 创建空间锚 |
> | DataAction | MixedReality/SpatialAnchorsAccounts/delete | 删除空间锚 |
> | DataAction | MixedReality/SpatialAnchorsAccounts/discovery/read | 发现附近的空间锚 |
> | DataAction | MixedReality/SpatialAnchorsAccounts/properties/read | 获取空间定位点的属性 |
> | 行动 | MixedReality/spatialAnchorsAccounts/providers/diagnosticSettings/read | 获取 MixedReality/spatialAnchorsAccounts 的诊断设置 |
> | 行动 | MixedReality/spatialAnchorsAccounts/providers/diagnosticSettings/write | 创建或更新 MixedReality/spatialAnchorsAccounts 的诊断设置 |
> | 行动 | MixedReality/spatialAnchorsAccounts/providers/metricDefinitions/read | 获取 MixedReality/spatialAnchorsAccounts 的可用指标 |
> | DataAction | MixedReality/SpatialAnchorsAccounts/query/read | 定位空间锚 |
> | DataAction | MixedReality/SpatialAnchorsAccounts/submitdiag/read | 提交诊断数据以帮助改进 Azure 空间锚定服务的质量 |
> | DataAction | MixedReality/SpatialAnchorsAccounts/write | 更新空间锚属性 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Checkfilepathavailability/位置/操作 | 检查文件路径是否可用 |
> | 行动 | Checknameavailability/位置/操作 | 检查资源名称是否可用 |
> | 行动 | Microsoft.NetApp/locations/operationresults/read | 读取操作结果资源。 |
> | 行动 | Microsoft.NetApp/locations/read | 读取可用性检查资源。 |
> | 行动 | Microsoft.NetApp/netAppAccounts/capacityPools/delete | 删除池资源。 |
> | 行动 | Microsoft.NetApp/netAppAccounts/capacityPools/read | 读取池资源。 |
> | 行动 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | 删除卷资源。 |
> | 行动 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/mountTargets/read | 读取装入目标资源。 |
> | 行动 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | 读取卷资源。 |
> | 行动 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | 删除快照资源。 |
> | 行动 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | 读取快照资源。 |
> | 行动 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | 写入快照资源。 |
> | 行动 | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | 写入卷资源。 |
> | 行动 | Microsoft.NetApp/netAppAccounts/capacityPools/write | 写入池资源。 |
> | 行动 | Microsoft.NetApp/netAppAccounts/delete | 删除帐户资源。 |
> | 行动 | Microsoft.NetApp/netAppAccounts/read | 读取帐户资源。 |
> | 行动 | Microsoft.NetApp/netAppAccounts/write | 写入帐户资源。 |
> | 行动 | Microsoft.NetApp/Operations/read | 读取操作资源。 |
> | 行动 | Microsoft-NetApp/注册/操作 | 向 Microsoft NetApp 资源提供程序注册订阅 |
> | 行动 | Microsoft-NetApp/注销/操作 | 取消注册与 Microsoft 的资源提供程序的订阅 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | 获取应用程序网关的可用请求标头 |
> | 行动 | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | 获取应用程序网关的可用响应标头 |
> | 行动 | Microsoft.Network/applicationGatewayAvailableServerVariables/read | 获取应用程序网关的可用服务器变量 |
> | 行动 | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | 应用程序网关 SSL 预定义策略 |
> | 行动 | Microsoft.Network/applicationGatewayAvailableSslOptions/read | 应用程序网关可用 SSL 选项 |
> | 行动 | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | 获取应用程序网关可用的 WAF 规则集 |
> | 行动 | Microsoft.Network/applicationGateways/backendAddressPools/join/action | 加入应用程序网关后端地址池。 Not 可报警。 |
> | 行动 | Microsoft.Network/applicationGateways/backendhealth/action | 获取应用程序网关后端运行状况 |
> | 行动 | Microsoft.Network/applicationGateways/delete | 删除应用程序网关 |
> | 行动 | ApplicationGateways/getBackendHealthOnDemand/action | 获取给定 http 设置和后端池的按需应用程序网关后端运行状况 |
> | 行动 | Microsoft.Network/applicationGateways/read | 获取应用程序网关 |
> | 行动 | Microsoft.Network/applicationGateways/start/action | 启动应用程序网关 |
> | 行动 | Microsoft.Network/applicationGateways/stop/action | 停止应用程序网关 |
> | 行动 | Microsoft.Network/applicationGateways/write | 创建应用程序网关，或更新应用程序网关 |
> | 行动 | ApplicationGatewayWebApplicationFirewallPolicies/删除 | 删除应用程序网关 WAF 策略 |
> | 行动 | ApplicationGatewayWebApplicationFirewallPolicies/read | 获取应用程序网关 WAF 策略 |
> | 行动 | ApplicationGatewayWebApplicationFirewallPolicies/write | 创建应用程序网关 WAF 策略，或更新应用程序网关 WAF 策略 |
> | 行动 | Microsoft.Network/applicationSecurityGroups/delete | 删除应用程序安全组 |
> | 行动 | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | 将 IP 配置加入到应用程序安全组中。 Not 可报警。 |
> | 行动 | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | 将安全规则加入到应用程序安全组中。 Not 可报警。 |
> | 行动 | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | 列出 ApplicationSecurityGroup 中的 IP 配置 |
> | 行动 | Microsoft.Network/applicationSecurityGroups/read | 获取应用程序安全组 ID。 |
> | 行动 | Microsoft.Network/applicationSecurityGroups/write | 创建应用程序安全组，或更新现有应用程序安全组。 |
> | 行动 | Microsoft.Network/azureFirewallFqdnTags/read | 获取 Azure 防火墙 FQDN 标记 |
> | 行动 | Microsoft.Network/azurefirewalls/delete | 删除 Azure 防火墙 |
> | 行动 | Microsoft.Network/azurefirewalls/read | 获取 Azure 防火墙 |
> | 行动 | Microsoft.Network/azurefirewalls/write | 创建或更新 Azure 防火墙 |
> | 行动 | Microsoft.Network/bastionHosts/delete | 删除守护主机 |
> | 行动 | Microsoft.Network/bastionHosts/read | 获取守护主机 |
> | 行动 | Microsoft.Network/bastionHosts/write | 创建或更新守护主机 |
> | 行动 | Microsoft.Network/bgpServiceCommunities/read | 获取 BGP 服务社区 |
> | 行动 | Microsoft.Network/checkFrontDoorNameAvailability/action | 检查 Front Door 名称是否可用 |
> | 行动 | Microsoft.Network/checkTrafficManagerNameAvailability/action | 检查流量管理器相对 DNS 名称的可用性。 |
> | 行动 | Microsoft.Network/connections/delete | 删除 VirtualNetworkGatewayConnection |
> | 行动 | Microsoft.Network/connections/read | 获取 VirtualNetworkGatewayConnection |
> | 行动 | Microsoft.Network/connections/revoke/action | 将 Express Route 连接状态标记为“已撤销” |
> | 行动 | Microsoft.Network/connections/sharedkey/action | 获取 VirtualNetworkGatewayConnection SharedKey |
> | 行动 | Microsoft.Network/connections/sharedKey/read | 获取 VirtualNetworkGatewayConnection SharedKey |
> | 行动 | Microsoft.Network/connections/sharedKey/write | 创建新的或更新现有的 VirtualNetworkGatewayConnection SharedKey |
> | 行动 | Microsoft.Network/connections/vpndeviceconfigurationscript/action | 获取 VirtualNetworkGatewayConnection 的 VPN 设备配置 |
> | 行动 | Microsoft.Network/connections/write | 创建新的或更新现有的 VirtualNetworkGatewayConnection |
> | 行动 | Microsoft.Network/ddosCustomPolicies/delete | 删除 DDoS 自定义策略 |
> | 行动 | Microsoft.Network/ddosCustomPolicies/read | 获取 DDoS 自定义策略定义 |
> | 行动 | Microsoft.Network/ddosCustomPolicies/write | 创建 DDoS 自定义策略，或更新现有的 DDoS 自定义策略 |
> | 行动 | Microsoft.Network/ddosProtectionPlans/delete | 删除 DDoS 保护计划 |
> | 行动 | Microsoft.Network/ddosProtectionPlans/join/action | 加入 DDoS 防护计划。 Not 可报警。 |
> | 行动 | Microsoft.Network/ddosProtectionPlans/read | 获取 DDoS 保护计划 |
> | 行动 | Microsoft.Network/ddosProtectionPlans/write | 创建 DDoS 保护计划或更新 DDoS 保护计划  |
> | 行动 | Microsoft.Network/dnsoperationresults/read | 获取 DNS 操作的结果 |
> | 行动 | Microsoft.Network/dnsoperationstatuses/read | 获取 DNS 操作的状态  |
> | 行动 | Microsoft.Network/dnszones/A/delete | 从 DNS 区域中删除具有给定名称的、类型为“A”的记录集。 |
> | 行动 | Microsoft.Network/dnszones/A/read | 获取 JSON 格式的、“A”类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。 |
> | 行动 | Microsoft.Network/dnszones/A/write | 在 DNS 区域中创建或更新“A”类型的记录集。 指定的记录将替换记录集中的当前记录。 |
> | 行动 | Microsoft.Network/dnszones/AAAA/delete | 从 DNS 区域中删除具有给定名称的、类型为“AAAA”的记录集。 |
> | 行动 | Microsoft.Network/dnszones/AAAA/read | 获取 JSON 格式的、“AAAA”类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。 |
> | 行动 | Microsoft.Network/dnszones/AAAA/write | 在 DNS 区域中创建或更新“AAAA”类型的记录集。 指定的记录将替换记录集中的当前记录。 |
> | 行动 | Microsoft.Network/dnszones/all/read | 获取各种类型的 DNS 记录集 |
> | 行动 | Microsoft.Network/dnszones/CAA/delete | 从 DNS 区域中删除具有给定名称的、类型为“CAA”的记录集。 |
> | 行动 | Microsoft.Network/dnszones/CAA/read | 获取 JSON 格式的、“CAA”类型的记录集。 记录集包含 TTL、标记和 etag。 |
> | 行动 | Microsoft.Network/dnszones/CAA/write | 在 DNS 区域中创建或更新“CAA”类型的记录集。 指定的记录将替换记录集中的当前记录。 |
> | 行动 | Microsoft.Network/dnszones/CNAME/delete | 从 DNS 区域中删除具有给定名称的、类型为“CNAME”的记录集。 |
> | 行动 | Microsoft.Network/dnszones/CNAME/read | 获取 JSON 格式的、“CNAME”类型的记录集。 记录集包含 TTL、标记和 etag。 |
> | 行动 | Microsoft.Network/dnszones/CNAME/write | 在 DNS 区域中创建或更新“CNAME”类型的记录集。 指定的记录将替换记录集中的当前记录。 |
> | 行动 | Microsoft.Network/dnszones/delete | 删除 JSON 格式的 DNS 区域。 区域属性包括 tags、etag、numberOfRecordSets 和 maxNumberOfRecordSets。 |
> | 行动 | Microsoft.Network/dnszones/MX/delete | 从 DNS 区域中删除具有给定名称的、类型为“MX”的记录集。 |
> | 行动 | Microsoft.Network/dnszones/MX/read | 获取 JSON 格式的、“MX”类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。 |
> | 行动 | Microsoft.Network/dnszones/MX/write | 在 DNS 区域中创建或更新“MX”类型的记录集。 指定的记录将替换记录集中的当前记录。 |
> | 行动 | Microsoft.Network/dnszones/NS/delete | 删除 NS 类型的 DNS 记录集 |
> | 行动 | Microsoft.Network/dnszones/NS/read | 获取 NS 类型的 DNS 记录集 |
> | 行动 | Microsoft.Network/dnszones/NS/write | 创建或更新 NS 类型的 DNS 记录集 |
> | 行动 | Microsoft.Network/dnszones/PTR/delete | 从 DNS 区域中删除具有给定名称的、类型为“PTR”的记录集。 |
> | 行动 | Microsoft.Network/dnszones/PTR/read | 获取 JSON 格式的、“PTR”类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。 |
> | 行动 | Microsoft.Network/dnszones/PTR/write | 在 DNS 区域中创建或更新“PTR”类型的记录集。 指定的记录将替换记录集中的当前记录。 |
> | 行动 | Microsoft.Network/dnszones/read | 获取 JSON 格式的 DNS 区域。 区域属性包括 tags、etag、numberOfRecordSets 和 maxNumberOfRecordSets。 请注意，此命令不会检索区域中包含的记录集。 |
> | 行动 | Microsoft.Network/dnszones/recordsets/read | 获取各种类型的 DNS 记录集 |
> | 行动 | Microsoft.Network/dnszones/SOA/read | 获取 SOA 类型的 DNS 记录集 |
> | 行动 | Microsoft.Network/dnszones/SOA/write | 创建或更新 SOA 类型的 DNS 记录集 |
> | 行动 | Microsoft.Network/dnszones/SRV/delete | 从 DNS 区域中删除具有给定名称的、类型为“SRV”的记录集。 |
> | 行动 | Microsoft.Network/dnszones/SRV/read | 获取 JSON 格式的、“SRV”类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。 |
> | 行动 | Microsoft.Network/dnszones/SRV/write | 创建或更新 SRV 类型的记录集 |
> | 行动 | Microsoft.Network/dnszones/TXT/delete | 从 DNS 区域中删除具有给定名称的、类型为“TXT”的记录集。 |
> | 行动 | Microsoft.Network/dnszones/TXT/read | 获取 JSON 格式的、“TXT”类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。 |
> | 行动 | Microsoft.Network/dnszones/TXT/write | 在 DNS 区域中创建或更新“TXT”类型的记录集。 指定的记录将替换记录集中的当前记录。 |
> | 行动 | Microsoft.Network/dnszones/write | 在资源组中创建或更新 DNS 区域。  用于更新 DNS 区域资源上的标记。 请注意，无法使用此命令在区域中创建或更新记录集。 |
> | 行动 | Microsoft.Network/expressRouteCircuits/authorizations/delete | 删除 ExpressRouteCircuit 授权 |
> | 行动 | Microsoft.Network/expressRouteCircuits/authorizations/read | 获取 ExpressRouteCircuit 授权 |
> | 行动 | Microsoft.Network/expressRouteCircuits/authorizations/write | 创建新的或更新现有的 ExpressRouteCircuit 授权 |
> | 行动 | Microsoft.Network/expressRouteCircuits/delete | 删除 ExpressRouteCircuit |
> | 行动 | Microsoft.Network/expressRouteCircuits/join/action | 联接 Express Route 线路。 Not 可报警。 |
> | 行动 | ExpressRouteCircuits/对等互连/arpTables/read | 获取 ExpressRouteCircuit 对等互连 ArpTable |
> | 行动 | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | 删除 ExpressRouteCircuit 连接 |
> | 行动 | Microsoft.Network/expressRouteCircuits/peerings/connections/read | 获取 ExpressRouteCircuit 连接 |
> | 行动 | Microsoft.Network/expressRouteCircuits/peerings/connections/write | 创建或更新现有 ExpressRouteCircuit 连接资源 |
> | 行动 | Microsoft.Network/expressRouteCircuits/peerings/delete | 删除 ExpressRouteCircuit 对等互连 |
> | 行动 | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | 获取 Express Route 线路的对等连接 |
> | 行动 | Microsoft.Network/expressRouteCircuits/peerings/read | 获取 ExpressRouteCircuit 对等互连 |
> | 行动 | ExpressRouteCircuits/对等互连/routeTables/read | 获取 ExpressRouteCircuit 对等互连 RouteTable |
> | 行动 | ExpressRouteCircuits/对等互连/routeTablesSummary/read | 获取 ExpressRouteCircuit 对等互连 RouteTable 摘要 |
> | 行动 | Microsoft.Network/expressRouteCircuits/peerings/stats/read | 获取 ExpressRouteCircuit 对等互连统计信息 |
> | 行动 | Microsoft.Network/expressRouteCircuits/peerings/write | 创建新的或更新现有的 ExpressRouteCircuit 对等互连 |
> | 行动 | Microsoft.Network/expressRouteCircuits/read | 获取 ExpressRouteCircuit |
> | 行动 | Microsoft.Network/expressRouteCircuits/stats/read | 获取 ExpressRouteCircuit 统计信息 |
> | 行动 | Microsoft.Network/expressRouteCircuits/write | 创建新的或更新现有的 ExpressRouteCircuit |
> | 行动 | Microsoft.Network/expressRouteCrossConnections/join/action | 联接快速路由交叉连接。 Not 可报警。 |
> | 行动 | ExpressRouteCrossConnections/对等互连/arpTables/read | 获取快速路由交叉对等互连 ARP 表 |
> | 行动 | Microsoft.Network/expressRouteCrossConnections/peerings/delete | 删除快速路由交叉对等互连 |
> | 行动 | Microsoft.Network/expressRouteCrossConnections/peerings/read | 获取快速路由交叉对等互连 |
> | 行动 | ExpressRouteCrossConnections/对等互连/routeTables/read | 获取快速路由交叉对等互连路由表 |
> | 行动 | ExpressRouteCrossConnections/对等互连/routeTableSummary/read | 获取快速路由交叉对等互连路由表摘要 |
> | 行动 | Microsoft.Network/expressRouteCrossConnections/peerings/write | 创建快速路由交叉对等互连，或更新现有快速路由交叉对等互连 |
> | 行动 | Microsoft.Network/expressRouteCrossConnections/read | 获取快速路由交叉连接 |
> | 行动 | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | 删除快速路由连接 |
> | 行动 | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | 获取快速路由连接 |
> | 行动 | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | 创建快速路由连接，或更新现有的快速路由连接 |
> | 行动 | Microsoft.Network/expressRouteGateways/join/action | 联接快速路由网关。 Not 可报警。 |
> | 行动 | Microsoft.Network/expressRouteGateways/read | 获取快速路由网关 |
> | 行动 | Microsoft.Network/expressRoutePorts/delete | 删除 ExpressRoutePorts |
> | 行动 | Microsoft.Network/expressRoutePorts/join/action | 联接 Express 路由端口。 Not 可报警。 |
> | 行动 | Microsoft.Network/expressRoutePorts/links/read | 获取 ExpressRouteLink |
> | 行动 | Microsoft.Network/expressRoutePorts/read | 获取 ExpressRoutePorts |
> | 行动 | Microsoft.Network/expressRoutePorts/write | 创建或更新 ExpressRoutePorts |
> | 行动 | Microsoft.Network/expressRoutePortsLocations/read | 获取快速路由端口位置 |
> | 行动 | Microsoft.Network/expressRouteServiceProviders/read | 获取 Express Route 服务提供商 |
> | 行动 | FirewallPolicies/删除 | 删除防火墙策略 |
> | 行动 | FirewallPolicies/联接/操作 | 加入防火墙策略。 Not 可报警。 |
> | 行动 | FirewallPolicies/read | 获取防火墙策略 |
> | 行动 | FirewallPolicies/规则组/delete | 删除防火墙策略规则组 |
> | 行动 | FirewallPolicies/规则组/read | 获取防火墙策略规则组 |
> | 行动 | FirewallPolicies/规则组/write | 创建防火墙策略规则组，或更新现有的防火墙策略规则组 |
> | 行动 | FirewallPolicies/write | 创建防火墙策略或更新现有的防火墙策略 |
> | 行动 | Microsoft.Network/frontDoors/backendPools/delete | 删除后端池 |
> | 行动 | Microsoft.Network/frontDoors/backendPools/read | 获取后端池 |
> | 行动 | Microsoft.Network/frontDoors/backendPools/write | 创建或更新后端池 |
> | 行动 | Microsoft.Network/frontDoors/delete | 删除 Front Door |
> | 行动 | Microsoft.Network/frontDoors/frontendEndpoints/delete | 删除前端终结点 |
> | 行动 | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | 在前端终结点上禁用 HTTPS |
> | 行动 | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | 在前端终结点上启用 HTTPS |
> | 行动 | Microsoft.Network/frontDoors/frontendEndpoints/read | 获取前端终结点 |
> | 行动 | Microsoft.Network/frontDoors/frontendEndpoints/write | 创建或更新前端终结点 |
> | 行动 | Microsoft.Network/frontDoors/healthProbeSettings/delete | 删除运行状况探测设置 |
> | 行动 | Microsoft.Network/frontDoors/healthProbeSettings/read | 获取运行状况探测设置 |
> | 行动 | Microsoft.Network/frontDoors/healthProbeSettings/write | 创建或更新运行状况探测设置 |
> | 行动 | Microsoft.Network/frontDoors/loadBalancingSettings/delete | 创建或更新负载均衡设置 |
> | 行动 | Microsoft.Network/frontDoors/loadBalancingSettings/read | 获取负载均衡设置 |
> | 行动 | Microsoft.Network/frontDoors/loadBalancingSettings/write | 创建或更新负载均衡设置 |
> | 行动 | Microsoft.Network/frontDoors/purge/action | 从 Front Door 中清除缓存的内容 |
> | 行动 | Microsoft.Network/frontDoors/read | 获取 Front Door |
> | 行动 | Microsoft.Network/frontDoors/routingRules/delete | 删除路由规则 |
> | 行动 | Microsoft.Network/frontDoors/routingRules/read | 获取路由规则 |
> | 行动 | Microsoft.Network/frontDoors/routingRules/write | 创建或更新路由规则 |
> | 行动 | Microsoft.Network/frontDoors/validateCustomDomain/action | 验证 Front Door 的前端终结点 |
> | 行动 | Microsoft.Network/frontDoors/write | 创建或更新 Front Door |
> | 行动 | FrontDoorWebApplicationFirewallManagedRuleSets/read | 获取 Web 应用程序防火墙托管规则集 |
> | 行动 | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | 删除 Web 应用程序防火墙策略 |
> | 行动 | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | 获取 Web 应用程序防火墙策略 |
> | 行动 | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | 创建或更新 Web 应用程序防火墙策略 |
> | 行动 | Microsoft.Network/loadBalancers/backendAddressPools/join/action | 加入负载均衡器后端地址池。 Not 可报警。 |
> | 行动 | Microsoft.Network/loadBalancers/backendAddressPools/read | 获取负载均衡器的后端地址池定义 |
> | 行动 | Microsoft.Network/loadBalancers/delete | 删除负载均衡器 |
> | 行动 | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | 加入负载均衡器前端 IP 配置。 Not 可报警。 |
> | 行动 | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | 获取负载均衡器的前端 IP 配置定义 |
> | 行动 | Microsoft.Network/loadBalancers/inboundNatPools/join/action | 加入负载均衡器入站 NAT 池。 Not 可报警。 |
> | 行动 | Microsoft.Network/loadBalancers/inboundNatPools/read | 获取负载均衡器的入站 NAT 池定义 |
> | 行动 | Microsoft.Network/loadBalancers/inboundNatRules/delete | 删除负载均衡器入站 NAT 规则 |
> | 行动 | Microsoft.Network/loadBalancers/inboundNatRules/join/action | 加入负载均衡器入站 nat 规则。 Not 可报警。 |
> | 行动 | Microsoft.Network/loadBalancers/inboundNatRules/read | 获取负载均衡器的入站 NAT 规则定义 |
> | 行动 | Microsoft.Network/loadBalancers/inboundNatRules/write | 创建负载均衡器入站 NAT 规则，或更新现有的负载均衡器入站 NAT 规则 |
> | 行动 | Microsoft.Network/loadBalancers/loadBalancingRules/read | 获取负载均衡器的负载均衡规则定义 |
> | 行动 | Microsoft.Network/loadBalancers/networkInterfaces/read | 获取对负载均衡器下的所有网络接口的引用 |
> | 行动 | Microsoft.Network/loadBalancers/outboundRules/read | 获取负载均衡器的出站规则定义 |
> | 行动 | Microsoft.Network/loadBalancers/probes/join/action | 允许使用负载均衡器的探测。 例如，使用此权限，VM 规模集的 healthProbe 属性可以引用探测。 Not 可报警。 |
> | 行动 | Microsoft.Network/loadBalancers/probes/read | 获取负载均衡器探测 |
> | 行动 | Microsoft.Network/loadBalancers/read | 获取负载均衡器定义 |
> | 行动 | Microsoft.Network/loadBalancers/virtualMachines/read | 获取对负载均衡器下的所有虚拟机的引用 |
> | 行动 | Microsoft.Network/loadBalancers/write | 创建负载均衡器，或更新现有的负载均衡器 |
> | 行动 | Microsoft.Network/localnetworkgateways/delete | 删除 LocalNetworkGateway |
> | 行动 | Microsoft.Network/localnetworkgateways/read | 获取 LocalNetworkGateway |
> | 行动 | Microsoft.Network/localnetworkgateways/write | 创建新的或更新现有的 LocalNetworkGateway |
> | 行动 | AutoApprovedPrivateLinkServices/位置/网络/读取 | 获取自动批准的私有链接服务 |
> | 行动 | Microsoft.Network/locations/availableDelegations/read | 获取可用的委派 |
> | 行动 | AvailablePrivateEndpointTypes/位置/网络/读取 | 获取可用的专用终结点资源 |
> | 行动 | AvailableServiceAliases/位置/网络/读取 | 获取可用的服务别名 |
> | 行动 | Microsoft.Network/locations/bareMetalTenants/action | 分配或验证裸机租户 |
> | 行动 | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | 检查加速网络支持 |
> | 行动 | Microsoft.Network/locations/checkDnsNameAvailability/read | 检查 DNS 标签是否可在指定的位置使用 |
> | 行动 | CheckPrivateLinkServiceVisibility/位置/操作 | 检查私有链接服务的可见性 |
> | 行动 | Microsoft.Network/locations/operationResults/read | 获取异步 POST 或 DELETE 操作的操作结果 |
> | 行动 | Microsoft.Network/locations/operations/read | 获取表示异步操作状态的操作资源 |
> | 行动 | ServiceTags/位置/网络/读取 | 获取服务标记 |
> | 行动 | Microsoft.Network/locations/supportedVirtualMachineSizes/read | 获取支持的虚拟机大小 |
> | 行动 | Microsoft.Network/locations/usages/read | 获取资源用量指标 |
> | 行动 | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | 获取可用虚拟网络终结点服务的列表 |
> | 行动 | Microsoft.Network/networkIntentPolicies/delete | 删除网络意向策略 |
> | 行动 | Microsoft.Network/networkIntentPolicies/read | 获取网络意向策略说明 |
> | 行动 | Microsoft.Network/networkIntentPolicies/write | 创建网络意向策略或更新现有网络意向策略 |
> | 行动 | Microsoft.Network/networkInterfaces/delete | 删除网络接口 |
> | 行动 | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | 获取 VM 的网络接口上配置的网络安全组 |
> | 行动 | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | 获取 VM 的网络接口上配置的路由表 |
> | 行动 | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | 加入网络接口 IP 配置。 Not 可报警。 |
> | 行动 | Microsoft.Network/networkInterfaces/ipconfigurations/read | 获取网络接口 IP 配置定义。  |
> | 行动 | Microsoft.Network/networkInterfaces/join/action | 将虚拟机加入到网络接口。 Not 可报警。 |
> | 行动 | Microsoft.Network/networkInterfaces/loadBalancers/read | 获取网络接口所属的所有负载均衡器 |
> | 行动 | Microsoft.Network/networkInterfaces/read | 获取网络接口定义。  |
> | 行动 | Microsoft.Network/networkInterfaces/tapConfigurations/delete | 删除网络接口 Tap 配置。 |
> | 行动 | Microsoft.Network/networkInterfaces/tapConfigurations/read | 获取网络接口 Tap 配置。 |
> | 行动 | Microsoft.Network/networkInterfaces/tapConfigurations/write | 创建网络接口 Tap 配置，或更新现有的网络接口 Tap 配置。 |
> | 行动 | Microsoft.Network/networkInterfaces/write | 创建网络接口，或更新现有的网络接口。  |
> | 行动 | Microsoft.Network/networkProfiles/delete | 删除网络配置文件 |
> | 行动 | Microsoft.Network/networkProfiles/read | 获取网络配置文件 |
> | 行动 | Microsoft.Network/networkProfiles/removeContainers/action | 删除容器 |
> | 行动 | Microsoft.Network/networkProfiles/setContainers/action | 设置容器 |
> | 行动 | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | 设置容器网络接口 |
> | 行动 | Microsoft.Network/networkProfiles/write | 创建或更新网络配置文件 |
> | 行动 | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | 获取默认的安全规则定义 |
> | 行动 | Microsoft.Network/networkSecurityGroups/delete | 删除网络安全组 |
> | 行动 | Microsoft.Network/networkSecurityGroups/join/action | 加入网络安全组。 Not 可报警。 |
> | 行动 | Microsoft.Network/networkSecurityGroups/read | 获取网络安全组定义 |
> | 行动 | Microsoft.Network/networkSecurityGroups/securityRules/delete | 删除安全规则 |
> | 行动 | Microsoft.Network/networkSecurityGroups/securityRules/read | 获取安全规则定义 |
> | 行动 | Microsoft.Network/networkSecurityGroups/securityRules/write | 创建安全规则，或更新现有的安全规则 |
> | 行动 | Microsoft.Network/networkSecurityGroups/write | 创建网络安全组，或更新现有的网络安全组 |
> | 行动 | Microsoft.Network/networkWatchers/availableProvidersList/action | 返回指定 Azure 区域的所有可用 Internet 服务提供商。 |
> | 行动 | Microsoft.Network/networkWatchers/azureReachabilityReport/action | 返回 Internet 服务提供商从指定位置到 Azure 区域的相对延迟评分。 |
> | 行动 | Microsoft.Network/networkWatchers/configureFlowLog/action | 配置目标资源的流日志记录。 |
> | 行动 | Microsoft.Network/networkWatchers/connectionMonitors/delete | 删除连接监视器 |
> | 行动 | Microsoft.Network/networkWatchers/connectionMonitors/query/action | 监视指定终结点之间连接的查询 |
> | 行动 | Microsoft.Network/networkWatchers/connectionMonitors/read | 获取连接监视器详细信息 |
> | 行动 | Microsoft.Network/networkWatchers/connectionMonitors/start/action | 开始监视指定终结点之间的连接 |
> | 行动 | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | 停止/暂停监视指定终结点之间的连接 |
> | 行动 | Microsoft.Network/networkWatchers/connectionMonitors/write | 创建连接监视器 |
> | 行动 | Microsoft.Network/networkWatchers/connectivityCheck/action | 验证从虚拟机到给定终结点（包括另一个 VM 或任意远程服务器）建立直接 TCP 连接的可能性。 |
> | 行动 | Microsoft.Network/networkWatchers/delete | 删除网络观察程序 |
> | 行动 | Microsoft.Network/networkWatchers/ipFlowVerify/action | 返回是允许还是拒绝与特定的目标相互发送数据包。 |
> | 行动 | Microsoft.Network/networkWatchers/lenses/delete | 删除可重用功能区 |
> | 行动 | Microsoft.Network/networkWatchers/lenses/query/action | 监视指定终结点上流量的查询 |
> | 行动 | Microsoft.Network/networkWatchers/lenses/read | 获取可重用功能区详细信息 |
> | 行动 | Microsoft.Network/networkWatchers/lenses/start/action | 开始监视指定终结点上的流量 |
> | 行动 | Microsoft.Network/networkWatchers/lenses/stop/action | 停止/暂停监视指定终结点上的流量 |
> | 行动 | Microsoft.Network/networkWatchers/lenses/write | 创建可重用功能区 |
> | 行动 | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | 诊断网络配置。 |
> | 行动 | Microsoft.Network/networkWatchers/nextHop/action | 对于指定的目标和目标 IP 地址，返回下一跃点类型和下一跃点 IP 地址。 |
> | 行动 | Microsoft.Network/networkWatchers/packetCaptures/delete | 删除数据包捕获 |
> | 行动 | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | 获取有关数据包捕获资源的属性和状态的信息。 |
> | 行动 | Microsoft.Network/networkWatchers/packetCaptures/read | 获取数据包捕获定义 |
> | 行动 | Microsoft.Network/networkWatchers/packetCaptures/stop/action | 停止正在运行的数据包捕获会话。 |
> | 行动 | Microsoft.Network/networkWatchers/packetCaptures/write | 创建数据包捕获 |
> | 行动 | Microsoft.Network/networkWatchers/pingMeshes/delete | 删除 PingMesh |
> | 行动 | Microsoft.Network/networkWatchers/pingMeshes/read | 获取 PingMesh 详细信息 |
> | 行动 | Microsoft.Network/networkWatchers/pingMeshes/start/action | 启动指定 VM 之间的 PingMesh |
> | 行动 | Microsoft.Network/networkWatchers/pingMeshes/stop/action | 停止指定 VM 之间的 PingMesh |
> | 行动 | Microsoft.Network/networkWatchers/pingMeshes/write | 创建 PingMesh |
> | 行动 | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | 监视指定终结点之间的连接的批处理查询 |
> | 行动 | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | 获取资源上流日志记录的状态。 |
> | 行动 | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | 获取上次运行的或当前正在运行的故障排除操作的故障排除结果。 |
> | 行动 | Microsoft.Network/networkWatchers/read | 获取网络观察程序定义 |
> | 行动 | Microsoft.Network/networkWatchers/securityGroupView/action | 查看 VM 上应用的已配置和有效的网络安全组规则。 |
> | 行动 | Microsoft.Network/networkWatchers/topology/action | 获取资源组中的资源及其关系的网络级视图。 |
> | 行动 | Microsoft.Network/networkWatchers/troubleshoot/action | 开始针对 Azure 中的网络资源进行故障排除。 |
> | 行动 | Microsoft.Network/networkWatchers/write | 创建网络观察程序，或更新现有的网络观察程序 |
> | 行动 | Microsoft.Network/operations/read | 获取可用操作 |
> | 行动 | Microsoft.Network/p2sVpnGateways/delete | 删除 P2SVpnGateway。 |
> | 行动 | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | 为 P2SVpnGateway 生成 VPN 配置文件 |
> | 行动 | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | 获取 P2SVpnGateway 的 P2S VPN 连接运行状况 |
> | 行动 | P2sVpnGateways/getp2svpnconnectionhealthdetailed/action | 获取用于 P2SVpnGateway 的 P2S Vpn 连接运行状况 |
> | 行动 | Microsoft.Network/p2sVpnGateways/read | 获取 P2SVpnGateway。 |
> | 行动 | Microsoft.Network/p2sVpnGateways/write | 放置 P2SVpnGateway。 |
> | 行动 | PrivateDnsOperationResults/read | 获取专用 DNS 操作的结果 |
> | 行动 | PrivateDnsOperationStatuses/read | 获取专用 DNS 操作的状态 |
> | 行动 | PrivateDnsZones/A/delete | 从专用 DNS 区域中删除具有给定名称和类型 "A" 的记录集。 |
> | 行动 | PrivateDnsZones///读取 | 以 JSON 格式获取专用 DNS 区域内类型为 "A" 的记录集。 记录集包含记录列表以及 TTL、标记和 etag。 |
> | 行动 | PrivateDnsZones///写入 | 在专用 DNS 区域中创建或更新 "A" 类型的记录集。 指定的记录将替换记录集中的当前记录。 |
> | 行动 | PrivateDnsZones/AAAA/delete | 从专用 DNS 区域中删除具有给定名称且类型为 "AAAA" 的记录集。 |
> | 行动 | PrivateDnsZones/AAAA/read | 以 JSON 格式获取专用 DNS 区域内类型为 "AAAA" 的记录集。 记录集包含记录列表以及 TTL、标记和 etag。 |
> | 行动 | PrivateDnsZones/AAAA/write | 在专用 DNS 区域中创建或更新 "AAAA" 类型的记录集。 指定的记录将替换记录集中的当前记录。 |
> | 行动 | PrivateDnsZones/全部/读取 | 获取跨类型专用 DNS 记录集 |
> | 行动 | PrivateDnsZones/CNAME/CNAME/delete | 从专用 DNS 区域中删除具有给定名称的、类型为 "CNAME" 的记录集。 |
> | 行动 | PrivateDnsZones/CNAME/已读 | 以 JSON 格式获取专用 DNS 区域内类型为 "CNAME" 的记录集。 |
> | 行动 | PrivateDnsZones/CNAME/write | 在专用 DNS 区域中创建或更新 "CNAME" 类型的记录集。 |
> | 行动 | PrivateDnsZones/删除 | 删除专用 DNS 区域。 |
> | 行动 | PrivateDnsZones/MX/delete | 从专用 DNS 区域中删除具有给定名称和类型 "MX" 的记录集。 |
> | 行动 | PrivateDnsZones/MX/已读 | 获取 JSON 格式的专用 DNS 区域内 "MX" 类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。 |
> | 行动 | PrivateDnsZones/MX/write | 在专用 DNS 区域中创建或更新 "MX" 类型的记录集。 指定的记录将替换记录集中的当前记录。 |
> | 行动 | PrivateDnsZones/PTR/删除 | 从专用 DNS 区域中删除具有给定名称和类型 "PTR" 的记录集。 |
> | 行动 | PrivateDnsZones/PTR/读取 | 获取 JSON 格式的专用 DNS 区域内 "PTR" 类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。 |
> | 行动 | PrivateDnsZones/PTR/写入 | 在专用 DNS 区域中创建或更新 "PTR" 类型的记录集。 指定的记录将替换记录集中的当前记录。 |
> | 行动 | PrivateDnsZones/read | 获取 JSON 格式的专用 DNS 区域属性。 请注意，此命令不会检索专用 DNS 区域所链接到的虚拟网络，也不会检索区域中包含的记录集。 |
> | 行动 | PrivateDnsZones/recordset/read | 获取跨类型专用 DNS 记录集 |
> | 行动 | PrivateDnsZones/SOA/read | 获取 JSON 格式的专用 DNS 区域内 "SOA" 类型的记录集。 |
> | 行动 | PrivateDnsZones/SOA/写入 | 在专用 DNS 区域中更新 "SOA" 类型的记录集。 |
> | 行动 | PrivateDnsZones/SRV/delete | 从专用 DNS 区域中删除具有给定名称和类型 "SRV" 的记录集。 |
> | 行动 | PrivateDnsZones/SRV/读取 | 获取 JSON 格式的专用 DNS 区域内 "SRV" 类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。 |
> | 行动 | PrivateDnsZones/SRV/写入 | 在专用 DNS 区域中创建或更新类型为 "SRV" 的记录集。 指定的记录将替换记录集中的当前记录。 |
> | 行动 | PrivateDnsZones/TXT/TXT/delete | 从专用 DNS 区域中删除具有给定名称和类型 "TXT" 的记录集。 |
> | 行动 | PrivateDnsZones/网络/TXT/读取 | 获取 JSON 格式的专用 DNS 区域中 "TXT" 类型的记录集。 记录集包含记录列表以及 TTL、标记和 etag。 |
> | 行动 | PrivateDnsZones/TXT/write | 在专用 DNS 区域中创建或更新 "TXT" 类型的记录集。 指定的记录将替换记录集中的当前记录。 |
> | 行动 | PrivateDnsZones/virtualNetworkLinks/delete | 删除虚拟网络的专用 DNS 区域链接。 |
> | 行动 | PrivateDnsZones/virtualNetworkLinks/read | 获取虚拟网络属性的专用 DNS 区域链接（采用 JSON 格式）。 |
> | 行动 | PrivateDnsZones/virtualNetworkLinks/write | 创建或更新虚拟网络的专用 DNS 区域链接。 |
> | 行动 | PrivateDnsZones/write | 在资源组中创建或更新专用 DNS 区域。 请注意，此命令不能用于在区域中创建或更新虚拟网络链接或记录集。 |
> | 行动 | PrivateEndpointRedirectMaps/read | 获取专用终结点 RedirectMap |
> | 行动 | PrivateEndpointRedirectMaps/write | 创建私有终结点 RedirectMap 或更新现有专用终结点 RedirectMap |
> | 行动 | PrivateEndpoints/删除 | 删除私有终结点资源。 |
> | 行动 | PrivateEndpoints/read | 获取专用终结点资源。 |
> | 行动 | PrivateEndpoints/write | 创建新的专用终结点，或更新现有的专用终结点。 |
> | 行动 | Microsoft.Network/privateLinkServices/delete | 删除专用链接服务资源。 |
> | 行动 | PrivateLinkServices/privateEndpointConnections/delete | 删除私有终结点连接。 |
> | 行动 | PrivateLinkServices/privateEndpointConnections/read | 获取私有终结点连接定义。 |
> | 行动 | PrivateLinkServices/privateEndpointConnections/write | 创建新的专用终结点连接，或更新现有的专用终结点连接。 |
> | 行动 | Microsoft.Network/privateLinkServices/read | 获取专用链接服务资源。 |
> | 行动 | Microsoft.Network/privateLinkServices/write | 创建新的专用链接服务，或更新现有的专用链接服务。 |
> | 行动 | Microsoft.Network/publicIPAddresses/delete | 删除公共 IP 地址。 |
> | 行动 | Microsoft.Network/publicIPAddresses/join/action | 加入公共 ip 地址。 Not 可报警。 |
> | 行动 | Microsoft.Network/publicIPAddresses/read | 获取公共 IP 地址定义。 |
> | 行动 | Microsoft.Network/publicIPAddresses/write | 创建公共 IP 地址，或更新现有的公共 IP 地址。  |
> | 行动 | Microsoft.Network/publicIPPrefixes/delete | 删除公共 IP 前缀 |
> | 行动 | Microsoft.Network/publicIPPrefixes/join/action | 联接 PublicIPPrefix。 Not 可报警。 |
> | 行动 | Microsoft.Network/publicIPPrefixes/read | 获取公共 IP 前缀定义 |
> | 行动 | Microsoft.Network/publicIPPrefixes/write | 创建公共 IP 前缀或更新现有的公共 IP 前缀 |
> | 行动 | Microsoft.Network/register/action | 注册订阅 |
> | 行动 | Microsoft.Network/routeFilters/delete | 删除路由筛选器定义 |
> | 行动 | Microsoft.Network/routeFilters/join/action | 联接路由筛选器。 Not 可报警。 |
> | 行动 | Microsoft.Network/routeFilters/read | 获取路由筛选器定义 |
> | 行动 | Microsoft.Network/routeFilters/routeFilterRules/delete | 删除路由筛选器规则定义 |
> | 行动 | Microsoft.Network/routeFilters/routeFilterRules/read | 获取路由筛选器规则定义 |
> | 行动 | Microsoft.Network/routeFilters/routeFilterRules/write | 创建路由筛选器规则，或更新现有的路由筛选器规则 |
> | 行动 | Microsoft.Network/routeFilters/write | 创建路由筛选器，或更新现有的路由筛选器 |
> | 行动 | Microsoft.Network/routeTables/delete | 删除路由表定义 |
> | 行动 | Microsoft.Network/routeTables/join/action | 联接路由表。 Not 可报警。 |
> | 行动 | Microsoft.Network/routeTables/read | 获取路由表定义 |
> | 行动 | Microsoft.Network/routeTables/routes/delete | 删除路由定义 |
> | 行动 | Microsoft.Network/routeTables/routes/read | 获取路由定义 |
> | 行动 | Microsoft.Network/routeTables/routes/write | 创建路由，或更新现有的路由 |
> | 行动 | Microsoft.Network/routeTables/write | 创建路由表，或更新现有的路由表 |
> | 行动 | Microsoft.Network/securegateways/delete | 删除安全网关 |
> | 行动 | Microsoft.Network/securegateways/read | 获取安全网关 |
> | 行动 | Microsoft.Network/securegateways/write | 创建或更新安全网关 |
> | 行动 | Microsoft.Network/serviceEndpointPolicies/delete | 删除服务终结点策略 |
> | 行动 | Microsoft.Network/serviceEndpointPolicies/join/action | 加入服务终结点策略。 Not 可报警。 |
> | 行动 | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | 将子网加入服务终结点策略。 Not 可报警。 |
> | 行动 | Microsoft.Network/serviceEndpointPolicies/read | 获取服务终结点策略说明 |
> | 行动 | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | 删除服务终结点策略定义 |
> | 行动 | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | 获取服务终结点策略定义说明 |
> | 行动 | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | 创建服务终结点策略定义或更新现有服务终结点策略定义 |
> | 行动 | Microsoft.Network/serviceEndpointPolicies/write | 创建服务终结点策略或更新现有服务终结点策略 |
> | 行动 | Microsoft.Network/trafficManagerGeographicHierarchies/read | 获取流量管理器地理层次结构，其中包含可以配合地理流量路由方法使用的区域 |
> | 行动 | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | 从现有流量管理器配置文件中删除 Azure 终结点。 流量管理器会停止将流量路由到已删除的 Azure 终结点。 |
> | 行动 | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | 获取属于流量管理器配置文件的 Azure 终结点，包括该 Azure 终结点的所有属性。 |
> | 行动 | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | 在现有流量管理器配置文件中添加新的 Azure 终结点，或更新该流量管理器配置文件中的现有 Azure 终结点的属性。 |
> | 行动 | Microsoft.Network/trafficManagerProfiles/delete | 删除流量管理器配置文件。 与流量管理器配置文件关联的所有设置都将丢失，该配置文件不再可用于路由流量。 |
> | 行动 | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | 从现有流量管理器配置文件中删除外部终结点。 流量管理器会停止将流量路由到已删除的外部终结点。 |
> | 行动 | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | 获取属于流量管理器配置文件的外部终结点，包括该外部终结点的所有属性。 |
> | 行动 | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | 在现有流量管理器配置文件中添加新的外部终结点，或更新该流量管理器配置文件中的现有外部终结点的属性。 |
> | 行动 | Microsoft.Network/trafficManagerProfiles/heatMaps/read | 获取给定流量管理器配置文件的流量管理器热度地图，其中包含按位置和源 IP 的查询计数和延迟数据。 |
> | 行动 | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | 从现有流量管理器配置文件中删除嵌套终结点。 流量管理器会停止将流量路由到已删除的嵌套终结点。 |
> | 行动 | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | 获取属于流量管理器配置文件的嵌套终结点，包括该嵌套终结点的所有属性。 |
> | 行动 | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | 在现有流量管理器配置文件中添加新的嵌套终结点，或更新该流量管理器配置文件中的现有嵌套终结点的属性。 |
> | 行动 | Microsoft.Network/trafficManagerProfiles/read | 获取流量管理器配置文件配置。 此配置包含 DNS 设置、流量路由设置、终结点监视设置，以及此流量管理器配置文件路由的终结点列表。 |
> | 行动 | Microsoft.Network/trafficManagerProfiles/write | 创建流量管理器配置文件，或修改现有流量管理器配置文件的配置。<br>这包括启用或禁用配置文件，以及修改 DNS 设置、流量路由设置或终结点监视设置。<br>可以添加、删除、启用或禁用流量管理器配置文件路由的终结点。 |
> | 行动 | Microsoft.Network/trafficManagerUserMetricsKeys/delete | 删除用于实时用户指标集合的订阅级别密钥。 |
> | 行动 | Microsoft.Network/trafficManagerUserMetricsKeys/read | 获取用于实时用户指标集合的订阅级别密钥。 |
> | 行动 | Microsoft.Network/trafficManagerUserMetricsKeys/write | 创建将用于实时用户指标集合的新订阅级别密钥。 |
> | 行动 | Microsoft.Network/unregister/action | 取消注册订阅 |
> | 行动 | Microsoft.Network/virtualHubs/delete | 删除虚拟中心 |
> | 行动 | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | 删除 HubVirtualNetworkConnection |
> | 行动 | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | 获取 HubVirtualNetworkConnection |
> | 行动 | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | 创建或更新 HubVirtualNetworkConnection |
> | 行动 | Microsoft.Network/virtualHubs/read | 获取虚拟中心 |
> | 行动 | VirtualHubs/routeTables/delete | 删除 VirtualHubRouteTableV2 |
> | 行动 | VirtualHubs/routeTables/read | 获取 VirtualHubRouteTableV2 |
> | 行动 | VirtualHubs/routeTables/write | 创建或更新 VirtualHubRouteTableV2 |
> | 行动 | Microsoft.Network/virtualHubs/write | 创建或更新虚拟中心 |
> | 行动 | microsoft.network/virtualnetworkgateways/connections/read | 获取 VirtualNetworkGatewayConnection |
> | 行动 | Microsoft.Network/virtualNetworkGateways/delete | 删除 virtualNetworkGateway |
> | 行动 | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | 生成 virtualNetworkGateway 的 VpnClient 包 |
> | 行动 | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | 生成 VirtualNetworkGateway 的 VpnProfile 包 |
> | 行动 | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | 获取 virtualNetworkGateway 播发路由 |
> | 行动 | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | 获取 virtualNetworkGateway BGP 对等状态 |
> | 行动 | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | 获取 virtualnetworkgateway 获知的路由 |
> | 行动 | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | 获取 VirtualNetworkGateway 的每个 VPN 客户端连接运行状况 |
> | 行动 | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | 获取 VirtualNetworkGateway P2S 客户端的 Vpnclient Ipsec 参数。 |
> | 行动 | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | 获取预生成的 VPN 客户端配置文件包的 URL |
> | 行动 | Microsoft.Network/virtualNetworkGateways/read | 获取 VirtualNetworkGateway |
> | 行动 | microsoft.network/virtualnetworkgateways/reset/action | 重置 virtualNetworkGateway |
> | 行动 | microsoft.network/virtualnetworkgateways/resetvpnclientsharedkey/action | 重置 VirtualNetworkGateway P2S 客户端的 Vpnclient 共享密钥。 |
> | 行动 | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | 设置 VirtualNetworkGateway P2S 客户端的 Vpnclient Ipsec 参数。 |
> | 行动 | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | 列出支持的 VPN 设备 |
> | 行动 | Microsoft.Network/virtualNetworkGateways/write | 创建或更新 VirtualNetworkGateway |
> | 行动 | Microsoft.Network/virtualNetworks/BastionHosts/action | 获取虚拟网络中的守护主机引用。 |
> | 行动 | VirtualNetworks/bastionHosts/default/action | 获取虚拟网络中的守护主机引用。 |
> | 行动 | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | 检查 IP 地址是否在指定的虚拟网络中可用 |
> | 行动 | Microsoft.Network/virtualNetworks/delete | 删除虚拟网络 |
> | 行动 | VirtualNetworks/联接/操作 | 加入虚拟网络。 Not 可报警。 |
> | 行动 | Microsoft.Network/virtualNetworks/peer/action | 在两个不同的虚拟网络之间建立对等互连 |
> | 行动 | Microsoft.Network/virtualNetworks/read | 获取虚拟网络定义 |
> | 行动 | VirtualNetworks/contextualServiceEndpointPolicies/delete | 删除上下文服务终结点策略 |
> | 行动 | VirtualNetworks/contextualServiceEndpointPolicies/read | 获取上下文服务终结点策略 |
> | 行动 | VirtualNetworks/contextualServiceEndpointPolicies/write | 创建上下文服务终结点策略或更新现有的上下文服务终结点策略 |
> | 行动 | Microsoft.Network/virtualNetworks/subnets/delete | 删除虚拟网络子网 |
> | 行动 | Microsoft.Network/virtualNetworks/subnets/join/action | 加入虚拟网络。 Not 可报警。 |
> | 行动 | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | 将存储帐户或 SQL 数据库等资源加入到子网。 Not 可报警。 |
> | 行动 | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | 通过应用必要的网络策略来准备子网 |
> | 行动 | Microsoft.Network/virtualNetworks/subnets/read | 获取虚拟网络子网定义 |
> | 行动 | VirtualNetworks/unprepareNetworkPolicies/action | 通过删除应用的网络策略 Unprepare 子网 |
> | 行动 | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | 获取对虚拟网络子网中的所有虚拟机的引用 |
> | 行动 | Microsoft.Network/virtualNetworks/subnets/write | 创建虚拟网络子网，或更新现有的虚拟网络子网 |
> | 行动 | Microsoft.Network/virtualNetworks/usages/read | 获取虚拟网络的每个子网的 IP 使用情况 |
> | 行动 | Microsoft.Network/virtualNetworks/virtualMachines/read | 获取对虚拟网络中的所有虚拟机的引用 |
> | 行动 | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | 删除虚拟网络对等互连 |
> | 行动 | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | 获取虚拟网络对等互连定义 |
> | 行动 | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | 创建虚拟网络对等互连，或更新现有的虚拟网络对等互连 |
> | 行动 | Microsoft.Network/virtualNetworks/write | 创建虚拟网络，或更新现有的虚拟网络 |
> | 行动 | Microsoft.Network/virtualNetworkTaps/delete | 删除虚拟网络点击 |
> | 行动 | Microsoft.Network/virtualNetworkTaps/join/action | 加入虚拟网络分流点。 Not 可报警。 |
> | 行动 | Microsoft.Network/virtualNetworkTaps/read | 获取虚拟网络点击 |
> | 行动 | Microsoft.Network/virtualNetworkTaps/write | 创建或更新虚拟网络点击 |
> | 行动 | VirtualRouters/删除 | 删除 VirtualRouter |
> | 行动 | VirtualRouters/联接/操作 | 联接 VirtualRouter。 Not 可报警。 |
> | 行动 | VirtualRouters/read | 获取 VirtualRouter |
> | 行动 | VirtualRouters/virtualRouterPeerings/delete | 删除 VirtualRouterPeering |
> | 行动 | VirtualRouters/virtualRouterPeerings/read | 获取 VirtualRouterPeering |
> | 行动 | VirtualRouters/virtualRouterPeerings/write | 创建 VirtualRouterPeering 或更新现有的 VirtualRouterPeering |
> | 行动 | VirtualRouters/write | 创建 VirtualRouter 或更新现有的 VirtualRouter |
> | 行动 | Microsoft.Network/virtualWans/delete | 删除虚拟 WAN |
> | 行动 | Virtualwans/generateVpnProfile/action | 生成 VirtualWanVpnServerConfiguration VpnProfile |
> | 行动 | Microsoft.Network/virtualWans/read | 获取虚拟 WAN |
> | 行动 | Microsoft.Network/virtualwans/supportedSecurityProviders/read | 获取支持的 VirtualWan 安全提供程序。 |
> | 行动 | Microsoft.Network/virtualWans/virtualHubs/read | 获取引用了某个虚拟 WAN 的所有虚拟中心。 |
> | 行动 | Microsoft.Network/virtualwans/vpnconfiguration/action | 获取 VPN 配置 |
> | 行动 | Virtualwans/vpnServerConfigurations/action | 获取 VirtualWanVpnServerConfigurations |
> | 行动 | Microsoft.Network/virtualWans/vpnSites/read | 获取引用了某个虚拟 WAN 的所有 VPN 站点。 |
> | 行动 | Microsoft.Network/virtualWans/write | 创建或更新虚拟 WAN |
> | 行动 | Microsoft.Network/vpnGateways/delete | 删除 VpnGateway。 |
> | 行动 | microsoft.network/vpngateways/listvpnconnectionshealth/action | 获取 VpnGateway 上所有或一部分连接的连接运行状况 |
> | 行动 | Microsoft.Network/vpnGateways/read | 获取 VpnGateway。 |
> | 行动 | microsoft.network/vpngateways/reset/action | 获取 VpnGateway |
> | 行动 | microsoft.network/vpnGateways/vpnConnections/delete | 删除 VpnConnection。 |
> | 行动 | microsoft.network/vpnGateways/vpnConnections/read | 获取 VpnConnection。 |
> | 行动 | vpnGateways/vpnConnections/vpnLinkConnections/read | 获取 Vpn 链接连接 |
> | 行动 | microsoft.network/vpnGateways/vpnConnections/write | 放置 VpnConnection。 |
> | 行动 | Microsoft.Network/vpnGateways/write | 放置 VpnGateway。 |
> | 行动 | VpnServerConfigurations/删除 | 删除 VpnServerConfiguration |
> | 行动 | VpnServerConfigurations/read | 获取 VpnServerConfiguration |
> | 行动 | VpnServerConfigurations/write | 创建或更新 VpnServerConfiguration |
> | 行动 | Microsoft.Network/vpnsites/delete | 删除 VPN 站点资源。 |
> | 行动 | Microsoft.Network/vpnsites/read | 获取 VPN 站点资源。 |
> | 行动 | vpnSites/vpnSiteLinks/read | 获取 Vpn 站点链接 |
> | 行动 | Microsoft.Network/vpnsites/write | 创建或更新 VPN 站点资源。 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | 检查给定的命名空间资源名称是否在 NotificationHub 服务中可用。 |
> | 行动 | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | 获取命名空间授权规则说明列表。 |
> | 行动 | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | 删除命名空间授权规则。 无法删除默认的命名空间授权规则。  |
> | 行动 | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | 获取命名空间的连接字符串 |
> | 行动 | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | 获取命名空间授权规则说明列表。 |
> | 行动 | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | 命名空间授权规则再生成主密钥/辅助密钥，指定需要再生成的密钥 |
> | 行动 | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | 创建命名空间级授权规则并更新其属性。 可以更新授权规则访问权限、主密钥和辅助密钥。 |
> | 行动 | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | 检查给定的 NotificationHub 名称是否在命名空间中可用。 |
> | 行动 | Microsoft.NotificationHubs/Namespaces/Delete | 删除命名空间资源 |
> | 行动 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | 获取通知中心授权规则列表 |
> | 行动 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | 删除通知中心授权规则 |
> | 行动 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | 获取通知中心的连接字符串 |
> | 行动 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | 获取通知中心授权规则列表 |
> | 行动 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | 通知中心授权规则再生成主密钥/辅助密钥，指定需要再生成的密钥 |
> | 行动 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | 创建通知中心授权规则并更新其属性。 可以更新授权规则访问权限、主密钥和辅助密钥。 |
> | 行动 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | 发送测试推送通知。 |
> | 行动 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | 删除通知中心资源 |
> | 行动 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | 获取命名空间指标资源说明列表 |
> | 行动 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | 获取所有通知中心的 PNS 凭据。 这包括 WNS、MPNS、APNS、GCM 和百度凭据 |
> | 行动 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | 获取通知中心资源说明列表 |
> | 行动 | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | 创建通知中心并更新其属性。 其属性主要包括 PNS 凭据。 授权规则和 TTL |
> | 行动 | Microsoft.NotificationHubs/Namespaces/read | 获取命名空间资源说明列表 |
> | 行动 | Microsoft.NotificationHubs/Namespaces/write | 创建命名空间资源并更新其属性。 命名空间的标记和容量是可更新的属性。 |
> | 行动 | Microsoft.NotificationHubs/operationResults/read | 返回通知中心提供程序的操作结果 |
> | 行动 | Microsoft.NotificationHubs/operations/read | 返回通知中心提供程序支持的操作列表 |
> | 行动 | Microsoft.NotificationHubs/register/action | 注册 NotificationHubs 资源提供程序的订阅，并启用命名空间和 NotificationHubs 的创建 |
> | 行动 | Microsoft.NotificationHubs/unregister/action | 取消注册 NotificationHubs 资源提供程序的订阅，并启用命名空间和 NotificationHubs 的创建 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.OffAzure/HyperVSites/clusters/read | 获取 Hyper-V 群集的属性 |
> | 行动 | Microsoft.OffAzure/HyperVSites/clusters/write | 创建或更新 Hyper-V 群集 |
> | 行动 | Microsoft.OffAzure/HyperVSites/delete | 删除 Hyper-V 站点 |
> | 行动 | OffAzure/HyperVSites/healthsummary/read | 获取 Hyper-v 资源的运行状况摘要 |
> | 行动 | Microsoft.OffAzure/HyperVSites/hosts/read | 获取 Hyper-V 主机的属性 |
> | 行动 | Microsoft.OffAzure/HyperVSites/hosts/write | 创建或更新 Hyper-V 主机 |
> | 行动 | Microsoft.OffAzure/HyperVSites/jobs/read | 获取 Hyper-V 作业的属性 |
> | 行动 | Microsoft.OffAzure/HyperVSites/machines/read | 获取 Hyper-V 计算机的属性 |
> | 行动 | Microsoft.OffAzure/HyperVSites/operationsstatus/read | 获取 Hyper-V 操作状态的属性 |
> | 行动 | Microsoft.OffAzure/HyperVSites/read | 获取 Hyper-V 站点的属性 |
> | 行动 | Microsoft.OffAzure/HyperVSites/refresh/action | 刷新 Hyper-V 站点中的对象 |
> | 行动 | Microsoft.OffAzure/HyperVSites/runasaccounts/read | 获取作为帐户运行的 Hyper-V 的属性 |
> | 行动 | Microsoft.OffAzure/HyperVSites/usage/read | 获取 Hyper-V 的使用情况 |
> | 行动 | Microsoft.OffAzure/HyperVSites/write | 创建或更新 Hyper-V 站点 |
> | 行动 | Microsoft.OffAzure/Operations/read | 读取公开的操作 |
> | 行动 | Microsoft.OffAzure/register/action | 使用 Microsoft.OffAzure 资源提供程序注册订阅 |
> | 行动 | OffAzure/ServerSites/作业/读取 | 获取服务器作业的属性 |
> | 行动 | OffAzure/ServerSites/计算机/读取 | 获取服务器计算机的属性 |
> | 行动 | OffAzure/ServerSites/计算机/写入 | 编写服务器计算机的属性 |
> | 行动 | OffAzure/ServerSites/operationsstatus/read | 获取服务器操作状态的属性 |
> | 行动 | OffAzure/ServerSites/read | 获取服务器站点的属性 |
> | 行动 | OffAzure/ServerSites/refresh/action | 刷新服务器站点中的对象 |
> | 行动 | OffAzure/ServerSites/运行方式帐户/read | 获取服务器运行方式帐户的属性 |
> | 行动 | OffAzure/ServerSites/用量/read | 获取服务器站点的使用情况 |
> | 行动 | OffAzure/ServerSites/write | 创建或更新服务器站点 |
> | 行动 | Microsoft.OffAzure/VMwareSites/delete | 删除 VMware 站点 |
> | 行动 | OffAzure/VMwareSites/healthsummary/read | 获取 VMware 资源的运行状况摘要 |
> | 行动 | Microsoft.OffAzure/VMwareSites/jobs/read | 获取 VMware 作业的属性 |
> | 行动 | Microsoft.OffAzure/VMwareSites/machines/read | 获取 VMware 计算机的属性 |
> | 行动 | Microsoft.OffAzure/VMwareSites/machines/start/action | 启动 VMware 计算机 |
> | 行动 | Microsoft.OffAzure/VMwareSites/machines/stop/action | 停止 VMware 计算机 |
> | 行动 | Microsoft.OffAzure/VMwareSites/operationsstatus/read | 获取 VMware 操作状态的属性 |
> | 行动 | Microsoft.OffAzure/VMwareSites/read | 获取 VMware 站点的属性 |
> | 行动 | Microsoft.OffAzure/VMwareSites/refresh/action | 刷新 VMware 站点中的对象 |
> | 行动 | Microsoft.OffAzure/VMwareSites/runasaccounts/read | 获取作为帐户运行的 VMware 的属性 |
> | 行动 | Microsoft.OffAzure/VMwareSites/usage/read | 获取 VMware 的使用情况 |
> | 行动 | Microsoft.OffAzure/VMwareSites/vcenters/read | 获取 VMware vCenter 的属性 |
> | 行动 | Microsoft.OffAzure/VMwareSites/vcenters/write | 创建或更新 VMware vCenter |
> | 行动 | Microsoft.OffAzure/VMwareSites/write | 创建或更新 VMware 站点 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.OperationalInsights/linkTargets/read | 列出不与 Azure 订阅关联的现有帐户。 要将此 Azure 订阅链接到工作区，请使用此操作在“创建工作区”操作的客户 ID 属性中返回的客户 ID。 |
> | 行动 | microsoft.operationalinsights/operations/read | 列出所有可用的 OperationalInsights Rest API 操作。 |
> | 行动 | microsoft.operationalinsights/注册/操作 | Rergisters 订阅。 |
> | 行动 | Microsoft.OperationalInsights/register/action | 将订阅注册到资源提供程序。 |
> | 行动 | microsoft.operationalinsights/注销/操作 | 取消注册订阅。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/analytics/query/action | 使用新引擎进行搜索。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | 获取搜索架构 V2。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/api/query/action | 使用新引擎进行搜索。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/api/query/schema/read | 获取搜索架构 V2。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | 删除配置范围 |
> | 行动 | Microsoft.OperationalInsights/workspaces/configurationScopes/read | 获取配置范围 |
> | 行动 | Microsoft.OperationalInsights/workspaces/configurationScopes/write | 设置配置范围 |
> | 行动 | Microsoft.OperationalInsights/workspaces/datasources/delete | 删除工作区下面的数据源。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/datasources/read | 获取工作区下面的数据源。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/datasources/write | 在工作区下面创建/更新数据源。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/delete | 删除工作区。 如果该工作区在创建时已链接到现有工作区，则不会删除它链接到的工作区。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/gateways/delete | 删除为工作区配置的网关。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | 为工作区生成注册证书。 此证书用于将 Microsoft System Center Operation Manager 连接到工作区。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | 为给定的工作区禁用智能包。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | 为给定的工作区启用智能包。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | 列出给定的工作区可见的所有智能包，并列出是为该工作区启用还是禁用了智能包。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/linkedServices/delete | 删除给定工作区下的链接服务。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/linkedServices/read | 获取给定工作区下的链接服务。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/linkedServices/write | 创建/更新给定工作区下的链接服务。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/listKeys/action | 检索工作区的列表密钥。 这些密钥用于将 Microsoft Operational Insights 代理连接到工作区。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/listKeys/read | 检索工作区的列表密钥。 这些密钥用于将 Microsoft Operational Insights 代理连接到工作区。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/managementGroups/read | 获取已连接到此工作区的 System Center Operations Manager 管理组的名称和元数据。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | 获取工作区下的指标定义 |
> | 行动 | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | 删除工作区的用户通知设置。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/notificationSettings/read | 获取工作区的用户通知设置。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/notificationSettings/write | 设置工作区的用户通知设置。 |
> | 行动 | microsoft.operationalinsights/工作区/操作/读取 | 获取 Microsoft.operationalinsights 工作区操作的状态。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/purge/action | 从工作区中删除指定数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/AADDomainServicesAccountLogon/读取 | 从 AADDomainServicesAccountLogon 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/AADDomainServicesAccountManagement/读取 | 从 AADDomainServicesAccountManagement 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/AADDomainServicesDirectoryServiceAccess/读取 | 从 AADDomainServicesDirectoryServiceAccess 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/AADDomainServicesLogonLogoff/读取 | 从 AADDomainServicesLogonLogoff 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/AADDomainServicesPolicyChange/读取 | 从 AADDomainServicesPolicyChange 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/AADDomainServicesPrivilegeUse/读取 | 从 AADDomainServicesPrivilegeUse 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/AADDomainServicesSystemSecurity/读取 | 从 AADDomainServicesSystemSecurity 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | 从 ADAssessmentRecommendation 表读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/AddonAzureBackupAlerts/读取 | 从 AddonAzureBackupAlerts 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/AddonAzureBackupJobs/读取 | 从 AddonAzureBackupJobs 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/AddonAzureBackupPolicy/读取 | 从 AddonAzureBackupPolicy 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/AddonAzureBackupProtectedInstance/读取 | 从 AddonAzureBackupProtectedInstance 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/AddonAzureBackupStorage/读取 | 从 AddonAzureBackupStorage 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/ADFActivityRun/读取 | 从 ADFActivityRun 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/ADFPipelineRun/读取 | 从 ADFPipelineRun 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/ADFTriggerRun/读取 | 从 ADFTriggerRun 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | 从 ADReplicationResult 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | 从 ADSecurityAssessmentRecommendation 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/Alert/read | 从 Alert 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | 从 AlertHistory 表读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/AmlComputeClusterEvent/读取 | 从 AmlComputeClusterEvent 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/AmlComputeClusterNodeEvent/读取 | 从 AmlComputeClusterNodeEvent 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/AmlComputeJobEvent/读取 | 从 AmlComputeJobEvent 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/ApiManagementGatewayLogs/读取 | 从 ApiManagementGatewayLogs 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | 从 AppCenterError 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | 从 ApplicationInsights 表读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/AppPlatformLogsforSpring/读取 | 从 AppPlatformLogsforSpring 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/AppServiceEnvironmentPlatformLogs/读取 | 从 AppServiceEnvironmentPlatformLogs 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | 从 AuditLogs 表读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/AutoscaleEvaluationsLog/读取 | 从 AutoscaleEvaluationsLog 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/AutoscaleScaleActionsLog/读取 | 从 AutoscaleScaleActionsLog 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/AWSCloudTrail/读取 | 从 AWSCloudTrail 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | 从 AzureActivity 表读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/AzureAssessmentRecommendation/读取 | 从 AzureAssessmentRecommendation 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | 从 AzureMetrics 表读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/BaiClusterEvent/读取 | 从 BaiClusterEvent 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/BaiClusterNodeEvent/读取 | 从 BaiClusterNodeEvent 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/BaiJobEvent/读取 | 从 BaiJobEvent 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/BlockchainApplicationLog/读取 | 从 BlockchainApplicationLog 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/BlockchainProxyLog/读取 | 从 BlockchainProxyLog 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | 从 BoundPort 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | 从 CommonSecurityLog 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | 从 ComputerGroup 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | 从 ConfigurationChange 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | 从 ConfigurationData 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | 从 ContainerImageInventory 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | 从 ContainerInventory 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | 从 ContainerLog 表读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/ContainerNodeInventory/读取 | 从 ContainerNodeInventory 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/ContainerRegistryLoginEvents/读取 | 从 ContainerRegistryLoginEvents 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/ContainerRegistryRepositoryEvents/读取 | 从 ContainerRegistryRepositoryEvents 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | 从 ContainerServiceLog 表读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/CoreAzureBackup/读取 | 从 CoreAzureBackup 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/DatabricksAccounts/读取 | 从 DatabricksAccounts 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/DatabricksClusters/读取 | 从 DatabricksClusters 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/DatabricksDBFS/读取 | 从 DatabricksDBFS 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/DatabricksInstancePools/读取 | 从 DatabricksInstancePools 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/DatabricksJobs/读取 | 从 DatabricksJobs 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/DatabricksNotebook/读取 | 从 DatabricksNotebook 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/DatabricksSecrets/读取 | 从 DatabricksSecrets 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/DatabricksSQLPermissions/读取 | 从 DatabricksSQLPermissions 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/DatabricksSSH/读取 | 从 DatabricksSSH 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/DatabricksTables/读取 | 从 DatabricksTables 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/DatabricksWorkspace/读取 | 从 DatabricksWorkspace 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | 从 DeviceAppCrash 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | 从 DeviceAppLaunch 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | 从 DeviceCalendar 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | 从 DeviceCleanup 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | 从 DeviceConnectSession 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | 从 DeviceEtw 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | 从 DeviceHardwareHealth 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | 从 DeviceHealth 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | 从 DeviceHeartbeat 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | 从 DeviceSkypeHeartbeat 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | 从 DeviceSkypeSignIn 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | 从 DeviceSleepState 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | 从 DHAppFailure 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | 从 DHAppReliability 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | 从 DHDriverReliability 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | 从 DHLogonFailures 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | 从 DHLogonMetrics 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | 从 DHOSCrashData 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | 从 DHOSReliability 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | 从 DHWipAppLearning 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | 从 DnsEvents 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | 从 DnsInventory 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | 从 ETWEvent 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/Event/read | 从 Event 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | 从 ExchangeAssessmentRecommendation 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | 从 ExchangeOnlineAssessmentRecommendation 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/FailedIngestion/读取 | 从 FailedIngestion 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | 从 Heartbeat 表读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/HuntingBookmark/读取 | 从 HuntingBookmark 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | 从 IISAssessmentRecommendation 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | 从 InboundConnection 表读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/InsightsMetrics/读取 | 从 InsightsMetrics 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | 从 IntuneAuditLogs 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | 从 IntuneOperationalLogs 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | 从 KubeEvents 表读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/KubeHealth/读取 | 从 KubeHealth 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | 从 KubeNodeInventory 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | 从 KubePodInventory 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | 从 KubeServices 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | 从 LinuxAuditLog 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | 从 MAApplication 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | 从 MAApplicationHealth 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | 从 MAApplicationHealthAlternativeVersions 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | 从 MAApplicationHealthIssues 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | 从 MAApplicationInstance 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | 从 MAApplicationInstanceReadiness 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | 从 MAApplicationReadiness 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | 从 MADeploymentPlan 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MADevice/read | 从 MADevice 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | 从 MADeviceNotEnrolled 表读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/MADeviceNRT/读取 | 从 MADeviceNRT 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | 从 MADevicePnPHealth 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | 从 MADevicePnPHealthAlternativeVersions 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | 从 MADevicePnPHealthIssues 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | 从 MADeviceReadiness 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | 从 MADriverInstanceReadiness 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | 从 MADriverReadiness 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | 从 MAOfficeAddin 表读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/MAOfficeAddinEntityHealth/读取 | 从 MAOfficeAddinEntityHealth 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | 从 MAOfficeAddinHealth 表读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/MAOfficeAddinHealthEventNRT/读取 | 从 MAOfficeAddinHealthEventNRT 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | 从 MAOfficeAddinHealthIssues 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | 从 MAOfficeAddinInstance 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | 从 MAOfficeAddinInstanceReadiness 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | 从 MAOfficeAddinReadiness 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | 从 MAOfficeApp 表读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/MAOfficeAppCrashesNRT/读取 | 从 MAOfficeAppCrashesNRT 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | 从 MAOfficeAppHealth 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | 从 MAOfficeAppInstance 表读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/MAOfficeAppInstanceHealth/读取 | 从 MAOfficeAppInstanceHealth 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | 从 MAOfficeAppReadiness 表读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/MAOfficeAppSessionsNRT/读取 | 从 MAOfficeAppSessionsNRT 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | 从 MAOfficeBuildInfo 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | 从 MAOfficeCurrencyAssessment 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | 从 MAOfficeCurrencyAssessmentDailyCounts 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | 从 MAOfficeDeploymentStatus 表读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/MAOfficeDeploymentStatusNRT/读取 | 从 MAOfficeDeploymentStatusNRT 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/MAOfficeMacroErrorNRT/读取 | 从 MAOfficeMacroErrorNRT 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/MAOfficeMacroGlobalHealth/读取 | 从 MAOfficeMacroGlobalHealth 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | 从 MAOfficeMacroHealth 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | 从 MAOfficeMacroHealthIssues 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | 从 MAOfficeMacroIssueInstanceReadiness 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | 从 MAOfficeMacroIssueReadiness 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | 从 MAOfficeMacroSummary 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | 从 MAOfficeSuite 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | 从 MAOfficeSuiteInstance 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAProposedPilotDevices/read | 从 MAProposedPilotDevices 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | 从 MAWindowsBuildInfo 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | 从 MAWindowsCurrencyAssessment 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | 从 MAWindowsCurrencyAssessmentDailyCounts 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | 从 MAWindowsDeploymentStatus 表读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/MAWindowsDeploymentStatusNRT/读取 | 从 MAWindowsDeploymentStatusNRT 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | 从 MAWindowsSysReqInstanceReadiness 表读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/McasShadowItReporting/读取 | 从 McasShadowItReporting 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/MicrosoftDataShareShareLog/读取 | 从 MicrosoftDataShareShareLog 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/MicrosoftHealthcareApisAuditLogs/读取 | 从 MicrosoftHealthcareApisAuditLogs 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/MicrosoftInsightsAzureActivityLog/读取 | 从 MicrosoftInsightsAzureActivityLog 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/MicrosoftWebApplicationLog/读取 | 从 MicrosoftWebApplicationLog 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/MicrosoftWebFunctionExecutionLogs/读取 | 从 MicrosoftWebFunctionExecutionLogs 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/MicrosoftWebStdOutStdErrLog/读取 | 从 MicrosoftWebStdOutStdErrLog 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/MicrosoftWebW3CLog/读取 | 从 MicrosoftWebW3CLog 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | 从 NetworkMonitoring 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | 从 OfficeActivity 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/Operation/read | 从 Operation 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | 从 OutboundConnection 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/Perf/read | 从 Perf 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | 从 ProtectionStatus 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/read | 基于工作区中的数据运行查询 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/ReservedAzureCommonFields/read | 从 ReservedAzureCommonFields 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | 从 ReservedCommonFields 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | 从 SCCMAssessmentRecommendation 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | 从 SCOMAssessmentRecommendation 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | 从 SecurityAlert 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | 从 SecurityBaseline 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | 从 SecurityBaselineSummary 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | 从 SecurityDetection 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | 从 SecurityEvent 表读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/SecurityIoTRawEvent/读取 | 从 SecurityIoTRawEvent 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/SecurityRecommendation/读取 | 从 SecurityRecommendation 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | 从 ServiceFabricOperationalEvent 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | 从 ServiceFabricReliableActorEvent 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | 从 ServiceFabricReliableServiceEvent 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | 从 SfBAssessmentRecommendation 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | 从 SfBOnlineAssessmentRecommendation 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | 从 SharePointOnlineAssessmentRecommendation 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | 从 SigninLogs 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | 从 SPAssessmentRecommendation 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | 从 SQLAssessmentRecommendation 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | 从 SQLQueryPerformance 表读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/SqlThreatProtectionLoginAudits/读取 | 从 SqlThreatProtectionLoginAudits 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/SqlVulnerabilityAssessmentResult/读取 | 从 SqlVulnerabilityAssessmentResult 表中读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/SucceededIngestion/读取 | 从 SucceededIngestion 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/Syslog/read | 从 Syslog 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | 从 SysmonEvent 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | 从任何自定义日志读取数据 |
> | 行动 | Microsoft.operationalinsights/工作区/查询/ThreatIntelligenceIndicator/读取 | 从 ThreatIntelligenceIndicator 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/UAApp/read | 从 UAApp 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | 从 UAComputer 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/UAComputerRank/read | 从 UAComputerRank 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/UADriver/read | 从 UADriver 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | 从 UADriverProblemCodes 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | 从 UAFeedback 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | 从 UAHardwareSecurity 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | 从 UAIESiteDiscovery 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | 从 UAOfficeAddIn 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | 从 UAProposedActionPlan 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | 从 UASysReqIssue 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | 从 UAUpgradedComputer 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/Update/read | 从 Update 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | 从 UpdateRunProgress 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | 从 UpdateSummary 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/Usage/read | 从 Usage 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | 从 VMBoundPort 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | 从 VMConnection 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | 从 W3CIISLog 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | 从 WaaSDeploymentStatus 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | 从 WaaSInsiderStatus 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | 从 WaaSUpdateStatus 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | 从 WDAVStatus 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | 从 WDAVThreat 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | 从 WindowsClientAssessmentRecommendation 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | 从 WindowsEvent 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | 从 WindowsFirewall 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | 从 WindowsServerAssessmentRecommendation 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/WireData/read | 从 WireData 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | 从 WorkloadMonitoringPerf 表中读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | 从 WUDOAggregatedStatus 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | 从 WUDOStatus 表读取数据 |
> | 行动 | Microsoft.OperationalInsights/workspaces/read | 获取现有工作区 |
> | 行动 | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | 重新生成指定的工作区共享密钥 |
> | 行动 | microsoft.operationalinsights/workspaces/rules/read | 获取所有警报规则。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/savedSearches/delete | 删除保存的搜索查询 |
> | 行动 | Microsoft.OperationalInsights/workspaces/savedSearches/read | 获取保存的搜索查询 |
> | 行动 | microsoft.operationalinsights/workspaces/savedsearches/results/read | 获取保存的搜索结果。 已放弃 |
> | 行动 | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/delete | 删除计划的搜索操作。 |
> | 行动 | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/read | 获取计划的搜索操作。 |
> | 行动 | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | 创建或更新计划的搜索操作。 |
> | 行动 | microsoft.operationalinsights/workspaces/savedsearches/schedules/delete | 删除计划的搜索。 |
> | 行动 | microsoft.operationalinsights/workspaces/savedsearches/schedules/read | 获取计划的搜索。 |
> | 行动 | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | 创建或更新计划的搜索。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/savedSearches/write | 创建保存的搜索查询 |
> | 行动 | Microsoft.OperationalInsights/workspaces/schema/read | 获取工作区的搜索架构。  搜索架构包含公开的字段及其类型。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/search/action | 执行搜索查询 |
> | 行动 | microsoft.operationalinsights/workspaces/search/read | 获取搜索结果。 已弃用。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/sharedKeys/action | 检索工作区的共享密钥。 这些密钥用于将 Microsoft Operational Insights 代理连接到工作区。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/sharedKeys/read | 检索工作区的共享密钥。 这些密钥用于将 Microsoft Operational Insights 代理连接到工作区。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | 删除存储配置。 这会阻止 Microsoft Operational Insights 从存储帐户中读取数据。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | 获取存储配置。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | 创建新的存储配置。 这些配置用于从现有存储帐户中的某个位置提取数据。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | 获取工作区的搜索升级转换失败日志 |
> | 行动 | Microsoft.OperationalInsights/workspaces/usages/read | 获取工作区的使用情况数据，包括工作区读取的数据量。 |
> | 行动 | Microsoft.OperationalInsights/workspaces/write | 创建新的工作区，或者通过提供现有工作区中的客户 ID 链接到现有工作区。 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.OperationsManagement/managementAssociations/delete | 删除现有管理关联 |
> | 行动 | Microsoft.OperationsManagement/managementAssociations/read | 获取现有管理关联 |
> | 行动 | Microsoft.OperationsManagement/managementAssociations/write | 创建新的管理关联 |
> | 行动 | Microsoft.OperationsManagement/managementConfigurations/delete | 删除现有管理配置 |
> | 行动 | Microsoft.OperationsManagement/managementConfigurations/read | 获取现有管理配置 |
> | 行动 | Microsoft.OperationsManagement/managementConfigurations/write | 创建新的管理配置 |
> | 行动 | Microsoft.OperationsManagement/register/action | 将订阅注册到资源提供程序。 |
> | 行动 | Microsoft.OperationsManagement/solutions/delete | 删除现有的 OMS 解决方案 |
> | 行动 | Microsoft.OperationsManagement/solutions/read | 获取现有的 OMS 解决方案 |
> | 行动 | Microsoft.OperationsManagement/solutions/write | 创建新的 OMS 解决方案 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.PolicyInsights/asyncOperationResults/read | 获取异步操作结果。 |
> | DataAction | PolicyInsights/checkDataPolicyCompliance/action | 对照数据策略检查给定组件的符合性状态。 |
> | 行动 | PolicyInsights/操作/读取 | 获取 PolicyInsights 命名空间上支持的操作 |
> | DataAction | PolicyInsights/policyEvents/logDataEvents/action | 记录资源组件策略事件。 |
> | 行动 | Microsoft.PolicyInsights/policyEvents/queryResults/action | 查询有关策略事件的信息。 |
> | 行动 | Microsoft.PolicyInsights/policyEvents/queryResults/read | 查询有关策略事件的信息。 |
> | 行动 | Microsoft.PolicyInsights/policyStates/queryResults/action | 查询有关策略状态的信息。 |
> | 行动 | Microsoft.PolicyInsights/policyStates/queryResults/read | 查询有关策略状态的信息。 |
> | 行动 | Microsoft.PolicyInsights/policyStates/summarize/action | 查询有关策略最新状态的摘要信息。 |
> | 行动 | Microsoft.PolicyInsights/policyStates/summarize/read | 查询有关策略最新状态的摘要信息。 |
> | 行动 | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | 为所选范围触发新的符合性评估。 |
> | 行动 | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | 查询有关 DeployIfNotExists 策略所需资源的信息 |
> | 行动 | Microsoft.PolicyInsights/register/action | 注册 Microsoft Policy Insights 资源提供程序，并对其启用操作。 |
> | 行动 | Microsoft.PolicyInsights/remediations/cancel/action | 取消正在进行的 Microsoft Policy 修正。 |
> | 行动 | Microsoft.PolicyInsights/remediations/delete | 删除策略修正。 |
> | 行动 | Microsoft.PolicyInsights/remediations/listDeployments/read | 列出策略修正所需的部署。 |
> | 行动 | Microsoft.PolicyInsights/remediations/read | 获取策略修正。 |
> | 行动 | Microsoft.PolicyInsights/remediations/write | 创建或更新 Microsoft Policy 修正。 |
> | 行动 | PolicyInsights/注销/操作 | 取消注册 Microsoft Policy Insights 资源提供程序。 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Portal/consoles/delete | 删除 Cloud Shell 实例。 |
> | 行动 | Microsoft 门户/控制台/读取 | 读取 Cloud Shell 实例。 |
> | 行动 | Microsoft.Portal/consoles/write | 创建或更新 Cloud Shell 的实例。 |
> | 行动 | Microsoft.Portal/dashboards/delete | 从订阅删除仪表板。 |
> | 行动 | Microsoft.Portal/dashboards/read | 读取订阅的仪表板。 |
> | 行动 | Microsoft.Portal/dashboards/write | 向订阅添加仪表板或修改仪表板。 |
> | 行动 | Microsoft.Portal/register/action | 注册到门户 |
> | 行动 | Microsoft.Portal/usersettings/delete | 删除 Cloud Shell 的用户设置。 |
> | 行动 | Microsoft.Portal/usersettings/read | 读取 Cloud Shell 的用户设置。 |
> | 行动 | Microsoft.Portal/usersettings/write | 创建或更新 Cloud Shell 的用户设置。 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.PowerBIDedicated/capacities/delete | 删除 Power BI 专用容量。 |
> | 行动 | Microsoft.PowerBIDedicated/capacities/read | 检索指定 Power BI 专用容量的信息。 |
> | 行动 | Microsoft.PowerBIDedicated/capacities/resume/action | 恢复容量。 |
> | 行动 | Microsoft.PowerBIDedicated/capacities/skus/read | 检索容量的可用 SKU 信息 |
> | 行动 | Microsoft.PowerBIDedicated/capacities/suspend/action | 暂停容量。 |
> | 行动 | Microsoft.PowerBIDedicated/capacities/write | 创建或更新指定的 Power BI 专用容量。 |
> | 行动 | PowerBIDedicated/位置/checkNameAvailability/action | 检查给定的 Power BI 专用容量名称是否有效且未被使用。 |
> | 行动 | Microsoft.PowerBIDedicated/locations/operationresults/read | 检索指定操作结果的信息。 |
> | 行动 | Microsoft.PowerBIDedicated/locations/operationstatuses/read | 检索指定操作状态的信息。 |
> | 行动 | Microsoft.PowerBIDedicated/operations/read | 检索操作的信息 |
> | 行动 | Microsoft.PowerBIDedicated/register/action | 注册 Power BI 专用资源提供程序。 |
> | 行动 | Microsoft.PowerBIDedicated/skus/read | 检索 SKU 的信息 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp 是服务使用的内部操作 |
> | 行动 | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp 是服务使用的内部操作 |
> | 行动 | Microsoft.RecoveryServices/Locations/backupPreValidateProtection/action |  |
> | 行动 | Microsoft.recoveryservices/位置/backupProtectedItem/写入 | 创建备份受保护项 |
> | 行动 | Microsoft.recoveryservices/位置/backupProtectedItems/读取 | 返回所有受保护项的列表。 |
> | 行动 | Microsoft.RecoveryServices/Locations/backupStatus/action | 检查恢复服务保管库的备份状态 |
> | 行动 | Microsoft.RecoveryServices/Locations/backupValidateFeatures/action | 验证功能 |
> | 行动 | Microsoft.RecoveryServices/locations/checkNameAvailability/action | “检查资源名称性”是一个 API，用于检查资源名称是否可用 |
> | 行动 | Microsoft.RecoveryServices/locations/operationStatus/read | 获取给定操作的操作状态 |
> | 行动 | Microsoft.RecoveryServices/operations/read | 操作返回资源提供程序的操作列表 |
> | 行动 | Microsoft.RecoveryServices/register/action | 注册给定资源提供程序的订阅 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupconfig/read | 返回恢复服务保管库的配置。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupconfig/write | 更新恢复服务保管库的配置。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupEngines/read | 返回使用保管库注册的所有备份管理服务器。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/delete | 删除备份保护意向 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | 获取备份保护意向 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | 创建备份保护意向 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | 返回操作状态 |
> | 行动 | Microsoft.recoveryservices/保管库/backupFabrics/operationsStatus/read | 返回操作状态 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | 获取所有可保护的容器 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | 删除已注册的容器 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | 在容器内进行工作负载的查询 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | 获取容器中的所有项 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | 获取对保护容器执行的操作的结果。 |
> | 行动 | Microsoft.recoveryservices/保管库/backupFabrics/protectionContainers/operationsStatus/read | 获取对保护容器执行的操作的状态。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | 对受保护的项执行备份。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | 删除受保护的项 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 获取对受保护项执行的操作的结果。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 返回对受保护项执行的操作的状态。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | 返回受保护项的对象详细信息 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | 预配受保护项的即时项恢复 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 获取受保护项的恢复点。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | 还原受保护项的恢复点。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | 吊销受保护项的即时项恢复 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | 创建备份受保护项 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | 返回所有已注册的容器 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | 创建已注册的容器 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | 刷新容器列表 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | 取消作业 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | 返回作业操作的结果。 |
> | 行动 | Microsoft.recoveryservices/保管库/backupJobs/operationsStatus/read | 返回作业操作的状态。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupJobs/read | 返回所有作业对象 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | 导出作业 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | 返回恢复服务保管库的备份操作结果。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupOperations/read | 返回恢复服务保管库的备份操作状态。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | 删除保护策略 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | 获取策略操作的结果。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | 获取策略操作的状态。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupPolicies/read | 返回所有保护策略 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupPolicies/write | 创建保护策略 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | 返回所有可保护项的列表。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | 返回所有受保护项的列表。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | 返回属于订阅的所有容器 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | 列出所有备份保护意向 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | 返回恢复服务保管库的安全 PIN 信息。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | 返回恢复服务保管库的存储配置。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | 更新恢复服务保管库的存储配置。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | 返回恢复服务的受保护项和受保护服务器的摘要。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | 验证对受保护项的操作 |
> | 行动 | Microsoft.RecoveryServices/Vaults/certificates/write | “更新资源证书”操作更新资源/保管库凭据证书。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/delete | “删除保管库”操作删除“vault”类型的指定 Azure 资源 |
> | 行动 | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | “获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象的扩展信息 |
> | 行动 | Microsoft.RecoveryServices/Vaults/extendedInformation/read | “获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象的扩展信息 |
> | 行动 | Microsoft.RecoveryServices/Vaults/extendedInformation/write | “获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象的扩展信息 |
> | 行动 | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | 获取恢复服务保管库的警报。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | 解决警报。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | 获取恢复服务保管库通知配置。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | 配置到恢复服务保管库的电子邮件通知。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/read | “获取保管库”操作获取表示“vault”类型的 Azure 资源的对象 |
> | 行动 | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | “取消注册容器”操作可用于取消注册容器。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | “获取操作结果”操作可用于获取异步提交的操作的操作状态和结果 |
> | 行动 | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | “获取容器”操作可用于获取针对资源注册的容器。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | “注册服务容器”操作可用于向恢复服务注册容器。 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | 读取任何警报设置 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | 创建或更新任何警报设置 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationEvents/read | 读取任何事件 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | 检查结构的一致性 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | 删除任何结构 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | 部署进程服务器映像 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | 将结构迁移到 AAD |
> | 行动 | Microsoft.recoveryservices/保管库/replicationFabrics/operationresults/read | 跟踪资源构造上异步操作的结果 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/read | 读取任何结构 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | 重新关联网关 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | 删除结构 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | 续订 Fabric 的证书 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | 读取任何逻辑网络 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | 读取任何网络 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | 删除任何网络映射 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | 读取任何网络映射 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | 创建或更新任何网络映射 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | 发现可保护项 |
> | 行动 | Microsoft.recoveryservices/保管库/replicationFabrics/replicationProtectionContainers/operationresults/read | 跟踪资源保护容器上异步操作的结果 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | 读取任何保护容器 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | 删除保护容器 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | 删除任何迁移项 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | 迁移项 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | 读取任何迁移恢复点 |
> | 行动 | Microsoft.recoveryservices/保管库/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/operationresults/read | 跟踪对资源迁移项的异步操作的结果 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | 读取任何迁移项 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | 测试迁移 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | 测试迁移清理 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | 创建或更新任何迁移项 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 读取任何可保护项 |
> | 行动 | Microsoft.recoveryservices/保管库/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | 添加磁盘 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | 应用还原点 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | 删除任何受保护的项 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | 故障转移提交 |
> | 行动 | Microsoft.recoveryservices/保管库/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/operationresults/read | 跟踪对受保护的资源上的异步操作的结果 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | 计划内故障转移 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 读取任何受保护项 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | 读取任何复制恢复点 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | 删除受保护的项 |
> | 行动 | Microsoft.recoveryservices/保管库/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/action | 删除磁盘 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | 修复复制 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | 重新保护受保护的项 |
> | 行动 | Microsoft.recoveryservices/保管库/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/action |  |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | 提交反馈 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | 读取任何目标计算大小 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | 测试故障转移 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | 测试故障转移清理 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | 故障转移： |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | 更新移动服务 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | 创建或更新任何受保护的项 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | 删除任何保护容器映射 |
> | 行动 | Microsoft.recoveryservices/保管库/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/operationresults/read | 跟踪资源保护容器映射的异步操作结果 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 读取任何保护容器映射 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | 删除保护容器映射 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | 创建或更新任何保护容器映射 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | 交换保护容器 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | 创建或更新任何保护容器 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | 删除任何恢复服务提供程序 |
> | 行动 | Microsoft.recoveryservices/保管库/replicationFabrics/replicationRecoveryServicesProviders/operationresults/read | 跟踪资源恢复服务提供程序上异步操作的结果 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | 读取任何恢复服务提供程序 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | 刷新提供程序 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | 删除恢复服务提供程序 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | 创建或更新任何恢复服务提供程序 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | 读取任何存储分类 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | 删除任何存储分类映射 |
> | 行动 | Microsoft.recoveryservices/保管库/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/operationresults/read | 跟踪资源存储分类映射的异步操作结果 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | 读取任何存储分类映射 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | 创建或更新任何存储分类映射 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | 删除任何 vCenter |
> | 行动 | Microsoft.recoveryservices/保管库/replicationFabrics/replicationvCenters/operationresults/read | 跟踪资源 Vcenter 上的异步操作结果 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | 读取任何 vCenter |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | 创建或更新任何 vCenter |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationFabrics/write | 创建或更新任何结构 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | 取消作业 |
> | 行动 | Microsoft.recoveryservices/保管库/replicationJobs/operationresults/read | 跟踪对资源作业的异步操作的结果 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationJobs/read | 读取任何作业 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | 重新启动作业 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | 恢复作业 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | 读取任何迁移项 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | 读取任何网络映射 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationNetworks/read | 读取任何网络 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | 删除任何策略 |
> | 行动 | Microsoft.recoveryservices/保管库/replicationPolicies/operationresults/read | 跟踪对资源策略的异步操作的结果 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationPolicies/read | 读取任何策略 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationPolicies/write | 创建或更新任何策略 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | 读取任何受保护项 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | 读取任何保护容器映射 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | 读取任何保护容器 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | 删除任何恢复计划 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | 故障转移提交恢复计划 |
> | 行动 | Microsoft.recoveryservices/保管库/replicationRecoveryPlans/operationresults/read | 跟踪资源恢复计划的异步操作结果 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | 计划内故障转移恢复计划 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | 读取任何恢复计划 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | 重新保护恢复计划 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | 测试故障转移恢复计划 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | 测试故障转移清理恢复计划 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | 故障转移恢复计划 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | 创建或更新任何恢复计划 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | 读取任何恢复服务提供程序 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | 读取任何存储分类映射 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | 读取任何存储分类 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | 读取任何内容  |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationUsages/read | 读取任何保管库复制使用情况 |
> | 行动 | Microsoft.recoveryservices/保管库/replicationVaultHealth/operationresults/read | 跟踪资源保管库复制运行状况的异步操作结果 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | 读取任何保管库复制运行状况 |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | 刷新保管库运行状况 |
> | 行动 | Microsoft.recoveryservices/保管库/replicationVaultSettings/read | 读取任何内容  |
> | 行动 | Microsoft.recoveryservices/保管库/replicationVaultSettings/write | 创建或更新任何  |
> | 行动 | Microsoft.RecoveryServices/vaults/replicationvCenters/read | 读取任何 vCenter |
> | 行动 | Microsoft.RecoveryServices/vaults/usages/read | 读取任何保管库使用情况 |
> | 行动 | Microsoft.RecoveryServices/Vaults/usages/read | 返回恢复服务保管库的使用情况详细信息。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/vaultTokens/read | “保管库令牌”操作可用于获取保管库级后端操作的保管库令牌。 |
> | 行动 | Microsoft.RecoveryServices/Vaults/write | “创建保管库”操作创建“vault”类型的 Azure 资源 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Relay/checkNameAvailability/action | 检查给定订阅下的命名空间可用性。 |
> | 行动 | Microsoft.Relay/checkNamespaceAvailability/action | 检查给定订阅下的命名空间可用性。 已弃用此 API，请改用 CheckNameAvailability。 |
> | 行动 | Microsoft.Relay/namespaces/authorizationRules/action | 更新命名空间授权规则。 已弃用此 API。 请改为使用 PUT 调用来更新命名空间授权规则。 不支持对 API 版本 2017-04-01 执行此操作。 |
> | 行动 | Microsoft.Relay/namespaces/authorizationRules/delete | 删除命名空间授权规则。 无法删除默认的命名空间授权规则。  |
> | 行动 | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | 获取命名空间的连接字符串 |
> | 行动 | Microsoft.Relay/namespaces/authorizationRules/read | 获取命名空间授权规则说明列表。 |
> | 行动 | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | 再生成资源的主密钥或辅助密钥 |
> | 行动 | Microsoft.Relay/namespaces/authorizationRules/write | 创建命名空间级授权规则并更新其属性。 可以更新授权规则访问权限、主密钥和辅助密钥。 |
> | 行动 | Microsoft.Relay/namespaces/Delete | 删除命名空间资源 |
> | 行动 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | 获取灾难恢复主命名空间的授权规则密钥 |
> | 行动 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | 获取灾难恢复主命名空间的授权规则 |
> | 行动 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | 禁用灾难恢复，并停止从主命名空间向辅助命名空间复制更改。 |
> | 行动 | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 检查给定订阅下命名空间别名的可用性。 |
> | 行动 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | 删除与命名空间关联的灾难恢复配置。 只能通过主命名空间调用此操作。 |
> | 行动 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | 调用异地灾难恢复故障转移并将命名空间别名重新配置为指向辅助命名空间。 |
> | 行动 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | 获取与命名空间关联的灾难恢复配置。 |
> | 行动 | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | 创建或更新与命名空间关联的灾难恢复配置。 |
> | 行动 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | 更新 HybridConnection 的操作。 不支持对 API 版本 2017-04-01 执行此操作。 授权规则。 请使用 PUT 调用来更新授权规则。 |
> | 行动 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | 用于删除 HybridConnection 授权规则的操作 |
> | 行动 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | 获取 HybridConnection 的连接字符串 |
> | 行动 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  获取 HybridConnection 授权规则的列表 |
> | 行动 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | 再生成资源的主密钥或辅助密钥 |
> | 行动 | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | 创建 HybridConnection 授权规则并更新其属性。 可以更新授权规则访问权限。 |
> | 行动 | Microsoft.Relay/namespaces/HybridConnections/Delete | 用于删除 HybridConnection 资源的操作 |
> | 行动 | Microsoft.Relay/namespaces/HybridConnections/read | 获取 HybridConnection 资源说明列表 |
> | 行动 | Microsoft.Relay/namespaces/HybridConnections/write | 创建或更新 HybridConnection 属性。 |
> | 行动 | Microsoft.Relay/namespaces/messagingPlan/read | 获取命名空间的消息传递计划。<br>已弃用此 API。<br>在以后的 API 版本中，通过 MessagingPlan 资源公开的属性将移动到（父）命名空间资源。<br>不支持对 API 版本 2017-04-01 执行此操作。 |
> | 行动 | Microsoft.Relay/namespaces/messagingPlan/write | 更新命名空间的消息传递计划。<br>已弃用此 API。<br>在以后的 API 版本中，通过 MessagingPlan 资源公开的属性将移动到（父）命名空间资源。<br>不支持对 API 版本 2017-04-01 执行此操作。 |
> | 行动 | Microsoft.Relay/namespaces/operationresults/read | 获取命名空间操作的状态 |
> | 行动 | Microsoft. 中继/命名空间/提供程序/diagnosticSettings/读取 | 获取命名空间诊断设置资源说明列表 |
> | 行动 | Microsoft. 中继/命名空间/提供程序/diagnosticSettings/写入 | 获取命名空间诊断设置资源说明列表 |
> | 行动 | Microsoft. 中继/命名空间/提供程序/logDefinitions/读取 | 获取命名空间日志资源说明列表 |
> | 行动 | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 获取命名空间指标资源说明列表 |
> | 行动 | Microsoft.Relay/namespaces/read | 获取命名空间资源说明列表 |
> | 行动 | Microsoft.Relay/namespaces/removeAcsNamepsace/action | 删除 ACS 命名空间 |
> | 行动 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | 更新 WcfRelay 的操作。 不支持对 API 版本 2017-04-01 执行此操作。 授权规则。 请使用 PUT 调用来更新授权规则。 |
> | 行动 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | 用于删除 WcfRelay 授权规则的操作 |
> | 行动 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | 获取 WcfRelay 的连接字符串 |
> | 行动 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  获取 WcfRelay 授权规则的列表 |
> | 行动 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | 再生成资源的主密钥或辅助密钥 |
> | 行动 | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | 创建 WcfRelay 授权规则并更新其属性。 可以更新授权规则访问权限。 |
> | 行动 | Microsoft.Relay/namespaces/WcfRelays/Delete | 用于删除 WcfRelay 资源的操作 |
> | 行动 | Microsoft.Relay/namespaces/WcfRelays/read | 获取 WcfRelay 资源说明列表 |
> | 行动 | Microsoft.Relay/namespaces/WcfRelays/write | 创建或更新 WcfRelay 属性。 |
> | 行动 | Microsoft.Relay/namespaces/write | 创建命名空间资源并更新其属性。 命名空间的标记和容量是可更新的属性。 |
> | 行动 | Microsoft.Relay/operations/read | 获取操作 |
> | 行动 | Microsoft.Relay/register/action | 注册中继资源提供程序的订阅，并启用中继资源的创建 |
> | 行动 | Microsoft.Relay/unregister/action | 注册中继资源提供程序的订阅，并启用中继资源的创建 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | 获取指定资源的可用性状态 |
> | 行动 | Microsoft.ResourceHealth/AvailabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | 行动 | Microsoft.ResourceHealth/events/read | 获取给定订阅的服务运行状况事件 |
> | 行动 | Microsoft.Resourcehealth/healthevent/action | 表示指定资源的运行状况状态的更改 |
> | 行动 | Microsoft.Resourcehealth/healthevent/Activated/action | 表示指定资源的运行状况状态的更改 |
> | 行动 | Microsoft.Resourcehealth/healthevent/InProgress/action | 表示指定资源的运行状况状态的更改 |
> | 行动 | Microsoft.Resourcehealth/healthevent/Pending/action | 表示指定资源的运行状况状态的更改 |
> | 行动 | Microsoft.Resourcehealth/healthevent/Resolved/action | 表示指定资源的运行状况状态的更改 |
> | 行动 | Microsoft.Resourcehealth/healthevent/Updated/action | 表示指定资源的运行状况状态的更改 |
> | 行动 | Microsoft.ResourceHealth/impactedResources/read | 获取给定订阅中受影响的资源 |
> | 行动 | ResourceHealth/metadata/read | 获取元数据 |
> | 行动 | Microsoft.ResourceHealth/Operations/read | 获取可用于 Microsoft ResourceHealth 的操作 |
> | 行动 | Microsoft.ResourceHealth/register/action | 注册 Microsoft ResourceHealth 的订阅 |
> | 行动 | Microsoft.ResourceHealth/unregister/action | 取消注册 Microsoft ResourceHealth 的订阅 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | CalculateTemplateHash/操作 | 计算所提供模板的哈希值。 |
> | 行动 | Microsoft.Resources/checkPolicyCompliance/action | 参照资源策略检查给定资源的符合性状态。 |
> | 行动 | Microsoft.Resources/checkResourceName/action | 检查资源名称的有效性。 |
> | 行动 | Microsoft.Resources/deployments/cancel/action | 取消部署。 |
> | 行动 | Microsoft.Resources/deployments/delete | 删除部署。 |
> | 行动 | ExportTemplate/部署/操作 | 导出部署模板 |
> | 行动 | Microsoft.Resources/deployments/operations/read | 获取或列出部署操作。 |
> | 行动 | Microsoft .Resources/部署/operationstatuses/读取 | 获取或列出部署操作状态。 |
> | 行动 | Microsoft.Resources/deployments/read | 获取或列出部署。 |
> | 行动 | Microsoft.Resources/deployments/validate/action | 验证部署。 |
> | 行动 | Microsoft. 资源/部署/whatIf/操作 | 预测模板部署更改。 |
> | 行动 | Microsoft.Resources/deployments/write | 创建或更新部署。 |
> | 行动 | Microsoft.Resources/links/delete | 删除资源链接。 |
> | 行动 | Microsoft.Resources/links/read | 获取或列出资源链接。 |
> | 行动 | Microsoft.Resources/links/write | 创建或更新资源链接。 |
> | 行动 | Microsoft.Resources/marketplace/purchase/action | 从市场购买资源。 |
> | 行动 | Microsoft.Resources/providers/read | 获取提供程序的列表。 |
> | 行动 | Microsoft.Resources/resources/read | 基于筛选器获取资源的列表。 |
> | 行动 | Microsoft.Resources/subscriptions/locations/read | 获取支持的位置列表。 |
> | 行动 | Microsoft.Resources/subscriptions/operationresults/read | 获取订阅操作结果。 |
> | 行动 | Microsoft.Resources/subscriptions/providers/read | 获取或列出资源提供程序。 |
> | 行动 | Microsoft.Resources/subscriptions/read | 获取订阅的列表。 |
> | 行动 | Microsoft.Resources/subscriptions/resourceGroups/delete | 删除资源组及其所有资源。 |
> | 行动 | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | 获取或列出部署操作。 |
> | 行动 | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | 获取或列出部署操作状态。 |
> | 行动 | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | 获取或列出部署。 |
> | 行动 | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | 创建或更新部署。 |
> | 行动 | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | 将资源从一个资源组移到另一个资源组。 |
> | 行动 | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | 行动 | Microsoft.Resources/subscriptions/resourcegroups/resources/read | 获取资源组的资源。 |
> | 行动 | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | 验证是否已将资源从一个资源组移到另一个资源组。 |
> | 行动 | Microsoft.Resources/subscriptions/resourceGroups/write | 创建或更新资源组。 |
> | 行动 | Microsoft.Resources/subscriptions/resources/read | 获取订阅的资源。 |
> | 行动 | Microsoft.Resources/subscriptions/tagNames/delete | 删除订阅标记。 |
> | 行动 | Microsoft.Resources/subscriptions/tagNames/read | 获取或列出订阅标记。 |
> | 行动 | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | 删除订阅标记值。 |
> | 行动 | Microsoft.Resources/subscriptions/tagNames/tagValues/read | 获取或列出订阅标记值。 |
> | 行动 | Microsoft.Resources/subscriptions/tagNames/tagValues/write | 添加订阅标记值。 |
> | 行动 | Microsoft.Resources/subscriptions/tagNames/write | 添加订阅标记。 |
> | 行动 | Microsoft .Resources/标记/删除 | 删除资源上的所有标记。 |
> | 行动 | Microsoft .Resources/标记/读取 | 获取资源上的所有标记。 |
> | 行动 | Microsoft .Resources/标记/写入 | 通过将现有标记替换为一组新的标记或删除现有标记来更新资源上的标记。 |
> | 行动 | Microsoft.Resources/tenants/read | 获取租户的列表。 |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Scheduler/jobcollections/delete | 删除作业集合。 |
> | 行动 | Microsoft.Scheduler/jobcollections/disable/action | 禁用作业集合。 |
> | 行动 | Microsoft.Scheduler/jobcollections/enable/action | 启用作业集合。 |
> | 行动 | Microsoft.Scheduler/jobcollections/jobs/delete | 删除作业。 |
> | 行动 | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | 基于计划程序作业生成逻辑应用定义。 |
> | 行动 | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | 获取作业历史记录。 |
> | 行动 | Microsoft.Scheduler/jobcollections/jobs/read | 获取作业。 |
> | 行动 | Microsoft.Scheduler/jobcollections/jobs/run/action | 运行作业。 |
> | 行动 | Microsoft.Scheduler/jobcollections/jobs/write | 创建或更新作业。 |
> | 行动 | Microsoft.Scheduler/jobcollections/read | 获取作业集合 |
> | 行动 | Microsoft.Scheduler/jobcollections/write | 创建或更新作业集合。 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Search/checkNameAvailability/action | 检查服务名称的可用性。 |
> | 行动 | Microsoft.Search/operations/read | 列出 Microsoft.Search 提供程序的所有可用操作。 |
> | 行动 | Microsoft.Search/register/action | 注册搜索资源提供程序的订阅，并启用搜索服务的创建。 |
> | 行动 | Microsoft.Search/searchServices/createQueryKey/action | 创建查询密钥。 |
> | 行动 | Microsoft.Search/searchServices/delete | 删除搜索服务。 |
> | 行动 | Microsoft.Search/searchServices/deleteQueryKey/delete | 删除查询密钥。 |
> | 行动 | Microsoft.Search/searchServices/listAdminKeys/action | 读取管理密钥。 |
> | 行动 | SearchServices/listQueryKeys/action | 返回给定 Azure 认知搜索服务的查询 API 密钥列表。 |
> | 行动 | Microsoft.Search/searchServices/listQueryKeys/read | 返回给定 Azure 认知搜索服务的查询 API 密钥列表。 |
> | 行动 | Microsoft.Search/searchServices/read | 读取搜索服务。 |
> | 行动 | Microsoft.Search/searchServices/regenerateAdminKey/action | 再生成管理密钥。 |
> | 行动 | Microsoft.Search/searchServices/start/action | 启动搜索服务。 |
> | 行动 | Microsoft.Search/searchServices/stop/action | 停止搜索服务。 |
> | 行动 | Microsoft.Search/searchServices/write | 创建或更新搜索服务。 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft. Security/adaptiveNetworkHardenings/强制/操作 | 通过在给定网络安全组上创建匹配的安全规则来强制执行给定的流量强化规则 |
> | 行动 | Microsoft. Security/adaptiveNetworkHardenings/read | 获取 Azure 受保护资源的自适应网络强化建议 |
> | 行动 | Microsoft.Security/advancedThreatProtectionSettings/read | 获取资源的高级威胁防护设置 |
> | 行动 | Microsoft.Security/advancedThreatProtectionSettings/write | 更新资源的高级威胁防护设置 |
> | 行动 | Microsoft.Security/alerts/read | 获取所有可用的安全警报 |
> | 行动 | Microsoft.Security/applicationWhitelistings/read | 获取应用程序允许列表 |
> | 行动 | Microsoft.Security/applicationWhitelistings/write | 创建新的或更新现有的应用程序允许列表 |
> | 行动 | Microsoft. Security/assessmentMetadata/read | 获取订阅上的可用安全评估元数据 |
> | 行动 | Microsoft. Security/assessmentMetadata/write | 创建或更新安全评估元数据 |
> | 行动 | Microsoft. Security/评估/读 | 获取订阅的安全评估 |
> | 行动 | Microsoft 安全/评估/写入 | 在订阅上创建或更新安全评估 |
> | 行动 | Microsoft.Security/complianceResults/read | 获取资源的符合性结果 |
> | 行动 | Microsoft.Security/informationProtectionPolicies/read | 获取资源的信息保护策略 |
> | 行动 | Microsoft.Security/informationProtectionPolicies/write | 更新资源的信息保护策略 |
> | 行动 | Microsoft.Security/locations/alerts/activate/action | 激活安全警报 |
> | 行动 | Microsoft.Security/locations/alerts/dismiss/action | 关闭安全警报 |
> | 行动 | Microsoft.Security/locations/alerts/read | 获取所有可用的安全警报 |
> | 行动 | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | 删除适时网络访问策略 |
> | 行动 | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | 启动适时网络访问策略请求 |
> | 行动 | Microsoft.Security/locations/jitNetworkAccessPolicies/read | 获取适时网络访问策略 |
> | 行动 | Microsoft.Security/locations/jitNetworkAccessPolicies/write | 创建新的更新现有的适时网络访问策略 |
> | 行动 | Microsoft.Security/locations/read | 获取安全数据位置 |
> | 行动 | Microsoft.Security/locations/tasks/activate/action | 激活安全建议 |
> | 行动 | Microsoft.Security/locations/tasks/dismiss/action | 关闭安全建议 |
> | 行动 | Microsoft.Security/locations/tasks/read | 获取所有可用的安全建议 |
> | 行动 | Microsoft.Security/locations/tasks/resolve/action | 解决安全建议 |
> | 行动 | Microsoft.Security/locations/tasks/start/action | 启用安全建议 |
> | 行动 | Microsoft.Security/policies/read | 获取安全策略 |
> | 行动 | Microsoft.Security/policies/write | 更新安全策略 |
> | 行动 | Microsoft.Security/pricings/delete | 删除某一范围的定价设置 |
> | 行动 | Microsoft.Security/pricings/read | 获取某一范围的定价设置 |
> | 行动 | Microsoft.Security/pricings/write | 更新某一范围的定价设置 |
> | 行动 | Microsoft.Security/register/action | 注册 Azure 安全中心的订阅 |
> | 行动 | Microsoft.Security/securityContacts/delete | 删除安全联系信息 |
> | 行动 | Microsoft.Security/securityContacts/read | 获取安全联系信息 |
> | 行动 | Microsoft.Security/securityContacts/write | 更新安全联系信息 |
> | 行动 | Microsoft.Security/securitySolutions/delete | 删除安全解决方案 |
> | 行动 | Microsoft.Security/securitySolutions/read | 获取安全解决方案 |
> | 行动 | Microsoft.Security/securitySolutions/write | 创建新的或更新现有的安全解决方案 |
> | 行动 | Microsoft.Security/securitySolutionsReferenceData/read | 获取安全解决方案引用数据 |
> | 行动 | Microsoft.Security/securityStatuses/read | 获取 Azure 资源的安全运行状况 |
> | 行动 | Microsoft.Security/securityStatusesSummaries/read | 获取某一范围的安全状态摘要 |
> | 行动 | Microsoft.Security/settings/read | 获取范围的设置 |
> | 行动 | Microsoft.Security/settings/write | 更新范围的设置 |
> | 行动 | Microsoft.Security/tasks/read | 获取所有可用的安全建议 |
> | 行动 | Microsoft.Security/unregister/action | 从 Azure 安全中心取消注册订阅 |
> | 行动 | Microsoft.Security/webApplicationFirewalls/delete | 删除 Web 应用程序防火墙 |
> | 行动 | Microsoft.Security/webApplicationFirewalls/read | 获取 Web 应用程序防火墙 |
> | 行动 | Microsoft.Security/webApplicationFirewalls/write | 创建新的或更新现有的 Web 应用程序防火墙 |
> | 行动 | Microsoft.Security/workspaceSettings/connect/action | 更改工作区设置重新连接设置 |
> | 行动 | Microsoft.Security/workspaceSettings/delete | 删除工作区设置 |
> | 行动 | Microsoft.Security/workspaceSettings/read | 获取工作区设置 |
> | 行动 | Microsoft.Security/workspaceSettings/write | 更新工作区设置 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.SecurityGraph/diagnosticsettings/delete | 删除诊断设置 |
> | 行动 | Microsoft.SecurityGraph/diagnosticsettings/read | 读取诊断设置 |
> | 行动 | Microsoft.SecurityGraph/diagnosticsettings/write | 写入诊断设置 |
> | 行动 | Microsoft.SecurityGraph/diagnosticsettingscategories/read | 读取诊断设置类别 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.ServiceBus/checkNameAvailability/action | 检查给定订阅下的命名空间可用性。 |
> | 行动 | Microsoft.ServiceBus/checkNamespaceAvailability/action | 检查给定订阅下的命名空间可用性。 已弃用此 API，请改用 CheckNameAvailability。 |
> | 行动 | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | 在服务总线资源提供程序中删除指定 VNet 的 VNet 规则 |
> | 行动 | Microsoft.ServiceBus/namespaces/authorizationRules/action | 更新命名空间授权规则。 已弃用此 API。 请改为使用 PUT 调用来更新命名空间授权规则。 不支持对 API 版本 2017-04-01 执行此操作。 |
> | 行动 | Microsoft.ServiceBus/namespaces/authorizationRules/delete | 删除命名空间授权规则。 无法删除默认的命名空间授权规则。  |
> | 行动 | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | 获取命名空间的连接字符串 |
> | 行动 | Microsoft.ServiceBus/namespaces/authorizationRules/read | 获取命名空间授权规则说明列表。 |
> | 行动 | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | 再生成资源的主密钥或辅助密钥 |
> | 行动 | Microsoft.ServiceBus/namespaces/authorizationRules/write | 创建命名空间级授权规则并更新其属性。 可以更新授权规则访问权限、主密钥和辅助密钥。 |
> | 行动 | Microsoft.ServiceBus/namespaces/Delete | 删除命名空间资源 |
> | 行动 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | 获取灾难恢复主命名空间的授权规则密钥 |
> | 行动 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | 获取灾难恢复主命名空间的授权规则 |
> | 行动 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | 禁用灾难恢复，并停止从主命名空间向辅助命名空间复制更改。 |
> | 行动 | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | 检查给定订阅下命名空间别名的可用性。 |
> | 行动 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | 删除与命名空间关联的灾难恢复配置。 只能通过主命名空间调用此操作。 |
> | 行动 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | 调用异地灾难恢复故障转移并将命名空间别名重新配置为指向辅助命名空间。 |
> | 行动 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | 获取与命名空间关联的灾难恢复配置。 |
> | 行动 | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | 创建或更新与命名空间关联的灾难恢复配置。 |
> | 行动 | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | 删除与命名空间关联的事件网格筛选器。 |
> | 行动 | Microsoft.ServiceBus/namespaces/eventGridFilters/read | 获取与命名空间关联的事件网格筛选器。 |
> | 行动 | Microsoft.ServiceBus/namespaces/eventGridFilters/write | 创建或更新与命名空间关联的事件网格筛选器。 |
> | 行动 | Microsoft.ServiceBus/namespaces/eventhubs/read | 获取 EventHub 资源说明列表 |
> | 行动 | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | 删除 IP 筛选器资源 |
> | 行动 | Microsoft.ServiceBus/namespaces/ipFilterRules/read | 获取 IP 筛选器资源 |
> | 行动 | Microsoft.ServiceBus/namespaces/ipFilterRules/write | 创建 IP 筛选器资源 |
> | DataAction | Node.js/命名空间/消息/接收/操作 | 接收消息 |
> | DataAction | Node.js/命名空间/消息/发送/操作 | 发送消息 |
> | 行动 | Microsoft.ServiceBus/namespaces/messagingPlan/read | 获取命名空间的消息传递计划。<br>已弃用此 API。<br>在以后的 API 版本中，通过 MessagingPlan 资源公开的属性将移动到（父）命名空间资源。<br>不支持对 API 版本 2017-04-01 执行此操作。 |
> | 行动 | Microsoft.ServiceBus/namespaces/messagingPlan/write | 更新命名空间的消息传递计划。<br>已弃用此 API。<br>在以后的 API 版本中，通过 MessagingPlan 资源公开的属性将移动到（父）命名空间资源。<br>不支持对 API 版本 2017-04-01 执行此操作。 |
> | 行动 | Microsoft.ServiceBus/namespaces/migrate/action | 迁移命名空间操作 |
> | 行动 | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | 删除迁移配置。 |
> | 行动 | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | 获取迁移配置以指示迁移状态并挂起复制操作 |
> | 行动 | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | 还原标准命名空间到高级命名空间的迁移 |
> | 行动 | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | 向高级命名空间分配与标准命名空间相关的 DNS，以完成迁移并停止从标准命名空间到高级命名空间的资源同步 |
> | 行动 | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | 创建或更新迁移配置。 这会开始将资源从标准命名空间同步到高级命名空间 |
> | 行动 | Networkruleset/命名空间/删除 | 删除 VNET 规则资源 |
> | 行动 | Networkruleset/命名空间/已读 | 获取 NetworkRuleSet 资源 |
> | 行动 | Networkruleset/命名空间/写入 | 创建 VNET 规则资源 |
> | 行动 | Networkrulesets/命名空间/删除 | 删除 VNET 规则资源 |
> | 行动 | Networkrulesets/命名空间/已读 | 获取 NetworkRuleSet 资源 |
> | 行动 | Networkrulesets/命名空间/写入 | 创建 VNET 规则资源 |
> | 行动 | Microsoft.ServiceBus/namespaces/operationresults/read | 获取命名空间操作的状态 |
> | 行动 | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | 获取命名空间诊断设置资源说明列表 |
> | 行动 | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | 获取命名空间诊断设置资源说明列表 |
> | 行动 | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | 获取命名空间日志资源说明列表 |
> | 行动 | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | 获取命名空间指标资源说明列表 |
> | 行动 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | 更新队列的操作。 不支持对 API 版本 2017-04-01 执行此操作。 授权规则。 请使用 PUT 调用来更新授权规则。 |
> | 行动 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | 用于删除队列授权规则的操作 |
> | 行动 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | 获取队列的连接字符串 |
> | 行动 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  获取队列授权规则列表 |
> | 行动 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | 再生成资源的主密钥或辅助密钥 |
> | 行动 | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | 创建队列授权规则并更新其属性。 可以更新授权规则访问权限。 |
> | 行动 | Microsoft.ServiceBus/namespaces/queues/Delete | 用于删除队列资源的操作 |
> | 行动 | Microsoft.ServiceBus/namespaces/queues/read | 获取队列资源说明列表 |
> | 行动 | Microsoft.ServiceBus/namespaces/queues/write | 创建或更新队列属性。 |
> | 行动 | Microsoft.ServiceBus/namespaces/read | 获取命名空间资源说明列表 |
> | 行动 | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | 删除 ACS 命名空间 |
> | 行动 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | 更新主题的操作。 不支持对 API 版本 2017-04-01 执行此操作。 授权规则。 请使用 PUT 调用来更新授权规则。 |
> | 行动 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | 用于删除主题授权规则的操作 |
> | 行动 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | 获取主题的连接字符串 |
> | 行动 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  获取主题授权规则列表 |
> | 行动 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | 再生成资源的主密钥或辅助密钥 |
> | 行动 | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | 创建主题授权规则并更新其属性。 可以更新授权规则访问权限。 |
> | 行动 | Microsoft.ServiceBus/namespaces/topics/Delete | 用于删除主题资源的操作 |
> | 行动 | Microsoft.ServiceBus/namespaces/topics/read | 获取主题资源说明列表 |
> | 行动 | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | 用于删除 TopicSubscription 资源的操作 |
> | 行动 | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | 获取 TopicSubscription 资源说明列表 |
> | 行动 | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | 用于删除规则资源的操作 |
> | 行动 | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | 获取规则资源说明列表 |
> | 行动 | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | 创建或更新规则属性。 |
> | 行动 | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | 创建或更新 TopicSubscription 属性。 |
> | 行动 | Microsoft.ServiceBus/namespaces/topics/write | 创建或更新主题属性。 |
> | 行动 | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | 删除 VNET 规则资源 |
> | 行动 | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | 获取 VNET 规则资源 |
> | 行动 | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | 创建 VNET 规则资源 |
> | 行动 | Microsoft.ServiceBus/namespaces/write | 创建命名空间资源并更新其属性。 命名空间的标记和容量是可更新的属性。 |
> | 行动 | Microsoft.ServiceBus/operations/read | 获取操作 |
> | 行动 | Microsoft.ServiceBus/register/action | 注册 ServiceBus 提供程序的订阅，并启用 ServiceBus 资源的创建 |
> | 行动 | Microsoft.ServiceBus/sku/read | 获取 SKU 资源说明列表 |
> | 行动 | Microsoft.ServiceBus/sku/regions/read | 获取 SKU 区域资源说明列表 |
> | 行动 | Microsoft.ServiceBus/unregister/action | 注册 ServiceBus 提供程序的订阅，并启用 ServiceBus 资源的创建 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.ServiceFabric/clusters/applications/delete | 删除任何应用程序 |
> | 行动 | Microsoft.ServiceFabric/clusters/applications/read | 读取任何应用程序 |
> | 行动 | Microsoft.ServiceFabric/clusters/applications/services/delete | 删除任何服务 |
> | 行动 | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | 读取任何分区 |
> | 行动 | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | 读取任何副本 |
> | 行动 | Microsoft.ServiceFabric/clusters/applications/services/read | 读取任何服务 |
> | 行动 | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | 读取任何服务状态 |
> | 行动 | Microsoft.ServiceFabric/clusters/applications/services/write | 创建或更新任何服务 |
> | 行动 | Microsoft.ServiceFabric/clusters/applications/write | 创建或更新任何应用程序 |
> | 行动 | Microsoft.ServiceFabric/clusters/applicationTypes/delete | 删除任何应用程序类型 |
> | 行动 | Microsoft.ServiceFabric/clusters/applicationTypes/read | 读取任何应用程序类型 |
> | 行动 | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | 删除任何应用程序类型版本 |
> | 行动 | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | 读取任何应用程序类型版本 |
> | 行动 | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | 创建或更新任何应用程序类型版本 |
> | 行动 | Microsoft.ServiceFabric/clusters/applicationTypes/write | 创建或更新任何应用程序类型 |
> | 行动 | Microsoft.ServiceFabric/clusters/delete | 删除任何群集 |
> | 行动 | Microsoft.ServiceFabric/clusters/nodes/read | 读取任何节点 |
> | 行动 | Microsoft.ServiceFabric/clusters/read | 读取任何群集 |
> | 行动 | Microsoft.ServiceFabric/clusters/statuses/read | 读取任何群集状态 |
> | 行动 | Microsoft.ServiceFabric/clusters/write | 创建或更新任何群集 |
> | 行动 | Microsoft.ServiceFabric/locations/clusterVersions/read | 读取任何群集版本 |
> | 行动 | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | 读取特定环境的任何群集版本 |
> | 行动 | Microsoft.ServiceFabric/locations/operationresults/read | 读取任何操作结果 |
> | 行动 | Microsoft.ServiceFabric/locations/operations/read | 按位置读取任何操作 |
> | 行动 | Microsoft.ServiceFabric/operations/read | 读取任何可用操作 |
> | 行动 | Microsoft.ServiceFabric/register/action | 注册任何操作 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.SignalRService/locations/checknameavailability/action | 检查某个名称是否可用于新的 SignalR 服务 |
> | 行动 | Microsoft.SignalRService/locations/operationresults/signalr/read | 查询异步操作的状态 |
> | 行动 | SignalRService/位置/operationStatuses/operationId/读取 | 查询异步操作的状态 |
> | 行动 | Microsoft.SignalRService/locations/usages/read | 获取 Azure SignalR 服务的配额使用情况 |
> | 行动 | Microsoft.SignalRService/operationresults/read | 查询异步操作的状态 |
> | 行动 | SignalRService/操作/读取 | 列出 Azure SignalR 服务的操作。 |
> | 行动 | SignalRService/operationstatus/read | 查询异步操作的状态 |
> | 行动 | Microsoft.SignalRService/register/action | 将“Microsoft.SignalRService”资源提供程序注册到订阅 |
> | 行动 | Microsoft.SignalRService/SignalR/delete | 删除整个 SignalR 服务 |
> | 行动 | SignalRService/SignalR/eventGridFilters/delete | 从 SignalR 中删除事件网格筛选器。 |
> | 行动 | SignalRService/SignalR/eventGridFilters/read | 获取指定的事件网格筛选器的属性，或列出指定 SignalR 的所有事件网格筛选器。 |
> | 行动 | SignalRService/SignalR/eventGridFilters/write | 使用指定参数创建或更新 SignalR 的事件网格筛选器。 |
> | 行动 | Microsoft.SignalRService/SignalR/listkeys/action | 通过管理门户或 API 查看 SignalR 访问密钥的值 |
> | 行动 | Microsoft.SignalRService/SignalR/read | 在管理门户中或通过 API 查看 SignalR 的设置和配置 |
> | 行动 | Microsoft.SignalRService/SignalR/regeneratekey/action | 通过管理门户或 API 更改 SignalR 访问密钥的值 |
> | 行动 | Microsoft.SignalRService/SignalR/restart/action | 通过管理门户或 API 重启 Azure SignalR 服务。 将会停机一段时间。 |
> | 行动 | Microsoft.SignalRService/SignalR/write | 在管理门户中或通过 API 修改 SignalR 的设置和配置 |
> | 行动 | Microsoft.SignalRService/unregister/action | 将“Microsoft.SignalRService”资源提供程序从订阅中取消注册 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | ApplicationDefinitions/applicationArtifacts/read | 列出应用程序定义的应用程序项目。 |
> | 行动 | Microsoft.Solutions/applicationDefinitions/delete | 删除应用程序定义。 |
> | 行动 | Microsoft.Solutions/applicationDefinitions/read | 检索应用程序定义列表。 |
> | 行动 | Microsoft.Solutions/applicationDefinitions/write | 添加或修改应用程序定义。 |
> | 行动 | Microsoft. 解决方案/应用程序/applicationArtifacts/读取 | 列出应用程序项目。 |
> | 行动 | Microsoft.Solutions/applications/delete | 删除应用程序。 |
> | 行动 | Microsoft.Solutions/applications/read | 检索应用程序列表。 |
> | 行动 | Microsoft. 解决方案/应用程序/refreshPermissions/操作 | 刷新应用程序权限。 |
> | 行动 | Microsoft. 解决方案/应用程序/updateAccess/操作 | 更新应用程序访问。 |
> | 行动 | Microsoft.Solutions/applications/write | 创建应用程序。 |
> | 行动 | Microsoft.Solutions/jitRequests/delete | 删除 JitRequest |
> | 行动 | Microsoft.Solutions/jitRequests/read | 检索 JitRequest 列表 |
> | 行动 | Microsoft.Solutions/jitRequests/write | 创建 JitRequest |
> | 行动 | Microsoft.Solutions/locations/operationStatuses/read | 读取资源的操作状态。 |
> | 行动 | Microsoft.Solutions/register/action | 注册到解决方案。 |
> | 行动 | Microsoft. 解决方案/注销/操作 | 从解决方案中取消注册。 |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Sql/checkNameAvailability/action | 验证给定的服务器名称是否可用于在全球范围内对给定的订阅进行预配。 |
> | 行动 | Microsoft.Sql/instancePools/delete | 删除实例池 |
> | 行动 | Microsoft.Sql/instancePools/read | 获取实例池 |
> | 行动 | InstancePools/使用情况/读取 | 获取实例池的使用情况信息 |
> | 行动 | Microsoft.Sql/instancePools/write | 创建或更新实例池 |
> | 行动 | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | 检索扩展服务器 blob 审核策略集操作的结果 |
> | 行动 | Microsoft.Sql/locations/auditingSettingsOperationResults/read | 检索服务器 Blob 审核策略集操作的结果 |
> | 行动 | Microsoft.Sql/locations/capabilities/read | 获取给定位置中的此订阅的功能 |
> | 行动 | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | 获取数据库操作的状态。 |
> | 行动 | Microsoft.Sql/locations/databaseOperationResults/read | 获取数据库操作的状态。 |
> | 行动 | Microsoft.Sql/locations/deletedServerAsyncOperation/read | 获取正在对已删除服务器进行的操作 |
> | 行动 | Microsoft.Sql/locations/deletedServerOperationResults/read | 获取正在对已删除服务器进行的操作 |
> | 行动 | Microsoft.Sql/locations/deletedServers/read | 返回已删除服务器的列表，或获取指定的已删除服务器的属性。 |
> | 行动 | Microsoft.Sql/locations/deletedServers/recover/action | 恢复已删除的服务器 |
> | 行动 | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | 获取弹性池异步操作的 Azure 异步操作 |
> | 行动 | Microsoft.Sql/locations/elasticPoolOperationResults/read | 获取弹性池操作的结果。 |
> | 行动 | Sql-dmo/位置/encryptionProtectorAzureAsyncOperation/读取 | 获取透明数据加密加密保护程序的正在进行的操作 |
> | 行动 | Sql-dmo/位置/encryptionProtectorOperationResults/读取 | 获取透明数据加密加密保护程序的正在进行的操作 |
> | 行动 | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | 检索扩展服务器 blob 审核策略集操作的结果 |
> | 行动 | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | 检索扩展服务器 blob 审核策略集操作的结果 |
> | 行动 | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | 获取防火墙规则操作的状态。 |
> | 行动 | Microsoft.Sql/locations/firewallRulesOperationResults/read | 获取防火墙规则操作的状态。 |
> | 行动 | Microsoft.Sql/locations/instanceFailoverGroups/delete | 删除现有的实例故障转移组。 |
> | 行动 | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | 在现有的实例故障转移组中执行计划的故障转移。 |
> | 行动 | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | 在现有的实例故障转移组中执行强制故障转移。 |
> | 行动 | Microsoft.Sql/locations/instanceFailoverGroups/read | 返回实例故障转移组的列表，或获取指定实例故障转移组的属性。 |
> | 行动 | Microsoft.Sql/locations/instanceFailoverGroups/write | 使用指定的参数创建实例故障转移组，或更新指定实例故障转移组的属性或标记。 |
> | 行动 | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | 获取实例池操作的状态 |
> | 行动 | Microsoft.Sql/locations/instancePoolOperationResults/read | 获取实例池操作的结果 |
> | 行动 | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | 返回特定接口终结点 Azure 异步操作的详细信息 |
> | 行动 | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | 返回特定接口终结点配置文件操作的详细信息 |
> | 行动 | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | 获取作业代理操作的状态。 |
> | 行动 | Microsoft.Sql/locations/jobAgentOperationResults/read | 获取作业代理操作的结果。 |
> | 行动 | Microsoft.Sql/locations/longTermRetentionBackups/read | 列出某个位置的每台服务器上的每个数据库的长期保留备份 |
> | 行动 | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | 列出服务器上每个数据库的长期保留备份 |
> | 行动 | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | 删除长期保留备份 |
> | 行动 | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | 列出数据库的长期保留备份 |
> | 行动 | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | 完成托管数据库还原操作 |
> | 行动 | Sql-dmo/位置/managedInstanceEncryptionProtectorAzureAsyncOperation/读取 | 获取对透明数据加密托管实例加密保护程序的正在进行的操作 |
> | 行动 | Sql-dmo/位置/managedInstanceEncryptionProtectorOperationResults/读取 | 获取对透明数据加密托管实例加密保护程序的正在进行的操作 |
> | 行动 | Sql-dmo/位置/managedInstanceKeyAzureAsyncOperation/读取 | 获取对透明数据加密托管实例密钥的正在进行的操作 |
> | 行动 | Sql-dmo/位置/managedInstanceKeyOperationResults/读取 | 获取对透明数据加密托管实例密钥的正在进行的操作 |
> | 行动 | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | 获取正在对托管数据库透明数据加密进行的操作 |
> | 行动 | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | 获取正在对托管数据库透明数据加密进行的操作 |
> | 行动 | Sql-dmo/位置/privateEndpointConnectionAzureAsyncOperation/读取 | 获取私有终结点连接操作的结果 |
> | 行动 | Sql-dmo/位置/privateEndpointConnectionOperationResults/读取 | 获取私有终结点连接操作的结果 |
> | 行动 | Sql-dmo/位置/privateEndpointConnectionProxyAzureAsyncOperation/读取 | 获取私有终结点连接代理操作的结果 |
> | 行动 | Sql-dmo/位置/privateEndpointConnectionProxyOperationResults/读取 | 获取私有终结点连接代理操作的结果 |
> | 行动 | Microsoft.Sql/locations/read | 获取给定订阅的可用位置 |
> | 行动 | Sql-dmo/位置/serverKeyAzureAsyncOperation/读取 | 获取对透明数据加密服务器密钥的正在进行的操作 |
> | 行动 | Sql-dmo/位置/serverKeyOperationResults/读取 | 获取对透明数据加密服务器密钥的正在进行的操作 |
> | 行动 | Microsoft.Sql/locations/syncAgentOperationResults/read | 检索同步代理资源操作的结果 |
> | 行动 | Microsoft.Sql/locations/syncDatabaseIds/read | 检索特定区域和订阅的同步数据库 ID |
> | 行动 | Microsoft.Sql/locations/syncGroupOperationResults/read | 检索同步组资源操作的结果 |
> | 行动 | Microsoft.Sql/locations/syncMemberOperationResults/read | 检索同步成员资源操作的结果 |
> | 行动 | Microsoft.Sql/locations/usages/read | 获取此位置中的此订阅的使用指标的集合 |
> | 行动 | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | 返回指定虚拟网络规则 Azure 异步操作的详细信息  |
> | 行动 | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | 返回指定虚拟网络规则操作的详细信息  |
> | 行动 | Microsoft.Sql/managedInstances/administrators/delete | 删除托管实例的现有管理员。 |
> | 行动 | Microsoft.Sql/managedInstances/administrators/read | 获取托管实例管理员的列表。 |
> | 行动 | Microsoft.Sql/managedInstances/administrators/write | 使用指定参数创建或更新托管实例管理员。 |
> | 行动 | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | 获取托管数据库的短期保留策略 |
> | 行动 | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | 更新托管数据库的短期保留策略 |
> | 行动 | ManagedInstances/数据库/列/读取 | 返回托管数据库的列列表 |
> | 行动 | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | 列出给定数据库的敏感度标签 |
> | 行动 | ManagedInstances/currentSensitivityLabels/write | 批更新敏感度标签 |
> | 行动 | Microsoft.Sql/managedInstances/databases/delete | 删除现有托管数据库 |
> | 行动 | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | 行动 | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | 行动 | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | 获取托管实例数据库的可用日志 |
> | 行动 | Microsoft.Sql/managedInstances/databases/read | 获取现有托管数据库 |
> | 行动 | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | 列出给定数据库的敏感度标签 |
> | 行动 | ManagedInstances/recommendedSensitivityLabels/write | 批处理更新建议的敏感度标签 |
> | 行动 | ManagedInstances/restoreDetails/read | 当还原正在进行时，返回托管数据库还原详细信息。 |
> | 行动 | ManagedInstances/数据库/架构/读取 | 获取托管数据库架构。 |
> | 行动 | ManagedInstances/数据库/架构/表/列/读取 | 获取托管数据库列 |
> | 行动 | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | 删除给定列的敏感度标签 |
> | 行动 | ManagedInstances/数据库/架构/表/列/sensitivityLabels/禁用/操作 | 对给定列禁用敏感性建议 |
> | 行动 | ManagedInstances/数据库/架构/表/列/sensitivityLabels/启用/操作 | 对给定列启用敏感性建议 |
> | 行动 | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | 获取给定列的敏感度标签 |
> | 行动 | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | 创建或更新给定列的敏感度标签 |
> | 行动 | ManagedInstances/数据库/架构/表/读取 | 获取托管数据库表 |
> | 行动 | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | 检索为指定服务器配置的托管数据库威胁检测策略的列表 |
> | 行动 | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | 更改给定托管数据库的数据库威胁检测策略 |
> | 行动 | Microsoft.Sql/managedInstances/databases/securityEvents/read | 检索托管数据库安全事件 |
> | 行动 | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | 列出给定数据库的敏感度标签 |
> | 行动 | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | 检索给定托管数据库上的数据库透明数据加密的详细信息 |
> | 行动 | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | 更改给定托管数据库的数据库透明数据加密 |
> | 行动 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | 删除给定数据库的漏洞评估 |
> | 行动 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | 检索给定数据库中的漏洞评估策略 |
> | 行动 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | 删除给定数据库的漏洞评估规则基线 |
> | 行动 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | 获取给定数据库的漏洞评估规则基线 |
> | 行动 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | 更改给定数据库的漏洞评估规则基线 |
> | 行动 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | 将现有扫描结果转换为可读取格式。 如果已存在，则没有任何反应 |
> | 行动 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | 执行漏洞评估数据库扫描。 |
> | 行动 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | 返回数据库漏洞评估扫描记录的列表，或获取指定扫描 ID 的扫描记录。 |
> | 行动 | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | 更改给定数据库的漏洞评估 |
> | 行动 | Microsoft.Sql/managedInstances/databases/write | 创建新数据库或更新现有数据库。 |
> | 行动 | Microsoft.Sql/managedInstances/delete | 删除现有托管实例。 |
> | 行动 | Microsoft.Sql/managedInstances/encryptionProtector/read | 返回服务器加密保护程序的列表，或获取指定服务器加密保护程序的属性。 |
> | 行动 | ManagedInstances/encryptionProtector/重新验证/操作 | 更新指定服务器加密保护程序的属性。 |
> | 行动 | Microsoft.Sql/managedInstances/encryptionProtector/write | 更新指定服务器加密保护程序的属性。 |
> | 行动 | Microsoft.Sql/managedInstances/keys/delete | 删除现有 Azure SQL 托管实例密钥。 |
> | 行动 | Microsoft.Sql/managedInstances/keys/read | 返回托管实例密钥的列表，或获取指定托管实例密钥的属性。 |
> | 行动 | Microsoft.Sql/managedInstances/keys/write | 使用指定参数创建密钥，或更新指定托管实例密钥的属性或标记。 |
> | 行动 | Microsoft.Sql/managedInstances/metricDefinitions/read | 获取托管实例指标定义 |
> | 行动 | Microsoft.Sql/managedInstances/metrics/read | 获取托管实例指标 |
> | 行动 | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | 行动 | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | 行动 | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | 获取托管实例的可用日志 |
> | 行动 | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | 返回托管实例可用的指标类型 |
> | 行动 | Microsoft.Sql/managedInstances/read | 返回托管实例的列表，或获取指定托管实例的属性。 |
> | 行动 | Microsoft.Sql/managedInstances/recoverableDatabases/read | 返回可恢复托管数据库的列表 |
> | 行动 | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | 获取已删除的托管数据库的短期保留策略 |
> | 行动 | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | 更新已删除的托管数据库的短期保留策略 |
> | 行动 | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | 返回可还原的已删除托管数据库的列表。 |
> | 行动 | Microsoft.Sql/managedInstances/securityAlertPolicies/read | 检索为指定服务器配置的托管服务器威胁检测策略的列表 |
> | 行动 | Microsoft.Sql/managedInstances/securityAlertPolicies/write | 更改给定托管服务器的托管服务器威胁检测策略 |
> | 行动 | Microsoft.Sql/managedInstances/tdeCertificates/action | 创建/更新 TDE 证书 |
> | 行动 | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | 删除给定托管实例的漏洞评估 |
> | 行动 | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | 检索给定托管实例中的漏洞评估策略 |
> | 行动 | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | 更改给定托管实例的漏洞评估 |
> | 行动 | Microsoft.Sql/managedInstances/write | 使用指定参数创建托管实例，或更新指定托管实例的属性或标记。 |
> | 行动 | Microsoft.Sql/operations/read | 获取可用的 REST 操作 |
> | 行动 | PrivateEndpointConnectionsApproval/操作 | 确定是否允许用户批准专用终结点连接 |
> | 行动 | Microsoft.Sql/register/action | 注册 Microsoft SQL 数据库资源提供程序的订阅，并启用 Microsoft SQL 数据库的创建。 |
> | 行动 | Microsoft.Sql/servers/administrators/delete | 删除特定 Azure Active Directory 管理员对象 |
> | 行动 | Microsoft.Sql/servers/administrators/read | 获取特定 Azure Active Directory 管理员对象 |
> | 行动 | Microsoft.Sql/servers/administrators/write | 添加或更新特定 Azure Active Directory 管理员对象 |
> | 行动 | Microsoft.Sql/servers/advisors/read | 返回可用于服务器的顾问列表 |
> | 行动 | Microsoft.Sql/servers/advisors/recommendedActions/read | 返回服务器的指定顾问的建议操作列表 |
> | 行动 | Microsoft.Sql/servers/advisors/recommendedActions/write | 对服务器应用建议的操作 |
> | 行动 | Microsoft.Sql/servers/advisors/write | 在服务器级别更新顾问的自动执行状态。 |
> | 行动 | Microsoft.Sql/servers/auditingPolicies/read | 检索在给定的服务器上配置的默认服务器表审核策略的详细信息 |
> | 行动 | Microsoft.Sql/servers/auditingPolicies/write | 更改给定服务器的默认服务器表审核 |
> | 行动 | Microsoft.Sql/servers/auditingSettings/operationResults/read | 检索服务器 Blob 审核策略集操作的结果 |
> | 行动 | Microsoft.Sql/servers/auditingSettings/read | 检索在给定的服务器上配置的服务器 Blob 审核策略的详细信息 |
> | 行动 | Microsoft.Sql/servers/auditingSettings/write | 更改给定服务器的服务器 Blob 审核 |
> | 行动 | Microsoft.Sql/servers/automaticTuning/read | 返回服务器的自动微调设置 |
> | 行动 | Microsoft.Sql/servers/automaticTuning/write | 更新服务器的自动微调设置并返回更新的设置 |
> | 行动 | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | 删除现有备份存档保管库。 |
> | 行动 | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | 此操作用于获取备份长期保留保管库。 它返回有关已注册到此服务器的保管库的信息 |
> | 行动 | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | 此操作用于将备份长期保留保管库注册到服务器 |
> | 行动 | Microsoft.Sql/servers/communicationLinks/delete | 删除现有服务器通信链接。 |
> | 行动 | Microsoft.Sql/servers/communicationLinks/read | 返回指定服务器的通信链接列表。 |
> | 行动 | Microsoft.Sql/servers/communicationLinks/write | 创建或更新服务器通信链接。 |
> | 行动 | Microsoft.Sql/servers/connectionPolicies/read | 返回指定服务器的服务器连接策略列表。 |
> | 行动 | Microsoft.Sql/servers/connectionPolicies/write | 创建或更新服务器连接策略。 |
> | 行动 | Microsoft.Sql/servers/databases/advisors/read | 返回可用于数据库的顾问列表 |
> | 行动 | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | 返回数据库的指定顾问的建议操作列表 |
> | 行动 | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | 对数据库应用建议的操作 |
> | 行动 | Microsoft.Sql/servers/databases/advisors/write | 在数据库级别更新顾问的自动执行状态。 |
> | 行动 | Microsoft.Sql/servers/databases/auditingPolicies/read | 检索在给定的数据库上配置的表审核策略的详细信息 |
> | 行动 | Microsoft.Sql/servers/databases/auditingPolicies/write | 更改给定数据库的表审核策略 |
> | 行动 | Microsoft.Sql/servers/databases/auditingSettings/read | 检索在给定的数据库上配置的 Blob 审核策略的详细信息 |
> | 行动 | Microsoft.Sql/servers/databases/auditingSettings/write | 更改给定数据库的 Blob 审核策略 |
> | 行动 | Microsoft.Sql/servers/databases/auditRecords/read | 检索数据库 Blob 审核记录 |
> | 行动 | Microsoft.Sql/servers/databases/automaticTuning/read | 返回数据库的自动微调设置 |
> | 行动 | Microsoft.Sql/servers/databases/automaticTuning/write | 更新数据库的自动微调设置并返回更新的设置 |
> | 行动 | Microsoft.Sql/servers/databases/azureAsyncOperation/read | 获取数据库操作的状态。 |
> | 行动 | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | 返回指定数据库的备份存档策略列表。 |
> | 行动 | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | 创建或更新数据库备份存档策略。 |
> | 行动 | Sql-dmo/服务器/数据库/backupShortTermRetentionPolicies/读取 | 获取数据库的短期保留策略 |
> | 行动 | Microsoft .Sql/服务器/数据库/backupShortTermRetentionPolicies/写入 | 为数据库更新短期保留策略 |
> | 行动 | Microsoft .Sql/服务器/数据库/列/读取 | 返回数据库的列列表 |
> | 行动 | Microsoft.Sql/servers/databases/connectionPolicies/read | 检索在给定的数据库上配置的连接策略的详细信息 |
> | 行动 | Microsoft.Sql/servers/databases/connectionPolicies/write | 更改给定数据库的连接策略 |
> | 行动 | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | 列出给定数据库的敏感度标签 |
> | 行动 | Microsoft .Sql/服务器/数据库/currentSensitivityLabels/写入 | 批更新敏感度标签 |
> | 行动 | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | 返回数据库数据掩码策略的列表。 |
> | 行动 | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | 删除给定数据库的数据掩码策略规则 |
> | 行动 | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | 检索在给定数据库上配置的数据掩码策略规则的详细信息 |
> | 行动 | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | 更改给定数据库的数据掩码策略规则 |
> | 行动 | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | 更改给定数据库的数据掩码策略 |
> | 行动 | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | 返回所选步骤 ID 的数据仓库查询的分布式查询步骤信息 |
> | 行动 | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | 返回所选查询 ID 的数据仓库分布查询信息 |
> | 行动 | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | 检索包含正在运行和已暂停查询的 SQL 数据仓库实例的用户活动 |
> | 行动 | Microsoft.Sql/servers/databases/delete | 删除现有数据库。 |
> | 行动 | Microsoft.Sql/servers/databases/export/action | 导出 Azure SQL 数据库 |
> | 行动 | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | 检索在给定的数据库上配置的扩展 blob 审核策略的详细信息 |
> | 行动 | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | 更改给定数据库的扩展 blob 审核策略 |
> | 行动 | Microsoft.Sql/servers/databases/extensions/read | 获取数据库的扩展集合。 |
> | 行动 | Microsoft.Sql/servers/databases/extensions/write | 更改给定数据库的扩展 |
> | 行动 | Microsoft .Sql/服务器/数据库/故障转移/操作 | 客户启动的数据库故障转移。 |
> | 行动 | Microsoft.Sql/servers/databases/geoBackupPolicies/read | 检索给定数据库的异地备份策略 |
> | 行动 | Microsoft.Sql/servers/databases/geoBackupPolicies/write | 创建或更新数据库异地备份策略 |
> | 行动 | Microsoft.Sql/servers/databases/importExportOperationResults/read | 获取正在进行的导入/导出操作 |
> | 行动 | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | 获取所选数据库的可用维护窗口列表。 |
> | 行动 | Microsoft.Sql/servers/databases/maintenanceWindows/read | 获取所选数据库的维护窗口设置。 |
> | 行动 | Microsoft.Sql/servers/databases/maintenanceWindows/write | 设置所选数据库的维护窗口设置。 |
> | 行动 | Microsoft.Sql/servers/databases/metricDefinitions/read | 返回可用于数据库的指标类型 |
> | 行动 | Microsoft.Sql/servers/databases/metrics/read | 返回数据库的指标 |
> | 行动 | Microsoft.Sql/servers/databases/move/action | 更改现有数据库的名称。 |
> | 行动 | Microsoft.Sql/servers/databases/operationResults/read | 获取数据库操作的状态。 |
> | 行动 | Microsoft.Sql/servers/databases/operations/cancel/action | 取消尚未完成的 Azure SQL 数据库挂起异步操作。 |
> | 行动 | Microsoft.Sql/servers/databases/operations/read | 返回对数据库执行的操作列表 |
> | 行动 | Microsoft.Sql/servers/databases/pause/action | 暂停 Azure SQL 数据仓库数据库 |
> | 行动 | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | 行动 | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | 行动 | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | 获取数据库的可用日志 |
> | 行动 | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | 返回可用于数据库的指标类型 |
> | 行动 | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | 返回与指定参数对应的查询文本的集合。 |
> | 行动 | Microsoft.Sql/servers/databases/queryStore/read | 返回数据库的查询存储设置的当前值。 |
> | 行动 | Microsoft.Sql/servers/databases/queryStore/write | 更新数据库的 Query Store 设置 |
> | 行动 | Microsoft.Sql/servers/databases/read | 返回数据库的列表，或获取指定数据库的属性。 |
> | 行动 | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | 列出给定数据库的敏感度标签 |
> | 行动 | Microsoft .Sql/服务器/数据库/recommendedSensitivityLabels/写入 | 批处理更新建议的敏感度标签 |
> | 行动 | Microsoft.Sql/servers/databases/replicationLinks/delete | 强制终止复制关系，这可能会丢失数据 |
> | 行动 | Microsoft.Sql/servers/databases/replicationLinks/failover/action | 从主节点同步所有更改后执行故障转移，使此数据库成为复制关系的主节点，并使远程主节点成为辅助节点 |
> | 行动 | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | 立即故障转移（可能会丢失数据），使此数据库成为复制关系的主节点，并使远程主节点成为辅助节点 |
> | 行动 | Microsoft.Sql/servers/databases/replicationLinks/read | 返回复制链接列表，或获取指定复制链接的属性。 |
> | 行动 | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | 强制终止或者在与合作伙伴同步后终止复制关系 |
> | 行动 | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | 将链接的复制模式更新为同步或异步模式 |
> | 行动 | Microsoft.Sql/servers/databases/restorePoints/action | 创建新的还原点 |
> | 行动 | Microsoft.Sql/servers/databases/restorePoints/delete | 删除数据库的还原点。 |
> | 行动 | Microsoft.Sql/servers/databases/restorePoints/read | 返回数据库的还原点。 |
> | 行动 | Microsoft.Sql/servers/databases/resume/action | 恢复 Azure SQL 数据仓库数据库 |
> | 行动 | Microsoft.Sql/servers/databases/schemas/read | 获取数据库架构。 |
> | 行动 | Microsoft.Sql/servers/databases/schemas/tables/columns/read | 获取数据库列。 |
> | 行动 | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | 删除给定列的敏感度标签 |
> | 行动 | Microsoft .Sql/服务器/数据库/架构/表/列/sensitivityLabels/禁用/操作 | 对给定列禁用敏感性建议 |
> | 行动 | Microsoft .Sql/服务器/数据库/架构/表/列/sensitivityLabels/启用/操作 | 对给定列启用敏感性建议 |
> | 行动 | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | 获取给定列的敏感度标签 |
> | 行动 | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | 创建或更新给定列的敏感度标签 |
> | 行动 | Microsoft.Sql/servers/databases/schemas/tables/read | 获取数据库表。 |
> | 行动 | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | 检索数据库上的索引建议列表 |
> | 行动 | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | 应用索引建议 |
> | 行动 | Microsoft.Sql/servers/databases/securityAlertPolicies/read | 检索为给定的服务器配置的数据库威胁检测策略的列表 |
> | 行动 | Microsoft.Sql/servers/databases/securityAlertPolicies/write | 更改给定数据库的数据库威胁检测策略 |
> | 行动 | Microsoft.Sql/servers/databases/securityMetrics/read | 获取数据库安全指标的集合 |
> | 行动 | Microsoft.Sql/servers/databases/sensitivityLabels/read | 列出给定数据库的敏感度标签 |
> | 行动 | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | 返回有关根据查询执行统计信息扩展或缩减数据库的建议，以提高性能或降低成本 |
> | 行动 | Microsoft.Sql/servers/databases/skus/read | 获取数据库可用的 SKU 的集合 |
> | 行动 | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | 取消同步组同步 |
> | 行动 | Microsoft.Sql/servers/databases/syncGroups/delete | 删除现有同步组。 |
> | 行动 | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | 返回同步中心数据库架构的列表 |
> | 行动 | Microsoft.Sql/servers/databases/syncGroups/logs/read | 返回同步组日志的列表 |
> | 行动 | Microsoft.Sql/servers/databases/syncGroups/read | 返回同步组的列表，或获取指定同步组的属性。 |
> | 行动 | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | 刷新同步中心数据库架构 |
> | 行动 | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | 检索同步中心架构刷新操作的结果 |
> | 行动 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | 删除现有同步成员。 |
> | 行动 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | 返回同步成员的列表，或获取指定同步成员的属性。 |
> | 行动 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | 刷新同步成员架构 |
> | 行动 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | 检索同步成员架构刷新操作的结果 |
> | 行动 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | 返回同步成员数据库架构的列表 |
> | 行动 | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | 使用指定参数创建同步成员，或更新指定同步成员的属性。 |
> | 行动 | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | 触发同步组同步 |
> | 行动 | Microsoft.Sql/servers/databases/syncGroups/write | 使用指定参数创建同步组，或更新指定同步组的属性。 |
> | 行动 | Microsoft.Sql/servers/databases/topQueries/queryText/action | 返回所选查询 ID 的 Transact-SQL 文本 |
> | 行动 | Microsoft.Sql/servers/databases/topQueries/read | 返回所选查询在所选时间段内的聚合运行时统计信息 |
> | 行动 | Microsoft.Sql/servers/databases/topQueries/statistics/read | 返回所选查询在所选时间段内的聚合运行时统计信息 |
> | 行动 | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | 获取正在对透明数据加密进行的操作 |
> | 行动 | Microsoft.Sql/servers/databases/transparentDataEncryption/read | 检索给定数据库的透明数据加密安全功能的状态和详细信息 |
> | 行动 | Microsoft.Sql/servers/databases/transparentDataEncryption/write | 更改透明数据加密状态 |
> | 行动 | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | 升级 Azure SQL 数据仓库数据库 |
> | 行动 | Microsoft.Sql/servers/databases/usages/read | 获取 Azure SQL 数据库使用情况信息 |
> | 行动 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | 删除给定数据库的漏洞评估 |
> | 行动 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | 检索给定数据库中的漏洞评估策略 |
> | 行动 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | 删除给定数据库的漏洞评估规则基线 |
> | 行动 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | 获取给定数据库的漏洞评估规则基线 |
> | 行动 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | 更改给定数据库的漏洞评估规则基线 |
> | 行动 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | 将现有扫描结果转换为可读取格式。 如果已存在，则没有任何反应 |
> | 行动 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | 执行漏洞评估数据库扫描。 |
> | 行动 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | 返回数据库漏洞评估扫描记录的列表，或获取指定扫描 ID 的扫描记录。 |
> | 行动 | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | 更改给定数据库的漏洞评估 |
> | 行动 | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | 执行漏洞评估数据库扫描。 |
> | 行动 | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | 检索数据库漏洞评估扫描执行操作的结果 |
> | 行动 | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | 检索在给定数据库上配置的漏洞评估的详细信息 |
> | 行动 | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | 更改给定数据库的漏洞评估 |
> | 行动 | Microsoft.Sql/servers/databases/write | 使用指定的参数创建数据库，或更新指定数据库的属性或标记。 |
> | 行动 | Microsoft.Sql/servers/delete | 删除现有服务器。 |
> | 行动 | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | 删除给定服务器的现有灾难恢复配置 |
> | 行动 | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | 故障转移 DisasterRecoveryConfiguration |
> | 行动 | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | 强制故障转移 DisasterRecoveryConfiguration |
> | 行动 | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | 获取包含此服务器的灾难恢复配置的集合 |
> | 行动 | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | 更改服务器灾难恢复配置 |
> | 行动 | Microsoft.Sql/servers/elasticPoolEstimates/read | 返回已为此服务器创建的弹性池估计列表 |
> | 行动 | Microsoft.Sql/servers/elasticPoolEstimates/write | 为提供的数据库列表创建新的弹性池估计 |
> | 行动 | Microsoft.Sql/servers/elasticPools/advisors/read | 返回可用于弹性池的顾问列表 |
> | 行动 | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | 返回弹性池的指定顾问的建议操作列表 |
> | 行动 | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | 对弹性池应用建议的操作 |
> | 行动 | Microsoft.Sql/servers/elasticPools/advisors/write | 在弹性池级别更新顾问的自动执行状态。 |
> | 行动 | Microsoft.Sql/servers/elasticPools/databases/read | 获取弹性池的数据库列表 |
> | 行动 | Microsoft.Sql/servers/elasticPools/delete | 删除现有弹性池 |
> | 行动 | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | 检索给定弹性数据库池的活动和详细信息 |
> | 行动 | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | 检索属于弹性数据库池的给定数据库的活动和详细信息 |
> | 行动 | ElasticPools/servers/故障转移/操作 | 客户启动了弹性池故障转移。 |
> | 行动 | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | 返回可用于弹性数据库池的指标类型 |
> | 行动 | Microsoft.Sql/servers/elasticPools/metrics/read | 返回弹性数据库池的指标 |
> | 行动 | Microsoft.Sql/servers/elasticPools/operations/cancel/action | 取消尚未完成的 Azure SQL 弹性池挂起异步操作。 |
> | 行动 | Microsoft.Sql/servers/elasticPools/operations/read | 返回对弹性池执行的操作列表 |
> | 行动 | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | 获取资源的诊断设置 |
> | 行动 | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | 创建或更新资源的诊断设置 |
> | 行动 | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | 返回可用于弹性数据库池的指标类型 |
> | 行动 | Microsoft.Sql/servers/elasticPools/read | 检索给定服务器上弹性池的详细信息 |
> | 行动 | Microsoft.Sql/servers/elasticPools/skus/read | 获取弹性池可用的 SKU 的集合 |
> | 行动 | Microsoft.Sql/servers/elasticPools/write | 创建新弹性池或更改现有弹性池的属性 |
> | 行动 | Microsoft.Sql/servers/encryptionProtector/read | 返回服务器加密保护程序的列表，或获取指定服务器加密保护程序的属性。 |
> | 行动 | EncryptionProtector/服务器/重新验证/操作 | 更新指定服务器加密保护程序的属性。 |
> | 行动 | Microsoft.Sql/servers/encryptionProtector/write | 更新指定服务器加密保护程序的属性。 |
> | 行动 | Microsoft.Sql/servers/extendedAuditingSettings/read | 检索在给定服务器上配置的扩展服务器 blob 审核策略的详细信息 |
> | 行动 | Microsoft.Sql/servers/extendedAuditingSettings/write | 更改给定服务器的扩展服务器 blob 审核 |
> | 行动 | Microsoft.Sql/servers/failoverGroups/delete | 删除现有故障转移组。 |
> | 行动 | Microsoft.Sql/servers/failoverGroups/failover/action | 在现有故障转移组中执行计划的故障转移。 |
> | 行动 | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | 在现有故障转移组中执行强制故障转移。 |
> | 行动 | Microsoft.Sql/servers/failoverGroups/read | 返回故障转移组的列表，或获取指定故障转移组的属性。 |
> | 行动 | Microsoft.Sql/servers/failoverGroups/write | 使用指定参数创建故障转移组，或更新指定故障转移组的属性或标记。 |
> | 行动 | Microsoft.Sql/servers/firewallRules/delete | 删除现有服务器防火墙规则。 |
> | 行动 | Microsoft.Sql/servers/firewallRules/read | 返回服务器防火墙规则的列表，或获取指定服务器防火墙规则的属性。 |
> | 行动 | Microsoft.Sql/servers/firewallRules/write | 使用指定参数创建服务器防火墙规则、更新指定规则的属性、或使用新的服务器防火墙规则覆盖所有现有规则。 |
> | 行动 | Microsoft.Sql/servers/import/action | 在服务器上创建新数据库，并部署 DacPac 包中的架构和数据 |
> | 行动 | Microsoft.Sql/servers/importExportOperationResults/read | 获取正在进行的导入/导出操作 |
> | 行动 | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | 删除指定的接口终结点配置文件 |
> | 行动 | Microsoft.Sql/servers/interfaceEndpointProfiles/read | 返回指定接口终结点配置文件的属性 |
> | 行动 | Microsoft.Sql/servers/interfaceEndpointProfiles/write | 使用指定的参数创建接口终结点配置文件，或更新指定接口终结点的属性或标记 |
> | 行动 | Microsoft.Sql/servers/jobAgents/delete | 删除 Azure SQL DB 作业代理 |
> | 行动 | Microsoft.Sql/servers/jobAgents/read | 获取 Azure SQL DB 作业代理 |
> | 行动 | Microsoft.Sql/servers/jobAgents/write | 创建或更新 Azure SQL DB 作业代理 |
> | 行动 | Microsoft.Sql/servers/keys/delete | 删除现有服务器密钥。 |
> | 行动 | Microsoft.Sql/servers/keys/read | 返回服务器密钥的列表，或获取指定服务器密钥的属性。 |
> | 行动 | Microsoft.Sql/servers/keys/write | 使用指定参数创建密钥，或更新指定服务器密钥的属性或标记。 |
> | 行动 | Microsoft.Sql/servers/operationResults/read | 获取正在进行的服务器操作 |
> | 行动 | PrivateEndpointConnectionProxies/servers/删除 | 删除现有的专用终结点连接代理 |
> | 行动 | PrivateEndpointConnectionProxies/servers/ | 返回专用终结点连接代理列表，或获取指定的专用终结点连接代理的属性。 |
> | 行动 | Microsoft .Sql/服务器/privateEndpointConnectionProxies/验证/操作 | 验证专用终结点连接从 NRP 端创建调用 |
> | 行动 | PrivateEndpointConnectionProxies/服务器/写入 | 使用指定的参数创建专用终结点连接代理，或更新指定专用终结点连接代理的属性或标记。 |
> | 行动 | PrivateEndpointConnections/servers/删除 | 删除现有的专用终结点连接 |
> | 行动 | PrivateEndpointConnections/servers/ | 返回专用终结点连接的列表，或获取指定的专用终结点连接的属性。 |
> | 行动 | PrivateEndpointConnections/服务器/写入 | 批准或拒绝现有的专用终结点连接 |
> | 行动 | PrivateEndpointConnectionsApproval/servers/操作 | 确定是否允许用户批准专用终结点连接 |
> | 行动 | PrivateLinkResources/servers/ | 获取相应 sql server 的专用链接资源 |
> | 行动 | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | 返回服务器可用的指标类型 |
> | 行动 | Microsoft.Sql/servers/read | 返回服务器列表，或获取指定服务器的属性。 |
> | 行动 | Microsoft.Sql/servers/recommendedElasticPools/databases/read | 检索给定服务器的建议弹性数据库池的指标 |
> | 行动 | Microsoft.Sql/servers/recommendedElasticPools/read | 检索针对弹性数据库池的建议，以便根据历史资源利用率降低成本或提高性能 |
> | 行动 | Microsoft.Sql/servers/recoverableDatabases/read | 此操作用于实时数据库的灾难恢复，以便将数据库还原到最后一个已知正常的备份点。 它返回有关最后一个正常备份的信息，但实际上不会还原数据库。 |
> | 行动 | Microsoft.Sql/servers/replicationLinks/read | 返回复制链接列表，或获取指定复制链接的属性。 |
> | 行动 | Microsoft.Sql/servers/restorableDroppedDatabases/read | 获取已在给定服务器中删除但仍包含在保留策略中的数据库列表。 |
> | 行动 | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | 检索服务器威胁检测策略写入操作的结果 |
> | 行动 | Microsoft.Sql/servers/securityAlertPolicies/read | 检索为指定服务器配置的服务器威胁检测策略的列表 |
> | 行动 | Microsoft.Sql/servers/securityAlertPolicies/write | 更改给定服务器的服务器威胁检测策略 |
> | 行动 | Microsoft.Sql/servers/serviceObjectives/read | 检索可在给定服务器上实现的服务级别目标（也称为性能层 |
> | 行动 | Microsoft.Sql/servers/syncAgents/delete | 删除现有同步代理。 |
> | 行动 | Microsoft.Sql/servers/syncAgents/generateKey/action | 生成同步代理注册密钥 |
> | 行动 | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | 返回同步代理链接数据库的列表 |
> | 行动 | Microsoft.Sql/servers/syncAgents/read | 返回同步代理的列表，或获取指定同步代理的属性。 |
> | 行动 | Microsoft.Sql/servers/syncAgents/write | 使用指定参数创建同步代理，或更新指定同步代理的属性。 |
> | 行动 | Microsoft.Sql/servers/tdeCertificates/action | 创建/更新 TDE 证书 |
> | 行动 | Microsoft.Sql/servers/usages/read | 返回服务器中所有数据库的服务器 DTU 配额和当前 DTU 使用情况 |
> | 行动 | Microsoft.Sql/servers/virtualNetworkRules/delete | 删除现有虚拟网络规则 |
> | 行动 | Microsoft.Sql/servers/virtualNetworkRules/read | 返回虚拟网络规则列表，或获取指定虚拟网络规则的属性。 |
> | 行动 | Microsoft.Sql/servers/virtualNetworkRules/write | 使用指定参数创建虚拟网络规则，或更新指定虚拟网络规则的属性或标记。 |
> | 行动 | Microsoft.Sql/servers/vulnerabilityAssessments/delete | 删除给定服务器的漏洞评估 |
> | 行动 | Microsoft.Sql/servers/vulnerabilityAssessments/read | 检索给定服务器中的漏洞评估策略 |
> | 行动 | Microsoft.Sql/servers/vulnerabilityAssessments/write | 更改给定服务器的漏洞评估 |
> | 行动 | Microsoft.Sql/servers/write | 使用指定参数创建服务器，或更新指定服务器的属性或标记。 |
> | 行动 | Microsoft.Sql/unregister/action | 取消注册 Microsoft SQL 数据库资源提供程序的订阅，并启用 Microsoft SQL 数据库的创建。 |
> | 行动 | VirtualClusters/删除 | 删除现有虚拟群集。 |
> | 行动 | Microsoft.Sql/virtualClusters/read | 返回虚拟群集的列表，或获取指定虚拟群集的属性。 |
> | 行动 | Microsoft.Sql/virtualClusters/write | 更新虚拟群集标记。 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Storage/checknameavailability/read | 检查帐户名称是否有效且未被使用。 |
> | 行动 | Microsoft. 存储/位置/checknameavailability/读取 | 检查帐户名称是否有效且未被使用。 |
> | 行动 | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | 向 Microsoft.Storage 通知正在删除虚拟网络或子网 |
> | 行动 | Microsoft.Storage/locations/usages/read | 返回指定订阅中的资源的限制和当前使用计数 |
> | 行动 | Microsoft.Storage/operations/read | 轮询异步操作的状态。 |
> | 行动 | Microsoft.Storage/register/action | 注册存储资源提供程序的订阅，并启用存储帐户的创建。 |
> | 行动 | Microsoft.Storage/skus/read | 列出 Microsoft.Storage 支持的 SKU。 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | 返回添加 blob 内容的结果 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | 返回删除 blob 的结果 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteAutomaticSnapshot/action | 返回删除自动快照的结果 |
> | DataAction | StorageAccounts/blobServices/容器/blob/筛选器/操作 | 返回具有匹配标记的帐户下的 blob 列表筛选器 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | 返回 blob 或 blob 列表 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action | 返回 blob 命令的结果 |
> | DataAction | StorageAccounts/blobServices/容器/blob/标记/读取 | 返回读取 blob 标记的结果 |
> | DataAction | StorageAccounts/blobServices/容器/blob/标记/写入 | 返回写入 blob 标记的结果 |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | 返回写入 blob 的结果 |
> | 行动 | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | 清除 blob 容器法定保留 |
> | 行动 | Microsoft.Storage/storageAccounts/blobServices/containers/delete | 返回删除容器的结果 |
> | 行动 | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | 删除 blob 容器不可变性策略 |
> | 行动 | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | 扩展 blob 容器不可变性策略 |
> | 行动 | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | 锁定 blob 容器不可变性策略 |
> | 行动 | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | 获取 blob 容器不可变性策略 |
> | 行动 | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | 放置 blob 容器不可变性策略 |
> | 行动 | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | 返回租用 blob 容器的结果 |
> | 行动 | Microsoft.Storage/storageAccounts/blobServices/containers/read | 返回容器 |
> | 行动 | Microsoft.Storage/storageAccounts/blobServices/containers/read | 返回容器列表 |
> | 行动 | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | 设置 blob 容器法定保留 |
> | 行动 | Microsoft.Storage/storageAccounts/blobServices/containers/write | 返回修补 blob 容器的结果 |
> | 行动 | Microsoft.Storage/storageAccounts/blobServices/containers/write | 返回放置 blob 容器的结果 |
> | 行动 | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | 返回 blob 服务的用户委托密钥 |
> | 行动 | Microsoft.Storage/storageAccounts/blobServices/read |  |
> | 行动 | Microsoft.Storage/storageAccounts/blobServices/read | 返回 blob 服务属性或统计信息 |
> | 行动 | Microsoft.Storage/storageAccounts/blobServices/write | 返回放置 blob 服务属性的结果 |
> | 行动 | Microsoft.Storage/storageAccounts/delete | 删除现有的存储帐户。 |
> | 行动 | StorageAccounts/encryptionScopes/read |  |
> | 行动 | StorageAccounts/encryptionScopes/write |  |
> | 行动 | StorageAccounts/故障转移/操作 | 在出现可用性问题时，客户能够控制故障转移 |
> | DataAction | StorageAccounts/fileServices/文件共享/files/actassuperuser/action | 获取文件管理员权限 |
> | DataAction | StorageAccounts/fileServices/文件共享/files/delete | 返回删除文件/文件夹的结果 |
> | DataAction | StorageAccounts/fileServices/文件共享/files/modifypermissions/action | 返回对文件/文件夹的修改权限的结果 |
> | DataAction | StorageAccounts/fileServices/文件共享/files/read | 返回文件/文件夹或文件/文件夹的列表 |
> | DataAction | StorageAccounts/fileServices/文件共享/files/write | 返回写入文件或创建文件夹的结果 |
> | 行动 | StorageAccounts/fileServices/read |  |
> | 行动 | StorageAccounts/fileServices/read | 获取文件服务属性 |
> | 行动 | StorageAccounts/fileServices/共享/删除 |  |
> | 行动 | StorageAccounts/fileServices/共享/读取 |  |
> | 行动 | StorageAccounts/fileServices/共享/读取 |  |
> | 行动 | StorageAccounts/fileServices/共享/写入 |  |
> | 行动 | StorageAccounts/fileServices/write |  |
> | 行动 | Microsoft.Storage/storageAccounts/listAccountSas/action | 返回指定存储帐户的帐户 SAS 令牌。 |
> | 行动 | Microsoft.Storage/storageAccounts/listkeys/action | 返回指定存储帐户的访问密钥。 |
> | 行动 | Microsoft.Storage/storageAccounts/listServiceSas/action | 返回指定存储帐户的服务 SAS 令牌。 |
> | 行动 | StorageAccounts/managementPolicies/delete | 删除存储帐户管理策略 |
> | 行动 | StorageAccounts/managementPolicies/read | 获取存储管理帐户策略 |
> | 行动 | StorageAccounts/managementPolicies/write | 放置存储帐户管理策略 |
> | 行动 | StorageAccounts/objectReplicationPolicies/delete |  |
> | 行动 | StorageAccounts/objectReplicationPolicies/read |  |
> | 行动 | StorageAccounts/objectReplicationPolicies/write |  |
> | 行动 | StorageAccounts/privateEndpointConnectionProxies/delete | 删除专用终结点连接代理 |
> | 行动 | StorageAccounts/privateEndpointConnectionProxies/read | 获取专用终结点连接代理 |
> | 行动 | StorageAccounts/privateEndpointConnectionProxies/write | 放置专用终结点连接代理 |
> | 行动 | StorageAccounts/privateEndpointConnections/delete | 删除专用终结点连接 |
> | 行动 | StorageAccounts/privateEndpointConnections/read | 获取专用终结点连接 |
> | 行动 | StorageAccounts/privateEndpointConnections/write | 放置专用终结点连接 |
> | 行动 | StorageAccounts/PrivateEndpointConnectionsApproval/action | 批准专用终结点连接 |
> | 行动 | StorageAccounts/privateLinkResources/read | 获取 StorageAccount groupids |
> | 行动 | Microsoft.Storage/storageAccounts/queueServices/queues/delete | 返回删除队列的结果 |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | 返回添加消息的结果 |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | 返回删除消息的结果 |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | 返回处理消息的结果 |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | 返回消息 |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | 返回编写消息的结果 |
> | 行动 | Microsoft.Storage/storageAccounts/queueServices/queues/read | 返回队列或队列列表。 |
> | 行动 | Microsoft.Storage/storageAccounts/queueServices/queues/write | 返回写入队列的结果 |
> | 行动 | Microsoft.Storage/storageAccounts/queueServices/read | 获取队列服务属性 |
> | 行动 | Microsoft.Storage/storageAccounts/queueServices/read | 返回队列服务属性或统计信息。 |
> | 行动 | Microsoft.Storage/storageAccounts/queueServices/write | 返回设置队列服务属性的结果 |
> | 行动 | Microsoft.Storage/storageAccounts/read | 返回存储帐户的列表，或获取指定存储帐户的属性。 |
> | 行动 | Microsoft.Storage/storageAccounts/regeneratekey/action | 再生成指定存储帐户的访问密钥。 |
> | 行动 | StorageAccounts/restoreBlobRanges/action | 将 blob 范围还原到指定时间的状态 |
> | 行动 | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | 撤销指定的存储帐户的所有用户委托密钥。 |
> | 行动 | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | 创建/更新存储帐户诊断设置。 |
> | 行动 | StorageAccounts/tableServices/read | 获取表服务属性 |
> | 行动 | Microsoft.Storage/storageAccounts/write | 使用指定的参数创建存储帐户、更新指定存储帐户的属性或标记，或者为其添加自定义域。 |
> | 行动 | Microsoft.Storage/usages/read | 返回指定订阅中的资源的限制和当前使用计数 |

## <a name="microsoftstoragesync"></a>storagesync.sys

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | microsoft.storagesync/locations/checkNameAvailability/action | 检查该存储同步服务名称是否有效且未被使用。 |
> | 行动 | microsoft.storagesync/locations/workflows/operations/read | 获取异步操作的状态 |
> | 行动 | storagesync.sys/操作/读取 | 获取支持的操作的列表 |
> | 行动 | storagesync.sys/注册/操作 | 注册存储同步提供程序的订阅 |
> | 行动 | microsoft.storagesync/storageSyncServices/delete | 删除任何存储同步服务 |
> | 行动 | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | 获取存储同步服务的可用指标 |
> | 行动 | microsoft.storagesync/storageSyncServices/read | 读取任何存储同步服务 |
> | 行动 | microsoft.storagesync/storageSyncServices/registeredServers/delete | 删除任何已注册服务器 |
> | 行动 | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | 获取已注册服务器的可用指标 |
> | 行动 | microsoft.storagesync/storageSyncServices/registeredServers/read | 读取任何已注册服务器 |
> | 行动 | microsoft.storagesync/storageSyncServices/registeredServers/write | 创建或更新任何已注册服务器 |
> | 行动 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | 删除任何云终结点 |
> | 行动 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | 获取异步备份/还原操作的状态 |
> | 行动 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | 备份后调用此操作 |
> | 行动 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | 存储后调用此操作 |
> | 行动 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | 备份前调用此操作 |
> | 行动 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | 存储前调用此操作 |
> | 行动 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | 读取任何云终结点 |
> | 行动 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | 还原检测信号 |
> | 行动 | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | 创建或更新任何云终结点 |
> | 行动 | microsoft.storagesync/storageSyncServices/syncGroups/delete | 删除任何同步组 |
> | 行动 | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | 获取同步组的可用指标 |
> | 行动 | microsoft.storagesync/storageSyncServices/syncGroups/read | 读取任何同步组 |
> | 行动 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | 删除任何服务器终结点 |
> | 行动 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | 获取服务器终结点的可用指标 |
> | 行动 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | 读取任何服务器终结点 |
> | 行动 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | 调用此操作，将文件撤回到服务器 |
> | 行动 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | 创建或更新任何服务器终结点 |
> | 行动 | microsoft.storagesync/storageSyncServices/syncGroups/write | 创建或更新任何同步组 |
> | 行动 | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | 获取异步操作的状态 |
> | 行动 | microsoft.storagesync/storageSyncServices/workflows/operations/read | 获取异步操作的状态 |
> | 行动 | microsoft.storagesync/storageSyncServices/workflows/read | 读取工作流 |
> | 行动 | microsoft.storagesync/storageSyncServices/write | 创建或更新任何存储同步服务 |
> | 行动 | storagesync.sys/注销/操作 | 注销存储同步提供程序的订阅 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.StorSimple/managers/accessControlRecords/delete | 删除访问控制记录 |
> | 行动 | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | 列出或获取操作结果 |
> | 行动 | Microsoft.StorSimple/managers/accessControlRecords/read | 列出或获取访问控制记录 |
> | 行动 | Microsoft.StorSimple/managers/accessControlRecords/write | 创建或更新访问控制记录 |
> | 行动 | Microsoft.StorSimple/managers/alerts/read | 列出或获取警报 |
> | 行动 | Microsoft.StorSimple/managers/backups/read | 列出或获取备份集 |
> | 行动 | Microsoft.StorSimple/managers/bandwidthSettings/delete | 删除现有的带宽设置（仅适用于 8000 系列） |
> | 行动 | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | 列出操作结果 |
> | 行动 | Microsoft.StorSimple/managers/bandwidthSettings/read | 列出带宽设置（仅适用于 8000 系列） |
> | 行动 | Microsoft.StorSimple/managers/bandwidthSettings/write | 新建或更新带宽设置（仅适用于 8000 系列） |
> | 行动 | Microsoft.StorSimple/managers/certificates/write | 创建或更新证书 |
> | 行动 | Microsoft.StorSimple/Managers/certificates/write | “更新资源证书”操作更新资源/保管库凭据证书。 |
> | 行动 | Microsoft.StorSimple/managers/clearAlerts/action | 清除与设备管理器关联的所有警报。 |
> | 行动 | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | 列出云设备支持的配置 |
> | 行动 | Microsoft.StorSimple/managers/configureDevice/action | 配置设备 |
> | 行动 | Microsoft.StorSimple/managers/delete | 删除设备管理器 |
> | 行动 | Microsoft.StorSimple/Managers/delete | “删除保管库”操作删除“vault”类型的指定 Azure 资源 |
> | 行动 | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | 列出或获取操作结果 |
> | 行动 | Microsoft.StorSimple/managers/devices/alertSettings/read | 列出或获取警报设置 |
> | 行动 | Microsoft.StorSimple/managers/devices/alertSettings/write | 创建或更新警报设置 |
> | 行动 | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | 为设备的服务加密密钥滚动更新授权 |
> | 行动 | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | 执行手动备份以创建策略保护的所有卷的按需备份。 |
> | 行动 | Microsoft.StorSimple/managers/devices/backupPolicies/delete | 删除现有的备份策略（仅适用于 8000 系列） |
> | 行动 | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | 列出操作结果 |
> | 行动 | Microsoft.StorSimple/managers/devices/backupPolicies/read | 列出备份策略（仅适用于 8000 系列） |
> | 行动 | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | 删除现有的计划 |
> | 行动 | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | 列出操作结果 |
> | 行动 | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | 列出计划 |
> | 行动 | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | 新建或更新计划 |
> | 行动 | Microsoft.StorSimple/managers/devices/backupPolicies/write | 新建或更新备份策略（仅适用于 8000 系列） |
> | 行动 | Microsoft.StorSimple/managers/devices/backups/delete | 删除备份集 |
> | 行动 | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | 使用备份元素克隆共享或卷。 |
> | 行动 | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | 列出或获取操作结果 |
> | 行动 | Microsoft.StorSimple/managers/devices/backups/operationResults/read | 列出或获取操作结果 |
> | 行动 | Microsoft.StorSimple/managers/devices/backups/read | 列出或获取备份集 |
> | 行动 | Microsoft.StorSimple/managers/devices/backups/restore/action | 从备份集还原所有卷。 |
> | 行动 | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | 删除备份计划组 |
> | 行动 | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | 列出或获取操作结果 |
> | 行动 | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | 列出或获取备份计划组 |
> | 行动 | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | 创建或更新备份计划组 |
> | 行动 | Microsoft.StorSimple/managers/devices/chapSettings/delete | 删除 Chap 设置 |
> | 行动 | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | 列出或获取操作结果 |
> | 行动 | Microsoft.StorSimple/managers/devices/chapSettings/read | 列出或获取 Chap 设置 |
> | 行动 | Microsoft.StorSimple/managers/devices/chapSettings/write | 创建或更新 Chap 设置 |
> | 行动 | Microsoft.StorSimple/managers/devices/deactivate/action | 停用设备。 |
> | 行动 | Microsoft.StorSimple/managers/devices/delete | 删除设备 |
> | 行动 | Microsoft.StorSimple/managers/devices/disks/read | 列出或获取磁盘 |
> | 行动 | Microsoft.StorSimple/managers/devices/download/action | 下载设备更新。 |
> | 行动 | Microsoft.StorSimple/managers/devices/failover/action | 设备故障转移。 |
> | 行动 | Microsoft.StorSimple/managers/devices/failover/operationResults/read | 列出或获取操作结果 |
> | 行动 | Microsoft.StorSimple/managers/devices/failoverTargets/read | 列出或获取设备的故障转移目标 |
> | 行动 | Microsoft.StorSimple/managers/devices/fileservers/backup/action | 备份文件服务器。 |
> | 行动 | Microsoft.StorSimple/managers/devices/fileservers/delete | 删除文件服务器 |
> | 行动 | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | 列出或获取指标 |
> | 行动 | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | 列出或获取指标定义 |
> | 行动 | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | 列出或获取操作结果 |
> | 行动 | Microsoft.StorSimple/managers/devices/fileservers/read | 列出或获取文件服务器 |
> | 行动 | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | 删除共享 |
> | 行动 | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | 列出或获取指标 |
> | 行动 | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | 列出或获取指标定义 |
> | 行动 | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | 列出或获取操作结果 |
> | 行动 | Microsoft.StorSimple/managers/devices/fileservers/shares/read | 列出或获取共享 |
> | 行动 | Microsoft.StorSimple/managers/devices/fileservers/shares/write | 创建或更新共享 |
> | 行动 | Microsoft.StorSimple/managers/devices/fileservers/write | 创建或更新文件服务器 |
> | 行动 | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | 更改硬件组件组的控制器电源状态 |
> | 行动 | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | 列出操作结果 |
> | 行动 | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | 列出硬件组件组 |
> | 行动 | Microsoft.StorSimple/managers/devices/install/action | 在设备上安装更新。 |
> | 行动 | Microsoft.StorSimple/managers/devices/installUpdates/action | 在设备（仅限 8000 系列）上安装更新。 |
> | 行动 | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | 备份 iSCSI 服务器。 |
> | 行动 | Microsoft.StorSimple/managers/devices/iscsiservers/delete | 删除 iSCSI 服务器 |
> | 行动 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | 删除磁盘 |
> | 行动 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | 列出或获取指标 |
> | 行动 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | 列出或获取指标定义 |
> | 行动 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | 列出或获取操作结果 |
> | 行动 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | 列出或获取磁盘 |
> | 行动 | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | 创建或更新磁盘 |
> | 行动 | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | 列出或获取指标 |
> | 行动 | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | 列出或获取指标定义 |
> | 行动 | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | 列出或获取操作结果 |
> | 行动 | Microsoft.StorSimple/managers/devices/iscsiservers/read | 列出或获取 iSCSI 服务器 |
> | 行动 | Microsoft.StorSimple/managers/devices/iscsiservers/write | 创建或更新 iSCSI 服务器 |
> | 行动 | Microsoft.StorSimple/managers/devices/jobs/cancel/action | 取消正在运行的作业 |
> | 行动 | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | 列出操作结果 |
> | 行动 | Microsoft.StorSimple/managers/devices/jobs/read | 列出或获取作业 |
> | 行动 | Microsoft.StorSimple/managers/devices/listFailoverSets/action | 列出现有设备（仅限 8000 系列）的故障转移集。 |
> | 行动 | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | 列出设备（仅限 8000 系列）的故障转移目标。 |
> | 行动 | Microsoft.StorSimple/managers/devices/metrics/read | 列出或获取指标 |
> | 行动 | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | 列出或获取指标定义 |
> | 行动 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | 确认成功迁移并提交结果。 |
> | 行动 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | 列出确认迁移状态 |
> | 行动 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | 提取迁移的确认状态。 |
> | 行动 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | 提取迁移估计作业的状态。 |
> | 行动 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | 提取迁移的状态。 |
> | 行动 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | 导入要迁移的源配置 |
> | 行动 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | 列出迁移估算 |
> | 行动 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | 列出迁移状态 |
> | 行动 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | 列出操作结果 |
> | 行动 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | 使用源配置开始迁移 |
> | 行动 | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | 启动作业来估计迁移过程的持续时间。 |
> | 行动 | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | 列出操作结果 |
> | 行动 | Microsoft.StorSimple/managers/devices/networkSettings/read | 列出或获取网络设置 |
> | 行动 | Microsoft.StorSimple/managers/devices/networkSettings/write | 新建或更新网络设置 |
> | 行动 | Microsoft.StorSimple/managers/devices/operationResults/read | 列出或获取操作结果 |
> | 行动 | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | 列出设备管理器的加密公钥 |
> | 行动 | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | 发布现有设备的支持包。 StorSimple 支持包是一种易于使用的机制，用于收集所有相关日志，协助 Microsoft 支持部门排除 StorSimple 设备问题。 |
> | 行动 | Microsoft.StorSimple/managers/devices/read | 列出或获取设备 |
> | 行动 | Microsoft.StorSimple/managers/devices/scanForUpdates/action | 扫描设备中的更新。 |
> | 行动 | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | 列出或获取操作结果 |
> | 行动 | Microsoft.StorSimple/managers/devices/securitySettings/read | 列出安全设置 |
> | 行动 | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | 同步设备的远程管理证书。 |
> | 行动 | Microsoft.StorSimple/managers/devices/securitySettings/update/action | 更新安全设置。 |
> | 行动 | Microsoft.StorSimple/managers/devices/securitySettings/write | 新建或更新安全设置 |
> | 行动 | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | 将测试警报电子邮件发送到配置的电子邮件收件人。 |
> | 行动 | Microsoft.StorSimple/managers/devices/shares/read | 列出或获取共享 |
> | 行动 | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | 列出操作结果 |
> | 行动 | Microsoft.StorSimple/managers/devices/timeSettings/read | 列出或获取时间设置 |
> | 行动 | Microsoft.StorSimple/managers/devices/timeSettings/write | 新建或更新时间设置 |
> | 行动 | Microsoft.StorSimple/managers/devices/updates/operationResults/read | 列出或获取操作结果 |
> | 行动 | Microsoft.StorSimple/managers/devices/updateSummary/read | 列出或获取更新摘要 |
> | 行动 | Microsoft.StorSimple/managers/devices/volumeContainers/delete | 删除现有的卷容器（仅适用于 8000 系列） |
> | 行动 | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | 列出指标 |
> | 行动 | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | 列出指标定义 |
> | 行动 | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | 列出操作结果 |
> | 行动 | Microsoft.StorSimple/managers/devices/volumeContainers/read | 列出卷容器（仅适用于 8000 系列） |
> | 行动 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | 删除现有的卷 |
> | 行动 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | 列出指标 |
> | 行动 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | 列出指标定义 |
> | 行动 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | 列出操作结果 |
> | 行动 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | 列出卷 |
> | 行动 | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | 新建或更新卷 |
> | 行动 | Microsoft.StorSimple/managers/devices/volumeContainers/write | 新建或更新卷容器（仅适用于 8000 系列） |
> | 行动 | Microsoft.StorSimple/managers/devices/volumes/read | 列出卷 |
> | 行动 | Microsoft.StorSimple/managers/devices/write | 创建或更新设备 |
> | 行动 | Microsoft.StorSimple/managers/encryptionSettings/read | 列出或获取加密设置 |
> | 行动 | Microsoft.StorSimple/managers/extendedInformation/delete | 删除扩展保管库信息 |
> | 行动 | Microsoft.StorSimple/Managers/extendedInformation/delete | “获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象的扩展信息 |
> | 行动 | Microsoft.StorSimple/managers/extendedInformation/read | 列出或获取扩展保管库信息 |
> | 行动 | Microsoft.StorSimple/Managers/extendedInformation/read | “获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象的扩展信息 |
> | 行动 | Microsoft.StorSimple/managers/extendedInformation/write | 创建或更新扩展保管库信息 |
> | 行动 | Microsoft.StorSimple/Managers/extendedInformation/write | “获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象的扩展信息 |
> | 行动 | Microsoft.StorSimple/managers/features/read | 列出功能 |
> | 行动 | Microsoft.StorSimple/managers/fileservers/read | 列出或获取文件服务器 |
> | 行动 | Microsoft.StorSimple/managers/getEncryptionKey/action | 获取设备管理器的加密密钥。 |
> | 行动 | Microsoft.StorSimple/managers/iscsiservers/read | 列出或获取 iSCSI 服务器 |
> | 行动 | Microsoft.StorSimple/managers/jobs/read | 列出或获取作业 |
> | 行动 | Microsoft.StorSimple/managers/listActivationKey/action | 获取 StorSimple 设备管理器的激活密钥。 |
> | 行动 | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | 列出 StorSimple 设备管理器的加密公钥。 |
> | 行动 | Microsoft.StorSimple/managers/metrics/read | 列出或获取指标 |
> | 行动 | Microsoft.StorSimple/managers/metricsDefinitions/read | 列出或获取指标定义 |
> | 行动 | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | 将管理器从经典模型迁移到资源管理器模型 |
> | 行动 | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | 列出迁移源配置（仅限 8000 系列） |
> | 行动 | Microsoft.StorSimple/managers/operationResults/read | 列出或获取操作结果 |
> | 行动 | Microsoft.StorSimple/managers/provisionCloudAppliance/action | 创建新的云设备。 |
> | 行动 | Microsoft.StorSimple/managers/read | 列出或获取设备管理器 |
> | 行动 | Microsoft.StorSimple/Managers/read | “获取保管库”操作获取表示“vault”类型的 Azure 资源的对象 |
> | 行动 | Microsoft.StorSimple/managers/regenerateActivationKey/action | 重新生成现有 StorSimple 设备管理器的激活密钥。 |
> | 行动 | Microsoft.StorSimple/managers/storageAccountCredentials/delete | 删除存储帐户凭据 |
> | 行动 | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | 列出或获取操作结果 |
> | 行动 | Microsoft.StorSimple/managers/storageAccountCredentials/read | 列出或获取存储帐户凭据 |
> | 行动 | Microsoft.StorSimple/managers/storageAccountCredentials/write | 创建或更新存储帐户凭据 |
> | 行动 | Microsoft.StorSimple/managers/storageDomains/delete | 删除存储域 |
> | 行动 | Microsoft.StorSimple/managers/storageDomains/operationResults/read | 列出或获取操作结果 |
> | 行动 | Microsoft.StorSimple/managers/storageDomains/read | 列出或获取存储域 |
> | 行动 | Microsoft.StorSimple/managers/storageDomains/write | 创建或更新存储域 |
> | 行动 | Microsoft.StorSimple/managers/write | 创建或更新设备管理器 |
> | 行动 | Microsoft.StorSimple/Managers/write | “创建保管库”操作创建“vault”类型的 Azure 资源 |
> | 行动 | Microsoft.StorSimple/operations/read | 列出或获取操作 |
> | 行动 | Microsoft.StorSimple/register/action | 注册提供程序 Microsoft.StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.StreamAnalytics/locations/quotas/Read | 读取流分析订阅配额 |
> | 行动 | Microsoft.StreamAnalytics/operations/Read | 读取流分析操作 |
> | 行动 | Microsoft.StreamAnalytics/Register/action | 使用流分析资源提供程序注册订阅 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/Delete | 删除流分析作业 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | 删除流分析作业函数 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | 读取流分析作业函数的操作结果 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/functions/Read | 读取流分析作业函数 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | 检索流分析作业函数的默认定义 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | 测试流分析作业函数 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/functions/Write | 写入流分析作业函数 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | 删除流分析作业输入 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | 读取流分析作业输入的操作结果 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | 读取流分析作业输入 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | 流分析作业输入采样 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | 测试流分析作业输入 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | 写入流分析作业输入 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | 读取指标定义 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | 读取流分析作业的操作结果 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | 删除流分析作业输出 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | 读取流分析作业输出的操作结果 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | 读取流分析作业输出 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | 测试流分析作业输出 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | 写入流分析作业输出 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | 读取诊断设置。 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | 写入诊断设置。 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | 获取 streamingjobs 的可用日志 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | 获取 streamingjobs 的可用指标 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/Read | 读取流分析作业 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/Start/action | 启动流分析作业 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/Stop/action | 停止流分析作业 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | 删除流分析作业转换 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | 读取流分析作业转换 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | 写入流分析作业转换 |
> | 行动 | Microsoft.StreamAnalytics/streamingjobs/Write | 写入流分析作业 |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft. 订阅/取消/操作 | 取消订阅 |
> | 行动 | Microsoft.Subscription/CreateSubscription/action | 创建 Azure 订阅 |
> | 行动 | Microsoft.Subscription/register/action | 使用 Microsoft.Subscription 资源提供程序注册订阅 |
> | 行动 | Microsoft. 订阅/重命名/操作 | 重命名订阅 |
> | 行动 | Microsoft.Subscription/SubscriptionDefinitions/read | 获取管理组中的 Azure 订阅定义。 |
> | 行动 | Microsoft.Subscription/SubscriptionDefinitions/write | 创建 Azure 订阅定义 |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.Support/register/action | 注册到支持资源提供程序 |
> | 行动 | Microsoft.Support/supportTickets/read | 获取支持票证详细信息（包括状态、严重性、联系详细信息和通信），或获取各个订阅中的支持票证列表。 |
> | 行动 | Microsoft.Support/supportTickets/write | 创建或更新支持票证。 可以针对技术、计费、配额或订阅管理相关的问题创建支持票证。 可以更新现有支持票证的严重性、联系详细信息和通信。 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | 删除访问策略。 |
> | 行动 | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | 获取访问策略的属性。 |
> | 行动 | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | 为环境创建新的访问策略，或更新现有访问策略。 |
> | 行动 | Microsoft.TimeSeriesInsights/environments/delete | 删除环境。 |
> | 行动 | Microsoft.TimeSeriesInsights/environments/eventsources/delete | 删除事件源。 |
> | 行动 | Microsoft.TimeSeriesInsights/environments/eventsources/read | 获取事件源的属性。 |
> | 行动 | Microsoft.TimeSeriesInsights/environments/eventsources/write | 为环境创建新的事件源，或更新现有事件源。 |
> | 行动 | Microsoft.TimeSeriesInsights/environments/read | 获取环境的属性。 |
> | 行动 | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | 删除参考数据集。 |
> | 行动 | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | 获取参考数据集的属性。 |
> | 行动 | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | 为环境创建新的参考数据集，或更新现有参考数据集。 |
> | 行动 | Microsoft.TimeSeriesInsights/environments/status/read | 获取环境的状态以及相关操作（如入口）的状态。 |
> | 行动 | Microsoft.TimeSeriesInsights/environments/write | 创建新环境，或更新现有环境。 |
> | 行动 | Microsoft.TimeSeriesInsights/register/action | 注册时序见解资源提供程序的订阅，并启用时序见解环境的创建。 |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.VisualStudio/Account/Delete | 删除帐户 |
> | 行动 | Microsoft.VisualStudio/Account/Extension/Read | 读取帐户/扩展 |
> | 行动 | Microsoft.VisualStudio/Account/Project/Read | 读取帐户/项目 |
> | 行动 | Microsoft.VisualStudio/Account/Project/Write | 设置帐户/项目 |
> | 行动 | Microsoft.VisualStudio/Account/Read | 读取帐户 |
> | 行动 | Microsoft.VisualStudio/Account/Write | 设置帐户 |
> | 行动 | Microsoft.VisualStudio/Extension/Delete | 删除扩展 |
> | 行动 | Microsoft.VisualStudio/Extension/Read | 读取扩展 |
> | 行动 | Microsoft.VisualStudio/Extension/Write | 设置扩展 |
> | 行动 | Microsoft.VisualStudio/Project/Delete | 删除项目 |
> | 行动 | Microsoft.VisualStudio/Project/Read | 读取项目 |
> | 行动 | Microsoft.VisualStudio/Project/Write | 设置项目 |
> | 行动 | Microsoft.VisualStudio/Register/Action | 使用 Microsoft.VisualStudio 提供程序注册 Azure 订阅 |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | microsoft.web/apimanagementaccounts/apiacls/read | 获取 API 管理帐户 Apiacls。 |
> | 行动 | microsoft.web/apimanagementaccounts/apis/apiacls/delete | 删除 API 管理帐户 API Apiacls。 |
> | 行动 | microsoft.web/apimanagementaccounts/apis/apiacls/read | 获取 API 管理帐户 API Apiacls。 |
> | 行动 | microsoft.web/apimanagementaccounts/apis/apiacls/write | 更新 API 管理帐户 API Apiacls。 |
> | 行动 | microsoft.web/apimanagementaccounts/apis/connectionacls/read | 获取 API 管理帐户 API Connectionacls。 |
> | 行动 | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | 确认同意代码 API 管理帐户 API 连接。 |
> | 行动 | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | 删除 API 管理帐户 API 连接 Connectionacls。 |
> | 行动 | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | 获取 API 管理帐户 API 连接 Connectionacls。 |
> | 行动 | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | 更新 API 管理帐户 API 连接 Connectionacls。 |
> | 行动 | microsoft.web/apimanagementaccounts/apis/connections/delete | 删除 API 管理帐户 API 连接。 |
> | 行动 | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | 获取 API 管理帐户 API 连接的同意链接。 |
> | 行动 | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | 列出连接密钥 API 管理帐户 API 连接。 |
> | 行动 | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | 列出机密 API 管理帐户 API 连接。 |
> | 行动 | microsoft.web/apimanagementaccounts/apis/connections/read | 获取 API 管理帐户 API 连接。 |
> | 行动 | microsoft.web/apimanagementaccounts/apis/connections/write | 更新 API 管理帐户 API 连接。 |
> | 行动 | microsoft.web/apimanagementaccounts/apis/delete | 删除 API 管理帐户 API。 |
> | 行动 | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | 删除 API 管理帐户 API 本地化定义。 |
> | 行动 | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | 获取 API 管理帐户 API 本地化定义。 |
> | 行动 | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | 更新 API 管理帐户 API 本地化定义。 |
> | 行动 | microsoft.web/apimanagementaccounts/apis/read | 获取 API 管理帐户 API。 |
> | 行动 | microsoft.web/apimanagementaccounts/apis/write | 更新 API 管理帐户 API。 |
> | 行动 | microsoft.web/apimanagementaccounts/connectionacls/read | 获取 API 管理帐户 Connectionacls。 |
> | 行动 | microsoft.web/availablestacks/read | 获取可用堆栈。 |
> | 行动 | Microsoft.Web/certificates/Delete | 删除现有证书。 |
> | 行动 | Microsoft.Web/certificates/Read | 获取证书列表。 |
> | 行动 | Microsoft.Web/certificates/Write | 添加新的或更新现有的证书。 |
> | 行动 | microsoft.web/checknameavailability/read | 检查资源名称是否可用。 |
> | 行动 | microsoft.web/classicmobileservices/read | 获取经典移动服务。 |
> | 行动 | Microsoft.Web/connectionGateways/Delete | 删除连接网关。 |
> | 行动 | Microsoft.Web/connectionGateways/Join/Action | 加入连接网关。 |
> | 行动 | Microsoft.Web/connectionGateways/ListStatus/Action | 列出连接网关的状态。 |
> | 行动 | Microsoft.Web/connectionGateways/Move/Action | 移动连接网关。 |
> | 行动 | Microsoft.Web/connectionGateways/Read | 获取连接网关列表。 |
> | 行动 | Microsoft.Web/connectionGateways/Write | 创建或更新连接网关。 |
> | 行动 | microsoft.web/connections/confirmconsentcode/action | 确认连接许可代码。 |
> | 行动 | Microsoft.Web/connections/Delete | 删除连接。 |
> | 行动 | Microsoft.Web/connections/Join/Action | 加入连接。 |
> | 行动 | microsoft.web/connections/listconsentlinks/action | 列出连接的许可链接。 |
> | 行动 | Microsoft.Web/connections/Move/Action | 移动连接。 |
> | 行动 | Microsoft.Web/connections/Read | 获取连接列表。 |
> | 行动 | Microsoft.Web/connections/Write | 创建或更新连接。 |
> | 行动 | Microsoft.Web/customApis/Delete | 删除自定义 API。 |
> | 行动 | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | 从 WSDL 中提取 API 定义。 |
> | 行动 | Microsoft.Web/customApis/Join/Action | 加入自定义 API。 |
> | 行动 | Microsoft.Web/customApis/listWsdlInterfaces/Action | 列出自定义 API 的 WSDL 接口。 |
> | 行动 | Microsoft.Web/customApis/Move/Action | 移动自定义 API。 |
> | 行动 | Microsoft.Web/customApis/Read | 获取自定义 API 的列表。 |
> | 行动 | Microsoft.Web/customApis/Write | 创建或更新自定义 API。 |
> | 行动 | Microsoft.Web/deletedSites/Read | 获取已删除的 Web 应用的属性 |
> | 行动 | microsoft.web/deploymentlocations/read | 获取部署位置。 |
> | 行动 | Microsoft.Web/geoRegions/Read | 获取地理区域的列表。 |
> | 行动 | microsoft.web/hostingenvironments/capacities/read | 获取宿主环境容量。 |
> | 行动 | Microsoft.Web/hostingEnvironments/Delete | 删除应用服务环境 |
> | 行动 | microsoft.web/hostingenvironments/detectors/read | 获取宿主环境检测器。 |
> | 行动 | microsoft.web/hostingenvironments/diagnostics/read | 获取宿主环境诊断。 |
> | 行动 | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | 获取所有入站依赖项的网络终结点。 |
> | 行动 | System.web/hostingEnvironments/Join/Action | 联接应用服务环境 |
> | 行动 | microsoft.web/hostingenvironments/metricdefinitions/read | 获取宿主环境的指标定义。 |
> | 行动 | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | 获取宿主环境的多角色池指标定义。 |
> | 行动 | microsoft.web/hostingenvironments/multirolepools/metrics/read | 获取宿主环境的多角色池指标。 |
> | 行动 | Microsoft.Web/hostingEnvironments/multiRolePools/Read | 获取应用服务环境中前端池的属性 |
> | 行动 | microsoft.web/hostingenvironments/multirolepools/skus/read | 获取宿主环境的多角色池 SKU。 |
> | 行动 | microsoft.web/hostingenvironments/multirolepools/usages/read | 获取宿主环境的多角色池使用情况。 |
> | 行动 | Microsoft.Web/hostingEnvironments/multiRolePools/Write | 在应用服务环境中创建新的或更新现有的前端池 |
> | 行动 | microsoft.web/hostingenvironments/operations/read | 获取宿主环境操作。 |
> | 行动 | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | 获取所有出站依赖项的网络终结点。 |
> | 行动 | System.web/hostingEnvironments/PrivateEndpointConnectionsApproval/action | 批准专用终结点连接 |
> | 行动 | Microsoft.Web/hostingEnvironments/Read | 获取应用服务环境的属性 |
> | 行动 | Microsoft.Web/hostingEnvironments/reboot/Action | 重新启动应用服务环境中的所有计算机 |
> | 行动 | microsoft.web/hostingenvironments/resume/action | 恢复宿主环境。 |
> | 行动 | microsoft.web/hostingenvironments/serverfarms/read | 获取宿主环境的应用服务计划。 |
> | 行动 | microsoft.web/hostingenvironments/sites/read | 获取宿主环境的 Web 应用。 |
> | 行动 | microsoft.web/hostingenvironments/suspend/action | 暂停宿主环境。 |
> | 行动 | microsoft.web/hostingenvironments/usages/read | 获取宿主环境使用情况。 |
> | 行动 | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | 获取宿主环境的辅助角色池指标定义。 |
> | 行动 | microsoft.web/hostingenvironments/workerpools/metrics/read | 获取宿主环境的辅助角色池指标。 |
> | 行动 | Microsoft.Web/hostingEnvironments/workerPools/Read | 获取应用服务环境中辅助角色池的属性 |
> | 行动 | microsoft.web/hostingenvironments/workerpools/skus/read | 获取宿主环境的辅助角色池 SKU。 |
> | 行动 | microsoft.web/hostingenvironments/workerpools/usages/read | 获取宿主环境的辅助角色池使用情况。 |
> | 行动 | Microsoft.Web/hostingEnvironments/workerPools/Write | 在应用服务环境中创建新的或更新现有的辅助角色池 |
> | 行动 | Microsoft.Web/hostingEnvironments/Write | 创建新的或更新现有的应用服务环境 |
> | 行动 | microsoft.web/ishostingenvironmentnameavailable/read | 检查宿主环境名称是否可用。 |
> | 行动 | microsoft.web/ishostnameavailable/read | 检查主机名是否可用。 |
> | 行动 | microsoft.web/isusernameavailable/read | 检查用户名是否可用。 |
> | 行动 | Microsoft.Web/listSitesAssignedToHostName/Read | 获取分配给主机名的站点名称。 |
> | 行动 | microsoft.web/locations/apioperations/read | 获取位置 API 操作。 |
> | 行动 | microsoft.web/locations/connectiongatewayinstallations/read | 获取位置连接网关安装。 |
> | 行动 | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | 位置的 VNet 或子网删除通知。 |
> | 行动 | microsoft.web/locations/extractapidefinitionfromwsdl/action | 从位置的 WSDL 中提取 API 定义。 |
> | 行动 | microsoft.web/locations/listwsdlinterfaces/action | 列出位置的 WSDL 接口。 |
> | 行动 | microsoft.web/locations/managedapis/apioperations/read | 获取位置托管 API 操作。 |
> | 行动 | Microsoft.Web/locations/managedapis/Join/Action | 联接托管的 API。 |
> | 行动 | microsoft.web/locations/managedapis/read | 获取位置托管 API。 |
> | 行动 | system.web/位置/operationResults/读取 | 获取操作。 |
> | 行动 | system.web/位置/操作/读取 | 获取操作。 |
> | 行动 | microsoft.web/operations/read | 获取操作。 |
> | 行动 | microsoft.web/publishingusers/read | 获取发布用户。 |
> | 行动 | microsoft.web/publishingusers/write | 更新发布用户。 |
> | 行动 | Microsoft.Web/recommendations/Read | 获取订阅的建议列表。 |
> | 行动 | microsoft.web/register/action | 注册订阅的 Microsoft.Web 资源提供程序。 |
> | 行动 | microsoft.web/resourcehealthmetadata/read | 获取资源运行状况元数据。 |
> | 行动 | microsoft.web/serverfarms/capabilities/read | 获取应用服务计划功能。 |
> | 行动 | Microsoft.Web/serverfarms/Delete | 删除现有的应用服务计划 |
> | 行动 | System.web/serverfarms/eventGridFilters/delete | 删除服务器场中的事件网格筛选器。 |
> | 行动 | System.web/serverfarms/eventGridFilters/read | 获取服务器场中的事件网格筛选器。 |
> | 行动 | System.web/serverfarms/eventGridFilters/write | 将事件网格筛选器放在服务器场中。 |
> | 行动 | microsoft.web/serverfarms/firstpartyapps/settings/delete | 删除应用服务计划第一方应用设置。 |
> | 行动 | microsoft.web/serverfarms/firstpartyapps/settings/read | 获取应用服务计划第一方应用设置。 |
> | 行动 | microsoft.web/serverfarms/firstpartyapps/settings/write | 更新应用服务计划第一方应用设置。 |
> | 行动 | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | 删除应用服务计划混合连接命名空间中继。 |
> | 行动 | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | 获取应用服务计划混合连接命名空间中继。 |
> | 行动 | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | 获取应用服务计划混合连接命名空间中继 Web 应用。 |
> | 行动 | microsoft.web/serverfarms/hybridconnectionplanlimits/read | 获取应用服务计划混合连接计划限制。 |
> | 行动 | microsoft.web/serverfarms/hybridconnectionrelays/read | 获取应用服务计划混合连接中继。 |
> | 行动 | microsoft.web/serverfarms/metricdefinitions/read | 获取应用服务计划指标定义。 |
> | 行动 | microsoft.web/serverfarms/metrics/read | 获取应用服务计划指标。 |
> | 行动 | microsoft.web/serverfarms/operationresults/read | 获取应用服务计划操作结果。 |
> | 行动 | Microsoft.Web/serverfarms/Read | 获取应用服务计划的属性 |
> | 行动 | Microsoft.Web/serverfarms/restartSites/Action | 重新启动应用服务计划中的所有 Web 应用 |
> | 行动 | microsoft.web/serverfarms/sites/read | 获取应用服务计划 Web 应用。 |
> | 行动 | microsoft.web/serverfarms/skus/read | 获取应用服务计划 SKU。 |
> | 行动 | microsoft.web/serverfarms/usages/read | 获取应用服务计划使用情况。 |
> | 行动 | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | 更新应用服务计划虚拟网络连接网关。 |
> | 行动 | microsoft.web/serverfarms/virtualnetworkconnections/read | 获取应用服务计划虚拟网络连接。 |
> | 行动 | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | 删除应用服务计划虚拟网络连接路由。 |
> | 行动 | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | 获取应用服务计划虚拟网络连接路由。 |
> | 行动 | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | 更新应用服务计划虚拟网络连接路由。 |
> | 行动 | microsoft.web/serverfarms/workers/reboot/action | 重新启动应用服务计划辅助角色。 |
> | 行动 | Microsoft.Web/serverfarms/Write | 创建新的或更新现有的应用服务计划。 |
> | 行动 | microsoft.web/sites/analyzecustomhostname/read | 分析自定义主机名。 |
> | 行动 | Microsoft.Web/sites/applySlotConfig/Action | 将目标槽中的 Web 应用槽配置应用到当前 Web 应用 |
> | 行动 | Microsoft.Web/sites/backup/Action | 创建新的 Web 应用备份 |
> | 行动 | microsoft.web/sites/backup/read | 获取 Web 应用备份。 |
> | 行动 | microsoft.web/sites/backup/write | 更新 Web 应用备份。 |
> | 行动 | microsoft.web/sites/backups/action | 发现可从 Azure 存储中的 blob 还原的现有应用备份。 |
> | 行动 | microsoft.web/sites/backups/delete | 删除 Web 应用备份。 |
> | 行动 | microsoft.web/sites/backups/list/action | 列出 Web 应用备份。 |
> | 行动 | Microsoft.Web/sites/backups/Read | 获取 Web 应用的备份属性 |
> | 行动 | microsoft.web/sites/backups/restore/action | 还原 Web 应用备份。 |
> | 行动 | microsoft.web/sites/backups/write | 更新 Web 应用备份。 |
> | 行动 | microsoft.web/sites/config/delete | 删除 Web 应用配置。 |
> | 行动 | Microsoft.Web/sites/config/list/Action | 列出 Web 应用的安全敏感设置，例如发布凭据、应用设置和连接字符串 |
> | 行动 | Microsoft.Web/sites/config/Read | 获取 Web 应用配置设置 |
> | 行动 | microsoft.web/sites/config/snapshots/read | 获取 Web 应用配置快照。 |
> | 行动 | Microsoft.Web/sites/config/Write | 更新 Web 应用的配置设置 |
> | 行动 | microsoft.web/sites/containerlogs/action | 获取 Web 应用的压缩容器日志。 |
> | 行动 | microsoft.web/sites/containerlogs/download/action | 下载 Web 应用容器日志。 |
> | 行动 | microsoft.web/sites/continuouswebjobs/delete | 删除 Web 应用连续 Web 作业。 |
> | 行动 | microsoft.web/sites/continuouswebjobs/read | 获取 Web 应用连续 Web 作业。 |
> | 行动 | microsoft.web/sites/continuouswebjobs/start/action | 启动 Web 应用连续 Web 作业。 |
> | 行动 | microsoft.web/sites/continuouswebjobs/stop/action | 停止 Web 应用连续 Web 作业。 |
> | 行动 | Microsoft.Web/sites/Delete | 删除现有的 Web 应用 |
> | 行动 | microsoft.web/sites/deployments/delete | 删除 Web 应用部署。 |
> | 行动 | microsoft.web/sites/deployments/log/read | 获取 Web 应用部署日志。 |
> | 行动 | microsoft.web/sites/deployments/read | 获取 Web 应用部署。 |
> | 行动 | microsoft.web/sites/deployments/write | 更新 Web 应用部署。 |
> | 行动 | microsoft.web/sites/detectors/read | 获取 Web 应用检测器。 |
> | 行动 | microsoft.web/sites/diagnostics/analyses/execute/Action | 运行 Web 应用诊断分析。 |
> | 行动 | microsoft.web/sites/diagnostics/analyses/read | 获取 Web 应用诊断分析。 |
> | 行动 | microsoft.web/sites/diagnostics/aspnetcore/read | 获取 ASP.NET Core 应用的 Web 应用诊断。 |
> | 行动 | microsoft.web/sites/diagnostics/autoheal/read | 获取 Web 应用诊断 Autoheal。 |
> | 行动 | microsoft.web/sites/diagnostics/deployment/read | 获取 Web 应用诊断部署。 |
> | 行动 | microsoft.web/sites/diagnostics/deployments/read | 获取 Web 应用诊断部署。 |
> | 行动 | microsoft.web/sites/diagnostics/detectors/execute/Action | 运行 Web 应用诊断检测程序。 |
> | 行动 | microsoft.web/sites/diagnostics/detectors/read | 获取 Web 应用诊断检测程序。 |
> | 行动 | microsoft.web/sites/diagnostics/failedrequestsperuri/read | 获取每个 Uri 的 Web 应用诊断失败的请求。 |
> | 行动 | microsoft.web/sites/diagnostics/frebanalysis/read | 获取 Web 应用诊断 FREB 分析。 |
> | 行动 | microsoft.web/sites/diagnostics/loganalyzer/read | 获取 Web 应用诊断日志分析器。 |
> | 行动 | microsoft.web/sites/diagnostics/read | 获取 Web 应用诊断类别。 |
> | 行动 | microsoft.web/sites/diagnostics/runtimeavailability/read | 获取 Web 应用诊断运行时可用性。 |
> | 行动 | microsoft.web/sites/diagnostics/servicehealth/read | 获取 Web 应用诊断服务运行状况。 |
> | 行动 | microsoft.web/sites/diagnostics/sitecpuanalysis/read | 获取 Web 应用诊断站点 CPU 分析。 |
> | 行动 | microsoft.web/sites/diagnostics/sitecrashes/read | 获取 Web 应用诊断站点故障。 |
> | 行动 | microsoft.web/sites/diagnostics/sitelatency/read | 获取 Web 应用诊断站点延迟。 |
> | 行动 | microsoft.web/sites/diagnostics/sitememoryanalysis/read | 获取 Web 应用诊断站点内存分析。 |
> | 行动 | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | 获取 Web 应用诊断站点重启设置更新。 |
> | 行动 | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | 获取 Web 应用诊断站点重启用户发起时间。 |
> | 行动 | microsoft.web/sites/diagnostics/siteswap/read | 获取 Web 应用诊断站点交换。 |
> | 行动 | microsoft.web/sites/diagnostics/threadcount/read | 获取 Web 应用诊断线程计数。 |
> | 行动 | microsoft.web/sites/diagnostics/workeravailability/read | 获取 Web 应用诊断 Workeravailability。 |
> | 行动 | microsoft.web/sites/diagnostics/workerprocessrecycle/read | 获取 Web 应用诊断工作进程回收。 |
> | 行动 | microsoft.web/sites/domainownershipidentifiers/read | 获取 Web 应用域所有权标识符。 |
> | 行动 | microsoft.web/sites/domainownershipidentifiers/write | 更新 Web 应用域所有权标识符。 |
> | 行动 | System.web/sites/eventGridFilters/delete | 删除 web 应用上的事件网格筛选器。 |
> | 行动 | System.web/sites/eventGridFilters/read | 获取 web 应用的事件网格筛选器。 |
> | 行动 | Node.js/站点/eventGridFilters/写入 | 在 web 应用上放置事件网格筛选器。 |
> | 行动 | microsoft. web/站点/扩展/删除 | 删除 Web 应用站点扩展。 |
> | 行动 | microsoft. web/站点/扩展/读取 | 获取 Web 应用站点扩展。 |
> | 行动 | microsoft. web/站点/扩展/写入 | 更新 Web 应用站点扩展。 |
> | 行动 | microsoft.web/sites/functions/action | 函数 Web 应用。 |
> | 行动 | microsoft.web/sites/functions/delete | 删除 Web 应用函数。 |
> | 行动 | system.web/sites/function/keys/delete | 删除功能键。 |
> | 行动 | microsoft. web/站点/函数/键/写入 | 更新功能键。 |
> | 行动 | system.web/sites/function/listkeys/action | 列出功能键。 |
> | 行动 | microsoft.web/sites/functions/listsecrets/action | 列出函数密码。 |
> | 行动 | microsoft.web/sites/functions/masterkey/read | 获取 Web 应用函数主密钥。 |
> | 行动 | microsoft. web/站点/函数/属性/读取 | 读取 Web 应用函数属性。 |
> | 行动 | microsoft. web/站点/函数/属性/写入 | 更新 Web 应用函数属性。 |
> | 行动 | microsoft.web/sites/functions/read | 获取 Web 应用函数。 |
> | 行动 | microsoft.web/sites/functions/token/read | 获取 Web 应用函数令牌。 |
> | 行动 | microsoft.web/sites/functions/write | 更新 Web 应用函数。 |
> | 行动 | system.web/sites/host/functionkeys/delete | Delete 函数承载函数键。 |
> | 行动 | system.web/sites/host/functionkeys/write | 更新函数承载函数键。 |
> | 行动 | system.web/sites/host/listkeys/action | 列出函数主机密钥。 |
> | 行动 | system.web/sites/host/listsyncstatus/action | 列出同步函数触发器状态。 |
> | 行动 | microsoft. web/站点/主机/属性/读取 | 读取 Web 应用函数主机属性。 |
> | 行动 | microsoft. web/站点/主机/同步/操作 | 同步函数触发器。 |
> | 行动 | system.web/sites/host/systemkeys/delete | 删除函数承载系统键。 |
> | 行动 | system.web/sites/host/systemkeys/write | 更新函数承载系统键。 |
> | 行动 | microsoft.web/sites/hostnamebindings/delete | 删除 Web 应用主机名绑定。 |
> | 行动 | microsoft.web/sites/hostnamebindings/read | 获取 Web 应用主机名绑定。 |
> | 行动 | microsoft.web/sites/hostnamebindings/write | 更新 Web 应用主机名绑定。 |
> | 行动 | microsoft.web/sites/hostruntime/functions/keys/read | 获取 Web 应用 Hostruntime 函数密钥。 |
> | 行动 | Microsoft.Web/sites/hostruntime/host/_master/read | 获取管理员操作的 Function App 主密钥 |
> | 行动 | Microsoft.Web/sites/hostruntime/host/action | 执行 Function App 运行时操作，例如同步触发器、添加函数、调用函数、删除函数等。 |
> | 行动 | microsoft.web/sites/hostruntime/host/read | 获取 Web 应用 Hostruntime 主机。 |
> | 行动 | microsoft.web/sites/hybridconnection/delete | 删除 Web 应用混合连接。 |
> | 行动 | microsoft.web/sites/hybridconnection/read | 获取 Web 应用混合连接。 |
> | 行动 | microsoft.web/sites/hybridconnection/write | 更新 Web 应用混合连接。 |
> | 行动 | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | 删除 Web 应用混合连接命名空间中继。 |
> | 行动 | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | 列出密钥 Web 应用混合连接命名空间中继。 |
> | 行动 | microsoft.web/sites/hybridconnectionnamespaces/relays/read | 获取 Web 应用混合连接命名空间中继。 |
> | 行动 | microsoft.web/sites/hybridconnectionnamespaces/relays/write | 更新 Web 应用混合连接命名空间中继。 |
> | 行动 | microsoft.web/sites/hybridconnectionrelays/read | 获取 Web 应用混合连接中继。 |
> | 行动 | microsoft.web/sites/instances/deployments/delete | 删除 Web 应用实例部署。 |
> | 行动 | microsoft.web/sites/instances/deployments/read | 获取 Web 应用实例部署。 |
> | 行动 | microsoft.web/sites/instances/extensions/log/read | 获取 Web 应用实例扩展日志。 |
> | 行动 | microsoft.web/sites/instances/extensions/processes/read | 获取 Web 应用实例扩展进程。 |
> | 行动 | microsoft.web/sites/instances/extensions/read | 获取 Web 应用实例扩展。 |
> | 行动 | microsoft.web/sites/instances/processes/delete | 删除 Web 应用实例进程。 |
> | 行动 | microsoft.web/sites/instances/processes/modules/read | 获取 Web 应用实例进程模块。 |
> | 行动 | microsoft.web/sites/instances/processes/read | 获取 Web 应用实例进程。 |
> | 行动 | microsoft.web/sites/instances/processes/threads/read | 获取 Web 应用实例进程线程。 |
> | 行动 | microsoft.web/sites/instances/read | 获取 Web 应用实例。 |
> | 行动 | microsoft.web/sites/listsyncfunctiontriggerstatus/action | 列出同步函数触发器状态。 |
> | 行动 | microsoft.web/sites/metricdefinitions/read | 获取 Web 应用指标定义。 |
> | 行动 | microsoft.web/sites/metrics/read | 获取 Web 应用指标。 |
> | 行动 | microsoft.web/sites/metricsdefinitions/read | 获取 Web 应用指标定义。 |
> | 行动 | microsoft.web/sites/migratemysql/action | 迁移 MySql Web 应用。 |
> | 行动 | microsoft.web/sites/migratemysql/read | 获取 Web 应用迁移 MySql。 |
> | 行动 | microsoft.web/sites/networktrace/action | 网络跟踪 Web 应用。 |
> | 行动 | system.web/sites/networktraces/operationresults/read | 获取 Web 应用网络跟踪操作结果。 |
> | 行动 | microsoft.web/sites/newpassword/action | Newpassword Web 应用。 |
> | 行动 | microsoft.web/sites/operationresults/read | 获取 Web 应用操作结果。 |
> | 行动 | microsoft.web/sites/operations/read | 获取 Web 应用操作。 |
> | 行动 | microsoft.web/sites/perfcounters/read | 获取 Web 应用性能计数器。 |
> | 行动 | microsoft.web/sites/premieraddons/delete | 删除 Web 应用高级加载项。 |
> | 行动 | microsoft.web/sites/premieraddons/read | 获取 Web 应用高级加载项。 |
> | 行动 | microsoft.web/sites/premieraddons/write | 更新 Web 应用高级加载项。 |
> | 行动 | microsoft.web/sites/privateaccess/read | 获取有关可访问站点的专用站点访问支持和授权虚拟网络的数据。 |
> | 行动 | System.web/sites/PrivateEndpointConnectionsApproval/action | 批准专用终结点连接 |
> | 行动 | microsoft. web/站点/进程/模块/读取 | 获取 Web 应用进程模块。 |
> | 行动 | microsoft.web/sites/processes/read | 获取 Web 应用进程。 |
> | 行动 | microsoft. web/站点/进程/线程/读取 | 获取 Web 应用进程线程。 |
> | 行动 | microsoft.web/sites/publiccertificates/delete | 删除 Web 应用公共证书。 |
> | 行动 | microsoft.web/sites/publiccertificates/read | 获取 Web 应用公共证书。 |
> | 行动 | microsoft.web/sites/publiccertificates/write | 更新 Web 应用公共证书。 |
> | 行动 | Microsoft.Web/sites/publish/Action | 发布 Web 应用 |
> | 行动 | Microsoft.Web/sites/publishxml/Action | 获取 Web 应用的发布配置文件 xml |
> | 行动 | microsoft.web/sites/publishxml/read | 获取 Web 应用发布 XML。 |
> | 行动 | Microsoft.Web/sites/Read | 获取 Web 应用的属性 |
> | 行动 | microsoft.web/sites/recommendationhistory/read | 获取 Web 应用建议历史记录。 |
> | 行动 | microsoft.web/sites/recommendations/disable/action | 禁用 Web 应用建议。 |
> | 行动 | Microsoft.Web/sites/recommendations/Read | 获取 Web 应用的建议列表。 |
> | 行动 | microsoft.web/sites/recover/action | 恢复 Web 应用。 |
> | 行动 | Microsoft.Web/sites/resetSlotConfig/Action | 重置 Web 应用配置 |
> | 行动 | microsoft.web/sites/resourcehealthmetadata/read | 获取 Web 应用资源运行状况元数据。 |
> | 行动 | Microsoft.Web/sites/restart/Action | 重新启动 Web 应用 |
> | 行动 | microsoft.web/sites/restore/read | 获取 Web 应用还原设置。 |
> | 行动 | microsoft.web/sites/restore/write | 还原 Web 应用。 |
> | 行动 | microsoft.web/sites/restorefrombackupblob/action | 基于备份 Blob 还原 Web 应用。 |
> | 行动 | system.web/sites/restorefromdeletedapp/action | 从已删除的应用还原 Web 应用。 |
> | 行动 | microsoft.web/sites/restoresnapshot/action | 还原 Web 应用快照。 |
> | 行动 | microsoft.web/sites/siteextensions/delete | 删除 Web 应用站点扩展。 |
> | 行动 | microsoft.web/sites/siteextensions/read | 获取 Web 应用站点扩展。 |
> | 行动 | microsoft.web/sites/siteextensions/write | 更新 Web 应用站点扩展。 |
> | 行动 | microsoft.web/sites/slots/analyzecustomhostname/read | 获取 Web 应用槽分析自定义主机名。 |
> | 行动 | Microsoft.Web/sites/slots/applySlotConfig/Action | 将目标槽中的 Web 应用槽配置应用到当前槽 |
> | 行动 | Microsoft.Web/sites/slots/backup/Action | 创建新的 Web 应用槽备份。 |
> | 行动 | microsoft.web/sites/slots/backup/read | 获取 Web 应用槽备份。 |
> | 行动 | microsoft.web/sites/slots/backup/write | 更新 Web 应用槽备份。 |
> | 行动 | microsoft.web/sites/slots/backups/action | 发现 Web 应用槽备份。 |
> | 行动 | microsoft.web/sites/slots/backups/delete | 删除 Web 应用槽备份。 |
> | 行动 | microsoft.web/sites/slots/backups/list/action | 列出 Web 应用槽备份。 |
> | 行动 | Microsoft.Web/sites/slots/backups/Read | 获取 Web 应用槽的备份属性 |
> | 行动 | microsoft.web/sites/slots/backups/restore/action | 还原 Web 应用槽备份。 |
> | 行动 | microsoft.web/sites/slots/config/delete | 删除 Web 应用槽配置。 |
> | 行动 | Microsoft.Web/sites/slots/config/list/Action | 列出 Web 应用槽的安全敏感设置，例如发布凭据、应用设置和连接字符串 |
> | 行动 | Microsoft.Web/sites/slots/config/Read | 获取 Web 应用槽的配置设置 |
> | 行动 | Microsoft.Web/sites/slots/config/Write | 更新 Web 应用槽的配置设置 |
> | 行动 | microsoft.web/sites/slots/containerlogs/action | 获取 Web 应用槽的压缩容器日志。 |
> | 行动 | microsoft.web/sites/slots/containerlogs/download/action | 下载 Web 应用槽容器日志。 |
> | 行动 | microsoft.web/sites/slots/continuouswebjobs/delete | 删除 Web 应用槽连续 Web 作业。 |
> | 行动 | microsoft.web/sites/slots/continuouswebjobs/read | 获取 Web 应用槽连续 Web 作业。 |
> | 行动 | microsoft.web/sites/slots/continuouswebjobs/start/action | 启动 Web 应用槽连续 Web 作业。 |
> | 行动 | microsoft.web/sites/slots/continuouswebjobs/stop/action | 停止 Web 应用槽连续 Web 作业。 |
> | 行动 | Microsoft.Web/sites/slots/Delete | 删除现有的 Web 应用槽 |
> | 行动 | microsoft.web/sites/slots/deployments/delete | 删除 Web 应用槽部署。 |
> | 行动 | microsoft.web/sites/slots/deployments/log/read | 获取 Web 应用槽部署日志。 |
> | 行动 | microsoft.web/sites/slots/deployments/read | 获取 Web 应用槽部署。 |
> | 行动 | microsoft.web/sites/slots/deployments/write | 更新 Web 应用槽部署。 |
> | 行动 | microsoft.web/sites/slots/detectors/read | 获取 Web 应用槽检测程序。 |
> | 行动 | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | 运行 Web 应用槽诊断分析。 |
> | 行动 | microsoft.web/sites/slots/diagnostics/analyses/read | 获取 Web 应用槽诊断分析。 |
> | 行动 | microsoft.web/sites/slots/diagnostics/aspnetcore/read | 获取 ASP.NET Core 应用的 Web 应用槽诊断。 |
> | 行动 | microsoft.web/sites/slots/diagnostics/autoheal/read | 获取 Web 应用槽诊断 Autoheal。 |
> | 行动 | microsoft.web/sites/slots/diagnostics/deployment/read | 获取 Web 应用槽诊断部署。 |
> | 行动 | microsoft.web/sites/slots/diagnostics/deployments/read | 获取 Web 应用槽诊断部署。 |
> | 行动 | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | 运行 Web 应用槽诊断检测程序。 |
> | 行动 | microsoft.web/sites/slots/diagnostics/detectors/read | 获取 Web 应用槽诊断检测程序。 |
> | 行动 | microsoft.web/sites/slots/diagnostics/frebanalysis/read | 获取 Web 应用槽诊断 FREB 分析。 |
> | 行动 | microsoft.web/sites/slots/diagnostics/loganalyzer/read | 获取 Web 应用槽诊断日志分析器。 |
> | 行动 | microsoft.web/sites/slots/diagnostics/read | 获取 Web 应用槽诊断。 |
> | 行动 | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | 获取 Web 应用槽诊断运行时可用性。 |
> | 行动 | microsoft.web/sites/slots/diagnostics/servicehealth/read | 获取 Web 应用槽诊断服务运行状况。 |
> | 行动 | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | 获取 Web 应用槽诊断站点 CPU 分析。 |
> | 行动 | microsoft.web/sites/slots/diagnostics/sitecrashes/read | 获取 Web 应用槽诊断站点故障。 |
> | 行动 | microsoft.web/sites/slots/diagnostics/sitelatency/read | 获取 Web 应用槽诊断站点延迟。 |
> | 行动 | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | 获取 Web 应用槽诊断站点内存分析。 |
> | 行动 | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | 获取 Web 应用槽诊断站点重启设置更新。 |
> | 行动 | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | 获取 Web 应用槽诊断站点重启用户发起时间。 |
> | 行动 | microsoft.web/sites/slots/diagnostics/siteswap/read | 获取 Web 应用槽诊断站点交换。 |
> | 行动 | microsoft.web/sites/slots/diagnostics/threadcount/read | 获取 Web 应用槽诊断线程计数。 |
> | 行动 | microsoft.web/sites/slots/diagnostics/workeravailability/read | 获取 Web 应用槽诊断 Workeravailability。 |
> | 行动 | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | 获取 Web 应用槽诊断工作进程回收。 |
> | 行动 | microsoft.web/sites/slots/domainownershipidentifiers/read | 获取 Web 应用槽域所有权标识符。 |
> | 行动 | microsoft.web/sites/slots/functions/listsecrets/action | 列出机密 Web 应用槽函数。 |
> | 行动 | microsoft.web/sites/slots/functions/read | 获取 Web 应用槽函数。 |
> | 行动 | microsoft.web/sites/slots/hostnamebindings/delete | 删除 Web 应用槽主机名绑定。 |
> | 行动 | microsoft.web/sites/slots/hostnamebindings/read | 获取 Web 应用槽主机名绑定。 |
> | 行动 | microsoft.web/sites/slots/hostnamebindings/write | 更新 Web 应用槽主机名绑定。 |
> | 行动 | microsoft.web/sites/slots/hybridconnection/delete | 删除 Web 应用槽混合连接。 |
> | 行动 | microsoft.web/sites/slots/hybridconnection/read | 获取 Web 应用槽混合连接。 |
> | 行动 | microsoft.web/sites/slots/hybridconnection/write | 更新 Web 应用槽混合连接。 |
> | 行动 | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | 删除 Web 应用槽混合连接命名空间中继。 |
> | 行动 | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | 更新 Web 应用槽混合连接命名空间中继。 |
> | 行动 | microsoft.web/sites/slots/hybridconnectionrelays/read | 获取 Web 应用槽混合连接中继。 |
> | 行动 | microsoft.web/sites/slots/instances/deployments/read | 获取 Web 应用槽实例部署。 |
> | 行动 | microsoft.web/sites/slots/instances/processes/delete | 删除 Web 应用槽实例进程。 |
> | 行动 | microsoft.web/sites/slots/instances/processes/read | 获取 Web 应用槽实例进程。 |
> | 行动 | microsoft.web/sites/slots/instances/read | 获取 Web 应用槽实例。 |
> | 行动 | microsoft.web/sites/slots/metricdefinitions/read | 获取 Web 应用槽指标定义。 |
> | 行动 | microsoft.web/sites/slots/metrics/read | 获取 Web 应用槽指标。 |
> | 行动 | microsoft.web/sites/slots/migratemysql/read | 获取 Web 应用槽迁移 MySql。 |
> | 行动 | microsoft.web/sites/slots/networktrace/action | 网络跟踪 Web 应用槽。 |
> | 行动 | system.web/sites/槽位/networktraces/operationresults/read | 获取 Web 应用槽网络跟踪操作结果。 |
> | 行动 | microsoft.web/sites/slots/newpassword/action | Newpassword Web 应用槽。 |
> | 行动 | microsoft.web/sites/slots/operationresults/read | 获取 Web 应用槽操作结果。 |
> | 行动 | microsoft.web/sites/slots/operations/read | 获取 Web 应用槽操作。 |
> | 行动 | microsoft.web/sites/slots/perfcounters/read | 获取 Web 应用槽性能计数器。 |
> | 行动 | microsoft.web/sites/slots/phplogging/read | 获取 Web 应用槽 Phplogging。 |
> | 行动 | microsoft.web/sites/slots/premieraddons/delete | 删除 Web 应用槽高级加载项。 |
> | 行动 | microsoft.web/sites/slots/premieraddons/read | 获取 Web 应用槽高级加载项。 |
> | 行动 | microsoft.web/sites/slots/premieraddons/write | 更新 Web 应用槽高级加载项。 |
> | 行动 | microsoft.web/sites/slots/processes/read | 获取 Web 应用槽进程。 |
> | 行动 | microsoft.web/sites/slots/publiccertificates/delete | 删除 Web 应用槽位公共证书。 |
> | 行动 | microsoft.web/sites/slots/publiccertificates/read | 获取 Web 应用槽公共证书。 |
> | 行动 | microsoft.web/sites/slots/publiccertificates/write | 创建或更新 Web 应用槽公共证书。 |
> | 行动 | Microsoft.Web/sites/slots/publish/Action | 发布 Web 应用槽 |
> | 行动 | Microsoft.Web/sites/slots/publishxml/Action | 获取 Web 应用槽的发布配置文件 xml |
> | 行动 | Microsoft.Web/sites/slots/Read | 获取 Web 应用部署槽的属性 |
> | 行动 | microsoft.web/sites/slots/recover/action | 恢复 Web 应用槽。 |
> | 行动 | Microsoft.Web/sites/slots/resetSlotConfig/Action | 重置 Web 应用槽配置 |
> | 行动 | microsoft.web/sites/slots/resourcehealthmetadata/read | 获取 Web 应用槽资源运行状况元数据。 |
> | 行动 | Microsoft.Web/sites/slots/restart/Action | 重新启动 Web 应用槽 |
> | 行动 | microsoft.web/sites/slots/restore/read | 获取 Web 应用槽还原设置。 |
> | 行动 | microsoft.web/sites/slots/restore/write | 还原 Web 应用槽。 |
> | 行动 | microsoft.web/sites/slots/restorefrombackupblob/action | 从备份 Blob 还原 Web 应用槽。 |
> | 行动 | system.web/sites/槽位/restorefromdeletedapp/action | 从已删除的应用还原 Web 应用槽位。 |
> | 行动 | microsoft.web/sites/slots/restoresnapshot/action | 还原 Web 应用槽快照。 |
> | 行动 | microsoft.web/sites/slots/siteextensions/delete | 删除 Web 应用槽站点扩展。 |
> | 行动 | microsoft.web/sites/slots/siteextensions/read | 获取 Web 应用槽站点扩展。 |
> | 行动 | microsoft.web/sites/slots/siteextensions/write | 更新 Web 应用槽站点扩展。 |
> | 行动 | Microsoft.Web/sites/slots/slotsdiffs/Action | 获取 Web 应用与槽之间的配置差异 |
> | 行动 | Microsoft.Web/sites/slots/slotsswap/Action | 交换 Web 应用部署槽 |
> | 行动 | microsoft.web/sites/slots/snapshots/read | 获取 Web 应用槽快照。 |
> | 行动 | Microsoft.Web/sites/slots/sourcecontrols/Delete | 删除 Web 应用槽的源代码管理配置设置 |
> | 行动 | Microsoft.Web/sites/slots/sourcecontrols/Read | 获取 Web 应用槽的源代码管理配置设置 |
> | 行动 | Microsoft.Web/sites/slots/sourcecontrols/Write | 更新 Web 应用槽的源代码管理配置设置 |
> | 行动 | Microsoft.Web/sites/slots/start/Action | 启动 Web 应用槽 |
> | 行动 | Microsoft.Web/sites/slots/stop/Action | 停止 Web 应用槽 |
> | 行动 | microsoft.web/sites/slots/sync/action | 同步 Web 应用槽。 |
> | 行动 | microsoft.web/sites/slots/triggeredwebjobs/delete | 删除 Web 应用槽触发的 Web 作业。 |
> | 行动 | microsoft.web/sites/slots/triggeredwebjobs/read | 获取 Web 应用槽触发的 Web 作业。 |
> | 行动 | microsoft.web/sites/slots/triggeredwebjobs/run/action | 运行 Web 应用槽触发的 Web 作业。 |
> | 行动 | microsoft.web/sites/slots/usages/read | 获取 Web 应用槽使用情况。 |
> | 行动 | microsoft.web/sites/slots/virtualnetworkconnections/delete | 删除 Web 应用槽虚拟网络连接。 |
> | 行动 | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | 更新 Web 应用槽虚拟网络连接网关。 |
> | 行动 | microsoft.web/sites/slots/virtualnetworkconnections/read | 获取 Web 应用槽虚拟网络连接。 |
> | 行动 | microsoft.web/sites/slots/virtualnetworkconnections/write | 更新 Web 应用槽虚拟网络连接。 |
> | 行动 | microsoft.web/sites/slots/webjobs/read | 获取 Web 应用槽 Web 作业。 |
> | 行动 | Microsoft.Web/sites/slots/Write | 创建新的或更新现有的 Web 应用槽 |
> | 行动 | Microsoft.Web/sites/slotsdiffs/Action | 获取 Web 应用与槽之间的配置差异 |
> | 行动 | Microsoft.Web/sites/slotsswap/Action | 交换 Web 应用部署槽 |
> | 行动 | microsoft.web/sites/snapshots/read | 获取 Web 应用快照。 |
> | 行动 | Microsoft.Web/sites/sourcecontrols/Delete | 删除 Web 应用的源代码管理配置设置 |
> | 行动 | Microsoft.Web/sites/sourcecontrols/Read | 获取 Web 应用的源代码管理配置设置 |
> | 行动 | Microsoft.Web/sites/sourcecontrols/Write | 更新 Web 应用的源代码管理配置设置 |
> | 行动 | Microsoft.Web/sites/start/Action | 启动 Web 应用 |
> | 行动 | Microsoft.Web/sites/stop/Action | 停止 Web 应用 |
> | 行动 | microsoft.web/sites/sync/action | 同步 Web 应用。 |
> | 行动 | microsoft.web/sites/syncfunctiontriggers/action | 同步函数触发器。 |
> | 行动 | microsoft.web/sites/triggeredwebjobs/delete | 删除 Web 应用触发的 Web 作业。 |
> | 行动 | microsoft.web/sites/triggeredwebjobs/history/read | 获取 Web 应用触发的 Web 作业历史记录。 |
> | 行动 | microsoft.web/sites/triggeredwebjobs/read | 获取 Web 应用触发的 Web 作业。 |
> | 行动 | microsoft.web/sites/triggeredwebjobs/run/action | 运行 Web 应用触发的 Web 作业。 |
> | 行动 | microsoft.web/sites/usages/read | 获取 Web 应用使用情况。 |
> | 行动 | microsoft.web/sites/virtualnetworkconnections/delete | 删除 Web 应用虚拟网络连接。 |
> | 行动 | microsoft.web/sites/virtualnetworkconnections/gateways/read | 获取 Web 应用虚拟网络连接网关。 |
> | 行动 | microsoft.web/sites/virtualnetworkconnections/gateways/write | 更新 Web 应用虚拟网络连接网关。 |
> | 行动 | microsoft.web/sites/virtualnetworkconnections/read | 获取 Web 应用虚拟网络连接。 |
> | 行动 | microsoft.web/sites/virtualnetworkconnections/write | 更新 Web 应用虚拟网络连接。 |
> | 行动 | microsoft.web/sites/webjobs/read | 获取 Web 应用 Web 作业。 |
> | 行动 | Microsoft.Web/sites/Write | 创建新的或更新现有的 Web 应用 |
> | 行动 | microsoft.web/skus/read | 获取 SKU。 |
> | 行动 | microsoft.web/sourcecontrols/read | 获取源代码管理。 |
> | 行动 | microsoft.web/sourcecontrols/write | 更新源代码管理。 |
> | 行动 | microsoft.web/unregister/action | 取消注册订阅的 Microsoft.Web 资源提供程序。 |
> | 行动 | microsoft.web/validate/action | 验证。 |
> | 行动 | microsoft.web/verifyhostingenvironmentvnet/action | 验证宿主环境 Vnet。 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | 操作类型 | Operation | 描述 |
> | --- | --- | --- |
> | 行动 | Microsoft.WorkloadMonitor/components/read | 获取资源的组件 |
> | 行动 | Microsoft.WorkloadMonitor/componentsSummary/read | 获取组件的摘要 |
> | 行动 | Microsoft.WorkloadMonitor/monitorInstances/read | 获取资源的监视器实例 |
> | 行动 | Microsoft.WorkloadMonitor/monitorInstancesSummary/read | 获取监视器实例的摘要 |
> | 行动 | Microsoft.WorkloadMonitor/monitors/read | 获取资源的监视器 |
> | 行动 | Microsoft.WorkloadMonitor/monitors/write | 配置资源的监视器 |
> | 行动 | Microsoft.WorkloadMonitor/notificationSettings/read | 获取资源的通知设置 |
> | 行动 | Microsoft.WorkloadMonitor/notificationSettings/write | 配置资源的通知设置 |
> | 行动 | Microsoft.WorkloadMonitor/operations/read | 获取支持的操作 |

## <a name="next-steps"></a>后续步骤

- [将资源提供程序与服务匹配](../azure-resource-manager/azure-services-resource-providers.md)
- [Azure 资源的自定义角色](custom-roles.md)
- [Azure 资源的内置角色](built-in-roles.md)
