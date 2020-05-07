---
title: 在标记项目中标记图像
title.suffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习标记项目中使用数据标记工具。
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 04/09/2020
ms.openlocfilehash: 2c21a8770209871be4d871a08e6355e4ca7ed169
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82204315"
---
# <a name="tag-images-in-a-labeling-project"></a>在标记项目中标记图像

项目管理员在 Azure 机器学习中[创建标记项目](https://docs.microsoft.com/azure/machine-learning/how-to-create-labeling-projects#create-a-labeling-project)后，你可以使用标记工具（公共预览版）为机器学习项目快速准备数据。 本文介绍：

> [!div class="checklist"]
> * 如何访问标签项目
> * 标记工具
> * 如何使用工具执行特定的标签任务

## <a name="prerequisites"></a>先决条件

* 或组织和项目的 [Microsoft 帐户](https://account.microsoft.com/account)或 Azure Active Directory 帐户
* 对包含标签项目的工作区具有参与者级别访问权限。

## <a name="sign-in-to-the-workspace"></a>登录到工作区

1. 登录到 [Azure 机器学习工作室](https://ml.azure.com)。

1. 选择包含标签项目的订阅和工作区。  从项目管理员处获取此信息。

1. 选择左侧的“数据标记”  来查找项目。  

1. 选择列表中的项目名称。

## <a name="understand-the-labeling-task"></a>了解标记任务

选择项目后，在页面顶部选择“标签数据”  。

你会看到特定于项目的说明。 其中解释了现有的数据类型、如何做出决策以及其他相关信息。 阅读此信息后，在页面顶部选择“任务”  。  或者在页面底部选择“开始标记”。 

## <a name="common-features-of-the-labeling-task"></a>标签任务的常用功能

在所有图像标记任务中，需从项目管理员指定的集内选择适当的标记。 可以使用键盘上的数字键选择前九个标记。  

在图像分类任务中，可以同时选择查看多个图像。 使用图像区域上方的图标选择布局。 

若要同时选择所有显示的图像，请使用“全选”。  若要选择单个图像，请使用图像右上角的循环选择按钮。 必须至少选择一个图像才能应用标记。 如果选择多个图像，选择的任何标记将应用到所有选定的图像。

此处我们选择了 2x2 布局，并将“哺乳动物”标记应用到熊和虎鲸的。 鲨鱼图像标记为“软骨鱼”；尚未对鬣蜥进行标记。

![多个图像布局和选择](./media/how-to-label-images/layouts.png)

> [!Important] 
> 仅当有包含未标记数据的新页面时，才可以切换布局。 切换布局会清除页面的正在进行的标记工作。

当你在页面上标记所有图像时，Azure 会启用“提交”  按钮。 选择“提交”以保存工作。 

提交手头数据的标记后，Azure 将使用工作队列中的一组新图像刷新页面。

### <a name="assisted-machine-learning"></a>辅助机器学习 

在执行多类或多标签分类任务期间，可能会触发机器学习算法。 如果在项目中启用了这些算法，你可能会看到以下内容：

* 在标记了一定数量的图像后，你可能会在屏幕顶部的项目名称旁边看到“群集任务”  。  这意味着这些图像被分组到一起，在同一页面上呈现相似的图像。  如果是这样，请切换到多个图像视图中的一个来利用分组。  

* 稍后，你可能会在项目名称旁边看到“预标记任务”  。  然后，图像将显示一个来自机器学习分类模型的建议标签。 任何机器学习模型都达不到 100% 的准确度。 虽然我们只使用模型有信心识别的图像，但这些图像仍然可能没有正确地预标记。  看到这些标签时，请在提交页面之前更正任何错误的标签。  

特别是在标记项目的早期阶段，机器学习模型可能只能准确地预标记一小部分图像。 在标记这些图像后，标记项目将返回到手动标记，为下一轮模型训练收集更多数据。 随着时间的推移，模型将对更高比例的图像更有信心，从而在项目的后期产生更多的预标记任务。

## <a name="tag-images-for-multi-class-classification"></a>标记图像以进行多类分类

如果项目的类型为“图像分类多类”，则会将单一标记分配给整个图像。 若要随时查看指导，请转到“说明”页，然后选择“查看详细说明”。  

如果在向图像分配标记后发现有误，可以修复标记。 选择图像下面显示的标签上的“X”可以清除标记。  或者选择该图像，然后选择另一个类。 新选择的值将替换以前应用的标记。

## <a name="tag-images-for-multi-label-classification"></a>标记图像以进行多标签分类

如果正在处理类型为“多标签图像分类”的项目，则会将一个或多个标记应用到图像。  若要查看特定于项目的指导，请选择“说明”并转到“查看详细说明”。  

选择要标记的图像，然后选择标记。 该标记将应用到所有选定的图像，然后会取消选择这些图像。 若要应用多个标记，必须重新选择图像。 以下动画演示了多标签标记：

1. “全选”用于应用“海洋”标记。 
1. 选择单个图像并将其标记为“特写”。
1. 选择了三个图像，并将其标记为“广角”。

![演示多标签流的动画](./media/how-to-label-images/multilabel.gif)

若要更正错误，可以单击“X”以清除单个标记，或选择图像后选择标记，从所有选定图像中清除该标记。  此处演示了上述场景。 单击“陆地”会从两个选定图像中清除该标记。

![显示多个取消选择操作的屏幕截图](./media/how-to-label-images/multiple-deselection.png)

仅当将至少一个标记应用于每个图像后，Azure 才会启用“提交”  按钮。 选择“提交”以保存工作。 


## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>标记图像并指定边界框以进行对象检测

如果项目的类型为“对象标识(边界框)”，请在图像中指定一个或多个边界框，并将标记应用到每个框。 图像都可以有多个边界框，每个框具有单个标记。 使用“查看详细说明”来确定项目中是否使用了多个边界框。 

1. 选择要创建的边界框的标记。
1. 选择“矩形框”工具 ![矩形框工具](./media/how-to-label-images/rectangular-box-tool.png) 或按“R”。 
3. 在目标中单击并沿对角线拖动以创建大致的边界框。 若要调整边界框，请拖动边或角。

![演示如何创建基本边界框的屏幕截图。](./media/how-to-label-images/bounding-box-sequence.png)

若要删除边界框，请在创建后单击边界框旁边显示的 X 形目标。

无法更改现有边界框的标记。 如果错误地分配了标记，则必须删除边界框，并使用正确的标记创建新的边界框。

默认情况下，可以编辑现有的边界框。 “锁定/解锁区域”  工具 ![锁定/解锁区域工具](./media/how-to-label-images/lock-bounding-boxes-tool.png) 或“L”可切换该行为。 如果区域已锁定，则只能更改新边界框的形状或位置。

使用“区域操作”  工具 ![区域操作工具](./media/how-to-label-images/regions-tool.png) 或“M”来调整现有的边界框。 拖动边或角来调整形状。 在内部单击即可拖动整个边界框。 如果无法编辑某个区域，则很可能已切换了“锁定/解锁区域”工具  。

使用“基于模板的框”  工具 ![模板的框工具](./media/how-to-label-images/template-box-tool.png) 或“T”来创建大小相同的多个边界框。 如果图像没有边界框，并且你激活基于模板的框，则该工具将生成 50x50 像素框。 如果创建边界框，然后激活基于模板的框，任何新边界框将采用上次创建的框的大小。 可以在放置后调整基于模板的框的大小。 调整基于模板的框的大小只会调整该特定框的大小。

若要删除当前图像中的所有边界框，请选择“删除所有区域”工具 ![删除区域工具](./media/how-to-label-images/delete-regions-tool.png)。  

创建图像的边界框后，请选择“提交”以保存工作，否则正在进行的工作不会保存。 

## <a name="finish-up"></a>完成

当你提交已标记数据的页时，Azure 会从工作队列为你分配新的未标记数据。 如果没有其他未标记的数据，你将看到一条消息，其中包含门户主页的链接。

完成标记操作后，请在标记门户的右上角选择自己的姓名，然后选择“注销”。  如果未注销，最终 Azure 会“超时”并将数据分配给另一个做标签的人。

## <a name="next-steps"></a>后续步骤

* 了解[在 Azure 中训练图像分类模型](https://docs.microsoft.com/azure/machine-learning/tutorial-train-models-with-aml)

