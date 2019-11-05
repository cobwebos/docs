---
title: 管理版本 - LUIS
titleSuffix: Azure Cognitive Services
description: 版本允许你构建和发布不同的模型。 较好的做法是在对模型进行更改之前将当前的活动模型复制到一个不同的应用版本。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: aa1cbd08bdf8d92653a8f30ae67ecd813e563999
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467511"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>使用版本进行编辑和测试，而不会影响暂存应用或生产应用

版本允许你构建和发布不同的模型。 较好的做法是在对模型进行更改之前将当前的活动模型复制到一个不同的应用[版本](luis-concept-version.md)。 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

若要处理版本，请在“我的应用”页面上通过选择你的应用名称来将其打开，在顶栏中选择“管理”，然后在左侧导航栏中选择“版本”。 

版本列表显示哪些版本已发布，它们发布在何处，以及哪个版本当前处于活动状态。 

[!["管理" 部分，"版本" 页](./media/luis-how-to-manage-versions/versions-import.png "“管理”部分的“版本”页面")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>克隆版本

1. 选择要克隆的版本，然后从工具栏中选择“克隆”。 

2. 在“克隆版本”对话框中，键入新版本的名称，如“0.2”。

   ![“克隆版本”对话框](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > 版本 ID 可以仅包含字符、数字或“.”，长度不得超过 10 个字符。
 
   此时，指定名称的新版本已创建，且设置为活动版本。

## <a name="set-active-version"></a>设置活动版本

从列表中选择一个版本，然后从工具栏中选择“激活”。 

[!["管理" 部分，"版本" 页，生成版本操作](./media/luis-how-to-manage-versions/versions-other.png ""管理" 部分，"版本" 页，生成版本操作")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>导入版本

1. 从工具栏中选择“导入版本”。 

2. 在“导入新版本”弹出窗口中，输入新的由十个字符构成的版本名称。 仅当应用中已有 JSON 文件中的版本时，才需要设置版本 ID。

    ![在“管理”部分的“版本”页，导入新版本](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    导入后，新版本就会成为活动版本。

### <a name="import-errors"></a>导入错误

* 标记器错误：如果在导入时收到**标记器错误**，则尝试导入的版本使用的[标记器](luis-language-support.md#custom-tokenizer-versions)不同于当前使用的应用。 若要解决此问题，请参阅[在 tokenizer 版本之间迁移](luis-language-support.md#migrating-between-tokenizer-versions)。

<a name = "export-version"></a>

## <a name="other-actions"></a>其他操作

* 若要**删除**某个版本，请从列表中选择一个版本，然后从工具栏中选择“删除”。 选择“确定”。 
* 若要**重命名**某个版本，请从列表中选择一个版本，然后从工具栏中选择“重命名”。 输入新名称并选择“完成”。 
* 若要**导出**某个版本，请从列表中选择一个版本，然后从工具栏中选择“导出应用”。 选择要导出以备份的 JSON，选择“为容器导出”以[在 LUIS 容器中使用此应用](luis-container-howto.md)。  

