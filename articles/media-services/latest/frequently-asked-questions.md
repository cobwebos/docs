---
title: Azure 媒体服务 v3 常见问题 | Microsoft Docs
description: 本文将解答 Azure 媒体服务 v3 常见问题。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/29/2018
ms.author: juliako
ms.openlocfilehash: 7fe59763162ca53ea0256d5902aeece90525f214
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659140"
---
# <a name="azure-media-services-v3-preview-frequently-asked-questions"></a>Azure 媒体服务 v3（预览版）常见问题

本文将解答 Azure 媒体服务 (AMS) v3 常见问题。

## <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>能否使用 Azure 门户来管理 v3 资源？

还不可以。 你可以使用某个受支持的 SDK。 请参阅本文档集中的教程和示例。

## <a name="is-there-an-api-for-configuring-media-reserved-units"></a>是否有用于配置媒体预留单位的 API？

媒体服务团队正努力消除 v3 中的 RU。 然而，必要的服务工作尚未完成。 在此之前，客户需要使用 Azure 门户或 AMS v2 API 来设置 RU（如[缩放媒体处理](../previous/media-services-scale-media-processing-overview.md)中所述）。 

S3 RU 是 **VideoAnalyzerPreset** and **AudioAnalyzerPreset** V3 预设所必需的。

## <a name="does-v3-asset-have-no-assetfile-concept"></a>V3 资产是否没有 AssetFile 的概念？

已从 AMS API 中删除 AssetFile，以便将媒体服务与存储 SDK 依赖项分开。 现在由存储而非媒体服务来保存属于存储的信息。 

## <a name="where-did-client-side-storage-encryption-go"></a>客户端存储加密在哪里进行？

现在推荐使用服务器端存储加密（默认启用）。有关详细信息，请参阅[静态数据的 Azure 存储服务加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。

## <a name="what-is-the-recommended-upload-method"></a>推荐使用哪种上传方法？

建议使用 HTTP(s) 引入。 有关详细信息，请参见 [HTTP(s) 引入](job-input-from-http-how-to.md)。

## <a name="how-does-pagination-work"></a>分页是如何工作的？

对于支持 OData 的资源，媒体服务支持使用 $top，但传递给 $top 的值必须小于 1000（例如，分页的页面大小）。

这样一来，既可以使用 $top 获取一小部分项目示例（例如，最近的 100 个项目），也可以使用分页功能对所有项目进行分页。 

媒体服务不支持以用户指定的页面大小对数据进行分页。

有关详细信息，请参阅[筛选、排序、分页](assets-concept.md#filtering-ordering-paging)

## <a name="how-to-retrieve-an-entity-in-media-services-v3"></a>如何在媒体服务 v3 中检索实体？

v3 基于一个统一的 API 接口，该接口公开了基于 **Azure 资源管理器**构建的管理和操作功能。 根据 **Azure 资源管理器**，资源名称始终是唯一的。 因此，可以为资源名称使用任何唯一的标识符字符串（例如，GUID）。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [媒体服务 v3 概述](media-services-overview.md)
