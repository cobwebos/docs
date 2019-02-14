---
title: 通过 API 控制台使用人工评审来审查内容 - 内容审查器
titlesuffix: Azure Cognitive Services
description: 使用评审 API 的审查操作为人工审查创建图像或文本审查。
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: d963cdd484727ac46aed1915643dd92462e2c643
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55857799"
---
# <a name="create-reviews-from-the-api-console"></a>从 API 控制台中创建审查

使用评审 API 的[审查操作](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)为人工审查创建图像或文本审查。 人工审查器使用审查工具审查内容。 基于审查后业务逻辑执行此操作。 通过使用任何内容审查器图像或文本 API 或其他认知服务 API 扫描内容后使用它。 

在人工审查器审查自动分配的标记和预测数据并提交了最终审核决定后，评审 API 会将所有信息提交到 API 终结点。

## <a name="use-the-api-console"></a>使用 API 控制台
若要使用联机控制台试用 API，需要在控制台中输入下面一些值：

- **teamName**：在设置审查工具帐户时创建的团队名称。 
- **ContentId**：此字符串将传递给 API，并通过回调返回。 ContentId 在关联内部标识符或元数据与审查作业结果时十分有用。
- **元数据**：回叫期间返回到 API 终结点的自定义键值对。 如果此键是审查工具中定义的短代码，则显示为标记。
- **Ocp-Apim-Subscription-Key**：位于“设置”选项卡中。有关详细信息，请参阅[概述](overview.md)。

从“凭据”窗口访问是访问测试工作台的最简单的方法。

1.  在“凭据”窗口中，选择[审查 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)。

  “审查 - 创建”页面打开。

2.  若要打开 API 测试控制台，选择最贴切地描述你所在位置的区域。

  ![审查 - 创建页面区域选择](images/test-drive-region.png)

  “审查 - 创建”API 控制台打开。
  
3.  为需要的查询参数、内容类型和订阅密钥输入值。 在“请求正文”框中，指定内容（例如图像位置）、元数据以及与内容关联的其他信息。

  ![审查 - 创建控制台查询参数、标头和请求正文框](images/test-drive-review-1.PNG)
  
4.  选择“发送”。 创建审查 ID。 复制此 ID，然后在以下步骤中使用它。

  ![审查 - 创建控制台响应内容框显示审查 ID](images/test-drive-review-2.PNG)
  
5.  选择“获取”，然后通过选择匹配区域的按钮打开 API。 在结果页面上，为“teamName”、“ReviewID”和“订阅密钥”输入值。 选择页面上的“发送”按钮。 

  ![审查 - 创建控制台获取结果](images/test-drive-review-3.PNG)
  
6.  会看到扫描的结果。

  ![审查 - 创建控制台响应内容框](images/test-drive-review-4.PNG)
  
7.  在内容审查器仪表板上，选择“审查” > “图像”。 出现已扫描图像，可供人工审查。

  ![足球的审查工具图像](images/test-drive-review-5.PNG)

## <a name="next-steps"></a>后续步骤

若要与你的应用程序集成，请在代码中使用 REST API，或者从[审查 .NET 快速入门](moderation-reviews-quickstart-dotnet.md)开始。
