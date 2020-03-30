---
title: 资源的标记支持
description: 显示支持标记的 Azure资源类型。 提供所有 Azure 服务的详细信息。
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 6100c667c7df0b3e1740777565d260af9fa818a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657567"
---
# <a name="tag-support-for-azure-resources"></a>Azure 资源的标记支持
本文介绍某一资源类型是否支持[标记](tag-resources.md)。 标记为“支持标记”**** 的列指示资源类型是否具有标记的属性。 标记为“在成本报表中标记”**** 的列指示该资源类型是否将标记传递给成本报表。 可以通过[成本管理成本分析](../../cost-management-billing/costs/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options)和 [Azure 账单发票和每日使用情况数据](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)中的标记查看成本。

若要以逗号分隔值文件的形式获取同一数据，请下载 [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv)。

跳转到资源提供程序命名空间：
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [微软.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [微软.阿兹康菲格](#microsoftazconfig)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [微软.Azure数据](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [微软.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [微软.经典计算](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [微软.经典网络](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [微软.认知服务](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [微软.消费](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [微软.客户锁定箱](#microsoftcustomerlockbox)
> - [微软.自定义提供程序](#microsoftcustomproviders)
> - [微软.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [微软.数据共享](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [微软.桌面虚拟化](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [微软.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [微软.企业知识图](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [微软.医疗保健Apis](#microsofthealthcareapis)
> - [](#microsofthybridcompute)Microsoft.HybridCompute
> - [Microsoft.HybridData](#microsofthybriddata)
> - [微软.海德拉](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [微软.钥匙库](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [微软.逻辑](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [微软.微服务4春天](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [微软.网络](#microsoftnetwork)
> - [微软.笔记本](#microsoftnotebooks)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [微软.对象商店](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [微软.运营洞察](#microsoftoperationalinsights)
> - [微软.运营管理](#microsoftoperationsmanagement)
> - [微软.对等](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [微软.项目巴比隆](#microsoftprojectbabylon)
> - [微软.恢复服务](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [微软.远程应用程序](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [微软.搜索](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [微软.服务总线](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [微软.服务](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [微软.软件计划](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [微软.拼车服务](#microsoftspoolservice)
> - [微软.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [微软.存储缓存](#microsoftstoragecache)
> - [微软.存储复制](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [微软.VMware云简单](#microsoftvmwarecloudsimple)
> - [微软.Vnf管理器](#microsoftvnfmanager)
> - [微软.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | DomainServices | 是 | 是 |
> | DomainServices / oucontainer | 否 | 否 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | supportProviders | 否 | 否 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | 否 | 否 |
> | addsservices | 否 | 否 |
> | 代理 | 否 | 否 |
> | anonymousapiusers | 否 | 否 |
> | 配置 | 否 | 否 |
> | 日志 | 否 | 否 |
> | 报表 | 否 | 否 |
> | servicehealthmetrics | 否 | 否 |
> | services | 否 | 否 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 配置 | 否 | 否 |
> | generateRecommendations | 否 | 否 |
> | metadata | 否 | 否 |
> | 建议 | 否 | 否 |
> | 禁止显示 | 否 | 否 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | actionRules | 是 | 是 |
> | alerts | 否 | 否 |
> | alertsList | 否 | 否 |
> | alertsMetaData | 否 | 否 |
> | alertsSummary | 否 | 否 |
> | alertsSummaryList | 否 | 否 |
> | 反馈 | 否 | 否 |
> | smartDetectorAlertRules | 是 | 是 |
> | smartDetectorRuntimeEnvironments | 否 | 否 |
> | smartGroups | 否 | 否 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | servers | 是 | 是 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | reportFeedback | 否 | 否 |
> | 服务 | 是 | 是 |
> | validateServiceName | 否 | 否 |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 配置存储 | 是 | 是 |
> | 配置存储/事件网格筛选器 | 否 | 否 |

## <a name="microsoftappplatform"></a>微软.AppPlatform

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | Spring | 是 | 是 |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | attestationProviders | 否 | 否 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | 否 | 否 |
> | dataAliases | 否 | 否 |
> | denyAssignments | 否 | 否 |
> | elevateAccess | 否 | 否 |
> | findOrphanRoleAssignments | 否 | 否 |
> | 锁定 | 否 | 否 |
> | 权限 | 否 | 否 |
> | policyAssignments | 否 | 否 |
> | policyDefinitions | 否 | 否 |
> | policySetDefinitions | 否 | 否 |
> | providerOperations | 否 | 否 |
> | roleAssignments | 否 | 否 |
> | 角色分配使用指标 | 否 | 否 |
> | roleDefinitions | 否 | 否 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | automationAccounts | 是 | 是 |
> | automationAccounts / configurations | 是 | 是 |
> | automationAccounts / jobs | 否 | 否 |
> | 自动化帐户 /专用端接策略 | 否 | 否 |
> | 自动化帐户/专用端点连接 | 否 | 否 |
> | 自动化账户/私人链接资源 | 否 | 否 |
> | automationAccounts / runbooks | 是 | 是 |
> | automationAccounts / softwareUpdateConfigurations | 否 | 否 |
> | automationAccounts / webhooks | 否 | 否 |

## <a name="microsoftazconfig"></a>微软.阿兹康菲格

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 配置存储 | 是 | 是 |
> | 配置存储/事件网格筛选器 | 否 | 否 |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | environments | 否 | 否 |
> | 环境/帐户 | 否 | 否 |
> | 环境/帐户/命名空间 | 否 | 否 |
> | 环境 / 帐户 / 命名空间 / 配置 | 否 | 否 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | 是 | 否 |
> | b2ctenants | 否 | 否 |

## <a name="microsoftazuredata"></a>微软.Azure数据

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 混合数据管理器 | 是 | 是 |
> | 后实例 | 是 | 是 |
> | sqlBigData 集群 | 是 | 是 |
> | sqlInstances | 是 | 是 |
> | sqlServer 注册 | 是 | 是 |
> | sqlServer 注册 / sqlServer | 否 | 否 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | registrations | 是 | 是 |
> | registrations / customerSubscriptions | 否 | 否 |
> | registrations / products | 否 | 否 |
> | verificationKeys | 否 | 否 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | batchAccounts | 是 | 是 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | billingAccounts | 否 | 否 |
> | 账单帐户/协议 | 否 | 否 |
> | 计费帐户/计费权限 | 否 | 否 |
> | 帐单帐户/帐单配置文件 | 否 | 否 |
> | 帐单帐户 / 帐单配置文件 / 计费权限 | 否 | 否 |
> | 帐单帐户 / 计费配置文件 / 计费角色分配 | 否 | 否 |
> | 账单账户/账单配置文件/计费角色定义 | 否 | 否 |
> | 帐单帐户 / 帐单配置文件 / 计费订阅 | 否 | 否 |
> | 计费帐户 /计费配置文件 / 创建计费角色分配 | 否 | 否 |
> | 账单账户/账单配置文件/客户 | 否 | 否 |
> | 账单账户/账单配置文件/说明 | 否 | 否 |
> | 帐单帐目 / 帐单配置文件 / 发票 | 否 | 否 |
> | 账单帐目 / 账单配置文件 / 发票 / 价格表 | 否 | 否 |
> | 帐单帐目 / 帐单配置文件 / 发票 / 交易记录 | 否 | 否 |
> | 帐单帐目 / 帐单配置文件 / 发票节 | 否 | 否 |
> | 帐单帐户 / 帐单配置文件 / 发票节 / 计费权限 | 否 | 否 |
> | 帐单帐户 / 帐单配置文件 / 发票节 / 计费角色分配 | 否 | 否 |
> | 账单科目 / 帐单配置文件 / 发票节 / 计费角色定义 | 否 | 否 |
> | 帐单帐户 / 帐单配置文件 / 发票节 / 计费订阅 | 否 | 否 |
> | 帐单帐目 / 帐单配置文件 / 发票节 / 创建计费角色分配 | 否 | 否 |
> | 账单账户 / 账单配置文件 / 发票节 / 启动转移 | 否 | 否 |
> | 账单账户 / 账单配置文件 / 发票节 / 产品 | 否 | 否 |
> | 账单账户 / 账单配置文件 / 发票部分 / 产品 / 转让 | 否 | 否 |
> | 账单帐户 / 帐单配置文件 / 发票节 / 产品 / 更新自动续订 | 否 | 否 |
> | 账单账户 / 帐单配置文件 / 发票节/ 交易记录 | 否 | 否 |
> | 账单账户 / 账单配置文件 / 发票部分 / 转账 | 否 | 否 |
> | 账单账户 / 计费配置文件 / 修补程序操作 | 否 | 否 |
> | 账单账户 / 账单配置文件 / 付款方式 | 否 | 否 |
> | 账单账户 /帐单配置文件/策略 | 否 | 否 |
> | 账单账户 / 账单配置文件 / 价格表 | 否 | 否 |
> | 账单账户 / 账单配置文件 / 价格表下载操作 | 否 | 否 |
> | 账单账户 / 账单配置文件 / 产品 | 否 | 否 |
> | 账单账户/账单配置文件/交易 | 否 | 否 |
> | 计费帐户/计费角色分配 | 否 | 否 |
> | 计费帐户/计费角色定义 | 否 | 否 |
> | 计费帐户/计费订阅 | 否 | 否 |
> | 帐单帐户 / 帐单订阅 / 发票 | 否 | 否 |
> | 计费帐户/ 创建计费角色分配 | 否 | 否 |
> | 计费帐户/创建发票节操作 | 否 | 否 |
> | 账单账户/客户 | 否 | 否 |
> | 计费帐户/客户/计费权限 | 否 | 否 |
> | 账单帐户/客户/计费订阅 | 否 | 否 |
> | 账单账户/客户/启动转账 | 否 | 否 |
> | 账单账户/客户/政策 | 否 | 否 |
> | 账单账户/客户/产品 | 否 | 否 |
> | 账单账户/客户/交易 | 否 | 否 |
> | 账单账户/客户/转账 | 否 | 否 |
> | 帐单帐户/部门 | 否 | 否 |
> | 帐单帐目/注册帐目 | 否 | 否 |
> | 帐单帐目/发票 | 否 | 否 |
> | 帐单科目 /发票部分 | 否 | 否 |
> | 帐单帐户 / 发票节 / 计费订阅移动操作 | 否 | 否 |
> | 帐单帐户 / 发票节 / 计费订阅 | 否 | 否 |
> | 账单账户 / 发票节 / 计费订阅 / 转账 | 否 | 否 |
> | 账单科目 / 发票节 / 提升 | 否 | 否 |
> | 账单科目 /发票节 / 启动转移 | 否 | 否 |
> | 帐单帐目 / 发票节 / 修补程序操作 | 否 | 否 |
> | 账单科目 / 发票节 / 产品移动操作 | 否 | 否 |
> | 账单科目 / 发票部分 / 产品 | 否 | 否 |
> | 账单账户 / 发票部分 / 产品 / 转移 | 否 | 否 |
> | 帐单帐户 / 发票部分 / 产品 / 更新自动续订 | 否 | 否 |
> | 帐单科目 / 发票部分 / 交易记录 | 否 | 否 |
> | 账单科目 / 发票部分 / 转账 | 否 | 否 |
> | 账单账户/信用额度 | 否 | 否 |
> | 计费帐户/修补程序操作 | 否 | 否 |
> | 账单账户/付款方式 | 否 | 否 |
> | 账单帐户/产品 | 否 | 否 |
> | 账单账户/交易 | 否 | 否 |
> | billingPeriods | 否 | 否 |
> | 计费权限 | 否 | 否 |
> | billingProperty | 否 | 否 |
> | 计费角色分配 | 否 | 否 |
> | 计费角色定义 | 否 | 否 |
> | 创建计费角色分配 | 否 | 否 |
> | departments | 否 | 否 |
> | enrollmentAccounts | 否 | 否 |
> | invoices | 否 | 否 |
> | transfers | 否 | 否 |
> | 转让/接受转移 | 否 | 否 |
> | 转移/拒绝转移 | 否 | 否 |
> | 转移/操作状态 | 否 | 否 |
> | 传输/验证传输 | 否 | 否 |
> | 验证地址 | 否 | 否 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | mapApis | 是 | 是 |
> | updateCommunicationPreference | 否 | 否 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 区块链成员 | 是 | 是 |
> | 脐带成员 | 是 | 是 |
> | 观察家 | 是 | 是 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | 否 | 否 |
> | 蓝图分配/分配操作 | 否 | 否 |
> | 蓝图分配/操作 | 否 | 否 |
> | blueprints | 否 | 否 |
> | 蓝图/工件 | 否 | 否 |
> | 蓝图/版本 | 否 | 否 |
> | 蓝图 /版本 / 工件 | 否 | 否 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | botServices | 是 | 是 |
> | 机器人服务/频道 | 否 | 否 |
> | 机器人服务/ 连接 | 否 | 否 |
> | 语言 | 否 | 否 |
> | 模板 | 否 | 否 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | Redis | 是 | 是 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | appliedReservations | 否 | 否 |
> | 自动配额增加 | 否 | 否 |
> | 计算交换 | 否 | 否 |
> | calculatePrice | 否 | 否 |
> | 计算买入价格 | 否 | 否 |
> | catalogs | 否 | 否 |
> | commercialReservationOrders | 否 | 否 |
> | 交易所 | 否 | 否 |
> | 地点采购订单 | 否 | 否 |
> | reservationOrders | 否 | 否 |
> | 预订订单/计算退款 | 否 | 否 |
> | 预订订单/合并 | 否 | 否 |
> | 预订订单/预订 | 否 | 否 |
> | 预订单/预订/修订 | 否 | 否 |
> | 预订单/退货 | 否 | 否 |
> | 预订订单/拆分 | 否 | 否 |
> | 预订单/交换 | 否 | 否 |
> | reservations | 否 | 否 |
> | 资源提供者 | 否 | 否 |
> | 资源 | 否 | 否 |
> | validateReservationOrder | 否 | 否 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | 否 | 否 |
> | CdnWebApplicationFirewallPolicies | 是 | 是 |
> | edgenodes | 否 | 否 |
> | 配置文件 | 是 | 是 |
> | profiles/endpoints | 是 | 是 |
> | profiles / endpoints / customdomains | 否 | 否 |
> | profiles / endpoints / origins | 否 | 否 |
> | validateProbe | 否 | 否 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | certificateOrders | 是 | 是 |
> | 证书订单/证书 | 否 | 否 |
> | validateCertificateRegistrationInformation | 否 | 否 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | capabilities | 否 | 否 |
> | domainNames | 否 | 否 |
> | domainNames / capabilities | 否 | 否 |
> | domainNames / internalLoadBalancers | 否 | 否 |
> | domainNames / serviceCertificates | 否 | 否 |
> | domainNames / slots | 否 | 否 |
> | domainNames / slots / roles | 否 | 否 |
> | domainNames / slots / roles / metricDefinitions | 否 | 否 |
> | domainNames / slots / roles / metrics | 否 | 否 |
> | moveSubscriptionResources | 否 | 否 |
> | operatingSystemFamilies | 否 | 否 |
> | operatingSystems | 否 | 否 |
> | quotas | 否 | 否 |
> | resourceTypes | 否 | 否 |
> | validateSubscriptionMoveAvailability | 否 | 否 |
> | virtualMachines | 否 | 否 |
> | virtualMachines / diagnosticSettings | 否 | 否 |
> | virtualMachines / metricDefinitions | 否 | 否 |
> | virtualMachines / metrics | 否 | 否 |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | 否 | 否 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | capabilities | 否 | 否 |
> | expressRouteCrossConnections | 否 | 否 |
> | expressRouteCrossConnections / peerings | 否 | 否 |
> | gatewaySupportedDevices | 否 | 否 |
> | networkSecurityGroups | 否 | 否 |
> | quotas | 否 | 否 |
> | reservedIps | 否 | 否 |
> | virtualNetworks | 否 | 否 |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | 否 | 否 |
> | virtualNetworks / virtualNetworkPeerings | 否 | 否 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | capabilities | 否 | 否 |
> | disks | 否 | 否 |
> | images | 否 | 否 |
> | osImages | 否 | 否 |
> | osPlatformImages | 否 | 否 |
> | publicImages | 否 | 否 |
> | quotas | 否 | 否 |
> | storageAccounts | 否 | 否 |
> | storageAccounts / blobServices | 否 | 否 |
> | storageAccounts / fileServices | 否 | 否 |
> | storageAccounts / metricDefinitions | 否 | 否 |
> | storageAccounts / metrics | 否 | 否 |
> | storageAccounts / queueServices | 否 | 否 |
> | storageAccounts / services | 否 | 否 |
> | storageAccounts / services / diagnosticSettings | 否 | 否 |
> | storageAccounts / services / metricDefinitions | 否 | 否 |
> | storageAccounts / services / metrics | 否 | 否 |
> | storageAccounts / tableServices | 否 | 否 |
> | storageAccounts / vmImages | 否 | 否 |
> | vmImages | 否 | 否 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | RateCard | 否 | 否 |
> | UsageAggregates | 否 | 否 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | availabilitySets | 是 | 是 |
> | diskEncryptionSets | 是 | 是 |
> | disks | 是 | 是 |
> | galleries | 是 | 是 |
> | galleries / applications | 否 | 否 |
> | galleries / applications / versions | 否 | 否 |
> | galleries/images | 否 | 否 |
> | galleries/images/versions | 否 | 否 |
> | hostGroups | 是 | 是 |
> | hostGroups / hosts | 是 | 是 |
> | images | 是 | 是 |
> | proximityPlacementGroups | 是 | 是 |
> | restorePointCollections | 是 | 是 |
> | restorePointCollections / restorePoints | 否 | 否 |
> | sharedVMImages | 是 | 是 |
> | sharedVMImages / versions | 否 | 否 |
> | snapshots | 是 | 是 |
> | virtualMachines | 是 | 是 |
> | virtualMachines / extensions | 是 | 是 |
> | virtualMachines / metricDefinitions | 否 | 否 |
> | virtualMachineScaleSets | 是 | 是 |
> | virtualMachineScaleSets / extensions | 否 | 否 |
> | virtualMachineScaleSets / networkInterfaces | 否 | 否 |
> | virtualMachineScaleSets / publicIPAddresses | 否 | 否 |
> | virtualMachineScaleSets / virtualMachines | 否 | 否 |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | 否 | 否 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | 否 | 否 |
> | 余额 | 否 | 否 |
> | 预算 | 否 | 否 |
> | Charges | 否 | 否 |
> | CostTags | 否 | 否 |
> | credits | 否 | 否 |
> | 活动 | 否 | 否 |
> | 预测 | 否 | 否 |
> | lots | 否 | 否 |
> | 市场 | 否 | 否 |
> | Pricesheets | 否 | 否 |
> | products | 否 | 否 |
> | ReservationDetails | 否 | 否 |
> | ReservationRecommendations | 否 | 否 |
> | ReservationSummaries | 否 | 否 |
> | ReservationTransactions | 否 | 否 |
> | Tags | 否 | 否 |
> | tenants | 否 | 否 |
> | 术语 | 否 | 否 |
> | UsageDetails | 否 | 否 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | containerGroups | 是 | 是 |
> | serviceAssociationLinks | 否 | 否 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | registries | 是 | 是 |
> | registries / builds | 否 | 否 |
> | registries / builds / cancel | 否 | 否 |
> | registries / builds / getLogLink | 否 | 否 |
> | registries / buildTasks | 是 | 是 |
> | registries / buildTasks / steps | 否 | 否 |
> | registries / eventGridFilters | 否 | 否 |
> | registries / generateCredentials | 否 | 否 |
> | registries / getBuildSourceUploadUrl | 否 | 否 |
> | registries / GetCredentials | 否 | 否 |
> | registries / importImage | 否 | 否 |
> | 注册表 / 私有端接连接 Proxies | 否 | 否 |
> | 注册表 / 私有端接连接Proxies / 验证 | 否 | 否 |
> | 注册表/专用端点连接 | 否 | 否 |
> | 注册/ 私人链接资源 | 否 | 否 |
> | registries / queueBuild | 否 | 否 |
> | registries / regenerateCredential | 否 | 否 |
> | registries / regenerateCredentials | 否 | 否 |
> | registries/replications | 是 | 是 |
> | registries / runs | 否 | 否 |
> | registries / runs / cancel | 否 | 否 |
> | registries / scheduleRun | 否 | 否 |
> | registries / scopeMaps | 否 | 否 |
> | registries / taskRuns | 是 | 是 |
> | registries/tasks | 是 | 是 |
> | registries / tokens | 否 | 否 |
> | registries / updatePolicies | 否 | 否 |
> | registries/webhooks | 是 | 是 |
> | registries / webhooks / getCallbackConfig | 否 | 否 |
> | registries / webhooks / ping | 否 | 否 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | containerServices | 是 | 是 |
> | managedClusters | 是 | 是 |
> | openShiftManagedClusters | 是 | 是 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 警报 | 否 | 否 |
> | BillingAccounts | 否 | 否 |
> | 预算 | 否 | 否 |
> | 云连接器 | 否 | 否 |
> | 连接器 | 是 | 是 |
> | Departments | 否 | 否 |
> | 维度 | 否 | 否 |
> | EnrollmentAccounts | 否 | 否 |
> | 出口 | 否 | 否 |
> | 外部结算帐户 | 否 | 否 |
> | 外部结算帐户/警报 | 否 | 否 |
> | 外部计费帐户 / 维度 | 否 | 否 |
> | 外部结算账户/预测 | 否 | 否 |
> | 外部计费帐户/查询 | 否 | 否 |
> | 外部订阅 | 否 | 否 |
> | 外部订阅/警报 | 否 | 否 |
> | 外部订阅/维度 | 否 | 否 |
> | 外部订阅/预测 | 否 | 否 |
> | 外部订阅/查询 | 否 | 否 |
> | 预测 | 否 | 否 |
> | 查询 | 否 | 否 |
> | register | 否 | 否 |
> | Reportconfigs | 否 | 否 |
> | 报表 | 否 | 否 |
> | 设置 | 否 | 否 |
> | 显示回法规则 | 否 | 否 |
> | 视图 | 否 | 否 |

## <a name="microsoftcustomerlockbox"></a>微软.客户锁定箱

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 请求 | 否 | 否 |

## <a name="microsoftcustomproviders"></a>微软.自定义提供程序

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 关联 | 否 | 否 |
> | 资源提供者 | 是 | 是 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | jobs | 是 | 是 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | 是 | 是 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | workspaces | 是 | 否 |
> | 工作区/数据库工作区 | 否 | 否 |
> | 工作区/存储加密 | 否 | 否 |
> | 工作区/虚拟网络对等互连 | 否 | 否 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | catalogs | 是 | 是 |
> | 数据目录 | 是 | 是 |
> | 数据目录/数据源 | 否 | 否 |
> | 数据目录 / 数据源 / 扫描 | 否 | 否 |
> | 数据目录 / 数据源 / 扫描 / 数据集 | 否 | 否 |
> | 数据目录 / 数据源 / 扫描 / 触发器 | 否 | 否 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | dataFactories | 是 | 否 |
> | dataFactories / diagnosticSettings | 否 | 否 |
> | dataFactories / metricDefinitions | 否 | 否 |
> | dataFactorySchema | 否 | 否 |
> | factories | 是 | 否 |
> | factories / integrationRuntimes | 否 | 否 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | 帐户/数据湖存储帐户 | 否 | 否 |
> | 帐户/存储帐户 | 否 | 否 |
> | 帐户 / 存储帐户 / 容器 | 否 | 否 |
> | 账户/转账分析单位 | 否 | 否 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | 帐户/事件网格筛选器 | 否 | 否 |
> | 帐户/防火墙规则 | 否 | 否 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | services | 否 | 否 |
> | services/projects | 否 | 否 |

## <a name="microsoftdatashare"></a>微软.数据共享

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | 账户/股份 | 否 | 否 |
> | 帐户/ 共享 / 数据集 | 否 | 否 |
> | 账户/ 股票 / 邀请 | 否 | 否 |
> | 帐户/共享 / 提供商共享订阅 | 否 | 否 |
> | 帐户/ 共享 / 同步设置 | 否 | 否 |
> | 帐户/共享订阅 | 否 | 否 |
> | 帐户 / 共享订阅 / 使用者来源数据集组 | 否 | 否 |
> | 帐户/共享订阅/数据集映射 | 否 | 否 |
> | 帐户 / 共享订阅 / 触发器 | 否 | 否 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | servers | 是 | 是 |
> | servers / advisors | 否 | 否 |
> | servers / keys | 否 | 否 |
> | servers / privateEndpointConnectionProxies | 否 | 否 |
> | servers / privateEndpointConnections | 否 | 否 |
> | servers / privateLinkResources | 否 | 否 |
> | servers / queryTexts | 否 | 否 |
> | servers / recoverableServers | 否 | 否 |
> | servers / topQueryStatistics | 否 | 否 |
> | servers / virtualNetworkRules | 否 | 否 |
> | servers / waitStatistics | 否 | 否 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | servers | 是 | 是 |
> | servers / advisors | 否 | 否 |
> | servers / keys | 否 | 否 |
> | servers / privateEndpointConnectionProxies | 否 | 否 |
> | servers / privateEndpointConnections | 否 | 否 |
> | servers / privateLinkResources | 否 | 否 |
> | servers / queryTexts | 否 | 否 |
> | servers / recoverableServers | 否 | 否 |
> | servers / topQueryStatistics | 否 | 否 |
> | servers / virtualNetworkRules | 否 | 否 |
> | servers / waitStatistics | 否 | 否 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | serverGroups | 是 | 是 |
> | servers | 是 | 是 |
> | servers / advisors | 否 | 否 |
> | servers / keys | 否 | 否 |
> | servers / privateEndpointConnectionProxies | 否 | 否 |
> | servers / privateEndpointConnections | 否 | 否 |
> | servers / privateLinkResources | 否 | 否 |
> | servers / queryTexts | 否 | 否 |
> | servers / recoverableServers | 否 | 否 |
> | servers / topQueryStatistics | 否 | 否 |
> | servers / virtualNetworkRules | 否 | 否 |
> | servers / waitStatistics | 否 | 否 |
> | serversv2 | 是 | 是 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 工件来源 | 是 | 是 |
> | rollouts | 是 | 是 |
> | 服务拓扑 | 是 | 是 |
> | 服务拓扑/服务 | 是 | 是 |
> | 服务机构 / 服务 / 服务单位 | 是 | 是 |
> | steps | 是 | 是 |

## <a name="microsoftdesktopvirtualization"></a>微软.桌面虚拟化

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 应用程序组 | 是 | 是 |
> | 应用程序组/应用程序 | 否 | 否 |
> | 应用程序组/桌面 | 否 | 否 |
> | 应用程序组/启动菜单项 | 否 | 否 |
> | 主机池 | 是 | 是 |
> | 主机池/会话主机 | 否 | 否 |
> | 主机池/会话主机/用户会话 | 否 | 否 |
> | 主机池/用户会话 | 否 | 否 |
> | workspaces | 是 | 是 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | ElasticPools | 是 | 是 |
> | ElasticPools / IotHubTenants | 是 | 是 |
> | 弹性池 / IotHub租户 / 安全设置 | 否 | 否 |
> | IotHubs | 是 | 是 |
> | IotHubs / eventGridFilters | 否 | 否 |
> | IotHubs / 安全设置 | 否 | 否 |
> | ProvisioningServices | 是 | 是 |
> | usages | 否 | 否 |

## <a name="microsoftdevops"></a>微软.DevOps

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 管道 | 是 | 是 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 控制器 | 是 | 是 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | labcenters | 是 | 是 |
> | labs | 是 | 是 |
> | 实验室/环境 | 是 | 是 |
> | 实验室/服务运行者 | 是 | 是 |
> | 实验室/虚拟机 | 是 | 是 |
> | schedules | 是 | 是 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | 否 | 否 |
> | databaseAccounts | 是 | 是 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | domains | 是 | 是 |
> | 域 /域所有权标识符 | 否 | 否 |
> | generateSsoRequest | 否 | 否 |
> | topLevelDomains | 否 | 否 |
> | validateDomainRegistrationInformation | 否 | 否 |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | lcsprojects | 否 | 否 |
> | lcs项目/云部署 | 否 | 否 |
> | lcs项目/ 连接器 | 否 | 否 |

## <a name="microsoftenterpriseknowledgegraph"></a>微软.企业知识图

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | services | 是 | 是 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | domains | 是 | 是 |
> | domains / topics | 否 | 否 |
> | eventSubscriptions | 否 | 否 |
> | extensionTopics | 否 | 否 |
> | 合作伙伴命名空间 | 是 | 是 |
> | 合作伙伴命名空间/ 事件通道 | 否 | 否 |
> | 合作伙伴注册 | 是 | 是 |
> | 合作伙伴主题 | 是 | 是 |
> | 合作伙伴主题/活动订阅 | 否 | 否 |
> | 系统主题 | 是 | 是 |
> | 系统主题/事件订阅 | 否 | 否 |
> | topics | 是 | 是 |
> | topicTypes | 否 | 否 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | namespaces | 是 | 是 |
> | namespaces / authorizationrules | 否 | 否 |
> | namespaces / disasterrecoveryconfigs | 否 | 否 |
> | namespaces / eventhubs | 否 | 否 |
> | namespaces / eventhubs / authorizationrules | 否 | 否 |
> | namespaces / eventhubs / consumergroups | 否 | 否 |
> | namespaces / networkrulesets | 否 | 否 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | features | 否 | 否 |
> | providers | 否 | 否 |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | enroll | 否 | 否 |
> | galleryitems | 否 | 否 |
> | generateartifactaccessuri | 否 | 否 |
> | myareas | 否 | 否 |
> | 我的区域/地区 | 否 | 否 |
> | 我的区域 / 区域 / 区域 | 否 | 否 |
> | 我的区域 / 区域 / 区域 / 画廊项目 | 否 | 否 |
> | 我的区域 / 区域 / 画廊项目 | 否 | 否 |
> | 我的区域 / 画廊项目 | 否 | 否 |
> | register | 否 | 否 |
> | 资源 | 否 | 否 |
> | retrieveresourcesbyid | 否 | 否 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 自动管理帐户 | 是 | 是 |
> | autoManagedVmConfigurationProfiles | 是 | 是 |
> | configurationProfileAssignments | 否 | 否 |
> | guestConfigurationAssignments | 否 | 否 |
> | software | 否 | 否 |
> | softwareUpdateProfile | 否 | 否 |
> | softwareUpdates | 否 | 否 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | hanainstances | 是 | 是 |
> | 皂显示器 | 是 | 是 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 专用的 HSM | 是 | 是 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | clusters/applications | 否 | 否 |

## <a name="microsofthealthcareapis"></a>微软.医疗保健Apis

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | services | 是 | 是 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 机器 | 是 | 是 |
> | 机器/扩展 | 是 | 是 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 数据管理器 | 是 | 是 |

## <a name="microsofthydra"></a>微软.海德拉

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | components | 是 | 是 |
> | 网络范围 | 是 | 是 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | jobs | 是 | 是 |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 否 | 否 |
> | diagnosticSettingsCategories | 否 | 否 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | appTemplates | 否 | 否 |
> | IoTApps | 是 | 是 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 图形 | 是 | 是 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | deletedVaults | 否 | 否 |
> | hsmPools | 是 | 是 |
> | vaults | 是 | 是 |
> | vaults / accessPolicies | 否 | 否 |
> | vaults / eventGridFilters | 否 | 否 |
> | vaults / secrets | 否 | 否 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | clusters / attacheddatabaseconfigurations | 否 | 否 |
> | clusters / databases | 否 | 否 |
> | clusters / databases / dataconnections | 否 | 否 |
> | clusters / databases / eventhubconnections | 否 | 否 |
> | 群集/数据库/委托分配 | 否 | 否 |
> | 群集/主体分配 | 否 | 否 |
> | clusters / sharedidentities | 否 | 否 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | labaccounts | 是 | 是 |
> | users | 否 | 否 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | 是 | 是 |
> | integrationAccounts | 是 | 是 |
> | integrationServiceEnvironments | 是 | 是 |
> | integrationServiceEnvironments / managedApis | 是 | 是 |
> | isolatedEnvironments | 是 | 是 |
> | workflows | 是 | 是 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | 是 | 是 |
> | webServices | 是 | 是 |
> | 工作区 | 是 | 是 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | workspaces | 是 | 是 |
> | 工作区/计算 | 否 | 否 |
> | 工作区/事件网格筛选器 | 否 | 否 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 标识 | 否 | 否 |
> | userAssignedIdentities | 是 | 是 |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 市场注册定义 | 否 | 否 |
> | 注册分配 | 否 | 否 |
> | 注册定义 | 否 | 否 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | getEntities | 否 | 否 |
> | managementGroups | 否 | 否 |
> | 管理组/设置 | 否 | 否 |
> | 资源 | 否 | 否 |
> | startTenantBackfill | 否 | 否 |
> | tenantBackfillStatus | 否 | 否 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | 帐户/事件网格筛选器 | 否 | 否 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | offers | 否 | 否 |
> | offerTypes | 否 | 否 |
> | 优惠类型/发布商 | 否 | 否 |
> | 产品类型 / 发布商 / 优惠 | 否 | 否 |
> | 优惠类型 / 发布商 / 优惠 / 计划 | 否 | 否 |
> | 优惠类型 / 发布商 / 优惠 / 计划 / 协议 | 否 | 否 |
> | 报价类型 / 发布商 / 优惠 / 计划 / 配置 | 否 | 否 |
> | 报价类型 / 发布商 / 优惠 / 计划 / 配置 / 导入图像 | 否 | 否 |
> | privategalleryitems | 否 | 否 |
> | 专用存储客户端 | 否 | 否 |
> | products | 否 | 否 |
> | publishers | 否 | 否 |
> | 出版商/优惠 | 否 | 否 |
> | 出版商/ 优惠 / 修订 | 否 | 否 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | classicDevServices | 是 | 是 |
> | updateCommunicationPreference | 否 | 否 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 协议 | 否 | 否 |
> | offertypes | 否 | 否 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | mediaservices | 是 | 是 |
> | mediaservices / accountFilters | 否 | 否 |
> | mediaservices / assets | 否 | 否 |
> | mediaservices / assets / assetFilters | 否 | 否 |
> | mediaservices / contentKeyPolicies | 否 | 否 |
> | mediaservices / eventGridFilters | 否 | 否 |
> | mediaservices / liveEventOperations | 否 | 否 |
> | mediaservices / liveEvents | 是 | 是 |
> | mediaservices / liveEvents / liveOutputs | 否 | 否 |
> | mediaservices / liveOutputOperations | 否 | 否 |
> | mediaservices / mediaGraphs | 否 | 否 |
> | mediaservices / streamingEndpointOperations | 否 | 否 |
> | mediaservices / streamingEndpoints | 是 | 是 |
> | mediaservices / streamingLocators | 否 | 否 |
> | mediaservices / streamingPolicies | 否 | 否 |
> | mediaservices / transforms | 否 | 否 |
> | mediaservices / transforms / jobs | 否 | 否 |

## <a name="microsoftmicroservices4spring"></a>微软.微服务4春天

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 应用群集 | 是 | 是 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 评估项目 | 是 | 是 |
> | migrateprojects | 是 | 是 |
> | 移动集合 | 是 | 是 |
> | projects | 是 | 是 |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 全息图形广播帐户 | 是 | 是 |
> | 对象理解帐户 | 是 | 是 |
> | 远程渲染帐户 | 是 | 是 |
> | 空间锚定帐户 | 是 | 是 |
> | 表面重建帐户 | 是 | 是 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 净应用账户 | 是 | 否 |
> | 净应用账户/容量池 | 是 | 否 |
> | 净应用账户 / 容量池 / 卷 | 是 | 否 |
> | 净应用账户 / 容量池 / 卷 / 快照 | 否 | 否 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | applicationGateways | 是 | 是 |
> | applicationGatewayWebApplicationFirewallPolicies | 是 | 是 |
> | applicationSecurityGroups | 是 | 是 |
> | azureFirewallFqdnTags | 否 | 否 |
> | azureFirewalls | 是 | 否 |
> | bastionHosts | 是 | 是 |
> | bgpServiceCommunities | 否 | 否 |
> | connections | 是 | 是 |
> | ddosCustomPolicies | 是 | 是 |
> | ddosProtectionPlans | 是 | 是 |
> | dnsOperationStatuses | 否 | 否 |
> | dnszones | 是 | 是 |
> | dnszones / A | 否 | 否 |
> | dnszones / AAAA | 否 | 否 |
> | dnszones / all | 否 | 否 |
> | dnszones / CAA | 否 | 否 |
> | dnszones / CNAME | 否 | 否 |
> | dnszones / MX | 否 | 否 |
> | dnszones / NS | 否 | 否 |
> | dnszones / PTR | 否 | 否 |
> | dnszones / recordsets | 否 | 否 |
> | dnszones / SOA | 否 | 否 |
> | dnszones / SRV | 否 | 否 |
> | dnszones / TXT | 否 | 否 |
> | expressRouteCircuits | 是 | 是 |
> | expressRouteCrossConnections | 是 | 是 |
> | expressRouteGateways | 是 | 是 |
> | expressRoutePorts | 是 | 是 |
> | expressRouteServiceProviders | 否 | 否 |
> | firewallPolicies | 是 | 是 |
> | frontdoors | 是，但受限制（请参见[下面的注释](#frontdoor)） | 是 |
> | frontdoorWebApplicationFirewallManagedRuleSets | 是，但受限制（请参见[下面的注释](#frontdoor)） | 否 |
> | frontdoorWebApplicationFirewallPolicies | 是，但受限制（请参见[下面的注释](#frontdoor)） | 是 |
> | getDnsResourceReference | 否 | 否 |
> | internalNotify | 否 | 否 |
> | loadBalancers | 是 | 否 |
> | localNetworkGateways | 是 | 是 |
> | natGateways | 是 | 是 |
> | networkIntentPolicies | 是 | 是 |
> | networkInterfaces | 是 | 是 |
> | networkProfiles | 是 | 是 |
> | networkSecurityGroups | 是 | 是 |
> | networkWatchers | 是 | 否 |
> | networkWatchers / connectionMonitors | 是 | 否 |
> | networkWatchers / lenses | 是 | 否 |
> | networkWatchers / pingMeshes | 是 | 否 |
> | p2sVpnGateways | 是 | 是 |
> | privateDnsOperationStatuses | 否 | 否 |
> | privateDnsZones | 是 | 是 |
> | privateDnsZones / A | 否 | 否 |
> | privateDnsZones / AAAA | 否 | 否 |
> | privateDnsZones / all | 否 | 否 |
> | privateDnsZones / CNAME | 否 | 否 |
> | privateDnsZones / MX | 否 | 否 |
> | privateDnsZones / PTR | 否 | 否 |
> | privateDnsZones / SOA | 否 | 否 |
> | privateDnsZones / SRV | 否 | 否 |
> | privateDnsZones / TXT | 否 | 否 |
> | privateDnsZones / virtualNetworkLinks | 是 | 是 |
> | privateEndpoints | 是 | 是 |
> | privateLinkServices | 是 | 是 |
> | publicIPAddresses | 是 | 是 |
> | publicIPPrefixes | 是 | 是 |
> | routeFilters | 是 | 是 |
> | routeTables | 是 | 是 |
> | serviceEndpointPolicies | 是 | 是 |
> | trafficManagerGeographicHierarchies | 否 | 否 |
> | trafficmanagerprofiles | 是 | 是 |
> | trafficmanagerprofiles/heatMaps | 否 | 否 |
> | trafficManagerUserMetricsKeys | 否 | 否 |
> | virtualHubs | 是 | 是 |
> | virtualNetworkGateways | 是 | 是 |
> | virtualNetworks | 是 | 是 |
> | virtualNetworkTaps | 是 | 是 |
> | virtualWans | 是 | 是 |
> | vpnGateways | 是 | 否 |
> | vpnSites | 是 | 是 |
> | webApplicationFirewallPolicies | 是 | 是 |

<a id="frontdoor" />

> [!NOTE]
> 对于 Azure 前门服务，可以在创建资源时应用标记，但当前不支持更新或添加标记。


## <a name="microsoftnotebooks"></a>微软.笔记本

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 笔记本 | 否 | 否 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | namespaces | 是 | 否 |
> | namespaces / notificationHubs | 是 | 否 |

## <a name="microsoftobjectstore"></a>微软.对象商店

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | os 命名空间 | 是 | 是 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 超级网站 | 是 | 是 |
> | 导入网站 | 是 | 是 |
> | 服务器网站 | 是 | 是 |
> | VMwareSites | 是 | 是 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | linkTargets | 否 | 否 |
> | storageInsightConfigs | 否 | 否 |
> | workspaces | 是 | 是 |
> | 工作区/数据导出 | 否 | 否 |
> | workspaces / dataSources | 否 | 否 |
> | workspaces / linkedServices | 否 | 否 |
> | 工作区/专用端接Proxie | 否 | 否 |
> | 工作区/专用端点连接 | 否 | 否 |
> | 工作区/专用链接资源 | 否 | 否 |
> | workspaces / query | 否 | 否 |
> | 工作区/作用域私人链接 Proxies | 否 | 否 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | managementassociations | 否 | 否 |
> | managementconfigurations | 是 | 是 |
> | solutions | 是 | 是 |
> | 视图 | 是 | 是 |

## <a name="microsoftpeering"></a>微软.对等

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 传统对等互连 | 否 | 否 |
> | 对等Asns | 否 | 否 |
> | 对等互连 | 是 | 是 |
> | 对等服务国家 | 否 | 否 |
> | 对等服务提供商 | 否 | 否 |
> | 对等服务 | 是 | 是 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | policyEvents | 否 | 否 |
> | policyMetadata | 否 | 否 |
> | policyStates | 否 | 否 |
> | policyTrackedResources | 否 | 否 |
> | remediations | 否 | 否 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | consoles | 否 | 否 |
> | dashboards | 是 | 是 |
> | userSettings | 否 | 否 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | 是 | 是 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | capacities | 是 | 是 |

## <a name="microsoftprojectbabylon"></a>微软.项目巴比隆

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | 否 | 否 |
> | vaults | 是 | 是 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | namespaces | 是 | 是 |
> | namespaces / authorizationrules | 否 | 否 |
> | namespaces / hybridconnections | 否 | 否 |
> | namespaces / hybridconnections / authorizationrules | 否 | 否 |
> | namespaces / wcfrelays | 否 | 否 |
> | namespaces / wcfrelays / authorizationrules | 否 | 否 |

## <a name="microsoftremoteapp"></a>微软.远程应用程序

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | accounts | 否 | 否 |
> | 集合 | 是 | 是 |
> | 集合/应用程序 | 否 | 否 |
> | 集合/安全主体 | 否 | 否 |
> | 模板图像 | 否 | 否 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 查询 | 是 | 是 |
> | resourceChangeDetails | 否 | 否 |
> | resourceChanges | 否 | 否 |
> | 资源 | 否 | 否 |
> | resourcesHistory | 否 | 否 |
> | subscriptionsStatus | 否 | 否 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | 否 | 否 |
> | childAvailabilityStatuses | 否 | 否 |
> | childResources | 否 | 否 |
> | 新兴问题 | 否 | 否 |
> | 活动 | 否 | 否 |
> | impactedResources | 否 | 否 |
> | metadata | 否 | 否 |
> | 通知 | 否 | 否 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | deployments | 是 | 否 |
> | deployments / operations | 否 | 否 |
> | deploymentScripts | 是 | 是 |
> | deploymentScripts / logs | 否 | 否 |
> | 链接 | 否 | 否 |
> | notifyResourceJobs | 否 | 否 |
> | providers | 否 | 否 |
> | resourceGroups | 是 | 否 |
> | subscriptions | 是 | 否 |
> | tenants | 否 | 否 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 服务 | 是 | 是 |
> | saasresources | 否 | 否 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | 否 | 否 |
> | searchServices | 是 | 是 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | 否 | 否 |
> | advancedThreatProtectionSettings | 否 | 否 |
> | alerts | 否 | 否 |
> | allowedConnections | 否 | 否 |
> | applicationWhitelistings | 否 | 否 |
> | assessmentMetadata | 否 | 否 |
> | assessments | 否 | 否 |
> | autoDismissAlertsRules | 否 | 否 |
> | automations | 是 | 是 |
> | AutoProvisioningSettings | 否 | 否 |
> | Compliances | 否 | 否 |
> | dataCollectionAgents | 否 | 否 |
> | deviceSecurityGroups | 否 | 否 |
> | discoveredSecuritySolutions | 否 | 否 |
> | externalSecuritySolutions | 否 | 否 |
> | InformationProtectionPolicies | 否 | 否 |
> | iotSecuritySolutions | 是 | 是 |
> | iotSecuritySolutions / analyticsModels | 否 | 否 |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | 否 | 否 |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | 否 | 否 |
> | jitNetworkAccessPolicies | 否 | 否 |
> | networkData | 否 | 否 |
> | 策略 | 否 | 否 |
> | pricings | 否 | 否 |
> | regulatoryComplianceStandards | 否 | 否 |
> | regulatoryComplianceStandards / regulatoryComplianceControls | 否 | 否 |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | 否 | 否 |
> | securityContacts | 否 | 否 |
> | securitySolutions | 否 | 否 |
> | securitySolutionsReferenceData | 否 | 否 |
> | securityStatuses | 否 | 否 |
> | securityStatusesSummaries | 否 | 否 |
> | serverVulnerabilityAssessments | 否 | 否 |
> | 设置 | 否 | 否 |
> | subAssessments | 否 | 否 |
> | 任务 | 否 | 否 |
> | topologies | 否 | 否 |
> | workspaceSettings | 否 | 否 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 否 | 否 |
> | diagnosticSettingsCategories | 否 | 否 |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | aggregations | 否 | 否 |
> | 警报规则 | 否 | 否 |
> | 警报规则模板 | 否 | 否 |
> | 书签 | 否 | 否 |
> | cases | 否 | 否 |
> | 数据连接器 | 否 | 否 |
> | 数据连接器检查要求 | 否 | 否 |
> | 实体 | 否 | 否 |
> | 实体查询 | 否 | 否 |
> | 事件 | 否 | 否 |
> | 办公室同意 | 否 | 否 |
> | 设置 | 否 | 否 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | namespaces | 是 | 否 |
> | namespaces / authorizationrules | 否 | 否 |
> | namespaces / disasterrecoveryconfigs | 否 | 否 |
> | namespaces / eventgridfilters | 否 | 否 |
> | namespaces / networkrulesets | 否 | 否 |
> | namespaces / queues | 否 | 否 |
> | namespaces / queues / authorizationrules | 否 | 否 |
> | namespaces / topics | 否 | 否 |
> | namespaces / topics / authorizationrules | 否 | 否 |
> | namespaces / topics / subscriptions | 否 | 否 |
> | namespaces / topics / subscriptions / rules | 否 | 否 |
> | premiumMessagingRegions | 否 | 否 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 服务 | 是 | 是 |
> | clusters | 是 | 是 |
> | clusters/applications | 否 | 否 |
> | containerGroups | 是 | 是 |
> | containerGroupSets | 是 | 是 |
> | edgeclusters | 是 | 是 |
> | edgeclusters / applications | 否 | 否 |
> | managedclusters | 是 | 是 |
> | 托管群集/节点类型 | 否 | 否 |
> | networks | 是 | 是 |
> | secretstores | 是 | 是 |
> | secretstores / certificates | 否 | 否 |
> | secretstores / secrets | 否 | 否 |
> | volumes | 是 | 是 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 服务 | 是 | 是 |
> | containerGroups | 是 | 是 |
> | gateways | 是 | 是 |
> | networks | 是 | 是 |
> | 机密 | 是 | 是 |
> | volumes | 是 | 是 |

## <a name="microsoftservices"></a>微软.服务

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | 否 | 否 |
> | 提供商注册/资源类型注册 | 否 | 否 |
> | rollouts | 是 | 是 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | SignalR | 是 | 是 |
> | SignalR / eventGridFilters | 否 | 否 |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | 是 | 是 |

## <a name="microsoftsoftwareplan"></a>微软.软件计划

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 混合使用优势 | 否 | 否 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | 是 | 是 |
> | 服务 | 是 | 是 |
> | jitRequests | 是 | 是 |

## <a name="microsoftspoolservice"></a>微软.拼车服务

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 注册订阅 | 否 | 否 |
> | 线轴 | 是 | 是 |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | managedInstances | 是 | 是 |
> | managedInstances / databases | 是（见[以下注释](#sqlnote)） | 是 |
> | managedInstances / databases / backupShortTermRetentionPolicies | 否 | 否 |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | 否 | 否 |
> | managedInstances / databases / vulnerabilityAssessments | 否 | 否 |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | 否 | 否 |
> | managedInstances / encryptionProtector | 否 | 否 |
> | managedInstances / keys | 否 | 否 |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | 否 | 否 |
> | managedInstances / vulnerabilityAssessments | 否 | 否 |
> | servers | 是 | 是 |
> | servers / administrators | 否 | 否 |
> | servers / communicationLinks | 否 | 否 |
> | servers/databases | 是（见[以下注释](#sqlnote)） | 是 |
> | servers / encryptionProtector | 否 | 否 |
> | servers / firewallRules | 否 | 否 |
> | servers / keys | 否 | 否 |
> | servers / restorableDroppedDatabases | 否 | 否 |
> | servers / serviceobjectives | 否 | 否 |
> | servers / tdeCertificates | 否 | 否 |
> | virtualClusters | 否 | 否 |

<a id="sqlnote" />

> [!NOTE]
> Master 数据库不支持标记，但其他数据库（包括 Azure SQL 数据仓库数据库）支持标记。 Azure SQL 数据仓库数据库必须处于活动（而非暂停）状态。

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | 是 | 是 |
> | Sql虚拟计算机组/可用性组侦听器 | 否 | 否 |
> | SqlVirtualMachines | 是 | 是 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | storageAccounts | 是 | 是 |
> | storageAccounts / blobServices | 否 | 否 |
> | storageAccounts / fileServices | 否 | 否 |
> | storageAccounts / queueServices | 否 | 否 |
> | storageAccounts / services | 否 | 否 |
> | storageAccounts / services / metricDefinitions | 否 | 否 |
> | storageAccounts / tableServices | 否 | 否 |
> | usages | 否 | 否 |

## <a name="microsoftstoragecache"></a>微软.存储缓存

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 缓存 | 是 | 是 |
> | 缓存/存储目标 | 否 | 否 |
> | 使用模型 | 否 | 否 |

## <a name="microsoftstoragereplication"></a>微软.存储复制

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 复制组 | 否 | 否 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 是 | 是 |
> | 存储同步服务/注册服务器 | 否 | 否 |
> | 存储同步服务/同步组 | 否 | 否 |
> | 存储同步服务 / 同步组 / 云终结点 | 否 | 否 |
> | 存储同步服务 / 同步组 / 服务器终结点 | 否 | 否 |
> | 存储同步服务/工作流 | 否 | 否 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 是 | 是 |
> | 存储同步服务/注册服务器 | 否 | 否 |
> | 存储同步服务/同步组 | 否 | 否 |
> | 存储同步服务 / 同步组 / 云终结点 | 否 | 否 |
> | 存储同步服务 / 同步组 / 服务器终结点 | 否 | 否 |
> | 存储同步服务/工作流 | 否 | 否 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 是 | 是 |
> | 存储同步服务/注册服务器 | 否 | 否 |
> | 存储同步服务/同步组 | 否 | 否 |
> | 存储同步服务 / 同步组 / 云终结点 | 否 | 否 |
> | 存储同步服务 / 同步组 / 服务器终结点 | 否 | 否 |
> | 存储同步服务/工作流 | 否 | 否 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | managers | 是 | 是 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | streamingjobs | 是（见下方备注） | 是 |

> [!NOTE]
> Streamingjobs 运行时无法添加标记。 停止要添加标记的资源。

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | cancel | 否 | 否 |
> | CreateSubscription | 否 | 否 |
> | enable | 否 | 否 |
> | 重命名 | 否 | 否 |
> | SubscriptionDefinitions | 否 | 否 |
> | SubscriptionOperations | 否 | 否 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | environments | 是 | 否 |
> | environments / accessPolicies | 否 | 否 |
> | environments/eventsources | 是 | 否 |
> | environments / referenceDataSets | 是 | 否 |

## <a name="microsoftvmwarecloudsimple"></a>微软.VMware云简单

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | 专用云节点 | 是 | 是 |
> | 专用云服务 | 是 | 是 |
> | virtualMachines | 是 | 是 |

## <a name="microsoftvnfmanager"></a>微软.Vnf管理器

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | devices | 是 | 是 |
> | 注册订阅 | 否 | 否 |
> | 供应商 | 否 | 否 |
> | 供应商 / 斯克乌斯 | 否 | 否 |
> | 供应商 / vnfs | 否 | 否 |
> | 文夫斯 | 是 | 是 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | 否 | 否 |
> | apiManagementAccounts / apiAcls | 否 | 否 |
> | apiManagementAccounts / apis | 否 | 否 |
> | apiManagementAccounts / apis / apiAcls | 否 | 否 |
> | apiManagementAccounts / apis / connectionAcls | 否 | 否 |
> | apiManagementAccounts / apis / connections | 否 | 否 |
> | apiManagementAccounts / apis / connections / connectionAcls | 否 | 否 |
> | apiManagementAccounts / apis / localizedDefinitions | 否 | 否 |
> | apiManagementAccounts / connectionAcls | 否 | 否 |
> | apiManagementAccounts / connections | 否 | 否 |
> | billingMeters | 否 | 否 |
> | certificates | 是 | 是 |
> | connectionGateways | 是 | 是 |
> | connections | 是 | 是 |
> | customApis | 是 | 是 |
> | deletedSites | 否 | 否 |
> | hostingEnvironments | 是 | 是 |
> | 托管环境/事件网格筛选器 | 否 | 否 |
> | hostingEnvironments / multiRolePools | 否 | 否 |
> | hostingEnvironments / workerPools | 否 | 否 |
> | publishingUsers | 否 | 否 |
> | 建议 | 否 | 否 |
> | resourceHealthMetadata | 否 | 否 |
> | runtimes | 否 | 否 |
> | serverFarms | 是 | 是 |
> | serverFarms / eventGridFilters | 否 | 否 |
> | sites | 是 | 是 |
> | sites / config  | 否 | 否 |
> | sites / eventGridFilters | 否 | 否 |
> | sites / hostNameBindings | 否 | 否 |
> | sites / networkConfig | 否 | 否 |
> | sites/premieraddons | 是 | 是 |
> | sites/slots | 是 | 是 |
> | sites / slots / eventGridFilters | 否 | 否 |
> | sites / slots / hostNameBindings | 否 | 否 |
> | sites / slots / networkConfig | 否 | 否 |
> | sourceControls | 否 | 否 |
> | 静态站点 | 是 | 是 |
> | validate | 否 | 否 |
> | verifyHostingEnvironmentVnet | 否 | 否 |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 否 | 否 |
> | diagnosticSettingsCategories | 否 | 否 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | DeviceServices | 是 | 是 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 资源类型 | 支持标记 | 在成本报表中标记 |
> | ------------- | ----------- | ----------- |
> | components | 否 | 否 |
> | componentsSummary | 否 | 否 |
> | monitorInstances | 否 | 否 |
> | monitorInstancesSummary | 否 | 否 |
> | monitors | 否 | 否 |
> | notificationSettings | 否 | 否 |

## <a name="next-steps"></a>后续步骤

若要了解如何将标记应用于资源，请参见[使用标记来组织 Azure 资源](tag-resources.md)。
