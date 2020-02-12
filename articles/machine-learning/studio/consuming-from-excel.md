---
title: 在 Excel 中使用 Web 服务
titleSuffix: ML Studio (classic) - Azure
description: 通过 Azure 机器学习 Studio （经典），无需编写任何代码，就可以轻松地直接从 Excel 调用 web 服务。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 2e95c4bfbe7342e251e6d845fd4acfed6ff6109a
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77150096"
---
# <a name="consuming-an-azure-machine-learning-studio-classic-web-service-from-excel"></a>使用 Excel 中的 Azure 机器学习 Studio （经典） Web 服务

 通过 Azure 机器学习 Studio （经典），无需编写任何代码，就可以轻松地直接从 Excel 调用 web 服务。

如果使用的 Excel 2013 （或更高版本）或 Excel Online，则建议使用 Excel 的 [Excel 加载项](excel-add-in-for-web-services.md)。



## <a name="steps"></a>步骤
发布 Web 服务。 [教程3：部署信用风险模型](tutorial-part3-credit-risk-deploy.md)说明了如何执行此操作。 目前，仅具有单个输出（即单个评分标签）的请求/响应服务支持 Excel 工作簿功能。 

获得 Web 服务后，点击工作室左侧的 **WEB SERVICES** 部分，并从Excel 中选择要使用的 Web 服务。

**经典 Web 服务**

1. 在 Web 服务的“DASHBOARD”选项卡上有一行“REQUEST/RESPONSE”服务。 如果此服务具有单个输出，应在该行看到“下载 Excel 工作簿”链接。

    ![使用 Studio （经典） Web 服务门户下载 Excel 工作簿](./media/consuming-from-excel/excellink.png)
2. 单击“下载 Excel 工作簿”。

**新的 Web 服务**

1. 在 Azure 机器学习 Web 服务门户中，选择“使用”。
2. 在使用页上的“Web 服务使用选项”部分中，单击 Excel 图标。

**使用工作簿**

1. 打开工作簿。
2. 系统会显示安全警告；单击“启用编辑”按钮。

    ![启用编辑以删除受保护的视图安全警告](./media/consuming-from-excel/enableeditting.png)
3. 系统会显示安全警告。 单击“启用内容”按钮，在电子表格上运行宏。

    ![启用内容以消除禁用宏的安全警告](./media/consuming-from-excel/enablecontent.png)
4. 启用宏后，将生成一个表。 要求将蓝色的列作为 RRS Web 服务的输入或 **PARAMETERS**。 注意，RRS 服务的输出，**PREDICTED VALUES** 为绿色。 给定行的所有列都填满时，工作簿会自动调用评分 API，并显示评分结果。

    ![参数输入的表和生成的预测值](./media/consuming-from-excel/sampletable.png)
5. 要对多行进行评分，请使用数据填充第二行，并生成预测值。 甚至可以一次粘贴多行。

可以使用任何具有预测值的 Excel 功能（Graph、Power Map、条件格式等），帮助可视化数据。

## <a name="sharing-your-workbook"></a>共享工作簿
若要能够使用宏，API 密钥必须是电子表格的一部分。 这意味着应只与信任的实体/个人共享工作簿。

## <a name="automatic-updates"></a>自动更新
在这两种情况进行 RRS 调用：

1. 第一次某行在其所有 **PARAMETERS** 中都有内容时
2. 任何时候，输入了其所有 **PARAMETERS** 的行中的任一 **PARAMETERS** 发生更改时。