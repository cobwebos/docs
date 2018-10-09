---
title: 管理 LUIS 应用中的版本
titleSuffix: Azure Cognitive Services
description: 版本允许你构建和发布不同的模型。 较好的做法是在对模型进行更改之前将当前的活动模型复制到一个不同的应用版本。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: db461191b70aabc322e570ecc814a076c21206f2
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033781"
---
# <a name="manage-versions"></a>管理版本

版本允许你构建和发布不同的模型。 较好的做法是在对模型进行更改之前将当前的活动模型复制到一个不同的应用[版本](luis-concept-version.md)。 

若要处理版本，请在“我的应用”页面上通过选择你的应用名称来将其打开，在顶栏中选择“管理”，然后在左侧导航栏中选择“版本”。 

版本列表显示哪些版本已发布，它们发布在何处，以及哪个版本当前处于活动状态。 

[![](./media/luis-how-to-manage-versions/versions-import.png "“管理”部分的“版本”页面")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>克隆版本

1. 选择要克隆的版本，然后从工具栏中选择“克隆”。 

2. 在“克隆版本”对话框中，键入新版本的名称，如“0.2”。

   ![“克隆版本”对话框](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > 版本 ID 可以仅包含字符、数字或“.”，长度不得超过 10 个字符。
 
 此时，指定名称的新版本已创建，且设置为活动版本。

## <a name="set-active-version"></a>设置活动版本

从列表中选择一个版本，然后从工具栏中选择“激活”。 

[![](./media/luis-how-to-manage-versions/versions-other.png "“管理”部分的“版本”页面")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>导入版本

1. 从工具栏中选择“导入版本”。 

2. 在“导入新版本”弹出窗口中，输入新的由十个字符构成的版本名称。 仅当应用中已有 JSON 文件中的版本时，才需要设置版本 ID。

    ![“管理”部分的“版本”页面](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    导入后，新版本就会成为活动版本。

<a name = "export-version"></a>

## <a name="other-actions"></a>其他操作

* 若要**删除**某个版本，请从列表中选择一个版本，然后从工具栏中选择“删除”。 选择“确定”。 
* 若要**重命名**某个版本，请从列表中选择一个版本，然后从工具栏中选择“重命名”。 输入新名称并选择“完成”。 
* 若要**导出**某个版本，请从列表中选择一个版本，然后从工具栏中选择“导出应用”。 文件将下载到本地计算机。 

