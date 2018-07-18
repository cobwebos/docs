---
title: 为 QnA Maker 服务创建业务连续性计划 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 如何为 QnA Maker 服务创建业务连续性计划
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: ca6e54b8a8ca8b38e8ef6b1a148f8b2c54bd43da
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366262"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>为 QnA Maker 服务创建业务连续性计划

业务连续性计划的主要目标是创建弹性知识库终结点，确保机器人或应用程序使用该终结点时不会发生停机。

![QnA Maker bcp 计划](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

上述高级想法如下：

1. 在 [Azure 配对区域](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions)中设置两个并行 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)。

2. 使主要和辅助 Azure 搜索索引保持同步。使用[此处](https://github.com/pchoudhari/QnAMakerBackupRestore)的 github 示例了解如何备份 - 还原 Azure 索引。

3. 使用[连续导出](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-export-telemetry)备份 Application Insights。

4. 主要和辅助堆栈设置完成后，使用[流量管理器](https://docs.microsoft.com/en-us/azure/traffic-manager/)配置两个终结点并设置路由方法。

5. 需要创建流量管理器终结点的 SSL 证书。 在应用服务中[绑定 SSL 证书](https://docs.microsoft.com/en-us/azure/app-service/app-service-web-tutorial-custom-ssl)。

6. 最后，在机器人或应用中使用流量管理器终结点。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [为 QnA Maker 开发选择容量](../Tutorials/choosing-capacity-qnamaker-deployment.md)
