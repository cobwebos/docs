---
title: "机器学习 Web 服务的 Excel 外接程序 | Microsoft Docs"
description: "如何在 Excel 中直接使用 Azure 机器学习 Web 服务，而无需编写任何代码。"
services: machine-learning
documentationcenter: 
author: tedway
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 9618079d-502f-4974-a3e2-8f924042a23f
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/14/2017
ms.author: tedway;garye
translationtype: Human Translation
ms.sourcegitcommit: 247d370c1f80729856e53690045991127ad54351
ms.openlocfilehash: 814decc2aace0379332af229d232eef1a26939de
ms.lasthandoff: 03/02/2017


---
# <a name="excel-add-in-for-azure-machine-learning-web-services"></a>适用于 Azure 机器学习 Web 服务的 Excel 外接程序
Excel 可以直接轻松调用 Web 服务，而无需编写任何代码。

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>在工作簿中使用现有 Web 服务的步骤

1. 打开“[示例 Excel 文件](http://aka.ms/amlexcel-sample-2)”，其中包含 Excel 外接程序和有关 Titanic 上的乘客数据。
2. 通过单击来选择 Web 服务，在此示例中为“Titanic 存活者预测器（Excel 外接程序示例）[分数]”。
   
    ![选择 Web 服务][01]
3. 系统转到“预测”部分。  此工作簿已包含示例数据，但如果是空白工作簿，可以在 Excel 中选择一个单元格，并单击“**使用示例数据**”。
4. 选择具有标头的数据，然后单击输入数据范围图标。  请确保选中“我的数据带有标题”框。
5. 在“**输出**”下方，输入你想要输出所在的单元格号，例如此处的 "H1"。
6. 单击“**预测**”。
   
    ![预测部分][02]

部署 Web 服务或使用现有的 Web 服务。 有关部署 Web 服务的详细信息，请参阅[演练步骤 5：部署 Azure 机器学习 Web 服务](machine-learning-walkthrough-5-publish-web-service.md)。

获取 Web 服务的 API 密钥。 执行此操作的位置取决于是否发布了新的机器学习 Web 服务的经典机器学习 Web 服务。

**使用经典 Web 服务** 

1. 在机器学习工作室中，单击左窗格中的“Web 服务”部分，然后选择 Web 服务。
   
    ![工作室选择一个 Web 服务][04]
2. 复制 Web 服务的 API 密钥。
   
    ![工作室 API 密钥][05]
3. 在 Web 服务的“仪表板”选项卡上，单击“请求/响应”链接。
4. 查找**请求 URI** 部分。  复制并保存 URL。

> [!NOTE]
> 现在可以登录到 [Azure 机器学习 Web 服务](https://services.azureml.net)门户，以获取经典机器学习 Web 服务的 API 密钥。
> 
> 

**使用新的 Web 服务**

1. 在 [Azure 机器学习 Web 服务](https://services.azureml.net)门户中，单击“Web 服务”，然后选择 Web 服务。 
2. 单击“**使用**”。
3. 查找**基本使用信息**部分。 复制并保存**主密钥**和**请求-响应** URL。

## <a name="steps-to-add-a-new-web-service"></a>添加新 Web 服务的步骤

1. 部署 Web 服务或使用现有的 Web 服务。 有关部署 Web 服务的详细信息，请参阅[演练步骤 5：部署 Azure 机器学习 Web 服务](machine-learning-walkthrough-5-publish-web-service.md)。
2. 单击“**使用**”。
3. 查找**基本使用信息**部分。 复制并保存**主密钥**和**请求-响应** URL。
4. 在 Excel 中，转到“Web 服务”部分（如果在“预测”部分中，请单击返回箭头转到 Web 服务列表）。
   
    ![转到 Web 服务选择][03]
5. 单击“**添加 Web 服务**”。
6. 将 URL 粘贴到标记为 **URL** 的 Excel 外接程序文本框中。
7. 将 API/主密钥粘贴到标记为 **API 密钥**的文本框中。
8. 单击 **“添加”**。
   
    ![经典 Web 服务的 URL 和 API 密钥。][06]
9. 若要使用 Web 服务，请按照前面的指导操作：“使用现有 Web 服务的步骤”。

## <a name="sharing-your-workbook"></a>共享工作簿
如果保存工作簿，则会一并保存为 Web 服务添加的 API/主密钥。 这意味着你只应与自己信任的人共享该工作簿。

请在以下评论部分中或在我们[论坛](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409)上提出任何问题。

[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png
[05]: ./media/machine-learning-excel-add-in-for-web-services/image5.png
[06]: ./media/machine-learning-excel-add-in-for-web-services/image6.png

