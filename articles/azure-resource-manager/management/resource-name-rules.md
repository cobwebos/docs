---
title: 资源命名限制
description: 显示用于命名 Azure 资源的规则和限制。
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 7b6a9e16ba28a2a0e5e4e181dc5650d2110eab88
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273912"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Azure 资源的命名规则和限制

本文汇总了 Azure 资源的命名规则和限制。 有关如何命名资源的建议，请参阅[Ready：推荐命名和标记约定](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)。

除非在 "有效字符" 列中特别注明，否则资源名称不区分大小写。

在下表中，术语 "字母数字" 指的是：

* **从 a**到**z** （小写字母）
* **A**到**Z** （大写字母）
* **0**到**9** （数字）

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | servers | 资源组 | 3-63 | 小写字母和数字。<br><br>小写字母开头。 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | 服务 | 全局 | 1-50 | 构成.<br><br>字母开头。 |
> | 服务/api | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/api/问题 | api | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/api/问题/附件 | 本期 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/api/问题/注释 | 本期 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/api/操作 | api | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/api/操作/标记 | operation | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/api/版本 | api | 1-80 | 字母数字、下划线和连字符。<br><br>以字母数字或下划线开头和结尾。 |
> | 服务/api/架构 | api | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/api/tagDescriptions | api | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/api/标记 | api | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/api 版本-集 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/authorizationServers | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/后端 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/证书 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/诊断 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/组 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/组/用户 | group | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/identityProviders | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/记录器 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/通知 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/通知/recipientEmails | 通知 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/openidConnectProviders | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/策略 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/产品 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/产品/api | product | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/产品/组 | product | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/产品/标记 | product | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/属性 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/订阅 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/标记 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/模板 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/用户 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | configurationStores | 资源组 | 5-50 | 字母数字、下划线和连字符。 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | 锁定 | 分配范围 | 1-90 | 字母数字、句点、下划线、连字符和括号。<br><br>不能以句点结束。 |
> | policyassignments | 分配范围 | 1-128 显示名称<br><br>1-260 资源名称 | 显示名称可以包含任何字符。<br><br>资源名称不能包含 `%` 且不能以句点或空格结尾。 |
> | policydefinitions | 定义的作用域 | 1-128 显示名称<br><br>1-260 资源名称 | 显示名称可以包含任何字符。<br><br>资源名称不能包含 `%` 且不能以句点或空格结尾。 |
> | policySetDefinitions | 定义的作用域 | 1-128 显示名称<br><br>1-260 资源名称 | 显示名称可以包含任何字符。<br><br>资源名称不能包含 `%` 且不能以句点或空格结尾。  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | automationAccounts | 资源组 | 6-50 | 字母数字和连字符。<br><br>以字母开头，以字母数字结尾。 |
> | automationAccounts/证书 | 自动化帐户 | 1-128 | 无法使用：<br> `<>*%&:\?.+/` <br><br>不能以空格结尾。  |
> | automationAccounts/连接 | 自动化帐户 | 1-128 | 无法使用：<br> `<>*%&:\?.+/` <br><br>不能以空格结尾。 |
> | automationAccounts/凭据 | 自动化帐户 | 1-128 | 无法使用：<br> `<>*%&:\?.+/` <br><br>不能以空格结尾。 |
> | automationAccounts/runbook | 自动化帐户 | 1-63 | 字母数字、下划线和连字符。<br><br>字母开头。  |
> | automationAccounts/计划 | 自动化帐户 | 1-128 | 无法使用：<br> `<>*%&:\?.+/` <br><br>不能以空格结尾。 |
> | automationAccounts/variables | 自动化帐户 | 1-128 | 无法使用：<br> `<>*%&:\?.+/` <br><br>不能以空格结尾。 |
> | automationAccounts/观察程序 | 自动化帐户 | 1-63 |  字母数字、下划线和连字符。<br><br>字母开头。 |
> | automationAccounts/webhook | 自动化帐户 | 1-128 | 无法使用：<br> `<>*%&:\?.+/` <br><br>不能以空格结尾。 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | batchAccounts | 区域 | 3-24 | 小写字母和数字。 |
> | batchAccounts/应用程序 | batch 帐户 | 1-64 | 字母数字、下划线和连字符。 |
> | batchAccounts/证书 | batch 帐户 | 5-45 | 字母数字、下划线和连字符。 |
> | batchAccounts/池 | batch 帐户 | 1-64 | 字母数字、下划线和连字符。 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | blockchainMembers | 全局 | 2-20 | 小写字母和数字。<br><br>小写字母开头。 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | botServices | 全局 | 2-64 |  字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 |
> | botServices/通道 | 机器人服务 | 2-64 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 |
> | botServices/连接 | 机器人服务 | 2-64 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 |
> | enterpriseChannels | 资源组 | 2-64 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | Redis | 全局 | 1-63 | 字母数字和连字符。<br><br>字母数字开头和结尾。 不允许使用连续的连字符。 |
> | Redis/firewallRules | Redis | 1-256 | 构成 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | 配置文件 | 资源组 | 1-260 | 字母数字和连字符。<br><br>字母数字开头和结尾。 |
> | 配置文件/终结点 | 全局 | 1-50 | 字母数字和连字符。<br><br>字母数字开头和结尾。 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | certificateOrders | 资源组 | 3-30 | 构成. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | accounts | 资源组 | 2-64 | 字母数字和连字符。<br><br>字母数字开头和结尾。 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | availabilitySets | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结束。 |
> | diskEncryptionSets | 资源组 | 1-80 | 字母数字和下划线。 |
> | disks | 资源组 | 1-80 | 字母数字和下划线。 |
> | galleries | 资源组 | 1-80 | 字母数字和句点。<br><br>字母数字开头和结尾。 |
> | 库/应用程序 | 图库 | 1-80 | 字母数字、连字符和句点。<br><br>字母数字开头和结尾。 |
> | 库/应用程序/版本 | application | 32-bit integer | 数字和句点。 |
> | 库/图像 | 图库 | 1-80 | 字母数字、连字符和句点。<br><br>字母数字开头和结尾。 |
> | 库/图像/版本 | image | 32-bit integer | 数字和句点。 |
> | images | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结束。 |
> | snapshots | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结束。 |
> | virtualMachines | 资源组 | 1-15 （Windows）<br>1-64 （Linux）<br><br>请参阅下面的说明。 | 无法使用：<br> `\/""[]:|<>+=;,?*@&`<br><br>不能以下划线开头。 不能以句点或连字符结尾。 |
> | virtualMachineScaleSets | 资源组 | 1-15 （Windows）<br>1-64 （Linux）<br><br>请参阅下面的说明。 | 无法使用：<br> `\/""[]:|<>+=;,?*@&`<br><br>不能以下划线开头。 不能以句点或连字符结尾。 |

> [!NOTE]
> Azure 虚拟机具有两个不同的名称：资源名称和主机名。 当你在门户中创建虚拟机时，这两个名称使用相同的值。 上表中的限制适用于主机名。 实际资源名称最多可包含 64 个字符。

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | containerGroups | 资源组 | 1-63 | 小写字母、数字和连字符。<br><br>不能以连字符开头或结尾。 不允许使用连续的连字符。 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | registries | 全局 | 5-50 | 构成. |
> | 注册表/buildTasks | 注册表 | 5-50 | 构成. |
> | 注册表/buildTasks/步骤 | 生成任务 | 5-50 | 构成. |
> | 注册表/复制 | 注册表 | 5-50 | 构成. |
> | 注册表/scopeMaps | 注册表 | 5-50 | 字母数字、连字符和下划线。 |
> | 注册表/任务 | 注册表 | 5-50 | 字母数字、连字符和下划线。 |
> | 注册表/标记 | 注册表 | 5-50 | 字母数字、连字符和下划线。 |
> | 注册表/webhook | 注册表 | 5-50 | 构成. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | managedClusters | 资源组 | 1-63 | 字母数字、下划线和连字符。<br><br>字母数字开头和结尾。 |
> | openShiftManagedClusters | 资源组 | 1-30 | 构成. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | hubs | 资源组 | 1-64 | 构成.<br><br>字母开头。  |
> | 中心/authorizationPolicies | 星 | 1-50 | 字母数字、下划线和句点。<br><br>字母数字开头和结尾。 |
> | 集线器/连接器 | 星 | 1-128 | 字母数字和下划线。<br><br>字母开头。 |
> | 集线器/连接器/映射 | 连接器 | 1-128 | 字母数字和下划线。<br><br>字母开头。 |
> | 中心/交互 | 星 | 1-128 | 字母数字和下划线。<br><br>字母开头。 |
> | 中心/kpi | 星 | 1-512 | 字母数字和下划线。<br><br>字母开头。 |
> | 中心/链接 | 星 | 1-512 | 字母数字和下划线。<br><br>字母开头。 |
> | 中心/预测 | 星 | 1-512 | 字母数字和下划线。<br><br>字母开头。 |
> | 集线器/配置文件 | 星 | 1-128 | 字母数字和下划线。<br><br>字母开头。 |
> | 中心/relationshipLinks | 星 | 1-512 | 字母数字和下划线。<br><br>字母开头。 |
> | 中心/关系 | 星 | 1-512 | 字母数字和下划线。<br><br>字母开头。 |
> | 中心/roleAssignments | 星 | 1-128 | 字母数字和下划线。<br><br>字母开头。 |
> | 中心/视图 | 星 | 1-512 | 字母数字和下划线。<br><br>字母开头。 |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | 关联 | 资源组 | 1-180 | 无法使用：<br>`%&\\?/`<br><br>不能以句点或空格结束。 |
> | resourceProviders | 资源组 | 3-64 | 无法使用：<br>`%&\\?/`<br><br>不能以句点或空格结束。 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | jobs | 资源组 | 3-24 | 字母数字、连字符、下划线和句点。 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | workspaces | 资源组 | 3-30 | 字母数字、下划线和连字符 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | factories | 全局 | 3-63 | 字母数字和连字符。<br><br>字母数字开头和结尾。 |
> | 工厂/数据流 | 工厂 | 1-260 | 无法使用：<br>`<>*#.%&:\\+?/`<br><br>以字母数字开头。 |
> | 工厂/数据集 | 工厂 | 1-260 | 无法使用：<br>`<>*#.%&:\\+?/`<br><br>以字母数字开头。 |
> | 工厂/integrationRuntimes | 工厂 | 3-63 | 字母数字和连字符。<br><br>字母数字开头和结尾。 |
> | 工厂/linkedservices.json | 工厂 | 1-260 | 无法使用：<br>`<>*#.%&:\\+?/`<br><br>以字母数字开头。 |
> | 工厂/管道 | 工厂 | 1-260 | 无法使用：<br>`<>*#.%&:\\+?/`<br><br>以字母数字开头。 |
> | 工厂/触发器 | 工厂 | 1-260 | 无法使用：<br>`<>*#.%&:\\+?/`<br><br>以字母数字开头。 |
> | 工厂/触发器/rerunTriggers | 触发器 | 1-260 | 无法使用：<br>`<>*#.%&:\\+?/`<br><br>以字母数字开头。 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | accounts | 全局 | 3-24 | 小写字母和数字。 |
> | 帐户/computePolicies | account | 3-60 | 字母数字、连字符和下划线。 |
> | 帐户/dataLakeStoreAccounts | account | 3-24 | 小写字母和数字。 |
> | 帐户/firewallRules | account | 3-50 | 字母数字、连字符和下划线。 |
> | 帐户/storageAccounts | account | 3-60 | 字母数字、连字符和下划线。 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | accounts | 全局 | 3-24 | 小写字母和数字。 |
> | 帐户/firewallRules | account | 3-50 | 字母数字、连字符和下划线。 |
> | 帐户/virtualNetworkRules | account | 3-50 | 字母数字、连字符和下划线。 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | services | 资源组 | 2-62 | 字母数字、连字符、句点和下划线。<br><br>以字母数字开头。 |
> | 服务/项目 | 服务 | 2-57 | 字母数字、连字符、句点和下划线。<br><br>以字母数字开头。 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | servers | 全局 | 3-63 | 小写字母、连字符和数字。<br><br>不能以连字符开头或结尾。 |
> | 服务器/数据库 | servers | 1-63 | 字母数字和连字符。 |
> | 服务器/firewallRules | servers | 1-128 | 字母数字、连字符和下划线。 |
> | 服务器/virtualNetworkRules | servers | 1-128 | 字母数字和连字符。 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | servers | 全局 | 3-63 | 小写字母、连字符和数字。<br><br>不能以连字符开头或结尾。 |
> | 服务器/数据库 | servers | 1-63 | 字母数字和连字符。 |
> | 服务器/firewallRules | servers | 1-128 | 字母数字、连字符和下划线。 |
> | 服务器/virtualNetworkRules | servers | 1-128 | 字母数字和连字符。 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | servers | 全局 | 3-63 | 小写字母、连字符和数字。<br><br>不能以连字符开头或结尾。 |
> | 服务器/数据库 | servers | 1-63 | 字母数字和连字符。 |
> | 服务器/firewallRules | servers | 1-128 | 字母数字、连字符和下划线。 |
> | 服务器/virtualNetworkRules | servers | 1-128 | 字母数字和连字符。 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | IotHubs | 全局 | 3-50 | 字母数字和连字符。<br><br>不能以连字符结尾。 |
> | IotHubs/证书 | IoT 中心 | 1-64 | 字母数字、连字符、句点和下划线。 |
> | IotHubs / eventHubEndpoints / ConsumerGroups | eventHubEndpoints | 1-50 | 字母数字、连字符、句点和下划线。 |
> | ProvisioningServices | 资源组 | 3-64 | 字母数字和连字符。<br><br>以字母数字结尾。 |
> | provisioningServices/证书 | ProvisioningServices | 1-64 | 字母数字、连字符、句点和下划线。 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | labs | 资源组 | 1-50 | 字母数字、下划线和连字符。 |
> | 实验室/customimages | 体验 | 1-80 | 字母数字、下划线、连字符和括号。 |
> | 实验室/公式 | 体验 | 1-80 | 字母数字、下划线、连字符和括号。 |
> | 实验室/virtualmachines | 体验 | 1-15 （Windows）<br>1-64 （Linux） | 字母数字和连字符。<br><br>字母数字开头和结尾。 不能全部是数字。 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | databaseAccounts | 全局 | 3-31 | 小写字母、数字和连字符。<br><br>小写字母或数字开头。 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | domains | 资源组 | 3-50 | 字母数字和连字符。 |
> | 域/主题 | 域 | 3-50 | 字母数字和连字符。 |
> | eventSubscriptions | 资源组 | 3-64 | 字母数字和连字符。 |
> | topics | 资源组 | 3-50 | 字母数字和连字符。 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | clusters | 资源组 | 6-50 | 字母数字和连字符。<br><br>字母开头。 以字母或数字结尾。 |
> | namespaces | 全局 | 6-50 | 字母数字和连字符。<br><br>字母开头。 以字母或数字结尾。 |
> | 命名空间/AuthorizationRules | 命名空间 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>字母或数字开头和结尾。 |
> | 命名空间/disasterRecoveryConfigs | 命名空间 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>字母或数字开头和结尾。 |
> | 命名空间/eventhubs | 命名空间 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>字母或数字开头和结尾。 |
> | 命名空间/eventhubs/authorizationRules | 事件中心 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>字母或数字开头和结尾。 |
> | 命名空间/eventhubs/consumergroups | 事件中心 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>字母或数字开头和结尾。 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | clusters | 全局 | 3-59 | 字母数字和连字符<br><br>字母或数字开头和结尾。 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | jobs | 资源组 | 2-64 | 字母数字和连字符。<br><br>字母开头。 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | IoTApps | 全局 | 2-63 | 小写字母、数字和连字符。<br><br>小写字母或数字开头。 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | vaults | 全局 | 3-24 | 字母数字和连字符。<br><br>字母开头。 以字母或数字结尾。 不能包含连续的连字符。 |
> | 保管库/机密 | 保管库 | 1-127 | 字母数字和连字符。 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | clusters | 全局 | 4-22 | 小写字母和数字。<br><br>字母开头。 |
> | /clusters/数据库 | cluster | 1-260 | 字母数字、连字符、空格和句点。 |
> | /clusters/数据库/dataConnections | database | 1-40 | 字母数字、连字符、空格和句点。 |
> | /clusters/数据库/eventhubconnections | database | 1-40 | 字母数字、连字符、空格和句点。 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | integrationAccounts | 资源组 | 1-80 | 字母数字、连字符、下划线、句点和括号。 |
> | integrationAccounts/程序集 | 集成帐户 | 1-80 | 字母数字、连字符、下划线、句点和括号。 |
> | integrationAccounts / batchConfigurations | 集成帐户 | 1-20 | 构成. |
> | integrationAccounts/证书 | 集成帐户 | 1-80 | 字母数字、连字符、下划线、句点和括号。 |
> | integrationAccounts/maps | 集成帐户 | 1-80 | 字母数字、连字符、下划线、句点和括号。 |
> | integrationAccounts/合作伙伴 | 集成帐户 | 1-80 | 字母数字、连字符、下划线、句点和括号。 |
> | integrationAccounts / rosettanetprocessconfigurations | 集成帐户 | 1-80 | 字母数字、连字符、下划线、句点和括号。 |
> | integrationAccounts/架构 | 集成帐户 | 1-80 | 字母数字、连字符、下划线、句点和括号。 |
> | integrationAccounts/会话 | 集成帐户 | 1-80 | 字母数字、连字符、下划线、句点和括号。 |
> | integrationServiceEnvironments | 资源组 | 1-80 | 字母数字、连字符、句点和下划线。 |
> | integrationServiceEnvironments / managedApis | integration service 环境 | 1-80 | 字母数字、连字符、句点和下划线。 |
> | workflows | 资源组 | 1-80 | 字母数字、连字符、下划线、句点和括号。 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | commitmentPlans | 资源组 | 1-260 | 无法使用：<br>`<>*%&:?+/\\`<br><br>不能以空格结尾。 |
> | webServices | 资源组 | 1-260 | 无法使用：<br>`<>*%&:?+/\\`<br><br>不能以空格结尾。 |
> | workspaces | 资源组 | 1-260 | 无法使用：<br>`<>*%&:?+/\\`<br><br>不能以空格结尾。 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | workspaces | 资源组 | 3-33 | 字母数字和连字符。 |
> | 工作区/计算 | 工作区 | 2-16 | 字母数字和连字符。 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | userAssignedIdentities | 资源组 | 3-128 | 字母数字、连字符和下划线<br><br>字母或数字开头。 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | accounts | 资源组 | 1-98 （用于资源组名称和帐户名称） | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | mediaservices | 资源组 | 3-24 | 小写字母和数字。 |
> | windowsazure.mediaservices/liveEvents | 媒体服务 | 1-32 | 字母数字和连字符。<br><br>以字母数字开头。 |
> | windowsazure.mediaservices/liveEvents/liveOutputs | 实时事件 | 1-256 | 字母数字和连字符。<br><br>以字母数字开头。 |
> | windowsazure.mediaservices/Streamingendpoint | 媒体服务 | 1-24 | 字母数字和连字符。<br><br>以字母数字开头。 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | applicationGateways | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | applicationSecurityGroups | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | azureFirewalls | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结束。 |
> | bastionHosts | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | connections | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | dnsZones | 资源组 | 1-63 个字符<br><br>2到34标签<br><br>每个标签都是用句点分隔的一组字符。 例如， **contoso.com**有2个标签。 | 每个标签可以包含字母数字、下划线和连字符。<br><br>每个标签用句点分隔。 |
> | expressRouteCircuits | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | firewallPolicies | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | firewallPolicies/规则组 | 防火墙策略 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | frontDoors | 全局 | 5-64 | 字母数字和连字符。<br><br>字母数字开头和结尾。 |
> | loadBalancers | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | loadBalancers/Loadbalancer.inboundnatrules | 负载均衡器 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | localNetworkGateways | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | networkInterfaces | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | networkSecurityGroups | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | networkSecurityGroups/securityRules | 网络安全组 | 1-80 |  字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | networkWatchers | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | privateDnsZones | 资源组 | 1-63 个字符<br><br>2到34标签<br><br>每个标签都是用句点分隔的一组字符。 例如， **contoso.com**有2个标签。 | 每个标签可以包含字母数字、下划线和连字符。<br><br>每个标签用句点分隔。 |
> | privateDnsZones / virtualNetworkLinks | 专用 DNS 区域 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | publicIPAddresses | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | publicIPPrefixes | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | routeFilters | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | routeFilters / routeFilterRules | 路由筛选器 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | routeTables | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | routeTables/路由 | 路由表 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | serviceEndpointPolicies | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | trafficmanagerprofiles | 全局 | 1-63 | 字母数字、连字符和句点。<br><br>字母数字开头和结尾。 |
> | virtualNetworkGateways | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | virtualNetworks | 资源组 | 2-64 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | virtualnetworks/子网 | 虚拟网络 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | virtualNetworks/virtualNetworkPeerings | 虚拟网络 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | virtualWans | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | vpnGateways | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | vpnGateways / vpnConnections | VPN 网关 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | vpnSites | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | namespaces | 全局 | 6-50 | 字母数字和连字符<br><br>字母数字开头和结尾。 |
> | 命名空间/AuthorizationRules | 命名空间 | 1-256 | 字母数字、句点、连字符和下划线。<br><br>开始字母数字。 |
> | 命名空间/notificationHubs | 命名空间 | 1-260 | 字母数字、句点、连字符和下划线。<br><br>开始字母数字。 |
> | 命名空间/notificationHubs/AuthorizationRules | 通知中心 | 1-256 | 字母数字、句点、连字符和下划线。<br><br>开始字母数字。 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | clusters | 资源组 | 4-63 | 字母数字和连字符。<br><br>字母数字开头和结尾。 |
> | workspaces | 资源组 | 4-63 | 字母数字和连字符。<br><br>字母数字开头和结尾。 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | dashboards | 资源组 | 3-160 | 字母数字和连字符。<br><br>若要使用受限字符，请使用要使用的仪表板名称添加名为**隐藏标题**的标记。 显示仪表板时，门户会显示该名称。 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | workspaceCollections | region | 3-63 | 字母数字和连字符。<br><br>不能以连字符开头。 不能使用连续的连字符。 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | capacities | region | 3-63 | 小写字母或数字<br><br>小写字母开头。 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | vaults | 资源组 | 2-50 | 字母数字和连字符。<br><br>字母开头。 |
> | 保管库/backupPolicies | 保管库 | 3-150 | 字母数字和连字符。<br><br>字母开头。 不能以连字符结尾。 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | namespaces | 全局 | 6-50 | 字母数字和连字符。<br><br>以字母开头。 以字母或数字结尾。 |
> | 命名空间/AuthorizationRules | 命名空间 | 1-50 |  字母数字、句点、连字符和下划线。<br><br>字母数字开头和结尾。 |
> | 命名空间/HybridConnections | 命名空间 | 1-260 | 字母数字、句点、连字符、下划线和斜杠。<br><br>字母数字开头和结尾。 |
> | 命名空间/HybridConnections/authorizationRules | 混合连接 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>字母数字开头和结尾。 |
> | 命名空间/WcfRelays | 命名空间 | 1-260 | 字母数字、句点、连字符、下划线和斜杠。<br><br>字母数字开头和结尾。 |
> | 命名空间/WcfRelays/authorizationRules | Wcf 中继 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>字母数字开头和结尾。 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | deployments | 资源组 | 1-64 | 字母数字、下划线、括号、连字符和句点。 |
> | resourcegroups | 订阅 | 1-90 | 匹配[正则表达式文档](/rest/api/resources/resourcegroups/createorupdate)的字母数字、下划线、括号、连字符、句点和 unicode 字符。<br><br>不能以句点结束。 |
> | tagNames | resource | 1-512 | 无法使用：<br>`<>%&\?/` |
> | tagNames / tagValues | 标记名称 | 1-256 | 所有字符。 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | namespaces | 全局 | 6-50 | 字母数字和连字符。<br><br>以字母开头。 以字母或数字结尾。<br><br>有关详细信息，请参阅[创建命名空间](/rest/api/servicebus/create-namespace)。 |
> | 命名空间/AuthorizationRules | 命名空间 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>Alphnumeric 的开头和结尾。 |
> | 命名空间/disasterRecoveryConfigs | 全局 | 6-50 | 字母数字和连字符。<br><br>字母开头。 以字母数字结尾。 |
> | 命名空间/migrationConfigurations | 命名空间 |  | 应始终 **$default**。 |
> | 命名空间/队列 | 命名空间 | 1-260 | 字母数字、句点、连字符、下划线和斜杠。<br><br>字母数字开头和结尾。 |
> | 命名空间/队列/authorizationRules | queue | 1-50 | 字母数字、句点、连字符和下划线。<br><br>Alphnumeric 的开头和结尾。 |
> | 命名空间/主题 | 命名空间 | 1-260 | 字母数字、句点、连字符、下划线和斜杠。<br><br>字母数字开头和结尾。 |
> | 命名空间/主题/authorizationRules | 主题 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>Alphnumeric 的开头和结尾。 |
> | 命名空间/主题/订阅 | 主题 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>Alphnumeric 的开头和结尾。 |
> | 命名空间/主题/订阅/规则 | 订阅 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>Alphnumeric 的开头和结尾。 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | clusters | region | 4-23 | 小写字母、数字和连字符。<br><br>小写字母开头。 以小写字母或数字结尾。 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | SignalR | 全局 | 3-63 | 字母数字和连字符。<br><br>字母开头。 以字母或数字结尾。  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | managedInstances | 全局 | 1-63 | 小写字母、数字和连字符。<br><br>不能以连字符开头或结尾。 |
> | servers | 全局 | 1-63 | 小写字母、数字和连字符。<br><br>不能以连字符开头或结尾。 |
> | 服务器/数据库 | server | 1-128 | 无法使用：<br>`<>*%&:\/?`<br><br>不能以句点或空格结束。 |
> | 服务器/数据库/syncGroups | database | 1-150 | 字母数字、连字符和下划线。 |
> | 服务器/elasticPools | server | 1-128 | 无法使用：<br>`<>*%&:\/?`<br><br>不能以句点或空格结束。 |
> | 服务器/failoverGroups | 全局 | 1-63 | 小写字母、数字和连字符。<br><br>不能以连字符开头或结尾。 |
> | 服务器/firewallRules | server | 1-128 | 无法使用：<br>`<>*%&:;\/?`<br><br>不能以句点结束。 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | storageAccounts | 全局 | 3-24 | 小写字母和数字。 |
> | storageAccounts/blobServices | 存储帐户 |  | 必须是 `default`。 |
> | storageAccounts/blobServices/容器 | 存储帐户 | 3-63 | 小写字母、数字和连字符。<br><br>小写字母或数字开头。 不能使用连续的连字符。 |
> | storageAccounts/fileServices | 存储帐户 |  | 必须是 `default`。 |
> | storageAccounts/fileServices/共享 | 存储帐户 | 3-63 | 小写字母、数字和连字符。<br><br>不能以连字符开头或结尾。 不能使用连续的连字符。 |
> | storageAccounts/managementPolicies | 存储帐户 |  | 必须是 `default`。 |
> | blob | 容器 (container) | 1-1024 | 任何 URL 字符，区分大小写 |
> | queue | 存储帐户 | 3-63 | 小写字母、数字和连字符。<br><br>不能以连字符开头或结尾。 不能使用连续的连字符。 |
> | 表 | 存储帐户 | 3-63 | 构成.<br><br>字母开头。 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | storageSyncServices | 资源组 | 1-260 | 字母数字、空格、句点、连字符和下划线。<br><br>不能以句点或空格结束。 |
> | storageSyncServices / syncGroups | 存储同步服务 | 1-260 | 字母数字、空格、句点、连字符和下划线。<br><br>不能以句点或空格结束。 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | managers | 资源组 | 2-50 | 字母数字和连字符。<br><br>字母开头。 以字母数字结尾。 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | streamingjobs | 资源组 | 3-63 | 字母数字、连字符和下划线。 |
> | streamingjobs/函数 | 流式处理作业 | 3-63 | 字母数字、连字符和下划线。 |
> | streamingjobs/输入 | 流式处理作业 | 3-63 | 字母数字、连字符和下划线。 |
> | streamingjobs/输出 | 流式处理作业 | 3-63 | 字母数字、连字符和下划线。 |
> | streamingjobs/转换 | 流式处理作业 | 3-63 | 字母数字、连字符和下划线。 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | environments | 资源组 | 1-90 | 无法使用：<br>`'<>%&:\?/#` |
> | 环境/accessPolicies | 环境 | 1-90 | 无法使用：<br> `'<>%&:\?/#` |
> | 环境/eventSources | 环境 | 1-90 | 无法使用：<br>`'<>%&:\?/#` |
> | 环境/referenceDataSets | 环境 | 3-63 | 构成 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | serverfarms | 资源组 | 1-40 | 字母数字和连字符。 |
> | sites | 全局 | 2-60 | 包含字母数字和连字符。<br><br>不能以连字符开头或结尾。 |
> | 站点/槽 | site | 2-59 | 字母数字和连字符。 |

## <a name="next-steps"></a>后续步骤

有关如何命名资源的建议，请参阅[Ready：推荐命名和标记约定](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)。
