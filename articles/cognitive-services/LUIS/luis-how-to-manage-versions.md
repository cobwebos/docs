---
title: 在 Azure 上的 LUIS 应用中管理版本 | Microsoft Docs
description: 了解如何在语言理解 (LUIS) 应用中管理版本。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/29/2017
ms.author: v-geberr
ms.openlocfilehash: 672f7991be0fc236e39daf7d1ce1d6080b31815b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365501"
---
# <a name="manage-versions"></a>管理版本

每次处理模型都会创建应用的不同[版本](luis-concept-version.md)。 

## <a name="set-active-version"></a>设置活动版本
若要处理版本，请在“我的应用”页上选择应用名称来打开应用，再选择顶栏中的“设置”。

![版本页](./media/luis-how-to-manage-versions/settings.png)

在“设置”页上，可以配置整个应用的设置，包括版本和协作者。 

## <a name="clone-a-version"></a>克隆版本
1. 在“设置”页上的“应用设置”和“协作者”部分后面，找到要克隆的版本所在的行。 选择最右边的三个点 (…)。 

    ![版本行属性](./media/luis-how-to-manage-versions/version-section.png)

2. 选择列表中的“克隆”。

    ![版本行属性选项](./media/luis-how-to-manage-versions/version-three-dots-modal.png)

3. 在“克隆版本”对话框中，键入新版本的名称，如“0.2”。

   ![“克隆版本”对话框](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
 > [!NOTE]
 > 版本 ID 可以仅包含字符、数字或“.”，长度不得超过 10 个字符。
 
 此时，指定名称的新版本已创建，且设置为活动版本。
 
  ![版本已创建且添加到列表](./media/luis-how-to-manage-versions/new-version.png)

 > [!NOTE]
 > 如上图所示，已发布版本与表明在哪种槽中发布的彩色标记相关联：生产（绿色）、过渡（红色）和两者（黑色）。 对于每个已发布版本，都会显示定型日期和发布日期。

## <a name="set-active-version"></a>设置活动版本
1. 在“设置”页上的“版本”列表中，选择最右侧的三个点 (…)。

2. 选择弹出列表中的“设置为活动”。

    ![设置活动版本](./media/luis-how-to-manage-versions/set-active-version.png)

    活动版本用浅蓝色突出显示，如下面的屏幕截图所示：

    ![活动版本](./media/luis-how-to-manage-versions/set-active-version-done.png) 


## <a name="import-version"></a>导入版本
可以从 JSON 文件导入版本。 导入后，新版本就会成为活动版本。

**若要导入版本，请执行以下操作：**

1. 在“设置”页上，选择“导入新版本”按钮。

    ![“导入新版本”按钮](./media/luis-how-to-manage-versions/import-version.png)

2. 依次选择“浏览”和 JSON 文件。

    ![“导入新版本”对话框](./media/luis-how-to-manage-versions/import-version-dialog.png)

仅当应用中已有 JSON 文件中的版本时，才需要设置版本 ID。

## <a name="export-version"></a>导出版本
可以将版本导出到 JSON 文件中。

**若要导出版本，请执行以下操作：**

1. 在“设置”页上的“版本”列表中，选择最右侧的三个点 (…)。

2. 依次选择弹出操作列表中的“导出”和所需的文件保存位置。

## <a name="delete-a-version"></a>删除版本
可以删除版本，但至少必须保留一个应用版本。 可以删除除活动版本之外的所有版本。 

1. 在“设置”页上的“版本”列表中，选择最右侧的三个点 (…)。

2. 依次选择弹出操作列表中的“删除”和所需的文件保存位置。

    ![确认删除版本](./media/luis-how-to-manage-versions/delete-menu.png) 


## <a name="rename-a-version"></a>重命名版本
只要版本名称尚未使用，即可重命名版本。  

1. 在“设置”页上的“版本”列表中，选择最右侧的三个点 (…)。

2. 选择弹出操作列表中的“重命名”。

3. 输入新版本名称，并选择“完成”。

    ![确认重命名版本](./media/luis-how-to-manage-versions/rename-popup.png) 
