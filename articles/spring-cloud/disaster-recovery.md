---
title: Azure 春云地质灾害恢复 |微软文档
description: 了解如何保护您的春云应用程序免受区域中断
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.openlocfilehash: 4961e5a63e5bc1933cf19b1f291b521d89cbda0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279140"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Azure 春云灾难恢复

本文介绍了一些可用于保护 Azure 春云应用程序免受停机的策略。  任何区域或数据中心都可能会遇到区域灾难造成的停机，但仔细规划可以减轻对客户的影响。

## <a name="plan-your-application-deployment"></a>规划应用程序部署

Azure 春云应用程序在特定区域中运行。  Azure 在世界各地的多个地理位置运营。 Azure 地理位置是至少包含一个 Azure 区域的规定世界区域。 Azure 区域是地理位置中的一个区域，包含一个或多个数据中心。  每个 Azure 区域与同一地理位置中另一个区域配对。 Azure 跨区域对序列化平台更新（计划维护），确保每次仅更新每个对中的一个区域。 如果发生影响多个区域的中断，则每对中的至少一个区域将优先进行恢复。

确保高可用性和抵御灾难需要将 Spring Cloud 应用程序部署到多个区域。  Azure 提供[配对区域](../best-practices-availability-paired-regions.md)的列表，以便您可以将春云部署规划为区域对。  我们建议您在设计微服务体系结构时考虑三个关键因素：区域可用性、Azure 配对区域和服务可用性。

*  区域可用性：选择靠近用户的地理区域，以最大限度地减少网络延迟和传输时间。
*  Azure 配对区域：选择所选地理区域内的配对区域，以确保协调平台更新，并在需要时确定恢复工作的优先级。
*  服务可用性：决定配对区域是热/热、热/热还是热/冷。

## <a name="use-azure-traffic-manager-to-route-traffic"></a>使用 Azure 流量管理器路由流量

[Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)提供基于 DNS 的流量负载平衡，并可以跨多个区域分发网络流量。  使用 Azure 流量管理器将客户定向到离他们最近的 Azure Spring 云服务实例。  为了获得最佳性能和冗余，请通过 Azure 流量管理器引导所有应用程序流量，然后再将其发送到 Azure Spring 云服务。

如果在多个区域中具有 Azure 春云应用程序，请使用 Azure 流量管理器控制每个区域中应用程序的流量。  使用服务 IP 为每个服务定义 Azure 流量管理器终结点。 客户应连接到指向 Azure Spring 云服务的 Azure 流量管理器 DNS 名称。  Azure 流量管理器负载平衡跨已定义终结点的流量。  如果灾难袭击数据中心，Azure 流量管理器将指示来自该区域的流量到其对，从而确保服务的连续性。