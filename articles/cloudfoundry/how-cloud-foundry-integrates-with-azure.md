---
title: 如何将 Cloud Foundry 与 Azure 集成 | Microsoft Docs
description: 介绍 Cloud Foundry 如何利用 Azure 服务来增强企业体验
services: virtual-machines-linux
documentationcenter: ''
author: ningk
manager: jeconnoc
editor: ''
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: 689730edcc98a23c82373ae8d36c3b831b33c076
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627434"
---
# <a name="integrate-cloud-foundry-with-azure"></a>将 Cloud Foundry 与 Azure 集成

[Cloud Foundry](https://docs.cloudfoundry.org/) 是在云提供程序的 IaaS 平台顶层运行的 PaaS 平台。 它跨云提供程序提供一致的应用程序部署体验。 此外，它还能与各种 Azure 服务集成，提供企业级的高可用性、可伸缩性和成本节省。
[Cloud Foundry 有 6 个可在线灵活缩放的子系统](https://docs.cloudfoundry.org/concepts/architecture/)，包括：路由、身份验证、应用程序生命周期管理、服务管理、消息传递和监视。 对于每个子系统，可将 Cloud Foundry 配置为利用相应的 Azure 服务。 

![Azure 集成体系结构上的 Cloud Foundry](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1.高可用性和可伸缩性
### <a name="managed-disk"></a>托管磁盘
Bosh 利用针对磁盘的 Azure CPI（云提供程序接口）来创建和删除例程。 默认使用非托管磁盘。 客户需要手动创建存储帐户，然后在 CF 清单文件中配置帐户。 这是因为每个存储帐户的磁盘数有限制。
[托管磁盘](https://azure.microsoft.com/services/managed-disks/)现已推出，它为虚拟机提供受管的安全可靠磁盘存储。 客户不再需要出于可伸缩性和高可用性目的来处理存储帐户。 Azure 会自动排列磁盘。 无论是对于新部署还是现有部署，Azure CPI 都会在 CF 部署期间处理托管磁盘的创建或迁移。 PCF 1.11 支持此功能。 也可以浏览开源 Cloud Foundry 的[托管磁盘指南](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks)来获得参考。 
### <a name="availability-zone-"></a>可用性区域 *
作为云原生的应用程序平台，Cloud Foundry 在设计上具有[四个高可用性级别](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html)。 尽管 CF 系统本身能够处理前三级软件故障，但云提供程序仍然提供平台容错。 应使用云提供程序的平台高可用性解决方案来保护关键的 CF 组件。 这些组件包括 GoRouters、Diego Brain、CF 数据库和服务磁贴。 默认情况下，[Azure 可用性集](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets)用于数据中心内群集之间的容错。
好消息是，[Azure 可用性区域](https://docs.microsoft.com/azure/availability-zones/az-overview )现已发布，它可以实现跨数据中心的低延迟冗余，将容错能力进一步提升。
Azure 可用性区域通过将 VM 集放入 2 个以上的数据中心，并使每个 VM 集与其他集相互冗余，以此实现高可用性。 如果一个区域发生故障，其他 VM 集仍会保持活动状态，避免受到灾难的影响。
> [!NOTE] 
> Azure 可用性区域尚未在所有区域推出。请查看最新的[受支持区域列表公告](https://docs.microsoft.com/azure/availability-zones/az-overview)。 对于开源 Cloud Foundry，请查看[适用于开源 Cloud Foundry 的 Azure 可用性区域指南](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone)。

## <a name="2-network-routing"></a>2.网络路由
默认情况下，Azure 基本负载均衡器用于传入的 CF API/应用请求，并将请求转发到 Gorouters。 Diego Brain、MySQL、ERT 等 CF 组件也可以使用负载均衡器来均衡流量，以实现高可用性。 此外，Azure 提供一套完全托管的负载均衡解决方案。 若要寻求 TLS 终止（“SSL 卸载”）或每个 HTTP/HTTPS 请求的应用层处理，请考虑使用应用程序网关。 对于第 4 层的高可用性和可伸缩性负载均衡，请考虑使用标准负载均衡器。
### <a name="azure-application-gateway-"></a>Azure 应用程序网关 *
[Azure 应用程序网关](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction)提供各种第 7 层负载均衡功能，包括 SSL 卸载、端到端 SSL、Web 应用程序防火墙、基于 Cookie 的会话关联，等等。 可[在开源 Cloud Foundry 中配置应用程序网关](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway)。 对于 PCF，请查看有关 POC 测试的 [PCF 2.1 发行说明](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway)。

### <a name="azure-standard-load-balancer-"></a>Azure 标准负载均衡器 *
Azure 负载均衡器是第 4 层负载均衡器。 它用于在负载均衡集中的服务实例之间分配流量。 标准版在基本版的基础上提供[高级功能](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)。 例如，1. 后端池的最大限制已从 100 个 VM 提升到 1000 个 VM。  2. 终结点现在支持多个可用性集，而不是一个可用性集。  3. 其他功能，例如高可用性端口、更丰富的监视数据，等等。若要转移到 Azure 可用性区域，需要标准负载均衡器。 对于新部署，我们建议从 Azure 标准负载均衡器着手。 

## <a name="3-authentication"></a>3.身份验证 
[Cloud Foundry 用户帐户和身份验证](https://docs.cloudfoundry.org/concepts/architecture/uaa.html)是 CF 及其各个组件的中心标识管理服务。 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) 是 Microsoft 提供的多租户、基于云的目录和标识管理服务。 默认情况下，UAA 用于 Cloud Foundry 身份验证。 作为一个高级选项，UAA 还支持将 Azure AD 用作外部用户存储。 Azure AD 用户可以使用其 LDAP 标识访问 Cloud Foundry，而无需使用 Cloud Foundry 帐户。 请遵循这些步骤[在 PCF 中为 UAA 配置 Azure AD](http://docs.pivotal.io/p-identity/1-6/azure/index.html)。

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4.Cloud Foundry 运行时系统的数据存储
Cloud Foundry 提供极佳的可扩展性，可将 Azure Blob 存储或 Azure MySQL/PostgreSQL 服务用于应用程序运行时系统存储。
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>用于 Cloud Foundry 云控制器 Blob 存储的 Azure Blob 存储
云控制器 Blob 存储是生成包、Droplet、包和资源池的关键数据存储。 默认情况下，NFS 服务器用于云控制器 Blob 存储。 为了避免单一故障点，请将 Azure Blob 存储用作外部存储。 请查看 [Cloud Foundry 文档](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html)了解背景知识，并查看 [Pivotal Cloud Foundry 中的选项](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html)。

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>用作 Cloud Foundry 弹性运行时数据库的 MySQL/PostgreSQL *
CF 弹性运行时需要两个主要系统数据库：
#### <a name="ccdb"></a>CCDB 
云控制器数据库。  云控制器提供 REST API 终结点，让客户端访问系统。 CCDB 存储云控制器的组织、空间、服务、用户角色等资源的表。
#### <a name="uaadb"></a>UAADB 
用于用户帐户和身份验证的数据库。 此数据库存储用户身份验证相关的数据，例如加密的用户名和密码。

默认情况下，可以使用本地系统数据库 (MySQL)。 为了实现高可用性和可伸缩性，请使用 Azure 托管的 MySQL 或 PostgreSQL 服务。 下面说明如何[使用开源 Cloud Foundry 为 CCDB、UAADB 和其他系统数据库启用 Azure MySQL/PostgreSQL](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service)。

## <a name="5-open-service-broker"></a>5.Open Service Broker
Azure Service Broker 提供一致的接口用于管理应用程序对 Azure 服务的访问。 新的 [Open Service Broker for Azure 项目](https://github.com/Azure/open-service-broker-azure)提供单一的简单方法，跨 Cloud Foundry、OpenShift 和 Kubernetes 将服务交付到应用程序。 有关 PCF 的部署说明，请参阅 [Azure Open Service Broker for PCF 磁贴](https://network.pivotal.io/products/azure-open-service-broker-pcf/)。

## <a name="6-metrics-and-logging"></a>6.指标和日志记录
Azure Log Analytics Nozzle 是一个 Cloud Foundry 组件，可将指标从 [Cloud Foundry Loggregator Firehose](https://docs.cloudfoundry.org/loggregator/architecture.html) 转发到 [Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/)。 使用 Nozzle，可跨多个部署收集、查看和分析 CF 系统的运行状况和性能指标。
单击[此处](https://docs.microsoft.com/azure/cloudfoundry/cloudfoundry-oms-nozzle)了解如何将 Azure Log Analytics Azure Log Analytics Nozzle 部署到开源 Cloud Foundry 和 Pivotal Cloud Foundry 环境，然后从 Azure Log Analytics OMS 控制台访问数据。 
> [!NOTE]
> 在 PCF 2.0 中，VM 的 BOSH 运行状况指标默认将转发到 Loggregator Firehose，并集成到 Azure Log Analytics OMS 控制台。

## <a name="7-cost-saving"></a>7.成本节省
### <a name="cost-saving-for-devtest-environments"></a>开发/测试环境的成本节省
#### <a name="b-series-"></a>B 系列：*
尽管我们往往建议对 Pivotal Cloud Foundry 生产环境使用 F 和 D VM 系列，但全新的“可迸发”[B 系列](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/)提供了新的选项。 对于并非持续需要 CPU 完全性能的工作负荷（如 Web 服务器、小型数据库以及开发和测试环境）而言，B 系列可突增 VM 十分理想。 这些工作负荷通常具有可突增的性能要求。 与 F1 的成本 0.05 美元/小时相比，B1 的成本已下降到 0.012 美元/小时。有关详细信息，请参阅 [VM 大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)和[价格](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)的完整列表。 
#### <a name="managed-standard-disk"></a>托管标准磁盘： 
建议在生产环境中使用高级磁盘，以保持可靠的性能。  借助[托管磁盘](https://azure.microsoft.com/services/managed-disks/)，标准存储也能提供类似的可靠性，但性能有所差别。 针对非性能敏感型的工作负荷，例如开发/测试或非关键环境，托管标准磁盘可以充当一种成本更低的替代选项。  
### <a name="cost-saving-in-general"></a>大致成本节省 
#### <a name="significant-vm-cost-saving-with-azure-reservations"></a>使用 Azure 预留大幅度节省 VM 成本： 
目前，所有 CF VM 都是根据“按需”定价计费的，即使环境在一般情况下能够无限期保持运行。 现在，可按 1 年或 3 年期预留 VM 容量，并获取 45-65% 的折扣。 折扣在计费系统中应用，无需对环境进行更改。 有关详细信息，请参阅 [Azure 预留的工作原理](https://azure.microsoft.com/pricing/reserved-vm-instances/)。 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>较小的托管高级磁盘： 
托管磁盘支持对高级磁盘和标准磁盘使用较小的磁盘大小，例如 P4 (32 GB) 和 P6 (64 GB)。 如果工作负荷较小，从标准高级磁盘迁移到托管高级磁盘可以节省成本。
#### <a name="utilizing-azure-first-party-services"></a>利用 Azure 第一方服务： 
利用 Azure 的第一方服务可以降低长期管理成本，此外还能实现前面部分中所述的高可用性和可靠性。 

Pivotal 为 PCF 客户推出了[空间占用量较小的 ERT](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html)。Pivotal 的组件只会共置到最多运行 2500 个应用程序实例的 4 个 VM 中。 目前可以通过 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)获取试用版。

## <a name="next-steps"></a>后续步骤
Azure 集成功能首先在[开源 Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/) 中推出，然后在 Pivotal Cloud Foundry 中推出。 带有 * 标记的功能尚未通过 PCF 推出。 本文档也未介绍 Cloud Foundry 与 Azure Stack 的集成。
有关 PCF 对带有 * 标记的功能的支持，或者 Cloud Foundry 与 Azure Stack 的集成，请联系 Pivotal 和 Microsoft 客户经理了解最新状态。 

