---
title: 使用 Azure 门户手动添加 API | Microsoft Docs
description: 本教程介绍如何使用 API 管理 (APIM) 手动添加 API。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: ef7cfa0f30eaaa426c312b21ce0a73aa4409d2ec
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "33934971"
---
# <a name="add-an-api-manually"></a>手动添加 API 

本文中的步骤说明如何使用 Azure 门户手动将 API 添加到 API 管理 (APIM) 实例。 想要模拟某个 API 时，就往往需要创建一个空白 API 并手动对其进行定义。 有关模拟 API 的详细信息，请参阅[模拟 API 响应](mock-api-responses.md)。

若要导入现有的 API，请参阅[相关主题](#related-topics)部分。

本文将会创建一个空白的 API，并将 [httpbin.org](http://httpbin.org)（一个公共测试服务）指定为后端 API。

## <a name="prerequisites"></a>先决条件

完成以下快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>创建 API

1. 在“API 管理”下面选择“API”。
2. 在左侧菜单中，选择“+ 添加 API”。
3. 从列表中选择“空白 API”。

    ![空白 API](media/add-api-manually/blank-api.png)
4. 输入 API 的设置。

    ![设置](media/add-api-manually/settings.png)

    |**Name**|**值**|**说明**|
    |---|---|---|
    |**显示名称**|“空白 API” |此名称显示在开发人员门户中。|
    |**Web 服务 URL**（可选）| "*http://httpbin.org*"| 若要模拟某个 API，可以不输入任何内容。 <br/>在此示例中，我们输入 [http://httpbin.org](http://httpbin.org)。这是一个公共测试服务。 <br/>若要导入已自动映射到后端的 API，请参阅[相关主题](#related-topics)部分中的主题之一。|
    |**URL 方案**|“*HTTPS*”|在本例中，尽管后端支持不安全的 HTTP 访问，但我们还是指定了对后端进行安全的 HTTPS APIM 访问。 <br/>此类方案（HTTPS 转 HTTP）称为 HTTPS 终结。 如果 API 位于虚拟网络（在其中，即使不使用 HTTPS，也能确认访问是安全的）中，则可以采用此方案。 <br/>可以使用“HTTPS 终结”来消减一些 CPU 周期。|
    |**URL 后缀**|“*hbin*”| 后缀是在此 APIM 实例中用于标识此特定 API 的名称。 它在此 APIM 实例中必须唯一。|
    |**产品**|“无限制” |通过关联 API 与产品来发布 API。 如果想要发布 API 并使其对开发人员可用，请将其添加到产品中。 可在 API 创建期间执行此操作，或稍后进行设置。<br/><br/>产品是一个或多个 API 的关联。 可包含多个 API，并通过开发人员门户将其提供给开发人员。 <br/>开发人员必须先订阅产品才能访问 API。 订阅时，他们会得到一个订阅密钥，此密钥对该产品中的任何 API 都有效。 如果创建了 APIM 实例，那么你已是管理员，因此默认情况下订阅了每个产品。<br/><br/> 每个 API 管理实例默认附带两个示例产品：“入门”和“无限制”。| 
5. 选择**创建**。

此时，APIM 中没有任何操作映射到后端 API 中的操作。 如果调用通过后端而不是通过 APIM 公开的操作，将收到 **404** 错误。 

>[!NOTE] 
> 默认情况下，在添加某个 API 时，除非已将某些操作列入白名单，否则即使该 API 已连接到某个后端服务，APIM 也不会公开任何操作。 若要将后端服务的某个操作列入白名单，请创建一个映射到后端操作的 APIM 操作。
>

## <a name="add-and-test-an-operation"></a>添加并测试操作

本部分介绍如何添加“/get”操作，以将其映射到后端“http://httpbin.org/get”操作。

### <a name="add-the-operation"></a>添加操作

1. 选择在上一步骤中创建的 API。
2. 单击“+ 添加操作”。
3. 在“URL”中，选择“GET”，并在资源中输入“/get”。
4. 输入“FetchData”作为“显示名称”。
5. 选择“保存”。

### <a name="test-the-operation"></a>测试操作

在 Azure 门户中测试操作。 或者，可以在**开发人员门户**中测试操作。

1. 选择“测试”选项卡。
2. 选择“FetchData”。
3. 按“发送”。

随后将显示“http://httpbin.org/get”操作生成的响应。 若要转换操作，请参阅[转换和保护 API](transform-api.md)。

## <a name="add-and-test-a-parameterized-operation"></a>添加并测试参数化操作

本部分介绍如何添加一个采用参数的操作。 在本例中，我们将该操作映射到“http://httpbin.org/status/200”。

### <a name="add-the-operation"></a>添加操作

1. 选择在上一步骤中创建的 API。
2. 单击“+ 添加操作”。
3. 在“URL”中，选择“GET”，并在资源中输入“/status/{code}”。 （可选）可以提供与此参数关联的某些信息。 例如，为“类型”输入“数字”，为“值”输入“200”（默认值）。
4. 输入“GetStatus”作为“显示名称”。
5. 选择“保存”。

### <a name="test-the-operation"></a>测试操作 

在 Azure 门户中测试操作。  或者，可以在**开发人员门户**中测试操作。

1. 选择“测试”选项卡。
2. 选择“GetStatus”。 默认情况下，代码值设置为“200”。 可以更改默认值以测试其他值。 例如，键入“418”。
3. 按“发送”。

    随后将显示“http://httpbin.org/status/200”操作生成的响应。 若要转换操作，请参阅[转换和保护 API](transform-api.md)。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转换和保护已发布的 API](transform-api.md)
