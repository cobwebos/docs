---
title: Azure 中的内置角色 | Microsoft Docs
description: 介绍 Azure 中基于角色的访问控制 (RBAC) 的内置角色。 列出 Actions、NotActions、DataActions 和 NotDataActions。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 08/07/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: 5a373c397df09653395eea7996b19262aee75c7a
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39619043"
---
# <a name="built-in-roles-in-azure"></a>Azure 中的内置角色
[基于角色的访问控制 (RBAC)](overview.md) 拥有多个内置角色定义，可将其分配给用户、组和服务主体。 角色分配是控制对 Azure 资源的访问的方式。 如果内置角色不能满足组织的特定需求，则你可以创建自己的[自定义角色](custom-roles.md)。

内置角色始终在不断演变。 若要获取最新的角色定义，请使用 [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) 或 [az role definition list](/cli/azure/role/definition#az-role-definition-list)。

## <a name="built-in-role-descriptions"></a>内置角色说明
下表提供内置角色的简短说明。 单击角色名称，查看每个角色的 `Actions`、`NotActions`、`DataActions` 和 `NotDataActions` 列表。


| 内置角色 | Description |
| --- | --- |
| [所有者](#owner) | 允许管理所有功能，包括对资源的访问权限。 |
| [参与者](#contributor) | 允许管理所有功能（对资源的访问权限除外）。 |
| [读取者](#reader) | 允许查看所有内容，但不能进行任何更改。 |
| [AcrImageSigner](#acrimagesigner) | ACR 映像签名程序 |
| [AcrQuarantineReader](#acrquarantinereader) | ACR 隔离数据读取器 |
| [AcrQuarantineWriter](#acrquarantinewriter) | ACR 隔离数据编写器 |
| [API 管理服务参与者](#api-management-service-contributor) | 允许你管理 API 管理服务，但不允许你访问这些服务。 |
| [API 管理服务操作员角色](#api-management-service-operator-role) | 可以管理服务，但不可管理 API |
| [API 管理服务读者角色](#api-management-service-reader-role) | 对服务和 API 的只读访问权限 |
| [Application Insights 组件参与者](#application-insights-component-contributor) | 可管理 Application Insights 组件 |
| [Application Insights 快照调试器](#application-insights-snapshot-debugger) | 授予用户使用 Application Insights 快照调试器功能的权限 |
| [自动化作业操作员](#automation-job-operator) | 使用自动化 Runbook 创建和管理作业。 |
| [自动化操作员](#automation-operator) | 自动化操作员能够启动、停止、暂停和恢复作业 |
| [自动化 Runbook 操作员](#automation-runbook-operator) | 读取 Runbook 属性 - 以能够创建 runbook 的作业。 |
| [Azure Stack 注册所有者](#azure-stack-registration-owner) | 允许管理 Azure Stack 注册。 |
| [备份参与者](#backup-contributor) | 允许管理备份服务，但不允许创建保管库以及授予其他人访问权限 |
| [备份操作员](#backup-operator) | 允许管理备份服务，但删除备份、创建保管库以及授予其他人访问权限除外 |
| [备份读取器](#backup-reader) | 可以查看备份服务，但是不能进行更改 |
| [计费读者](#billing-reader) | 允许读取计费数据 |
| [BizTalk 参与者](#biztalk-contributor) | 允许管理 BizTalk 服务，但不允许访问这些服务。 |
| [CDN 终结点参与者](#cdn-endpoint-contributor) | 可以管理 CDN 终结点，但不能向其他用户授予访问权限。 |
| [CDN 终结点读者](#cdn-endpoint-reader) | 可以查看 CDN 终结点，但不能进行更改。 |
| [CDN 配置文件参与者](#cdn-profile-contributor) | 可以管理 CDN 配置文件及其终结点，但不能向其他用户授予访问权限。 |
| [CDN 配置文件读者](#cdn-profile-reader) | 可以查看 CDN 配置文件及其终结点，但不能进行更改。 |
| [经典网络参与者](#classic-network-contributor) | 允许管理经典网络，但不允许访问这些网络。 |
| [经典存储帐户参与者](#classic-storage-account-contributor) | 允许管理经典存储帐户，但不允许对其进行访问。 |
| [经典存储帐户密钥操作员服务角色](#classic-storage-account-key-operator-service-role) | 允许经典存储帐户密钥操作员在经典存储帐户上列出和再生成密钥 |
| [经典虚拟机参与者](#classic-virtual-machine-contributor) | 允许管理经典虚拟机，但不允许访问这些虚拟机及其连接到的虚拟网络或存储帐户。 |
| [ClearDB MySQL DB 参与者](#cleardb-mysql-db-contributor) | 允许管理 ClearDB MySQL 数据库，但不允许访问这些数据库。 |
| [Cosmos DB 帐户读者角色](#cosmos-db-account-reader-role) | 可以读取 Azure Cosmos DB 帐户数据。 请参阅 [Cosmos DB 帐户参与者](#documentdb-account-contributor)，了解如何管理 Azure Cosmos DB 帐户。 |
| [Data Box 参与者](#data-box-contributor) | 可让你管理 Data Box 服务下的所有内容，但不能向其他人授予访问权限。 |
| [Data Box 操作员](#data-box-operator) | 可让你管理 Data Box 服务，但不能创建订单或编辑订单详细信息，以及向其他人授予访问权限。 |
| [数据工厂参与者](#data-factory-contributor) | 允许管理数据工厂，但不允许访问它们。 |
| [Data Lake Analytics 开发人员](#data-lake-analytics-developer) | 允许提交、监视和管理自己的作业，但是不允许创建或删除 Data Lake Analytics 帐户。 |
| [数据清除程序](#data-purger) | 可清除分析数据 |
| [实验室用户](#devtest-labs-user) | 允许连接、启动、重启和关闭 Azure 开发测试实验室中的虚拟机。 |
| [DNS 区域参与者](#dns-zone-contributor) | 允许管理 Azure DNS 中的 DNS 区域和记录集，但不允许控制对其访问的人员。 |
| [DocumentDB 帐户参与者](#documentdb-account-contributor) | 可管理 Azure Cosmos DB 帐户。 Azure Cosmos DB 以前称为 DocumentDB。 |
| [智能系统帐户参与者](#intelligent-systems-account-contributor) | 允许管理智能系统帐户，但不允许访问这些帐户。 |
| [密钥保管库参与者](#key-vault-contributor) | 允许管理密钥保管库，但不允许对其进行访问。 |
| [实验室创建者](#lab-creator) | 允许在 Azure 实验室帐户下创建、管理、删除托管实验室。 |
| [Log Analytics 参与者](#log-analytics-contributor) | Log Analytics 参与者可以读取所有监视数据并编辑监视设置。 编辑监视设置包括向 VM 添加 VM 扩展、读取存储帐户密钥以便能够从 Azure 存储配置日志集合、创建和配置自动化帐户、添加解决方案以及配置所有 Azure 资源上的 Azure 诊断。 |
| [Log Analytics 读者](#log-analytics-reader) | Log Analytics 读者可以查看和搜索所有监视数据并查看监视设置，其中包括查看所有 Azure 资源上的 Azure 诊断的配置。 |
| [逻辑应用参与者](#logic-app-contributor) | 允许管理逻辑应用，但不允许对其进行访问。 |
| [逻辑应用操作员](#logic-app-operator) | 允许读取、启用和禁用逻辑应用。 |
| [托管应用程序操作员角色](#managed-application-operator-role) | 可让你在托管应用程序资源上读取和执行操作 |
| [托管的标识参与者](#managed-identity-contributor) | 创建、读取、更新和删除用户分配的标识 |
| [托管的标识操作员](#managed-identity-operator) | 读取和分配用户分配的标识 |
| [管理组参与者](#management-group-contributor) | 管理组参与者角色 |
| [管理组读取者](#management-group-reader) | 管理组读取者角色 |
| [监视参与者](#monitoring-contributor) | 可以读取所有监视数据和编辑监视设置。 另请参阅 [Azure Monitor 的角色、权限和安全入门](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles)。 |
| [监视查阅者](#monitoring-reader) | 可以读取所有监视数据（指标、日志等）。 另请参阅 [Azure Monitor 的角色、权限和安全入门](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles)。 |
| [网络参与者](#network-contributor) | 允许管理网络，但不允许访问这些网络。 |
| [New Relic APM 帐户参与者](#new-relic-apm-account-contributor) | 允许管理 New Relic 应用程序性能管理帐户和应用程序，但不允许访问它们。 |
| [读取器和数据访问](#reader-and-data-access) | 允许查看所有内容，但不允许删除或创建存储帐户或包含的资源。 它还允许使用存储帐户密钥对存储帐户中包含的所有数据进行读/写访问。 |
| [Redis 缓存参与者](#redis-cache-contributor) | 允许管理 Redis 缓存，但不允许访问这些缓存。 |
| [资源策略参与者（预览）](#resource-policy-contributor-preview) | （预览）通过 EA 回填的 用户，具有创建/修改资源策略、创建支持票证和读取资源/层次结构的权限。 |
| [计划程序作业集合参与者](#scheduler-job-collections-contributor) | 允许管理计划程序作业集合，但不允许访问这些集合。 |
| [搜索服务参与者](#search-service-contributor) | 允许管理搜索服务，但不允许访问这些服务。 |
| [安全管理员](#security-admin) | 仅在安全中心内：可以查看安全策略、查看安全状态、编辑安全策略、查看警报和建议、关闭警报和建议 |
| [安全经理](#security-manager) | 允许管理安全性组件、安全策略和虚拟机 |
| [安全读者](#security-reader) | 仅在安全中心内：可以查看建议和警报、查看安全策略、查看安全状态，但不能进行更改 |
| [Site Recovery 参与者](#site-recovery-contributor) | 允许管理除保管库创建和角色分配外的 Site Recovery 服务 |
| [Site Recovery 运算符](#site-recovery-operator) | 允许进行故障转移和故障回复，但不允许执行其他 Site Recovery 管理操作 |
| [Site Recovery 读取器](#site-recovery-reader) | 允许查看 Site Recovery 状态，但不允许执行其他管理操作 |
| [SQL DB 参与者](#sql-db-contributor) | 允许管理 SQL 数据库，但不允许访问这些数据库。 此外，不允许管理其安全相关的策略或其父 SQL 服务器。 |
| [SQL 安全管理器](#sql-security-manager) | 允许管理 SQL 服务器和数据库的安全相关策略，但不允许访问它们。 |
| [SQL Server 参与者](#sql-server-contributor) | 允许管理 SQL 服务器和数据库，但不允许访问它们及其安全相关的策略。 |
| [存储帐户参与者](#storage-account-contributor) | 允许管理存储帐户，但不允许对其进行访问。 |
| [存储帐户密钥操作员服务角色](#storage-account-key-operator-service-role) | 允许存储帐户密钥操作员在存储帐户上列出和重新生成密钥 |
| [存储 Blob 数据参与者（预览）](#storage-blob-data-contributor-preview) | 授予对 Azure 存储 blob 容器和数据的读取、写入和删除权限 |
| [存储 Blob 数据读者（预览）](#storage-blob-data-reader-preview) | 授予对 Azure 存储 blob 容器和数据的读取权限 |
| [存储队列数据参与者（预览）](#storage-queue-data-contributor-preview) | 授予对 Azure 存储队列和队列消息的读取、写入和删除权限 |
| [存储队列数据读者（预览）](#storage-queue-data-reader-preview) | 授予对 Azure 存储队列和队列消息的读取权限 |
| [支持请求参与者](#support-request-contributor) | 允许创建和管理支持请求 |
| [流量管理器参与者](#traffic-manager-contributor) | 允许管理流量管理器配置文件，但不允许控制谁可以访问它们。 |
| [用户访问管理员](#user-access-administrator) | 允许管理用户对 Azure 资源的访问权限。 |
| [虚拟机管理员登录](#virtual-machine-administrator-login) | 在门户中查看虚拟机并以管理员身份登录 |
| [虚拟机参与者](#virtual-machine-contributor) | 允许管理虚拟机，但不允许访问这些虚拟机及其连接到的虚拟网络或存储帐户。 |
| [虚拟机用户登录](#virtual-machine-user-login) | 在门户中查看虚拟机并以普通用户身份登录。 |
| [Web 计划参与者](#web-plan-contributor) | 允许管理网站的 Web 计划，但不允许访问这些计划。 |
| [网站参与者](#website-contributor) | 允许管理网站（而非 Web 计划），但不允许访问这些网站。 |


## <a name="owner"></a>所有者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理所有功能，包括对资源的访问权限。 |
> | **Id** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **操作** |  |
> | * | 创建和管理所有类型的资源 |

## <a name="contributor"></a>参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理所有功能（对资源的访问权限除外）。 |
> | **Id** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **操作** |  |
> | * | 创建和管理所有类型的资源 |
> | **不操作** |  |
> | Microsoft.Authorization/*/Delete | 无法删除角色和角色分配 |
> | Microsoft.Authorization/*/Write | 无法创建角色和角色分配 |
> | Microsoft.Authorization/elevateAccess/Action | 向调用方授予租户范围的“用户访问管理员”访问权限 |
> | Microsoft.Blueprint/blueprintAssignments/write |  |
> | Microsoft.Blueprint/blueprintAssignments/delete |  |

## <a name="reader"></a>读取器
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许查看所有内容，但不能进行任何更改。 |
> | **Id** | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | **操作** |  |
> | */read | 读取除密码外的所有类型的资源。 |

## <a name="acrimagesigner"></a>AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | ACR 映像签名程序 |
> | **Id** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **操作** |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |
> | Microsoft.ContainerRegistry/registries/*/write |  |

## <a name="acrquarantinereader"></a>AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | ACR 隔离数据读取器 |
> | **Id** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **操作** |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |

## <a name="acrquarantinewriter"></a>AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | ACR 隔离数据编写器 |
> | **Id** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **操作** |  |
> | Microsoft.ContainerRegistry/registries/*/write |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |

## <a name="api-management-service-contributor"></a>API 管理服务参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许你管理 API 管理服务，但不允许你访问这些服务。 |
> | **Id** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **操作** |  |
> | Microsoft.ApiManagement/service/* | 创建和管理 API 管理服务 |
> | Microsoft.Authorization/*/read | 读取授权 |
> | Microsoft.Insights/alertRules/* | 创建和管理警报规则 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="api-management-service-operator-role"></a>API 管理服务操作员角色
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 可以管理服务，但不可管理 API |
> | **Id** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **操作** |  |
> | Microsoft.ApiManagement/service/*/read | 读取 API 管理服务实例 |
> | Microsoft.ApiManagement/service/backup/action | 将 API 管理服务备份到用户提供的存储帐户中的指定容器 |
> | Microsoft.ApiManagement/service/delete | 删除 API 管理服务实例 |
> | Microsoft.ApiManagement/service/managedeployments/action | 更改 API 管理服务的 SKU/单位，以及添加/删除其区域部署 |
> | Microsoft.ApiManagement/service/read | 读取 API 管理服务实例的元数据 |
> | Microsoft.ApiManagement/service/restore/action | 从用户提供的存储帐户中的指定容器还原 API 管理服务 |
> | Microsoft.ApiManagement/service/updatecertificate/action | 上传 API 管理服务的 SSL 证书 |
> | Microsoft.ApiManagement/service/updatehostname/action | 设置、更新或删除 API 管理服务的自定义域名 |
> | Microsoft.ApiManagement/service/write | 创建 API 管理服务的新实例 |
> | Microsoft.Authorization/*/read | 读取授权 |
> | Microsoft.Insights/alertRules/* | 创建和管理警报规则 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |
> | **不操作** |  |
> | Microsoft.ApiManagement/service/users/keys/read | 获取用户密钥的列表 |

## <a name="api-management-service-reader-role"></a>API 管理服务读者角色
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 对服务和 API 的只读访问权限 |
> | **Id** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **操作** |  |
> | Microsoft.ApiManagement/service/*/read | 读取 API 管理服务实例 |
> | Microsoft.ApiManagement/service/read | 读取 API 管理服务实例的元数据 |
> | Microsoft.Authorization/*/read | 读取授权 |
> | Microsoft.Insights/alertRules/* | 创建和管理警报规则 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |
> | **不操作** |  |
> | Microsoft.ApiManagement/service/users/keys/read | 获取用户密钥的列表 |

## <a name="application-insights-component-contributor"></a>Application Insights 组件参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 可管理 Application Insights 组件 |
> | **Id** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Insights/alertRules/* | 创建和管理警报规则 |
> | Microsoft.Insights/components/* | 创建和管理 Insights 组件 |
> | Microsoft.Insights/webtests/* | 创建和管理 Web 测试 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="application-insights-snapshot-debugger"></a>Application Insights 快照调试器
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 授予用户使用 Application Insights 快照调试器功能的权限 |
> | **Id** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Insights/alertRules/* | 创建和管理 Insights 警报规则 |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="automation-job-operator"></a>自动化作业操作员
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 使用自动化 Runbook 创建和管理作业。 |
> | **Id** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Automation/automationAccounts/jobs/read | 获取 Azure 自动化作业 |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | 恢复 Azure 自动化作业 |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | 停止 Azure 自动化作业 |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | 读取混合 Runbook 辅助角色资源 |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | 获取 Azure 自动化作业流 |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | 暂停 Azure 自动化作业 |
> | Microsoft.Automation/automationAccounts/jobs/write | 创建 Azure 自动化作业 |
> | Microsoft.Insights/alertRules/* | 创建和管理 Insights 警报规则 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="automation-operator"></a>自动化运算符
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 自动化操作员能够启动、停止、暂停和恢复作业 |
> | **Id** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | 读取混合 Runbook 辅助角色资源 |
> | Microsoft.Automation/automationAccounts/jobs/read | 获取 Azure 自动化作业 |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | 恢复 Azure 自动化作业 |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | 停止 Azure 自动化作业 |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | 获取 Azure 自动化作业流 |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | 暂停 Azure 自动化作业 |
> | Microsoft.Automation/automationAccounts/jobs/write | 创建 Azure 自动化作业 |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | 获取 Azure 自动化作业计划 |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | 创建 Azure 自动化作业计划 |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | 获取链接到自动化帐户的工作区 |
> | Microsoft.Automation/automationAccounts/read | 获取 Azure 自动化帐户。 |
> | Microsoft.Automation/automationAccounts/runbooks/read | 获取 Azure 自动化 Runbook |
> | Microsoft.Automation/automationAccounts/schedules/read | 获取 Azure 自动化计划资产 |
> | Microsoft.Automation/automationAccounts/schedules/write | 创建或更新 Azure 自动化计划资产 |
> | Microsoft.Insights/alertRules/* | 创建和管理 Insights 警报规则 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Automation/automationAccounts/jobs/output/read | 获取作业的输出 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="automation-runbook-operator"></a>自动化 Runbook 操作员
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 读取 Runbook 属性 - 以能够创建 runbook 的作业。 |
> | **Id** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Automation/automationAccounts/runbooks/read | 获取 Azure 自动化 Runbook |
> | Microsoft.Insights/alertRules/* | 创建和管理 Insights 警报规则 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="azure-stack-registration-owner"></a>Azure Stack 注册所有者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理 Azure Stack 注册。 |
> | **Id** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **操作** |  |
> | Microsoft.AzureStack/registrations/products/listDetails/action | 检索 Azure Stack 市场产品的扩展详细信息 |
> | Microsoft.AzureStack/registrations/products/read | 获取 Azure Stack 市场产品的属性 |
> | Microsoft.AzureStack/registrations/read | 获取 Azure Stack 注册的属性 |

## <a name="backup-contributor"></a>备份参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理备份服务，但不允许创建保管库以及授予其他人访问权限 |
> | **Id** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Network/virtualNetworks/read | 获取虚拟网络定义 |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp 是服务使用的内部操作 |
> | Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | 管理备份管理操作的结果 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | 创建和管理恢复服务保管库备份结构中的备份容器 |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | 创建和管理备份作业 |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | 导出作业 |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | 返回导出作业操作的结果。 |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | 创建和管理与备份管理相关的元数据 |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | 创建和管理备份管理操作的结果 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | 创建和管理备份策略 |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | 创建和管理可备份的项 |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | 创建和管理已备份的项 |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | 创建和管理包含备份项的容器 |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | 返回恢复服务的受保护项和受保护服务器的摘要。 |
> | Microsoft.RecoveryServices/Vaults/certificates/* | 创建和管理与恢复服务保管库中的备份相关的证书 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | 创建和管理与保管库相关的扩展信息 |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | 获取恢复服务保管库的警报。 |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | “获取保管库”操作获取表示“vault”类型的 Azure 资源的对象 |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/* | 管理用于获取新创建的容器的发现操作 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | 创建和管理已注册的标识 |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
> | Microsoft.RecoveryServices/Vaults/usages/* | 创建和管理恢复服务保管库的使用情况 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Storage/storageAccounts/read | 返回存储帐户的列表，或获取指定存储帐户的属性。 |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="backup-operator"></a>备份操作员
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理备份服务，但删除备份、创建保管库以及授予其他人访问权限除外 |
> | **Id** | 00c29273-979b-4161-815c-10b084fb9324 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Network/virtualNetworks/read | 获取虚拟网络定义 |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp 是服务使用的内部操作 |
> | Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | 返回操作状态 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | 获取对保护容器执行的操作的结果。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | 对受保护的项执行备份。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 获取对受保护项执行的操作的结果。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 返回对受保护项执行的操作的状态。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | 返回受保护项的对象详细信息 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | 预配受保护项的即时项恢复 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 获取受保护项的恢复点。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | 还原受保护项的恢复点。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | 吊销受保护项的即时项恢复 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | 创建备份受保护项 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | 返回所有已注册的容器 |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | 创建和管理备份作业 |
> | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | 取消作业 |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | 返回作业操作的结果。 |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | 返回所有作业对象 |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | 导出作业 |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | 返回导出作业操作的结果。 |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | 返回恢复服务保管库的备份管理元数据。 |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | 创建和管理备份管理操作的结果 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | 获取策略操作的结果。 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationStatus/read |  |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | 返回所有保护策略 |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | 创建和管理可备份的项 |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | 返回所有可保护项的列表。 |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | 返回所有受保护项的列表。 |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | 返回属于订阅的所有容器 |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | 返回恢复服务的受保护项和受保护服务器的摘要。 |
> | Microsoft.RecoveryServices/Vaults/certificates/write | “更新资源证书”操作更新资源/保管库凭据证书。 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | “获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象的扩展信息 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | “获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象的扩展信息 |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | 获取恢复服务保管库的警报。 |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | “获取保管库”操作获取表示“vault”类型的 Azure 资源的对象 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | 刷新容器列表 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | “获取操作结果”操作可用于获取异步提交的操作的操作状态和结果 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | “获取容器”操作可用于获取针对资源注册的容器。 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | “注册服务容器”操作可用于向恢复服务注册容器。 |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | 返回恢复服务保管库的使用情况详细信息。 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Storage/storageAccounts/read | 返回存储帐户的列表，或获取指定存储帐户的属性。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="backup-reader"></a>备份读取器
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 可以查看备份服务，但是不能进行更改 |
> | **Id** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | 返回操作状态 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | 获取对保护容器执行的操作的结果。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | 获取对受保护项执行的操作的结果。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | 返回对受保护项执行的操作的状态。 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | 返回受保护项的对象详细信息 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | 返回所有已注册的容器 |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | 返回作业操作的结果。 |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | 返回所有作业对象 |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | 导出作业 |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | 返回恢复服务保管库的备份管理元数据。 |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | 返回恢复服务保管库的备份操作结果。 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | 获取策略操作的结果。 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | 返回所有保护策略 |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | 返回所有受保护项的列表。 |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | 返回属于订阅的所有容器 |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | 返回恢复服务的受保护项和受保护服务器的摘要。 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | “获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象的扩展信息 |
> | Microsoft.RecoveryServices/Vaults/read | “获取保管库”操作获取表示“vault”类型的 Azure 资源的对象 |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | “获取操作结果”操作可用于获取异步提交的操作的操作状态和结果 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | “获取容器”操作可用于获取针对资源注册的容器。 |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp 是服务使用的内部操作 |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | 获取恢复服务保管库的警报。 |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | 获取受保护项的恢复点。 |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | 返回导出作业操作的结果。 |
> | Microsoft.RecoveryServices/Vaults/usages/read | 返回恢复服务保管库的使用情况详细信息。 |

## <a name="billing-reader"></a>计费读者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许读取计费数据 |
> | **Id** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Billing/*/read | 读取计费信息 |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Management/managementGroups/read | 列出已通过身份验证的用户的管理组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="biztalk-contributor"></a>BizTalk 参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理 BizTalk 服务，但不允许访问这些服务。 |
> | **Id** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.BizTalkServices/BizTalk/* | 创建和管理 BizTalk 服务 |
> | Microsoft.Insights/alertRules/* | 创建和管理警报规则 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="cdn-endpoint-contributor"></a>CDN 终结点参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 可以管理 CDN 终结点，但不能向其他用户授予访问权限。 |
> | **Id** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | 创建和管理 Insights 警报规则 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="cdn-endpoint-reader"></a>CDN 终结点读者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 可以查看 CDN 终结点，但不能进行更改。 |
> | **Id** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | 创建和管理 Insights 警报规则 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="cdn-profile-contributor"></a>CDN 配置文件参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 可以管理 CDN 配置文件及其终结点，但不能向其他用户授予访问权限。 |
> | **Id** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | 创建和管理 Insights 警报规则 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="cdn-profile-reader"></a>CDN 配置文件读者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 可以查看 CDN 配置文件及其终结点，但不能进行更改。 |
> | **Id** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | 创建和管理 Insights 警报规则 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="classic-network-contributor"></a>经典网络参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理经典网络，但不允许访问这些网络。 |
> | **Id** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取授权 |
> | Microsoft.ClassicNetwork/* | 创建和管理经典网络 |
> | Microsoft.Insights/alertRules/* | 创建和管理 Insights 警报规则 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="classic-storage-account-contributor"></a>经典存储帐户参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理经典存储帐户，但不允许对其进行访问。 |
> | **Id** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取授权 |
> | Microsoft.ClassicStorage/storageAccounts/* | 创建和管理存储帐户 |
> | Microsoft.Insights/alertRules/* | 创建和管理 Insights 警报规则 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="classic-storage-account-key-operator-service-role"></a>经典存储帐户密钥操作员服务角色
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许经典存储帐户密钥操作员在经典存储帐户上列出和再生成密钥 |
> | **Id** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **操作** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | 列出存储帐户的访问密钥。 |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | 再生成存储帐户的现有访问密钥。 |

## <a name="classic-virtual-machine-contributor"></a>经典虚拟机参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理经典虚拟机，但不允许访问这些虚拟机及其连接到的虚拟网络或存储帐户。 |
> | **Id** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取授权 |
> | Microsoft.ClassicCompute/domainNames/* | 创建和管理经典计算域名 |
> | Microsoft.ClassicCompute/virtualMachines/* | 创建和管理虚拟机 |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | 链接保留 IP |
> | Microsoft.ClassicNetwork/reservedIps/read | 获取保留 IP |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | 加入虚拟网络。 |
> | Microsoft.ClassicNetwork/virtualNetworks/read | 获取虚拟网络。 |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | 返回存储帐户磁盘。 |
> | Microsoft.ClassicStorage/storageAccounts/images/read | 返回存储帐户映像。 （已弃用。 请使用“Microsoft.ClassicStorage/storageAccounts/vmImages”） |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | 列出存储帐户的访问密钥。 |
> | Microsoft.ClassicStorage/storageAccounts/read | 返回包含给定帐户的存储帐户。 |
> | Microsoft.Insights/alertRules/* | 创建和管理 Insights 警报规则 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="cleardb-mysql-db-contributor"></a>ClearDB MySQL DB 参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理 ClearDB MySQL 数据库，但不允许访问这些数据库。 |
> | **Id** | 9106cda0-8a86-4e81-b686-29a22c54effe |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Insights/alertRules/* | 创建和管理警报规则 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |
> | successbricks.cleardb/databases/* | 创建和管理 ClearDB MySQL 数据库 |

## <a name="cosmos-db-account-reader-role"></a>Cosmos DB 帐户读者角色
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 可以读取 Azure Cosmos DB 帐户数据。 请参阅 [Cosmos DB 帐户参与者](#documentdb-account-contributor)，了解如何管理 Azure Cosmos DB 帐户。 |
> | **Id** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配，可以读取授予每个用户的权限 |
> | Microsoft.DocumentDB/*/read | 读取任何集合 |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | 读取数据库帐户只读密钥。 |
> | Microsoft.Insights/MetricDefinitions/read | 读取指标定义 |
> | Microsoft.Insights/Metrics/read | 添加指标 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="data-box-contributor"></a>Data Box 参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 可让你管理 Data Box 服务下的所有内容，但不能向其他人授予访问权限。 |
> | **Id** | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |
> | Microsoft.Databox/* |  |

## <a name="data-box-operator"></a>Data Box 操作员
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 可让你管理 Data Box 服务，但不能创建订单或编辑订单详细信息，以及向其他人授予访问权限。 |
> | **Id** | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Support/* | 创建和管理支持票证 |
> | Microsoft.Databox/jobs/listsecrets/action | 列出与订单相关的未加密密码。 |

## <a name="data-factory-contributor"></a>数据工厂参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理数据工厂，但不允许访问它们。 |
> | **Id** | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.DataFactory/dataFactories/* | 创建和管理数据工厂，以及它们包含的子资源。 |
> | Microsoft.DataFactory/factories/* | 创建和管理数据工厂，以及它们包含的子资源。 |
> | Microsoft.Insights/alertRules/* | 创建和管理警报规则 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="data-lake-analytics-developer"></a>Data Lake Analytics 开发人员
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许提交、监视和管理自己的作业，但是不允许创建或删除 Data Lake Analytics 帐户。 |
> | **Id** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | 创建和管理 Insights 警报规则 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |
> | **不操作** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | 删除 DataLakeAnalytics 帐户。 |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | 授权取消由其他用户提交的作业。 |
> | Microsoft.DataLakeAnalytics/accounts/Write | 创建或更新 DataLakeAnalytics 帐户。 |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | 获取或更新 DataLakeAnalytics 帐户的链接 DataLakeStore 帐户。 |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | 从 DataLakeAnalytics 帐户取消链接 DataLakeStore 帐户。 |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | 创建或更新 DataLakeAnalytics 帐户的链接存储帐户。 |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | 从 DataLakeAnalytics 帐户取消链接存储帐户。 |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | 创建或更新防火墙规则。 |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | 删除防火墙规则。 |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | 创建或更新计算策略。 |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | 删除计算策略。 |

## <a name="data-purger"></a>数据清除程序
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 可清除分析数据 |
> | **Id** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **操作** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | 从 Application Insights 清除数据 |
> | Microsoft.OperationalInsights/workspaces/*/read |  |
> | Microsoft.OperationalInsights/workspaces/purge/action | 从工作区中删除指定数据 |

## <a name="devtest-labs-user"></a>DevTest 实验室用户
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许连接、启动、重启和关闭 Azure 开发测试实验室中的虚拟机。 |
> | **Id** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Compute/availabilitySets/read | 获取可用性集的属性 |
> | Microsoft.Compute/virtualMachines/*/read | 读取虚拟机属性（VM 大小、运行时状态、VM 扩展等） |
> | Microsoft.Compute/virtualMachines/deallocate/action | 关闭虚拟机并释放计算资源 |
> | Microsoft.Compute/virtualMachines/read | 获取虚拟机的属性 |
> | Microsoft.Compute/virtualMachines/restart/action | 重新启动虚拟机 |
> | Microsoft.Compute/virtualMachines/start/action | 启动虚拟机 |
> | Microsoft.DevTestLab/*/read | 读取实验室属性 |
> | Microsoft.DevTestLab/labs/createEnvironment/action | 在实验室中创建虚拟机。 |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | 在实验室中认领随机可认领虚拟机。 |
> | Microsoft.DevTestLab/labs/formulas/delete | 删除公式。 |
> | Microsoft.DevTestLab/labs/formulas/read | 读取公式。 |
> | Microsoft.DevTestLab/labs/formulas/write | 添加或修改公式。 |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | 评估实验室策略。 |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | 获得现有虚拟机的所有权 |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | 加入负载均衡器后端地址池 |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | 加入负载均衡器入站 NAT 规则 |
> | Microsoft.Network/networkInterfaces/*/read | 读取网络接口（例如，此网络接口所属的所有负载均衡器）的属性 |
> | Microsoft.Network/networkInterfaces/join/action | 将虚拟机加入到网络接口 |
> | Microsoft.Network/networkInterfaces/read | 获取网络接口定义。  |
> | Microsoft.Network/networkInterfaces/write | 创建网络接口，或更新现有的网络接口。  |
> | Microsoft.Network/publicIPAddresses/*/read | 读取公共 IP 地址的属性 |
> | Microsoft.Network/publicIPAddresses/join/action | 联接公共 IP 地址 |
> | Microsoft.Network/publicIPAddresses/read | 获取公共 IP 地址定义。 |
> | Microsoft.Network/virtualNetworks/subnets/join/action | 加入虚拟网络 |
> | Microsoft.Resources/deployments/operations/read | 获取或列出部署操作。 |
> | Microsoft.Resources/deployments/read | 获取或列出部署。 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Storage/storageAccounts/listKeys/action | 返回指定存储帐户的访问密钥。 |
> | **不操作** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | 列出可将虚拟机更新到的大小 |

## <a name="dns-zone-contributor"></a>DNS 区域参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理 Azure DNS 中的 DNS 区域和记录集，但不允许控制对其访问的人员。 |
> | **Id** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Insights/alertRules/* | 创建和管理警报规则 |
> | Microsoft.Network/dnsZones/* | 创建和管理 DNS 区域和记录 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="documentdb-account-contributor"></a>DocumentDB 帐户参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 可管理 Azure Cosmos DB 帐户。 Azure Cosmos DB 以前称为 DocumentDB。 |
> | **Id** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.DocumentDb/databaseAccounts/* | 创建并管理 Azure Cosmos DB 帐户 |
> | Microsoft.Insights/alertRules/* | 创建和管理警报规则 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="intelligent-systems-account-contributor"></a>Intelligent Systems 帐户参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理智能系统帐户，但不允许访问这些帐户。 |
> | **Id** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Insights/alertRules/* | 创建和管理警报规则 |
> | Microsoft.IntelligentSystems/accounts/* | 创建和管理智能系统帐户 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="key-vault-contributor"></a>密钥保管库参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理密钥保管库，但不允许对其进行访问。 |
> | **Id** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Insights/alertRules/* | 创建和管理 Insights 警报规则 |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |
> | **不操作** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | 清除软删除的 Key Vault |
> | Microsoft.KeyVault/hsmPools/* |  |

## <a name="lab-creator"></a>实验室创建者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许在 Azure 实验室帐户下创建、管理、删除托管实验室。 |
> | **Id** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | 在实验室帐户中创建实验室。 |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action | 获取实验室帐户下配置的每个大小类别的区域可用性信息 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="log-analytics-contributor"></a>Log Analytics 参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | Log Analytics 参与者可以读取所有监视数据并编辑监视设置。 编辑监视设置包括向 VM 添加 VM 扩展、读取存储帐户密钥以便能够从 Azure 存储配置日志集合、创建和配置自动化帐户、添加解决方案以及配置所有 Azure 资源上的 Azure 诊断。 |
> | **Id** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **操作** |  |
> | */read | 读取除密码外的所有类型的资源。 |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | 列出存储帐户的访问密钥。 |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | 创建和管理 Insights 警报规则 |
> | Microsoft.Insights/diagnosticSettings/* | 创建、更新或读取 Analysis Server 的诊断设置 |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | 返回指定存储帐户的访问密钥。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="log-analytics-reader"></a>Log Analytics 读者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | Log Analytics 读者可以查看和搜索所有监视数据并查看监视设置，其中包括查看所有 Azure 资源上的 Azure 诊断的配置。 |
> | **Id** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **操作** |  |
> | */read | 读取除密码外的所有类型的资源。 |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | 使用新引擎进行搜索。 |
> | Microsoft.OperationalInsights/workspaces/search/action | 执行搜索查询 |
> | Microsoft.Support/* | 创建和管理支持票证 |
> | **不操作** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | 检索工作区的共享密钥。 这些密钥用于将 Microsoft Operational Insights 代理连接到工作区。 |

## <a name="logic-app-contributor"></a>逻辑应用参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理逻辑应用，但不允许对其进行访问。 |
> | **Id** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | 列出存储帐户的访问密钥。 |
> | Microsoft.ClassicStorage/storageAccounts/read | 返回包含给定帐户的存储帐户。 |
> | Microsoft.Insights/alertRules/* | 创建和管理 Insights 警报规则 |
> | Microsoft.Insights/diagnosticSettings/* | 创建、更新或读取 Analysis Server 的诊断设置 |
> | Microsoft.Insights/logdefinitions/* | 此权限对于需要通过门户访问活动日志的用户是必需的。 列出活动日志中的日志类别。 |
> | Microsoft.Insights/metricDefinitions/* | 读取指标定义（资源的可用指标类型的列表）。 |
> | Microsoft.Logic/* | 管理逻辑应用资源。 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/operationresults/read | 获取订阅操作结果。 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Storage/storageAccounts/listkeys/action | 返回指定存储帐户的访问密钥。 |
> | Microsoft.Storage/storageAccounts/read | 返回存储帐户的列表，或获取指定存储帐户的属性。 |
> | Microsoft.Support/* | 创建和管理支持票证 |
> | Microsoft.Web/connectionGateways/* | 创建和管理连接网关。 |
> | Microsoft.Web/connections/* | 创建和管理连接。 |
> | Microsoft.Web/customApis/* | 创建和管理自定义 API。 |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | 获取应用服务计划的属性 |
> | Microsoft.Web/sites/functions/listSecrets/action | 列出机密 Web 应用函数。 |

## <a name="logic-app-operator"></a>逻辑应用运算符
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许读取、启用和禁用逻辑应用。 |
> | **Id** | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Insights/alertRules/*/read | 读取 Insights 警报规则 |
> | Microsoft.Insights/diagnosticSettings/*/read | 获取逻辑应用的诊断设置 |
> | Microsoft.Insights/metricDefinitions/*/read | 获取逻辑应用的可用指标。 |
> | Microsoft.Logic/*/read | 读取逻辑应用资源。 |
> | Microsoft.Logic/workflows/disable/action | 禁用工作流。 |
> | Microsoft.Logic/workflows/enable/action | 启用工作流。 |
> | Microsoft.Logic/workflows/validate/action | 验证工作流。 |
> | Microsoft.Resources/deployments/operations/read | 获取或列出部署操作。 |
> | Microsoft.Resources/subscriptions/operationresults/read | 获取订阅操作结果。 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |
> | Microsoft.Web/connectionGateways/*/read | 读取连接网关。 |
> | Microsoft.Web/connections/*/read | 读取连接。 |
> | Microsoft.Web/customApis/*/read | 读取自定义 API。 |
> | Microsoft.Web/serverFarms/read | 获取应用服务计划的属性 |

## <a name="managed-application-operator-role"></a>托管应用程序操作员角色
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 可让你在托管应用程序资源上读取和执行操作 |
> | **Id** | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | **操作** |  |
> | Microsoft.Solutions/applications/read | 检索应用程序列表。 |

## <a name="managed-identity-contributor"></a>托管的标识参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 创建、读取、更新和删除用户分配的标识 |
> | **Id** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | **操作** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/write |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/delete |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Insights/alertRules/* | 创建和管理 Insights 警报规则 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="managed-identity-operator"></a>托管的标识操作员
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 读取和分配用户分配的标识 |
> | **Id** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **操作** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Insights/alertRules/* | 创建和管理 Insights 警报规则 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="management-group-contributor"></a>管理组参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 管理组参与者角色 |
> | **Id** | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | **操作** |  |
> | Microsoft.Management/managementGroups/delete | 删除管理组。 |
> | Microsoft.Management/managementGroups/read | 列出已通过身份验证的用户的管理组。 |
> | Microsoft.Management/managementGroups/subscriptions/delete | 从管理组取消关联订阅。 |
> | Microsoft.Management/managementGroups/subscriptions/write | 将现有订阅与管理组关联。 |
> | Microsoft.Management/managementGroups/write | 创建或更新管理组。 |

## <a name="management-group-reader"></a>管理组读取者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 管理组读取者角色 |
> | **Id** | ac63b705-f282-497d-ac71-919bf39d939d |
> | **操作** |  |
> | Microsoft.Management/managementGroups/read | 列出已通过身份验证的用户的管理组。 |

## <a name="monitoring-contributor"></a>监视参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 可以读取所有监视数据和编辑监视设置。 另请参阅 [Azure Monitor 的角色、权限和安全入门](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles)。 |
> | **Id** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **操作** |  |
> | */read | 读取除密码外的所有类型的资源。 |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/AlertRules/* | 读取/写入/删除警报规则。 |
> | Microsoft.Insights/components/* | 读取/写入/删除 Application Insights 组件。 |
> | Microsoft.Insights/DiagnosticSettings/* | 读取/写入/删除诊断设置。 |
> | Microsoft.Insights/eventtypes/* | 列出订阅中的活动日志事件（管理事件）。 此权限适用于对活动日志的编程和门户访问。 |
> | Microsoft.Insights/LogDefinitions/* | 此权限对于需要通过门户访问活动日志的用户是必需的。 列出活动日志中的日志类别。 |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | 读取指标定义（资源的可用指标类型的列表）。 |
> | Microsoft.Insights/Metrics/* | 读取资源的指标。 |
> | Microsoft.Insights/Register/Action | 注册 Microsoft Insights 提供程序 |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | 读取/写入/删除 Application Insights Web 测试。 |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | 读取/写入/删除 Log Analytics 解决方案包。 |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | 读取/写入/删除 Log Analytics 保存的搜索。 |
> | Microsoft.OperationalInsights/workspaces/search/action | 执行搜索查询 |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | 检索工作区的共享密钥。 这些密钥用于将 Microsoft Operational Insights 代理连接到工作区。 |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | 读取/写入/删除 Log Analytics 存储深入了解配置。 |
> | Microsoft.Support/* | 创建和管理支持票证 |
> | Microsoft.WorkloadMonitor/workloads/* |  |
> | Microsoft.WorkloadMonitor/workloadInsights/* |  |

## <a name="monitoring-reader"></a>监视查阅者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 可以读取所有监视数据（指标、日志等）。 另请参阅 [Azure Monitor 的角色、权限和安全入门](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles)。 |
> | **Id** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **操作** |  |
> | */read | 读取除密码外的所有类型的资源。 |
> | Microsoft.OperationalInsights/workspaces/search/action | 执行搜索查询 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="network-contributor"></a>网络参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理网络，但不允许访问这些网络。 |
> | **Id** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Insights/alertRules/* | 创建和管理警报规则 |
> | Microsoft.Network/* | 创建并管理网络 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="new-relic-apm-account-contributor"></a>New elic APM 帐户参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理 New Relic 应用程序性能管理帐户和应用程序，但不允许访问它们。 |
> | **Id** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Insights/alertRules/* | 创建和管理 Insights 警报规则 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |
> | NewRelic.APM/accounts/* |  |

## <a name="reader-and-data-access"></a>读取器和数据访问
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许查看所有内容，但不允许删除或创建存储帐户或包含的资源。 它还允许使用存储帐户密钥对存储帐户中包含的所有数据进行读/写访问。 |
> | **Id** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **操作** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | 返回指定存储帐户的访问密钥。 |
> | Microsoft.Storage/storageAccounts/read | 返回存储帐户的列表，或获取指定存储帐户的属性。 |

## <a name="redis-cache-contributor"></a>Redis Cache 参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理 Redis 缓存，但不允许访问这些缓存。 |
> | **Id** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Cache/redis/* | 创建和管理 Redis 缓存 |
> | Microsoft.Insights/alertRules/* | 创建和管理警报规则 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="resource-policy-contributor-preview"></a>资源策略参与者（预览）
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | （预览）通过 EA 回填的 用户，具有创建/修改资源策略、创建支持票证和读取资源/层次结构的权限。 |
> | **Id** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **操作** |  |
> | */read | 读取除密码外的所有类型的资源。 |
> | Microsoft.Authorization/policyassignments/* | 创建和管理策略分配 |
> | Microsoft.Authorization/policydefinitions/* | 创建和管理策略定义 |
> | Microsoft.Authorization/policysetdefinitions/* | 创建和管理策略集 |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="scheduler-job-collections-contributor"></a>计划程序作业集合参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理计划程序作业集合，但不允许访问这些集合。 |
> | **Id** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Insights/alertRules/* | 创建和管理警报规则 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Scheduler/jobcollections/* | 创建和管理作业集合 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="search-service-contributor"></a>搜索服务参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理搜索服务，但不允许访问这些服务。 |
> | **Id** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Insights/alertRules/* | 创建和管理警报规则 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Search/searchServices/* | 创建和管理搜索服务 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="security-admin"></a>安全管理员
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 仅在安全中心内：可以查看安全策略、查看安全状态、编辑安全策略、查看警报和建议、关闭警报和建议 |
> | **Id** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Authorization/policyAssignments/* | 创建和管理策略分配 |
> | Microsoft.Authorization/policyDefinitions/* | 创建和管理策略定义 |
> | Microsoft.Authorization/policySetDefinitions/* | 创建和管理策略集 |
> | Microsoft.Insights/alertRules/* | 创建和管理警报规则 |
> | Microsoft.Management/managementGroups/read | 列出已通过身份验证的用户的管理组。 |
> | Microsoft.operationalInsights/workspaces/*/read | 查看 Log Analytics 数据 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Security/*/read | 读取安全组件和策略 |
> | Microsoft.Security/locations/alerts/activate/action | 激活安全警报 |
> | Microsoft.Security/locations/alerts/dismiss/action | 消除安全警报 |
> | Microsoft.Security/locations/tasks/activate/action | 激活安全建议 |
> | Microsoft.Security/locations/tasks/dismiss/action | 关闭安全建议 |
> | Microsoft.Security/policies/write | 更新安全策略 |
> | Microsoft.Security/securityContacts/write | 更新安全联系信息 |
> | Microsoft.Security/securityContacts/delete | 删除安全联系信息 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="security-manager"></a>安全管理器
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理安全性组件、安全策略和虚拟机 |
> | **Id** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.ClassicCompute/*/read | 读取经典虚拟机的配置信息 |
> | Microsoft.ClassicCompute/virtualMachines/*/write | 写入经典虚拟机的配置 |
> | Microsoft.ClassicNetwork/*/read | 读取有关经典网络的配置信息 |
> | Microsoft.Insights/alertRules/* | 创建和管理警报规则 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Security/* | 创建和管理安全组件和策略 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="security-reader"></a>安全读取者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 仅在安全中心内：可以查看建议和警报、查看安全策略、查看安全状态，但不能进行更改 |
> | **Id** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Insights/alertRules/* | 创建和管理警报规则 |
> | Microsoft.operationalInsights/workspaces/*/read | 查看 Log Analytics 数据 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Security/*/read | 读取安全组件和策略 |
> | Microsoft.Support/* | 创建和管理支持票证 |
> | Microsoft.Management/managementGroups/read | 列出已通过身份验证的用户的管理组。 |

## <a name="site-recovery-contributor"></a>Site Recovery 参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理除保管库创建和角色分配外的 Site Recovery 服务 |
> | **Id** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Insights/alertRules/* | 创建和管理警报规则 |
> | Microsoft.Network/virtualNetworks/read | 获取虚拟网络定义 |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp 是服务使用的内部操作 |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp 是服务使用的内部操作 |
> | Microsoft.RecoveryServices/Vaults/certificates/write | “更新资源证书”操作更新资源/保管库凭据证书。 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | 创建和管理与保管库相关的扩展信息 |
> | Microsoft.RecoveryServices/Vaults/read | “获取保管库”操作获取表示“vault”类型的 Azure 资源的对象 |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | 创建和管理已注册的标识 |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | 创建或更新复制警报设置 |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | 读取任何事件 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | 创建和管理复制结构 |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | 创建和管理复制作业 |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | 创建和管理复制策略 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | 创建和管理恢复计划 |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | 创建和管理恢复服务保管库的存储配置 |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | 返回恢复服务保管库的令牌信息。 |
> | Microsoft.RecoveryServices/Vaults/usages/read | 返回恢复服务保管库的使用情况详细信息。 |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | “保管库令牌”操作可用于获取保管库级后端操作的保管库令牌。 |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | 读取恢复服务保管库的警报 |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Storage/storageAccounts/read | 返回存储帐户的列表，或获取指定存储帐户的属性。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="site-recovery-operator"></a>Site Recovery 运算符
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许进行故障转移和故障回复，但不允许执行其他 Site Recovery 管理操作 |
> | **Id** | 494ae006-db33-4328-bf46-533a6560a3ca |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Insights/alertRules/* | 创建和管理警报规则 |
> | Microsoft.Network/virtualNetworks/read | 获取虚拟网络定义 |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp 是服务使用的内部操作 |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp 是服务使用的内部操作 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | “获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象的扩展信息 |
> | Microsoft.RecoveryServices/Vaults/read | “获取保管库”操作获取表示“vault”类型的 Azure 资源的对象 |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | “获取操作结果”操作可用于获取异步提交的操作的操作状态和结果 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | “获取容器”操作可用于获取针对资源注册的容器。 |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | 读取任何警报设置 |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | 读取任何事件 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | 检查结构的一致性 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | 读取任何结构 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | 重新关联网关 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | 续订 Fabric 的证书 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | 读取任何网络 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | 读取任何网络映射 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | 读取任何保护容器 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 读取任何可保护项 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | 应用还原点 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | 故障转移提交 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | 计划内故障转移 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 读取任何受保护项 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | 读取任何复制恢复点 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | 修复复制 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | 重新保护受保护的项 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | 测试故障转移 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | 测试故障转移清理 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | 故障转移 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | 更新移动服务 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 读取任何保护容器映射 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | 读取任何恢复服务提供程序 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | 刷新提供程序 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | 读取任何存储分类 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | 读取任何存储分类映射 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | 读取任何 vCenter |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | 创建和管理复制作业 |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | 读取任何策略 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | 故障转移提交恢复计划 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | 计划内故障转移恢复计划 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | 读取任何恢复计划 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | 重新保护恢复计划 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | 测试故障转移恢复计划 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | 测试故障转移清理恢复计划 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | 故障转移恢复计划 |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | 读取恢复服务保管库的警报 |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | 返回恢复服务保管库的令牌信息。 |
> | Microsoft.RecoveryServices/Vaults/usages/read | 返回恢复服务保管库的使用情况详细信息。 |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | “保管库令牌”操作可用于获取保管库级后端操作的保管库令牌。 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Storage/storageAccounts/read | 返回存储帐户的列表，或获取指定存储帐户的属性。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="site-recovery-reader"></a>Site Recovery 读取器
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许查看 Site Recovery 状态，但不允许执行其他管理操作 |
> | **Id** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp 是服务使用的内部操作 |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | “获取扩展信息”操作获取表示“vault”类型的 Azure 资源的对象的扩展信息 |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | 获取恢复服务保管库的警报。 |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | “获取保管库”操作获取表示“vault”类型的 Azure 资源的对象 |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | “获取操作结果”操作可用于获取异步提交的操作的操作状态和结果 |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | “获取容器”操作可用于获取针对资源注册的容器。 |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | 读取任何警报设置 |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | 读取任何事件 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | 读取任何结构 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | 读取任何网络 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | 读取任何网络映射 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | 读取任何保护容器 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | 读取任何可保护项 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | 读取任何受保护项 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | 读取任何复制恢复点 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | 读取任何保护容器映射 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | 读取任何恢复服务提供程序 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | 读取任何存储分类 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | 读取任何存储分类映射 |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | 读取任何 vCenter |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | 读取任何作业 |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | 读取任何策略 |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | 读取任何恢复计划 |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | 返回恢复服务保管库的令牌信息。 |
> | Microsoft.RecoveryServices/Vaults/usages/read | 返回恢复服务保管库的使用情况详细信息。 |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | “保管库令牌”操作可用于获取保管库级后端操作的保管库令牌。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="sql-db-contributor"></a>SQL DB 参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理 SQL 数据库，但不允许访问这些数据库。 此外，不允许管理其安全相关的策略或其父 SQL 服务器。 |
> | **Id** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Insights/alertRules/* | 创建和管理警报规则 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | 创建和管理 SQL 数据库 |
> | Microsoft.Sql/servers/read | 返回服务器列表，或获取指定服务器的属性。 |
> | Microsoft.Support/* | 创建和管理支持票证 |
> | **不操作** |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | 无法编辑审核策略 |
> | Microsoft.Sql/servers/databases/auditingSettings/* | 无法编辑审核设置 |
> | Microsoft.Sql/servers/databases/auditRecords/read | 检索数据库 Blob 审核记录 |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | 无法编辑连接策略 |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | 无法编辑数据屏蔽策略 |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | 无法编辑安全警报策略 |
> | Microsoft.Sql/servers/databases/securityMetrics/* | 无法编辑安全度量值 |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |

## <a name="sql-security-manager"></a>SQL 安全管理器
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理 SQL 服务器和数据库的安全相关策略，但不允许访问它们。 |
> | **Id** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取 Microsoft 授权 |
> | Microsoft.Insights/alertRules/* | 创建和管理 Insights 警报规则 |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | 将存储帐户或 SQL 数据库等资源加入到子网。 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Sql/servers/auditingPolicies/* | 创建和管理 SQL 服务器审核策略 |
> | Microsoft.Sql/servers/auditingSettings/* | 创建和管理 SQL 服务器审核设置 |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | 创建和管理 SQL 服务器数据库审核策略 |
> | Microsoft.Sql/servers/databases/auditingSettings/* | 创建和管理 SQL 服务器数据库审核设置 |
> | Microsoft.Sql/servers/databases/auditRecords/read | 读取审核记录 |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | 创建和管理 SQL 服务器数据库连接策略 |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | 创建和管理 SQL 服务器数据库数据屏蔽策略 |
> | Microsoft.Sql/servers/databases/read | 返回数据库的列表，或获取指定数据库的属性。 |
> | Microsoft.Sql/servers/databases/schemas/read | 检索数据库的架构列表 |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | 检索表的列列表 |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | 检索数据库的表列表 |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | 创建和管理 SQL 服务器数据库安全警报策略 |
> | Microsoft.Sql/servers/databases/securityMetrics/* | 创建和管理 SQL 服务器数据库安全度量值 |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | 返回服务器列表，或获取指定服务器的属性。 |
> | Microsoft.Sql/servers/securityAlertPolicies/* | 创建和管理 SQL 服务器安全警报策略 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="sql-server-contributor"></a>SQL Server 参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理 SQL 服务器和数据库，但不允许访问它们及其安全相关的策略。 |
> | **Id** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Insights/alertRules/* | 创建和管理 Insights 警报规则 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | 创建和管理 SQL 服务器 |
> | Microsoft.Support/* | 创建和管理支持票证 |
> | **不操作** |  |
> | Microsoft.Sql/servers/auditingPolicies/* | 无法编辑 SQL 服务器审核策略 |
> | Microsoft.Sql/servers/auditingSettings/* | 无法编辑 SQL 服务器审核设置 |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | 无法编辑 SQL 服务器数据库审核策略 |
> | Microsoft.Sql/servers/databases/auditingSettings/* | 无法编辑 SQL 服务器数据库审核设置 |
> | Microsoft.Sql/servers/databases/auditRecords/read | 无法读取审核记录 |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | 无法编辑 SQL 服务器数据库连接策略 |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | 无法编辑 SQL 服务器数据库数据屏蔽策略 |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | 无法编辑 SQL 服务器数据库安全警报策略 |
> | Microsoft.Sql/servers/databases/securityMetrics/* | 无法编辑 SQL 服务器数据库安全度量值 |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | 无法编辑 SQL 服务器安全警报策略 |

## <a name="storage-account-contributor"></a>存储帐户参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理存储帐户，但不允许对其进行访问。 |
> | **Id** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取所有授权 |
> | Microsoft.Insights/alertRules/* | 创建和管理 Insights 警报规则 |
> | Microsoft.Insights/diagnosticSettings/* | 管理诊断设置 |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | 将存储帐户或 SQL 数据库等资源加入到子网。 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Storage/storageAccounts/* | 创建和管理存储帐户 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="storage-account-key-operator-service-role"></a>存储帐户密钥操作员服务角色
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许存储帐户密钥操作员在存储帐户上列出和重新生成密钥 |
> | **Id** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **操作** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | 返回指定存储帐户的访问密钥。 |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | 再生成指定存储帐户的访问密钥。 |

## <a name="storage-blob-data-contributor-preview"></a>存储 Blob 数据参与者（预览版）
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 授予对 Azure 存储 blob 容器和数据的读取、写入和删除权限 |
> | **Id** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **操作** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | 返回删除容器的结果 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | 返回容器或容器列表 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | 返回放置或租用 blob 容器的结果 |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | 返回删除 blob 的结果 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | 返回 blob 或 blob 列表 |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | 返回写入 blob 的结果 |

## <a name="storage-blob-data-reader-preview"></a>存储 Blob 数据读者（预览）
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 授予对 Azure 存储 blob 容器和数据的读取权限 |
> | **Id** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **操作** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | 返回容器或容器列表 |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | 返回 blob 或 blob 列表 |

## <a name="storage-queue-data-contributor-preview"></a>存储队列数据参与者（预览）
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 授予对 Azure 存储队列和队列消息的读取、写入和删除权限 |
> | **Id** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | **操作** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | 返回删除队列的结果 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | 返回队列或队列列表。 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | 返回写入队列的结果 |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | 返回删除消息的结果 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | 返回消息 |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | 返回编写消息的结果 |

## <a name="storage-queue-data-reader-preview"></a>存储队列数据读者（预览）
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 授予对 Azure 存储队列和队列消息的读取权限 |
> | **Id** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **操作** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | 返回队列或队列列表。 |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | 返回消息 |

## <a name="support-request-contributor"></a>支持请求参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许创建和管理支持请求 |
> | **Id** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取授权 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="traffic-manager-contributor"></a>流量管理器参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理流量管理器配置文件，但不允许控制谁可以访问它们。 |
> | **Id** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取角色和角色分配 |
> | Microsoft.Insights/alertRules/* | 创建和管理 Insights 警报规则 |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="user-access-administrator"></a>用户访问管理员
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理用户对 Azure 资源的访问权限。 |
> | **Id** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **操作** |  |
> | */read | 读取除密码外的所有类型的资源。 |
> | Microsoft.Authorization/* | 管理授权 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="virtual-machine-administrator-login"></a>虚拟机管理员登录
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 在门户中查看虚拟机并以管理员身份登录 |
> | **Id** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **操作** |  |
> | Microsoft.Network/publicIPAddresses/read | 获取公共 IP 地址定义。 |
> | Microsoft.Network/virtualNetworks/read | 获取虚拟网络定义 |
> | Microsoft.Network/loadBalancers/read | 获取负载均衡器定义 |
> | Microsoft.Network/networkInterfaces/read | 获取网络接口定义。  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | 以普通用户身份登录虚拟机 |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | 以 Windows 管理员身份或 Linux 根用户权限登录虚拟机 |

## <a name="virtual-machine-contributor"></a>虚拟机参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理虚拟机，但不允许访问这些虚拟机及其连接到的虚拟网络或存储帐户。 |
> | **Id** | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取授权 |
> | Microsoft.Compute/availabilitySets/* | 创建和管理计算可用性集 |
> | Microsoft.Compute/locations/* | 创建和管理计算位置 |
> | Microsoft.Compute/virtualMachines/* | 创建和管理虚拟机 |
> | Microsoft.Compute/virtualMachineScaleSets/* | 创建和管理虚拟机规模集 |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | 创建和管理 Insights 警报规则 |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | 加入应用程序网关后端地址池 |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | 加入负载均衡器后端地址池 |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | 加入负载均衡器入站 NAT 池 |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | 加入负载均衡器入站 NAT 规则 |
> | Microsoft.Network/loadBalancers/probes/join/action | 允许使用负载均衡器的探测。 例如，使用此权限，VM 规模集的 healthProbe 属性可以引用探测。 |
> | Microsoft.Network/loadBalancers/read | 获取负载均衡器定义 |
> | Microsoft.Network/locations/* | 创建和管理网络位置 |
> | Microsoft.Network/networkInterfaces/* | 创建和管理网络接口 |
> | Microsoft.Network/networkSecurityGroups/join/action | 加入网络安全组 |
> | Microsoft.Network/networkSecurityGroups/read | 获取网络安全组定义 |
> | Microsoft.Network/publicIPAddresses/join/action | 联接公共 IP 地址 |
> | Microsoft.Network/publicIPAddresses/read | 获取公共 IP 地址定义。 |
> | Microsoft.Network/virtualNetworks/read | 获取虚拟网络定义 |
> | Microsoft.Network/virtualNetworks/subnets/join/action | 加入虚拟网络 |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | 返回受保护项的对象详细信息 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | 创建备份受保护项 |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | 创建备份保护意向 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | 返回所有保护策略 |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | 创建保护策略 |
> | Microsoft.RecoveryServices/Vaults/read | “获取保管库”操作获取表示“vault”类型的 Azure 资源的对象 |
> | Microsoft.RecoveryServices/Vaults/usages/read | 返回恢复服务保管库的使用情况详细信息。 |
> | Microsoft.RecoveryServices/Vaults/write | “创建保管库”操作创建“vault”类型的 Azure 资源 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Storage/storageAccounts/listKeys/action | 返回指定存储帐户的访问密钥。 |
> | Microsoft.Storage/storageAccounts/read | 返回存储帐户的列表，或获取指定存储帐户的属性。 |
> | Microsoft.Support/* | 创建和管理支持票证 |

## <a name="virtual-machine-user-login"></a>虚拟机用户登录
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 在门户中查看虚拟机并以普通用户身份登录。 |
> | **Id** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **操作** |  |
> | Microsoft.Network/publicIPAddresses/read | 获取公共 IP 地址定义。 |
> | Microsoft.Network/virtualNetworks/read | 获取虚拟网络定义 |
> | Microsoft.Network/loadBalancers/read | 获取负载均衡器定义 |
> | Microsoft.Network/networkInterfaces/read | 获取网络接口定义。  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | 以普通用户身份登录虚拟机 |

## <a name="web-plan-contributor"></a>Web 计划参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理网站的 Web 计划，但不允许访问这些计划。 |
> | **Id** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取授权 |
> | Microsoft.Insights/alertRules/* | 创建和管理 Insights 警报规则 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |
> | Microsoft.Web/serverFarms/* | 创建和管理服务器场 |

## <a name="website-contributor"></a>网站参与者
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **说明** | 允许管理网站（而非 Web 计划），但不允许访问这些网站。 |
> | **Id** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **操作** |  |
> | Microsoft.Authorization/*/read | 读取授权 |
> | Microsoft.Insights/alertRules/* | 创建和管理 Insights 警报规则 |
> | Microsoft.Insights/components/* | 创建和管理 Insights 组件 |
> | Microsoft.ResourceHealth/availabilityStatuses/read | 获取指定范围内所有资源的可用性状态 |
> | Microsoft.Resources/deployments/* | 创建和管理资源组部署 |
> | Microsoft.Resources/subscriptions/resourceGroups/read | 获取或列出资源组。 |
> | Microsoft.Support/* | 创建和管理支持票证 |
> | Microsoft.Web/certificates/* | 创建和管理网站证书 |
> | Microsoft.Web/listSitesAssignedToHostName/read | 获取分配给主机名的站点名称。 |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | 获取应用服务计划的属性 |
> | Microsoft.Web/sites/* | 创建和管理网站（站点创建还需要对关联应用服务计划有写入权限） |

## <a name="next-steps"></a>后续步骤

- [自定义角色](custom-roles.md)
- [使用 Azure 门户管理角色分配](role-assignments-portal.md)
- [Azure 安全中心中的权限](../security-center/security-center-permissions.md)
