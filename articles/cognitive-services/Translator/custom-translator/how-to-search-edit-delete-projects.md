---
title: 如何搜索、编辑和删除项目 - 自定义翻译
titleSuffix: Azure Cognitive Services
description: 自定义翻译提供了各种方法来高效地管理你的项目。 你可以创建多个项目，根据条件进行搜索，以及编辑项目。 还可以在自定义翻译中删除项目。
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 185febd18d773a07601fce4dfa8c4575855f84b0
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626720"
---
# <a name="search-edit-and-delete-projects"></a>搜索、编辑和删除项目

自定义翻译提供了多种方法来高效地管理你的项目。 你可以创建许多项目，根据条件进行搜索，以及编辑项目。 还可以在自定义翻译中删除项目。  

## <a name="search-and-filter-projects"></a>搜索和筛选项目

筛选工具可以根据不同的筛选条件搜索项目。 它可以根据项目的项目名称、状态、源和目标语言以及类别等进行筛选。

1.  单击筛选器按钮。

    ![搜索项目](media/how-to/how-to-search-project.png)

1.  可以根据下列任意（或所有）字段进行筛选：项目名称、状态、源语言、目标语言，以及类别。

2.  单击“应用”。

    ![搜索项目筛选器选项](media/how-to/how-to-search-project-filters.png)

3.  通过点击“清除”来清除筛选器以查看所有项目。


## <a name="edit-a-project"></a>编辑项目

使用自定义翻译，可以编辑项目的名称和说明。 其他项目元数据（例如类别、源语言、目标语言）不可编辑。 以下步骤介绍了如何编辑项目。

1.  单击将鼠标指针悬停在项目上时出现的铅笔图标。

    ![编辑项目](media/how-to/how-to-edit-project.png)

2.  在对话框中，可以修改项目名称或项目说明，但无法修改项目标签、类别或语言对。

    ![“编辑项目”对话框](media/how-to/how-to-edit-project-dialog.png)

3.  单击筛选器按钮。

## <a name="delete-a-project"></a>删除项目

不再需要某个项目时可以将其删除。 以下步骤介绍了如何删除项目。

1. 将鼠标指针悬停在任何项目记录上，并单击垃圾桶图标。

   ![删除项目](media/how-to/how-to-delete-project.png)

2. 确认删除。 删除某个项目将删除在该项目中创建的所有模型。 删除项目不会影响你的文档。

   ![删除确认对话框](media/how-to/how-to-delete-project-confirm.png)

## <a name="next-steps"></a>后续步骤

- [上传文档](how-to-upload-document.md)以开始生成自定义翻译模型。
