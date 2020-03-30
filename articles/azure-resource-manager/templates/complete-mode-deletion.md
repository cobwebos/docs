---
title: 完整模式删除
description: 显示资源类型如何在 Azure 资源管理器模板中进行完全模式删除。
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 5f797974212636460306c6a17869d6b8380545ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664400"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>针对完全模式部署的 Azure 资源删除

本文描述了资源类型如何在不是以完全模式部署的模板中进行删除。

标记为 **Yes** 的资源类型不在以完全模式部署的模板中时，将会删除该类型。

标记为 **No** 的资源类型不在模板中时不会自动删除；但是，如果删除了父资源，则会删除它们。 有关此行为的完整描述，请参阅 [Azure 资源管理器部署模式](deployment-modes.md)。

如果部署到[模板中的多个资源组](cross-resource-group-deployment.md)，则可以删除部署操作中指定的资源组中的资源。 辅助资源组中的资源不会被删除。

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
> - [微软.笔记本](#microsoftnotebooks)
> - [微软.网络](#microsoftnetwork)
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
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | DomainServices | 是 |
> | DomainServices / oucontainer | 否 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | supportProviders | 否 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | aadsupportcases | 否 |
> | addsservices | 否 |
> | 代理 | 否 |
> | anonymousapiusers | 否 |
> | 配置 | 否 |
> | 日志 | 否 |
> | 报表 | 否 |
> | servicehealthmetrics | 否 |
> | services | 否 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 配置 | 否 |
> | generateRecommendations | 否 |
> | metadata | 否 |
> | 建议 | 否 |
> | 禁止显示 | 否 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | actionRules | 是 |
> | alerts | 否 |
> | alertsList | 否 |
> | alertsMetaData | 否 |
> | alertsSummary | 否 |
> | alertsSummaryList | 否 |
> | 反馈 | 否 |
> | smartDetectorAlertRules | 是 |
> | smartDetectorRuntimeEnvironments | 否 |
> | smartGroups | 否 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | servers | 是 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | reportFeedback | 否 |
> | 服务 | 是 |
> | validateServiceName | 否 |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 配置存储 | 是 |
> | 配置存储/事件网格筛选器 | 否 |

## <a name="microsoftappplatform"></a>微软.AppPlatform

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | Spring | 是 |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | attestationProviders | 否 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | classicAdministrators | 否 |
> | dataAliases | 否 |
> | denyAssignments | 否 |
> | elevateAccess | 否 |
> | findOrphanRoleAssignments | 否 |
> | 锁定 | 否 |
> | 权限 | 否 |
> | policyAssignments | 否 |
> | policyDefinitions | 否 |
> | policySetDefinitions | 否 |
> | providerOperations | 否 |
> | roleAssignments | 否 |
> | 角色分配使用指标 | 否 |
> | roleDefinitions | 否 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | automationAccounts | 是 |
> | automationAccounts / configurations | 是 |
> | automationAccounts / jobs | 否 |
> | 自动化帐户 /专用端接策略 | 否 |
> | 自动化帐户/专用端点连接 | 否 |
> | 自动化账户/私人链接资源 | 否 |
> | automationAccounts / runbooks | 是 |
> | automationAccounts / softwareUpdateConfigurations | 否 |
> | automationAccounts / webhooks | 否 |

## <a name="microsoftazconfig"></a>微软.阿兹康菲格

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 配置存储 | 是 |
> | 配置存储/事件网格筛选器 | 否 |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | environments | 否 |
> | 环境/帐户 | 否 |
> | 环境/帐户/命名空间 | 否 |
> | 环境 / 帐户 / 命名空间 / 配置 | 否 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | b2cDirectories | 是 |
> | b2ctenants | 否 |

## <a name="microsoftazuredata"></a>微软.Azure数据

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 混合数据管理器 | 是 |
> | 后实例 | 是 |
> | sqlBigData 集群 | 是 |
> | sqlInstances | 是 |
> | sqlServer 注册 | 是 |
> | sqlServer 注册 / sqlServer | 否 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | registrations | 是 |
> | registrations / customerSubscriptions | 否 |
> | registrations / products | 否 |
> | verificationKeys | 否 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | batchAccounts | 是 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | billingAccounts | 否 |
> | 账单帐户/协议 | 否 |
> | 计费帐户/计费权限 | 否 |
> | 帐单帐户/帐单配置文件 | 否 |
> | 帐单帐户 / 帐单配置文件 / 计费权限 | 否 |
> | 帐单帐户 / 计费配置文件 / 计费角色分配 | 否 |
> | 账单账户/账单配置文件/计费角色定义 | 否 |
> | 帐单帐户 / 帐单配置文件 / 计费订阅 | 否 |
> | 计费帐户 /计费配置文件 / 创建计费角色分配 | 否 |
> | 账单账户/账单配置文件/客户 | 否 |
> | 账单账户/账单配置文件/说明 | 否 |
> | 帐单帐目 / 帐单配置文件 / 发票 | 否 |
> | 账单帐目 / 账单配置文件 / 发票 / 价格表 | 否 |
> | 帐单帐目 / 帐单配置文件 / 发票 / 交易记录 | 否 |
> | 帐单帐目 / 帐单配置文件 / 发票节 | 否 |
> | 帐单帐户 / 帐单配置文件 / 发票节 / 计费权限 | 否 |
> | 帐单帐户 / 帐单配置文件 / 发票节 / 计费角色分配 | 否 |
> | 账单科目 / 帐单配置文件 / 发票节 / 计费角色定义 | 否 |
> | 帐单帐户 / 帐单配置文件 / 发票节 / 计费订阅 | 否 |
> | 帐单帐目 / 帐单配置文件 / 发票节 / 创建计费角色分配 | 否 |
> | 账单账户 / 账单配置文件 / 发票节 / 启动转移 | 否 |
> | 账单账户 / 账单配置文件 / 发票节 / 产品 | 否 |
> | 账单账户 / 账单配置文件 / 发票部分 / 产品 / 转让 | 否 |
> | 账单帐户 / 帐单配置文件 / 发票节 / 产品 / 更新自动续订 | 否 |
> | 账单账户 / 帐单配置文件 / 发票节/ 交易记录 | 否 |
> | 账单账户 / 账单配置文件 / 发票部分 / 转账 | 否 |
> | 账单账户 / 计费配置文件 / 修补程序操作 | 否 |
> | 账单账户 / 账单配置文件 / 付款方式 | 否 |
> | 账单账户 /帐单配置文件/策略 | 否 |
> | 账单账户 / 账单配置文件 / 价格表 | 否 |
> | 账单账户 / 账单配置文件 / 价格表下载操作 | 否 |
> | 账单账户 / 账单配置文件 / 产品 | 否 |
> | 账单账户/账单配置文件/交易 | 否 |
> | 计费帐户/计费角色分配 | 否 |
> | 计费帐户/计费角色定义 | 否 |
> | 计费帐户/计费订阅 | 否 |
> | 帐单帐户 / 帐单订阅 / 发票 | 否 |
> | 计费帐户/ 创建计费角色分配 | 否 |
> | 计费帐户/创建发票节操作 | 否 |
> | 账单账户/客户 | 否 |
> | 计费帐户/客户/计费权限 | 否 |
> | 账单帐户/客户/计费订阅 | 否 |
> | 账单账户/客户/启动转账 | 否 |
> | 账单账户/客户/政策 | 否 |
> | 账单账户/客户/产品 | 否 |
> | 账单账户/客户/交易 | 否 |
> | 账单账户/客户/转账 | 否 |
> | 帐单帐户/部门 | 否 |
> | 帐单帐目/注册帐目 | 否 |
> | 帐单帐目/发票 | 否 |
> | 帐单科目 /发票部分 | 否 |
> | 帐单帐户 / 发票节 / 计费订阅移动操作 | 否 |
> | 帐单帐户 / 发票节 / 计费订阅 | 否 |
> | 账单账户 / 发票节 / 计费订阅 / 转账 | 否 |
> | 账单科目 / 发票节 / 提升 | 否 |
> | 账单科目 /发票节 / 启动转移 | 否 |
> | 帐单帐目 / 发票节 / 修补程序操作 | 否 |
> | 账单科目 / 发票节 / 产品移动操作 | 否 |
> | 账单科目 / 发票部分 / 产品 | 否 |
> | 账单账户 / 发票部分 / 产品 / 转移 | 否 |
> | 帐单帐户 / 发票部分 / 产品 / 更新自动续订 | 否 |
> | 帐单科目 / 发票部分 / 交易记录 | 否 |
> | 账单科目 / 发票部分 / 转账 | 否 |
> | 账单账户/信用额度 | 否 |
> | 计费帐户/修补程序操作 | 否 |
> | 账单账户/付款方式 | 否 |
> | 账单帐户/产品 | 否 |
> | 账单账户/交易 | 否 |
> | billingPeriods | 否 |
> | 计费权限 | 否 |
> | billingProperty | 否 |
> | 计费角色分配 | 否 |
> | 计费角色定义 | 否 |
> | 创建计费角色分配 | 否 |
> | departments | 否 |
> | enrollmentAccounts | 否 |
> | invoices | 否 |
> | transfers | 否 |
> | 转让/接受转移 | 否 |
> | 转移/拒绝转移 | 否 |
> | 转移/操作状态 | 否 |
> | 传输/验证传输 | 否 |
> | 验证地址 | 否 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | mapApis | 是 |
> | updateCommunicationPreference | 否 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 区块链成员 | 是 |
> | 脐带成员 | 是 |
> | 观察家 | 是 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | blueprintAssignments | 否 |
> | 蓝图分配/分配操作 | 否 |
> | 蓝图分配/操作 | 否 |
> | blueprints | 否 |
> | 蓝图/工件 | 否 |
> | 蓝图/版本 | 否 |
> | 蓝图 /版本 / 工件 | 否 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | botServices | 是 |
> | 机器人服务/频道 | 否 |
> | 机器人服务/ 连接 | 否 |
> | 语言 | 否 |
> | 模板 | 否 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | Redis | 是 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | appliedReservations | 否 |
> | 自动配额增加 | 否 |
> | 计算交换 | 否 |
> | calculatePrice | 否 |
> | 计算买入价格 | 否 |
> | catalogs | 否 |
> | commercialReservationOrders | 否 |
> | 交易所 | 否 |
> | 地点采购订单 | 否 |
> | reservationOrders | 否 |
> | 预订订单/计算退款 | 否 |
> | 预订订单/合并 | 否 |
> | 预订订单/预订 | 否 |
> | 预订单/预订/修订 | 否 |
> | 预订单/退货 | 否 |
> | 预订订单/拆分 | 否 |
> | 预订单/交换 | 否 |
> | reservations | 否 |
> | 资源提供者 | 否 |
> | 资源 | 否 |
> | validateReservationOrder | 否 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | 否 |
> | CdnWebApplicationFirewallPolicies | 是 |
> | edgenodes | 否 |
> | 配置文件 | 是 |
> | profiles/endpoints | 是 |
> | profiles / endpoints / customdomains | 否 |
> | profiles / endpoints / origins | 否 |
> | validateProbe | 否 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | certificateOrders | 是 |
> | 证书订单/证书 | 否 |
> | validateCertificateRegistrationInformation | 否 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | capabilities | 否 |
> | domainNames | 是 |
> | domainNames / capabilities | 否 |
> | domainNames / internalLoadBalancers | 否 |
> | domainNames / serviceCertificates | 否 |
> | domainNames / slots | 否 |
> | domainNames / slots / roles | 否 |
> | domainNames / slots / roles / metricDefinitions | 否 |
> | domainNames / slots / roles / metrics | 否 |
> | moveSubscriptionResources | 否 |
> | operatingSystemFamilies | 否 |
> | operatingSystems | 否 |
> | quotas | 否 |
> | resourceTypes | 否 |
> | validateSubscriptionMoveAvailability | 否 |
> | virtualMachines | 是 |
> | virtualMachines / diagnosticSettings | 否 |
> | virtualMachines / metricDefinitions | 否 |
> | virtualMachines / metrics | 否 |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | classicInfrastructureResources | 否 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | capabilities | 否 |
> | expressRouteCrossConnections | 否 |
> | expressRouteCrossConnections / peerings | 否 |
> | gatewaySupportedDevices | 否 |
> | networkSecurityGroups | 是 |
> | quotas | 否 |
> | reservedIps | 是 |
> | virtualNetworks | 是 |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | 否 |
> | virtualNetworks / virtualNetworkPeerings | 否 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | capabilities | 否 |
> | disks | 否 |
> | images | 否 |
> | osImages | 否 |
> | osPlatformImages | 否 |
> | publicImages | 否 |
> | quotas | 否 |
> | storageAccounts | 是 |
> | storageAccounts / blobServices | 否 |
> | storageAccounts / fileServices | 否 |
> | storageAccounts / metricDefinitions | 否 |
> | storageAccounts / metrics | 否 |
> | storageAccounts / queueServices | 否 |
> | storageAccounts / services | 否 |
> | storageAccounts / services / diagnosticSettings | 否 |
> | storageAccounts / services / metricDefinitions | 否 |
> | storageAccounts / services / metrics | 否 |
> | storageAccounts / tableServices | 否 |
> | storageAccounts / vmImages | 否 |
> | vmImages | 否 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | accounts | 是 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | RateCard | 否 |
> | UsageAggregates | 否 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | availabilitySets | 是 |
> | diskEncryptionSets | 是 |
> | disks | 是 |
> | galleries | 是 |
> | galleries / applications | 否 |
> | galleries / applications / versions | 否 |
> | galleries/images | 否 |
> | galleries/images/versions | 否 |
> | hostGroups | 是 |
> | hostGroups / hosts | 是 |
> | images | 是 |
> | proximityPlacementGroups | 是 |
> | restorePointCollections | 是 |
> | restorePointCollections / restorePoints | 否 |
> | sharedVMImages | 是 |
> | sharedVMImages / versions | 否 |
> | snapshots | 是 |
> | virtualMachines | 是 |
> | virtualMachines / extensions | 是 |
> | virtualMachines / metricDefinitions | 否 |
> | virtualMachineScaleSets | 是 |
> | virtualMachineScaleSets / extensions | 否 |
> | virtualMachineScaleSets / networkInterfaces | 否 |
> | virtualMachineScaleSets / publicIPAddresses | 否 |
> | virtualMachineScaleSets / virtualMachines | 否 |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | 否 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | AggregatedCost | 否 |
> | 余额 | 否 |
> | 预算 | 否 |
> | Charges | 否 |
> | CostTags | 否 |
> | credits | 否 |
> | 活动 | 否 |
> | 预测 | 否 |
> | lots | 否 |
> | 市场 | 否 |
> | Pricesheets | 否 |
> | products | 否 |
> | ReservationDetails | 否 |
> | ReservationRecommendations | 否 |
> | ReservationSummaries | 否 |
> | ReservationTransactions | 否 |
> | Tags | 否 |
> | tenants | 否 |
> | 术语 | 否 |
> | UsageDetails | 否 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | containerGroups | 是 |
> | serviceAssociationLinks | 否 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | registries | 是 |
> | registries / builds | 否 |
> | registries / builds / cancel | 否 |
> | registries / builds / getLogLink | 否 |
> | registries / buildTasks | 是 |
> | registries / buildTasks / steps | 否 |
> | registries / eventGridFilters | 否 |
> | registries / generateCredentials | 否 |
> | registries / getBuildSourceUploadUrl | 否 |
> | registries / GetCredentials | 否 |
> | registries / importImage | 否 |
> | 注册表 / 私有端接连接 Proxies | 否 |
> | 注册表 / 私有端接连接Proxies / 验证 | 否 |
> | 注册表/专用端点连接 | 否 |
> | 注册/ 私人链接资源 | 否 |
> | registries / queueBuild | 否 |
> | registries / regenerateCredential | 否 |
> | registries / regenerateCredentials | 否 |
> | registries/replications | 是 |
> | registries / runs | 否 |
> | registries / runs / cancel | 否 |
> | registries / scheduleRun | 否 |
> | registries / scopeMaps | 否 |
> | registries / taskRuns | 是 |
> | registries/tasks | 是 |
> | registries / tokens | 否 |
> | registries / updatePolicies | 否 |
> | registries/webhooks | 是 |
> | registries / webhooks / getCallbackConfig | 否 |
> | registries / webhooks / ping | 否 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | containerServices | 是 |
> | managedClusters | 是 |
> | openShiftManagedClusters | 是 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | accounts | 是 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 警报 | 否 |
> | BillingAccounts | 否 |
> | 预算 | 否 |
> | 云连接器 | 否 |
> | 连接器 | 是 |
> | Departments | 否 |
> | 维度 | 否 |
> | EnrollmentAccounts | 否 |
> | 出口 | 否 |
> | 外部结算帐户 | 否 |
> | 外部结算帐户/警报 | 否 |
> | 外部计费帐户 / 维度 | 否 |
> | 外部结算账户/预测 | 否 |
> | 外部计费帐户/查询 | 否 |
> | 外部订阅 | 否 |
> | 外部订阅/警报 | 否 |
> | 外部订阅/维度 | 否 |
> | 外部订阅/预测 | 否 |
> | 外部订阅/查询 | 否 |
> | 预测 | 否 |
> | 查询 | 否 |
> | register | 否 |
> | Reportconfigs | 否 |
> | 报表 | 否 |
> | 设置 | 否 |
> | 显示回法规则 | 否 |
> | 视图 | 否 |

## <a name="microsoftcustomerlockbox"></a>微软.客户锁定箱

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 请求 | 否 |

## <a name="microsoftcustomproviders"></a>微软.自定义提供程序

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 关联 | 否 |
> | 资源提供者 | 是 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | jobs | 是 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | 是 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | workspaces | 是 |
> | 工作区/数据库工作区 | 否 |
> | 工作区/存储加密 | 否 |
> | 工作区/虚拟网络对等互连 | 否 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | catalogs | 是 |
> | 数据目录 | 是 |
> | 数据目录/数据源 | 否 |
> | 数据目录 / 数据源 / 扫描 | 否 |
> | 数据目录 / 数据源 / 扫描 / 数据集 | 否 |
> | 数据目录 / 数据源 / 扫描 / 触发器 | 否 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | dataFactories | 是 |
> | dataFactories / diagnosticSettings | 否 |
> | dataFactories / metricDefinitions | 否 |
> | dataFactorySchema | 否 |
> | factories | 是 |
> | factories / integrationRuntimes | 否 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | accounts | 是 |
> | 帐户/数据湖存储帐户 | 否 |
> | 帐户/存储帐户 | 否 |
> | 帐户 / 存储帐户 / 容器 | 否 |
> | 账户/转账分析单位 | 否 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | accounts | 是 |
> | 帐户/事件网格筛选器 | 否 |
> | 帐户/防火墙规则 | 否 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | services | 是 |
> | services/projects | 是 |

## <a name="microsoftdatashare"></a>微软.数据共享

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | accounts | 是 |
> | 账户/股份 | 否 |
> | 帐户/ 共享 / 数据集 | 否 |
> | 账户/ 股票 / 邀请 | 否 |
> | 帐户/共享 / 提供商共享订阅 | 否 |
> | 帐户/ 共享 / 同步设置 | 否 |
> | 帐户/共享订阅 | 否 |
> | 帐户 / 共享订阅 / 使用者来源数据集组 | 否 |
> | 帐户/共享订阅/数据集映射 | 否 |
> | 帐户 / 共享订阅 / 触发器 | 否 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | servers | 是 |
> | servers / advisors | 否 |
> | servers / keys | 否 |
> | servers / privateEndpointConnectionProxies | 否 |
> | servers / privateEndpointConnections | 否 |
> | servers / privateLinkResources | 否 |
> | servers / queryTexts | 否 |
> | servers / recoverableServers | 否 |
> | servers / topQueryStatistics | 否 |
> | servers / virtualNetworkRules | 否 |
> | servers / waitStatistics | 否 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | servers | 是 |
> | servers / advisors | 否 |
> | servers / keys | 否 |
> | servers / privateEndpointConnectionProxies | 否 |
> | servers / privateEndpointConnections | 否 |
> | servers / privateLinkResources | 否 |
> | servers / queryTexts | 否 |
> | servers / recoverableServers | 否 |
> | servers / topQueryStatistics | 否 |
> | servers / virtualNetworkRules | 否 |
> | servers / waitStatistics | 否 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | serverGroups | 是 |
> | servers | 是 |
> | servers / advisors | 否 |
> | servers / keys | 否 |
> | servers / privateEndpointConnectionProxies | 否 |
> | servers / privateEndpointConnections | 否 |
> | servers / privateLinkResources | 否 |
> | servers / queryTexts | 否 |
> | servers / recoverableServers | 否 |
> | servers / topQueryStatistics | 否 |
> | servers / virtualNetworkRules | 否 |
> | servers / waitStatistics | 否 |
> | serversv2 | 是 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 工件来源 | 是 |
> | rollouts | 是 |
> | 服务拓扑 | 是 |
> | 服务拓扑/服务 | 是 |
> | 服务机构 / 服务 / 服务单位 | 是 |
> | steps | 是 |

## <a name="microsoftdesktopvirtualization"></a>微软.桌面虚拟化

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 应用程序组 | 是 |
> | 应用程序组/应用程序 | 否 |
> | 应用程序组/桌面 | 否 |
> | 应用程序组/启动菜单项 | 否 |
> | 主机池 | 是 |
> | 主机池/会话主机 | 否 |
> | 主机池/会话主机/用户会话 | 否 |
> | 主机池/用户会话 | 否 |
> | workspaces | 是 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | ElasticPools | 是 |
> | ElasticPools / IotHubTenants | 是 |
> | 弹性池 / IotHub租户 / 安全设置 | 否 |
> | IotHubs | 是 |
> | IotHubs / eventGridFilters | 否 |
> | IotHubs / 安全设置 | 否 |
> | ProvisioningServices | 是 |
> | usages | 否 |

## <a name="microsoftdevops"></a>微软.DevOps

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 管道 | 是 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 控制器 | 是 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | labcenters | 是 |
> | labs | 是 |
> | 实验室/环境 | 是 |
> | 实验室/服务运行者 | 是 |
> | 实验室/虚拟机 | 是 |
> | schedules | 是 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | databaseAccountNames | 否 |
> | databaseAccounts | 是 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | domains | 是 |
> | 域 /域所有权标识符 | 否 |
> | generateSsoRequest | 否 |
> | topLevelDomains | 否 |
> | validateDomainRegistrationInformation | 否 |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | lcsprojects | 否 |
> | lcs项目/云部署 | 否 |
> | lcs项目/ 连接器 | 否 |

## <a name="microsoftenterpriseknowledgegraph"></a>微软.企业知识图

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | services | 是 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | domains | 是 |
> | domains / topics | 否 |
> | eventSubscriptions | 否 |
> | extensionTopics | 否 |
> | 合作伙伴命名空间 | 是 |
> | 合作伙伴命名空间/ 事件通道 | 否 |
> | 合作伙伴注册 | 是 |
> | 合作伙伴主题 | 是 |
> | 合作伙伴主题/活动订阅 | 否 |
> | 系统主题 | 是 |
> | 系统主题/事件订阅 | 否 |
> | topics | 是 |
> | topicTypes | 否 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | clusters | 是 |
> | namespaces | 是 |
> | namespaces / authorizationrules | 否 |
> | namespaces / disasterrecoveryconfigs | 否 |
> | namespaces / eventhubs | 否 |
> | namespaces / eventhubs / authorizationrules | 否 |
> | namespaces / eventhubs / consumergroups | 否 |
> | namespaces / networkrulesets | 否 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | features | 否 |
> | providers | 否 |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | enroll | 否 |
> | galleryitems | 否 |
> | generateartifactaccessuri | 否 |
> | myareas | 否 |
> | 我的区域/地区 | 否 |
> | 我的区域 / 区域 / 区域 | 否 |
> | 我的区域 / 区域 / 区域 / 画廊项目 | 否 |
> | 我的区域 / 区域 / 画廊项目 | 否 |
> | 我的区域 / 画廊项目 | 否 |
> | register | 否 |
> | 资源 | 否 |
> | retrieveresourcesbyid | 否 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | accounts | 是 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 自动管理帐户 | 是 |
> | autoManagedVmConfigurationProfiles | 是 |
> | configurationProfileAssignments | 否 |
> | guestConfigurationAssignments | 否 |
> | software | 否 |
> | softwareUpdateProfile | 否 |
> | softwareUpdates | 否 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | hanainstances | 是 |
> | 皂显示器 | 是 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 专用的 HSM | 是 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | clusters | 是 |
> | clusters/applications | 否 |

## <a name="microsofthealthcareapis"></a>微软.医疗保健Apis

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | services | 是 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 机器 | 是 |
> | 机器/扩展 | 是 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 数据管理器 | 是 |

## <a name="microsofthydra"></a>微软.海德拉

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | components | 是 |
> | 网络范围 | 是 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | jobs | 是 |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | diagnosticSettings | 否 |
> | diagnosticSettingsCategories | 否 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | appTemplates | 否 |
> | IoTApps | 是 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 图形 | 是 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | deletedVaults | 否 |
> | hsmPools | 是 |
> | vaults | 是 |
> | vaults / accessPolicies | 否 |
> | vaults / eventGridFilters | 否 |
> | vaults / secrets | 否 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | clusters | 是 |
> | clusters / attacheddatabaseconfigurations | 否 |
> | clusters / databases | 否 |
> | clusters / databases / dataconnections | 否 |
> | clusters / databases / eventhubconnections | 否 |
> | 群集/数据库/委托分配 | 否 |
> | 群集/主体分配 | 否 |
> | clusters / sharedidentities | 否 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | labaccounts | 是 |
> | users | 否 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | hostingEnvironments | 是 |
> | integrationAccounts | 是 |
> | integrationServiceEnvironments | 是 |
> | integrationServiceEnvironments / managedApis | 是 |
> | isolatedEnvironments | 是 |
> | workflows | 是 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | commitmentPlans | 是 |
> | webServices | 是 |
> | 工作区 | 是 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | workspaces | 是 |
> | 工作区/计算 | 否 |
> | 工作区/事件网格筛选器 | 否 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 标识 | 否 |
> | userAssignedIdentities | 是 |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 市场注册定义 | 否 |
> | 注册分配 | 否 |
> | 注册定义 | 否 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | getEntities | 否 |
> | managementGroups | 否 |
> | 管理组/设置 | 否 |
> | 资源 | 否 |
> | startTenantBackfill | 否 |
> | tenantBackfillStatus | 否 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | accounts | 是 |
> | 帐户/事件网格筛选器 | 否 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | offers | 否 |
> | offerTypes | 否 |
> | 优惠类型/发布商 | 否 |
> | 产品类型 / 发布商 / 优惠 | 否 |
> | 优惠类型 / 发布商 / 优惠 / 计划 | 否 |
> | 优惠类型 / 发布商 / 优惠 / 计划 / 协议 | 否 |
> | 报价类型 / 发布商 / 优惠 / 计划 / 配置 | 否 |
> | 报价类型 / 发布商 / 优惠 / 计划 / 配置 / 导入图像 | 否 |
> | privategalleryitems | 否 |
> | 专用存储客户端 | 否 |
> | products | 否 |
> | publishers | 否 |
> | 出版商/优惠 | 否 |
> | 出版商/ 优惠 / 修订 | 否 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | classicDevServices | 是 |
> | updateCommunicationPreference | 否 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 协议 | 否 |
> | offertypes | 否 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | mediaservices | 是 |
> | mediaservices / accountFilters | 否 |
> | mediaservices / assets | 否 |
> | mediaservices / assets / assetFilters | 否 |
> | mediaservices / contentKeyPolicies | 否 |
> | mediaservices / eventGridFilters | 否 |
> | mediaservices / liveEventOperations | 否 |
> | mediaservices / liveEvents | 是 |
> | mediaservices / liveEvents / liveOutputs | 否 |
> | mediaservices / liveOutputOperations | 否 |
> | mediaservices / mediaGraphs | 否 |
> | mediaservices / streamingEndpointOperations | 否 |
> | mediaservices / streamingEndpoints | 是 |
> | mediaservices / streamingLocators | 否 |
> | mediaservices / streamingPolicies | 否 |
> | mediaservices / transforms | 否 |
> | mediaservices / transforms / jobs | 否 |

## <a name="microsoftmicroservices4spring"></a>微软.微服务4春天

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 应用群集 | 是 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 评估项目 | 是 |
> | migrateprojects | 是 |
> | 移动集合 | 是 |
> | projects | 是 |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 全息图形广播帐户 | 是 |
> | 对象理解帐户 | 是 |
> | 远程渲染帐户 | 是 |
> | 空间锚定帐户 | 是 |
> | 表面重建帐户 | 是 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 净应用账户 | 是 |
> | 净应用账户/容量池 | 是 |
> | 净应用账户 / 容量池 / 卷 | 是 |
> | 净应用账户 / 容量池 / 卷 / 装载目标 | 是 |
> | 净应用账户 / 容量池 / 卷 / 快照 | 是 |

## <a name="microsoftnotebooks"></a>微软.笔记本

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 笔记本 | 否 |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | applicationGateways | 是 |
> | applicationGatewayWebApplicationFirewallPolicies | 是 |
> | applicationSecurityGroups | 是 |
> | azureFirewallFqdnTags | 否 |
> | azureFirewalls | 是 |
> | bastionHosts | 是 |
> | bgpServiceCommunities | 否 |
> | connections | 是 |
> | ddosCustomPolicies | 是 |
> | ddosProtectionPlans | 是 |
> | dnsOperationStatuses | 否 |
> | dnszones | 是 |
> | dnszones / A | 否 |
> | dnszones / AAAA | 否 |
> | dnszones / all | 否 |
> | dnszones / CAA | 否 |
> | dnszones / CNAME | 否 |
> | dnszones / MX | 否 |
> | dnszones / NS | 否 |
> | dnszones / PTR | 否 |
> | dnszones / recordsets | 否 |
> | dnszones / SOA | 否 |
> | dnszones / SRV | 否 |
> | dnszones / TXT | 否 |
> | expressRouteCircuits | 是 |
> | expressRouteCrossConnections | 是 |
> | expressRouteGateways | 是 |
> | expressRoutePorts | 是 |
> | expressRouteServiceProviders | 否 |
> | firewallPolicies | 是 |
> | frontdoors | 是 |
> | frontdoorWebApplicationFirewallManagedRuleSets | 否 |
> | frontdoorWebApplicationFirewallPolicies | 是 |
> | getDnsResourceReference | 否 |
> | internalNotify | 否 |
> | loadBalancers | 是 |
> | localNetworkGateways | 是 |
> | natGateways | 是 |
> | networkIntentPolicies | 是 |
> | networkInterfaces | 是 |
> | networkProfiles | 是 |
> | networkSecurityGroups | 是 |
> | networkWatchers | 是 |
> | networkWatchers / connectionMonitors | 是 |
> | networkWatchers / lenses | 是 |
> | networkWatchers / pingMeshes | 是 |
> | p2sVpnGateways | 是 |
> | privateDnsOperationStatuses | 否 |
> | privateDnsZones | 是 |
> | privateDnsZones / A | 否 |
> | privateDnsZones / AAAA | 否 |
> | privateDnsZones / all | 否 |
> | privateDnsZones / CNAME | 否 |
> | privateDnsZones / MX | 否 |
> | privateDnsZones / PTR | 否 |
> | privateDnsZones / SOA | 否 |
> | privateDnsZones / SRV | 否 |
> | privateDnsZones / TXT | 否 |
> | privateDnsZones / virtualNetworkLinks | 是 |
> | privateEndpoints | 是 |
> | privateLinkServices | 是 |
> | publicIPAddresses | 是 |
> | publicIPPrefixes | 是 |
> | routeFilters | 是 |
> | routeTables | 是 |
> | serviceEndpointPolicies | 是 |
> | trafficManagerGeographicHierarchies | 否 |
> | trafficmanagerprofiles | 是 |
> | trafficmanagerprofiles / heatMaps | 否 |
> | trafficManagerUserMetricsKeys | 否 |
> | virtualHubs | 是 |
> | virtualNetworkGateways | 是 |
> | virtualNetworks | 是 |
> | virtualNetworkTaps | 是 |
> | virtualWans | 是 |
> | vpnGateways | 是 |
> | vpnSites | 是 |
> | webApplicationFirewallPolicies | 是 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | namespaces | 是 |
> | namespaces / notificationHubs | 是 |

## <a name="microsoftobjectstore"></a>微软.对象商店

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | os 命名空间 | 是 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 超级网站 | 是 |
> | 导入网站 | 是 |
> | 服务器网站 | 是 |
> | VMwareSites | 是 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | clusters | 是 |
> | linkTargets | 否 |
> | storageInsightConfigs | 否 |
> | workspaces | 是 |
> | 工作区/数据导出 | 否 |
> | workspaces / dataSources | 否 |
> | workspaces / linkedServices | 否 |
> | 工作区/专用端接Proxie | 否 |
> | 工作区/专用端点连接 | 否 |
> | 工作区/专用链接资源 | 否 |
> | workspaces / query | 否 |
> | 工作区/作用域私人链接 Proxies | 否 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | managementassociations | 否 |
> | managementconfigurations | 是 |
> | solutions | 是 |
> | 视图 | 是 |

## <a name="microsoftpeering"></a>微软.对等

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 传统对等互连 | 否 |
> | 对等Asns | 否 |
> | 对等互连 | 是 |
> | 对等服务国家 | 否 |
> | 对等服务提供商 | 否 |
> | 对等服务 | 是 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | policyEvents | 否 |
> | policyMetadata | 否 |
> | policyStates | 否 |
> | policyTrackedResources | 否 |
> | remediations | 否 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | consoles | 否 |
> | dashboards | 是 |
> | userSettings | 否 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | workspaceCollections | 是 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | capacities | 是 |

## <a name="microsoftprojectbabylon"></a>微软.项目巴比隆

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | accounts | 是 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | backupProtectedItems | 否 |
> | vaults | 是 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | namespaces | 是 |
> | namespaces / authorizationrules | 否 |
> | namespaces / hybridconnections | 否 |
> | namespaces / hybridconnections / authorizationrules | 否 |
> | namespaces / wcfrelays | 否 |
> | namespaces / wcfrelays / authorizationrules | 否 |

## <a name="microsoftremoteapp"></a>微软.远程应用程序

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | accounts | 否 |
> | 集合 | 是 |
> | 集合/应用程序 | 否 |
> | 集合/安全主体 | 否 |
> | 模板图像 | 否 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 查询 | 是 |
> | resourceChangeDetails | 否 |
> | resourceChanges | 否 |
> | 资源 | 否 |
> | resourcesHistory | 否 |
> | subscriptionsStatus | 否 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | availabilityStatuses | 否 |
> | childAvailabilityStatuses | 否 |
> | childResources | 否 |
> | 新兴问题 | 否 |
> | 活动 | 否 |
> | impactedResources | 否 |
> | metadata | 否 |
> | 通知 | 否 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | deployments | 否 |
> | deployments / operations | 否 |
> | deploymentScripts | 是 |
> | deploymentScripts / logs | 否 |
> | 链接 | 否 |
> | notifyResourceJobs | 否 |
> | providers | 否 |
> | resourceGroups | 否 |
> | subscriptions | 否 |
> | tenants | 否 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 服务 | 是 |
> | saasresources | 否 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | resourceHealthMetadata | 否 |
> | searchServices | 是 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | 否 |
> | advancedThreatProtectionSettings | 否 |
> | alerts | 否 |
> | allowedConnections | 否 |
> | applicationWhitelistings | 否 |
> | assessmentMetadata | 否 |
> | assessments | 否 |
> | autoDismissAlertsRules | 否 |
> | automations | 是 |
> | AutoProvisioningSettings | 否 |
> | Compliances | 否 |
> | dataCollectionAgents | 否 |
> | deviceSecurityGroups | 否 |
> | discoveredSecuritySolutions | 否 |
> | externalSecuritySolutions | 否 |
> | InformationProtectionPolicies | 否 |
> | iotSecuritySolutions | 是 |
> | iotSecuritySolutions / analyticsModels | 否 |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | 否 |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | 否 |
> | jitNetworkAccessPolicies | 否 |
> | networkData | 否 |
> | 策略 | 否 |
> | pricings | 否 |
> | regulatoryComplianceStandards | 否 |
> | regulatoryComplianceStandards / regulatoryComplianceControls | 否 |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | 否 |
> | securityContacts | 否 |
> | securitySolutions | 否 |
> | securitySolutionsReferenceData | 否 |
> | securityStatuses | 否 |
> | securityStatusesSummaries | 否 |
> | serverVulnerabilityAssessments | 否 |
> | 设置 | 否 |
> | subAssessments | 否 |
> | 任务 | 否 |
> | topologies | 否 |
> | workspaceSettings | 否 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | diagnosticSettings | 否 |
> | diagnosticSettingsCategories | 否 |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | aggregations | 否 |
> | 警报规则 | 否 |
> | 警报规则模板 | 否 |
> | 书签 | 否 |
> | cases | 否 |
> | 数据连接器 | 否 |
> | 数据连接器检查要求 | 否 |
> | 实体 | 否 |
> | 实体查询 | 否 |
> | 事件 | 否 |
> | 办公室同意 | 否 |
> | 设置 | 否 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | namespaces | 是 |
> | namespaces / authorizationrules | 否 |
> | namespaces / disasterrecoveryconfigs | 否 |
> | namespaces / eventgridfilters | 否 |
> | namespaces / networkrulesets | 否 |
> | namespaces / queues | 否 |
> | namespaces / queues / authorizationrules | 否 |
> | namespaces / topics | 否 |
> | namespaces / topics / authorizationrules | 否 |
> | namespaces / topics / subscriptions | 否 |
> | namespaces / topics / subscriptions / rules | 否 |
> | premiumMessagingRegions | 否 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 服务 | 是 |
> | clusters | 是 |
> | clusters/applications | 否 |
> | containerGroups | 是 |
> | containerGroupSets | 是 |
> | edgeclusters | 是 |
> | edgeclusters / applications | 否 |
> | managedclusters | 是 |
> | 托管群集/节点类型 | 否 |
> | networks | 是 |
> | secretstores | 是 |
> | secretstores / certificates | 否 |
> | secretstores / secrets | 否 |
> | volumes | 是 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 服务 | 是 |
> | containerGroups | 是 |
> | gateways | 是 |
> | networks | 是 |
> | 机密 | 是 |
> | volumes | 是 |

## <a name="microsoftservices"></a>微软.服务

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | providerRegistrations | 否 |
> | 提供商注册/资源类型注册 | 否 |
> | rollouts | 是 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | SignalR | 是 |
> | SignalR / eventGridFilters | 否 |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | SiteRecoveryVault | 是 |

## <a name="microsoftsoftwareplan"></a>微软.软件计划

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 混合使用优势 | 否 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | applicationDefinitions | 是 |
> | 服务 | 是 |
> | jitRequests | 是 |

## <a name="microsoftspoolservice"></a>微软.拼车服务

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 注册订阅 | 否 |
> | 线轴 | 是 |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | managedInstances | 是 |
> | managedInstances / databases | 是 |
> | managedInstances / databases / backupShortTermRetentionPolicies | 否 |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | 否 |
> | managedInstances / databases / vulnerabilityAssessments | 否 |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | 否 |
> | managedInstances / encryptionProtector | 否 |
> | managedInstances / keys | 否 |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | 否 |
> | managedInstances / vulnerabilityAssessments | 否 |
> | servers | 是 |
> | servers / administrators | 否 |
> | servers / communicationLinks | 否 |
> | servers/databases | 是 |
> | servers / encryptionProtector | 否 |
> | servers / firewallRules | 否 |
> | servers / keys | 否 |
> | servers / restorableDroppedDatabases | 否 |
> | servers / serviceobjectives | 否 |
> | servers / tdeCertificates | 否 |
> | virtualClusters | 否 |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | 是 |
> | Sql虚拟计算机组/可用性组侦听器 | 否 |
> | SqlVirtualMachines | 是 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | storageAccounts | 是 |
> | storageAccounts / blobServices | 否 |
> | storageAccounts / fileServices | 否 |
> | storageAccounts / queueServices | 否 |
> | storageAccounts / services | 否 |
> | storageAccounts / services / metricDefinitions | 否 |
> | storageAccounts / tableServices | 否 |
> | usages | 否 |

## <a name="microsoftstoragecache"></a>微软.存储缓存

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 缓存 | 是 |
> | 缓存/存储目标 | 否 |
> | 使用模型 | 否 |

## <a name="microsoftstoragereplication"></a>微软.存储复制

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 复制组 | 否 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | storageSyncServices | 是 |
> | 存储同步服务/注册服务器 | 否 |
> | 存储同步服务/同步组 | 否 |
> | 存储同步服务 / 同步组 / 云终结点 | 否 |
> | 存储同步服务 / 同步组 / 服务器终结点 | 否 |
> | 存储同步服务/工作流 | 否 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | storageSyncServices | 是 |
> | 存储同步服务/注册服务器 | 否 |
> | 存储同步服务/同步组 | 否 |
> | 存储同步服务 / 同步组 / 云终结点 | 否 |
> | 存储同步服务 / 同步组 / 服务器终结点 | 否 |
> | 存储同步服务/工作流 | 否 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | storageSyncServices | 是 |
> | 存储同步服务/注册服务器 | 否 |
> | 存储同步服务/同步组 | 否 |
> | 存储同步服务 / 同步组 / 云终结点 | 否 |
> | 存储同步服务 / 同步组 / 服务器终结点 | 否 |
> | 存储同步服务/工作流 | 否 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | managers | 是 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | streamingjobs | 是 |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | cancel | 否 |
> | CreateSubscription | 否 |
> | enable | 否 |
> | 重命名 | 否 |
> | SubscriptionDefinitions | 否 |
> | SubscriptionOperations | 否 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | environments | 是 |
> | environments / accessPolicies | 否 |
> | environments/eventsources | 是 |
> | environments / referenceDataSets | 是 |

## <a name="microsoftvmwarecloudsimple"></a>微软.VMware云简单

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | 专用云节点 | 是 |
> | 专用云服务 | 是 |
> | virtualMachines | 是 |

## <a name="microsoftvnfmanager"></a>微软.Vnf管理器

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | devices | 是 |
> | 注册订阅 | 否 |
> | 供应商 | 否 |
> | 供应商 / 斯克乌斯 | 否 |
> | 供应商 / vnfs | 否 |
> | 文夫斯 | 是 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | apiManagementAccounts | 否 |
> | apiManagementAccounts / apiAcls | 否 |
> | apiManagementAccounts / apis | 否 |
> | apiManagementAccounts / apis / apiAcls | 否 |
> | apiManagementAccounts / apis / connectionAcls | 否 |
> | apiManagementAccounts / apis / connections | 否 |
> | apiManagementAccounts / apis / connections / connectionAcls | 否 |
> | apiManagementAccounts / apis / localizedDefinitions | 否 |
> | apiManagementAccounts / connectionAcls | 否 |
> | apiManagementAccounts / connections | 否 |
> | billingMeters | 否 |
> | certificates | 是 |
> | connectionGateways | 是 |
> | connections | 是 |
> | customApis | 是 |
> | deletedSites | 否 |
> | hostingEnvironments | 是 |
> | 托管环境/事件网格筛选器 | 否 |
> | hostingEnvironments / multiRolePools | 否 |
> | hostingEnvironments / workerPools | 否 |
> | publishingUsers | 否 |
> | 建议 | 否 |
> | resourceHealthMetadata | 否 |
> | runtimes | 否 |
> | serverFarms | 是 |
> | serverFarms / eventGridFilters | 否 |
> | sites | 是 |
> | sites/config  | 否 |
> | sites / eventGridFilters | 否 |
> | sites / hostNameBindings | 否 |
> | sites / networkConfig | 否 |
> | sites/premieraddons | 是 |
> | sites/slots | 是 |
> | sites / slots / eventGridFilters | 否 |
> | sites / slots / hostNameBindings | 否 |
> | sites / slots / networkConfig | 否 |
> | sourceControls | 否 |
> | 静态站点 | 是 |
> | validate | 否 |
> | verifyHostingEnvironmentVnet | 否 |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | diagnosticSettings | 否 |
> | diagnosticSettingsCategories | 否 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | DeviceServices | 是 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 资源类型 | 完整模式删除 |
> | ------------- | ----------- |
> | components | 否 |
> | componentsSummary | 否 |
> | monitorInstances | 否 |
> | monitorInstancesSummary | 否 |
> | monitors | 否 |
> | notificationSettings | 否 |

## <a name="next-steps"></a>后续步骤

若要以逗号分隔值文件的形式获取同一数据，请下载 [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv)。
