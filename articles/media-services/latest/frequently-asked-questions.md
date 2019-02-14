---
title: Azure 媒体服务 v3 常见问题 | Microsoft Docs
description: 本文将解答 Azure 媒体服务 v3 常见问题。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/05/2019
ms.author: juliako
ms.openlocfilehash: be4c08bc31c8811655230ab89b48271f4c2b3164
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756574"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Azure 媒体服务 v3 常见问题

本文将解答 Azure 媒体服务 (AMS) v3 常见问题。

## <a name="v3-apis"></a>v3 API

### <a name="how-do-i-configure-media-reserved-units"></a>如何配置媒体保留单位？

对于由媒体服务 v3 或视频索引器触发的音频分析和视频分析作业，强烈建议为你的帐户预配 10 S3 MRU。 如果需要超过 10 S3 MRU 的数量，请使用 [Azure 门户](https://portal.azure.com/)打开一个支持票证。

有关详细信息，请参阅[使用 CLI 调整媒体处理的规模](media-reserved-units-cli-how-to.md)。

### <a name="what-is-the-recommended-method-to-process-videos"></a>什么是处理视频的建议方法？

建议使用指向视频的 HTTP(s) URL 提交作业。 有关详细信息，请参阅 [HTTP(s) 引入](job-input-from-http-how-to.md)。 在可以处理之前，无需先使用输入视频创建资产。

### <a name="how-does-pagination-work"></a>分页是如何工作的？

对于支持 OData 的资源，媒体服务支持使用 $top，但传递给 $top 的值必须小于 1000（例如，分页的页面大小）。

这样一来，既可以使用 $top 获取一小部分项目示例（例如，最近的 100 个项目），也可以使用分页功能对所有项目进行分页。 

媒体服务不支持以用户指定的页面大小对数据进行分页。

有关详细信息，请参阅[筛选、排序、分页](entities-overview.md)。

### <a name="how-to-retrieve-an-entity-in-media-services-v3"></a>如何在媒体服务 v3 中检索实体？

v3 基于一个统一的 API 接口，该接口公开了基于 **Azure 资源管理器**构建的管理和操作功能。 根据 **Azure 资源管理器**，资源名称始终是唯一的。 因此，可以为资源使用任何唯一的标识符字符串（例如，GUID）。

## <a name="live-streaming"></a>实时传送视频流 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>如何在实时流过程中插入中断/视频和图像盖板？

媒体服务 v3 实时编码尚不支持在实时流过程中插入视频或图像盖板。 

可以使用[实时本地编码器](recommended-on-premises-live-encoders.md)切换源视频。 许多应用提供切换源（包括 Telestream Wirecast、Switcher Studio（在 iOS 上）、OBS Studio（免费应用）等）的功能。

## <a name="media-services-v2-vs-v3"></a>媒体服务 v2 与 v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>能否使用 Azure 门户来管理 v3 资源？

还不可以。 你可以使用某个受支持的 SDK。 请参阅本文档集中的教程和示例。

### <a name="is-there-an-assetfile-concept-in-v3"></a>v3 中是否有 AssetFile 概念？

已从 AMS API 中删除 AssetFile，以便将媒体服务与存储 SDK 依赖项分开。 现在由存储而非媒体服务来保存属于存储的信息。 

有关详细信息，请参阅[迁移到媒体服务 v3](migrate-from-v2-to-v3.md)。

### <a name="where-did-client-side-storage-encryption-go"></a>客户端存储加密在哪里进行？

现在建议使用服务器端存储加密（在默认情况下为打开状态）。 有关详细信息，请参阅[静态数据的 Azure 存储服务加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [媒体服务 v3 概述](media-services-overview.md)
