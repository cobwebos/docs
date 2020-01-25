---
title: 没有800计数限制的资源
description: 列出资源组中可包含800个以上实例的 Azure 资源类型。
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 42e8ddeebcd1dda4fc67e4e7be137c5e01decdf4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715626"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>资源不限于每个资源组800个实例

默认情况下，可以在每个资源组中部署最多800个资源类型的实例。 但是，某些资源类型不受800实例限制的限制。 本文列出了资源组中可包含800个以上实例的 Azure 资源类型。 所有其他资源类型限制为800实例。

对于某些资源类型，需要联系支持人员以删除800实例限制。 本文中注明了这些资源类型。


## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* registrations
* registrations/customerSubscriptions
* registrations/products
* verificationKeys

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices-默认情况下，限于800实例。 可以通过联系支持人员来增加该限制。

## <a name="microsoftcompute"></a>Microsoft.Compute

* disks
* images
* 스냅샷
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroups

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

* 서버

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* 서버

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* serverGroups
* 서버
* serversv2

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft.EventHub

* clusters
* 네임스페이스

## <a name="microsoftexperimentation"></a>Microsoft 试验

* experimentWorkspaces

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* autoManagedVmConfigurationProfiles
* configurationProfileAssignments
* guestConfigurationAssignments
* software
* softwareUpdateProfile
* softwareUpdates

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* workflows

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/卷
* netAppAccounts/capacityPools/卷集/mountTargets
* netAppAccounts/capacityPools/卷/快照

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
* publicIPAddresses-默认情况下，限于800实例。 可以通过联系支持人员来增加该限制。
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections-默认情况下，限于800实例。 可以通过联系支持人员来增加该限制。

## <a name="microsoftrelay"></a>Microsoft.Relay

* 네임스페이스

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* 네임스페이스

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

* 애플리케이션
* containerGroups
* gateways
* networks
* secrets
* volumes

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* sites

## <a name="microsoftinsights"></a>microsoft.insights

* metricalerts

## <a name="next-steps"></a>다음 단계

有关配额和限制的完整列表，请参阅[Azure 订阅和服务限制、配额和约束](azure-subscription-service-limits.md)。
