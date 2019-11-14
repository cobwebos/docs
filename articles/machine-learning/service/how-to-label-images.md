---
title: 如何使用 Azure 机器学习的数据标签工具
title.suffix: Azure Machine Learning
description: 本文介绍如何在 Azure 机器学习标签项目中使用数据标记工具。
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: af12361ed11d0a16e5a5d0cfe5989bb3918ce154
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585583"
---
# <a name="how-to-tag-images-in-a-labeling-project"></a>如何在标签项目中标记图像

一旦项目管理员在 Azure 机器学习工作室中创建了标签项目，你就可以使用标签工具为机器学习项目快速准备数据。 

> [!div class="checklist"]
> * 如何访问标签项目
> * 标签工具
> * 如何使用工具执行特定的标签任务

## <a name="prerequisites"></a>先决条件

* 正在运行的数据标签项目的标签门户 URL
* [Microsoft 帐户](https://account.microsoft.com/account)或
* 组织和项目的 Azure Active Directory 帐户

> [!Note]
> 项目管理员可以在“项目详细信息”  页的“详细信息”  选项卡上找到标签门户 URL。 

## <a name="logging-in-to-the-projects-labeling-portal"></a>登录到项目的标签门户

转到由项目管理员提供给你的标签门户 URL。 

使用管理员用于将你添加到团队的电子邮件帐户登录。 对于大多数用户来说，登录将通过 Microsoft 帐户完成。 如果标签项目使用 Azure Active Directory，则这就是登录的方式。 

## <a name="understanding-the-labeling-task"></a>了解标签任务

登录后，将进入项目的概述页。 

要做的第一个操作是查看详细说明  。 这些说明特定于你的项目，并将介绍你所面临的数据类型、你应该如何做出决策以及其他相关信息。 完成后，返回到项目页，然后选择“开始标签”  。

## <a name="common-features-of-the-labeling-task"></a>标签任务的常用功能

所有图像标签任务都涉及从项目管理员指定的集合中选择合适的标记。 可以通过使用键盘上的数字键在前九个标记之间进行选择。  

图像分类任务允许选择同时显示多个图像。 可以使用图像区域上方的图标选择不同的布局。 可以通过按“全部选择”  按钮同时选择所有显示的图像。 使用图像区域右上角的“循环选择”按钮选择单个照片。 必须至少选择一个图像才能应用标记。 如果选择了多个图像，则选择标记会将该标记应用于每个选定的照片。

在这里，我们选择了 2x2 布局，并将“哺乳动物”标记应用于熊和虎鲸的图像。 鲨鱼图像已被标记为“软骨鱼”，尚未对鬣蜥进行标记。

![多个图像布局和选择](media/how-to-label-images/layouts.png)

> [!Important] 
> 仅当有包含未标记数据的新页面时，才切换布局。 切换布局会清除页面的正在进行的标记工作。 

当你在页面上标记所有图像时，Azure 会启用“提交”  按钮。 按“提交”  以保存工作。 

提交手头数据的标记后，Azure 将使用工作队列中的一组新图像刷新页面。  

## <a name="tagging-images-for-multi-class-classification"></a>标记图像以进行多类分类

如果项目的类型为“图像分类多类”，则会将单一标记分配给整个图像。 在任何时候，选择“说明”  页，然后导航到“查看详细说明”  以查看特定于项目的指南。 

如前文所述，你可以从多个布局中进行选择，以便呈现图像。 如果在选择图像并为其分配标记后发现错误，则可以对其进行修复。 如果在图像下显示的标签中单击 `X` 目标，则将清除标记。 或者，如果选择该图像并选择另一个类，则标记将切换到新选择的值。

## <a name="tagging-images-for-multi-label-classification"></a>标记图像以进行多标签分类

如果正在处理类型为“图像分类多标签”的项目，则会将一个或多个  标记应用于图像。 在任何时候，选择“说明”  页，然后导航到“查看详细说明”  以查看特定于项目的指南。 

选择要标记的图像，然后单击该标记。 选择标记会将其应用于所有选定的图像，并取消选择它们。 若要应用多个标记，必须重新选择图像。 此动画显示了多标签标记页面：

* “全部选择”  用于应用“海洋”标记
* 选择单个图像并将其标记为“特写”
* 选择了三个图像，并将其标记为“广角”

![显示多标签流的动画](media/how-to-label-images/multilabel.gif)

若要更正错误，可以单击 `X` 以清除各个标记，或者选择图像然后选择标记，这将从所有选定的图像中清除标记。 此处显示了这种情况，单击“陆地”将从两个选定的图像中清除该标记。

![显示多个取消选择的屏幕截图](media/how-to-label-images/multiple-deselection.png)

仅当将至少一个标记应用于每个图像后，Azure 才会启用“提交”  按钮。 按“提交”  以保存工作。

## <a name="tagging-images-and-bounding-boxes-for-object-detection"></a>标记图像和边界框以进行对象检测

如果项目的类型为“对象标识(边界框)”，则需要在图像中指定一个或多个边界框，并将标记应用于该框。 每个图像都可以有多个边界框，每个框都具有单一标记。 使用“查看详细说明”  来确定添加多个边界框是否适用于你的项目。

1. 为要创建的边界框选择标记
1. 选择“矩形框”  工具 ![矩形框工具](media/how-to-label-images/rectangular-box-tool.png) 或按“R” 
1. 在目标中单击并沿对角线拖动以创建大致的边界框
    * 通过单击并拖动框的边缘或角来调整边界框

![基本边界框创建操作的屏幕截图](media/how-to-label-images/bounding-box-sequence.png)

如果要删除边界框，请在创建后单击边界框旁显示的 X 形目标。

不能重新分配现有边界框的标记。 如果标记分配出错，则必须删除该边界框，并使用正确的标记创建新的边界框。

默认情况下，可以编辑现有的边界框。 “锁定/解锁区域”  工具 ![锁定/解锁区域工具](media/how-to-label-images/lock-bounding-boxes-tool.png) 或“L”可切换该行为。 如果区域已锁定，则只能更改新边界框的形状或位置。

使用“区域操作”  工具 ![区域操作工具](media/how-to-label-images/regions-tool.png) 或“M”来调整现有的边界框。 可以单击并拖动边缘或角来调整形状。 如果在内部单击，则可以拖动整个边界框。 如果无法编辑某个区域，则很可能已切换了“锁定/解锁区域”工具  。 

使用“基于模板的框”  工具 ![模板的框工具](media/how-to-label-images/template-box-tool.png) 或“T”来创建大小相同的多个边界框。 如果图像没有边界框，并且你激活基于模板的框，则该工具将生成 50x50 像素框。 如果已创建了边界框，然后激活基于模板的框，则新的边界框将是你所做的上一个框的大小。 可以在放置后调整基于模板的框的大小。 调整基于模板的框的大小仅会调整特定框的大小。 

如果要删除当前图像中的所有边界框  ，可以选择“删除所有区域”  工具 ![删除区域工具](media/how-to-label-images/delete-regions-tool.png)。 

创建图像的边界框后，按“提交”  以保存工作。 除非按下“提交”  按钮，否则不会保存正在进行的工作。 

## <a name="finishing-up"></a>即将完成 

提交已标记的数据页时，Azure 会从工作队列为你分配新的未标记数据。 如果没有更多未标记的数据，你将看到一条该结果的消息，其中包含一个返回门户主页的链接。 

如果你知道不会进行更多的标签操作，请在标签门户的右上角选择你的名称，并选择“注销”  。 如果未注销，最终 Azure 会“超时”并将数据分配给另一个做标签的人。 

## <a name="next-steps"></a>后续步骤

* 了解[在 Azure 中训练图像分类模型](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
* 阅读有关[使用 Azure 和更快 CNN 技术进行对象检测](https://www.microsoft.com/developerblog/2017/10/24/bird-detection-with-azure-ml-workbench/)的信息