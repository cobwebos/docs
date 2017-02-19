---
title: "使用 Excel 中的机器学习 Web 服务 | Microsoft Docs"
description: "使用 Excel 中的 Azure 机器学习 Web 服务"
services: machine-learning
documentationcenter: 
author: tedway
manager: jhubbard
editor: cgronlun
ms.assetid: 3f3cdd2f-1816-487e-ab78-530e01e9788f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/13/2017
ms.author: tedway
translationtype: Human Translation
ms.sourcegitcommit: 156e2f01e6a010ddab06dbb8e4746dceb54a413a
ms.openlocfilehash: 9f1aac04d54221888ee9374317be339400dcf085


---
# <a name="consuming-an-azure-machine-learning-web-service-from-excel"></a>使用 Excel 中的 Azure 机器学习 Web 服务
 Azure 机器学习工作室可以轻松地直接从 Excel 调用 Web 服务，而无需编写任何代码。

如果使用的 Excel 2013 （或更高版本）或 Excel Online，则建议使用 Excel 的 [Excel 加载项](machine-learning-excel-add-in-for-web-services.md)。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="steps"></a>步骤
发布 Web 服务。 [此页](machine-learning-walkthrough-5-publish-web-service.md)解释如何执行此操作。 目前，仅具有单个输出（即单个评分标签）的请求/响应服务支持 Excel 工作簿功能。 

获得 Web 服务后，点击工作室左侧的 **WEB SERVICES** 部分，然后从Excel 中选择要使用的 Web 服务。

**经典 Web 服务**

1. 在 Web 服务的“DASHBOARD”选项卡上有一行“REQUEST/RESPONSE”服务。 如果此服务具有单个输出，应在该行看到“下载 Excel 工作簿”链接。
   
    ![][1]
2. 单击“下载 Excel 工作簿”。

**新的 Web 服务**

1. 在 Azure 机器学习 Web 服务门户中，选择“使用”。
2. 在使用页上的“Web 服务使用选项”部分中，单击 Excel 图标。

**使用工作簿**

1. 打开工作簿。
2. 系统将显示安全警告；单击“启用编辑”按钮。
   
    ![][2]
3. 系统将显示安全警告。 单击“启用内容”按钮，在电子表格上运行宏。
   
    ![][3]
4. 启用宏后，将生成一个表。 要求将蓝色的列作为 RRS Web 服务的输入或 **PARAMETERS**。 注意，RRS 服务的输出，**PREDICTED VALUES** 为绿色。 给定行的所有列都填满时，工作簿会自动调用评分 API，并显示评分结果。
   
    ![][4]
5. 要对多行进行评分，请使用数据填充第二行，并生成预测值。 甚至可以一次粘贴多行。

可以使用任何具有预测值的 Excel 功能（Graph、Power Map、条件格式等），帮助可视化数据。    

## <a name="sharing-your-workbook"></a>共享工作簿
若要能够使用宏，API 密钥必须是电子表格的一部分。 这意味着应只与信任的实体/个人共享工作簿。

## <a name="automatic-updates"></a>自动更新
在这两种情况进行 RRS 调用：

1. 第一次某行在其所有 **PARAMETERS** 中都有内容时
2. 任何时候，输入了其所有 **PARAMETERS** 的行中的任一 **PARAMETERS** 发生更改时。

[1]: ./media/machine-learning-consuming-from-excel/excellink.png
[2]: ./media/machine-learning-consuming-from-excel/enableeditting.png
[3]: ./media/machine-learning-consuming-from-excel/enablecontent.png
[4]: ./media/machine-learning-consuming-from-excel/sampletable.png



<!--HONumber=Feb17_HO2-->


