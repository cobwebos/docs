---
title: 查看系统测试结果和部署 - 自定义翻译
titleSuffix: Azure Cognitive Services
description: 训练成功后，可以审查系统测试以分析训练结果。 如果对训练结果感到满意，可以针对训练的模型下达部署请求。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 3361241bf0a330abc18701f93460208b8804a7dc
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83994253"
---
# <a name="view-system-test-results"></a>查看系统测试结果

训练成功后，可以审查系统测试以分析训练结果。 如果对训练结果感到满意，可以针对训练的模型下达部署请求。

## <a name="system-test-results-page"></a>系统测试结果页

选择一个项目，然后选择该项目的“模型”选项卡，再找到要使用的模型，最后选择“测试”选项卡。

“测试”选项卡显示：

1.  **系统测试结果：** 培训过程中测试过程的结果。 测试过程生成 BLEU 评分。

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

1.  **自定义 mt：** 此文件包含由使用用户数据训练的模型完成的目标语言中源语言句子的机器翻译。

2.  **引用 .txt：** 此文件包含用户在目标语言中提供的源语言句子翻译。

3.  **源 .txt：** 此文件包含源语言中的句子。

    ![下载系统测试结果](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>部署模型

请求部署：

1.  选择一个项目，并转到“模型”选项卡。

2. 对于已成功训练的模型，如果未部署，它将显示 "部署" 按钮。

    ![部署模型](media/how-to/how-to-deploy-model.png)

3.  单击“部署”。
4.  针对要在其中部署模型的区域选择“已部署”****，然后单击“保存”。 可以为多个区域选择“已部署”。****

    ![部署模型](media/how-to/how-to-deploy-model-regions.png)

5.  您可以在 "状态" 列中查看模型的状态。

>[!Note]
>自定义转换器在任一时间点在工作区中支持10个已部署的模型。

## <a name="update-deployment-settings"></a>更新部署设置

若要更新部署设置，请执行以下操作：

1.  选择一个项目，并转到“模型”**** 选项卡。

2. 对于已成功部署的模型，它会显示“更新”**** 按钮。

    ![部署模型](media/how-to/how-to-update-undeploy-model.png)

3.  选择“更新”。
4.  针对要在其中部署或取消部署模型的区域选择“已部署”或“已取消部署”，然后单击“保存”。************

    ![部署模型](media/how-to/how-to-undeploy-model.png)

>[!Note]
>如果针对所有区域选择“已取消部署”，则会将模型从所有区域取消部署，并将其置于已取消部署状态。**** 它现在不可使用。

## <a name="next-steps"></a>后续步骤

- 开始通过[转换器 V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)使用已部署的自定义翻译模型。
- 了解[如何管理设置](how-to-manage-settings.md)以共享工作区和管理订阅密钥。
- 了解如何从 [Microsoft Translator Hub](https://hub.microsofttranslator.com)[迁移工作区和项目](how-to-migrate.md)
