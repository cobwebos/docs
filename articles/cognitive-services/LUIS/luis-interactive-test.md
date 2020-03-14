---
title: 在 LUIS 门户中测试应用
description: 使用语言理解 (LUIS) 持续优化应用程序并改进其语言理解能力。
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: ba7e23a72cd308dd4393bf9a581571e2bc9f5fa0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219815"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>在 LUIS 门户中测试 LUIS 应用

[测试](luis-concept-test.md)应用程序是一个迭代过程。 训练 LUIS 应用后，采用示例陈述来对应用进行测试，查看应用是否能准确地识别意向和实体。 如果未能准确识别，请对 LUIS 应用进行更新和训练，然后再次测试。

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>测试前训练

若要针对活动应用的最新版本进行测试，请在测试前从顶部菜单中选择 "**训练**"。

## <a name="test-an-utterance"></a>测试陈述

测试查询文本不应与应用中的任何示例最谈话完全相同。 测试查询文本应包括用户预期的 word 选择、短语长度和实体使用情况。

1. 在“我的应用”页面上选择应用名称以访问应用。

1. 若要访问“测试”滑出面板，请在应用程序的顶部面板中选择“测试”。

    > [!div class="mx-imgBorder"]
    > ![训练 & 测试应用程序 "页](./media/luis-how-to-interactive-test/test.png)

1. 在文本框中输入陈述，然后按 Enter。 虽然在“测试”中可键入任意数量的测试陈述，但一次只能键入一个。

1. 陈述的最高意向和分数会添加至文本框下方的陈述列表。

    ![交互式测试识别错误意向](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>检查分数

在“检查”面板中检查测试结果的详细信息。

1. 打开“测试”滑出面板后，对想要比对的陈述选择“检查”。

    ![选择“检查”按钮可查看有关测试结果的更多详细信息](./media/luis-how-to-interactive-test/inspect.png)

1. 此时将显示“检查”面板。 此面板包括评分最高的意向以及任何已识别的实体。 此面板显示所选陈述的结果。

    ![此面板包括评分最高的意向以及任何已识别的实体。 此面板显示所选陈述的结果。](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>更正评分最高的意向

1. 如果评分最高的意向不正确，请选择“编辑”按钮。

1.  在下拉列表中选择该陈述的正确意向。

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

## <a name="additional-settings-in-test-panel"></a>测试面板中的其他设置

### <a name="luis-endpoint"></a>LUIS 终结点

如果有多个 LUIS 终结点，请使用测试的“已发布”窗格上的“其他设置”链接来更改用于测试的终结点。 如果不确定要使用哪个终结点，请选择默认的“Starter_Key”。

> [!div class="mx-imgBorder"]
> 突出显示了其他设置链接 ![测试面板](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>批处理测试
请参阅批处理测试[概念](luis-concept-batch-test.md)并了解陈述批量测试的[方法](luis-how-to-batch-test.md)。

## <a name="next-steps"></a>后续步骤

如果测试表明 LUIS 应用未正确识别意向和实体，则可以通过标记更多陈述或添加功能来提高 LUIS 应用的准确性。

* [使用 LUIS 标记建议的陈述](luis-how-to-review-endpoint-utterances.md)
* [使用相关功能来改进 LUIS 应用的性能](luis-how-to-add-features.md)
