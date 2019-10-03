---
title: Web 服务的 Excel 加载项
titleSuffix: Azure Machine Learning Studio
description: 如何在 Excel 中直接使用 Azure 机器学习 Web 服务，而无需编写任何代码。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 9e801e0d7a26cd4d6c43118959aee1dec7216b1c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60750162"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-web-services"></a>适用于 Azure 机器学习工作室 Web 服务的 Excel 加载项
Excel 可以直接轻松调用 Web 服务，而无需编写任何代码。

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>在工作簿中使用现有 Web 服务的步骤

1. 打开“[示例 Excel 文件](https://aka.ms/amlexcel-sample-2)”，其中包含 Excel 外接程序和有关 Titanic 上的乘客数据。 
 
    > [!NOTE]
    > 你将看到与该文件相关的 Web 服务列表，并在底部显示“自动预测”复选框。 如果启用自动预测，则每次输入发生更改时，**所有**服务的预测都将更新。 如果未选中该复选框，则必须单击“全部预测”才能刷新。 若要在服务级别启用自动预测，请转到步骤 6。

2. 通过单击来选择 Web 服务，在此示例中为“Titanic 存活者预测器（Excel 外接程序示例）[分数]”。
   
    ![选择 Web 服务](./media/excel-add-in-for-web-services/image1.png)
3. 系统转到“预测”  部分。  此工作簿已包含示例数据，但如果是空白工作簿，可以在 Excel 中选择一个单元格，并单击“**使用示例数据**”。
4. 选择具有标头的数据，并单击输入数据范围图标。  请确保选中“我的数据带有标题”框。
5. 在“输出”  下方，输入你想要输出所在的单元格号，例如此处的 "H1"。
6. 单击“**预测**”。 如果选中“自动预测”复选框，则所选区域（指定为输入的区域）中的任何更改都将触发请求并更新输出单元格，而无需按下预测按钮。
   
    ![预测部分](./media/excel-add-in-for-web-services/image1.png)

部署 Web 服务或使用现有的 Web 服务。 有关部署 Web 服务的详细信息，请参阅[教程 3：部署信用风险模型](tutorial-part3-credit-risk-deploy.md)。

获取 Web 服务的 API 密钥。 执行此操作的位置取决于是否发布了新的机器学习 Web 服务的经典机器学习 Web 服务。

**使用经典 Web 服务** 

1. 在机器学习工作室中，单击左窗格中的“Web 服务”  部分，并选择 Web 服务。
   
    ![工作室选择一个 Web 服务](./media/excel-add-in-for-web-services/image4.png)
2. 复制 Web 服务的 API 密钥。
   
    ![工作室 API 密钥](./media/excel-add-in-for-web-services/image5.png)
3. 在 Web 服务的“仪表板”  选项卡上，单击“请求/响应”  链接。
4. 查找**请求 URI** 部分。  复制并保存 URL。

> [!NOTE]
> 现在可以登录到 [Azure 机器学习 Web 服务](https://services.azureml.net)门户，以获取经典机器学习 Web 服务的 API 密钥。
> 
> 

**使用新的 Web 服务**

1. 在 [Azure 机器学习 Web 服务](https://services.azureml.net)门户中，单击“Web 服务  ”，并选择 Web 服务。 
2. 单击“**使用**”。
3. 查找**基本使用信息**部分。 复制并保存**主密钥**和**请求-响应** URL。

## <a name="steps-to-add-a-new-web-service"></a>添加新 Web 服务的步骤

1. 部署 Web 服务或使用现有的 Web 服务。 有关部署 Web 服务的详细信息，请参阅[教程 3：部署信用风险模型](tutorial-part3-credit-risk-deploy.md)。
2. 单击“**使用**”。
3. 查找**基本使用信息**部分。 复制并保存**主密钥**和**请求-响应** URL。
4. 在 Excel 中，转到“Web 服务”  部分（如果在“预测”  部分中，请单击返回箭头转到 Web 服务列表）。
   
    ![转到 Web 服务选择](./media/excel-add-in-for-web-services/image3.png)
5. 单击“**添加 Web 服务**”。
6. 将 URL 粘贴到标记为 **URL** 的 Excel 外接程序文本框中。
7. 将 API/主密钥粘贴到标记为 **API 密钥**的文本框中。
8. 单击“添加”  。
   
    ![经典 Web 服务的 URL 和 API 密钥。](./media/excel-add-in-for-web-services/image6.png)
9. 若要使用 Web 服务，请按照前面的指导操作：“使用现有 Web 服务的步骤”。

## <a name="sharing-your-workbook"></a>共享工作簿
如果保存工作簿，则会一并保存为 Web 服务添加的 API/主密钥。 这意味着只应与自己信任的人共享该工作簿。

请在以下评论部分中或在我们[论坛](https://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409)上提出任何问题。
