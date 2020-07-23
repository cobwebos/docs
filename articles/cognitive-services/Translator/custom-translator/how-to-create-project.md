---
title: 如何创建项目-自定义转换器
titleSuffix: Azure Cognitive Services
description: 本文介绍如何在 Azure 认知服务自定义转换器中创建和管理项目。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 03f6f045e0809f79117518a22abfd74520b6d0a6
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83992770"
---
# <a name="create-a-project"></a>创建一个项目

项目是模型、文档和测试的容器。 每个项目自动包含所有上传到该工作区的、具有正确语言对的文档。

创建项目是生成模型的第一步。

## <a name="create-a-project"></a>创建项目：

1.  在[自定义翻译](https://portal.customtranslator.azure.ai)门户中，单击“创建项目”。

    ![创建项目](media/how-to/how-to-create-project.png)

2.  在对话框中输入有关项目的以下详细信息：

    a.  项目名称（必需）：为项目提供唯一且有意义的名称。 不需要在标题中提到语言。

    b.  说明：有关项目的简短摘要。 此说明对自定义翻译的行为或生成的自定义系统没有影响，但可以帮助区分不同的项目。

    c.  语言对（必需）：选择翻译的源语言和目标语言。

    d.  类别（必需）：选择最适合项目的类别。 类别描述要翻译的文档的术语和样式。

    e.  类别说明：使用此字段可以更好地描述你所在的特定领域或行业。 例如，如果你的类别是“医疗”，则你可以添加特定的文档，例如“外科”或“儿科”。 此说明对自定义翻译的行为或生成的自定义系统没有影响。

    f.  项目标签：[项目标签](workspace-and-project.md#project-labels)可以区分具有相同语言对和类别的项目。 最佳做法是，仅当你打算为相同语言对和相同类别生成多个项目，并且想要使用不同的 CategoryID 访问这些项目时，才使用标签。** 如果只为一个类别生成系统，请不要使用此字段。 项目标签不是必需的，对区分不同的语言对没有帮助。 可对多个项目使用同一个标签。

    ![“创建项目”对话框](media/how-to/how-to-create-project-dialog.png)

3.  单击“法律条款” 

## <a name="view-project-details"></a>查看项目详细信息

自定义翻译登陆页显示工作区中的前 10 个项目。 其中会显示项目名称、语言对、类别、状态和 BLEU 评分。

选择某个项目后，会在项目页上看到以下信息：

- CategoryID：CategoryID 是通过连接 WorkspaceID、项目标签和类别代码创建的。 使用带有文本转换器的 "类别 Id" 来获取自定义翻译。

- 训练按钮：使用此按钮可以开始[训练模型](how-to-train-model.md)。

- 添加文档按钮：使用此按钮可以[上传文档](how-to-upload-document.md)。

- 筛选文档按钮：使用此按钮可以筛选和搜索特定的文档。

    ![查看项目详细信息](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>后续步骤

- 了解[如何搜索、编辑和删除项目](how-to-search-edit-delete-projects.md)。
- 了解[如何上传文档](how-to-upload-document.md)以生成翻译模型。
