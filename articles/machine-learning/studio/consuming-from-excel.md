---
title: 在 Excel 中使用 Web 服务
titleSuffix: Azure Machine Learning Studio
description: Azure 机器学习工作室可以轻松地直接从 Excel 调用 Web 服务，而无需编写任何代码。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: ef1d8f1a72c5936ff661636c4c51acf439a0a5ea
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57855191"
---
# <a name="consuming-an-azure-machine-learning-studio-web-service-from-excel"></a>从 Excel 使用 Azure 机器学习工作室 Web 服务

 Azure 机器学习工作室可以轻松地直接从 Excel 调用 Web 服务，而无需编写任何代码。

如果使用的 Excel 2013 （或更高版本）或 Excel Online，则建议使用 Excel 的 [Excel 加载项](excel-add-in-for-web-services.md)。



## <a name="steps"></a>Steps
发布 Web 服务。 [教程 3：部署信用风险模型](tutorial-part3-credit-risk-deploy.md)说明如何执行此操作。 目前，仅具有单个输出（即单个评分标签）的请求/响应服务支持 Excel 工作簿功能。 

获得 Web 服务后，点击工作室左侧的 **WEB SERVICES** 部分，并从Excel 中选择要使用的 Web 服务。

**经典 Web 服务**

1. 在 Web 服务的“DASHBOARD”选项卡上有一行“REQUEST/RESPONSE”服务。 如果此服务具有单个输出，应在该行看到“下载 Excel 工作簿”链接。

    ![下载 Excel 工作簿使用工作室 Web 服务门户](./media/consuming-from-excel/excellink.png)
2. 单击“下载 Excel 工作簿”。

**新的 Web 服务**

1. 在 Azure 机器学习 Web 服务门户中，选择“使用”。
2. 在使用页上的“Web 服务使用选项”部分中，单击 Excel 图标。

**使用工作簿**

1. 打开工作簿。
2. 系统会显示安全警告；单击“启用编辑”按钮。

    ![启用编辑若要删除受保护的视图安全警告](./media/consuming-from-excel/enableeditting.png)
3. 系统会显示安全警告。 单击“启用内容”按钮，在电子表格上运行宏。

    ![使内容能够忽略禁用宏的安全警告](./media/consuming-from-excel/enablecontent.png)
4. 启用宏后，将生成一个表。 要求将蓝色的列作为 RRS Web 服务的输入或 **PARAMETERS**。 注意，RRS 服务的输出，**PREDICTED VALUES** 为绿色。 给定行的所有列都填满时，工作簿会自动调用评分 API，并显示评分结果。

    ![表参数输入和生成的预测值](./media/consuming-from-excel/sampletable.png)
5. 要对多行进行评分，请使用数据填充第二行，并生成预测值。 甚至可以一次粘贴多行。

可以使用任何具有预测值的 Excel 功能（Graph、Power Map、条件格式等），帮助可视化数据。

## <a name="sharing-your-workbook"></a>共享工作簿
若要能够使用宏，API 密钥必须是电子表格的一部分。 这意味着应只与信任的实体/个人共享工作簿。

## <a name="automatic-updates"></a>自动更新
在这两种情况进行 RRS 调用：

1. 第一次某行在其所有 **PARAMETERS** 中都有内容时
2. 任何时候，输入了其所有 **PARAMETERS** 的行中的任一 **PARAMETERS** 发生更改时。