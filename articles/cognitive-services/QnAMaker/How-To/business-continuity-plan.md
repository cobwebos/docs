---
title: 业务连续性计划 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 业务连续性计划的主要目标是创建弹性知识库终结点，确保机器人或应用程序使用该终结点时不会发生停机。
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.openlocfilehash: 66d2b4667adaac6fa5d6bba2a5a78af3a0c2c255
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221107"
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
