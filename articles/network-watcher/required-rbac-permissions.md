---
title: 使用 Azure 网络观察程序功能所需的权限 | Microsoft Docs
description: 了解使用网络观察程序功能需要哪些 Azure 基于角色的访问控制权限。
services: network-watcher
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: jdial
ms.openlocfilehash: 09f3a1e1d9c6796cb55ae8f0ab18bf8e1b3fa198
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077752"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>使用网络观察程序功能所需的基于角色的访问控制权限

通过 Azure 基于角色的访问控制 (RBAC)，你可以将具体操作仅分配给需要完成其分配的职责的组织成员。 若要使用网络观察程序功能，登录 Azure 所使用的帐户必须分配给[所有者](/role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner)、[参与者](/role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor)或[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor)内置角色，或分配给[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)（已向自定义角色分配了以下各节中为每个网络观察程序功能列出的操作）。 若要了解有关网络观察程序的功能的详细信息，请参阅[什么是网络观察程序？](network-watcher-monitoring-overview.md)。

## <a name="network-watcher"></a>网络观察程序

| 操作                                                              | 名称                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | 获取网络观察程序                                          |
| Microsoft.Network/networkWatchers/write                             | 创建或更新网络观察程序                             |
| Microsoft.Network/networkWatchers/delete                            | 删除网络观察程序                                       |

## <a name="nsg-flow-logs"></a>NSG 流日志

| 操作                                                              | 名称                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | 配置流日志                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | 流日志的查询状态                                    |

## <a name="connection-troubleshoot"></a>排查连接问题

| 操作                                                              | 名称                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | 连接故障排除测试的查询结果                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | 运行连接故障排除测试                             |

## <a name="connection-monitor"></a>连接监视器

| 操作                                                              | 名称                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | 启动连接监视器                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | 停止连接监视器                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | 查询连接监视器                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | 获取连接监视器                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | 创建连接监视器                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | 删除连接监视器                                    |

## <a name="packet-capture"></a>数据包捕获

| 操作                                                              | 名称                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | 查询数据包捕获的状态                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | 停止数据包捕获                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | 获取数据包捕获                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | 创建数据包捕获                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | 删除数据包捕获                                        |

## <a name="ip-flow-verify"></a>IP 流验证

| 操作                                                              | 名称                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | 验证 IP 流                                              |

## <a name="next-hop"></a>下一跃点

| 操作                                                              | 名称                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/action                    | 从 VM 获取下一跃点                                     |

## <a name="network-security-group-view"></a>网络安全组视图

| 操作                                                              | 名称                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | 查看安全组                                           |

## <a name="topology"></a>拓扑

| 操作                                                              | 名称                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topology/action                   | 获取拓扑                                                   |

## <a name="reachability-report"></a>可访问性报表

| 操作                                                              | 名称                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | 获取 Azure 可访问性报表                               |

## <a name="additional-actions"></a>其他操作

网络观察程序功能还需要以下操作：

- Microsoft.Storage/Read
- Microsoft.Authorization/Read
- Microsoft.Resources/subscriptions/resourceGroups/Read
- Microsoft.Storage/storageAccounts/listServiceSas/Action
- Microsoft.Storage/storageAccounts/listAccountSas/Action
- Microsoft.Storage/storageAccounts/listKeys/Action
- Microsoft.Compute/virtualMachines/Read
- Microsoft.Compute/virtualMachines/Write
- Microsoft.Compute/virtualMachineScaleSets/Read
- Microsoft.Compute/virtualMachineScaleSets/Write
- Microsoft.Insights/alertRules/*
- Microsoft.Support/*