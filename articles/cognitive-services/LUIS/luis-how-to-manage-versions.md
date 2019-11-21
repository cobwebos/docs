---
title: Manage versions - LUIS
titleSuffix: Azure Cognitive Services
description: 版本允许你构建和发布不同的模型。 较好的做法是在对模型进行更改之前将当前的活动模型复制到一个不同的应用版本。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 138b84a9b7f54782fd6254304a3fdcf4dba83182
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74221930"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>使用版本进行编辑和测试，而不会影响暂存应用或生产应用

版本允许你构建和发布不同的模型。 较好的做法是在对模型进行更改之前将当前的活动模型复制到一个不同的应用[版本](luis-concept-version.md)。 

若要处理版本，请在“我的应用”页面上通过选择你的应用名称来将其打开，在顶栏中选择“管理”，然后在左侧导航栏中选择“版本”。 

The list of versions shows which versions are published, where they are published, and which version is currently active. 

> [!div class="mx-imgBorder"]
> [![Manage section, versions page](./media/luis-how-to-manage-versions/versions-import.png "“管理”部分的“版本”页面")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>克隆版本

1. 选择要克隆的版本，然后从工具栏中选择“克隆”。 

2. 在“克隆版本”对话框中，键入新版本的名称，如“0.2”。

   ![“克隆版本”对话框](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > 版本 ID 可以仅包含字符、数字或“.”，长度不得超过 10 个字符。
 
   此时，指定名称的新版本已创建，且设置为活动版本。

## <a name="set-active-version"></a>设置活动版本

Select a version from the list, then select **Activate** from the toolbar. 

> [!div class="mx-imgBorder"]
> [![Manage section, versions page, make a version action](./media/luis-how-to-manage-versions/versions-other.png "Manage section, versions page, make a version action")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>导入版本

You can import a `.json` or a `.lu` version of your application.

1. Select **Import** from the toolbar, then select the format. 

2. 在“导入新版本”弹出窗口中，输入新的由十个字符构成的版本名称。 You only need to set a version ID if the version in the file already exists in the app.

    ![在“管理”部分的“版本”页，导入新版本](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    导入后，新版本就会成为活动版本。

### <a name="import-errors"></a>Import errors

* Tokenizer errors: If you get a **tokenizer error** when importing, you are trying to import a version that uses a different [tokenizer](luis-language-support.md#custom-tokenizer-versions) than the app currently uses. To fix this, see [Migrating between tokenizer versions](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>其他操作

* 若要**删除**某个版本，请从列表中选择一个版本，然后从工具栏中选择“删除”。 选择“确定”。 
* 若要**重命名**某个版本，请从列表中选择一个版本，然后从工具栏中选择“重命名”。 输入新名称并选择“完成”。 
* 若要**导出**某个版本，请从列表中选择一个版本，然后从工具栏中选择“导出应用”。 Choose JSON to export for backup, choose **Export for container** to [use this app in a LUIS container](luis-container-howto.md).  

