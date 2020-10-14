---
title: 使用 v3 API 进行开发
titleSuffix: Azure Media Services
description: 了解在使用媒体服务 v3 进行开发时适用于实体和 API 的规则。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 5d57a6705973fbd5ee39042404015347d75b49b3
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019760"
---
# <a name="develop-with-media-services-v3-apis"></a>使用媒体服务 v3 API 进行开发

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

作为开发者，可以利用媒体服务 [REST API](/rest/api/media/) 或客户端库，与 REST API 交互，以轻松创建、管理和维护自定义媒体工作流。 [媒体服务 v3](https://aka.ms/ams-v3-rest-sdk) API 基于 OpenAPI 规范（以前称为 Swagger）。

本文论述使用媒体服务 v3 进行开发时适用于实体和 API 的规则。

## <a name="accessing-the-azure-media-services-api"></a>访问 Azure 媒体服务 API

若要有权访问媒体服务资源和媒体服务 API，必须先进行身份验证。 媒体服务支持[基于 Azure Active Directory (Azure AD) 的身份验证](../../active-directory/fundamentals/active-directory-whatis.md)。 有两种常用的身份验证选项：
 
* **服务主体身份验证**：用于对服务进行身份验证（例如 Web 应用、函数应用、逻辑应用、API 和微服务）。 常常使用这种身份验证方法的应用程序是运行守护程序服务、中间层服务或计划作业的应用程序。 例如，对于 Web 应用而言，应始终有一个使用服务主体连接到媒体服务的中间层。
* **用户身份验证**：用于验证使用应用与媒体服务资源进行交互的用户。 交互式应用应先提示用户输入用户凭据。 例如，授权用户用来监视编码作业或实时传送视频流的管理控制台应用程序。

媒体服务 API 有两个要求：发出 REST API 请求的用户或应用有权访问媒体服务帐户资源，这些用户或应用使用“参与者”或“所有者”角色 。 使用“读者”角色可访问 API，但该角色只能执行“获取”或“列出”操作  。 有关详细信息，请参阅[对媒体服务帐户基于角色的访问控制](rbac-overview.md)。

请考虑使用托管标识（而不是创建服务主体），以便 Azure 资源通过 Azure 资源管理器访问媒体服务 API。 若要详细了解 Azure 资源托管标识，请参阅[什么是 Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

### <a name="azure-ad-service-principal"></a>Azure AD 服务主体

Azure AD 的应用程序和服务主体应在同一个租户中。 创建应用后，向应用授予对媒体服务帐户的“参与者”或“所有者”角色访问权限 。

如果不确定自己是否有权创建 Azure AD 应用，请查看[需要的权限](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)。

在下图中，数字表示按时间顺序的请求流：

![从 Web API 向 AAD 进行中间层应用的身份验证](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. 中间层应用请求获取包含以下参数的 Azure AD 访问令牌：  

   * Azure AD 租户终结点。
   * 媒体服务资源 URI。
   * REST 媒体服务的资源 URI。
   * Azure AD 应用值：客户端 ID 和客户端密码。

   若要获取需要的所有值，请参阅[访问 Azure 媒体服务 API](./access-api-howto.md)。

2. Azure AD 访问令牌发送到中间层。
4. 中间层使用 Azure AD 令牌向 Azure 媒体 REST API 发送请求。
5. 中间层获取媒体服务返回的数据。

### <a name="samples"></a>示例

查看演示如何连接 Azure AD 服务主体的以下示例：

* [通过 REST 进行连接](media-rest-apis-with-postman.md)  
* [通过 Java 进行连接](configure-connect-java-howto.md)
* [通过 .NET 进行连接](configure-connect-dotnet-howto.md)
* [通过 Node.js 进行连接](configure-connect-nodejs-howto.md)
* [通过 Python 进行连接](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>命名约定

Azure 媒体服务 v3 资源名称（例如，资产、作业、转换）需遵循 Azure 资源管理器命名约束。 根据 Azure 资源管理器的要求，资源名称始终必须唯一。 因此，可以为资源名称使用任何唯一的标识符字符串（例如，GUID）。

媒体服务资源名称不能包含“<”、“>”、“%”、“&”、“:”、“&#92;”、“?”、“/”、“*”、“+”、“.”、单引号或任何控制字符。 允许其他所有字符。 资源名称的最大长度为 260 个字符。

如需详细了解 Azure 资源管理器的命名，请参阅[命名要求](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource)和[命名约定](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)。

### <a name="names-of-filesblobs-within-an-asset"></a>资产内的文件名/blob 名

资产内的文件名/blob 名必须符合 [blob 名称要求](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)和 [NTFS 名称要求](/windows/win32/fileio/naming-a-file)。 有这些要求的原因是，可将文件从 blob 存储复制到本地 NTFS 磁盘进行处理。

## <a name="long-running-operations"></a>长期运行的操作

在 Azure 媒体服务的 [swagger 文件](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json)中标记有 `x-ms-long-running-operation` 的操作为长期运行的操作。 

如需了解如何跟踪异步 Azure 操作，请参阅[异步操作](../../azure-resource-manager/management/async-operations.md)。

媒体服务拥有以下长期运行的操作：

* [创建直播活动](/rest/api/media/liveevents/create)
* [更新直播活动](/rest/api/media/liveevents/update)
* [删除直播活动](/rest/api/media/liveevents/delete)
* [启动直播活动](/rest/api/media/liveevents/start)
* [停止直播活动](/rest/api/media/liveevents/stop)

  停止活动时，使用 `removeOutputsOnStop` 参数删除所有关联的实时输出。  
* [重置直播活动](/rest/api/media/liveevents/reset)
* [创建实时输出](/rest/api/media/liveevents/create)
* [删除实时输出](/rest/api/media/liveevents/delete)
* [创建流式处理终结点](/rest/api/media/streamingendpoints/create)
* [更新流式处理终结点](/rest/api/media/streamingendpoints/update)
* [删除流式处理终结点](/rest/api/media/streamingendpoints/delete)
* [启动流式处理终结点](/rest/api/media/streamingendpoints/start)
* [停止流式处理终结点](/rest/api/media/streamingendpoints/stop)
* [缩放流式处理终结点](/rest/api/media/streamingendpoints/scale)

成功提交某个长期运行的操作后，你收到消息“202 已接受”，必须使用返回的操作 ID 轮询操作的完成情况。

[跟踪异步 Azure 操作](../../azure-resource-manager/management/async-operations.md)一文深入说明了如何通过响应中返回的值跟踪异步 Azure 操作的状态。

对于给定的直播活动或任何与之相关的实时输出，仅支持一个长期运行的操作。 启动长期运行的操作后，必须先完成该操作，再为同一个直播活动或任何关联的实时输出启动下一个长期运行的操作。 对于拥有多个实时输出的直播活动，你必须等到对某个实时输出的长期运行的操作完成后，才能为另一个实时输出触发长期运行的操作。 

## <a name="sdks"></a>SDK

> [!NOTE]
> Azure 媒体服务 v3 SDK 不保证是线程安全的。 在开发多线程应用时，应添加自己的线程同步逻辑以保护客户端，或对每个线程使用新的 AzureMediaServicesClient 对象。 你还应该注意由代码提供给客户端的可选对象引入的多线程问题（如 .NET 中的 HttpClient 实例）。

|SDK 中 IsInRole 中的声明|参考|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[.NET 参考](/dotnet/api/overview/azure/mediaservices/management)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Java 参考](/java/api/overview/azure/mediaservices/management)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Python 参考](/python/api/overview/azure/mediaservices/management)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Node.js 参考](/javascript/api/overview/azure/mediaservices/management)| 
|[Go SDK](https://aka.ms/ams-v3-go-sdk) |[Go 参考](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>另请参阅

- [包含媒体服务事件的 EventGrid .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [媒体服务事件的定义](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Azure 媒体服务浏览器

[Azure 媒体服务浏览器](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) 是可供希望了解媒体服务的 Windows 客户使用的工具。 AMSE 是一个 Winforms/C# 应用程序，用于通过媒体服务对 VOD 和实时内容进行上传、下载、编码和流式传输。 AMSE 工具适用于希望在不编写任何代码的情况下测试媒体服务的客户。 对于希望使用媒体服务进行开发的客户，可以为其提供 AMSE 代码作为资源。

AMSE 是一个开源项目，由社区提供支持（可以将问题报告给 https://github.com/Azure/Azure-Media-Services-Explorer/issues) ）。 此项目采用了 [Microsoft 开放源代码行为准则](https://opensource.microsoft.com/codeofconduct/)。 有关详细信息，请参阅[行为准则常见问题解答](https://opensource.microsoft.com/codeofconduct/faq/)，如有任何其他问题或评论，请联系 opencode@microsoft.com。

## <a name="filtering-ordering-paging-of-media-services-entities"></a>媒体服务实体的筛选、排序和分页

请参阅 [Azure 媒体服务实体的筛选、排序、分页](entities-overview.md)。

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="see-also"></a>另请参阅

若要获取需要的所有值，请参阅[访问 Azure 媒体服务 API](./access-api-howto.md)。

## <a name="next-steps"></a>后续步骤

* [连接到与 Java 结合使用的媒体服务](configure-connect-java-howto.md)
* [连接到与 .NET 结合使用的媒体服务](configure-connect-dotnet-howto.md)
* [连接到与 Node.js 结合使用的媒体服务](configure-connect-nodejs-howto.md)
* [连接到与 Python 结合使用的媒体服务](configure-connect-python-howto.md)