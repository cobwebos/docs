---
title: 没有800计数限制的 Azure 资源
description: 列出资源组中可包含800个以上实例的 Azure 资源类型。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 7/30/2019
ms.author: tomfitz
ms.openlocfilehash: a796896450a5b786e3b78aeddd81e6d66b02eb94
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700452"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>资源不限于每个资源组800个实例

默认情况下, 可以在每个资源组中部署最多800个资源类型的实例。 但是, 某些资源类型不受800实例限制的限制。 本文列出了资源组中可包含800个以上实例的 Azure 资源类型。 所有其他资源类型限制为800实例。

对于某些资源类型, 需要联系支持人员以删除800实例限制。 本文中注明了这些资源类型。


## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* registrations
* registrations/customerSubscriptions
* registrations/products

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices-联系支持人员以扩展限制。

## <a name="microsoftcompute"></a>Microsoft.Compute

* 磁盘
* 映像
* 快照
* virtualMachines

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* registries/buildTasks
* 注册表/buildTasks/listSourceRepositoryProperties
* registries/buildTasks/steps
* 注册表/buildTasks/步骤/listBuildArguments
* registries/eventGridFilters
* registries/replications
* registries/tasks
* registries/webhooks

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* 服务器

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* 服务器

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* serverGroups
* 服务器
* serversv2

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* guestConfigurationAssignments
* software
* softwareUpdateProfile
* softwareUpdates

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* workflows

## <a name="microsoftnetwork"></a>Microsoft.Network

* applicationGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* bastionHosts
* ddosProtectionPlans
* dnszones
* dnszones/A
* dnszones/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* dnszones/all
* dnszones/recordsets
* networkIntentPolicies
* networkInterfaces
* privateDnsZones
* privateDnsZones/A
* privateDnsZones/AAAA
* privateDnsZones/CNAME
* privateDnsZones/MX
* privateDnsZones/PTR
* privateDnsZones/SOA
* privateDnsZones/SRV
* privateDnsZones/TXT
* privateDnsZones/所有
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* publicIPAddresses-联系支持人员以扩展限制。
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections-联系支持人员以扩展限制。

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* 站点

## <a name="next-steps"></a>后续步骤

有关配额和限制的完整列表, 请参阅[Azure 订阅和服务限制、配额和约束](../azure-subscription-service-limits.md)。
