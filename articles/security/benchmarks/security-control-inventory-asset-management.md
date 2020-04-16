---
title: Azure 安全控制 - 库存和资产管理
description: Azure 安全控制清单和资产管理
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: aece7278d134145e00b027c184f3817d5566e1e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408362"
---
# <a name="security-control-inventory-and-asset-management"></a>安全控制：库存和资产管理

清单和资产管理建议侧重于解决与主动管理（清单、跟踪和更正）所有 Azure 资源相关的问题，以便仅授予授权资源访问权限，并识别和删除未经授权的非托管资源。

## <a name="61-use-automated-asset-discovery-solution"></a>6.1： 使用自动资产发现解决方案

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 6.1 | 1.1, 1.2, 1.3, 1.4, 9.1, 12.1 | 客户 |

使用 Azure 资源图查询/发现订阅中的所有资源（如计算、存储、网络、端口和协议等）。  请确保租户中的适当（读取）权限，并枚举订阅中的所有 Azure 订阅以及资源。

尽管可以通过资源图发现经典 Azure 资源，但强烈建议今后创建和使用 Azure 资源管理器资源。

- [如何使用 Azure 资源图创建查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [如何查看 Azure 订阅](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [了解 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a name="62-maintain-asset-metadata"></a>6.2： 维护资产元数据

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 6.2 | 1.5 | 客户 |

将标记应用于 Azure 资源，使元数据以逻辑方式将它们组织到分类中。

- [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="63-delete-unauthorized-azure-resources"></a>6.3： 删除未经授权的 Azure 资源

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 6.3 | 1.6 | 客户 |

在适当情况下，使用标记、管理组和单独的订阅来组织和跟踪资产。 定期协调库存，确保及时从订阅中删除未经授权的资源。

- [如何创建其他 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [如何创建管理组](https://docs.microsoft.com/azure/governance/management-groups/create)

- [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定义和维护已批准的 Azure 资源清单

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 6.4 | 2.1 | 客户 |

根据我们的组织需求，创建已批准的 Azure 资源和已批准的软件清单，以便计算资源。

## <a name="65-monitor-for-unapproved-azure-resources"></a>6.5： 监视未经批准的 Azure 资源

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 6.5 | 2.3、2.4 | 客户 |

使用 Azure 策略对可在订阅中创建的资源类型施加限制。

使用 Azure 资源图查询/发现其订阅中的资源。  确保环境中的所有 Azure 资源都获得批准。

- [如何配置和管理 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [如何使用 Azure 图形创建查询](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6： 监控计算资源中未经批准的软件应用程序

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 6.6 | 2.3、2.4 | 客户 |

使用 Azure 虚拟机清单自动收集有关虚拟机上所有软件的信息。 软件名称、版本、发布者和刷新时间可从 Azure 门户获得。 要访问安装日期和其他信息，请启用来宾级诊断并将 Windows 事件日志引入日志分析工作区。

- [如何启用 Azure 虚拟机清单](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7： 删除未经批准的 Azure 资源和软件应用程序

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 6.7 | 2.5 | 客户 |

使用 Azure 安全中心的文件完整性监视（更改跟踪）和虚拟机清单标识安装在虚拟机上的所有软件。 您可以实现自己的删除未经授权的软件的过程。 您还可以使用第三方解决方案来识别未经批准的软件。

- [如何使用文件完整性监视](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring)

- [了解 Azure 更改跟踪](https://docs.microsoft.com/azure/automation/change-tracking)

- [如何启用 Azure 虚拟机清单](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

## <a name="68-use-only-approved-applications"></a>6.8： 仅使用已批准的应用程序

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 6.8 | 2.6 | 客户 |

使用 Azure 安全中心自适应应用程序控件确保仅执行授权软件，并且阻止在 Azure 虚拟机上执行所有未经授权的软件。

- [如何使用 Azure 安全中心自适应应用程序控件](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="69-use-only-approved-azure-services"></a>6.9： 仅使用已批准的 Azure 服务

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 6.9 | 2.6 | 客户 |

使用 Azure 策略限制可以在环境中预配哪些服务。

- [如何配置和管理 Azure 策略](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [如何使用 Azure 策略拒绝特定资源类型](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

## <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10： 维护已批准软件标题的清单

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 6.10 | 2.7 | 客户 |

使用 Azure 安全中心自适应应用程序控件指定规则可能应用于哪些文件类型，也可能不适用于哪些文件类型。

如果不符合要求，则实施第三方解决方案。

- [如何使用 Azure 安全中心自适应应用程序控件](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制用户与 Azure 资源管理器交互的能力

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 6.11 | 2.9 | 客户 |

使用 Azure 条件访问通过为"Microsoft Azure 管理"应用配置"阻止访问"来限制用户与 Azure 资源管理器交互的能力。

- [如何配置条件访问以阻止对 Azure 资源管理器的访问](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12： 限制用户在计算资源中执行脚本的能力

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 6.12 | 2.9 | 客户 |

根据脚本的类型，可以使用操作系统特定的配置或第三方资源来限制用户在 Azure 计算资源中执行脚本的能力。  您还可以利用 Azure 安全中心自适应应用程序控件来确保仅执行授权软件，并且阻止在 Azure 虚拟机上执行所有未经授权的软件。

- [如何在 Windows 环境中控制 PowerShell 脚本执行](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

- [如何使用 Azure 安全中心自适应应用程序控件](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13： 物理或逻辑上隔离高风险应用程序

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 6.13 | 2.9 | 客户 |

业务操作所需的软件，但可能会给组织带来更高的风险，应隔离在自己的虚拟机和/或虚拟网络中，并且使用 Azure 防火墙或网络安全组进行充分保护。

- [如何创建虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [如何创建具有安全配置的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)


## <a name="next-steps"></a>后续步骤

- 请参阅下一个安全控制：[安全配置](security-control-secure-configuration.md)