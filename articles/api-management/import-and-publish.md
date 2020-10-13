---
title: 教程 - 在 Azure API 管理中导入和发布第一个 API
description: 本教程将在 Azure 门户中将 OpenAPI 规范 API 导入 Azure API 管理并测试该 API。
author: mikebudzynski
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: apimpm
ms.openlocfilehash: 9ff64f57e61002101b4e2c560bdcd91863cc461e
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91626935"
---
# <a name="tutorial-import-and-publish-your-first-api"></a>教程：导入和发布第一个 API

本教程介绍如何将 JSON 格式的 OpenAPI 规范后端 API 导入 Azure API 管理。 Microsoft 提供本示例中使用的后端 API，并将其托管在 Azure 上 ([https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json))。

将后端 API 导入 API 管理后，API 管理 API 将成为后端 API 的外观。 可以在 API 管理中自定义该外观，而无需修改后端 API。 有关详细信息，请参阅[转换和保护 API](transform-api.md)。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 将 API 导入 API 管理
> * 在 Azure 门户中测试 API

导入后，可以在 Azure 门户中管理 API。

:::image type="content" source="media/import-and-publish/created-api.png" alt-text="API 管理中新的 API":::

## <a name="prerequisites"></a>先决条件

- 理解 [Azure API 管理的术语](api-management-terminology.md)。
- [创建一个 Azure API 管理实例](get-started-create-service-instance.md)。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-backend-api"></a>导入和发布后端 API

本部分介绍如何导入和发布 OpenAPI 规范后端 API。

1. 在 API 管理实例的左侧导航栏中，选择“API”。
1. 选择“OpenAPI”磁贴。
1. 在“基于 OpenAPI 规范创建”窗口中，选择“完全” 。
1. 输入下表中的值。 然后选择“创建”以创建 API。

   可以在创建 API 期间设置 API 值，或者以后转到“设置”选项卡进行设置。

   :::image type="content" source="media/import-and-publish/create-api.png" alt-text="API 管理中新的 API":::


   |设置|值|说明|
   |-------|-----|-----------|
   |**OpenAPI 规范**|*https:\//conferenceapi.azurewebsites.net?format=json*|实现 API 的服务。 API 管理将请求转发到此地址。|
   |**显示名称**|输入上述服务 URL 后，API 管理将根据 JSON 填写此字段。|显示在[开发人员门户](api-management-howto-developer-portal.md)中的名称。|
   |**名称**|输入上述服务 URL 后，API 管理将根据 JSON 填写此字段。|API 的唯一名称。|
   |**说明**|输入上述服务 URL 后，API 管理将根据 JSON 填写此字段。|API 的可选说明。|
   |**URL 方案**|**HTTPS**|哪些协议可以访问 API。|
   |**API URL 后缀**|会议|追加到 API 管理服务基 URL 的后缀。 API 管理根据 API 的后缀区分 API，因此后缀对于给定发布者的每个 API 必须唯一。|
   |**标记**| |用于组织搜索、分组或筛选 API 的标记。|
   |**产品**|**不受限制**|一个或多个 API 的关联。 每个 API 管理实例附带两个示例产品：**初学者**和**无限**。 通过将 API 关联到某个产品（在本示例中为“无限制”）来发布该 API。<br/><br/> 可在一个产品中包含多个 API，并通过开发人员门户将其提供给开发人员。 若要将此 API 添加到另一个产品，请键入或选择产品名称。 重复此步骤以将 API 添加到多个产品。 以后也可以从“设置”页将 API 添加到产品。<br/><br/>  有关产品的详细信息，请参阅[创建和发布产品](api-management-howto-add-products.md)。|
   |**网关**|**托管**|公开此 API 的 API 网关。 此字段仅在“开发人员”和“高级”层服务中提供。<br/><br/>“托管”指示内置于 API 管理服务中并由 Microsoft 托管在 Azure 中的网关。 [自承载网关](self-hosted-gateway-overview.md)仅在“高级”和“开发人员”服务层中提供。 可以将这些网关部署在本地或其他云中。<br/><br/> 如果未选择任何网关，则无法使用此 API，API 请求不会成功。|
   |**对此 API 进行版本控制？**|选择或取消选择|有关详细信息，请参阅[发布 API 的多个版本](api-management-get-started-publish-versions.md)。|

   > [!NOTE]
   > 若要向 API 使用者发布 API，必须将其与某个产品相关联。

2. 选择“创建”。

如果在导入 API 定义时遇到问题，请参阅[已知问题列表和限制](api-management-api-import-restrictions.md)。

## <a name="test-the-new-api-in-the-azure-portal"></a>在 Azure 门户中测试新的 API

可直接从 Azure 门户调用 API 操作，这样就可以方便地查看和测试操作。

1. 在 API 管理实例的左侧导航栏中，选择“API” > “演示会议 API” 。
1. 选择“测试”选项卡，然后选择“GetSpeakers”。  页面中会显示“查询参数”和“标头”（如果有）。  对于与此 API 关联的订阅密钥，系统会自动填写“Ocp-Apim-Subscription-Key”。
1. 选择“发送”。

   :::image type="content" source="media/import-and-publish/01-import-first-api-01.png" alt-text="API 管理中新的 API":::

   后端以“200 正常”和某些数据做出响应。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 导入第一个 API
> * 在 Azure 门户中测试 API

转到下一教程，了解如何创建和发布产品：

> [!div class="nextstepaction"]
> [创建和发布产品](api-management-howto-add-products.md)
