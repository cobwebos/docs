---
title: 教程：创建用于图像分类的标记项目
titleSuffix: Azure Machine Learning
description: 了解如何管理图像标记过程，使图像可在多类图像分类模型中使用。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sgilley
author: sdgilley
ms.reviewer: ranku
ms.date: 04/09/2020
ms.openlocfilehash: f880f189b19b4909268b6e0b24654fc4682ab7c8
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759177"
---
# <a name="tutorial-create-a-labeling-project-preview-for-multi-class-image-classification"></a>教程：创建用于多类图像分类的标记项目（预览） 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本教程介绍如何管理在构建机器学习模型时用作数据的图像的标记过程。 Azure 机器学习中的数据标记功能目前为公共预览版。

若要训练某个机器学习模型来对图像进行分类，需要数百甚至数千个正确标记的图像。  Azure 机器学习可帮助你管理领域专家私人团队在标记你的数据时的进度。
 
本教程将使用猫和狗的图像。  由于每张图像要么是猫，要么是狗，因此这属于一个多类标记项目。 将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 创建一个 Azure 存储帐户并将图像上传到该帐户。
> * 创建 Azure 机器学习工作区。
> * 创建一个多类图像标记项目。
> * 标记你的数据。  可由你或者标记人员执行此任务。
> * 检查并导出数据，以此完成项目。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://aka.ms/AMLFree)。

## <a name="create-a-workspace"></a>创建工作区

Azure 机器学习工作区是云中的基础资源，用于试验、训练和部署机器学习模型。 它将 Azure 订阅和资源组关联到服务中一个易于使用的对象。

通过 Azure 门户创建工作区，该门户是用于管理 Azure 资源的基于 Web 的控制台。

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="start-a-labeling-project"></a>启动标记项目

接下来，在 Azure 机器学习工作室中管理数据标记项目。机器学习工作室是一个整合的界面，其中包含的机器学习工具可供各种技能水平的数据科学实践者用来执行数据科学方案。 Internet Explorer 浏览器不支持此工作室。

1. 登录到 [Azure 机器学习工作室](https://ml.azure.com)。

1. 选择创建的订阅和工作区。

### <a name="create-a-datastore"></a><a name="create-datastore"></a>创建数据存储

Azure 机器学习数据存储用于存储连接信息，例如订阅 ID 和令牌授权。 在此处，你将使用数据存储连接到包含本教程所用图像的存储帐户。

1. 在工作区的左侧，选择“数据存储”。

1. 选择“+ 新建数据存储”。

1. 在窗体中填写以下设置：

    字段|说明 
    ---|---
    数据存储名称 | 为数据存储提供一个名称。  此处我们使用 labeling_tutorial。
    数据存储类型 | 选择存储的类型。  此处我们使用“Azure Blob 存储”，这是适用于图像的首选存储。
    帐户选择方法 | 选择“手动输入”。
    代码 | `https://azureopendatastorage.blob.core.windows.net/openimagescontainer`
    身份验证类型 | 选择“SAS 令牌”。
    帐户密钥 | `?sv=2019-02-02&ss=bfqt&srt=sco&sp=rl&se=2025-03-25T04:51:17Z&st=2020-03-24T20:51:17Z&spr=https&sig=7D7SdkQidGT6pURQ9R4SUzWGxZ%2BHlNPCstoSRRVg8OY%3D`

1. 选择“创建”以创建数据存储。

### <a name="create-a-labeling-project"></a>创建标记项目

现在，你有权访问所要标记的数据，接下来请创建标记项目。

1. 在页面顶部选择“项目”。

1. 选择“+ 添加项目”。

    ![创建一个项目](media/tutorial-labeling/create-project.png)

### <a name="project-details"></a>项目详细信息

1. 在“项目详细信息”窗体中使用以下输入：

    字段|说明 
    ---|---
    项目名称 | 为你的项目命名。  此处我们使用 tutorial-cats-n-dogs。
    标记任务类型 | 选择“多类图像分类”。  
    
    选择“下一步”继续创建项目。

### <a name="select-or-create-a-dataset"></a>选择或创建数据集

1.   在“选择或创建数据集”窗体中，选择第二个选项“创建数据集”，然后选择“从数据存储”链接。  

1. 在“从数据存储创建数据集”窗体中使用以下输入：

    1. 在“基本信息”窗体中添加一个名称，此处我们使用 images-for-tutorial。   添加说明（如果需要）。  然后，选择“下一步”。
    1. 在“选择数据存储”窗体中，使用下拉列表选择以前创建的数据存储，例如“tutorial_images (Azure Blob 存储)”  
    1. 接下来，仍在“选择数据存储”窗体中，依次选择“浏览”、“MultiClass - DogsCats”。    选择“保存”并使用“/MultiClass - DogsCats”作为路径。 
    1. 选择“下一步”来确认详细信息，然后选择“创建”以创建数据集。 
    1. 在列表中选择数据集名称（例如“images-for-tutorial”）旁边的圆圈。

1. 选择“下一步”继续创建项目。

### <a name="label-classes"></a>标签类

1. 在“标签类”窗体中键入标签名称，然后选择“+ 添加标签”以键入下一个标签。   对于本项目，标签为“猫”、“狗”和“不确定”。  

1. 添加所有标签后，选择“下一步”。

### <a name="labeling-instructions"></a>标记说明

1. 在“标记说明”窗体中，可以输入一个为标记人员提供详细说明的网站的链接。  对于本教程，我们将此窗体留空。

1. 还可以直接在窗体中添加任务的简短说明。  键入“标记教程 - 猫和狗”。

1. 选择“**下一页**”。

1. 在“ML 辅助标记”窗体中，将复选框保留未选中状态。 ML 辅助标记所需的数据比在本教程中使用的数据更多。

1. 选择“创建项目”。

此页面不会自动刷新。 片刻之后，请手动刷新页面，直到项目状态更改为“已创建”。

## <a name="start-labeling"></a>开始标记

你现在已设置了 Azure 资源，并已配置了数据标记项目。 接下来需要向数据添加标签。

### <a name="tag-the-images"></a>标记图像

在本教程部分，你要将角色从“项目管理员”切换为标记人员的角色。   任何对你的工作区具有参与者访问权限的人都可以成为标记人员。

1. 在[机器学习工作室](https://ml.azure.com)中，选择左侧的“数据标记”来找到你的项目。  

1. 选择列表中的项目名称。

1. 在项目名称下方，选择“标签数据”。

1. 阅读说明，然后选择“任务”。

1. 选择右侧的缩略图以显示要一次性标记的图像数。 必须标记所有这些图像才能继续操作。 仅当有包含未标记数据的新页面时，才可以切换布局。 切换布局会清除页面的正在进行的标记工作。

1. 选择一个或多个图像，然后选择要应用到所选图像的标记。 标记将显示在图像的下方。  在页面上继续选择并标记所有图像。  若要同时选择所有显示的图像，请选择“全选”。 请至少选择一个要应用标记的图像。


    > [!TIP]
    > 可以使用键盘上的数字键选择前九个标记。

1. 标记页面中的所有图像后，选择“提交”以提交这些标签。

    ![标记图像](media/tutorial-labeling/catsndogs.gif)

1. 提交手头数据的标记后，Azure 将使用工作队列中的一组新图像刷新页面。

## <a name="complete-the-project"></a>完成项目

现在，请将角色切换回到标记项目的“项目管理员”。

作为管理员，你可能想要审查标记人员的工作。  

### <a name="review-labeled-data"></a>审查标记的数据

1. 在[机器学习工作室](https://ml.azure.com)中，选择左侧的“数据标记”来找到你的项目。  

1. 选择项目名称链接。

1. 仪表板将显示项目的进度。

1. 在页面顶部选择“数据”。

1. 在左侧选择“标记的数据”以查看标记的图像。  

1. 如果你不同意使用某个标签，请选择相应的图像，然后在页面底部选择“拒绝”。  标记随即被删除，而该图像会放回到未标记图像的队列中。

### <a name="export-labeled-data"></a>导出标记的数据

随时可以导出标签数据以进行机器学习试验。 用户经常需要导出数据多次并训练不同的模型，而不是等待所有图像标记完成。

可以使用 [COCO 格式](http://cocodataset.org/#format-data)导出图像标签，或将其导出为 Azure 机器学习数据集。 数据集采用的格式使得数据集可以方便地在 Azure 机器学习中用于训练。  

1. 在[机器学习工作室](https://ml.azure.com)中，选择左侧的“数据标记”来找到你的项目。  

1. 选择项目名称链接。

1. 依次选择“导出”、“作为 Azure ML 数据集导出”。  

    “导出”按钮的正下方会显示导出状态。 

1. 成功导出标签后，选择左侧的“数据集”来查看结果。

## <a name="clean-up-resources"></a>清理资源


[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你已标记了图像。  现在，请使用标记的数据来执行以下操作：

> [!div class="nextstepaction"]
> [训练机器学习图像识别模型](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb)。
