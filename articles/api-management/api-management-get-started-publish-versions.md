---
title: "使用 Azure API 管理发布 API 版本 | Microsoft Docs"
description: "遵循本教程的步骤了解如何在 API 管理中发布多个版本。"
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
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 7c355e2feb5ebe5971d8391b326422a1abec1497
ms.contentlocale: zh-cn
ms.lasthandoff: 09/14/2017

---

# <a name="publish-multiple-versions-of-your-api-in-a-predictable-way"></a>以可预测的方式发布 API 的多个版本
本教程介绍如何设置 API 的版本，以及选择 API 开发人员调用这些版本的方式。

## <a name="prerequisites"></a>先决条件
若要完成本教程，需要创建一个 API 管理服务，并准备好一个可以更改的现有 API（取代“会议 API”）。

## <a name="about-versions"></a>关于版本
有时，让 API 的所有调用方都使用完全相同的版本是不切实际的。 有时，你希望将新的或不同的 API 功能发布给某些用户，还有一些人希望坚持使用目前他们所用的 API。 如果调用方想要升级到更高的版本，他们会希望以一种易于理解的方法实现此目的。  在 Azure API 管理中使用**版本**即可实现此目的。

## <a name="walkthrough"></a>演练
本演练在现有 API 中添加一个新版本，并选择版本控制方案和版本标识符。

## <a name="add-a-new-version"></a>添加新版本
![API 上下文菜单 - 添加版本](media/api-management-getstarted-publish-versions/AddVersionMenu.png)
1. 浏览到 Azure 门户上 API 管理服务中的“API”页。
2. 从 API 列表中选择“会议 API”，并选择它旁边的上下文菜单 (**...**)。
3. 选择“+ 添加版本”。

    > [!TIP]
    > 也可以在首次创建新 API 时启用版本 - 在“添加 API”屏幕上选择“对此 API 进行版本控制?”。

## <a name="choose-a-versioning-scheme"></a>选择版本控制方案
在 Azure API 管理中，可以选择调用方指定其所需 API 版本的允许方式。 为此，可以选择一种**版本控制方案**。 此方案可以是**路径、标头或查询字符串**。 本示例使用路径。
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
    > 如果你为版本不受控制的 API 添加了版本，我们始终会创建“原始”API - 这是对默认 URL 的响应。 这可以确保所有现有调用方不会被添加版本的过程中断。 如果在一开始就启用了版本的情况下创建新 API，则不会创建“原始”API。

6. 现在，可以编辑和配置“v1”（与“原始”API 完全不同的 API）。 对一个版本进行更改不会影响另一个版本。

## <a name="add-the-version-to-a-product"></a>将版本添加到产品
要让调用方看到新版本，必须将该版本添加到**产品**（产品不会从父版本继承）。

1. 在服务管理页中选择“产品”。
2. 选择“不受限制”。
3. 选择“API”。
4. 选择“添加”。
5. 选择“会议 API，版本 v1”。
6. 返回到服务管理页并选择“API”。

## <a name="browse-the-developer-portal-to-see-the-version"></a>浏览开发人员门户以查看版本
1. 在顶部菜单中选择“开发人员门户”。
2. 选择“API”，请注意，“会议 API”显示了“原始”和“v1”版本。
3. 选择“v1”。
4. 请注意列表中第一个操作的“请求 URL”。 其中显示 API URL 路径包含“v1”。
![开发人员门户上显示的版本](media/api-management-getstarted-publish-versions/VersionDevPortal.PNG)

