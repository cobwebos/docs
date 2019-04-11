---
title: 使用 v3 Api-Azure 进行开发 |Microsoft Docs
description: 本文介绍了使用媒体服务 v3 进行开发时对实体和 Api 应用的规则。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 18b72ceaee0ca0747a0bf2144d5f9ffddbee8b8c
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471862"
---
# <a name="developing-with-media-services-v3-apis"></a>使用媒体服务 v3 Api 进行开发

本文介绍了使用媒体服务 v3 进行开发时对实体和 Api 应用的规则。

## <a name="naming-conventions"></a>命名约定

Azure 媒体服务 v3 资源名称（例如，资产、作业、转换）需遵循 Azure 资源管理器命名约束。 根据 Azure 资源管理器的要求，资源名称始终必须唯一。 因此，可以为资源名称使用任何唯一的标识符字符串（例如，GUID）。 

媒体服务资源名称不能包含“<”、“>”、“%”、“&”、“:”、“&#92;”、“?”、“/”、“*”、“+”、“.”、单引号或任何控制字符。 允许其他所有字符。 资源名称的最大长度为 260 个字符。 

有关 Azure 资源管理器命名的详细信息，请参阅：[命名需求](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource)和[命名约定](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。

## <a name="v3-api-design-principles-and-rbac"></a>v3 API 设计原理和 RBAC

V3 API 的主要设计原则之一是使 API 更安全。 v3 Api 不返回机密或凭据上**获取**或**列表**操作。 在响应中，密钥始终为 null、空值或进行了净化。 用户需要调用一个单独的操作方法来获取机密信息或凭据。 **读取器**角色不能调用操作，以便它不能调用 Asset.ListContainerSas，StreamingLocator.ListContentKeys，ContentKeyPolicies.GetPolicyPropertiesWithSecrets 等操作。 具有单独的操作，可根据需要自定义角色中设置更精细的 RBAC 安全权限。

有关详细信息，请参阅：

- [内置的角色定义](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)
- [使用 RBAC 管理访问权限](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [媒体服务帐户的基于角色的访问控制](rbac-overview.md)
- [获取内容密钥的策略-.NET](get-content-key-policy-dotnet-howto.md)。

## <a name="long-running-operations"></a>长时间运行的操作

操作标记为与`x-ms-long-running-operation`在 Azure 媒体服务[swagger 文件](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json)是长时间运行的操作。 

有关如何跟踪异步 Azure 操作的详细信息，请参阅[异步操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation)。

媒体服务具有以下长时间运行的操作：

* 创建 LiveEvent
* 更新 LiveEvent
* 删除 LiveEvent
* Start LiveEvent
* 停止 LiveEvent
* 重置 LiveEvent
* 创建 LiveOutput
* Delete LiveOutput
* 创建 StreamingEndpoint
* 更新 StreamingEndpoint
* 删除 StreamingEndpoint
* 启动 StreamingEndpoint
* 停止 StreamingEndpoint
* 缩放 StreamingEndpoint

## <a name="filtering-ordering-paging-of-media-services-entities"></a>媒体服务实体的筛选、排序和分页

请参阅[筛选、 排序、 分页的 Azure 媒体服务实体](entities-overview.md)

## <a name="next-steps"></a>后续步骤

[使用 SDK/工具通过媒体服务 v3 API 开始进行开发](developers-guide.md)
