---
title: 查看系统测试结果和部署 - 自定义翻译
titleSuffix: Azure Cognitive Services
description: 训练成功后，可以审查系统测试以分析训练结果。 如果对训练结果感到满意，可以针对训练的模型下达部署请求。
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 61d3869559d88e14c0b9a3c3e23cd8a1f9c2b6c4
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626729"
---
# <a name="view-system-test-results"></a>查看系统测试结果

训练成功后，可以审查系统测试以分析训练结果。 如果对训练结果感到满意，可以针对训练的模型下达部署请求。 

## <a name="system-test-results-page"></a>系统测试结果页

选择一个项目，然后选择该项目的“模型”选项卡，再找到要使用的模型，最后选择“测试”选项卡。

“测试”选项卡显示：

1.  系统测试结果：训练中测试过程的结果。 测试过程生成 BLEU 评分。

    **句子计数：** 在测试集中使用了多少个并行句子。

     **BLEU 评分：** 完成训练后为模型生成的 BLEU 评分。

    **状态：** 指示测试过程是已完成还是正在进行。

    ![系统测试结果](media/how-to/how-to-system-test-results.png)

2.  单击“系统测试结果”，转到“测试结果详细信息”页。 此页显示测试数据集中包含的句子的机器翻译。

3.  “测试结果详细信息”页上的表包含两列 - 语言对中的每种语言各有一列。 源语言对应的列显示要翻译的句子。 目标语言对应的列在每行中包含两个句子。

    **参考：** 此句子是测试数据集中为源句子提供的参考翻译。

    **机器翻译：** 此句子是执行训练后生成的模型为源句子提供的自动翻译。

    ![系统测试结果比较](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>下载测试

单击“下载翻译”链接下载一个 zip 文件。 该 zip 文件包含测试数据集中源句子的机器翻译。

![下载测试](media/how-to/how-to-system-test-download.png)

下载的 zip 存档中包含三个文件。

1.  custom.mt.txt：此文件包含使用用户数据训练的模型为源语言句子提供的目标语言机器翻译。

2.  ref.txt：此文件包含用户为源语言句子提供的目标语言翻译。

3.  source.txt：此文件包含源语言的句子。

    ![下载系统测试结果](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>部署模型

请求部署：

1.  选择一个项目，并转到“模型”选项卡。

2. 对于成功训练的但尚未部署的模型，将显示“部署”按钮。

    ![部署模型](media/how-to/how-to-deploy-model.png)

3.  单击“部署”。
4.  选择要将模型部署到的区域，并单击“保存”。 可以选择多个区域。

    ![部署模型](media/how-to/how-to-deploy-model-regions.png)

5.  可在“状态”列中查看模型的状态。

>[!Note]
>如果已部署某个模型，会看到该模型对应的“取消部署”按钮。 若要取消部署模型，请单击“取消部署”。

## <a name="next-steps"></a>后续步骤

- 通过 [Microsoft 文本翻译 API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) 开始使用已部署的自定义翻译模型。
- 了解[如何管理设置](how-to-manage-settings.md)以共享工作区和管理订阅密钥。
- 了解如何从 [Microsoft Translator Hub](https://hub.microsofttranslator.com) [迁移工作区和项目](how-to-migrate.md)