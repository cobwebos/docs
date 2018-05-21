---
title: 有关 Azure Service Fabric 与 API 管理的概述 | Microsoft Docs
description: 本文介绍了如何将 Azure API 管理用作 Service Fabric 应用程序的网关。
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: vturecek
ms.openlocfilehash: 6bf7ea90bb5351411984110fd8fb05c2f8cb0650
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-with-azure-api-management-overview"></a>有关 Azure Service Fabric 与 API 管理的概述

云应用程序通常都需要使用前端网关，为用户、设备或其他应用程序提供同一个入口点。 在 Service Fabric 中，网关可以是任意无状态服务（如 [ASP.NET Core 应用程序](service-fabric-reliable-services-communication-aspnetcore.md)），也可以是其他专为流量入口设计的服务（如[事件中心](https://docs.microsoft.com/azure/event-hubs/)、[IoT 中心](https://docs.microsoft.com/azure/iot-hub/)或 [Azure API 管理](https://docs.microsoft.com/azure/api-management/)）。

本文介绍了如何将 Azure API 管理用作 Service Fabric 应用程序的网关。 API 管理直接与 Service Fabric 集成，以便可以使用一组丰富的路由规则向后端 Service Fabric 服务发布 API。 

## <a name="architecture"></a>体系结构
常见 Service Fabric 体系结构使用单页 Web 应用程序，向公开 HTTP API 的后端服务发出 HTTP 调用。 [Service Fabric 入门示例应用程序](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)展示了此体系结构示例。

在此方案中，无状态 Web 服务用作 Service Fabric 应用程序的网关。 使用这种方法，需要编写可以将 HTTP 请求代理到后端服务的 Web 服务，如下图所示：

![有关 Service Fabric 与 Azure API 管理的概述 - 拓扑][sf-web-app-stateless-gateway]

随着应用程序越来越复杂，必须向大量后端服务呈现 API 的网关亦是如此。 Azure API 管理旨在通过路由规则、访问控制、速率限制、监视、事件日志记录和响应缓存来处理复杂 API，最大限度地减少用户需要执行的操作。 Azure API 管理支持 Service Fabric 服务发现、分区解析和副本选择，从而智能地将请求直接路由到 Service Fabric 中的后端服务，用户无需编写自己的无状态 API 网关。 

在此方案中，仍通过 Web 服务为 Web UI 提供服务，同时通过 Azure API 管理来托管和路由 HTTP API 调用，如下图所示：

![有关 Service Fabric 与 Azure API 管理的概述 - 拓扑][sf-apim-web-app]

## <a name="application-scenarios"></a>应用程序方案

Service Fabric 中的服务可以是无状态服务，也可以是有状态服务，可采用以下三种方案之一进行分区：单独分区、Int64 范围分区和已命名分区。 必须确定特定服务实例的具体分区，才能解析服务终结点。 解析服务终结点时，必须指定服务实例名称（例如，`fabric:/myapp/myservice`）以及服务的具体分区，但单独分区情况除外。

Azure API 管理可与无状态服务、有状态服务和任何分区方案的任意组合配合使用。

## <a name="send-traffic-to-a-stateless-service"></a>将流量发送到无状态服务

最简单的情况是将流量转发到无状态服务实例。 为此，API 管理操作包含使用 Service Fabric 后端的入站处理策略，用于将请求映射到 Service Fabric 后端中的特定无状态服务实例。 发送给相应服务的请求会被发送到无状态服务实例的随机副本。

#### <a name="example"></a>示例
在以下方案中，Service Fabric 应用程序包含名为“`fabric:/app/fooservice`”的无状态服务，用于公开内部 HTTP API。 服务实例名称已知，并可直接在 API 管理入站处理策略中进行硬编码。 

![有关 Service Fabric 与 Azure API 管理的概述 - 拓扑][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>将流量发送到有状态服务

与无状态服务方案类似，流量可以转发到有状态服务实例。 在此示例中，API 管理操作包含使用 Service Fabric 后端的入站处理策略，用于将请求映射到特定有状态服务实例的特定分区。 每个请求映射到的分区是通过 lambda 方法并根据传入 HTTP 请求中的一些输入（如 URL 路径中的值）计算得出。 可以将策略配置为仅将请求发送到主要副本，也可以配置为发送到读取操作的随机副本。

#### <a name="example"></a>示例

在以下方案中，Service Fabric 应用程序包含名为“`fabric:/app/userservice`”的已分区有状态服务，用于公开内部 HTTP API。 服务实例名称已知，并可直接在 API 管理入站处理策略中进行硬编码。  

服务通过 Int64 分区方案分为两个分区，键范围介于 `Int64.MinValue` 到 `Int64.MaxValue` 之间。 后端策略将 URL 请求路径中的 `id` 值转换为 64 位整数，在此范围内计算分区键，尽管可以使用任何算法来计算分区键。 

![有关 Service Fabric 与 Azure API 管理的概述 - 拓扑][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>将流量发送到多个无状态服务

在更高级方案中，可以定义 API 管理操作，将请求映射到多个服务实例。 在此方案中，每个操作均包含一个策略，用于根据传入 HTTP 请求中的值（如 URL 路径或查询字符串），将请求映射到特定服务实例（如果是有状态服务，则将请求映射到服务实例中的分区）。 

为此，API 管理操作包含使用 Service Fabric 后端的入站处理策略，用于根据从传入 HTTP 请求中检索到的值，将请求映射到 Service Fabric 后端中的无状态服务实例。 发送给服务实例的请求会被发送到服务实例的随机副本。

#### <a name="example"></a>示例

在此示例中，使用以下公式为应用的每个用户新建一个无状态服务实例，名称动态生成：
 
 - `fabric:/app/users/<username>`

 每个服务都有一个独一无二的名称，但这些名称并不是提前已知，因为服务是根据用户或管理员输入创建而成，无法硬编码到 APIM 策略或路由规则中。 相反，向其发送请求的服务的名称是根据 URL 请求路径中的 `name` 值在后端策略定义中生成。 例如：

  - 对 `/api/users/foo` 发出的请求被路由到服务实例 `fabric:/app/users/foo`
  - 对 `/api/users/bar` 发出的请求被路由到服务实例 `fabric:/app/users/bar`

![有关 Service Fabric 与 Azure API 管理的概述 - 拓扑][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>将流量发送到多个有状态服务

与无状态服务示例类似，API 管理操作可以将请求映射到多个有状态服务实例。在此方案中，可能还需要对每个有状态服务实例执行分区解析。

为此，API 管理操作包含使用 Service Fabric 后端的入站处理策略，用于根据从传入 HTTP 请求中检索到的值，将请求映射到 Service Fabric 后端中的有状态服务实例。 除了可以映射到特定服务实例外，还可以将请求映射到服务实例中的特定分区，并视需要映射到分区内的主要副本或随机次要副本。

#### <a name="example"></a>示例

在此示例中，使用以下公式为应用的每个用户新建一个有状态服务实例，名称动态生成：
 
 - `fabric:/app/users/<username>`

 每个服务都有一个独一无二的名称，但这些名称并不是提前已知，因为服务是根据用户或管理员输入创建而成，无法硬编码到 APIM 策略或路由规则中。 相反，向其发送请求的服务的名称是根据 URL 请求路径中的 `name` 值在后端策略定义中生成。 例如：

  - 对 `/api/users/foo` 发出的请求被路由到服务实例 `fabric:/app/users/foo`
  - 对 `/api/users/bar` 发出的请求被路由到服务实例 `fabric:/app/users/bar`

每个服务实例同样通过 Int64 分区方案分为两个分区，键范围介于 `Int64.MinValue` 到 `Int64.MaxValue` 之间。 后端策略将 URL 请求路径中的 `id` 值转换为 64 位整数，在此范围内计算分区键，尽管可以使用任何算法来计算分区键。 

![有关 Service Fabric 与 Azure API 管理的概述 - 拓扑][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>后续步骤

按照[教程](service-fabric-tutorial-deploy-api-management.md)操作，使用 API 管理创建首个 Service Fabric 群集，并通过 API 管理向服务发送请求。

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png