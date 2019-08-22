---
title: 业务连续性计划 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 业务连续性计划的主要目标是创建弹性知识库终结点，确保机器人或应用程序使用该终结点时不会发生停机。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: 84f13f7e1d83f1ead00303b694b617d3ba1c8931
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876646"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>为 QnA Maker 服务创建业务连续性计划

业务连续性计划的主要目标是创建弹性知识库终结点，确保机器人或应用程序使用该终结点时不会发生停机。

![QnA Maker bcp 计划](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

上述高级想法如下：

1. 在 [Azure 配对区域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)中设置两个并行 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)。

2. 使主要和辅助 Azure 搜索索引保持同步。使用[此处](https://github.com/pchoudhari/QnAMakerBackupRestore)的 GitHub 示例了解如何备份 - 还原 Azure 索引。

3. 使用[连续导出](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)备份 Application Insights。

4. 主要和辅助堆栈设置完成后，使用[流量管理器](https://docs.microsoft.com/azure/traffic-manager/)配置两个终结点并设置路由方法。

5. 需要创建流量管理器终结点的 SSL 证书。 在应用服务中[绑定 SSL 证书](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)。

6. 最后，在机器人或应用中使用流量管理器终结点。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [为 QnA Maker 开发选择容量](../Tutorials/choosing-capacity-qnamaker-deployment.md)
