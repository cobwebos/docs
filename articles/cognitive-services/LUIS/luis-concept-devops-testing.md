---
title: LUIS 应用 DevOps 测试
description: 如何在 DevOps 环境中测试语言理解 (LUIS) 应用。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/3/2020
ms.openlocfilehash: c41e9fe1f197334bce27241ab9f28309c92f7e0a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316539"
---
# <a name="testing-for-luis-devops"></a>LUIS DevOps 测试

正在开发语言理解 (LUIS) 应用的软件工程师可以通过遵循以下指南，应用关于[源代码管理](luis-concept-devops-sourcecontrol.md)、[自动生成](luis-concept-devops-automation.md)、[测试](luis-concept-devops-testing.md)和[发布管理](luis-concept-devops-automation.md#release-management)的 DevOps 实践。

在敏捷软件开发方法中，测试在生成优质软件方面扮演着重要的角色。 对 LUIS 应用的每个重大更改都应附带测试，旨在测试开发人员在应用中生成的新功能。 这些测试将与 LUIS 应用的 `.lu` 源一起签入到源代码存储库中。 当应用满足测试条件时，将完成更改的实现。

测试是 [CI/CD 工作流](luis-concept-devops-automation.md)的关键部分。 当拉取请求 (PR) 建议对 LUIS 应用进行更改时，或者在将更改合并到主分支后，CI 工作流应运行测试，以验证更新是否未导致任何回归。

## <a name="how-to-do-unit-testing-and-batch-testing"></a>如何进行单元测试和批处理测试

对于需要在持续集成工作流中执行的 LUIS 应用，有两种不同类型的测试：

- **单元测试** - 相对简单的测试，用于验证 LUIS 应用的主要功能。 当给定的测试言语返回预期意向和预期实体时，单元测试通过。 所有单元测试都必须通过，测试运行才能成功完成。  
这种测试类似于[交互式测试](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test)，可以在 [LUIS 门户](https://www.luis.ai/)中进行。

- **批处理测试** - 批处理测试是对当前已训练的模型进行的全面测试，用于衡量其性能。 与单元测试不同，批处理测试不是关于“通过或失败”的测试。 批处理测试的预期不是每个测试都将返回预期意向和预期实体。 相反，批处理测试可帮助你在应用中查看每个意向和实体的准确性，并帮助你将一段时间内的改进进行比较。  
这种类型的测试与可以在 LUIS 门户中以交互方式执行的[批处理测试](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test)相同。

可以从项目的开头使用单元测试。 当你开发 LUIS 应用的架构后，要提高其准确性时，批处理测试才具有真正的价值。

对于单元测试和批处理测试，请确保测试言语与训练言语保持独立。 如果将用于训练的数据用于测试，你会得到应用性能极佳的错误印象，而事实只是模型与测试数据过拟合而已。 测试对于模型必须是陌生的，以测试它的通用化程度。

### <a name="writing-tests"></a>编写测试

编写一组测试时，对于每个测试，都需要定义：

* 测试话语
* 预期意向
* 预期实体。

使用 LUIS [批处理文件语法](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test#batch-syntax-template-for-intents-with-entities)在 JSON 格式的文件中定义一组测试。 例如：

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

一些测试工具，如 [NLU.DevOps](https://github.com/microsoft/NLU.DevOps) 还支持 LUDown 格式的测试文件。

#### <a name="designing-unit-tests"></a>设计单元测试

单元测试应设计为测试 LUIS 应用的核心功能。 在应用开发的每个迭代或冲刺 (sprint) 中，应编写足够多的测试，以验证在该迭代中实现的关键功能是否正常工作。

在每个单元测试中，对于给定的测试言语，可以：

* 测试是否返回了正确的意向
* 测试是否正在返回对解决方案至关重要的“密钥”实体。
* 测试意向和实体的[预测分数](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-prediction-score)是否超出了定义的阈值。 例如，可以决定仅在意向和关键实体的预测分数超过 0.75 时才认为测试已通过。

在单元测试中，最好测试是否已在预测响应中返回关键实体，但忽略任何误报。 误报是在预测响应中找到的实体，但未在测试的预期结果中定义。 忽略误报使得创作单元测试变得不太繁琐，同时仍允许你将精力集中在测试是否在预测响应中返回对解决方案至关重要的数据。

> [!TIP]
> [NLU.DevOps](https://github.com/microsoft/NLU.DevOps) 工具支持所有 LUIS 测试需求。 当在[单元测试模式](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode)下使用时，`compare` 命令将断言所有测试都通过，并将忽略未在预期结果中标记的实体的误报结果。

#### <a name="designing-batch-tests"></a>设计批处理测试

批处理测试集应包含大量测试用例，旨在跨 LUIS 应用中的所有意向和所有实体进行测试。 有关定义批处理测试集的信息，请参阅 [LUIS 门户中的批处理测试](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test)。

### <a name="running-tests"></a>运行测试

LUIS 门户提供的功能可帮助进行交互式测试：

* 通过[交互式测试](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test)，可以提交示例言语，并获取 LUIS 识别的意向和实体的响应。 通过视觉检测来验证测试是否成功。

* [批处理测试](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test)使用批处理测试文件作为输入来验证活动训练版本，以判断其预测准确性。 批处理测试可帮助你查看活动版本中每个意向和实体的准确性，并使用图表显示结果。

#### <a name="running-tests-in-an-automated-build-workflow"></a>在自动生成工作流中运行测试

LUIS 门户中的交互式测试功能非常有用，但对于 DevOps，在 CI/CD 工作流中执行的自动测试具有某些要求：

* 测试工具必须在生成服务器上的工作流步骤中运行。 这意味着工具必须能够在命令行上运行。
* 测试工具必须能够对终结点执行一组测试，并根据实际结果自动验证预期结果。
* 如果测试失败，则测试工具必须返回状态代码以暂停工作流并“让生成失败”。

LUIS 不提供命令行工具，可不提供可提供这些功能的高级 API。 建议使用 [NLU.DevOps](https://github.com/microsoft/NLU.DevOps) 工具，在命令行和 CI/CD 工作流中的自动测试期间运行测试和验证结果。

LUIS 门户中可用的测试功能不需要已发布的终结点，并且属于 LUIS 创作功能。 在自动生成工作流中实现测试时，必须将要测试的 LUIS 应用版本发布到到终结点，以便测试工具（如 NLU.DevOps）可以在测试过程中发送预测请求。

> [!TIP]
> * 如果要实现自己的测试解决方案并编写代码将测试言语发送到终结点，请记住，如果使用 LUIS 创作密钥，则允许的事务速率被限制为 5TPS。 限制发送速率，或改用预测密钥。
> * 将测试查询发送到终结点时，请记住在预测请求的查询字符串中使用 `log=false`。 这可确保测试言语不会被 LUIS 记录下来，并最终出现在 LUIS [主动学习](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances)功能提供的终结点言语评审列表中，从而被意外地添加到应用的训练言语。

#### <a name="running-unit-tests-at-the-command-line-and-in-cicd-workflows"></a>在命令行和 CI/CD 工作流中运行单元测试

可以使用 [NLU.DevOps](https://github.com/microsoft/NLU.DevOps) 包，在命令行运行测试：

* 使用 NLU.DevOps [测试命令](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md)将测试文件中的测试提交到终结点，并在文件中捕获实际的预测结果。
* 使用 NLU.DevOps [比较命令](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md)将实际结果与输入测试文件中定义的预期结果进行比较。 `compare` 命令生成 NUnit 测试输出，在通过使用 `--unit-test` 标志使用[单元测试模式](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#unit-test-mode)时，将断言所有测试都通过。

### <a name="running-batch-tests-at-the-command-line-and-in-cicd-workflows"></a>在命令行和 CI/CD 工作流中运行批处理测试

还可以使用 NLU.DevOps 包，用于在命令行运行批处理测试。

* 使用 NLU.DevOps [测试命令](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Test.md)将测试文件中的测试提交到终结点，并在文件中捕获实际的预测结果，与单元测试相同。
* 在[性能测试模式](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md#performance-test-mode)下使用 NLU.DevOps [比较命令](https://github.com/microsoft/NLU.DevOps/blob/master/docs/Analyze.md)衡量应用的性能，还可以将应用的性能与基线性能基准进行比较，例如最新提交到主版本或当前版本的结果。 在性能测试模式中，`compare` 命令生成 NUnit 测试输出，并以 JSON 格式生成[批处理测试结果](https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#batch-test)。

## <a name="luis-non-deterministic-training-and-the-effect-on-testing"></a>LUIS 非确定性训练和对测试的影响

LUIS 训练模型（例如意向）时，既需要正数据（为模型训练应用而提供的标记的训练言语），也需要负数据（不是模型用途有效示例的数据）。 在训练过程中，LUIS 将从为其他模型提供的所有正数据生成一个模型的负数据，但在某些情况下，可能会导致数据不平衡。 为了避免这种不平衡，LUIS 以非确定性的方式为负数据的一个子集进行采样，进行优化以获得更平衡的训练集、提高模型性能并缩短训练时间。

这种非确定性训练的结果是，可能会[在不同的培训会话之间得到略微不同的预测响应](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-prediction-score)，通常用于[预测分数](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-prediction-score)不高的意向和/或实体。

如果要为出于测试目的而生成的 LUIS 应用版本禁用非确定性训练，请使用[版本设置 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings)，将 `UseAllTrainingData` 设置设为 `true`。

## <a name="next-steps"></a>后续步骤

* 了解如何[实现 CI/CD 工作流](luis-concept-devops-automation.md)
* 了解如何[通过 GitHub 为 LUIS 实现 DevOps](luis-how-to-devops-with-github.md)