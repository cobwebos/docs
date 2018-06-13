---
title: 在 Azure AD 应用代理中使用连接器组在单独的网络和位置上发布应用程序 | Microsoft Docs
description: 介绍如何在 Azure AD 应用程序代理中创建和管理连接器组。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017; it-pro
ms.openlocfilehash: 50f17d7b833874cd15ddc9f2e4f93fa7af1700a2
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161590"
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>使用连接器组在单独的网络和位置上发布应用程序。

客户在越来越多的方案和应用程序中使用 Azure AD 的应用程序代理。 因此，我们通过启用更多拓扑使应用代理更加灵活。 可以创建应用程序代理连接器组，以便分配特定连接器以服务特定应用程序。 此功能提供更多控制以及优化应用程序代理部署的方法。 

将每个应用程序代理连接器分配给连接器组。 属于同一连接器组的所有连接器作为一个单独的单位，以实现高可用性和负载均衡。 所有连接器都属于某个连接器组。 如果不创建组，那么所有连接器都在默认组中。 管理员可以在 Azure 门户中创建新组并向其分配连接器。 

将所有应用程序分配给一个连接器组。 如果不创建组，那么所有应用程序都将分配到默认组中。 但是，如果将连接器整理到组中，则可以将每个应用程序设置为使用特定连接器组。 在这种情况下，只有该组中的连接器根据请求为应用程序提供服务。 如果应用程序托管在不同的位置，那么此功能非常有用。 可以基于位置创建连接器组，以便应用程序始终由物理位置靠近它们的连接器提供。

>[!TIP] 
>如果有大型应用程序代理部署，则不向默认连接器组分配任何应用程到。 这样一来，将新连接器分配给活动的连接器组之前，新连接器不接收任何实时流量。 此配置还可以通过将连接器移回默认组，让它们处于空闲模式，从而你可以在不会影响用户的状态下执行维护。

## <a name="prerequisites"></a>先决条件
为了为连接器分组，必须先确保[已安装多个连接器](application-proxy-enable.md)。 安装新的连接器时，该连接器会自动加入**默认**连接器组。

## <a name="create-connector-groups"></a>创建连接器组
使用以下步骤创建任意数量的连接器组。 

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 选择“Azure Active Directory” > “企业应用程序” > “应用程序代理”。
2. 选择“新建连接器组”。 此时会显示“新建连接器组”边栏选项卡。

   ![选择“新建连接器组”](./media/application-proxy-connector-groups/new-group.png)

3. 为新的连接器组提供一个名称，并使用下拉菜单选择哪些连接器属于此组。
4. 选择“保存”。

## <a name="assign-applications-to-your-connector-groups"></a>将应用程序分配到连接器组
对已使用应用程序代理发布的每个应用程序使用以下步骤。 首次发布应用程序时，可以将其分配到连接器组，也可以在任何时候使用以下步骤更改分配。   

1. 在目录的管理仪表板中，选择“企业应用程序” > “所有应用程序”> 需要分配到连接器组的应用程序 >“应用程序代理”。
2. 使用“连接器组”下拉菜单选择要让应用程序使用的组。
3. 单击“保存”应用所做的更改。

## <a name="use-cases-for-connector-groups"></a>连接器组用例 

连接器组可用于各种方案，包括：

### <a name="sites-with-multiple-interconnected-datacenters"></a>带有多个互联数据中心的站点

许多组织具有数目众多的互连数据中心。 在此情况下，需要在数据中心内保留尽可能多的流量，因为跨数据中心链接昂贵而缓慢。 可在每个数据中心中部署连接器，仅服务驻留在数据中心内的应用程序。 此方法可最大程度减少跨数据中心链接，并向用户提供完全透明的体验。

### <a name="applications-installed-on-isolated-networks"></a>安装在隔离网络上的应用程序

应用程序可托管在不属于主要公司网络的隔离网络中。 可使用连接器组在隔离网络上安装专用连接器，以便将应用程序也隔离到该网络中。 这通常发生在第三方供应商为组织维护特定应用程序的时候。 

连接器组允许为仅发布特定应用程序的网络安装专用连接器，从而更轻松、更安全地将应用程序管理外包给第三方供应商。

### <a name="applications-installed-on-iaas"></a>安装在 IaaS 上的应用程序 

对于在 IaaS 上安装用于云访问的应用程序，连接器组提供常见服务，保护对应用的所有访问。 连接器不在公司网络上创建其他依赖项或分裂应用体验。 连接器可安装在每个云数据中心中，并且仅服务驻留在此网络中的应用程序。 可安装多个连接器，实现高可用性。

例如，组织有许多虚拟机连接到其自己的 IaaS 托管虚拟网络。 为了允许员工使用这些应用程序，这些专用网络使用站点到站点 VPN 连接到公司网络。 这为位于本地的员工提供了良好的体验。 但是，这可能不适合远程员工，因为它需要额外的本地基础结构来路由访问权限，如下图所示：

![AzureAD Iaas 网络](./media/application-proxy-connector-groups/application-proxy-iaas-network.png)
  
使用 Azure AD 应用程序代理连接器组，可以启用常见服务来保护对所有应用程序的访问，而不会对公司网络创建额外的依赖项：

![AzureAD Iaas 多个云供应商](./media/application-proxy-connector-groups/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>多林 - 每个林使用不同连接器组

已部署应用程序代理的大多数客户通过执行 Kerberos 约束委派 (KCD) 来使用其单一登录 (SSO) 功能。 为了实现此目的，需要将连接器的计算机加入到一个域中，该域可将用户委派给应用程序。 KCD 支持跨林功能。 但对于具有不同的多林环境并且林之间没有信任的公司，不能将单一连接器用于所有林。 

在这种情况下，可为每个林部署特定连接器，并将其设置为服务已发布的应用程序，以仅为该特定林的用户提供服务。 每个连接器组表示不同的林。 虽然租户和大多数体验对所有林统一，但可使用 Azure AD 组将用户分配给他们自己的林应用程序。
 
### <a name="disaster-recovery-sites"></a>灾难恢复站点

根据灾难恢复 (DR) 站点的实现方式，可采取两种不同的方法：

* 如果灾难恢复站点采用主动 - 主动模式构建，其与主站点完全相同，并具有相同的网络和 AD 设置，则可在与主站点相同的连接器组中的灾难恢复站点上创建连接器。 这使 Azure AD 能检测故障转移。
* 如果灾难恢复站点与主站点分离，则可在灾难恢复站点中创建不同的连接器组，并且 1) 具有备份应用程序，或者 2) 根据需要将现有应用程序手动转移到灾难恢复连接器组。
 
### <a name="serve-multiple-companies-from-a-single-tenant"></a>服务单个租户中的多个公司

有多种方式来实现模型，其中单一服务提供程序为多个公司部署和维护 Azure AD 相关服务。 连接器组帮助管理员将连接器和应用程序分隔到不同的组中。 一种适合小公司的方法是使用单个 Azure AD 租户，不同的公司拥有其自己的域名和网络。 这同样适用于 M&A 情景以及单一 IT 部门出于监管或业务原因而服务几家公司的情况。 

## <a name="sample-configurations"></a>示例配置

一些可实现的示例，包括以下连接器组。
 
### <a name="default-configuration--no-use-for-connector-groups"></a>默认配置 - 不使用连接器组

如果不使用连接器组，配置将如下所示：

![AzureAD 无连接器组](./media/application-proxy-connector-groups/application-proxy-sample-config-1.png)
 
此配置足以用于小型部署和测试。 如果组织具有平面网络拓扑，则此配置也非常适用。
 
### <a name="default-configuration-and-an-isolated-network"></a>默认配置和隔离网络

此配置是默认配置的演变，其中存在一个运行在隔离网络（如 IaaS 虚拟网络）的特定应用： 

![AzureAD 无连接器组](./media/application-proxy-connector-groups/application-proxy-sample-config-2.png)
 
### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>建议配置 - 几个特定组和一个用于空闲连接器的默认组

对于大型和复杂组织的建议配置是，将默认连接器组设置为不服务任何应用程序且用于空闲或新安装连接器的组。 使用自定义连接器组服务所有应用程序。 这使得上述方案的所有复杂性成为可能。

在下面的示例中，公司有两个数据中心，数据中心 A 和数据中心 B，每个站点有两个连接器。 每个站点都有不同的应用程序在其上运行。 

![AzureAD 无连接器组](./media/application-proxy-connector-groups/application-proxy-sample-config-3.png)
 
## <a name="next-steps"></a>后续步骤

* [了解 Azure AD 应用程序代理连接器](application-proxy-connectors.md)
* [启用单一登录](application-proxy-single-sign-on.md)


