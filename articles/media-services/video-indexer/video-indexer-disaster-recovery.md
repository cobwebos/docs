---
title: 视频索引器故障转移和灾难恢复
titleSuffix: Azure Media Services
description: 了解如何在区域数据中心发生故障或灾难时故障转移到辅助视频索引器帐户。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: 17c21900448fcb6d0a40fe5407f3b8bd62f9e3e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499620"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>视频索引器故障转移和灾难恢复

如果区域数据中心中断或发生故障，Azure 媒体服务视频索引器不会提供服务的即时故障转移。 本文介绍如何为故障转移配置环境，以确保应用的最佳可用性，并在发生灾难时最大限度地减少恢复时间。

我们建议您跨区域对配置业务连续性灾难恢复 （BCDR），以便从 Azure 的隔离和可用性策略中受益。 有关详细信息，请参阅 [Azure 配对区域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。

## <a name="prerequisites"></a>先决条件

Azure 订阅。 如果尚未使用 Azure 订阅，请注册[Azure 免费试用](https://azure.microsoft.com/free/)版。

## <a name="failover-to-a-secondary-account"></a>故障转移到辅助帐户

要实现 BCDR，您需要有两个视频索引器帐户来处理冗余。

1. 创建连接到 Azure 的两个视频索引器[帐户（请参阅创建视频索引器帐户](connect-to-azure.md)）。 为主区域创建一个帐户，为配对的 Azure 区域创建另一个帐户。
1. 如果主区域出现故障，请使用辅助帐户切换到索引。

> [!TIP]
> 您可以通过根据"[在服务通知上创建活动日志警报](../../service-health/alerts-activity-log-service-notifications.md)"为服务运行状况通知设置活动日志警报来自动执行 BCDR。

有关使用多个租户的信息，请参阅[管理多个租户](manage-multiple-tenants.md)。 要实现 BCDR，请选择以下两个选项之一：[每个租户的视频索引器帐户](manage-multiple-tenants.md#video-indexer-account-per-tenant)或[每个租户的 Azure 订阅](manage-multiple-tenants.md#azure-subscription-per-tenant)。

## <a name="next-steps"></a>后续步骤

[管理连接到 Azure 的视频索引器帐户](manage-account-connected-to-azure.md)。
