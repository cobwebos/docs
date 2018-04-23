---
title: 在 Azure API 管理中使用修订安全做出非重大更改 | Microsoft Docs
description: 遵循本教程中的步骤了解如何在 API 管理中使用修订进行非重大更改。
services: api-management
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: b4812ea8d93e4bfb784370e3a3196a5d20e47519
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="use-revisions-to-make-non-breaking-changes-safely"></a>使用修订安全做出非重大更改
当 API 准备就绪并即将供开发人员使用时，我们通常需要谨慎地对该 API 进行更改，同时避免干扰 API 的调用方。 另一种有效的做法是让开发人员知道所做的更改。 可以在 Azure API 管理中使用**修订**实现此目的。 有关详细信息，请参阅[版本和修订](https://blogs.msdn.microsoft.com/apimanagement/2017/09/14/versions-revisions/)以及[使用 Azure API 管理进行 API 版本控制](https://blogs.msdn.microsoft.com/apimanagement/2017/09/13/api-versioning-with-azure-api-management/)。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 添加新的修订版
> * 对修订版进行非重大更改
> * 将修订版设为当前版本并添加更改日志项目
> * 浏览开发人员门户以查看更改与更改日志

![开发人员门户上的更改日志](media/api-management-getstarted-revise-api/azure_portal.PNG)

## <a name="prerequisites"></a>先决条件

+ 完成以下快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)。
+ 此外，请完成以下教程：[导入并发布第一个 API](import-and-publish.md)。

## <a name="add-a-new-revision"></a>添加新的修订版

1. 选择“API”页。
2. 从 API 列表中选择“会议 API”（或想要添加修订的其他 API）。
3. 在靠近页面顶部的菜单中单击“修订”选项卡。
4. 选择“+ 添加修订版”

    > [!TIP]
    > 也可以在 API 上的上下文菜单 (**...**) 中选择“添加修订版”。
    
    ![靠近屏幕顶部的“修订”菜单](media/api-management-getstarted-revise-api/TopMenu.PNG)

5. 提供新修订版的说明，帮助记住其用途。
6. 选择“创建”
7. 现已创建新的修订版。

    > [!NOTE]
    > 原始 API 保留在“修订版 1”中。 这是在将其他修订版设为当前版本之前，用户继续调用的修订版。

## <a name="make-non-breaking-changes-to-your-revision"></a>对修订版进行非重大更改

1. 从 API 列表中选择“会议 API”。
2. 选择靠近屏幕顶部的“设计”选项卡。
3. 请注意，**修订版选择器**（紧靠在“设计”选项卡的上方）显示当前修订版为“修订版 2”。

    > [!TIP]
    > 使用修订版选择器可在要使用的修订版之间切换。

4. 选择“+ 添加操作”。
5. 将新操作设置为“POST”，将操作的“名称”和“显示名称”设置为“测试”
6. **保存**新操作。
7. 现已对**修订版 2** 进行更改。 使用靠近页面顶部的**修订版选择器**切换回到**修订版 1**。
8. 请注意，新操作未出现在“修订版 1”中。 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>将修订版设为当前版本并添加更改日志项目
1. 在靠近页面顶部的菜单中选择“修订”选项卡。

    ![修订屏幕上的修订菜单。](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)
1. 打开“修订版 2”对应的上下文菜单 (**...**)。
2. 选择“设为当前版本”。 如果想要发布有关此更改的说明，请选中“发布有关此 API 的公共更改日志”。
3. 选择“发布到此 API 的公共更改日志”
4. 提供可让开发人员看到的更改说明，例如，“测试修订。添加了新的“测试”操作。
5. **修订版 2** 现在是当前版本。

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>浏览开发人员门户以查看更改与更改日志
1. 在 Azure 门户中，选择“API”
2. 在顶部菜单中选择“开发人员门户”。
3. 依次选择“API”、“会议 API”。
4. 请注意，新的“测试”操作现已显示。
5. 选择 API 名称下面的“API 更改历史记录”。
6. 请注意，更改日志项目已显示在此列表中。

    ![开发人员门户](media/api-management-getstarted-revise-api/developer_portal.PNG)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 添加新的修订版
> * 对修订版进行非重大更改
> * 将修订版设为当前版本并添加更改日志项目
> * 浏览开发人员门户以查看更改与更改日志

转到下一教程：

> [!div class="nextstepaction"]
> [发布 API 的多个版本](api-management-get-started-publish-versions.md)