---
title: 迭代应用设计-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS 在反复的模型变更、陈述示例、发布以及从终结点查询收集信息等周期中，会取得最佳的学习成效。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: c1c1b2df301634a435b610c395a1a58aa5573da3
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422603"
---
# <a name="iterative-app-design-for-luis"></a>LUIS 的迭代应用设计

语言理解（LUIS）应用程序学习并使用迭代最有效地执行。 下面是典型的迭代周期：

* 创建新版本
* 编辑 LUIS 应用架构。 这包括：
    * 示例最谈话
    * 实体
    * 功能
* 定型、测试和发布
    * 在预测终结点测试活动学习
* 从终结点查询收集数据

![创作周期](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>生成 LUIS 架构

应用的架构定义用户所要求的内容（_意图_或_意向_）和意向的哪些部分提供用于帮助确定答案的详细信息（称为_实体_）。 

应用程序架构必须特定于应用域，以确定相关的单词和短语，并决定典型的字词排序。 

示例最谈话表示应用程序在运行时需要的用户输入（如识别的语音或文本）。 

架构需要意向，并且_应具有_实体。 

### <a name="example-schema-of-intents"></a>意向架构示例

最常见的架构是使用意向组织的目的架构。 这种类型的架构使用 LUIS 来确定用户的意图。 

如果它有助于 LUIS 确定用户的意图，则意向架构类型可能具有实体。 例如，发货实体（作为意向的描述符）可帮助 LUIS 确定发货目的。 

### <a name="example-schema-of-entities"></a>实体的示例架构

实体架构侧重于实体，实体是从用户最谈话中提取的数据。 例如，如果用户说 "我想要订购三个 pizzas"。 将提取两个实体：_三个_和_pizzas_。 它们用于帮助满足目的，目的是进行订单。 

对于实体架构，查询文本的意图对客户端应用程序而言不太重要。 

组织实体架构的一种常用方法是将所有示例最谈话添加到**无**意向。 

### <a name="example-of-a-mixed-schema"></a>混合架构的示例

最强大且成熟的架构是一种具有各种实体和功能的意向架构。 此架构可以作为意向架构或实体架构开始，并扩大为包含两者的概念，因为客户端应用程序需要这些信息片段。 

## <a name="add-example-utterances-to-intents"></a>将最谈话示例添加到意向

LUIS 需要在每个**意向**中使用几个最谈话示例。 示例最谈话需要足够多的单词选择和字序，才能确定查询文本的用途。 

> [!CAUTION]
> 不要批量添加示例最谈话。 从15到30个具体的示例开始。 

查询文本的每个示例都需要具有**所需的任何数据，以**使用**实体**进行设计和标记。 

|Key 元素|目的|
|--|--|
|意向|将用户最谈话**分类**为单一意图或操作。 示例包括 `BookFlight` 和 `GetWeather`。|
|实体|从所需的查询文本**提取**数据以完成目的。 例如，旅行日期和时间以及位置。|

LUIS 应用可以通过将查询文本分配给**None** ，来忽略与应用的域无关的最谈话。

## <a name="test-and-train-your-app"></a>测试并训练应用

如果每个意向有15到30个不同的示例最谈话，并标记了所需的实体，则需要测试并[训练](luis-how-to-train.md)LUIS 应用。 

## <a name="publish-to-a-prediction-endpoint"></a>发布到预测终结点

必须发布 LUIS 应用，以使其可用于列表[预测终结点区域](luis-reference-regions.md)。

## <a name="test-your-published-app"></a>测试已发布的应用

可以从 HTTPS 预测终结点测试已发布的 LUIS 应用。 通过预测终结点进行测试，LUIS 可以选择具有低置信度的任何[最谈话。](luis-how-to-review-endpoint-utterances.md)  

## <a name="create-a-new-version-for-each-cycle"></a>为每个周期创建新版本

每个版本都是 LUIS 应用的时间快照。 在对应用进行更改之前，请创建新版本。 更容易返回到较旧的版本，而不是尝试删除意向，最谈话到先前的状态。

版本 ID 由字符、数字或“.”组成，且长度不得超过 10 个字符。

初始版本 (0.1) 是默认活动版本。 

### <a name="begin-by-cloning-an-existing-version"></a>首先克隆现有版本

克隆现有版本，作为每个新版本的起点。 克隆版本后，新版本将成为**活动**版本。 

### <a name="publishing-slots"></a>发布槽

你可以发布到 "阶段" 和/或 "生产" 槽。 每个槽可以具有不同的版本或相同的版本。 这对于在发布到生产环境之前验证更改非常有用，这些更改可供 bot 或其他 LUIS 调用应用使用。 

LUIS 应用的[终结点](luis-glossary.md#endpoint)上不会自动提供经过训练的版本。 要使其在 LUIS 应用终结点上可用，必须[发布](luis-how-to-publish-app.md)或重新发布版本。 你可以发布到**过渡**和**生产环境**，提供终结点上可用的两个版本的应用。 如果需要在终结点上提供更多版本的应用，则应导出版本，并将其重新导入到新应用。 新的应用具有不同的应用 ID。

### <a name="import-and-export-a-version"></a>导入和导出版本

可以在应用级别导入版本。 该版本将成为活动版本，并使用应用文件的 `versionId` 属性中的版本 ID。 你还可以在版本级别导入到现有应用。 新版本将成为活动版本。 

版本也可以在应用或版本级别导出。 唯一的区别是应用级别导出的版本是当前活动版本，而在版本级别，可以在“**设置[”页上选择任意要导出的版本](luis-how-to-manage-versions.md)** 。 

导出的文件**不**包含：

* 计算机获知的信息，因为该应用程序在导入后重新训练
* 参与者信息

若要备份 LUIS 应用架构，请从[LUIS 门户](https://www.luis.ai/applications)导出版本。

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>管理版本和参与者的参与者更改

LUIS 通过提供 Azure 资源级权限，将参与者的概念用于应用。 将此概念与版本控制结合，以提供目标协作。 

使用以下技术管理对应用的参与者更改。

### <a name="manage-multiple-versions-inside-the-same-app"></a>在同一应用中管理多个版本

从每个作者的基本版本中开始[克隆](luis-how-to-manage-versions.md#clone-a-version)。 

每个创建者对自己的应用版本进行更改。 如果对该模型感到满意，请将新版本导出到 JSON 文件。  

可对导出的应用、json 或 lu 文件进行比较以进行更改。 合并文件以创建新版本的单个文件。 更改 `versionId` 属性以表示新的合并版本。 将该版本导入原始应用。 

通过此方法可获得一个活动版本、阶段版本和已发布版本的应用。 可以在[交互式测试窗格](luis-interactive-test.md)中将活动版本与发布版本（阶段或生产）的结果进行比较。

### <a name="manage-multiple-versions-as-apps"></a>将各版本作为应用进行管理

[导出](luis-how-to-manage-versions.md#export-version)基础版本。 各创建者导入该版本。 导入应用的创建者即为该版本的所有者。 修改应用后，导出该版本。 

导出的应用为 JSON 格式的文件，可与导出的基础版进行比较，发现所做更改。 合并这些文件，创建一个新版本的 JSON 文件。 更改 JSON 中的 versionId 属性以表示新的合并版本。 将该版本导入原始应用。

详细了解来自[协作者](luis-how-to-collaborate.md)的创作贡献。

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>查看终结点最谈话以开始新的迭代周期

完成迭代循环后，可以重复该过程。 开始[审阅预测终结点最谈话](luis-how-to-review-endpoint-utterances.md)LUIS 标记为低置信度。 检查这些最谈话以查找正确的预测意向，并准确和完整地提取实体。 查看并接受更改后，审阅列表应为空。  

## <a name="next-steps"></a>后续步骤

了解有关[协作](luis-concept-keys.md)的概念。
