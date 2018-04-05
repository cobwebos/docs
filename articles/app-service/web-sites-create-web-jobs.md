---
title: 在 Azure 应用服务中使用 WebJobs 运行后台任务
description: 了解如何使用 WebJobs 在 Azure 应用服务 Web 应用、API 应用或移动应用中运行后台任务。
services: app-service
documentationcenter: ''
author: tdykstra
manager: erikre
editor: jimbe
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2017
ms.author: glenga;david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: f41cc83bfb18146e46e7d8501318acd68ce9c421
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>在 Azure 应用服务中使用 WebJobs 运行后台任务

## <a name="overview"></a>概述
WebJobs 是 [Azure 应用服务](https://docs.microsoft.com/azure/app-service/)的一项功能，可以在与 Web 应用、API 应用或移动应用相同的上下文中运行程序或脚本。 使用 Web 作业无需支付额外的费用。

本文介绍如何使用 [Azure 门户](https://portal.azure.com)部署 WebJobs，以便上传可执行文件或脚本。 有关如何使用 Visual Studio 开发和部署 WebJobs 的信息，请参阅[使用 Visual Studio 部署 WebJobs](websites-dotnet-deploy-webjobs.md)。

可以结合 WebJobs 使用 Azure WebJobs SDK 来简化许多编程任务。 有关详细信息，请参阅[什么是 WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)。

Azure Functions 提供了运行程序和脚本的另一种方式。 有关 WebJobs 与 Functions 之间的比较，请参阅[在 Flow、逻辑应用、Functions 与 WebJobs 之间选择](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)。

## <a name="webjob-types"></a>Web 作业类型

下表描述了*连续*和*触发* Web 作业之间的差别。


|连续  |触发  |
|---------|---------|
| 创建 Web 作业后立即启动。 若要防止作业终止，程序或脚本通常在无限循环中执行其工作。 如果作业确实终止，可将其重启。 | 仅当手动触发或按计划触发时启动。 |
| 在运行 Web 应用的所有实例上运行。 可以选择性地将 Web 作业限制为单个实例。 |在 Azure 选择用于负载均衡的单个实例上运行。|
| 支持远程调试。 | 不支持远程调试。|

> [!NOTE]
> Web 应用可在进入非活动状态 20 分钟后超时。 只有在门户中针对 scm（部署）站点或 Web 应用页面发出的请求才能重置计时器。 针对实际站点发出的请求不会重置计时器。 如果应用运行连续或计划的 Web 作业，可启用 **Always On** 来确保 Web 作业可靠运行。 此功能仅在基本、标准和高级[定价层](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)中提供。

## <a name="acceptablefiles"></a>支持的脚本或程序文件类型

支持以下文件类型：

* .cmd、.bat、.exe（使用 Windows cmd）
* .ps1（使用 PowerShell）
* .sh（使用 Bash）
* .php（使用 PHP）
* .py（使用 Python）
* .js（使用 Node.js）
* .jar（使用 Java）

## <a name="CreateContinuous"></a>创建连续 Web 作业

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. 在 [Azure 门户](https://portal.azure.com)中，转到应用服务 Web 应用、API 应用或移动应用的“应用服务”页。

2. 选择“Web 作业”。

   ![选择“Web 作业”](./media/web-sites-create-web-jobs/select-webjobs.png)

2. 在“Web 作业”页中，选择“添加”。

    ![“Web 作业”页](./media/web-sites-create-web-jobs/wjblade.png)

3. 使用表中指定的“添加 Web 作业”设置。

   ![“添加 Web 作业”页](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | 设置      | 示例值   | 说明  |
   | ------------ | ----------------- | ------------ |
   | **Name** | myContinuousWebJob | 在应用服务应用中唯一的名称。 必须以字母或数字开头，且不能包含除“-”和“_”以外的特殊字符。 |
   | **文件上传** | ConsoleApp.zip | 一个 *.zip* 文件，其中包含可执行文件或脚本文件，以及运行程序或脚本所需的所有支持文件。 [支持的文件类型](#acceptablefiles)部分中列出了支持的可执行文件或脚本文件类型。 |
   | **类型** | 连续 | 本文前面介绍了 [Web 作业类型](#webjob-types)。 |
   | **缩放** | 多实例 | 仅适用于连续 Web 作业。 确定程序或脚本是在所有实例还是只在一个实例上运行。 指定要在多个实例上运行的选项不适用于免费或共享[定价层](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 | 

4. 单击“确定”。

   新的 Web 作业随即显示在“Web 作业”页上。

   ![Web 作业列表](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. 要停止或重启某个连续 Web 作业，请在列表中右键单击该 Web 作业，并单击“停止”或“启动”。

    ![停止连续 Web 作业](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="CreateOnDemand"></a>创建手动触发的 Web 作业

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. 在 [Azure 门户](https://portal.azure.com)中，转到应用服务 Web 应用、API 应用或移动应用的“应用服务”页。

2. 选择“Web 作业”。

   ![选择“Web 作业”](./media/web-sites-create-web-jobs/select-webjobs.png)

2. 在“Web 作业”页中，选择“添加”。

    ![“Web 作业”页](./media/web-sites-create-web-jobs/wjblade.png)

3. 使用表中指定的“添加 Web 作业”设置。

   ![“添加 Web 作业”页](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | 设置      | 示例值   | 说明  |
   | ------------ | ----------------- | ------------ |
   | **Name** | myTriggeredWebJob | 在应用服务应用中唯一的名称。 必须以字母或数字开头，且不能包含除“-”和“_”以外的特殊字符。|
   | **文件上传** | ConsoleApp.zip | 一个 *.zip* 文件，其中包含可执行文件或脚本文件，以及运行程序或脚本所需的所有支持文件。 [支持的文件类型](#acceptablefiles)部分中列出了支持的可执行文件或脚本文件类型。 |
   | **类型** | 触发 | 本文前面介绍了 [Web 作业类型](#webjob-types)。 |
   | **触发器** | 手动 | |

4. 单击“确定”。

   新的 Web 作业随即显示在“Web 作业”页上。

   ![Web 作业列表](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. 要运行 WebJob，请在列表中右键单击其名称，并单击“运行”。
   
    ![运行 Web 作业](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="CreateScheduledCRON"></a>创建计划的 Web 作业

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. 在 [Azure 门户](https://portal.azure.com)中，转到应用服务 Web 应用、API 应用或移动应用的“应用服务”页。

2. 选择“Web 作业”。

   ![选择“Web 作业”](./media/web-sites-create-web-jobs/select-webjobs.png)

2. 在“Web 作业”页中，选择“添加”。

   ![“Web 作业”页](./media/web-sites-create-web-jobs/wjblade.png)

3. 使用表中指定的“添加 Web 作业”设置。

   ![“添加 Web 作业”页](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | 设置      | 示例值   | 说明  |
   | ------------ | ----------------- | ------------ |
   | **Name** | myScheduledWebJob | 在应用服务应用中唯一的名称。 必须以字母或数字开头，且不能包含除“-”和“_”以外的特殊字符。 |
   | **文件上传** | ConsoleApp.zip | 一个 *.zip* 文件，其中包含可执行文件或脚本文件，以及运行程序或脚本所需的所有支持文件。 [支持的文件类型](#acceptablefiles)部分中列出了支持的可执行文件或脚本文件类型。 |
   | **类型** | 触发 | 本文前面介绍了 [Web 作业类型](#webjob-types)。 |
   | **触发器** | 计划 | 要使计划可靠运行，请启用 Always On 功能。 Always On 仅在基本、标准和高级定价层中提供。|
   | **CRON 表达式** | 0 0/20 * * * * | 以下部分介绍了 [CRON 表达式](#cron-expressions)。 |

4. 单击“确定”。

   新的 Web 作业随即显示在“Web 作业”页上。

   ![Web 作业列表](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="cron-expressions"></a>CRON 表达式

可以在门户中输入 [CRON 表达式](../azure-functions/functions-bindings-timer.md#cron-expressions)，或者在 Web 作业 .zip 文件的根目录中包含一个 `settings.job` 文件，如以下示例中所示：

```json
{
    "schedule": "0 */15 * * * *"
}
``` 

> [!NOTE]
> 从 Visual Studio 部署 Web 作业时，请将 `settings.job` 文件属性标记为“如果较新则复制”。

## <a name="ViewJobHistory"></a>查看作业历史记录

1. 选择要查看其历史记录 Web 作业，并选择“日志”按钮。
   
   ![“日志”按钮](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. 在“Web 作业详细信息”页中，选择一个时间以查看一个运行轮次的详细信息。
   
   ![Web 作业详细信息](./media/web-sites-create-web-jobs/webjobdetails.png)

3. 在“Web 作业运行详细信息”页中，选择“切换输出”查看日志内容的文本。
   
    ![Web 作业运行详细信息](./media/web-sites-create-web-jobs/webjobrundetails.png)

   若要在单独的浏览器窗口中查看输出文本，请选择“下载”。 若要下载文本本身，请右键单击“下载”，并使用浏览器选项来保存文件内容。
   
5. 选择页面顶部的“Web 作业”痕迹导航链接转到 Web 作业列表。

    ![Web 作业痕迹导航](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![历史记录仪表板中的 Web 作业列表](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="NextSteps"></a>后续步骤

可以结合 WebJobs 使用 Azure WebJobs SDK 来简化许多编程任务。 有关详细信息，请参阅[什么是 WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)。
