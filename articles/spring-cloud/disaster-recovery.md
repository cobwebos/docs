---
title: Azure 春季云异地灾难恢复 |Microsoft Docs
description: 了解如何防止你的春季云应用程序发生区域服务中断
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.openlocfilehash: e8f32f574a4ff7be0cc3cc7915b8203b53824c63
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792320"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Azure 春季云灾难恢复

本文介绍一些可用于保护 Azure 春季云应用程序不会遇到停机时间的策略。  任何区域或数据中心可能会遇到区域灾难导致的停机时间，但周密的规划可以减少对客户的影响。

## <a name="plan-your-application-deployment"></a>规划应用程序部署

Azure 春季云应用程序在特定区域中运行。  Azure 在世界各地的多个地理位置运营。 Azure 地理位置是至少包含一个 Azure 区域的规定世界区域。 Azure 区域是包含一个或多个数据中心的地理区域内的区域。  每个 Azure 区域与同一地理位置中另一个区域配对。 Azure 跨区域对序列化平台更新（计划内维护），确保每个对中一次只能更新一个区域。 如果发生影响多个区域的中断，则每对中的至少一个区域将优先进行恢复。

若要确保高可用性和灾难保护，要求你将春季云应用程序部署到多个区域。  Azure 提供了[成对区域](../best-practices-availability-paired-regions.md)的列表，以便你可以规划春季云部署到区域对。  建议在设计微服务体系结构时考虑三个关键因素：地区可用性、Azure 配对区域和服务可用性。

*  区域可用性：选择靠近用户的地理区域，以最大程度地减少网络延迟和传输时间。
*  Azure 配对区域：选择所选地理区域中的配对区域，以确保进行协调后的平台更新和优先恢复工作（如果需要）。
*  服务可用性：决定配对区域是运行热/热、热/温还是热/冷。

## <a name="use-azure-traffic-manager-to-route-traffic"></a>使用 Azure 流量管理器路由流量

[Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)提供基于 DNS 的流量负载平衡，并可将网络流量分布到多个区域。  使用 Azure 流量管理器将客户定向到最近的 Azure 春季云服务实例。  为了获得最佳性能和冗余，请在将所有应用程序流量发送到 Azure 春季云服务之前，直接通过 Azure 流量管理器进行。

如果有多个区域的 Azure 春季云应用程序，请使用 Azure 流量管理器控制每个区域中的应用程序的流量流。  使用服务 IP 定义每个服务的 Azure 流量管理器终结点。 客户应连接到指向 Azure 春季云服务的 Azure 流量管理器 DNS 名称。  Azure 流量管理器对已定义终结点的流量进行负载均衡。  如果发生灾难，Azure 流量管理器会将该区域的流量定向到其对，确保服务的连续性。

## <a name="create-azure-traffic-manager-for-azure-spring-cloud"></a>创建 azure 春季云的 Azure 流量管理器

1. 在两个不同的区域创建 Azure 春季 Cloud。
需要在两个不同的区域（美国东部和西欧）部署两个 Azure 春季云服务实例。 使用 Azure 门户启动现有的 Azure 春季云应用程序，以创建两个服务实例。 每个都将用作流量的主要和故障转移终结点。 

**两个服务实例信息：**

| 服务名称 | 位置 | 应用程序 |
|--|--|--|
| 服务-示例-a | 美国东部 | 网关/身份验证-服务/帐户 |
| 服务-示例-b | 西欧 | 网关/身份验证-服务/帐户 |

2. 为服务设置自定义域跟随[自定义域文档](spring-cloud-tutorial-custom-domain.md)为这两个现有服务实例设置自定义域。 成功设置后，这两个服务实例将绑定到自定义域： bcdr-test.contoso.com

3. 创建流量管理器和两个终结点：[使用 Azure 门户创建流量管理器配置文件](https://docs.microsoft.com/azure/traffic-manager/quickstart-create-traffic-manager-profile)。

下面是流量管理器配置文件：
* 流量管理器 DNS 名称：http://asc-bcdr.trafficmanager.net
* 终结点配置文件： 

| 配置文件 | 类型 | 目标 | Priority | 自定义标头设置 |
|--|--|--|--|--|
| 终结点 A 配置文件 | 外部终结点 | service-sample-a.asc-test.net | 1 | 主机： bcdr-test.contoso.com |
| 终结点 B 配置文件 | 外部终结点 | service-sample-b.asc-test.net | 2 | 主机： bcdr-test.contoso.com |

4. 在 DNS 区域中创建 CNAME 记录： bcdr-test.contoso.com CNAME asc-bcdr.trafficmanager.net。 

5. 现在，环境已完全设置。 客户应能够通过以下方式访问应用： bcdr-test.contoso.com