---
title: "在 Azure API 管理中使用修订更新 API | Microsoft Docs"
description: "遵循本教程中的步骤了解如何在 API 管理中使用修订进行非重大更改。"
services: api-management
documentationcenter: 
author: mattfarm
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2017
ms.author: apimpm
ms.translationtype: HT
ms.sourcegitcommit: 212b163c49fdd133b0ed2d99b33035fcc391aec6
ms.openlocfilehash: 0d67166a16ae4d640080ad83e7625e594b0dc246
ms.contentlocale: zh-cn
ms.lasthandoff: 09/21/2017

---

# <a name="make-non-breaking-changes-safely-using-revisions"></a>使用修订安全进行非重大更改
本教程介绍如何对 API 进行更改，并将更改情况告知开发人员。

## <a name="prerequisites"></a>先决条件
若要完成本教程，需要创建一个 API 管理服务，并准备好一个可以更改的现有 API（取代“会议 API”）。

## <a name="about-revisions"></a>关于修订
当 API 准备就绪并即将供开发人员使用时，我们通常需要谨慎地对该 API 进行更改 - 以免干扰 API 的调用方。 另一种有效的做法是让开发人员知道所做的更改。 可以在 Azure API 管理中使用**修订**实现此目的。

## <a name="walkthrough"></a>演练
本演练添加一个新的修订版，在其中添加一个操作，并将该修订版设为当前版本 - 到时会创建更改日志项目。

## <a name="add-a-new-revision"></a>添加新的修订版
1. 浏览到 Azure 门户上 API 管理服务中的“API”页。
2. 从 API 列表中选择“会议 API”，并从靠近页面顶部的菜单中选择“修订”选项卡。
3. 选择“+ 添加修订版”

    > [!TIP]
    > 也可以在 API 上的上下文菜单 (**...**) 中选择“添加修订版”。
![靠近屏幕顶部的“修订”菜单](media/api-management-getstarted-revise-api/TopMenu.PNG)

4. 提供新修订版的说明，帮助记住其用途。
5. 选择“创建”
6. 现已创建新的修订版。

    > [!NOTE]
    > 原始 API 保留在“修订版 1”中。 这是在你将其他修订版设为当前版本之前，用户继续调用的修订版。

## <a name="make-non-breaking-changes-to-your-revision"></a>对修订版进行非重大更改
1. 选择靠近屏幕顶部的“设计”选项卡。
2. 请注意，**修订版选择器**（紧靠在“设计”选项卡的上方）显示当前修订版为“修订版 2”。

    > [!TIP]
    > 使用修订版选择器可在要使用的修订版之间切换。

3. 选择“+ 添加操作”。
4. 将新操作设置为“POST”，将操作的“名称”和“显示名称”设置为“反馈”
5. **保存**新操作。
6. 现已对**修订版 2** 进行更改。 使用靠近页面顶部的**修订版选择器**切换回到**修订版 1**。
7. 请注意，新操作未出现在“修订版 1”中。 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>将修订版设为当前版本并添加更改日志项目
1. 在靠近页面顶部的菜单中选择“修订”选项卡。
![修订屏幕上的修订菜单。](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)
2. 打开“修订版 2”对应的上下文菜单 (**...**)。
3. 选择“设为当前版本”。
![将修订版设为当前版本并发布到更改日志](media/api-management-getstarted-revise-api/MakeCurrent.PNG)
4. 选择“发布到此 API 的公共更改日志”
5. 提供可让开发人员看到的更改说明，例如，“添加了新的反馈操作”。
6. **修订版 2** 现在是当前版本。

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>浏览开发人员门户以查看更改与更改日志
1. 在顶部菜单中选择“开发人员门户”。
2. 依次选择“API”、“会议 API”。
3. 请注意，新的“反馈”操作现已显示。
4. 选择 API 名称下面的“API 更改历史记录”。
5. 请注意，更改日志项目已显示在此列表中。
![开发人员门户上的更改日志](media/api-management-getstarted-revise-api/ChangeLogDevPortal.PNG)

## <a name="next-steps"></a>后续步骤
[使用 Azure API 管理发布 API 版本](#api-management-getstarted-publish-versions.md)
