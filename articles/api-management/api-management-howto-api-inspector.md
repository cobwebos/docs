---
title: 在 Azure API 管理中使用请求跟踪调试 API | Microsoft Docs
description: 遵循本教程的步骤了解如何在 Azure API 管理中检查请求处理步骤。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: fc5e8c7a7aa0d4693d96c3405ec0e180a6d13f8e
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768515"
---
# <a name="debug-your-apis-using-request-tracing"></a>使用请求跟踪调试 API

本教程介绍如何检查请求处理，以帮助对 API 进行调试和故障排除。 

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 跟踪调用

![API 检查器](media/api-management-howto-api-inspector/api-inspector001.PNG)

## <a name="prerequisites"></a>必备条件

+ 了解 [Azure API 管理术语](api-management-terminology.md)。
+ 请完成以下快速入门：[创建一个 Azure API 管理实例](get-started-create-service-instance.md)。
+ 此外，请完成以下教程：[导入和发布第一个 API](import-and-publish.md)。

## <a name="trace-a-call"></a>跟踪调用

![API 跟踪](media/api-management-howto-api-inspector/06-DebugYourAPIs-01-TraceCall.png)

1. 选择“API”。 
2. 在 API 列表中单击“演示会议 API”。 
3. 切换到“测试”  选项卡。
4. 选择“GetSpeakers”操作。 
5. 请确保包含名为 **Ocp-Apim-Trace**、值设置为 **true** 的 HTTP 标头。

   > [!NOTE]
   > * 如果 Ocp-Apim-Subscription-Key 未自动填充，可以通过转到开发人员门户并在配置文件页面上公开密钥来检索它。
   > * 若若要在使用 Ocp-Apim-Trace HTTP 标头时获取跟踪，必须启用订阅密钥的“允许跟踪”  设置。 若要配置“允许跟踪”  设置，请在左侧菜单中的“API 管理”  下，选择“订阅”  。
   >   ![允许在“API 管理订阅”窗格上进行跟踪](media/api-management-howto-api-inspector/allowtracing.png)

6. 单击“发送”发出 API 调用。  
7. 等待调用完成。 
8. 在“API 控制台”中转到“跟踪”选项卡。   可以单击以下任一链接跳转到详细跟踪信息：“入站”、“后端”或“出站”。   

    在“入站”部分，可以看到 API 管理从调用方收到的原始请求，以及应用到该请求的所有策略，包括步骤 2 中添加的 rate-limit 和 set-header 策略。 

    在“后端”部分，可以看到 API 管理发送到 API 后端的请求以及收到的响应。 

    在“出站”部分，可以看到在将响应发回给调用方之前应用到该响应的所有策略。 

    > [!TIP]
    > 每个步骤还显示了自 API 管理收到请求以来消逝的时间。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 跟踪调用

转到下一教程：

> [!div class="nextstepaction"]
> [使用修订版](api-management-get-started-revise-api.md)
