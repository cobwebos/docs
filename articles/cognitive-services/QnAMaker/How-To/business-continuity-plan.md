---
title: 业务连续性计划 - QnA Maker
description: 业务连续性计划的主要目标是创建弹性知识库终结点，确保机器人或应用程序使用该终结点时不会发生停机。
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 5d1501ecc42fe948959075cec7d728f6c9df908a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "80887056"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>为 QnA Maker 服务创建业务连续性计划

业务连续性计划的主要目标是创建弹性知识库终结点，确保机器人或应用程序使用该终结点时不会发生停机。

## <a name="business-continuity-with-traffic-manager"></a>流量管理器的业务连续性

> [!div class="mx-imgBorder"]
> ![QnA Maker bcp 计划](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

上述高级想法如下：

1. 在 [Azure 配对区域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)中设置两个并行 [QnA Maker 服务](set-up-qnamaker-service-azure.md)。

2. [备份](../../../app-service/manage-backup.md)主 QnA Maker 应用服务，并在辅助安装程序中将其[还原](../../../app-service/web-sites-restore.md)。 这将确保两个设置使用相同的主机名和密钥。

3. 使主要和辅助 Azure 搜索索引保持同步。在[此处](https://github.com/pchoudhari/QnAMakerBackupRestore)使用 GitHub 示例，了解如何备份-还原 Azure 索引。

4. 使用[连续导出](../../../application-insights/app-insights-export-telemetry.md)备份 Application Insights。

5. 主要和辅助堆栈设置完成后，使用[流量管理器](../../../traffic-manager/traffic-manager-overview.md)配置两个终结点并设置路由方法。

6. 需要为流量管理器终结点创建一个传输层安全性（TLS）（以前称为安全套接字层（SSL））证书。 绑定应用服务中[的 TLS/SSL 证书](../../../app-service/configure-ssl-bindings.md)。

7. 最后，在机器人或应用中使用流量管理器终结点。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [选择容量](./improve-knowledge-base.md)