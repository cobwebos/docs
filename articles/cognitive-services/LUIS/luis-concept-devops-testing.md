---
title: DevOps for LUIS apps 的测试
description: 如何在 DevOps 环境中测试语言理解（LUIS）应用。
ms.topic: conceptual
ms.date: 06/3/2020
ms.openlocfilehash: 2556d2e904aff720bc02e4c7d58bf5a72af4d413
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86538065"
---
# <a name="testing-for-luis-devops"></a>LUIS DevOps 的测试

正在开发语言理解（LUIS）应用程序的软件工程师可以遵循以下准则，在[源代码](luis-concept-devops-sourcecontrol.md)管理、[自动生成](luis-concept-devops-automation.md)、[测试](luis-concept-devops-testing.md)和[发布管理](luis-concept-devops-automation.md#release-management)方面应用 DevOps 做法。

在敏捷软件开发方法中，测试在构建质量软件方面扮演着重要的角色。 对 LUIS 应用的每个重大更改都应附带测试，这些测试旨在测试开发人员在应用中生成的新功能。 这些测试将与 LUIS 应用程序的源一起检入到源代码存储库中 `.lu` 。 当应用程序满足测试时，将完成更改的实现。

测试是[CI/CD 工作流](luis-concept-devops-automation.md)的关键部分。 当拉取请求（PR）中建议了对 LUIS 应用程序的更改时，或者在将更改合并到主分支后，CI 工作流应运行测试，以验证更新是否未引发任何退化。

## <a name="how-to-do-unit-testing-and-batch-testing"></a>如何进行单元测试和批处理测试

对于需要在持续集成工作流中执行的 LUIS 应用，有两种不同类型的测试：

- **单元测试**-验证 LUIS 应用的主要功能的相对简单的测试。 当给定的测试查询文本返回预期意图和预期的实体时，单元测试将通过。 所有单元测试都必须通过，测试运行才能成功完成。  
这种测试类似于[交互式测试](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test)，你可以在[LUIS 门户](https://www.luis.ai/)中执行此操作。

- **批处理测试**-批处理测试是针对当前训练模型的全面测试，用于测量其性能。 与单元测试不同，批处理测试未通过 | 测试失败。 批处理测试的预期不是每个测试都将返回预期的意图和预期的实体。 相反，批处理测试可帮助你在应用中查看每个意向和实体的准确性，并帮助你在进行改进时进行比较。  
这种测试与可以在 LUIS 门户中以交互方式执行的[批处理测试](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test)相同。

你可以从项目的开头使用单元测试。 一旦您开发了 LUIS 应用程序的架构，并且您正在努力改进其准确性，批处理测试就是真正的价值。

对于单元测试和批处理测试，请确保测试最谈话与定型最谈话保持分离。 如果您在训练的相同数据上进行测试，当您的应用程序只是过度拟合测试数据时，您将会获得错误的印象。 测试必须在模型中不可见，以测试它的通用化程度。

### <a name="writing-tests"></a>编写测试

编写一组测试时，对于每个测试，都需要定义：

* 测试话语
* 预期意向
* 应为实体。

使用 LUIS[批处理文件语法](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test#batch-syntax-template-for-intents-with-entities)在 JSON 格式的文件中定义一组测试。 例如：

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities":
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

一些测试工具，例如[NLU。DevOps](https://github.com/microsoft/NLU.DevOps)还支持 LUDown 格式的测试文件。

#### <a name="designing-unit-tests"></a>设计单元测试

单元测试应设计为测试 LUIS 应用程序的核心功能。 在您的应用程序开发的每个迭代或冲刺（sprint）中，您应该编写足够多的测试，以验证您在该迭代中实现的关键功能是否正常工作。

在每个单元测试中，对于给定的测试查询文本，可以：

* 测试是否返回了正确意向
* 测试 "密钥" 实体-正在返回对解决方案至关重要的实体。
* 测试意向和实体的[预测分数](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-prediction-score)是否超出定义的阈值。 例如，你可以决定仅在意图的预测评分和关键实体超过0.75 时才会考虑测试已通过。

在单元测试中，最好测试是否已在预测响应中返回了关键实体，但忽略了任何误报。 *误报*是在预测响应中找到的实体，但未在测试的预期结果中定义。 忽略误报，这使得创作单元测试变得不太繁琐，同时仍允许您将精力集中在预测响应中以测试是关键的解决方案的数据。

> [!TIP]
> [NLU。DevOps](https://github.com/microsoft/NLU.DevOps)工具支持所有 LUIS 测试需求。 `compare`在[单元测试模式](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode)中使用时，该命令将断言所有测试都通过，并将忽略未在预期结果中标记的实体的假正结果。

#### <a name="designing-batch-tests"></a>设计批处理测试

批处理测试集应包含大量测试用例，旨在跨所有方法和 LUIS 应用中的所有实体进行测试。 有关定义批处理测试集的信息，请参阅[LUIS 门户中的批处理测试](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test)。

### <a name="running-tests"></a>运行测试

LUIS 门户提供的功能可帮助进行交互式测试：

* [**交互式测试**](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test)允许提交示例查询文本并获取 LUIS 认可的意向和实体的响应。 可以通过视觉检测来验证测试是否成功。

* [**批处理**](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test)测试使用批处理测试文件作为输入来验证您的活动定型版本，以衡量其预测准确性。 批处理测试可帮助你查看活动版本中每个意向和实体的准确性，并使用图表显示结果。

#### <a name="running-tests-in-an-automated-build-workflow"></a>在自动生成工作流中运行测试

LUIS 门户中的交互式测试功能非常有用，但对于 DevOps，在 CI/CD 工作流中执行的自动测试会带来某些要求：

* 测试工具必须在生成服务器上的工作流步骤中运行。 这意味着工具必须能够在命令行上运行。
* 测试工具必须能够对终结点执行一组测试，并根据实际结果自动验证预期的结果。
* 如果测试失败，则测试工具必须返回状态代码以暂停工作流和 "生成失败"。

LUIS 不提供命令行工具或提供这些功能的高级 API。 建议使用[NLU。DevOps](https://github.com/microsoft/NLU.DevOps)工具，用于在命令行和 CI/CD 工作流中的自动测试期间运行测试和验证结果。

LUIS 门户中可用的测试功能不需要已发布的终结点，并且属于 LUIS 创作功能。 在自动生成工作流中实现测试时，必须发布 LUIS 应用版本以便测试到终结点，以便测试工具（如 NLU）。DevOps 可以在测试过程中发送预测请求。

> [!TIP]
> * 如果要实现自己的测试解决方案并编写代码将测试最谈话发送到终结点，请记住，如果使用 LUIS 创作密钥，则允许的事务速率仅限5TPS。 要么限制发送速率，要么改用预测密钥。
> * 将测试查询发送到终结点时，请记住 `log=false` 在预测请求的查询字符串中使用。 这可确保你的测试最谈话不会被 LUIS 记录下来，并最终出现在 LUIS[活动学习](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances)功能提供的 endpoint 最谈话查看列表中，因此，意外地将其添加到应用的定型最谈话。

#### <a name="running-unit-tests-at-the-command-line-and-in-cicd-workflows"></a>在命令行和 CI/CD 工作流中运行单元测试

可以使用[NLU。DevOps](https://github.com/microsoft/NLU.DevOps)包，用于在命令行中运行测试：

* 请使用 NLU。DevOps [test 命令](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md)，将测试文件中的测试提交到终结点，并在文件中捕获实际的预测结果。
* 请使用 NLU。DevOps [compare 命令](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md)将实际结果与输入测试文件中定义的预期结果进行比较。 `compare`命令生成 NUnit 测试输出，并通过使用标志在[单元测试模式](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode)中使用时 `--unit-test` ，将断言所有测试都通过。

### <a name="running-batch-tests-at-the-command-line-and-in-cicd-workflows"></a>在命令行和 CI/CD 工作流中运行批处理测试

还可以使用 NLU。DevOps package，用于在命令行运行批测试。

* 请使用 NLU。DevOps [test 命令](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md)，将测试文件中的测试提交到终结点，并在文件中捕获实际的预测结果，与使用单元测试一样。
* 请使用 NLU。DevOps "[性能测试模式](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#performance-test-mode)" 中的 "[比较" 命令](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md)来测量应用程序的性能，还可以将应用程序的性能与基线性能基准进行比较，例如，从最新的提交到 master 或当前版本的结果。 在性能测试模式中，该 `compare` 命令将生成 JSON 格式的 NUnit 测试输出和[批处理测试结果](https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#batch-test)。

## <a name="luis-non-deterministic-training-and-the-effect-on-testing"></a>LUIS 非确定性培训和对测试的影响

当 LUIS 在训练模型（例如意向）时，它需要两个正的数据（已标记的培训最谈话，你为该模型的使用情况提供培训，*以便为模型*和负值数据提供培训。 在定型过程中，LUIS 将从为其他模型提供的所有正面数据生成一个模型的负值数据，但在某些情况下，可能会导致数据不平衡。 为了避免这种不平衡性，LUIS 将以非确定性的方式为负值数据的一个子集进行优化，以优化更好的平衡定型集、改进模型性能以及缩短定型时间。

这种非确定性定型的结果是，您可能会在[不同培训会话之间获得略微不同的预测响应](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-prediction-score)，这通常适用于[预测分数](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-prediction-score)不高的意图和/或实体。

如果要针对出于测试目的而生成的 LUIS 应用版本禁用非确定性培训，请使用设置为的 "[版本设置" API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) `UseAllTrainingData` `true` 。

## <a name="next-steps"></a>后续步骤

* 了解如何[实现 CI/CD 工作流](luis-concept-devops-automation.md)
* 了解如何[通过 GitHub 为 LUIS 实现 DevOps](luis-how-to-devops-with-github.md)