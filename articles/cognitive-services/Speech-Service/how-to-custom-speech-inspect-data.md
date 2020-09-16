---
title: 检查自定义语音识别的数据质量 - 语音服务
titleSuffix: Azure Cognitive Services
description: 自定义语音识别提供的工具可以将音频数据与相应的识别结果进行比较，因此可以通过视觉方式检查模型的质量。 你可以播放上传的音频，确定提供的识别结果是否正确。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: erhopf
ms.openlocfilehash: d4da9a819d7aa96992259112c75154b1651341ac
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604730"
---
# <a name="inspect-custom-speech-data"></a>检查自定义语音识别数据

> [!NOTE]
> 此页假定你已阅读[准备自定义语音识别的测试数据](how-to-custom-speech-test-data.md)，并已上传用于检查的数据集。

自定义语音识别提供的工具可以将音频数据与相应的识别结果进行比较，因此可以通过视觉方式检查模型的质量。 在[自定义语音识别门户](https://speech.microsoft.com/customspeech)中，你可以播放上传的音频，确定提供的识别结果是否正确。 此工具可帮助你检查 Microsoft 的基线语音到文本模型的质量，检查训练的自定义模型，或比较两个模型的脚本。

在本文档中，你将了解如何以视觉方式检查 Microsoft 的基线语音到文本模型的质量以及你训练的自定义模型。 你还将了解如何使用联机脚本编辑器来创建和优化标记的音频数据集。

## <a name="create-a-test"></a>创建测试

按这些说明创建测试：

1. 登录到[自定义语音识别门户](https://speech.microsoft.com/customspeech)。
2. 导航到“语音转文本”>“自定义语音识别”> [项目名称] >“测试”。
3. 单击“添加测试”。
4. 选择“检查质量(仅音频数据)”。 为测试提供名称和说明，然后选择你的音频数据集。
5. 选择最多两个要测试的模型。
6. 单击**创建**。

成功创建测试后，你可以查看模型对指定的音频数据集的转录方式，或将两个模型中的结果并行进行比较。

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>并排进行模型比较

当测试状态为“成功”时，请单击测试项名称，查看测试详细信息。 此详细信息页列出了数据集中的所有话语，并显示了要比较的两个模型的识别结果。

可以通过切换各种错误类型（包括插入、删除和替换）来查看并排比较的结果。 通过听音频并比较每个列（显示人为标记的听录和两个语音转文本模型的结果）中的识别结果，你可以确定哪个模型符合自己的需求，以及需要在哪些方面进行改进。

并行模型测试可用于验证哪个语音识别模型最适合应用程序。 若要客观度量准确度，需对音频进行听录，并按[评估准确度](how-to-custom-speech-evaluate-data.md)中的说明操作。

## <a name="online-transcription-editor"></a>联机脚本编辑器

使用联机脚本编辑器，可以轻松地在自定义语音中使用音频转录。 编辑器的主要用例如下： 

* 只有音频数据，但想要从头开始构建准确的音频和人标记的数据集，以便在模型定型中使用。
* 您已经具有音频和人标记的数据集，但脚本中存在错误或缺陷。 编辑器允许您快速修改转录，以获得最佳的培训准确度。

使用脚本编辑器的唯一要求是将音频数据上传 (仅音频或音频 + 脚本) 。

### <a name="import-datasets-to-editor"></a>将数据集导入编辑器

若要将数据导入到编辑器，请首先导航到 **自定义语音 > [项目] > 编辑器**。

![编辑器选项卡](media/custom-speech/custom-speech-editor-detail.png)

接下来，使用以下步骤导入数据。

1. 单击 "**导入数据**"
1. ) 创建新的数据集，并为其指定说明 (
1. 选择数据集。 支持多项选择，并且你可以仅选择音频数据，以及音频和人标记的数据。
1. 对于仅音频数据，您可以选择使用默认模型在导入到编辑器后自动生成计算机脚本
1. 单击 "**导入**"

成功导入数据后，可单击数据集并开始编辑。

> [!TIP]
> 还可以通过选择数据集并单击 "**导出到编辑器**"，直接将数据集导入编辑器

### <a name="edit-transcription-by-listening-to-audio"></a>通过倾听音频来编辑脚本

数据上传成功后，单击每个项目名称以查看数据的详细信息。 详细信息页将列出数据集中的所有文件，并可单击到所需的查询文本。 对于每个查询文本，可以播放音频并检查脚本，并在发现任何插入、删除或替换错误时编辑转录。 有关错误类型的详细信息，请参阅 [数据计算操作方法](how-to-custom-speech-evaluate-data.md) 。

![编辑器页](media/custom-speech/custom-speech-editor.png)

如果音频文件过长，会自动将其分段为较小的部分。 您可以使用 " **上一** 步" 和 " **下一步** " 逐个编辑它们。 完成编辑后，单击 " **保存** " 按钮。

### <a name="export-datasets-from-the-editor"></a>从编辑器导出数据集

若要将数据集导出回 " **数据** " 选项卡，请导航到数据详细信息页，并单击 " **导出** " 按钮，将所有文件导出为新的数据集。 还可以按上次编辑时间、音频持续时间等对文件进行筛选，以部分选择所需的文件。 

![导出数据](media/custom-speech/custom-speech-editor-export.png)

导出到数据的文件将用作全新数据集，不会影响任何现有的数据/培训/测试实体。

## <a name="next-steps"></a>后续步骤

- [评估数据](how-to-custom-speech-evaluate-data.md)
- [训练模型](how-to-custom-speech-train-model.md)
- [改进模型](how-to-custom-speech-improve-accuracy.md)
- [部署模型](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>其他资源

- [准备自定义语音识别的测试数据](how-to-custom-speech-test-data.md)
