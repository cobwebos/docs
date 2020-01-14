---
title: Azure 安全控制-库存和资产管理
description: 安全控制清单和资产管理
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 2f24012a69a7673426644a8ded6162cd1021938e
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930062"
---
# <a name="security-control-inventory-and-asset-management"></a>安全控制：清单和资产管理

清单和资产管理建议侧重于解决与主动管理（清点、跟踪和更正）所有 Azure 资源相关的问题，以便仅向授权的资源授予访问权限，而未经授权和非托管资源是标识并删除。

## <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 资产发现

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 6.1 | 1.1、1.2、1.3、1.4、9.1、12。1 | 客户 |

使用 Azure 资源关系图查询/发现订阅中的所有资源（如计算、存储、网络、端口和协议等）。  请确保租户中的相应（读取）权限，并枚举所有 Azure 订阅以及订阅中的资源。

虽然可通过资源图发现经典 Azure 资源，但强烈建议创建和使用 Azure 资源管理器资源。

如何通过 Azure 资源关系图创建查询：

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何查看 Azure 订阅：

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

了解 Azure RBAC：

https://docs.microsoft.com/azure/role-based-access-control/overview

## <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 6.2 | 1.5 | 客户 |

将标记应用到提供元数据的 Azure 资源，以逻辑方式将它们组织到分类。

如何创建和使用标记：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="63-delete-unauthorized-azure-resources"></a>6.3：删除未经授权的 Azure 资源

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 6.3 | 1.6 | 客户 |

使用标记、管理组和单独的订阅（如果适用）来组织和跟踪资产。 定期协调清点，并确保及时地从订阅中删除未经授权的资源。

如何创建其他 Azure 订阅：

https://docs.microsoft.com/azure/billing/billing-create-subscription

如何创建管理组：

https://docs.microsoft.com/azure/governance/management-groups/create

如何创建和使用标记：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：维护已批准的 Azure 资源和软件标题的清单

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 6.4 | 2.1 | 客户 |

为计算资源定义已批准的 Azure 资源和批准的软件。

## <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未经批准的 Azure 资源

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 6.5 | 2.3、2.4 | 客户 |

使用 Azure 策略对可在订阅中创建的资源类型施加限制。

使用 Azure 资源关系图在其订阅中查询/发现资源。 &nbsp;确保环境中存在的所有 Azure 资源都已获得批准。

如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何通过 Azure Graph 创建查询：

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：监视计算资源中未经批准的软件应用程序

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 6.6 | 2.3/2。4 | 客户 |

使用 Azure 虚拟机库存自动收集有关虚拟机上所有软件的信息。 可从 Azure 门户获取软件名称、版本、发布者和刷新时间。 若要获取安装日期和其他信息的访问权限，请启用来宾级别诊断，并将 Windows 事件日志引入 Log Analytics 工作区。

如何启用 Azure 虚拟机清单：

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：删除未经批准的 Azure 资源和软件应用程序

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 6.7 | 2.5 | 客户 |

使用 Azure 安全中心的文件完整性监视（更改跟踪）和虚拟机清单来识别在虚拟机上安装的所有软件。 你可以实现自己的过程来删除未经授权的软件。 你还可以使用第三方解决方案来确定未批准的软件。

如何使用文件完整性监视：

https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring

了解 Azure 更改跟踪：

https://docs.microsoft.com/azure/automation/change-tracking

如何启用 Azure 虚拟机清单：

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="68-use-only-approved-applications"></a>6.8：仅使用批准的应用程序

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 6.8 | 2.6 | 客户 |

使用 Azure 安全中心自适应应用程序控制，以确保仅执行授权的软件，并且阻止所有未经授权的软件在 Azure 虚拟机上执行。

如何使用 Azure 安全中心自适应应用程序控件：

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="69-use-only-approved-azure-services"></a>6.9：仅使用批准的 Azure 服务

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 6.9 | 2.6 | 客户 |

使用 Azure 策略限制可在环境中预配的服务。

如何配置和管理 Azure 策略：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 策略拒绝特定的资源类型：

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

## <a name="610-implement-approved-application-list"></a>6.10：实现已批准的应用程序列表

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 6.1 | 2.7 | 客户 |

使用 Azure 安全中心自适应应用程序控件指定规则可以或不适用于的文件类型。

如果这不符合要求，则实现第三方解决方案。

如何使用 Azure 安全中心自适应应用程序控件：

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6.11：限制用户通过脚本与 Azure 资源管理器进行交互的能力

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 6.11 | 2.8 | 客户 |

使用 Azure 条件性访问，通过为 &quot;Microsoft Azure 管理&quot; 应用配置 &quot;阻止访问&quot;，限制用户与 Azure 资源管理器的交互能力。

如何配置条件访问以阻止访问 Azure 资源管理器：

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制用户在计算资源中执行脚本的能力

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 6.12 | 2.8 | 客户 |

使用特定于操作系统的配置或第三方资源来限制用户在 Azure 计算资源中执行脚本的能力。

例如，如何控制 Windows 环境中的 PowerShell 脚本执行：

https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：对高风险应用程序进行物理或逻辑分离

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 6.13 | 2.9 | 客户 |

业务运营所需的软件，但可能会对组织造成更高的风险，应在其自己的虚拟机和/或虚拟网络中隔离，并使用 Azure 防火墙或网络安全组进行充分的保护。

如何创建虚拟网络：

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

如何创建具有安全配置的 NSG：

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="next-steps"></a>后续步骤

请参阅下一安全控制：[安全配置](security-control-secure-configuration.md)
