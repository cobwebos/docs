---
title: "使用 Azure API 管理转换和保护 API | Microsoft Docs"
description: "了解如何使用配额和限制（速率限制）策略保护 API。"
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 772f3828d85c54e7b8bb44c857e555175b7444cc
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2017
---
# <a name="transform-and-protect-your-api"></a>转换和保护 API 

本教程介绍如何转换 API，使其不会透露私密的后端信息。 例如，你可能想要隐藏有关后端上运行的技术堆栈的信息。 此外，还可能想要隐藏 API HTTP 响应正文中显示的原始 URL，而不是将其重定向到 APIM 网关。

本教程介绍如何使用 Azure API 管理配置速率限制，轻松为后端 API 添加保护。 例如，可以限制 API 的调用次数，以防开发人员过度使用它。 有关详细信息，请参阅 [API 管理策略](api-management-policies.md)

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 转换 API 以剥离响应标头
> * 将 API 响应正文中的原始 URL 替换为 APIM 网关 URL
> * 通过添加速率限制策略（限制）来保护 API
> * 测试转换

![策略](./media/transform-api/api-management-management-console.png)

## <a name="prerequisites"></a>先决条件

+ 完成以下快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)。
+ 此外，请完成以下教程：[导入并发布第一个 API](import-and-publish.md)。
 
[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>转换 API 以剥离响应标头

本部分介绍如何隐藏不想要向用户显示的 HTTP 标头。 在此示例中，会删除 HTTP 响应中的以下标头：

* **X-Powered-By**
* **X-AspNet-Version**

### <a name="test-the-original-response"></a>测试原始响应

若要查看原始响应，请执行以下操作：

1. 选择“API”选项卡。
2. 在 API 列表中单击“演示会议 API”。
3. 选择“GetSpeakers”操作。
4. 单击屏幕顶部的“测试”选项卡。
5. 按屏幕底部的“发送”按钮。 

    可以看到如下所示的原始响应：

    ![策略](./media/transform-api/original-response.png)

### <a name="set-the-transformation-policy"></a>设置转换策略

1. 浏览到自己的 APIM 实例。
2. 选择“API”选项卡。
3. 在 API 列表中单击“演示会议 API”。
4. 选择“所有操作”。
5. 选择屏幕顶部的“设计”选项卡。
6. 在“出站处理”窗口中，单击三角形（铅笔旁边）。
7. 选择“代码编辑器”。
    
     ![编辑策略](./media/set-edit-policies/set-edit-policies01.png)
9. 将光标置于 **<outbound>** 元素内。
10. 在右侧窗口中的“转换策略”下面，单击“+ 设置 HTTP 标头”两次（以插入两个策略代码片段）。

    ![策略](./media/transform-api/transform-api.png)
11. 按如下所示修改 **<outbound>** 代码：

        <set-header name="X-Powered-By" exists-action="delete" />
        <set-header name="X-AspNet-Version" exists-action="delete" />
                
## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>将 API 响应正文中的原始 URL 替换为 APIM 网关 URL

本部分介绍如何隐藏 API HTTP 响应正文中显示的原始 URL，而不是将其重定向到 APIM 网关。

### <a name="test-the-original-response"></a>测试原始响应

若要查看原始响应，请执行以下操作：

1. 选择“API”选项卡。
2. 在 API 列表中单击“演示会议 API”。
3. 选择“GetSpeakers”操作。
4. 单击屏幕顶部的“测试”选项卡。
5. 按屏幕底部的“发送”按钮。 

    可以看到如下所示的原始响应：

    ![策略](./media/transform-api/original-response2.png)

### <a name="set-the-transformation-policy"></a>设置转换策略

1. 浏览到自己的 APIM 实例。
2. 选择“API”选项卡。
3. 在 API 列表中单击“演示会议 API”。
4. 选择“所有操作”。
5. 选择屏幕顶部的“设计”选项卡。
6. 在“出站处理”窗口中，单击三角形（铅笔旁边）。
7. 选择“代码编辑器”。
8. 将光标置于 **<outbound>** 元素内。
9. 在右侧窗口中的“转换策略”下面，单击“+ 查找并替换正文中的字符串”。
10. 修改 **<find-and-replace** 代码（在 **<outbound>** 元素中）以替换 URL，使之与 APIM 网关匹配。 例如：

        <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>通过添加速率限制策略（限制）来保护 API

本部分介绍如何通过配置速率限制来为后端 API 添加保护。 例如，可以限制 API 的调用次数，以防开发人员过度使用它。 在此示例中，对每个订阅 ID 设置的限制为每 15 秒 3 次调用。15 秒后，开发人员可以重试调用该 API。

1. 浏览到自己的 APIM 实例。
2. 选择“API”选项卡。
3. 在 API 列表中单击“演示会议 API”。
4. 选择“所有操作”。
5. 选择屏幕顶部的“设计”选项卡。
6. 在“入站处理”窗口中，单击三角形（铅笔旁边）。
7. 选择“代码编辑器”。
8. 将光标置于 **<inbound>** 元素内。
9. 在右侧窗口中的“访问限制策略”下面，单击“+ 限制每个键的调用速率”。
10. 将 **<rate-limit-by-key** 代码（在 **<inbound>** 元素中）修改为以下代码：

        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />

## <a name="test-the-transformations"></a>测试转换
        
此时，策略代码应如下所示：

    <policies>
        <inbound>
            <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
            <base />
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <set-header name="X-Powered-By" exists-action="delete" />
            <set-header name="X-AspNet-Version" exists-action="delete" />
            <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>

本部分的余下内容介绍如何测试本文中设置的策略转换。

### <a name="test-the-stripped-response-headers"></a>测试剥离响应标头

1. 浏览到自己的 APIM 实例。
2. 选择“API”选项卡。
3. 在 API 列表中单击“演示会议 API”。
4. 单击“GetSpeakers”操作。
5. 选择“测试”选项卡。
6. 按“发送”。

    可以看到，标头已剥离：

    ![策略](./media/transform-api/final-response1.png)

### <a name="test-the-replaced-url"></a>测试替换 URL

1. 浏览到自己的 APIM 实例。
2. 选择“API”选项卡。
3. 在 API 列表中单击“演示会议 API”。
4. 单击“GetSpeakers”操作。
5. 选择“测试”选项卡。
6. 按“发送”。

    可以看到，URL 已替换。

    ![策略](./media/transform-api/final-response2.png)

### <a name="test-the-rate-limit-throttling"></a>测试速率限制（限制）

1. 浏览到自己的 APIM 实例。
2. 选择“API”选项卡。
3. 在 API 列表中单击“演示会议 API”。
4. 单击“GetSpeakers”操作。
5. 选择“测试”选项卡。
6. 连续按“发送”三次。

    发送请求 3 次之后，会收到“429 请求过多”响应。
7. 等待大约 15 秒，然后再次按“发送”。 此时应会收到“200 正常”响应。

    ![限制](./media/transform-api/test-throttling.png)

## <a name="video"></a>视频

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]
> 
> 

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 转换 API 以剥离响应标头
> * 将 API 响应正文中的原始 URL 替换为 APIM 网关 URL
> * 通过添加速率限制策略（限制）来保护 API
> * 测试转换

转到下一教程：

> [!div class="nextstepaction"]
> [监视 API](api-management-howto-use-azure-monitor.md)
