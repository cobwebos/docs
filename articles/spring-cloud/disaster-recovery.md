---
title: Azure Spring Cloud 异地灾难恢复 | Microsoft Docs
description: 了解在发生区域性服务中断时如何保护 Spring Cloud 应用程序
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: e18193b40bb7c59a21a279f451673dc7d11140e4
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92092896"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Azure Spring Cloud 灾难恢复

本文适用于：✔️ Java ✔️ C#

本文介绍了在出现停机时可用于保护 Azure Spring Cloud 应用程序的一些策略。  区域性的灾难可能会给任何区域或数据中心造成停机，但是，精心的规划可以缓解此问题对客户造成的影响。

## <a name="plan-your-application-deployment"></a>规划应用程序部署

Azure Spring Cloud 应用程序在特定的区域中运行。  Azure 在世界各地的多个地理位置运营。 Azure 地理位置是至少包含一个 Azure 区域的规定世界区域。 Azure 区域是某个地理位置内包含一个或多个数据中心的区域。  每个 Azure 区域与同一地理位置中另一个区域配对。 Azure 跨区域对将平台更新（计划内维护）序列化，确保每次只更新每个对中的一个区域。 如果发生影响多个区域的中断，则每对中的至少一个区域将优先进行恢复。

若要确保高可用性并在发生灾难时提供保护，需要将 Spring Cloud 应用程序部署到多个区域。  Azure 提供了一系列 [配对区域](../best-practices-availability-paired-regions.md)，使你可以规划如何在区域对中进行 Spring Cloud 部署。  在设计微服务体系结构时，我们建议考虑三个关键因素：区域可用性、Azure 配对区域和服务可用性。

*  区域可用性：选择靠近用户的地理区域，以尽量减少网络延迟和传输时间。
*  Azure 配对区域：选择所选地理区域中的配对区域，确保以协调的方式进行平台更新，并根据需要确定恢复工作的优先级。
*  服务可用性： 确定配对区域应当采用热/热、热/暖还是热/冷模式运行。

## <a name="use-azure-traffic-manager-to-route-traffic"></a>使用 Azure 流量管理器路由流量

[Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)提供基于 DNS 的流量负载均衡，并可以在多个区域之间分配网络流量。  使用 Azure 流量管理器将客户定向到离他们最近的 Azure Spring Cloud 服务实例。  为了实现最佳性能和冗余，请先通过 Azure 流量管理器定向所有应用程序流量，然后再将其发送到你的 Azure Spring Cloud 服务。

如果你在多个区域中部署了 Azure Spring Cloud 应用程序，请使用 Azure 流量管理器来控制在每个区域中发送到你的应用程序的流量流。  使用服务 IP 定义每个服务的 Azure 流量管理器终结点。 客户应连接到指向 Azure Spring Cloud 服务的 Azure 流量管理器 DNS 名称。  Azure 流量管理器将在所定义的终结点之间对流量进行负载均衡。  如果数据中心遭受灾难，Azure 流量管理器会将来自该区域的流量定向到其配对区域，以确保服务连续性。

## <a name="create-azure-traffic-manager-for-azure-spring-cloud"></a>为 Azure Spring Cloud 创建 Azure 流量管理器

1. 在两个不同的区域中创建 Azure Spring Cloud。
你需要在两个不同的区域（“美国东部”和“西欧”）部署两个 Azure Spring Cloud 服务实例。 使用 Azure 门户启动现有的 Azure Spring Cloud 应用程序，以创建两个服务实例。 每个实例将充当流量的主要终结点和故障转移终结点。 

**两个服务实例的信息：**

| 服务名称 | 位置 | 应用程序 |
|--|--|--|
| service-sample-a | 美国东部 | gateway / auth-service / account-service |
| service-sample-b | 西欧 | gateway / auth-service / account-service |

2. 为服务设置自定义域。根据[自定义域文档](spring-cloud-tutorial-custom-domain.md)为这两个现有服务实例设置自定义域。 成功设置后，这两个服务实例将绑定到自定义域：bcdr-test.contoso.com

3. 创建流量管理器和两个终结点：[使用 Azure 门户创建流量管理器配置文件](../traffic-manager/quickstart-create-traffic-manager-profile.md)。

下面是流量管理器配置文件：
* 流量管理器 DNS 名称：`http://asc-bcdr.trafficmanager.net`
* 终结点配置文件： 

| 配置文件 | 类型 | 目标 | 优先度 | 自定义标头设置 |
|--|--|--|--|--|
| 终结点 A 配置文件 | 外部终结点 | service-sample-a.asc-test.net | 1 | 主机：bcdr-test.contoso.com |
| 终结点 B 配置文件 | 外部终结点 | service-sample-b.asc-test.net | 2 | 主机：bcdr-test.contoso.com |

4. 在 DNS 区域中创建 CNAME 记录：bcdr-test.contoso.com CNAME asc-bcdr.trafficmanager.net。 

5. 现在，环境已完全设置。 客户应该可以通过 bcdr-test.contoso.com 访问应用了

## <a name="next-steps"></a>后续步骤

* [快速入门：部署第一个 Azure Spring Cloud 应用程序](spring-cloud-quickstart.md)
