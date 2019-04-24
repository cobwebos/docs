---
title: 什么是工作区和项目？ - 自定义翻译
titleSuffix: Azure Cognitive Services
description: 工作区是用于编写和构建自定义翻译系统的工作区域。 工作区可以包含多个项目、模型和文档。 项目是模型、文档和测试的包装器。 每个项目自动包含所有上传到该工作区的、具有正确语言对的文档。
services: cognitive-services
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: dc45847f3dc2e11af9b7cf566fdd6780ec2b9dac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60509573"
---
# <a name="what-is-a-custom-translator-workspace"></a>什么是自定义翻译工作区？

工作区是用于编写和构建自定义翻译系统的工作区域。 工作区可以包含多个项目、模型和文档。 在自定义翻译中完成的所有工作都位于特定的工作区中。

工作区是私密性的，专供你和你邀请加入该工作区的人员使用。 未受邀的人员无法访问你的工作区内容。 你可以邀请任意数量的人员加入你的工作区，并随时可以修改或删除其访问权限。 还可以创建新的工作区。 默认情况下，工作区不会包含其他工作区中的任何项目或文档。

## <a name="what-is-a-custom-translator-project"></a>什么是自定义翻译项目？

项目是模型、文档和测试的包装器。 每个项目自动包含所有上传到该工作区的、具有正确语言对的文档。 例如，如果你有一个英语到西班牙语翻译项目，以及一个西班牙语到英语翻译项目，则这两个项目将包含相同的文档。 每个项目有一个关联的 CategoryID，查询 [V3 API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) 进行翻译时，将使用此 ID。 CategoryID 是用于从使用自定义翻译构建的自定义系统获取翻译的参数。

## <a name="project-categories"></a>项目类别

类别标识项目的领域 – 要使用的术语和样式的区域。 请选择与文档最相关的类别。 在某些情况下，所选的类别会直接影响自定义翻译的行为。

我们有两组基线模型。 它们是“常规”和“技术”。 如果选择了“技术”类别，则将使用“技术”基线模型。 如果选择了任何其他类别，则将使用“常规”基线模型。 “技术”基线模型在技术领域表现良好，但如果用于翻译的句子不在技术领域内，则表现出较低的质量。 我们建议客户只有在句子严格属于技术领域时才选择“技术”类别。

在同一工作区中，可为不同类别中的相同语言对创建项目。 自定义翻译会阻止创建具有相同语言对和类别的重复项目。 将标签应用到项目可避免这种限制。 除非要为多个客户端构建翻译系统，否则请不要使用标签，因为将唯一标签添加到项目的操作会反映在项目的 CategoryID 中。

## <a name="project-labels"></a>项目标签

自定义翻译允许将项目标签分配到项目。 项目标签可以区分具有相同语言对和类别的多个项目。 最佳做法是，除非有必要，否则应避免使用项目标签。

项目标签用作 CategoryID 的一部分。 如果未设置项目标签或者为不同的项目设置相同的项目标签，则具有相同类别和不同语言对的项目将共享同一个 CategoryID。 这种方法比较有利，因为它允许你或你的客户在使用文本翻译 API 时切换语言，而无需考虑每个项目的 CategoryID 是否唯一。

例如，若要在技术领域启用从英语到法语以及从法语到英语的翻译，可以创建两个项目：一个是英语到法语翻译项目，另一个是法语到英语翻译项目。 为这两个项目指定相同的类别（“技术”），并将项目标签留空。 这两个项目的 CategoryID 将会匹配，因此，无需修改 CategoryID，即可查询英语和法语翻译的 API。

如果你是语言服务提供商，并想要使用保留相同类别和语言对的不同模型为多个客户提供服务，则明智的做法是使用项目标签来区分客户。

## <a name="next-steps"></a>后续步骤

- 阅读[训练和模型](training-and-model.md)了解如何有效构建翻译模型。
