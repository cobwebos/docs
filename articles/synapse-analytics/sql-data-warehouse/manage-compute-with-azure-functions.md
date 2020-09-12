---
title: 教程：管理具有 Azure Functions 的计算
description: 如何使用 Azure 函数在 Azure Synapse Analytics 中管理 SQL 池的计算。
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/27/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 11cb0c30a1a6ed70cca82e494fcec73936975f39
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89442215"
---
# <a name="use-azure-functions-to-manage-compute-resources-in-azure-synapse-analytics-sql-pool"></a>使用 Azure Functions 管理 Azure Synapse Analytics SQL 池中的计算资源

本教程使用 Azure Functions 在 Azure Synapse Analytics 中管理 SQL 池的计算资源。

若要将 Azure Function App 与 SQL 池一起使用，必须创建一个 [服务主体帐户](../../active-directory/develop/howto-create-service-principal-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ，该帐户具有与 sql 池实例相同的订阅中的参与者访问权限。

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板部署基于计时器的缩放

若要部署该模板，需提供以下信息：

- SQL 池实例所在的资源组的名称
- SQL 池实例所在的服务器的名称
- SQL 池实例的名称
- Azure Active Directory 的租户 ID（目录 ID）
- 订阅 ID
- 服务主体应用程序 ID
- 服务主体密钥

有了上述信息以后，即可部署该模板：

[![图像显示标记为“部署到 Azure”的按钮。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json)

部署模板以后，应该可以找到三个新资源：一个免费的 Azure 应用服务计划、一个基于使用情况的 Function App 计划，以及一个将要用于处理日志记录和操作队列的存储帐户。 请继续阅读其他部分，了解如何根据需要修改部署的函数。

## <a name="change-the-compute-level"></a>更改计算级别

1. 导航到 Function App 服务。 如果使用默认值部署了模板，该服务的名称应该为 ** DWOperations。 打开 Function App 以后，会看到五个函数部署到 Function App 服务。

   ![使用模板部署的函数](./media/manage-compute-with-azure-functions/five-functions.png)

2. 选择 ** DWScaleDownTrigger 或 ** DWScaleUpTrigger，具体取决于是否要更改纵向扩展或纵向缩减时间。 在下拉菜单中，选择“集成”。

   ![选择“集成”作为函数](./media/manage-compute-with-azure-functions/select-integrate.png)

3. 目前显示的值应该为 **%ScaleDownTime% 或 **%ScaleUpTime%。 这些值指示计划基于在[应用程序设置](../../azure-functions/functions-how-to-use-azure-function-app-settings.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)中定义的值。 目前可以忽略该值，根据后续步骤将计划更改为首选时间。

4. 在 "计划" 区域中，添加 CRON 表达式的时间，以反映想要将 Azure Synapse Analytics 向上扩展的频率。

   ![更改函数计划](./media/manage-compute-with-azure-functions/change-schedule.png)

   `schedule` 的值是 [CRON 表达式](https://en.wikipedia.org/wiki/Cron#CRON_expression)，其中包含以下六个字段：

   ```json
   {second} {minute} {hour} {day} {month} {day-of-week}
   ```

   例如， *"0 30 9 * * 1-5"* 将在每个工作日的9：上午9:30 反映一个触发器。 有关详细信息，请访问 Azure Functions [计划示例](../../azure-functions/functions-bindings-timer.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#example)。

## <a name="change-the-time-of-the-scale-operation"></a>更改缩放操作的时间

1. 导航到 Function App 服务。 如果使用默认值部署了模板，该服务的名称应该为 ** DWOperations。 打开 Function App 以后，会看到五个函数部署到 Function App 服务。

2. 选择 ** DWScaleDownTrigger 或 ** DWScaleUpTrigger，具体取决于是否要更改纵向扩展或纵向缩减计算值。 选择函数以后，窗格会显示 ** index.js 文件。

   ![更改函数触发器计算级别](././media/manage-compute-with-azure-functions/index-js.png)

3. 将 ** ServiceLevelObjective 的值更改为所需级别，然后点击“保存”。 此值是根据“集成”部分定义的计划，数据仓库实例应缩放到的计算级别。

## <a name="use-pause-or-resume-instead-of-scale"></a>使用暂停或继续而非缩放

目前，默认启用的函数为 ** DWScaleDownTrigger 和 ** DWScaleUpTrigger。 若要改用暂停和继续功能，可以启用 ** DWPauseTrigger 或 ** DWResumeTrigger。

1. 导航到“函数”窗格。

   ![“函数”窗格](./media/manage-compute-with-azure-functions/functions-pane.png)

2. 单击要启用的触发器所对应的滑动切换开关。

3. 导航到用于更改计划的各个触发器的“集成”选项卡。**

   > [!NOTE]
   > 缩放触发器和暂停/继续触发器之间的功能差异在于发送给队列的消息。 有关详细信息，请参阅[添加新的触发器函数](manage-compute-with-azure-functions.md#add-a-new-trigger-function)。

## <a name="add-a-new-trigger-function"></a>添加新的触发器函数

目前，模板中只包括两个缩放函数。 使用这些函数，在一天中只能纵向缩减和纵向扩展各一次。 若要进行更精细的控制，例如每天纵向缩减多次，或者在周末表现出不同的缩放行为，则需添加另一触发器。

1. 创建新的空白函数。 选择 *+* 函数位置附近的按钮以显示函数模板窗格。

   ![创建新的函数](./media/manage-compute-with-azure-functions/create-new-function.png)

2. 在 "语言" 中，选择 " *JavaScript*"，然后选择 " *TimerTrigger*"。

   ![创建新的函数](./media/manage-compute-with-azure-functions/timertrigger-js.png)

3. 为函数命名，然后设置计划。 下图显示用户如何在每周六凌晨（周五深夜）触发其函数。

   ![周六纵向缩减](./media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. 从其他触发器函数中的一个复制 ** index.js 的内容。

   ![复制 index js](././media/manage-compute-with-azure-functions/index-js.png)

5. 将操作变量设置为所需行为，如下所示：

   ```JavaScript
   // Resume the SQL pool instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the SQL pool instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the SQL pool instance to DW600
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600"
   }
   ```

## <a name="complex-scheduling"></a>复杂的计划

本部分将简要介绍如何才能对暂停、继续和缩放功能进行更复杂的计划。

### <a name="example-1"></a>示例 1

每日早晨 8 点纵向扩展到 DW600，晚上 8 点纵向缩减到 DW200。

| 函数  | 计划     | 操作                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW600"}` |
| Function2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-2"></a>示例 2

每日早晨 8 点纵向扩展到 DW1000，下午 4 点纵向缩减到 DW600 一次，晚上 10 点纵向缩减到 DW200。

| 函数  | 计划     | 操作                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Function2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Function3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-3"></a>示例 3

在工作日的早晨 8 点纵向扩展到 DW1000，下午 4 点纵向缩减到 DW600 一次。 周五晚上 11 点暂停，周一早晨 7 点继续。

| 函数  | 计划       | 操作                                |
| :-------- | :------------- | :--------------------------------------- |
| Function1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Function2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Function3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| Function4 | 0 0 7 * * 0    | `var operation = {"operationType": "ResumeDw"}` |

## <a name="next-steps"></a>后续步骤

详细了解 Azure Functions 的[计时器触发器](../../azure-functions/functions-create-scheduled-function.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。

签出 SQL 池 [示例存储库](https://github.com/Microsoft/sql-data-warehouse-samples)。
