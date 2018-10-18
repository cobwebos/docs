---
title: Azure Batch AI 服务配额和限制 | Microsoft Docs
description: 了解默认的 Azure Batch AI 配额、限制和约束，以及如何请求提高配额
services: batch-ai
documentationcenter: ''
author: johnwu10
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/08/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: cade124cefbd4e2e63ab4cb6fa4f22b3bd672ad0
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391804"
---
# <a name="batch-ai-service-quotas-and-limits"></a>Batch AI 服务配额和限制

与其他 Azure 服务一样，与 Batch AI 服务关联的某些资源存在限制。 在 Batch AI 中，这些限制是[提供](https://azure.microsoft.com/global-infrastructure/services/)服务的每个区域在订阅级别应用的默认配额。 本文将描述这些默认值，以及如何请求提高配额。

设计和增加 Batch AI 资源时，请记住这些配额。 例如，如果群集没有达到指定的节点目标数量，那么可能是已达到订阅的 Batch AI 核心限制。

如果打算在 Batch AI 中运行生产工作负荷，可能需要将一个或多个配额提高到默认值以上。

> [!NOTE]
> 配额是一种信用限制，不附带容量保证。 如果有大规模的容量需求，请联系 Azure 支持。
> 
> 

## <a name="resource-quotas"></a>资源配额

[!INCLUDE [azure-batch-ai-limits](../../includes/azure-batch-ai-limits.md)]

## <a name="other-limits"></a>其他限制

下面是严格的限制，不能超出。

| **资源** | **最大限制** |
| --- | --- |
| 每个资源组的最大工作区数 | 800 |
| 最大群集大小 | 100 个节点 |
| 每个节点的最大 GPU MPI 进程数 | 1-4 |
| 每个节点的最大 GPU 辅助角色数 | 1-4 |
| 最长作业生存期 | 7 天<sup>1</sup> |
| 每个节点的最大参数服务器数 | 1 |

<sup>1</sup> 最长生存期是指作业开始运行的时间及其完成的时间。 已完成的作业会无限期保存；最长生存期内未完成的作业的数据不可访问。

## <a name="view-batch-ai-quotas"></a>查看 Batch AI 配额

在 [Azure 门户][portal]中查看当前 Batch AI 订阅配额。

1. 在左窗格中，单击“所有服务”。 然后搜索 **Batch AI**，以单击方式打开服务。
2. 在 Batch AI 菜单上单击“使用情况 + 配额”。
3. 选择你的订阅以查看配额限制。

## <a name="increase-a-batch-ai-cores-quota"></a>提高 Batch AI 核心配额

执行以下步骤，使用 [Azure 门户][portal]请求提高 Batch AI 订阅的配额。 

1. 在左窗格中，单击“所有服务”。 然后搜索 **Batch AI**，以单击方式打开服务。
2. 在 Batch AI 菜单上单击“新建支持请求”。
3. 在“基本信息”中：
   
    a. “问题类型” > “配额”
   
    b. **订阅** > 选择自己的订阅。
   
    c. “配额类型” > “Batch AI”
   
    d. **支持计划** > 选择自己的支持计划。

    单击“下一步”。
4. 在“问题”中：
   
    a. 根据[业务影响情况][support_sev]选择“严重性”。
   
    b. 在“配额详细信息”中，指定位置、配额类型和资源类型。 指定要请求的新限制。 单击“保存并继续”。

    c. 可选 - 上传任何相关的文件，详细说明提高配额的原因。
   
    单击“下一步”。
5. 在“联系人信息”中：
   
    a. 选择“首选联系方法”。
   
    b. 输入并确认所需的联系人详细信息。
   
    单击“创建”提交支持请求。

提交支持请求后，Azure 支持人员将与你取得联系。 完成该请求最多需要 2 个工作日。


## <a name="next-steps"></a>后续步骤

熟悉配额限制以后，请查看以下文章，了解 Batch AI 的使用入门。

> [!div class="nextstepaction"]
> [Batch AI 快速入门教程](quickstart-tensorflow-training-cli.md)
> [Batch AI 方案](https://github.com/Azure/BatchAI/tree/master/recipes)
> [详细了解 Batch AI 资源](resource-concepts.md)

[portal]: https://portal.azure.com
[support_sev]: http://aka.ms/supportseverity