---
title: 使用 Azure API 管理发布 API 版本 | Microsoft Docs
description: 遵循本教程的步骤了解如何在 API 管理中发布多个版本。
services: api-management
documentationcenter: ''
author: vladvino
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
ms.openlocfilehash: 1cbe63184578f7d1e72992577a11c58b9b83a002
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="publish-multiple-versions-of-your-api"></a>发布 API 的多个版本 

有时，让 API 的所有调用方都使用完全相同的版本是不切实际的。 如果调用方想要升级到更高的版本，他们会希望以一种易于理解的方法实现此目的。 在 Azure API 管理中使用**版本**即可实现此目的。 有关详细信息，请参阅[版本和修订](https://blogs.msdn.microsoft.com/apimanagement/2017/09/14/versions-revisions/)。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 将新版本添加到现有 API
> * 选择版本方案
> * 将版本添加到产品
> * 浏览开发人员门户以查看版本

![开发人员门户上显示的版本](media/api-management-getstarted-publish-versions/azure_portal.PNG)

## <a name="prerequisites"></a>先决条件

* 完成以下快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)。
* 此外，请完成以下教程：[导入并发布第一个 API](import-and-publish.md)。

## <a name="add-a-new-version"></a>添加新版本

![API 上下文菜单 - 添加版本](media/api-management-getstarted-publish-versions/AddVersionMenu.png)

1. 从 API 列表中选择“会议 API”。
2. 选择它旁边的上下文菜单 (**...**)。
3. 选择“+ 添加版本”。

    > [!TIP]
    > 也可以在首次创建新 API 时启用版本 - 在“添加 API”屏幕上选择“对此 API 进行版本控制?”。

## <a name="choose-a-versioning-scheme"></a>选择版本控制方案

在 Azure API 管理中，可以选择调用方指定其所需 API 版本的允许方式。 通过选择“版本控制方案”可指定要使用的 API 版本。 此方案可以是**路径、标头或查询字符串**。 在下面的示例中，路径用于选择版本控制方案。

![“添加版本”屏幕](media/api-management-getstarted-publish-versions/AddVersion.PNG)

1. 保留选择“路径”作为**版本控制方案**。
2. 添加“v1”作为**版本标识符**。

    > [!TIP]
    > 如果选择“标头”或“查询字符串”作为版本控制方案，需要提供附加的值 - 标头或查询字符串参数的名称。

3. 根据需要提供说明。
4. 选择“创建”设置新版本。
5. 在“API 列表”中的“大型会议 API”下面，现在会出现两个不同的 API -“原始”和“v1”。

    ![Azure 门户中 API 下面列出的版本](media/api-management-getstarted-publish-versions/VersionList.PNG)

    > [!Note]
    > 如果为不受版本控制的 API 添加了版本，则会自动创建“原始”API - 这是对默认 URL 的响应。 这可以确保所有现有调用方不会被添加版本的过程中断。 如果在一开始就启用了版本的情况下创建新 API，则不会创建“原始”API。

6. 现在，可以编辑和配置“v1”使其作为与“原始”API 不同的独立 API。 对一个版本进行更改不会影响另一个版本。

## <a name="add-the-version-to-a-product"></a>将版本添加到产品

要使调用方看到新版本，必须将该版本添加到**产品**。

1. 从经典部署模型页选择“产品”。
2. 选择“不受限制”。
3. 选择“API”。
4. 选择 **添加** 。
5. 选择“会议 API，版本 v1”。
6. 导航到服务管理页并选择“API”。

## <a name="browse-the-developer-portal-to-see-the-version"></a>浏览开发人员门户以查看版本

1. 在顶部菜单中选择“开发人员门户”。
2. 选择“API”，请注意，“会议 API”显示了“原始”和“v1”版本。
3. 选择“v1”。
4. 请注意列表中第一个操作的“请求 URL”。 其中显示 API URL 路径包含“v1”。

    ![API 上下文菜单 - 添加版本](media/api-management-getstarted-publish-versions/developer_portal.png)

## <a name="next-steps"></a>后续步骤

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 将新版本添加到现有 API
> * 选择版本方案 
> * 将版本添加到产品
> * 浏览开发人员门户以查看版本

转到下一教程：

> [!div class="nextstepaction"]
> [升级和缩放](upgrade-and-scale.md)