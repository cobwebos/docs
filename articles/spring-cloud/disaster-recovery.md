---
title: Azure 春季云异地灾难恢复 |Microsoft Docs
description: 了解如何防止你的春季云应用程序发生区域服务中断
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.openlocfilehash: 4961e5a63e5bc1933cf19b1f291b521d89cbda0e
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279140"
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