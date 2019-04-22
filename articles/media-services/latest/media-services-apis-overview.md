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
ms.date: 04/15/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 5ae7563892cb4792f5c329b2850d7b88d37c0e7d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698257"
---
# <a name="developing-with-media-services-v3-apis"></a>使用媒体服务 v3 Api 进行开发

本文介绍了使用媒体服务 v3 进行开发时对实体和 Api 应用的规则。

## <a name="accessing-the-azure-media-services-api"></a>访问 Azure 媒体服务 API

若要访问 Azure 媒体服务资源，可以使用 Azure Active Directory (AD) 服务主体身份验证。
媒体服务 API 需要用户或应用程序进行 REST API 请求有权访问媒体服务帐户资源，并使用**参与者**或**所有者**角色。 可以使用访问 API**读取器**角色但只**获取**或**列表** 操作将可用。 有关详细信息，请参阅[媒体服务帐户的基于角色的访问控制](rbac-overview.md)。

而不是创建服务主体，请考虑使用 Azure 资源的管理的标识访问通过 Azure 资源管理器中的媒体服务 API。 若要了解有关 Azure 资源的管理的标识的详细信息，请参阅[什么是 Azure 资源的管理的标识](../../active-directory/managed-identities-azure-resources/overview.md)。

### <a name="azure-ad-service-principal"></a>Azure AD 服务主体 

如果要创建 Azure AD 应用程序和服务主体，该应用程序必须处于它自己的租户。 创建应用程序后，让应用程序**参与者**或**所有者**角色到媒体服务帐户的访问。 

如果您不确定是否有权创建一个 Azure AD 应用程序，请参阅[所需的权限](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)。

在下图中，数字表示按时间顺序的请求流：

![中间层应用](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. 中间层应用程序请求 Azure AD 访问令牌具有以下参数：  

   * Azure AD 租户终结点。
   * 媒体服务资源 URI。
   * REST 媒体服务的资源 URI。
   * Azure AD 应用程序值：客户端 ID和客户端密码。
   
   若要获取所有所需的值，请参阅[使用 Azure CLI 访问 Azure 媒体服务 API](access-api-cli-how-to.md)

2. Azure AD 访问令牌发送到中间层。
4. 中间层使用 Azure AD 令牌向 Azure 媒体 REST API 发送请求。
5. 中间层获取媒体服务返回的数据。

## <a name="naming-conventions"></a>命名约定

Azure 媒体服务 v3 资源名称（例如，资产、作业、转换）需遵循 Azure 资源管理器命名约束。 根据 Azure 资源管理器的要求，资源名称始终必须唯一。 因此，可以为资源名称使用任何唯一的标识符字符串（例如，GUID）。 

媒体服务资源名称不能包含“<”、“>”、“%”、“&”、“:”、“&#92;”、“?”、“/”、“*”、“+”、“.”、单引号或任何控制字符。 允许其他所有字符。 资源名称的最大长度为 260 个字符。 

有关 Azure 资源管理器命名的详细信息，请参阅：[命名需求](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource)和[命名约定](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。

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

[开始使用媒体服务 v3 API 使用 Sdk/工具进行开发](developers-guide.md)
