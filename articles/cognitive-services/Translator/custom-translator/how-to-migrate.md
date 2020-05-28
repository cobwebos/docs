---
title: 迁移 Microsoft Translator Hub 工作区和项目 - 自定义翻译
titleSuffix: Azure Cognitive Services
description: 本文介绍如何将中心工作区和项目迁移到 Azure 认知服务自定义转换器。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: face605d756d2107c04b3df0c072602ac91d147d
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83992889"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>将 Hub 工作区和项目迁移到自定义翻译

可轻松地将 [Microsoft Translator Hub](https://hub.microsofttranslator.com/) 工作区和项目迁移到自定义翻译器。 从 Microsoft Hub 启动迁移，具体方式是选择一个工作区或项目，然后在自定义翻译器中选择一个工作区，并选择想要迁移的训练。 迁移开始后，将迁移所选的训练设置及所有相关文档。 将训练已部署的模型，并且可在训练完成后自动部署它们。

这些操作在迁移时执行：
* 所有文档和项目定义的名称都将通过添加 "hub_" 作为名称的前缀来传输。 自动生成的测试和优化数据将命名为 hub_systemtune_\<modelid> 或 hub_systemtest_\<modelid>。
* 进行迁移时，将自动使用 Hub 训练文档训练处于已部署状态的训练。 订阅无需为此训练付费。 如果为迁移选择了自动部署，则训练完成后将自动部署已训练的模型。 将收取常规托管费用。
* 如果迁移的训练未处于已部署状态，则会将它们置于迁移的草稿状态。 在此状态下，可以选择使用已迁移的定义训练模型，但需为常规训练费用付费。
* 在任何时候，都可以在 "MT 中心的 Bleu 评分" 标题中的模型的 TrainingDetails 页中找到从中心定型迁移的 BLEU 分数。

> [!Note] 
> 若要成功完成培训，自定义翻译人员需要至少10000个唯一的提取句子。 自定义转换程序不能执行比建议的[最小值](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment#suggested-minimum-number-of-sentences)更少的培训。

## <a name="find-custom-translator-workspace-id"></a>查找自定义翻译器工作区 ID

若要迁移 [Microsoft Translator Hub](https://hub.microsofttranslator.com/) 工作区，需要获取自定义翻译器中的目标工作区 ID。 自定义翻译中的目标工作区是所有 Hub 工作区和项目要迁移到的位置。

可以在自定义翻译器的“设置”页上找到目标工作区 ID：

1. 在自定义翻译门户中转到“设置”页。

2. 在“基本信息”部分可以找到工作区 ID。

    ![如何查找目标工作区 ID](media/how-to/how-to-find-destination-ws-id.png)

3. 请保留目标工作区 ID，以便在迁移过程中参考。

## <a name="migrate-a-project"></a>迁移项目

Microsoft Translator Hub 允许有选择性地迁移项目。

迁移项目：

1. 登录到 Microsoft Translator Hub。

2. 转到“项目”页。

3. 单击相应项目的“迁移”链接。

    ![如何从 Hub 迁移](media/how-to/how-to-migrate-from-hub.png)

4. 点击“迁移”链接后，将显示一个窗体，可以在其中执行下列操作：
   * 指定要迁移到的自定义翻译器上的工作区
   * 指示要迁移包含已成功训练的所有训练，还是仅迁移已部署的训练。 默认情况下，将迁移所有已成功的训练。
   * 指示训练完成后是否要自动部署训练。 默认情况下，训练完成后不会进行自动部署。

5. 单击“提交请求”。

## <a name="migrate-a-workspace"></a>迁移工作区

除迁移单个项目外，还可以迁移工作区内包含已成功训练的所有项目。 这种情况下，如同点击了“迁移”链接一样，会评估工作区内的每个项目。 此功能适用于具有许多项目且想要使用同样的设置将它们全部迁移到自定义翻译器的用户。 可以从 Translator Hub 的设置页面启动工作区迁移。

迁移工作区：

1. 登录到 Microsoft Translator Hub。

2. 转到“设置”页。

3. 在“设置”页上单击“将工作区数据迁移到自定义翻译”。

    ![如何从 Hub 迁移](media/how-to/how-to-migrate-workspace-from-hub.png)

4. 在下一页上，选择以下两个选项之一：

    a. 仅部署的训练：如果选择此选项，则只会已部署的系统和相关文档。

    b. 所有成功的训练：如果选择此选项，则会迁移所有已成功的训练和相关文档。

    c. 在自定义翻译中输入目标工作区 ID。

    ![如何从 Hub 迁移](media/how-to/how-to-migrate-from-hub-screen.png)

5. 单击“提交请求”。

## <a name="migration-history"></a>迁移历史记录

从中心请求工作区/项目迁移后，可在 "自定义转换器设置" 页中找到迁移历史记录。

若要查看迁移历史记录，请执行以下步骤：

1. 在自定义翻译门户中转到“设置”页。

2. 在“设置”页的“迁移历史记录”部分，单击“迁移历史记录”。

    ![迁移历史记录](media/how-to/how-to-migration-history.png)

“迁移历史记录”页显示所请求的每次迁移的以下摘要信息。

1. 迁移者：提交此迁移请求的用户的姓名和电子邮件

2. 迁移时间：迁移日期和时间戳

3. 项目数：请求迁移的项目数，以及成功迁移的项目数。

4. 训练数：请求迁移的训练数，以及成功迁移的训练数。

5. 文档数：请求迁移的文档数，以及成功迁移的文档数。

    ![迁移历史记录详细信息](media/how-to/how-to-migration-history-details.png)

如需有关项目、训练和文档的更详细迁移报告，可以选择以 CSV 格式导出详细信息。

## <a name="implementation-notes"></a>实现说明
* 在自定义转换程序中尚不可用的语言对的系统将仅可用于访问数据或通过自定义转换器取消部署。 这些项目将在 "项目" 页上标记为 "不可用"。 由于我们通过自定义转换器启用了新的语言对，因此这些项目将变为活动状态以进行定型和部署。 
* 将项目从 Hub 迁移到自定义翻译器不会对 Hub 训练或项目产生任何影响。 迁移期间，我们不会从 Hub 删除项目或文档，并且不会取消部署模型。
* 每个项目只可迁移一次。 如需重复执行对某个项目的迁移，请与我们联系。
* 自定义转换器支持 NMT 语言对和英语。 [查看支持的语言的完整列表](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization)。 Hub 无需基准模型，因此它支持数千种语言。 可以迁移不受支持的语言对，但我们将仅执行对文档和项目定义的迁移。 我们无法训练新模型。 此外，这些文档和项目将显示为非活动状态，以指示它们当前不可用。 若添加了对这些项目和/或文档的支持，它们将变为活动且可训练状态。
* 自定义翻译器当前不支持单语训练数据。 与不受支持的语言对一样，可以迁移单语文档，但在支持单语数据前，它们将显示为非活动状态。
* 自定义翻译器需要具备 1 万个并行句子，才能执行训练。 Microsoft Hub 可以针对小型数据集执行训练。 若要迁移的训练不符合此要求，则不会对它执行训练。

## <a name="custom-translator-versus-hub"></a>自定义翻译与 Hub

此表比较了 Microsoft Translator Hub 和自定义翻译器之间的功能。

|   | Hub | 自定义翻译 |
|:-----|:----:|:----:|
|自定义功能状态    | 正式版    | 正式版 |
| 文本 API 版本    | V2     | V3  |
| SMT 自定义    | 是    | 否 |
| NMT 自定义    | 否    | 是 |
| 新的统一语音服务自定义    | 否    | 是 |
| 无跟踪 | 是 | 是 |

## <a name="new-languages"></a>新语言

如果你是致力于创建 Microsoft Translator 的新语言系统的社区或组织，请访问以 [custommt@microsoft.com](mailto:custommt@microsoft.com) 获取详细信息。

## <a name="next-steps"></a>后续步骤

- [为模型定型](how-to-train-model.md)。
- 开始通过[转换器 V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)使用已部署的自定义翻译模型。
