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
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 12a1f2304e4255eb9abd04ab2e2d0726066dd1e6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487762"
---
# <a name="authoring-cycles-and-versions"></a>创作周期和版本

你的 LUIS 应用程序的迭代周期最佳：

* 创建新版本
* 编辑应用架构
    * 示例最谈话
    * 实体
    * features
* 辆
* 测试
* 发布
    * 在预测终结点测试活动学习
* 从终结点查询收集数据

![创作周期](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>生成 LUIS 架构

应用架构的目的是定义用户正在请求的内容（意图或意向）以及问题的哪些部分提供有助于确定答案的详细信息（实体）。 

应用架构需要特定于应用域，才能确定相关的单词和短语以及典型的字词排序。 

示例最谈话表示应用程序在运行时应获取的用户输入。 

架构需要意向，并且_应具有_实体。 

### <a name="example-schema-of-intents"></a>意向架构示例

最常见的架构是使用意向组织的目的架构。 这种类型的架构依赖于 LUIS 来确定用户的意图。 

如果它有助于 LUIS 确定意图，则此架构类型可能具有实体。 例如，发货实体（作为意向的描述符）可帮助 LUIS 确定发货目的。 

### <a name="example-schema-of-entities"></a>实体的示例架构

实体架构侧重于实体，这些实体是要从最谈话中提取的数据。 

对于客户端应用程序而言，查询文本的目的更少或不重要。 

组织实体架构的一种常用方法是将所有示例最谈话添加到无意向。 

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

可以通过将查询文本分配给**None** ，来设计 LUIS 应用，以忽略与应用的域无关的最谈话。 

## <a name="test-and-train-your-app"></a>测试并训练应用

每个意向中有15到30个不同的示例最谈话，并标记所需的实体时，需要测试和[定型](luis-how-to-train.md)。 

## <a name="publish-to-a-prediction-endpoint"></a>发布到预测终结点

请确保发布应用，使其在所需的[预测终结点区域](luis-reference-regions.md)中可用。 

## <a name="test-your-published-app"></a>测试已发布的应用

可以从 HTTPS 预测终结点测试已发布的 LUIS 应用。 通过预测终结点进行测试，LUIS 可以选择具有低置信度的任何[最谈话。](luis-how-to-review-endpoint-utterances.md)  

## <a name="create-a-new-version-for-each-cycle"></a>为每个周期创建新版本

LUIS 中的版本与传统编程中的版本类似。 每个版本都是应用的即时快照。 在对应用进行更改之前，请创建新版本。 更容易返回到较旧版本，然后尝试删除方法并最谈话到以前的状态。

版本 ID 由字符、数字或“.”组成，且长度不得超过 10 个字符。

初始版本 (0.1) 是默认活动版本。 

### <a name="begin-by-cloning-an-existing-version"></a>首先克隆现有版本

克隆现有版本，作为新版本的起点。 克隆版本后，新版本将成为活动版本。 

### <a name="publishing-slots"></a>发布槽
你可以发布到阶段和生产槽。 每个槽可以具有不同的版本或相同的版本。 这对于在发布到生产环境之前验证更改非常有用，这些更改可供 bot 或其他 LUIS 调用应用程序使用。 

训练的版本在应用的[终结点](luis-glossary.md#endpoint)上不会自动提供。 为使版本在终结点中可用，必须[发布](luis-how-to-publish-app.md)或重新发布版本。 你可以发布到**过渡**和**生产环境**，提供终结点上可用的两个版本的应用。 如果需要更多版本的应用在终结点上可用，应导出版本并重新导入到新的应用。 新的应用具有不同的应用 ID。

### <a name="import-and-export-a-version"></a>导入和导出版本
可以在应用级别导入版本。 该版本将成为活动版本，并使用应用文件的 `versionId` 属性中的版本 ID。 你还可以在版本级别导入到现有应用。 新版本将成为活动版本。 

可以在应用或版本级别导出版本。 唯一的区别是应用级别导出的版本是当前活动版本，而在版本级别，可以在“**设置[”页上选择任意要导出的版本](luis-how-to-manage-versions.md)** 。 

导出的文件不包含：

* 计算机获知的信息，因为该应用程序在导入后重新训练
* 参与者信息

若要备份 LUIS 应用架构，请从 LUIS 门户导出版本。

## <a name="manage-contributor-changes-with-versions-and-apps"></a>管理版本和应用的参与者更改

LUIS 提供了应用程序参与者的概念，通过提供 Azure 资源级权限。 将此概念与版本控制结合，以提供目标协作。 

使用以下技术管理对应用的参与者更改。

### <a name="manage-multiple-versions-inside-the-same-app"></a>在同一应用中管理多个版本
首先，每个创建者基于基础版进行[克隆](luis-how-to-manage-versions.md#clone-a-version)。 

每个创建者对自己的应用版本进行更改。 得到满意的模型后，将新版本导出到 JSON 文件中。  

可对导出的应用、json 或 lu 文件进行比较以进行更改。 合并文件以创建新版本的单个文件。 更改**versionId**属性以表示新的合并版本。 将该版本导入原始应用。 

通过此方法可获得一个活动版本、阶段版本和已发布版本的应用。 可以在[交互式测试窗格](luis-interactive-test.md)中将活动版本与发布版本（阶段或生产）的结果进行比较。

### <a name="manage-multiple-versions-as-apps"></a>将各版本作为应用进行管理
[导出](luis-how-to-manage-versions.md#export-version)基础版本。 各创建者导入该版本。 导入应用的创建者即为该版本的所有者。 修改应用后，导出该版本。 

导出的应用为 JSON 格式的文件，可与导出的基础版进行比较，发现所做更改。 合并这些文件，创建一个新版本的 JSON 文件。 更改 JSON 中的 versionId 属性以表示新的合并版本。 将该版本导入原始应用。

详细了解来自[协作者](luis-how-to-collaborate.md)的创作贡献。

## <a name="review-endpoint-utterances-to-begin-the-new-authoring-cycle"></a>查看终结点最谈话以开始新的创作周期

结束一个创作周期后可以重新开始。 开始[审阅预测终结点最谈话](luis-how-to-review-endpoint-utterances.md)LUIS 标记为低置信度。 检查这些最谈话以查找正确的预测意向，并准确和完整地提取实体。 查看并接受更改后，审阅列表应为空。  

## <a name="next-steps"></a>后续步骤

了解有关[协作](luis-concept-keys.md)的概念。
