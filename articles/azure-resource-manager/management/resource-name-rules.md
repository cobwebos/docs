---
title: 资源命名限制
description: 说明 Azure 资源的命名规则和限制。
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: a4c3bf58bf5e84051b1e21863d2d581af5fa9f23
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447531"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Azure 资源的命名规则和限制

本文汇总了 Azure 资源的命名规则和限制。 有关如何命名资源的建议，请参阅[建议的命名和标记约定](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)。

本文按资源提供程序命名空间列出了资源。 有关资源提供程序如何匹配 Azure 服务的列表，请参阅[适用于 Azure 服务的资源提供程序](azure-services-resource-providers.md)。

除非“有效字符”列中另有专门的说明，否则资源名称不区分大小写。

在下表中，术语“字母数字”是指：

* **a** 至 **z**（小写字母）
* **A** 至 **Z**（大写字母）
* **0** 至 **9**（数字）

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | servers | 资源组 | 3-63 | 小写字母和数字。<br><br>以小写字母开头。 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | 服务 | 全局 | 1-50 | 字母数字和连字符。<br><br>以字母开头，以字母数字结尾。 |
> | service / apis | 服务 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / apis / issues | api | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / apis / issues / attachments | 问题 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / apis / issues / comments | 问题 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / apis / operations | api | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / apis / operations / tags | operation | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / apis / releases | api | 1-80 | 字母数字、下划线和连字符。<br><br>以字母数字或下划线开头和结尾。 |
> | service / apis / schemas | api | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / apis / tagDescriptions | api | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / apis / tags | api | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / api-version-sets | 服务 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / authorizationServers | 服务 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / backends | 服务 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / certificates | 服务 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / diagnostics | 服务 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / groups | 服务 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / groups / users | group | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / identityProviders | 服务 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / loggers | 服务 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / notifications | 服务 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / notifications / recipientEmails | 通知 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / openidConnectProviders | 服务 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / policies | 服务 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / products | 服务 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / products / apis | product | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / products / groups | product | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / products / tags | product | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / properties | 服务 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / subscriptions | 服务 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / tags | 服务 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / templates | 服务 | 1-256 | 不能使用：<br> `*#&+:<>?` |
> | service / users | 服务 | 1-256 | 不得使用：<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | configurationStores | 资源组 | 5-50 | 字母数字、下划线字符和连字符。 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | 锁定 | 分配范围 | 1-90 | 字母数字、句点、下划线、连字符和括号。<br><br>不能以句点结尾。 |
> | policyAssignments | 分配范围 | 1-128 显示名称<br><br>1-64 资源名称 | 显示名称可以包含任何字符。<br><br>资源名称不能包含 `%`，且不能以句点或空格结尾。 |
> | policyDefinitions | 定义范围 | 1-128 显示名称<br><br>1-64 资源名称 | 显示名称可以包含任何字符。<br><br>资源名称不能包含 `%`，且不能以句点或空格结尾。 |
> | policySetDefinitions | 定义范围 | 1-128 显示名称<br><br>1-64 资源名称<br><br>1-24 管理组范围内的资源名称 | 显示名称可以包含任何字符。<br><br>资源名称不能包含 `%`，且不能以句点或空格结尾。  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | automationAccounts | 资源组 | 6-50 | 字母数字和连字符。<br><br>以字母开头，以字母数字结尾。 |
> | automationAccounts / certificates | 自动化帐户 | 1-128 | 不能使用：<br> `<>*%&:\?.+/` <br><br>不能以空格结尾。  |
> | automationAccounts / connections | 自动化帐户 | 1-128 | 不能使用：<br> `<>*%&:\?.+/` <br><br>不能以空格结尾。 |
> | automationAccounts / credentials | 自动化帐户 | 1-128 | 不能使用：<br> `<>*%&:\?.+/` <br><br>不能以空格结尾。 |
> | automationAccounts / runbooks | 自动化帐户 | 1-63 | 字母数字、下划线和连字符。<br><br>以字母开头。  |
> | automationAccounts / schedules | 自动化帐户 | 1-128 | 不能使用：<br> `<>*%&:\?.+/` <br><br>不能以空格结尾。 |
> | automationAccounts / variables | 自动化帐户 | 1-128 | 不能使用：<br> `<>*%&:\?.+/` <br><br>不能以空格结尾。 |
> | automationAccounts / watchers | 自动化帐户 | 1-63 |  字母数字、下划线和连字符。<br><br>以字母开头。 |
> | automationAccounts / webhooks | 自动化帐户 | 1-128 | 不能使用：<br> `<>*%&:\?.+/` <br><br>不能以空格结尾。 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | batchAccounts | 区域 | 3-24 | 小写字母和数字。 |
> | batchAccounts / applications | Batch 帐户 | 1-64 | 字母数字、下划线和连字符。 |
> | batchAccounts / certificates | Batch 帐户 | 5-45 | 字母数字、下划线和连字符。 |
> | batchAccounts / pools | Batch 帐户 | 1-64 | 字母数字、下划线字符和连字符。 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | blockchainMembers | 全局 | 2-20 | 小写字母和数字。<br><br>以小写字母开头。 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | botServices | 全局 | 2-64 |  字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 |
> | botServices/channels | 机器人服务 | 2-64 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 |
> | botServices/Connections | 机器人服务 | 2-64 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 |
> | enterpriseChannels | 资源组 | 2-64 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | Redis | 全局 | 1-63 | 字母数字和连字符。<br><br>以字母数字开头和结尾。 不允许连续的连字符。 |
> | Redis / firewallRules | Redis | 1-256 | 字母数字 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | 配置文件 | 资源组 | 1-260 | 字母数字和连字符。<br><br>以字母数字开头和结尾。 |
> | profiles/endpoints | 全局 | 1-50 | 字母数字和连字符。<br><br>以字母数字开头和结尾。 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | certificateOrders | 资源组 | 3-30 | 字母数字。 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | accounts | 资源组 | 2-64 | 字母数字和连字符。<br><br>以字母数字开头和结尾。 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | availabilitySets | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | diskEncryptionSets | 资源组 | 1-80 | 字母数字和下划线。 |
> | disks | 资源组 | 1-80 | 字母数字和下划线。 |
> | galleries | 资源组 | 1-80 | 字母数字和句点。<br><br>以字母数字开头和结尾。 |
> | galleries / applications | 库 | 1-80 | 字母数字、连字符和句点。<br><br>以字母数字开头和结尾。 |
> | galleries / applications/versions | application | 32-bit integer | 数字和句点。 |
> | galleries/images | 库 | 1-80 | 字母数字、连字符和句点。<br><br>以字母数字开头和结尾。 |
> | galleries/images/versions | 图像 | 32-bit integer | 数字和句点。 |
> | images | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | snapshots | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | virtualMachines | 资源组 | 1-15 (Windows)<br>1-64 (Linux)<br><br>参阅下面的说明。 | 不能使用空格或以下字符：<br> `\/"'[]:|<>+=;,?*@&`<br><br>不能以下划线开头。 不能以句点或连字符结尾。 |
> | virtualMachineScaleSets | 资源组 | 1-15 (Windows)<br>1-64 (Linux)<br><br>参阅下面的说明。 | 不能使用空格或以下字符：<br> `\/"'[]:|<>+=;,?*@&`<br><br>不能以下划线开头。 不能以句点或连字符结尾。 |

> [!NOTE]
> Azure 虚拟机有两个不同的名称：资源名称和主机名。 在门户中创建虚拟机时，将对这两个名称使用同一个值。 上表中的限制适用于主机名。 实际资源名称最多可包含 64 个字符。

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | containerGroups | 资源组 | 1-63 | 小写字母、数字和连字符。<br><br>不能以连字符开头或结尾。 不允许连续的连字符。 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | registries | 全局 | 5-50 | 字母数字。 |
> | registries / buildTasks | 注册表 | 5-50 | 字母数字。 |
> | registries / buildTasks/steps | 生成任务 | 5-50 | 字母数字。 |
> | registries/replications | 注册表 | 5-50 | 字母数字。 |
> | registries / scopeMaps | 注册表 | 5-50 | 字母数字、连字符和下划线。 |
> | registries/tasks | 注册表 | 5-50 | 字母数字、连字符和下划线。 |
> | registries / tokens | 注册表 | 5-50 | 字母数字、连字符和下划线。 |
> | registries/webhooks | 注册表 | 5-50 | 字母数字。 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | managedClusters | 资源组 | 1-63 | 字母数字、下划线和连字符。<br><br>以字母数字开头和结尾。 |
> | openShiftManagedClusters | 资源组 | 1-30 | 字母数字。 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | hubs | 资源组 | 1-64 | 字母数字。<br><br>以字母开头。  |
> | hubs/authorizationPolicies | 中心 | 1-50 | 字母数字、下划线字符和句点。<br><br>以字母数字开头和结尾。 |
> | hubs/connectors | 中心 | 1-128 | 字母数字和下划线字符。<br><br>以字母开头。 |
> | hubs/connectors/mappings | 连接器 | 1-128 | 字母数字和下划线字符。<br><br>以字母开头。 |
> | hubs/interactions | 中心 | 1-128 | 字母数字和下划线字符。<br><br>以字母开头。 |
> | hubs/kpi | 中心 | 1-512 | 字母数字和下划线字符。<br><br>以字母开头。 |
> | hubs/links | 中心 | 1-512 | 字母数字和下划线字符。<br><br>以字母开头。 |
> | hubs/predictions | 中心 | 1-512 | 字母数字和下划线字符。<br><br>以字母开头。 |
> | hubs/profiles | 中心 | 1-128 | 字母数字和下划线字符。<br><br>以字母开头。 |
> | hubs/relationshiplinks | 中心 | 1-512 | 字母数字和下划线字符。<br><br>以字母开头。 |
> | hubs/relationships | 中心 | 1-512 | 字母数字和下划线字符。<br><br>以字母开头。 |
> | hubs/roleAssignments | 中心 | 1-128 | 字母数字和下划线字符。<br><br>以字母开头。 |
> | hubs/views | 中心 | 1-512 | 字母数字和下划线字符。<br><br>以字母开头。 |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | associations | 资源组 | 1-180 | 不得使用：<br>`%&\\?/`<br><br>不得以句点或空格结尾。 |
> | resourceProviders | 资源组 | 3-64 | 不得使用：<br>`%&\\?/`<br><br>不得以句点或空格结尾。 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | jobs | 资源组 | 3-24 | 字母数字、连字符、下划线字符和句点。 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | workspaces | 资源组 | 3-30 | 字母数字、下划线字符和连字符 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | factories | 全局 | 3-63 | 字母数字和连字符。<br><br>以字母数字开头和结尾。 |
> | factories / dataflows | 工厂 | 1-260 | 不能使用：<br>`<>*#.%&:\\+?/`<br><br>以字母数字开头。 |
> | factories / datasets | 工厂 | 1-260 | 不能使用：<br>`<>*#.%&:\\+?/`<br><br>以字母数字开头。 |
> | factories / integrationRuntimes | 工厂 | 3-63 | 字母数字和连字符。<br><br>以字母数字开头和结尾。 |
> | factories / linkedservices | 工厂 | 1-260 | 不能使用：<br>`<>*#.%&:\\+?/`<br><br>以字母数字开头。 |
> | factories / pipelines | 工厂 | 1-260 | 不能使用：<br>`<>*#.%&:\\+?/`<br><br>以字母数字开头。 |
> | factories / triggers | 工厂 | 1-260 | 不能使用：<br>`<>*#.%&:\\+?/`<br><br>以字母数字开头。 |
> | factories / triggers / rerunTriggers | 触发器 | 1-260 | 不能使用：<br>`<>*#.%&:\\+?/`<br><br>以字母数字开头。 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | accounts | 全局 | 3-24 | 小写字母和数字。 |
> | ccounts/computePolicies | account | 3-60 | 字母数字、连字符和下划线字符。 |
> | accounts/dataLakeStoreAccounts | account | 3-24 | 小写字母和数字。 |
> | accounts/firewallRules | account | 3-50 | 字母数字、连字符和下划线字符。 |
> | accounts/storageAccounts | account | 3-60 | 字母数字、连字符和下划线字符。 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | accounts | 全局 | 3-24 | 小写字母和数字。 |
> | accounts/firewallRules | account | 3-50 | 字母数字、连字符和下划线字符。 |
> | accounts/virtualNetworkRules | account | 3-50 | 字母数字、连字符和下划线字符。 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | services | 资源组 | 2-62 | 字母数字、连字符、句点和下划线。<br><br>以字母数字开头。 |
> | services/projects | 服务 | 2-57 | 字母数字、连字符、句点和下划线。<br><br>以字母数字开头。 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | servers | 全局 | 3-63 | 小写字母、连字符和数字。<br><br>不能以连字符开头或结尾。 |
> | servers/databases | servers | 1-63 | 字母数字和连字符。 |
> | servers / firewallRules | servers | 1-128 | 字母数字、连字符和下划线。 |
> | servers / virtualNetworkRules | servers | 1-128 | 字母数字和连字符。 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | servers | 全局 | 3-63 | 小写字母、连字符和数字。<br><br>不能以连字符开头或结尾。 |
> | servers/databases | servers | 1-63 | 字母数字和连字符。 |
> | servers / firewallRules | servers | 1-128 | 字母数字、连字符和下划线。 |
> | servers / virtualNetworkRules | servers | 1-128 | 字母数字和连字符。 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | servers | 全局 | 3-63 | 小写字母、连字符和数字。<br><br>不能以连字符开头或结尾。 |
> | servers/databases | servers | 1-63 | 字母数字和连字符。 |
> | servers / firewallRules | servers | 1-128 | 字母数字、连字符和下划线。 |
> | servers / virtualNetworkRules | servers | 1-128 | 字母数字和连字符。 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | IotHubs | 全局 | 3-50 | 字母数字和连字符。<br><br>不能以连字符结尾。 |
> | IotHubs / certificates | IoT 中心 | 1-64 | 字母数字、连字符、句点和下划线。 |
> | IotHubs / eventHubEndpoints / ConsumerGroups | eventHubEndpoints | 1-50 | 字母数字、连字符、句点和下划线。 |
> | provisioningServices | 资源组 | 3-64 | 字母数字和连字符。<br><br>以字母数字结尾。 |
> | provisioningServices / certificates | provisioningServices | 1-64 | 字母数字、连字符、句点和下划线字符。 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | labs | 资源组 | 1-50 | 字母数字、下划线字符和连字符。 |
> | labs/customimages | 实验室 | 1-80 | 字母数字、下划线字符、连字符和括号。 |
> | labs/formulas | 实验室 | 1-80 | 字母数字、下划线字符、连字符和括号。 |
> | labs/virtualmachines | 实验室 | 1-15 (Windows)<br>1-64 (Linux) | 字母数字和连字符。<br><br>以字母数字开头和结尾。 不得全都是数字。 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | databaseAccounts | 全局 | 3-44 | 小写字母、数字和连字符。<br><br>以小写字母或数字开头。 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | domains | 资源组 | 3-50 | 字母数字和连字符。 |
> | domains / topics | 域 | 3-50 | 字母数字和连字符。 |
> | eventSubscriptions | 资源组 | 3-64 | 字母数字和连字符。 |
> | topics | 资源组 | 3-50 | 字母数字和连字符。 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | clusters | 资源组 | 6-50 | 字母数字和连字符。<br><br>以字母开头。 以字母或数字结尾。 |
> | namespaces | 全局 | 6-50 | 字母数字和连字符。<br><br>以字母开头。 以字母或数字结尾。 |
> | namespaces / AuthorizationRules | 命名空间 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>以字母或数字开头和结尾。 |
> | namespaces / disasterRecoveryConfigs | 命名空间 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>以字母或数字开头和结尾。 |
> | namespaces / eventhubs | 命名空间 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>以字母或数字开头和结尾。 |
> | namespaces / eventhubs / authorizationRules | 事件中心 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>以字母或数字开头和结尾。 |
> | namespaces / eventhubs / consumergroups | 事件中心 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>以字母或数字开头和结尾。 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | clusters | 全局 | 3-59 | 字母数字和连字符<br><br>以字母或数字开头和结尾。 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | jobs | 资源组 | 2-64 | 字母数字和连字符。<br><br>以字母开头。 |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | actionGroups | 资源组 | 1-260 | 不得使用：<br>`/&%\?` <br><br>不得以空格或句点结尾。  |
> | components | 资源组 | 1-260 | 不得使用：<br>`%&\?/` <br><br>不得以空格或句点结尾。  |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | IoTApps | 全局 | 2-63 | 小写字母、数字和连字符。<br><br>以小写字母或数字开头。 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | vaults | 全局 | 3-24 | 字母数字和连字符。<br><br>以字母开头。 以字母或数字结尾。 不能包含连续的连字符。 |
> | vaults / secrets | 保管库 | 1-127 | 字母数字和连字符。 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | clusters | 全局 | 4-22 | 小写字母和数字。<br><br>以字母开头。 |
> | /clusters / databases | cluster | 1-260 | 字母数字、连字符、空格和句点。 |
> | /clusters / databases / dataConnections | database | 1-40 | 字母数字、连字符、空格和句点。 |
> | /clusters / databases / eventhubconnections | database | 1-40 | 字母数字、连字符、空格和句点。 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | integrationAccounts | 资源组 | 1-80 | 字母数字、连字符、下划线、句点和括号。 |
> | integrationAccounts / assemblies | 集成帐户 | 1-80 | 字母数字、连字符、下划线、句点和括号。 |
> | integrationAccounts / batchConfigurations | 集成帐户 | 1-20 | 字母数字。 |
> | integrationAccounts / certificates | 集成帐户 | 1-80 | 字母数字、连字符、下划线、句点和括号。 |
> | integrationAccounts / maps | 集成帐户 | 1-80 | 字母数字、连字符、下划线、句点和括号。 |
> | integrationAccounts / partners | 集成帐户 | 1-80 | 字母数字、连字符、下划线、句点和括号。 |
> | integrationAccounts / rosettanetprocessconfigurations | 集成帐户 | 1-80 | 字母数字、连字符、下划线、句点和括号。 |
> | integrationAccounts / schemas | 集成帐户 | 1-80 | 字母数字、连字符、下划线、句点和括号。 |
> | integrationAccounts / sessions | 集成帐户 | 1-80 | 字母数字、连字符、下划线、句点和括号。 |
> | integrationServiceEnvironments | 资源组 | 1-80 | 字母数字、连字符、句点和下划线。 |
> | integrationServiceEnvironments / managedApis | 集成服务环境 | 1-80 | 字母数字、连字符、句点和下划线。 |
> | workflows | 资源组 | 1-80 | 字母数字、连字符、下划线字符、句点和括号。 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | commitmentPlans | 资源组 | 1-260 | 不得使用：<br>`<>*%&:?+/\\`<br><br>不得以空格结尾。 |
> | webServices | 资源组 | 1-260 | 不得使用：<br>`<>*%&:?+/\\`<br><br>不得以空格结尾。 |
> | workspaces | 资源组 | 1-260 | 不得使用：<br>`<>*%&:?+/\\`<br><br>不得以空格结尾。 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | workspaces | 资源组 | 3-33 | 字母数字和连字符。 |
> | workspaces / computes | 工作区 | 2-16 | 字母数字和连字符。 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | userAssignedIdentities | 资源组 | 3-128 | 字母数字、连字符和下划线<br><br>以字母或数字开头。 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | accounts | 资源组 | 1-98（对于资源组名称和帐户名） | 字母数字、下划线字符、句点和连字符。<br><br>以字母数字开头。 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | mediaservices | 资源组 | 3-24 | 小写字母和数字。 |
> | mediaservices / liveEvents | 媒体服务 | 1-32 | 字母数字和连字符。<br><br>以字母数字开头。 |
> | mediaservices / liveEvents / liveOutputs | 实时事件 | 1-256 | 字母数字和连字符。<br><br>以字母数字开头。 |
> | mediaservices / streamingEndpoints | 媒体服务 | 1-24 | 字母数字和连字符。<br><br>以字母数字开头。 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 实体 | 作用域 | Length | 有效的字符 |
> | --- | --- | --- | --- |
> | applicationGateways | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | applicationSecurityGroups | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | azureFirewalls | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | bastionHosts | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | connections | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | dnsZones | 资源组 | 1-63 个字符<br><br>2 到 34 个标签<br><br>每个标签是以句点分隔的一组字符。 例如，**contoso.com** 有 2 个标签。 | 每个标签可以包含字母数字、下划线和连字符。<br><br>每个标签以句点分隔。 |
> | expressRouteCircuits | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | firewallPolicies | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | firewallPolicies / ruleGroups | 防火墙策略 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | frontDoors | 全局 | 5-64 | 字母数字和连字符。<br><br>以字母数字开头和结尾。 |
> | loadBalancers | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | loadBalancers / inboundNatRules | 负载均衡器 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | localNetworkGateways | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | networkInterfaces | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | networkSecurityGroups | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | networkSecurityGroups / securityRules | 网络安全组 | 1-80 |  字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | networkWatchers | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | privateDnsZones | 资源组 | 1-63 个字符<br><br>2 到 34 个标签<br><br>每个标签是以句点分隔的一组字符。 例如，**contoso.com** 有 2 个标签。 | 每个标签可以包含字母数字、下划线和连字符。<br><br>每个标签以句点分隔。 |
> | privateDnsZones / virtualNetworkLinks | 专用 DNS 区域 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | publicIPAddresses | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | publicIPPrefixes | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | routeFilters | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | routeFilters / routeFilterRules | 路由筛选器 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | routeTables | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | routeTables / routes | 路由表 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | serviceEndpointPolicies | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | trafficmanagerprofiles | 全局 | 1-63 | 字母数字、连字符和句点。<br><br>以字母数字开头和结尾。 |
> | virtualNetworkGateways | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | virtualNetworks | 资源组 | 2-64 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | virtualnetworks / subnets | 虚拟网络 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | virtualNetworks / virtualNetworkPeerings | 虚拟网络 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | virtualWans | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | vpnGateways | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | vpnGateways / vpnConnections | VPN 网关 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |
> | vpnSites | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结尾。 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | namespaces | 全局 | 6-50 | 字母数字和连字符<br><br>以字母开头。 以字母数字结尾。 |
> | namespaces / AuthorizationRules | 命名空间 | 1-256 | 字母数字、句点、连字符和下划线。<br><br>以字母数字开头。 |
> | namespaces / notificationHubs | 命名空间 | 1-260 | 字母数字、句点、连字符和下划线。<br><br>以字母数字开头。 |
> | namespaces / notificationHubs / AuthorizationRules | 通知中心 | 1-256 | 字母数字、句点、连字符和下划线。<br><br>以字母数字开头。 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | clusters | 资源组 | 4-63 | 字母数字和连字符。<br><br>以字母数字开头和结尾。 |
> | workspaces | 资源组 | 4-63 | 字母数字和连字符。<br><br>以字母数字开头和结尾。 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | solutions | 工作区 | 空值 | 对于 Microsoft 创作的解决方案，名称必须采用以下格式：<br>`SolutionType(WorkspaceName)`<br><br>对于第三方创作的解决方案，名称必须采用以下格式：<br>`SolutionType[WorkspaceName]`<br><br>例如，有效名称为：<br>`AntiMalware(contoso-IT)`<br><br>解决方案类型区分大小写。 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | dashboards | 资源组 | 3-160 | 字母数字和连字符。<br><br>若要使用受限字符，请添加名为 **hidden-title** 的标记以及要使用的仪表板名称。 显示仪表板时，门户会显示该名称。 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | workspaceCollections | region | 3-63 | 字母数字和连字符。<br><br>不能以连字符开头。 不能使用连续的连字符。 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | capacities | region | 3-63 | 小写字母或数字<br><br>以小写字母开头。 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | vaults | 资源组 | 2-50 | 字母数字和连字符。<br><br>以字母开头。 |
> | vaults / backupPolicies | 保管库 | 3-150 | 字母数字和连字符。<br><br>以字母开头。 不能以连字符结尾。 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | namespaces | 全局 | 6-50 | 字母数字和连字符。<br><br>以字母开头。 以字母或数字结尾。 |
> | namespaces / AuthorizationRules | 命名空间 | 1-50 |  字母数字、句点、连字符和下划线。<br><br>以字母数字开头和结尾。 |
> | namespaces / HybridConnections | 命名空间 | 1-260 | 字母数字、句点、连字符、下划线和斜杠。<br><br>以字母数字开头和结尾。 |
> | namespaces / HybridConnections/authorizationRules | 混合连接 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>以字母数字开头和结尾。 |
> | namespaces / WcfRelays | 命名空间 | 1-260 | 字母数字、句点、连字符、下划线和斜杠。<br><br>以字母数字开头和结尾。 |
> | namespaces / WcfRelays / authorizationRules | WCF 中继 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>以字母数字开头和结尾。 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | deployments | 资源组 | 1-64 | 字母数字、下划线、括号、连字符和句点。 |
> | resourcegroups | 订阅 | 1-90 | 字母数字、下划线、括号、连字符、句点，以及与[正则表达式文档](/rest/api/resources/resourcegroups/createorupdate)匹配的 Unicode 字符。<br><br>不能以句点结尾。 |
> | tagNames | resource | 1-512 | 不能使用：<br>`<>%&\?/` |
> | tagNames / tagValues | 标记名称 | 1-256 | 所有字符。 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | namespaces | 全局 | 6-50 | 字母数字和连字符。<br><br>以字母开头。 以字母或数字结尾。<br><br>有关详细信息，请参阅[创建命名空间](/rest/api/servicebus/create-namespace)。 |
> | namespaces / AuthorizationRules | 命名空间 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>以字母数字开头和结尾。 |
> | namespaces / disasterRecoveryConfigs | 全局 | 6-50 | 字母数字和连字符。<br><br>以字母开头。 以字母数字结尾。 |
> | namespaces / migrationConfigurations | 命名空间 |  | 应始终为 **$default**。 |
> | namespaces / queues | 命名空间 | 1-260 | 字母数字、句点、连字符、下划线和斜杠。<br><br>以字母数字开头和结尾。 |
> | namespaces / queues / authorizationRules | queue | 1-50 | 字母数字、句点、连字符和下划线。<br><br>以字母数字开头和结尾。 |
> | namespaces / topics | 命名空间 | 1-260 | 字母数字、句点、连字符、下划线和斜杠。<br><br>以字母数字开头和结尾。 |
> | namespaces / topics / authorizationRules | 主题 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>以字母数字开头和结尾。 |
> | namespaces / topics / subscriptions | 主题 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>以字母数字开头和结尾。 |
> | namespaces / topics / subscriptions / rules | 订阅 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>以字母数字开头和结尾。 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | clusters | region | 4-23 | 小写字母、数字和连字符。<br><br>以小写字母开头。 以小写字母或数字结尾。 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | signalR | 全局 | 3-63 | 字母数字和连字符。<br><br>以字母开头。 以字母或数字结尾。  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | managedInstances | 全局 | 1-63 | 小写字母、数字和连字符。<br><br>不能以连字符开头或结尾。 |
> | servers | 全局 | 1-63 | 小写字母、数字和连字符。<br><br>不能以连字符开头或结尾。 |
> | servers / administrators | server |  | 必须是 `ActiveDirectory`。 |
> | servers/databases | server | 1-128 | 不能使用：<br>`<>*%&:\/?`<br><br>不能以句点或空格结尾。 |
> | servers / databases / syncGroups | database | 1-150 | 字母数字、连字符和下划线。 |
> | servers / elasticPools | server | 1-128 | 不能使用：<br>`<>*%&:\/?`<br><br>不能以句点或空格结尾。 |
> | servers / failoverGroups | 全局 | 1-63 | 小写字母、数字和连字符。<br><br>不能以连字符开头或结尾。 |
> | servers / firewallRules | server | 1-128 | 不能使用：<br>`<>*%&:;\/?`<br><br>不能以句点结尾。 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | storageAccounts | 全局 | 3-24 | 小写字母和数字。 |
> | storageAccounts / blobServices | 存储帐户 |  | 必须是 `default`。 |
> | storageAccounts / blobServices / containers | 存储帐户 | 3-63 | 小写字母、数字和连字符。<br><br>以小写字母或数字开头。 不能使用连续的连字符。 |
> | storageAccounts / fileServices | 存储帐户 |  | 必须是 `default`。 |
> | storageAccounts / fileServices / shares | 存储帐户 | 3-63 | 小写字母、数字和连字符。<br><br>不能以连字符开头或结尾。 不能使用连续的连字符。 |
> | storageAccounts / managementPolicies | 存储帐户 |  | 必须是 `default`。 |
> | blob | container | 1-1024 | 任何 URL 字符，区分大小写 |
> | queue | 存储帐户 | 3-63 | 小写字母、数字和连字符。<br><br>不能以连字符开头或结尾。 不能使用连续的连字符。 |
> | 表 | 存储帐户 | 3-63 | 字母数字。<br><br>以字母开头。 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | storageSyncServices | 资源组 | 1-260 | 字母数字、空格、句点、连字符和下划线字符。<br><br>不得以句点或空格结尾。 |
> | storageSyncServices/syncGroups | 存储同步服务 | 1-260 | 字母数字、空格、句点、连字符和下划线字符。<br><br>不得以句点或空格结尾。 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | managers | 资源组 | 2-50 | 字母数字和连字符。<br><br>以字母开头。 以字母数字结尾。 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | streamingjobs | 资源组 | 3-63 | 字母数字、连字符和下划线。 |
> | streamingjobs / functions | 流式处理作业 | 3-63 | 字母数字、连字符和下划线。 |
> | streamingjobs / inputs | 流式处理作业 | 3-63 | 字母数字、连字符和下划线。 |
> | streamingjobs / outputs | 流式处理作业 | 3-63 | 字母数字、连字符和下划线。 |
> | streamingjobs / transformations | 流式处理作业 | 3-63 | 字母数字、连字符和下划线。 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | environments | 资源组 | 1-90 | 不能使用：<br>`'<>%&:\?/#` |
> | environments / accessPolicies | 环境 | 1-90 | 不能使用：<br> `'<>%&:\?/#` |
> | environments / eventSources | 环境 | 1-90 | 不能使用：<br>`'<>%&:\?/#` |
> | environments / referenceDataSets | 环境 | 3-63 | 字母数字 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | certificates | 资源组 | 1-260 | 不得使用：<br>`/` <br><br>不得以空格或句点结尾。  | 
> | serverfarms | 资源组 | 1-40 | 字母数字和连字符。 |
> | sites | 全局 | 2-60 | 包含字母数字和连字符。<br><br>不能以连字符开头或结尾。 |
> | sites/slots | site | 2-59 | 字母数字和连字符。 |

> [!NOTE]
> Azure Functions 具有与 Microsoft.Web/sites 相同的命名规则和限制。

## <a name="next-steps"></a>后续步骤

有关如何命名资源的建议，请参阅[准备就绪：建议的命名和标记约定](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)。
