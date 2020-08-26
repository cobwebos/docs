---
title: 教程：使用 AI Builder 创建表单处理应用 - 表单识别器
titleSuffix: Azure Cognitive Services
description: 在本教程中，将使用 AI Builder 创建和训练表单处理应用程序。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 07/01/2020
ms.author: pafarley
ms.openlocfilehash: 981c6f6bb2b0eb597b32ce8e428ef0aa7d19929b
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "88003364"
---
# <a name="tutorial-create-a-form-processing-app-with-ai-builder"></a>教程：使用 AI Builder 创建表单处理应用

[AI Builder](https://docs.microsoft.com/ai-builder/overview) 是一种 Power Platform 功能，可让你自动执行进程并预测结果以提高业务绩效。 可以使用 AI Builder 表单处理创建用于标识和提取表单文档中的键值对和表数据的 AI 模型。

> [!NOTE]
> 此项目也作为 [Microsoft Learn 模块](https://docs.microsoft.com/learn/modules/get-started-with-form-processing/)提供。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建表单处理 AI 模型
> * 训练模型
> * 发布要在 Azure Power Apps 或 Power Automate 中使用的模型

## <a name="prerequisites"></a>先决条件

* 一组具有相同类型的表单，至少五个，用于训练/测试数据。 有关整理训练数据集的提示和选项，请参阅[生成训练数据集](./build-training-data-set.md)。 对于本快速入门，可以使用[示例数据集](https://go.microsoft.com/fwlink/?linkid=2128080)的 **Train** 文件夹下的文件。
* Power Apps 或 Power Automate 许可证 - 请参阅[许可指南](https://go.microsoft.com/fwlink/?linkid=2085130)。 许可证必须包含 [Common Data Service](https://powerplatform.microsoft.com/en-us/common-data-service/)。
* AI Builder [附加产品或试用版](https://go.microsoft.com/fwlink/?LinkId=2113956&clcid=0x409)。


## <a name="create-a-form-processing-project"></a>创建表单处理项目

1. 转到 [Power Apps](https://make.powerapps.com/) 或 [Power Automate](https://flow.microsoft.com/signin)，并使用你的组织帐户登录。
1. 在左窗格中选择“AI Builder” > “生成”。 
1. 选择“表单处理”卡。
1. 键入模型的名称。
1. 选择“创建”。

## <a name="upload-and-analyze-documents"></a>上传和分析文档

在“添加文档”页上，需要提供示例文档，以便针对要从中提取信息的表单类型来训练模型。 上传文档之后，AI Builder 会进行分析，以检查它们是否足以训练模型。

> [!NOTE]
> AI Builder 目前不支持以下类型的表单处理输入数据：
>
> - 复杂表（嵌套表、合并的标题或单元格，等等）
> - 复选框或单选按钮
> - 超过 50 页的 PDF 文档
> - 可填写的 PDF
>
> 有关输入文档要求的详细信息，请参阅[输入要求](./overview.md#input-requirements)。

### <a name="upload-your-documents"></a>上传文档

1. 选择“添加文档”，选择最少五个文档，然后选择“上传”。
1. 上传完成之后，选择“关闭”。
1. 然后选择“分析”。

> [!NOTE] 
> 上传这些文档之后，仍可以删除某些文档或上传其他文档。

> [!div class="mx-imgBorder"]
> ![添加文档页](./media/tutorial-ai-builder/add-documents-page.png)

### <a name="analyze-your-documents"></a>分析文档

在分析步骤中，AI Builder 会检查上传的文档并检测字段和表。 完成此操作所需的时间取决于提供的文档数，但在大多数情况下，应该只需几分钟即可完成。

完成分析后，选择缩略图以打开字段选择体验。

> [!IMPORTANT]
> 如果分析失败，则可能是由于 AI Builder 无法在文档中检测到结构化文本。 验证所更新的文档是否符合[输入要求](./overview.md#input-requirements)。

## <a name="select-your-form-fields"></a>选择表单字段

在字段选择页上，可选择感到重要的字段：

1. 若要选择字段，请单击指示文档中检测到的字段的矩形，或通过单击并拖动来选择多个字段。 还可以从右侧窗格中选择字段。
1. 如果要进行重命名以符合需要或规范化提取的标签，请单击所选字段的名称。

    单击检测到的字段时，以下信息会出现：

    - 字段名称：检测到的字段的标签名称。
    - 字段值：检测到的字段的值。

> [!div class="mx-imgBorder"]
> ![添加文档页](./media/tutorial-ai-builder/select-fields-page.png)

### <a name="label-undetected-fields"></a>标记未检测到的字段

如果模型未自动检测到要标记的字段，可以在其值周围绘制一个矩形，然后在出现的对话框中键入标签名称。

## <a name="train-your-model"></a>训练模型

1. 选择“下一步”以检查所选表单字段。 如果一切正常，请选择“训练”来训练模型。

    > [!div class="mx-imgBorder"]
    > ![添加文档页](./media/tutorial-ai-builder/summary-train-page.png)
1. 训练完成后，在“训练完成”屏幕中选择“转到详细信息页”。
## <a name="quick-test-your-model"></a>快速测试模型

通过“详细信息”页可以在发布或使用模型之前进行测试：

1. 在“详细信息”页上，选择“快速测试”。
2. 可以拖放文档，也可以选择“从我的设备上传”以上传测试文件。 快速测试应该只需几秒钟即可显示结果。
3. 可以在完成后选择“重新开始”以运行其他测试，也可选择“关闭”。

### <a name="troubleshooting-tips"></a>故障排除提示

如果对于某些字段收到不良结果或较低的可信度分数，请尝试以下提示：

- 使用每个字段中具有不同值的表单重新训练。
- 使用更大的一组训练文档重新训练。 标记的文档越多，AI Builder 便越了解如何更好地识别字段。
- 可以通过仅选择要进行训练的特定页来优化 PDF 文件。 使用“打印” > “打印为 PDF”选项来选择文档中的特定页。

## <a name="publish-your-model"></a>发布模型

如果对模型感到满意，请选择“发布”进行发布。 发布完成后，模型会提升为“已发布”，已准备好进行使用。

> [!div class="mx-imgBorder"]
> ![添加文档页](./media/tutorial-ai-builder/model-page.png)

发布了表单处理模型之后，可以在 [Power Apps 画布应用](https://docs.microsoft.com/ai-builder/form-processor-component-in-powerapps)或 [Power Automate](https://docs.microsoft.com/ai-builder/form-processing-model-in-flow) 中进行使用。

## <a name="next-steps"></a>后续步骤

按照 AI Builder 文档中的说明使用表单处理模型。

* [在 Power Apps 中使用表单处理器组件](https://docs.microsoft.com/ai-builder/form-processor-component-in-powerapps)
* [在 Power Automate 中使用表单处理模型](https://docs.microsoft.com/ai-builder/form-processing-model-in-flow)