---
title: 在 Azure 数据工厂中进行迭代开发和调试
description: 了解如何在 ADF UX 中以迭代方式开发和调试数据工厂管道
ms.date: 08/28/2020
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.openlocfilehash: 7e27a9c11f6470a48f20d54ebbd32c06824940de
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079287"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>使用 Azure 数据工厂进行迭代开发和调试
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

通过 Azure 数据工厂，你可以在开发数据集成解决方案时以迭代方式开发和调试数据工厂管道。 这些功能使你能够在创建拉取请求或将其发布到数据工厂服务之前测试更改。 

有关此功能的 8 分钟简介和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="debugging-a-pipeline"></a>调试管道

使用管道画布进行创作时，可以使用 **调试** 功能来测试活动。 执行测试运行时，在选择“调试”之前，不需要将更改发布到数据工厂。  当希望确保更改在你更新数据工厂工作流之前按预期工作时，此功能很有帮助。

![管道画布上的调试功能](media/iterative-development-debugging/iterative-development-1.png)

管道运行时，可以在管道画布的 " **输出** " 选项卡中查看每个活动的结果。

在管道画布的“输出”  窗口中查看测试运行的结果。

![管道画布“输出”窗口](media/iterative-development-debugging/iterative-development-2.png)

在测试运行成功后，向管道中添加更多活动并继续以迭代方式进行调试。 还可以在测试运行正在执行时将其**取消**。

> [!IMPORTANT]
> 选择”调试”  会实际运行管道。 例如，如果管道包含复制活动，则测试运行将数据从源复制到目标。 因此，在调试时，建议在复制活动和其他活动中使用测试文件夹。 在调试管道后，切换到要在正常操作中使用的实际文件夹。

### <a name="setting-breakpoints"></a>设置断点

使用 Azure 数据工厂可以调试管道，直到到达管道画布上的特定活动。 在要测试的活动上放置一个断点，然后选择 " **调试**"。 数据工厂会确保测试仅运行到管道画布上的断点活动。 如果不想测试整个管道，只想测试该管道内的一部分活动，则此“调试至”  功能非常有用。

![管道画布上的断点](media/iterative-development-debugging/iterative-development-3.png)

若要设置断点，请选择管道画布上的元素。 “调试至”  选项在元素的右上角显示为空心的红色圆圈。

![在所选元素上设置断点之前](media/iterative-development-debugging/iterative-development-4.png)

选择“调试至”选项后，它将变为实心的红色圆圈，以指示已启用断点  。

![在所选元素上设置断点之后](media/iterative-development-debugging/iterative-development-5.png)

## <a name="monitoring-debug-runs"></a>监视调试运行

运行管道调试运行时，结果将显示在管道画布的 " **输出** " 窗口中。 "输出" 选项卡只包含在当前浏览器会话过程中出现的最新运行。 

![管道画布“输出”窗口](media/iterative-development-debugging/iterative-development-2.png)

若要查看调试运行的历史视图或查看所有活动的调试运行的列表，你可以进入 **监视** 体验。 

![选择查看活动调试运行图标](media/iterative-development-debugging/view-debug-runs.png)

> [!NOTE]
> Azure 数据工厂服务仅将调试运行历史记录保留15天。 

## <a name="debugging-mapping-data-flows"></a>调试映射数据流

映射数据流允许生成大规模运行的无代码数据转换逻辑。 构建逻辑时，可以打开调试会话，使用实时 Spark 群集以交互方式处理数据。 若要了解详细信息，请参阅 [映射数据流调试模式](concepts-data-flow-debug-mode.md)。

可以在 **监视器** 体验中跨工厂监视活动数据流调试会话。

![查看数据流调试会话](media/iterative-development-debugging/view-dataflow-debug-sessions.png)
 
### <a name="debugging-a-pipeline-with-a-data-flow-activity"></a>使用数据流活动调试管道

运行带有数据流的调试运行时，有两个选项可供使用。 你可以使用现有调试群集，或为数据流启动新的实时群集。

使用现有的调试会话可大大减少数据流启动时间，因为群集已在运行，但不建议用于复杂或并行工作负荷，因为多个作业同时运行时可能会失败。 

使用活动运行时将使用在每个数据流活动的集成运行时中指定的设置创建一个新的群集。 这允许隔离每个作业，并将其用于复杂的工作负载或性能测试。

![使用数据流运行管道](media/iterative-development-debugging/iterative-development-dataflow.png)

## <a name="next-steps"></a>后续步骤

测试更改后，使用 [Azure 数据工厂中的持续集成和部署](continuous-integration-deployment.md)将其升级到更高的环境。
