---
title: 如何创建项目？ - 自定义翻译
titleSuffix: Azure Cognitive Services
description: 如何在自定义翻译中创建项目？
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 4e5ac4386af55855c5240f89557feafd4a93adfb
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626703"
---
# <a name="create-a-project"></a>创建一个项目

项目是模型、文档和测试的容器。 每个项目自动包含所有上传到该工作区的、具有正确语言对的文档。 

创建项目是生成模型的第一步。 

## <a name="create-a-project"></a>创建项目：

1.  在[自定义翻译](https://portal.customtranslator.azure.ai)门户中，单击“创建项目”。

    ![创建项目](media/how-to/how-to-create-project.png)

2.  在对话框中输入有关项目的以下详细信息：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。  项目名称（必需）：为项目提供唯一且有意义的名称。 不需要在标题中提到语言。

    b.  说明：有关项目的简短摘要。 此说明对自定义翻译的行为或生成的自定义系统没有影响，但可以帮助区分不同的项目。

    c.  语言对（必需）：选择翻译的源语言和目标语言。

    d.  类别（必需）：选择最适合项目的类别。 类别描述要翻译的文档的术语和样式。

    e.  类别说明：使用此字段可以更好地描述你所在的特定领域或行业。 例如，如果你的类别是“医疗”，则你可以添加特定的文档，例如“外科”或“儿科”。 此说明对自定义翻译的行为或生成的自定义系统没有影响。

    f.  项目标签：[项目标签](workspace-and-project.md#project-labels)可以区分具有相同语言对和类别的项目。 最佳做法是，仅当你打算为相同语言对和相同类别生成多个项目，并且想要使用不同的 CategoryID 访问这些项目时，才使用标签。 如果只为一个类别生成系统，请不要使用此字段。 项目标签不是必需的，对区分不同的语言对没有帮助。 可对多个项目使用同一个标签。

    ![“创建项目”对话框](media/how-to/how-to-create-project-dialog.png)

3.  单击“法律条款” 

## <a name="view-project-details"></a>查看项目详细信息

自定义翻译登陆页显示工作区中的前 10 个项目。 其中会显示项目名称、语言对、类别、状态和 BLEU 评分。

选择某个项目后，会在项目页上看到以下信息：

- CategoryID：CategoryID 是通过连接 WorkspaceID、项目标签和类别代码创建的。 在文本翻译 API 中使用 CategoryID 可以获取自定义译文。

- 训练按钮：使用此按钮可以开始[训练模型](how-to-train-model.md)。

- 添加文档按钮：使用此按钮可以[上传文档](how-to-upload-document.md)。

- 筛选文档按钮：使用此按钮可以筛选和搜索特定的文档。

    ![查看项目详细信息](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>后续步骤

- 了解[如何搜索、编辑和删除项目](how-to-search-edit-delete-projects.md)。
- 了解[如何上传文档](how-to-upload-document.md)以生成翻译模型。
