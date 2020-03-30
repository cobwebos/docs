---
title: Azure 快速路由交叉管理 API 开发和集成
description: 本文为 ExpressRoute 合作伙伴提供有关 ExpressRoute 交叉连接资源类型的详细概述。
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: mialdrid
ms.openlocfilehash: b4a83013d2cbeb2871a3963ec0c95144c02f4d66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187010"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>快速路由交叉康宁 API 开发和集成

ExpressRoute 合作伙伴资源管理器 API 允许 ExpressRoute 合作伙伴管理客户 ExpressRoute 电路的第 2 层和第 3 层配置。 ExpressRoute 合作伙伴资源管理器 API 引入了一种新的资源类型 **，即 ExpressRouteCrossConnections**。 合作伙伴使用此资源管理客户 ExpressRoute 电路。

## <a name="workflow"></a>工作流

ExpressRouteCrossConnections 资源是快速路由电路的影子资源。 当 Azure 客户创建 ExpressRoute 电路并选择特定的 ExpressRoute 合作伙伴时，Microsoft 会在合作伙伴的 Azure ExpressRoute 管理订阅中创建 ExpressRouteCrossConnections 资源。 这样，Microsoft 将定义一个资源组来创建中的 ExpressRouteCross连接资源。 资源组的命名标准为 [*交叉连接 -* 对等位置];其中对等位置 = 快速路由位置。 例如，如果客户在丹佛创建 ExpressRoute 电路，则交叉连接将在合作伙伴的 Azure 订阅中创建，在以下资源组中：**交叉康宁-丹佛**。

ExpressRoute 合作伙伴通过针对 ExpressRouteCrossConnections 资源发出 REST 操作来管理第 2 层和第 3 层配置。

## <a name="benefits"></a>优点

迁移到快速路由交叉连接资源的好处：

* ExpressRoute 合作伙伴的任何后续增强功能都将在 ExpressRoute 交叉连接资源中提供。

* 合作伙伴可以将[基于角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/overview)应用于快速 RouteCross 连接资源。 这些控件可以定义用户帐户可以修改 ExpressRouteCrossConnect 资源以及添加/更新/删除对等配置的权限。

* ExpressRouteCross连接资源公开了可用于排除快速路由连接的 API。 这包括 ARP 表、BGP 路由表摘要和 BGP 路由表详细信息。 经典部署 API 不支持此功能。

* 合作伙伴还可以使用*RouteFilter*资源查找 Microsoft 对等互连上的广告社区。

## <a name="api-development-and-integration-steps"></a>API 开发和集成步骤

要针对合作伙伴 API 进行开发，ExpressRoute 合作伙伴利用测试客户和测试合作伙伴设置。 测试客户设置将用于在映射到虚拟设备和端口的测试对等位置创建 ExpressRoute 电路。 测试伙伴设置用于管理在测试对等位置创建的 ExpressRoute 电路。

### <a name="1-enlist-subscriptions"></a>1. 登记订阅

要请求测试合作伙伴和测试客户设置，请向 ExpressRoute 工程联系人登记两个即用即付 Azure 订阅：
* **ExpressRoute_API_Dev_Provider_Sub：** 此订阅将用于管理在虚拟设备和端口上的测试对等位置创建的 ExpressRoute 电路。

* **ExpressRoute_API_Dev_Customer_Sub：** 此订阅将用于在映射到虚拟设备和端口的测试对等位置创建 ExpressRoute 电路。

测试对等位置：默认情况下，虚拟设备和端口不会向生产客户公开。 为了创建映射到测试设置的 ExpressRoute 电路，需要启用订阅功能标志。

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. 注册Dev_Provider订阅以访问快速路由交叉连接 API

为了访问 ExpressRouteCrossConnections API，合作伙伴订阅需要注册到**Microsoft.Network 资源提供程序**。 按照[Azure 资源提供程序和类型](/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal)文章中的步骤完成注册过程。

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. 为 Azure 资源管理器 REST API 调用设置身份验证

大多数 Azure 服务要求客户端代码在调用服务 API 之前使用有效的凭据与资源管理器进行身份验证。 Azure AD 协调了各个参与者之间的身份验证，并为客户端提供访问令牌作为身份验证证明。

身份验证过程涉及两个主要步骤：

1. [注册客户端](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)。
2. [创建访问请求](https://docs.microsoft.com/rest/api/azure/#create-the-request)。

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. 向客户端应用程序提供网络参与者权限

成功配置身份验证后，您需要根据Dev_Provider_Sub授予对客户端应用程序的网络参与者访问权限。 要授予权限，请登录到[Azure 门户](https://ms.portal.azure.com/#home)并完成以下步骤：

1. 导航到订阅并选择Dev_Provider_Sub
2. 导航到访问控制 （IAM）
3. 添加角色分配
4. 选择网络参与者角色
5. 分配对 Azure AD 用户、组或服务主体的访问
6. 选择客户端应用程序
7. 保存更改

### <a name="5-develop"></a>5. 开发

针对[快速路由交叉连接 API](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections)进行开发。

## <a name="rest-api"></a>REST API

有关 REST API 文档[，请参阅快速路由交叉连接 REST API。](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections)

## <a name="next-steps"></a>后续步骤

有关所有快速路由 REST API 的详细信息，请参阅[快速路由 REST API](https://docs.microsoft.com/rest/api/expressroute/)。