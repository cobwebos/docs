---
title: 训练和测试 LUIS 应用 - Azure | Microsoft Docs
description: 使用语言理解智能服务 (LUIS) 持续优化应用程序并改进其语言理解能力。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: 925b96b61de876d40105c89556fd6dcb600d1b1a
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223644"
---
# <a name="test-your-luis-app"></a>测试 LUIS 应用
对应用进行<a name="train-your-app"></a>
[训练](luis-how-to-train.md)和[测试](luis-concept-test.md)是一个迭代过程。 训练 LUIS 应用后，采用示例陈述来对应用进行测试，查看应用是否能准确地识别意向和实体。 如果未能准确识别，请对 LUIS 应用进行更新和训练，然后再次测试。 

<!-- anchors for H2 name changes -->
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>
## <a name="test-an-utterance"></a>测试陈述

1. 在“我的应用”页面选择应用的名字以访问应用。 

2. 若要访问“测试”滑出面板，请在应用程序的顶部面板中选择“测试”。

    ![训练和测试应用页](./media/luis-how-to-interactive-test/test.png)

3. 在文本框中输入陈述，然后按 Enter。 虽然在“测试”中可键入任意数量的测试陈述，但一次只能键入一个。

4. 陈述的最高意向和分数会添加至文本框下方的陈述列表。

    ![交互式测试识别错误意向](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="clear-test-panel"></a>清除测试面板
若要从测试控制台中清除所有输入的测试陈述及其结果，请选择“测试”面板左上角的“重新开始”。 

## <a name="close-test-panel"></a>关闭测试面板
若要关闭“测试”面板，请再次选择“测试”按钮。

## <a name="inspect-score"></a>检查分数
在“检查”面板中检查测试结果的详细信息。 
 
1. 打开“测试”滑出面板后，对想要比对的陈述选择“检查”。 

    ![“检查”按钮](./media/luis-how-to-interactive-test/inspect.png)

2. 此时将显示“检查”面板。 此面板包括评分最高的意向以及任何已识别的实体。 此面板显示所选陈述的结果。

    ![“检查”按钮](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>更正评分最高的意向

1. 如果评分最高的意向不正确，请选择“编辑”按钮。

2.  在下拉列表中选择该陈述的正确意向。

    ![选择正确的意向](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>查看情绪结果

如果在[发布](luis-how-to-publish-app.md#enable-sentiment-analysis)页面上配置了“情绪分析”，则测试结果会包括在该陈述中发现的情绪。 

![包含情绪分析的“测试”窗格的图像](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>更正匹配的模式的意向
如果使用[模式](luis-concept-patterns.md)并且该陈述与某个模式匹配，但是意向预测错误，请选择该模式旁边的“编辑”链接，然后选择正确的意向。

## <a name="compare-with-published-version"></a>与已发布的版本进行比较
可以使用已发布的[终结点](luis-glossary.md#endpoint)版本测试应用的活动版本。 在“检查”面板中选择“与已发布版本进行比较”。 针对该发布模型的任何测试都会从 Azure 订阅配额余量中扣除。 

![与已发布版本进行比较](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>在测试面板中查看终结点 JSON
选择“显示 JSON 视图”，可以查看该比较返回的终结点 JSON。

![已发布的 JSON 响应](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>测试面板中的其他设置

### <a name="luis-endpoint"></a>LUIS 终结点
如果有多个 LUIS 终结点，请使用测试的“已发布”窗格上的“其他设置”链接来更改用于测试的终结点。 如果不确定要使用哪个终结点，请选择默认的“Starter_Key”。 

![突出显示了“其他设置”链接的测试面板](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)


### <a name="view-bing-spell-check-corrections-in-test-panel"></a>在测试面板中查看必应拼写检查更正
查看拼写检查的要求： 

* 已发布的应用
* 必应拼写检查[服务密钥](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)。 密钥服务不会存储，需对每个浏览器会话重置。 

按照以下流程，将[必应拼写检查 v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) 服务包括在“测试”窗格结果中。 

1. 在“测试”窗格中输入一个陈述。 预测陈述后，选择输入的陈述下方的“[检查](#inspect-score)”。 

2. “检查”面板打开后，选择“[与已发布版本进行比较](#compare-with-published-version)”。 

3. “已发布”面板打开后，选择“[其他设置](#additional-settings-in-test-panel)”。

4. 在弹出的对话框中，输入必应拼写检查服务密钥。 
    ![输入必应拼写检查服务密钥](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)

5. 输入一个拼写不正确的查询，例如 `book flite to seattle`，并按 Enter。 在发送至 LUIS 的查询中，拼写不正确的 `flite` 单词会被替换，生成的 JSON 将原始查询显示为 `query`，并将查询中更正的拼写显示为 `alteredQuery`。

    ![更正的 JSON 拼写](./media/luis-how-to-interactive-test/interactive-with-spell-check-results.png)

<a name="json-file-with-no-duplicates"></a>
<a name="import-a-dataset-file-for-batch-testing"></a>
<a name="export-rename-delete-or-download-dataset"></a>
<a name="run-a-batch-test-on-your-trained-app"></a>
<a name="access-batch-test-result-details-in-a-visualized-view"></a>
<a name="filter-chart-results-by-intent-or-entity"></a>
<a name="investigate-false-sections"></a>
<a name="view single-point utterance data"></a>
<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>
## <a name="batch-testing"></a>批处理测试
请参阅批处理测试[概念](luis-concept-batch-test.md)并了解陈述批量测试的[方法](luis-how-to-batch-test.md)。

## <a name="next-steps"></a>后续步骤

如果测试表明 LUIS 应用未正确识别意向和实体，则可以通过标记更多陈述或添加功能来提高 LUIS 应用的准确性。 

* [使用 LUIS 标记建议的陈述](luis-how-to-review-endoint-utt.md) 
* [使用相关功能来改进 LUIS 应用的性能](luis-how-to-add-features.md) 
