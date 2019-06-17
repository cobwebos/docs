---
title: 使用视频索引器管理多个租户 - Azure
description: 本文推荐不同的集成选项，用于通过视频索引器管理多个租户。
services: media-services
documentationcenter: ''
author: ika-microsoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/15/2019
ms.author: ikbarmen
ms.openlocfilehash: a9b75c3454c67112b0e00c7ea4b4e8c676ebcc97
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65949469"
---
# <a name="manage-multiple-tenants"></a>管理多个租户

本文介绍用于通过视频索引器管理多个租户的不同选项。 请选择最适合自己方案的方法：

* 每个租户都有视频索引器帐户
* 所有租户使用单个视频索引器帐户
* 每个租户都有 Azure 订阅

## <a name="video-indexer-account-per-tenant"></a>每个租户都有视频索引器帐户

使用此体系结构时，将为每个租户创建一个视频索引器帐户。 租户在持久层和计算层中完全隔离。  

![每个租户都有视频索引器帐户](./media/manage-multiple-tenants/video-indexer-account-per-tenant.png)

### <a name="considerations"></a>注意事项

* 客户不共享存储帐户（除非客户手动进行配置）。
* 客户不共享计算（预留单位），且不相互影响作业处理时间。
* 可以通过删除视频索引器帐户，轻松从系统中删除租户。
* 租户之间无法共享自定义模型。

    确保不存在共享自定义模型的业务要求。
* 由于每个租户有多个视频索引器（和关联的媒体服务）帐户，因此更难管理。

> [!TIP]
> 在[视频索引器开发人员门户](https://api-portal.videoindexer.ai/)中创建系统的管理员用户，并使用授权 API 为租户提供相关的[帐户访问令牌](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token)。

## <a name="single-video-indexer-account-for-all-users"></a>所有用户使用单个视频索引器帐户

使用此体系结构时，客户负责实现租户隔离。 所有租户必须结合单个 Azure 媒体服务帐户使用单个视频索引器帐户。 上传、搜索或删除内容时，客户需要筛选该租户的正确结果。

![所有用户使用单个视频索引器帐户](./media/manage-multiple-tenants/single-video-indexer-account-for-all-users.png)

使用此选项时，可以通过按租户筛选模型，在租户之间共享或隔离自定义模型（人员、语言和品牌）。

[上传视频](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)时，可为每个租户指定不同的分区属性。 这可以在 [搜索 API](https://api-portal.videoindexer.ai/docs/services/operations/operations/Search-videos?)中实现隔离。 在搜索 API 中指定分区属性后，只会获取指定分区的结果。 

### <a name="considerations"></a>注意事项

* 可以在租户之间共享内容和自定义模型。
* 一个租户会影响其他租户的性能。
* 客户需要在视频索引器的顶层生成复杂的管理层。

> [!TIP]
> 可以使用 [priority](upload-index-videos.md) 属性来指定租户作业的优先级。

## <a name="azure-subscription-per-tenant"></a>每个租户都有 Azure 订阅 

在使用此体系结构，每个租户将具有其自己的 Azure 订阅。 对于每个用户，需在租户订阅中创建新的视频索引器帐户。

![每个租户都有 Azure 订阅](./media/manage-multiple-tenants/azure-subscription-per-tenant.png)

### <a name="considerations"></a>注意事项

* 这是唯一能够实现计费分摊的选项。
* 与“每个租户都有视频索引器帐户”相比，这种集成的管理开销更大。 如果计费不是一项要求，建议使用本文中所述的其他选项之一。

## <a name="next-steps"></a>后续步骤

[概述](video-indexer-overview.md)
