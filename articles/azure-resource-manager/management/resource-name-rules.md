---
title: 资源命名限制
description: 显示命名 Azure 资源的规则和限制。
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: f6203f987654b33b32da72bfec030a9d0ab69df8
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80981522"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Azure 资源的命名规则和限制

本文总结了 Azure 资源的命名规则和限制。 有关如何命名资源的建议，请参阅[建议命名和标记约定](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)。

除非在有效字符列中明确注明，否则资源名称不区分大小写。

在下表中，术语字母数字是指：

* **a**到**z（** 小写字母）
* **A**到**Z（** 大写字母）
* **0**到**9** （数字）

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | servers | 资源组 | 3-63 | 小写字母和数字。<br><br>从小写字母开始。 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | 服务 | global | 1-50 | 字母数字。<br><br>从字母开始。 |
> | 服务 / apis | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务 / apis / 问题 | api | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务 / apis / 问题 / 附件 | 问题 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务 / apis / 问题 / 评论 | 问题 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务 / apis / 操作 | api | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务 / apis / 操作 / 标签 | operation | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务 / apis / 版本 | api | 1-80 | 字母数字、下划线和连字符。<br><br>以字母数字或下划线开始和结束。 |
> | 服务 / apis / 架构 | api | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务 / apis / 标签描述 | api | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务 / apis / 标签 | api | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/api 版本集 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/授权服务器 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/后端 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/证书 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/诊断 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/组 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务 / 组 / 用户 | group | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/身份提供者 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/记录器 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/通知 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务 / 通知 / 收件人电子邮件 | 通知 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务 / 开放连接提供商 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/策略 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/产品 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务 / 产品 / 产品 | product | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务 / 产品 / 组 | product | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务 / 产品 / 标签 | product | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/属性 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/订阅 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/标签 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/模板 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |
> | 服务/用户 | 服务 | 1-256 | 无法使用：<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | 配置存储 | 资源组 | 5-50 | 字母数字、下划线和连字符。 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | 锁定 | 分配范围 | 1-90 | 字母数字、句点、下划线、连字符和括号。<br><br>不能在期间结束。 |
> | policyassignments | 分配范围 | 1-128 显示名称<br><br>1-260 资源名称 | 显示名称可以包含任何字符。<br><br>资源名称不能包含`%`，也不能以期间或空格结尾。 |
> | 策略定义 | 定义范围 | 1-128 显示名称<br><br>1-260 资源名称 | 显示名称可以包含任何字符。<br><br>资源名称不能包含`%`，也不能以期间或空格结尾。 |
> | policySetDefinitions | 定义范围 | 1-128 显示名称<br><br>1-260 资源名称 | 显示名称可以包含任何字符。<br><br>资源名称不能包含`%`，也不能以期间或空格结尾。  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | automationAccounts | 资源组 | 6-50 | 字母数字和连字符。<br><br>以字母开头，以字母数字结尾。 |
> | 自动化帐户/证书 | 自动化帐户 | 1-128 | 无法使用：<br> `<>*%&:\?.+/` <br><br>不能以空间结束。  |
> | 自动化账户/连接 | 自动化帐户 | 1-128 | 无法使用：<br> `<>*%&:\?.+/` <br><br>不能以空间结束。 |
> | 自动化帐户/凭据 | 自动化帐户 | 1-128 | 无法使用：<br> `<>*%&:\?.+/` <br><br>不能以空间结束。 |
> | automationAccounts / runbooks | 自动化帐户 | 1-63 | 字母数字、下划线和连字符。<br><br>从字母开始。  |
> | 自动化帐户/时间表 | 自动化帐户 | 1-128 | 无法使用：<br> `<>*%&:\?.+/` <br><br>不能以空间结束。 |
> | 自动化帐户/变量 | 自动化帐户 | 1-128 | 无法使用：<br> `<>*%&:\?.+/` <br><br>不能以空间结束。 |
> | 自动化帐户/观察程序 | 自动化帐户 | 1-63 |  字母数字、下划线和连字符。<br><br>从字母开始。 |
> | automationAccounts / webhooks | 自动化帐户 | 1-128 | 无法使用：<br> `<>*%&:\?.+/` <br><br>不能以空间结束。 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | batchAccounts | 区域 | 3-24 | 小写字母和数字。 |
> | 批处理帐/应用程序 | 批次帐户 | 1-64 | 字母数字、下划线和连字符。 |
> | 批处理帐/证书 | 批次帐户 | 5-45 | 字母数字、下划线和连字符。 |
> | 批处理帐户/池 | 批次帐户 | 1-64 | 字母数字、下划线和连字符。 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | 区块链成员 | global | 2-20 | 小写字母和数字。<br><br>从小写字母开始。 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | botServices | global | 2-64 |  字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 |
> | 机器人服务/频道 | 机器人服务 | 2-64 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 |
> | 机器人服务 / 连接 | 机器人服务 | 2-64 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 |
> | 企业渠道 | 资源组 | 2-64 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | Redis | global | 1-63 | 字母数字和连字符。<br><br>以字母数字开始和结束。 不允许连续连字符。 |
> | 雷瑞斯 / 防火墙规则 | Redis | 1-256 | 字母数字 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | 配置文件 | 资源组 | 1-260 | 字母数字和连字符。<br><br>以字母数字开始和结束。 |
> | profiles/endpoints | global | 1-50 | 字母数字和连字符。<br><br>以字母数字开始和结束。 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | certificateOrders | 资源组 | 3-30 | 字母数字。 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | accounts | 资源组 | 2-64 | 字母数字和连字符。<br><br>以字母数字开始和结束。 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | availabilitySets | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结束。 |
> | diskEncryptionSets | 资源组 | 1-80 | 字母数字和下划线。 |
> | disks | 资源组 | 1-80 | 字母数字和下划线。 |
> | galleries | 资源组 | 1-80 | 字母数字和句点。<br><br>以字母数字开始和结束。 |
> | galleries / applications | 库 | 1-80 | 字母数字、连字符和句点。<br><br>以字母数字开始和结束。 |
> | 画廊 /应用程序/版本 | application | 32-bit integer | 数字和期间。 |
> | galleries/images | 库 | 1-80 | 字母数字、连字符和句点。<br><br>以字母数字开始和结束。 |
> | galleries/images/versions | image | 32-bit integer | 数字和期间。 |
> | images | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结束。 |
> | snapshots | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结束。 |
> | virtualMachines | 资源组 | 1-15 （视窗）<br>1-64 （Linux）<br><br>请参见下面的注释。 | 无法使用：<br> `\/""[]:|<>+=;,?*@&`<br><br>不能从下划线开始。 不能以句点或连字符结束。 |
> | virtualMachineScaleSets | 资源组 | 1-15 （视窗）<br>1-64 （Linux）<br><br>请参见下面的注释。 | 无法使用：<br> `\/""[]:|<>+=;,?*@&`<br><br>不能从下划线开始。 不能以句点或连字符结束。 |

> [!NOTE]
> Azure 虚拟机有两个不同的名称：资源名称和主机名。 在门户中创建虚拟机时，两个名称使用相同的值。 上表中的限制适用于主机名。 实际资源名称最多可包含 64 个字符。

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | containerGroups | 资源组 | 1-63 | 小写字母、数字和连字符。<br><br>不能以连字符开始或结尾。 不允许使用连续的连字符。 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | registries | global | 5-50 | 字母数字。 |
> | registries / buildTasks | 注册表 | 5-50 | 字母数字。 |
> | 注册表/生成任务/步骤 | 生成任务 | 5-50 | 字母数字。 |
> | registries/replications | 注册表 | 5-50 | 字母数字。 |
> | registries / scopeMaps | 注册表 | 5-50 | 字母数字、连字符和下划线。 |
> | registries/tasks | 注册表 | 5-50 | 字母数字、连字符和下划线。 |
> | registries / tokens | 注册表 | 5-50 | 字母数字、连字符和下划线。 |
> | registries/webhooks | 注册表 | 5-50 | 字母数字。 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | managedClusters | 资源组 | 1-63 | 字母数字、下划线和连字符。<br><br>以字母数字开始和结束。 |
> | openShiftManagedClusters | 资源组 | 1-30 | 字母数字。 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | hubs | 资源组 | 1-64 | 字母数字。<br><br>从字母开始。  |
> | 集线器/授权政策 | 中心 | 1-50 | 字母数字、下划线和句点。<br><br>以字母数字开始和结束。 |
> | 集线器/连接器 | 中心 | 1-128 | 字母数字和下划线。<br><br>从字母开始。 |
> | 集线器/连接器/映射 | 连接器 | 1-128 | 字母数字和下划线。<br><br>从字母开始。 |
> | 集线器/交互 | 中心 | 1-128 | 字母数字和下划线。<br><br>从字母开始。 |
> | 集线器 / kpi | 中心 | 1-512 | 字母数字和下划线。<br><br>从字母开始。 |
> | 集线器/链接 | 中心 | 1-512 | 字母数字和下划线。<br><br>从字母开始。 |
> | 集线器/预测 | 中心 | 1-512 | 字母数字和下划线。<br><br>从字母开始。 |
> | 集线器/配置文件 | 中心 | 1-128 | 字母数字和下划线。<br><br>从字母开始。 |
> | 集线器/关系链接 | 中心 | 1-512 | 字母数字和下划线。<br><br>从字母开始。 |
> | 集线器/关系 | 中心 | 1-512 | 字母数字和下划线。<br><br>从字母开始。 |
> | 集线器/角色分配 | 中心 | 1-128 | 字母数字和下划线。<br><br>从字母开始。 |
> | 集线器/视图 | 中心 | 1-512 | 字母数字和下划线。<br><br>从字母开始。 |

## <a name="microsoftcustomproviders"></a>微软.自定义提供程序

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | 关联 | 资源组 | 1-180 | 无法使用：<br>`%&\\?/`<br><br>不能以句点或空格结束。 |
> | 资源提供者 | 资源组 | 3-64 | 无法使用：<br>`%&\\?/`<br><br>不能以句点或空格结束。 |

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
> | factories | global | 3-63 | 字母数字和连字符。<br><br>以字母数字开始和结束。 |
> | 工厂/数据流 | 工厂 | 1-260 | 无法使用：<br>`<>*#.%&:\\+?/`<br><br>以字母数字开头。 |
> | 工厂/数据集 | 工厂 | 1-260 | 无法使用：<br>`<>*#.%&:\\+?/`<br><br>以字母数字开头。 |
> | factories / integrationRuntimes | 工厂 | 3-63 | 字母数字和连字符。<br><br>以字母数字开始和结束。 |
> | 工厂/链接服务 | 工厂 | 1-260 | 无法使用：<br>`<>*#.%&:\\+?/`<br><br>以字母数字开头。 |
> | 工厂/管道 | 工厂 | 1-260 | 无法使用：<br>`<>*#.%&:\\+?/`<br><br>以字母数字开头。 |
> | 工厂/触发器 | 工厂 | 1-260 | 无法使用：<br>`<>*#.%&:\\+?/`<br><br>以字母数字开头。 |
> | 工厂 / 触发器 / 重新运行触发器 | 触发器 | 1-260 | 无法使用：<br>`<>*#.%&:\\+?/`<br><br>以字母数字开头。 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | accounts | global | 3-24 | 小写字母和数字。 |
> | 帐户/计算策略 | account | 3-60 | 字母数字、连字符和下划线。 |
> | 帐户/数据湖存储帐户 | account | 3-24 | 小写字母和数字。 |
> | 帐户/防火墙规则 | account | 3-50 | 字母数字、连字符和下划线。 |
> | 帐户/存储帐户 | account | 3-60 | 字母数字、连字符和下划线。 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | accounts | global | 3-24 | 小写字母和数字。 |
> | 帐户/防火墙规则 | account | 3-50 | 字母数字、连字符和下划线。 |
> | 帐户/虚拟网络规则 | account | 3-50 | 字母数字、连字符和下划线。 |

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
> | servers | global | 3-63 | 小写字母、连字符和数字。<br><br>不能以连字符开始或结尾。 |
> | servers/databases | servers | 1-63 | 字母数字和连字符。 |
> | servers / firewallRules | servers | 1-128 | 字母数字、连字符和下划线。 |
> | servers / virtualNetworkRules | servers | 1-128 | 字母数字和连字符。 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | servers | global | 3-63 | 小写字母、连字符和数字。<br><br>不能以连字符开始或结尾。 |
> | servers/databases | servers | 1-63 | 字母数字和连字符。 |
> | servers / firewallRules | servers | 1-128 | 字母数字、连字符和下划线。 |
> | servers / virtualNetworkRules | servers | 1-128 | 字母数字和连字符。 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | servers | global | 3-63 | 小写字母、连字符和数字。<br><br>不能以连字符开始或结尾。 |
> | servers/databases | servers | 1-63 | 字母数字和连字符。 |
> | servers / firewallRules | servers | 1-128 | 字母数字、连字符和下划线。 |
> | servers / virtualNetworkRules | servers | 1-128 | 字母数字和连字符。 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | IotHubs | global | 3-50 | 字母数字和连字符。<br><br>不能以连字符结束。 |
> | IotHubs / 证书 | 物联网中心 | 1-64 | 字母数字、连字符、句点和下划线。 |
> | IotHubs / 事件 HubPoints / 消费者群体 | 事件 HubEndpoints | 1-50 | 字母数字、连字符、句点和下划线。 |
> | 预配服务 | 资源组 | 3-64 | 字母数字和连字符。<br><br>以字母数字结束。 |
> | 预配服务/证书 | 预配服务 | 1-64 | 字母数字、连字符、句点和下划线。 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | labs | 资源组 | 1-50 | 字母数字、下划线和连字符。 |
> | 实验室/自定义图像 | 实验室 | 1-80 | 字母数字、下划线、连字符和括号。 |
> | 实验室/配方 | 实验室 | 1-80 | 字母数字、下划线、连字符和括号。 |
> | 实验室/虚拟机 | 实验室 | 1-15 （视窗）<br>1-64 （Linux） | 字母数字和连字符。<br><br>以字母数字开始和结束。 不可能是所有数字。 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | databaseAccounts | global | 3-31 | 小写字母、数字和连字符。<br><br>从小写字母或数字开始。 |

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
> | clusters | 资源组 | 6-50 | 字母数字和连字符。<br><br>从字母开始。 以字母或数字结尾。 |
> | namespaces | global | 6-50 | 字母数字和连字符。<br><br>从字母开始。 以字母或数字结尾。 |
> | 命名空间/授权规则 | namespace | 1-50 | 字母数字、句点、连字符和下划线。<br><br>以字母或数字开头和结尾。 |
> | 命名空间 / 灾难恢复配置 | namespace | 1-50 | 字母数字、句点、连字符和下划线。<br><br>以字母或数字开头和结尾。 |
> | namespaces / eventhubs | namespace | 1-50 | 字母数字、句点、连字符和下划线。<br><br>以字母或数字开头和结尾。 |
> | 命名空间 / 事件中心 / 授权规则 | 事件中心 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>以字母或数字开头和结尾。 |
> | namespaces / eventhubs / consumergroups | 事件中心 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>以字母或数字开头和结尾。 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | clusters | global | 3-59 | 字母数字和连字符<br><br>以字母或数字开头和结尾。 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | jobs | 资源组 | 2-64 | 字母数字和连字符。<br><br>从字母开始。 |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | components | 资源组 | 1-260 | 无法使用：<br>`%&\?/` <br><br>不能以空格或句点结束。  |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | IoTApps | global | 2-63 | 小写字母、数字和连字符。<br><br>从小写字母或数字开始。 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | vaults | global | 3-24 | 字母数字和连字符。<br><br>从字母开始。 以字母或数字结尾。 不能包含连续连字符。 |
> | vaults / secrets | 保管库 | 1-127 | 字母数字和连字符。 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | clusters | global | 4-22 | 小写字母和数字。<br><br>从字母开始。 |
> | /群集 / 数据库 | cluster | 1-260 | 字母数字、连字符、空格和句点。 |
> | /群集 / 数据库 / 数据连接 | database | 1-40 | 字母数字、连字符、空格和句点。 |
> | /群集 / 数据库 / 事件点连接 | database | 1-40 | 字母数字、连字符、空格和句点。 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | integrationAccounts | 资源组 | 1-80 | 字母数字、连字符、下划线、句点和括号。 |
> | 集成帐户/程序集 | 集成帐户 | 1-80 | 字母数字、连字符、下划线、句点和括号。 |
> | 集成帐户/批处理配置 | 集成帐户 | 1-20 | 字母数字。 |
> | 集成帐户/证书 | 集成帐户 | 1-80 | 字母数字、连字符、下划线、句点和括号。 |
> | 集成帐户/地图 | 集成帐户 | 1-80 | 字母数字、连字符、下划线、句点和括号。 |
> | 集成帐户/合作伙伴 | 集成帐户 | 1-80 | 字母数字、连字符、下划线、句点和括号。 |
> | 集成帐户/罗塞塔内程序配置 | 集成帐户 | 1-80 | 字母数字、连字符、下划线、句点和括号。 |
> | 集成帐户/架构 | 集成帐户 | 1-80 | 字母数字、连字符、下划线、句点和括号。 |
> | 集成帐户/会话 | 集成帐户 | 1-80 | 字母数字、连字符、下划线、句点和括号。 |
> | integrationServiceEnvironments | 资源组 | 1-80 | 字母数字、连字符、句点和下划线。 |
> | integrationServiceEnvironments / managedApis | 集成服务环境 | 1-80 | 字母数字、连字符、句点和下划线。 |
> | workflows | 资源组 | 1-80 | 字母数字、连字符、下划线、句点和括号。 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | commitmentPlans | 资源组 | 1-260 | 无法使用：<br>`<>*%&:?+/\\`<br><br>不能以空格结束。 |
> | webServices | 资源组 | 1-260 | 无法使用：<br>`<>*%&:?+/\\`<br><br>不能以空格结束。 |
> | workspaces | 资源组 | 1-260 | 无法使用：<br>`<>*%&:?+/\\`<br><br>不能以空格结束。 |

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
> | userAssignedIdentities | 资源组 | 3-128 | 字母数字、连字符和下划线<br><br>以字母或数字开头。 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | accounts | 资源组 | 1-98（资源组名称和帐户名称） | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | mediaservices | 资源组 | 3-24 | 小写字母和数字。 |
> | mediaservices / liveEvents | 媒体服务 | 1-32 | 字母数字和连字符。<br><br>以字母数字开头。 |
> | mediaservices / liveEvents / liveOutputs | 现场活动 | 1-256 | 字母数字和连字符。<br><br>以字母数字开头。 |
> | mediaservices / streamingEndpoints | 媒体服务 | 1-24 | 字母数字和连字符。<br><br>以字母数字开头。 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | applicationGateways | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | applicationSecurityGroups | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | azureFirewalls | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 以字母数字或下划线结束。 |
> | bastionHosts | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | connections | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | dns区域 | 资源组 | 1-63 个字符<br><br>2 到 34 个标签<br><br>每个标签都是一组字符，由句点分隔。 例如 **，contoso.com**有 2 个标签。 | 每个标签可以包含字母数字、下划线和连字符。<br><br>每个标签由一个句点分隔。 |
> | expressRouteCircuits | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | firewallPolicies | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | 防火墙策略/规则组 | 防火墙策略 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | 前门 | global | 5-64 | 字母数字和连字符。<br><br>以字母数字开始和结束。 |
> | loadBalancers | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | 负载平衡器/入站Nat规则 | 负载均衡器 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | localNetworkGateways | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | networkInterfaces | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | networkSecurityGroups | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | 网络安全组/安全规则 | 网络安全组 | 1-80 |  字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | networkWatchers | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | privateDnsZones | 资源组 | 1-63 个字符<br><br>2 到 34 个标签<br><br>每个标签都是一组字符，由句点分隔。 例如 **，contoso.com**有 2 个标签。 | 每个标签可以包含字母数字、下划线和连字符。<br><br>每个标签由一个句点分隔。 |
> | privateDnsZones / virtualNetworkLinks | 专用 DNS 区域 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | publicIPAddresses | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | publicIPPrefixes | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | routeFilters | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | 路由筛选器/路由筛选器规则 | 路由筛选器 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | routeTables | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | 路由表/ 路由 | 路由表 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | serviceEndpointPolicies | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | trafficmanagerprofiles | global | 1-63 | 字母数字、连字符和句点。<br><br>以字母数字开始和结束。 |
> | virtualNetworkGateways | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | virtualNetworks | 资源组 | 2-64 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | 虚拟网络/子网 | 虚拟网络 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | virtualNetworks / virtualNetworkPeerings | 虚拟网络 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | virtualWans | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | vpnGateways | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | vpn网关 / vpn连接 | VPN 网关 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |
> | vpnSites | 资源组 | 1-80 | 字母数字、下划线、句点和连字符。<br><br>以字母数字开头。 结束字母数字或下划线。 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | namespaces | global | 6-50 | 字母数字和连字符<br><br>以字母数字开始和结束。 |
> | 命名空间/授权规则 | namespace | 1-256 | 字母数字、句点、连字符和下划线。<br><br>开始字母数字。 |
> | namespaces / notificationHubs | namespace | 1-260 | 字母数字、句点、连字符和下划线。<br><br>开始字母数字。 |
> | 命名空间 / 通知中心 / 授权规则 | 通知中心 | 1-256 | 字母数字、句点、连字符和下划线。<br><br>开始字母数字。 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | clusters | 资源组 | 4-63 | 字母数字和连字符。<br><br>以字母数字开始和结束。 |
> | workspaces | 资源组 | 4-63 | 字母数字和连字符。<br><br>以字母数字开始和结束。 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | dashboards | 资源组 | 3-160 | 字母数字和连字符。<br><br>要使用受限字符，请使用要使用的仪表板名称添加名为**隐藏标题**的标记。 显示仪表板时，门户将显示该名称。 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | workspaceCollections | region | 3-63 | 字母数字和连字符。<br><br>不能以连字符开头。 不能使用连续连字符。 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | capacities | region | 3-63 | 小写字母或数字<br><br>从小写字母开始。 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | vaults | 资源组 | 2-50 | 字母数字和连字符。<br><br>从字母开始。 |
> | 保管库/备份策略 | 保管库 | 3-150 | 字母数字和连字符。<br><br>从字母开始。 不能以连字符结束。 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | namespaces | global | 6-50 | 字母数字和连字符。<br><br>以字母开头。 以字母或数字结尾。 |
> | 命名空间/授权规则 | namespace | 1-50 |  字母数字、句点、连字符和下划线。<br><br>以字母数字开始和结束。 |
> | 命名空间/混合连接 | namespace | 1-260 | 字母数字、句点、连字符、下划线和斜杠。<br><br>以字母数字开始和结束。 |
> | 命名空间 / 混合连接/授权规则 | 混合连接 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>以字母数字开始和结束。 |
> | 命名空间 / WcfRelay | namespace | 1-260 | 字母数字、句点、连字符、下划线和斜杠。<br><br>以字母数字开始和结束。 |
> | 命名空间 / WcfRelay / 授权规则 | Wcf 继电器 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>以字母数字开始和结束。 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | deployments | 资源组 | 1-64 | 字母数字、下划线、括号、连字符和句点。 |
> | 资源组 | 订阅 | 1-90 | 与[regex 文档](/rest/api/resources/resourcegroups/createorupdate)匹配的字母数字、下划线、括号、连字符、句点和单码字符。<br><br>不能以句点结束。 |
> | 标签名称 | resource | 1-512 | 无法使用：<br>`<>%&\?/` |
> | 标记名称/标记值 | 标记名称 | 1-256 | 全部字符。 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | namespaces | global | 6-50 | 字母数字和连字符。<br><br>以字母开头。 以字母或数字结尾。<br><br>有关详细信息，请参阅[创建命名空间](/rest/api/servicebus/create-namespace)。 |
> | 命名空间/授权规则 | namespace | 1-50 | 字母数字、句点、连字符和下划线。<br><br>以数字开始和结束。 |
> | 命名空间 / 灾难恢复配置 | global | 6-50 | 字母数字和连字符。<br><br>从字母开始。 以字母数字结束。 |
> | 命名空间/迁移配置 | namespace |  | 应始终 **$default。** |
> | namespaces / queues | namespace | 1-260 | 字母数字、句点、连字符、下划线和斜杠。<br><br>以字母数字开始和结束。 |
> | 命名空间/ 队列 / 授权规则 | queue | 1-50 | 字母数字、句点、连字符和下划线。<br><br>以数字开始和结束。 |
> | namespaces / topics | namespace | 1-260 | 字母数字、句点、连字符、下划线和斜杠。<br><br>以字母数字开始和结束。 |
> | 命名空间/ 主题 / 授权规则 | 主题 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>以数字开始和结束。 |
> | namespaces / topics / subscriptions | 主题 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>以数字开始和结束。 |
> | namespaces / topics / subscriptions / rules | 订阅 | 1-50 | 字母数字、句点、连字符和下划线。<br><br>以数字开始和结束。 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | clusters | region | 4-23 | 小写字母、数字和连字符。<br><br>从小写字母开始。 以小写字母或数字结尾。 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | 信号R | global | 3-63 | 字母数字和连字符。<br><br>从字母开始。 以字母或数字结尾。  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | managedInstances | global | 1-63 | 小写字母、数字和连字符。<br><br>不能以连字符开始或结尾。 |
> | servers | global | 1-63 | 小写字母、数字和连字符。<br><br>不能以连字符开始或结尾。 |
> | servers / administrators | server |  | 必须是 `ActiveDirectory`。 |
> | servers/databases | server | 1-128 | 无法使用：<br>`<>*%&:\/?`<br><br>不能以句点或空格结束。 |
> | 服务器/ 数据库 / 同步组 | database | 1-150 | 字母数字、连字符和下划线。 |
> | 服务器/弹性池 | server | 1-128 | 无法使用：<br>`<>*%&:\/?`<br><br>不能以句点或空格结束。 |
> | 服务器/故障转移组 | global | 1-63 | 小写字母、数字和连字符。<br><br>不能以连字符开始或结尾。 |
> | servers / firewallRules | server | 1-128 | 无法使用：<br>`<>*%&:;\/?`<br><br>不能以句点结束。 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | storageAccounts | global | 3-24 | 小写字母和数字。 |
> | storageAccounts / blobServices | 存储帐户 |  | 必须是 `default`。 |
> | 存储帐户 / blob 服务 / 容器 | 存储帐户 | 3-63 | 小写字母、数字和连字符。<br><br>从小写字母或数字开始。 不能使用连续连字符。 |
> | storageAccounts / fileServices | 存储帐户 |  | 必须是 `default`。 |
> | 存储帐户 / 文件服务 / 共享 | 存储帐户 | 3-63 | 小写字母、数字和连字符。<br><br>不能以连字符开始或结尾。 不能使用连续连字符。 |
> | 存储帐户/管理策略 | 存储帐户 |  | 必须是 `default`。 |
> | blob | 容器 (container) | 1-1024 | 任何 URL 字符，大小写敏感 |
> | queue | 存储帐户 | 3-63 | 小写字母、数字和连字符。<br><br>不能以连字符开始或结尾。 不能使用连续连字符。 |
> | 表 | 存储帐户 | 3-63 | 字母数字。<br><br>从字母开始。 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | storageSyncServices | 资源组 | 1-260 | 字母数字、空格、句点、连字符和下划线。<br><br>不能以句点或空格结束。 |
> | 存储同步服务/同步组 | 存储同步服务 | 1-260 | 字母数字、空格、句点、连字符和下划线。<br><br>不能以句点或空格结束。 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | managers | 资源组 | 2-50 | 字母数字和连字符。<br><br>从字母开始。 以字母数字结束。 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | streamingjobs | 资源组 | 3-63 | 字母数字、连字符和下划线。 |
> | 流式处理/函数 | 流作业 | 3-63 | 字母数字、连字符和下划线。 |
> | 流式处理/输入 | 流作业 | 3-63 | 字母数字、连字符和下划线。 |
> | 流式处理/输出 | 流作业 | 3-63 | 字母数字、连字符和下划线。 |
> | 流式处理/转换 | 流作业 | 3-63 | 字母数字、连字符和下划线。 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | environments | 资源组 | 1-90 | 无法使用：<br>`'<>%&:\?/#` |
> | environments / accessPolicies | 环境 | 1-90 | 无法使用：<br> `'<>%&:\?/#` |
> | 环境/事件源 | 环境 | 1-90 | 无法使用：<br>`'<>%&:\?/#` |
> | environments / referenceDataSets | 环境 | 3-63 | 字母数字 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 实体 | 范围 | 长度 | 有效的字符 |
> | --- | --- | --- | --- |
> | serverfarms | 资源组 | 1-40 | 字母数字和连字符。 |
> | sites | global | 2-60 | 包含字母数字和连字符。<br><br>不能以连字符开始或结尾。 |
> | sites/slots | site | 2-59 | 字母数字和连字符。 |

## <a name="next-steps"></a>后续步骤

有关如何命名资源的建议，请参阅["就绪：建议命名和标记约定](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)"。
