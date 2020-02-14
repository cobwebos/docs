---
title: Azure ExpressRoute CrossConnnections API 开发和集成
description: 本文详细概述了有关 expressRouteCrossConnections 资源类型的 ExpressRoute 合作伙伴。
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: mialdrid
ms.openlocfilehash: b4a83013d2cbeb2871a3963ec0c95144c02f4d66
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187010"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>ExpressRoute CrossConnnections API 开发和集成

ExpressRoute 合作伙伴资源管理器 API 允许 ExpressRoute 合作伙伴管理客户 ExpressRoute 线路的第2层和第3层配置。 ExpressRoute 合作伙伴资源管理器 API 引入了新的资源类型**expressRouteCrossConnections**。 合作伙伴使用此资源来管理客户 ExpressRoute 线路。

## <a name="workflow"></a>工作流

ExpressRouteCrossConnections 资源是 ExpressRoute 线路的影子资源。 当 Azure 客户创建 ExpressRoute 线路并选择特定 ExpressRoute 伙伴时，Microsoft 会在合作伙伴的 Azure ExpressRoute 管理订阅中创建 expressRouteCrossConnections 资源。 在此过程中，Microsoft 定义了要在其中创建 expressRouteCrossConnections 资源的资源组。 资源组的命名标准是 **CrossConnection-* PeeringLocation * * *;其中 PeeringLocation = ExpressRoute 位置。 例如，如果客户在丹佛创建了 ExpressRoute 线路，则会在合作伙伴的 Azure 订阅中的以下资源组中创建 CrossConnection： **CrossConnnection-丹佛**。

ExpressRoute 合作伙伴通过针对 expressRouteCrossConnections 资源发出 REST 操作来管理第2层和第3层配置。

## <a name="benefits"></a>优点

移动到 expressRouteCrossConnections 资源的好处：

* 对于 ExpressRoute 合作伙伴，任何将来的增强功能都将在 ExpressRouteCrossConnection 资源上提供。

* 合作伙伴可将[基于角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/overview)应用到 expressRouteCrossConnection 资源。 这些控件可以定义权限，用户帐户可以对这些权限修改 expressRouteCrossConnection 资源并添加/更新/删除对等互连配置。

* ExpressRouteCrossConnection 资源公开有助于排查 ExpressRoute 连接问题的 Api。 这包括 ARP 表、BGP 路由表摘要和 BGP 路由表详细信息。 经典部署 Api 不支持此功能。

* 合作伙伴还可以使用*RouteFilter*资源在 Microsoft 对等互连上查找播发的社区。

## <a name="api-development-and-integration-steps"></a>API 开发和集成步骤

为了针对合作伙伴 API 进行开发，ExpressRoute 合作伙伴利用测试客户和测试合作伙伴设置。 测试客户设置将用于在映射到虚拟设备和端口的测试对等位置中创建 ExpressRoute 线路。 测试伙伴设置用于管理在测试对等位置中创建的 ExpressRoute 线路。

### <a name="1-enlist-subscriptions"></a>1. 登记订阅

若要请求测试合作伙伴和测试客户安装，请将两个即用即付 Azure 订阅登记到 ExpressRoute 工程联系人：
* **ExpressRoute_API_Dev_Provider_Sub：** 此订阅将用于管理在虚拟设备和端口上的测试对等位置中创建的 ExpressRoute 线路。

* **ExpressRoute_API_Dev_Customer_Sub：** 此订阅将用于在映射到虚拟设备和端口的测试对等位置中创建 ExpressRoute 线路。

测试对等位置：默认情况下，不向生产客户公开虚拟设备和端口。 若要创建映射到测试设置的 ExpressRoute 线路，需要启用订阅功能标志。

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. 注册 Dev_Provider 订阅以访问 expressRouteCrossConnections API

若要访问 expressRouteCrossConnections API，需要在**Microsoft 网络资源提供程序**中注册合作伙伴订阅。 按照[Azure 资源提供程序和类型](/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal)一文中的步骤完成注册过程。

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. 设置 Azure 资源管理器 REST API 调用的身份验证

大多数 Azure 服务要求客户端代码使用有效凭据在调用服务 Api 之前，使用资源管理器进行身份验证。 身份验证通过 Azure AD 在各种执行组件之间进行协调，并向客户端提供访问令牌作为身份验证的证明。

身份验证过程涉及两个主要步骤：

1. [注册客户端](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)。
2. [创建访问请求](https://docs.microsoft.com/rest/api/azure/#create-the-request)。

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. 向客户端应用程序提供网络参与者权限

成功配置身份验证后，需要在 Dev_Provider_Sub 下向网络参与者授予客户端应用程序的访问权限。 若要授予权限，请登录到[Azure 门户](https://ms.portal.azure.com/#home)并完成以下步骤：

1. 导航到 "订阅"，然后选择 "Dev_Provider_Sub
2. 导航到访问控制（IAM）
3. 添加角色分配
4. 选择 "网络参与者" 角色
5. 分配对 Azure AD 用户、组或服务主体的访问权限
6. 选择客户端应用程序
7. 保存更改

### <a name="5-develop"></a>5. 开发

针对[EXPRESSROUTECROSSCONNECTIONS API](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections)进行开发。

## <a name="rest-api"></a>REST API

有关 REST API 文档，请参阅[ExpressRoute CrossConnections REST API](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections) 。

## <a name="next-steps"></a>后续步骤

有关所有 ExpressRoute REST Api 的详细信息，请参阅[EXPRESSROUTE Rest api](https://docs.microsoft.com/rest/api/expressroute/)。