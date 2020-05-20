---
title: 迭代应用设计 - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS 在反复的模型变更、陈述示例、发布以及从终结点查询收集信息等周期中，会取得最佳的学习成效。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: diberry
ms.openlocfilehash: 0545be9ebe067a62b398c6c89b79a8484f0b48d4
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683115"
---
# <a name="iterative-app-design-for-luis"></a>LUIS 的迭代应用设计

语言理解 (LUIS) 应用可以使用迭代进行学习并最高效地执行。 下面是典型的迭代周期：

* 创建新版本
* 编辑 LUIS 应用架构。 这包括：
    * 意向和示例言语
    * 实体
    * 功能
* 训练、测试和发布
    * 在预测终结点上进行主动学习测试
* 从终结点查询收集数据

![创作周期](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>生成 LUIS 架构

应用的架构定义用户的需求是什么（意图或意向），以及意向的哪些部分提供有助于确定答案的详细信息（称为实体）。   

应用架构必须特定于应用域，以此确定相关的单词和短语，并确定典型的单词顺序。

示例言语表示应用在运行时所需的用户输入，例如识别的语音或文本。

架构需要意向，并且应该包含实体。 

### <a name="example-schema-of-intents"></a>意向的示例架构

最常见的架构是使用意向组织的意向架构。 此类架构使用 LUIS 来确定用户的意图。

如果意向架构类型有助于 LUIS 确定用户的意图，则此类型可以包含实体。 例如，发货实体（作为意向的_机器学习功能_）有助于 LUIS 决定发货目的。

### <a name="example-schema-of-entities"></a>实体的示例架构

实体架构侧重于实体，是从用户言语中提取的数据。 例如，如果用户说“我想要订三份披萨”。 将会提取两个实体：“三份”和“披萨”。   这些实体有助于实现意图，即订餐。

在实体架构方面，言语的意图对于客户端应用程序而言不太重要。

组织实体架构的常用方法是将所有示例言语添加到 **None** 意向。

### <a name="example-of-a-mixed-schema"></a>混合架构的示例

最强大且成熟的架构是包含完整实体和特征的意向架构。 此架构可用作意向架构或实体架构的起点，并可以不断扩充以包含两者的概念，因为客户端应用程序需要这些信息片段。

## <a name="add-example-utterances-to-intents"></a>将示例言语添加到意向

LUIS 需要在每个**意向**中添加几个示例言语。 示例言语需要有足够的单词选择和单词顺序方面的变化，才能确定言语表达的意向。

> [!CAUTION]
> 请不要批量添加示例言语。 先添加 15 到 30 个具体的且有变化的示例。

需要为每个示例言语设计任何**需要提取的数据**，并将这些数据标记为**实体**。

|关键元素|目的|
|--|--|
|Intent|将用户言语**分类**成单一意图或操作。 示例包括 `BookFlight` 和 `GetWeather`。|
|实体|从言语中**提取**实现意图所需的数据。 示例包括旅行日期和时间以及地点。|

可以通过将言语分配到 **None** 意向，将 LUIS 应用设计为忽略与应用域不相关的言语。

## <a name="test-and-train-your-app"></a>测试并训练应用

在每个意向中添加 15 到 30 个不同的示例言语并标记所需的实体后，需要测试并[训练](luis-how-to-train.md) LUIS 应用。

## <a name="publish-to-a-prediction-endpoint"></a>发布到预测终结点

必须发布 LUIS 应用，使之出现在[预测终结点区域](luis-reference-regions.md)列表中。

## <a name="test-your-published-app"></a>测试已发布的应用

可以从 HTTPS 预测终结点测试已发布的 LUIS 应用。 通过预测终结点进行测试，LUIS 可以选择具有低置信度的任何[最谈话。](luis-how-to-review-endpoint-utterances.md)

## <a name="create-a-new-version-for-each-cycle"></a>为每个周期创建新版本

每个版本是 LUIS 应用的即时快照。 在对应用进行更改之前，请创建新版本。 恢复旧版本比尝试将意向和言语转移到以前的状态要更容易。

版本 ID 由字符、数字或“.”组成，且长度不得超过 10 个字符。

初始版本 (0.1) 是默认活动版本。

### <a name="begin-by-cloning-an-existing-version"></a>首先克隆现有版本

克隆现有版本，用作每个新版本的起点。 克隆版本后，新版本将成为**活动**版本。

### <a name="publishing-slots"></a>发布槽

可以发布到过渡槽和/或生产槽。 每个槽可以具有不同的版本或相同的版本。 这样，在将更改发布到可供机器人或其他 LUIS 调用应用使用的生产槽之前，可以方便地验证这些更改。

训练的版本在 LUIS 应用的[终结点](luis-glossary.md#endpoint)中不会自动可用。 要使其在 LUIS 应用终结点上可用，必须[发布](luis-how-to-publish-app.md)或重新发布版本。 可以发布到“过渡”和“生产”槽，从而提供两个在终结点中可用的应用版本。******** 如果需要更多应用版本在终结点上可用，应导出版本并将其重新导入到新的应用。 新的应用具有不同的应用 ID。

### <a name="import-and-export-a-version"></a>导入和导出版本

可以在应用级别导入版本。 该版本将成为活动版本，并使用应用文件的 `versionId` 属性中的版本 ID。 也可以在版本级别导入到现有应用。 新版本将成为活动版本。

也可以在应用或版本级别导出版本。 唯一的区别是应用级别导出的版本是当前活动版本，而在版本级别，可以在“[设置](luis-how-to-manage-versions.md)”页上选择任意要导出的版本****。

导出的文件**不**包含：

* 计算机学习信息，因为该应用程序在导入后重新训练
* 参与者信息

若要备份 LUIS 应用架构，请从 [LUIS 门户](https://www.luis.ai/applications)导出版本。

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>使用版本和参与者信息管理参与者更改

LUIS 通过提供 Azure 资源级权限来使用应用参与者的概念。 将此概念与版本控制相结合可以提供有针对性的协作。

使用以下方法来管理应用的参与者更改。

### <a name="manage-multiple-versions-inside-the-same-app"></a>在同一应用中管理多个版本

从每个作者的基本版本中开始[克隆](luis-how-to-manage-versions.md#clone-a-version)。

每个创建者对自己的应用版本进行更改。 创建者对模型感到满意后，将新版本导出到 JSON 文件中。

可以比较导出的应用、.json 或 .lu 文件的差异。 合并这些文件，创建一个新版本的文件。 更改 `versionId` 属性以指示新的合并版本。 将该版本导入原始应用。

通过此方法可获得一个活动版本、阶段版本和已发布版本的应用。 可以在[交互式测试窗格](luis-interactive-test.md)中将活动版本与发布版本（阶段或生产）的结果进行比较。

### <a name="manage-multiple-versions-as-apps"></a>将各版本作为应用进行管理

[导出](luis-how-to-manage-versions.md#export-version)基础版本。 各创建者导入该版本。 导入应用的创建者即为该版本的所有者。 修改应用后，导出该版本。

导出的应用为 JSON 格式的文件，可与导出的基础版进行比较，发现所做更改。 合并这些文件，创建一个新版本的 JSON 文件。 更改 JSON 中的 versionId 属性以表示新的合并版本****。 将该版本导入原始应用。

详细了解来自[协作者](luis-how-to-collaborate.md)的创作贡献。

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>评审终结点言语以开始新的迭代周期

完成一个迭代循环后，可以重复该过程。 开始[审阅预测终结点最谈话](luis-how-to-review-endpoint-utterances.md)LUIS 标记为低置信度。 检查这些言语中预测的意向是否正确，以及提取的实体是否正确且完整。 评审并接受更改后，评审列表应是空的。

## <a name="next-steps"></a>后续步骤

了解有关[协作](luis-concept-keys.md)的概念。
