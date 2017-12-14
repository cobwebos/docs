---
title: "Azure SQL 数据仓库 - 使用 Azure Functions 自动调整 SQL 数据仓库计算级别 | Microsoft Docs"
description: "如何使用 Azure Functions 管理数据仓库的计算。"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: barbkess
ms.assetid: 52DFC191-E094-4B04-893F-B64D5828A901
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: quickstart
ms.date: 11/06/2017
ms.author: elbutter
ms.openlocfilehash: 8947da9d34261be46ad9aea961b6020141484172
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="use-azure-functions-to-automate-sql-dw-compute-levels"></a>使用 Azure Functions 自动调整 SQL DW 计算级别

本教程将演示如何使用 Azure Functions 管理 Azure SQL 数据仓库的计算级别。 建议将以下体系结构与[进行弹性优化][Performance Tiers]的 SQL 数据仓库配合使用。

若要将 Azure Function App 与 SQL 数据仓库配合使用，必须在数据仓库实例所在的订阅下创建具有参与者访问权限的[服务主体帐户](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)。 

## <a name="deploy-timer-based-scaler-with-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板部署基于计时器的 ScaleR

若要部署该模板，需提供以下信息：

- SQL DW 实例所在资源组的名称
- SQL DW 实例所在逻辑服务器的名称
- SQL DW 实例的名称
- Azure Active Directory 的租户 ID（目录 ID）
- 订阅 ID 
- 服务主体应用程序 ID
- 服务主体密钥

有了上述信息以后，即可部署该模板：

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

部署模板以后，应该可以找到三个新资源：一个免费的 Azure 应用服务计划、一个基于使用情况的 Function App 计划，以及一个将要用于处理日志记录和操作队列的存储帐户。 请继续阅读其他部分，了解如何根据需要修改部署的函数。

### <a name="change-the-scale-up-or-scale-down-compute-level"></a>更改纵向扩展或纵向缩减计算级别

1. 导航到 Function App 服务。 如果使用默认值部署了模板，该服务的名称应该为 DWOperations。 打开 Function App 以后，会看到五个函数部署到 Function App 服务。 

   ![使用模板部署的函数](media/manage-compute-with-azure-functions/five-functions.png)

2. 选择 DWScaleDownTrigger 或 DWScaleUpTrigger，具体取决于是否要更改纵向扩展或纵向缩减时间。 在下拉列表中，选择“集成”。

   ![选择“集成”作为函数](media/manage-compute-with-azure-functions/select-integrate.png)

3. 目前显示的值应该为 %ScaleDownTime% 或 %ScaleUpTime%。 这些值指示计划基于在[应用程序设置][Application Settings]中定义的值。 目前可以忽略这些，根据后续步骤将计划更改为首选时间。

4. 在计划区域向 CRON 表达式添加所需时间，此时间反映了你所希望的对 SQL 数据仓库进行纵向扩展的频率。 

  ![更改函数计划](media/manage-compute-with-azure-functions/change-schedule.png)

  `schedule` 的值是 [CRON 表达式](http://en.wikipedia.org/wiki/Cron#CRON_expression)，其中包含以下六个字段： 
  ```json
  {second} {minute} {hour} {day} {month} {day-of-week}
  ```

  例如，“0 30 9 * * 1-5”表示在每个工作日的上午 9:30 触发一次。 有关详细信息，请访问 Azure Functions [计划示例][schedule examples]。


### <a name="change-the-scale-up-or-scale-down-time"></a>更改纵向扩展或纵向缩减时间

1. 导航到 Function App 服务。 如果使用默认值部署了模板，该服务的名称应该为 DWOperations。 打开 Function App 以后，会看到五个函数部署到 Function App 服务。 

2. 选择 DWScaleDownTrigger 或 DWScaleUpTrigger，具体取决于是否要更改纵向扩展或纵向缩减计算值。 选择函数以后，窗格会显示 index.js 文件。

   ![更改函数触发器计算级别](media/manage-compute-with-azure-functions/index-js.png)

3. 将 ServiceLevelObjective 的值更改为所需级别，然后点击“保存”。 这是根据“集成”部分定义的计划，数据仓库实例应扩展到的计算级别。

### <a name="use-pause-or-resume-instead-of-scale"></a>使用暂停或继续而非缩放 

目前，默认启用的函数为 DWScaleDownTrigger 和 DWScaleUpTrigger。 若要改用暂停和继续功能，可以启用 DWPauseTrigger 或 DWResumeTrigger。

1. 导航到“函数”窗格。

   ![“函数”窗格](media/manage-compute-with-azure-functions/functions-pane.png)



2. 单击要启用的触发器所对应的滑动切换开关。

3. 导航到用于更改计划的各个触发器的“集成”选项卡。

   [!NOTE]: The functional difference between the scaling triggers and the pause/resume triggers is the message that is sent to the queue. See [Add a new trigger function][Add a new trigger function] for more information.



### <a name="add-a-new-trigger-function"></a>添加新的触发器函数

目前，模板中只包括两个缩放函数。 这意味着，在一天中只能纵向缩减和纵向扩展各一次。 若要进行更精细的控制，例如每天纵向缩减多次，或者在周末表现出不同的缩放行为，则需添加另一触发器。

1. 创建新的空白函数。 选择靠近“函数”位置的 + 按钮，显示函数模板窗格。

   ![创建新的函数](media/manage-compute-with-azure-functions/create-new-function.png)

2. 从“语言”中选择“Javascript”，然后选择“TimerTrigger”。

   ![创建新的函数](media/manage-compute-with-azure-functions/timertrigger-js.png)

3. 为函数命名，然后设置计划。 下图显示用户如何在每周六凌晨（周五深夜）触发其函数。

   ![周六纵向缩减](media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. 从其他触发器函数中的一个复制 index.js 的内容。

   ![复制 index js](media/manage-compute-with-azure-functions/index-js.png)

5. 将操作变量设置为所需行为，如下所示：

   ```javascript
   // Resume the data warehouse instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the data warehouse instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the data warehouse instance to DW600
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600"
   }
   ```


### <a name="complex-scheduling"></a>复杂的计划

本部分将简要介绍如何才能对暂停、继续和缩放功能进行更复杂的计划。

#### <a name="example-1"></a>示例 1：

每日早晨 8 点纵向扩展到 DW600，晚上 8 点纵向缩减到 DW200。

| 函数  | 计划     | 操作                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW600"}` |
| Function2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

#### <a name="example-2"></a>示例 2： 

每日早晨 8 点纵向扩展到 DW1000，下午 4 点纵向缩减到 DW600 一次，晚上 10 点纵向缩减到 DW200。

| 函数  | 计划     | 操作                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW1000"}` |
| Function2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Function3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

#### <a name="example-3"></a>示例 3： 

在工作日的早晨 8 点纵向扩展到 DW1000，下午 4 点纵向缩减到 DW600 一次。 周五晚上 11 点暂停，周一早晨 7 点继续。

| 函数  | 计划       | 操作                                |
| :-------- | :------------- | :--------------------------------------- |
| Function1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Function2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Function3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| Function4 | 0 0 7 * * 0    | `var operation = {"operationType": "ResumeDw"}` |



## <a name="next-steps"></a>后续步骤

详细了解 Azure Functions 的[计时器触发器](../azure-functions/functions-create-scheduled-function.md)。

查看 SQL 数据仓库[示例存储库](https://github.com/Microsoft/sql-data-warehouse-samples)。



[schedule examples]: ../azure-functions/functions-bindings-timer.md#example

[Application Settings]: ../azure-functions/functions-how-to-use-azure-function-app-settings.md
[Add a new trigger function]: manage-compute-with-azure-functions.md#add-a-new-trigger-function

[Performance Tiers]: performance-tiers.md
