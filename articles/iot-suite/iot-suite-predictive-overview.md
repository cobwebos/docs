---
title: 预测性维护解决方案加速器概述 - Azure | Microsoft Docs
description: Azure IoT 预测性维护解决方案加速器的介绍。
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: b370b3d7-2ce5-4906-9818-3aeedd471ee3
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: 4b1667b6449f9e425ea601f47f91bfe8cce8538e
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
ms.locfileid: "34304201"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>预测性维护解决方案加速器概述

预测性维护[解决方案加速器][lnk_preconfigured_solutions]是 [Microsoft Azure IoT 解决方案加速器][lnk_iot_suite]其中的一种。 此解决方案将实时设备遥测收集与使用 [Azure 机器学习][lnk-machine-learning]创建的预测模型相集成。

使用 Azure IoT 解决方案加速器，可以快速方便地连接和监视资产，在仪表板和视觉对象中实时分析遥测数据。 在预测性维护解决方案加速器中，仪表板与视觉对象可以提供新的信息，从而提升你的效率并增加收入来源。

## <a name="the-scenario"></a>应用场景

Fabrikam 是一家区域性航空公司，致力于以优惠的价格为客户提供优质的体验。 维护问题是造成航班延误的原因之一，而飞机引擎维护又是其中最为棘手的项目。 因为必须严防飞行期间发生引擎故障，所以 Fabrikam 不仅会定期检查其引擎，而且会恪守所安排的维护计划。 但因为飞机引擎的问题不一， 所以有一些引擎维护工作并非必要。 更重要的是，若在执行维护工作之前发生问题，可能会造成飞机停飞。 如果飞机所在地点正好缺少合适的技术人员或备用零件，这些问题会造成尤其严重的损失。

Fabrikam 飞机的引擎由各种传感器进行检测，这些传感器监视飞行期间的引擎状况。 Fabrikam 使用预测性维护解决方案加速器收集飞行期间所收集的传感器数据。 经过多年累积引擎运行数据和故障数据之后，Fabrikam 的数据科学家制作出了一个模型，可以预测飞机引擎的剩余使用寿命 (RUL)。 该模型使用在四个引擎传感器提供的数据与最终会导致故障的引擎磨损之间存在的相关性。 Fabrikam 现在除了继续执行定期检查来确保安全之外，还会在每次飞行后，使用模型来计算每个引擎的 RUL。 该模型使用飞行期间从引擎收集的遥测数据。 Fabrikam 现在可以预测未来的故障点，并预先规划维护和维修工作。

> [!NOTE]
> 该解决方案模型使用实际的引擎损耗数据。

通过预测必要维护时间点，Fabrikam 可以优化各项作业，进而降低成本。

维护专员与排班专员一起合作来执行以下操作：

- 根据飞机在特定地点停机的时间规划维护。
- 确保飞机有足够的停飞时间，不会造成排班中断。
- 安排技术人员，确保飞机无需浪费时间等待，就可获得有效率的维修。

库存控制管理员会收到维护计划，因此可以优化其订单流程和备用零件库存。

这样安排活动不仅让 Fabrikam 可以将飞机停飞的时间降至最低，还可以降低运营成本，同时确保了乘客与乘务员的安全。

若要了解 [Azure IoT 解决方案加速器][lnk_iot_suite]如何提供客户实现预测性维护潜力所需的功能，请查看此[信息图][lnk_infographic]。

## <a name="how-the-predictive-maintenance-solution-accelerator-is-built"></a>如何生成预测性维护解决方案加速器

该解决方案利用可作为模板的现有 Azure 机器学习模型，演示这些功能如何运用通过 IoT 解决方案加速器服务收集而来的设备遥测数据。 Microsoft 基于公开的数据<sup>\[1\]</sup> 构建了飞机引擎的[回归模型][lnk_regression_model]，并发布了有关如何使用该模型的分步指南。

Azure IoT 预测性维护解决方案加速器使用通过此模板创建的回归模型。 该模型将部署到 Azure 订阅，通过自动生成的 API 公开。 该解决方案包含代表 4 个（共 100 个）引擎和 4 个（共 21 个）传感器数据流的测试数据的子集。 该数据可通过已训练模型提供精确的结果。

*\[1\] A. Saxena and K. Goebel (2008)."Turbofan Engine Degradation Simulation Data Set", NASA Ames Prognostics Data Repository (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/), NASA Ames Research Center, Moffett Field, CA*

## <a name="get-started-with-predictive-maintenance"></a>预测性维护入门

本教程介绍如何预配预测性维护解决方案。 此外，还逐步讲解了预测性维护解决方案加速器的基本功能。 可以通过随解决方案加速器一起部署的解决方案仪表板来访问其中的多项功能。

需要有效的 Azure 订阅才能完成此教程。

> [!NOTE]
> 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用][lnk_free_trial]。

1. 使用 Azure 帐户凭据登录 [azureiotsuite.com][lnk-azureiotsuite]，并单击“+”创建解决方案。
1. 单击“选择预测性维护”磁贴。
1. 为预测性维护解决方案加速器输入“解决方案名称”。
1. 选择要用于预配解决方案的“区域”和“订阅”。
1. 单击“创建解决方案”  开始预配过程。 此过程通常需要数分钟的运行时间。

### <a name="wait-for-the-provisioning-process-to-complete"></a>等待预配过程完成

1. 单击状态为“正在预配”的解决方案磁贴。
1. 请在 Azure 服务部署到 Azure 订阅中时，注意“预配状态”  。
1. 预配完成之后，状态将更改为“就绪”。
1. 单击磁贴，会在右边窗格中看到解决方案的详细信息。 通过此窗格可以启动解决方案仪表板，并访问机器学习工作区。

> [!NOTE]
> 如果在部署解决方案加速器时遇到问题，请查看 [azureiotsuite.com 站点上的权限][lnk-permissions]和[常见问题解答][lnk-faq]。 如果问题仍然存在，请在[门户][lnk-portal]中创建服务票证。

是否有你预期会看到但没有列出的解决方案详细信息？ 请在[用户之声](https://feedback.azure.com/forums/321918-azure-iot)中提供功能建议。

## <a name="view-the-solution"></a>查看解决方案

本部分逐步讲解解决方案 UI。

### <a name="predictive-maintenance-dashboard"></a>预见性维护仪表板

Web 应用程序中的此页面会使用 PowerBI JavaScript 控件（请参阅 [PowerBI 可视化效果存储库][lnk-powerbi]）以可视化方式呈现：

* blob 存储中流分析作业的输出数据。
* 每个飞机引擎的 RUL 和周期计数。

### <a name="observing-the-behavior-of-the-cloud-solution"></a>观察云解决方案的行为

在 Azure 门户中，导航到具有你选定的解决方案名称的资源组，以查看已预配的资源。

![][img-resource-group]

预配该解决方案加速器时，会收到一封电子邮件，其中包含机器学习工作区的链接。 还可导航到预配解决方案的 [azureiotsuite.com][lnk-azureiotsuite] 页面的机器学习工作区。 当解决方案处于“就绪”状态时，该页中会提供一个磁贴。

![][img-machine-learning]

在解决方案门户中，可以看到本示例预配了四个模拟设备，表示各有 2 个引擎的 2 架飞机，以及每个引擎带有的 4 个传感器。 第一次导航到解决方案门户时，模拟便会停止。

![][img-simulation-stopped]

单击“开始模拟”开始模拟。 传感器历史记录、RUL、周期和 RUL 历史记录会填充到仪表板中。

![][img-simulation-running]

当 RUL 小于 160 时（出于演示目的而选择的任意阈值），解决方案门户会在 RUL 显示器旁边显示警告符号。 解决方案门户也会以黄色突出显示飞机引擎。 你会注意到 RUL 值有整体向下的趋势，但呈上下波动形式。 此行为是因为周期长度和模型精确度不同的缘故。

![][img-simulation-warning]

完整模拟需要约 35 分钟的时间才能完成 148 个周期。 160 RUL 阈值第一次在大约 5 分钟的时候达到，而这两个引擎在大约 8 分钟的时候同时达到阈值。

模拟会彻底运行 148 个周期的完整数据集并确定最终的 RUL 和周期值。

可以随时停止模拟，但单击“开始模拟”会从数据集的开头重播模拟。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure IoT 如何实现预见性维护方案的详细信息，请阅读[捕获物联网的价值][lnk_capture_value]。

[演练][lnk-predictive-walkthrough]预测性维护解决方案加速器。

还可以浏览 IoT 解决方案加速器的一些其他特性和功能：

* [IoT 解决方案加速器的常见问题解答][lnk-faq]
* [从源头保障 IoT 的安全][lnk-security-groundup]

[img-resource-group]: media/iot-suite-predictive-overview/resource-group.png
[img-simulation-stopped]: media/iot-suite-predictive-overview/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-overview/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-overview/simulation-warning.png
[img-machine-learning]: media/iot-suite-predictive-overview/machine-learning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]:../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md
[lnk_iot_suite]: iot-suite-options.md
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3

[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF
[lnk-faq]: iot-suite-v1-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-portal]: http://portal.azure.com/
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/