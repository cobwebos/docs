---
title: 在 Azure 门户中管理逻辑应用
description: 使用 Azure 门户编辑、启用、禁用或删除逻辑应用。
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 04/13/2020
ms.openlocfilehash: f726ca90c215c4aff3734bd8022bbc1ad4dc5f87
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415993"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>在 Azure 门户中管理逻辑应用

您可以使用[Azure 门户](https://portal.azure.com)或[可视化工作室](manage-logic-apps-with-visual-studio.md)管理逻辑应用。 本文演示如何在 Azure 门户中编辑、禁用、启用或删除逻辑应用。 如果您是 Azure 逻辑应用的新增功能，请参阅[什么是 Azure 逻辑应用](logic-apps-overview.md)？

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 现有逻辑应用。 要了解如何在 Azure 门户中创建逻辑应用，请参阅[快速入门：使用 Azure 逻辑应用 - Azure 门户创建第一个工作流](quickstart-create-first-logic-app-workflow.md)。

<a name="find-logic-app"></a>

## <a name="find-your-logic-apps"></a>查找逻辑应用

要查找和打开逻辑应用，请按照以下步骤操作：

1. 使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 搜索栏中`logic apps`，输入 ，然后选择**逻辑应用**。

   ![查找并选择“逻辑应用”](./media/manage-logic-apps-with-azure-portal/find-select-logic-apps.png)

1. 在 **"逻辑应用"** 页上，查找并选择要管理的逻辑应用。

   打开逻辑应用的 **"概述"** 窗格后，可以通过以下方式筛选 **"逻辑应用"** 页上显示的列表：

   * 按名称搜索逻辑应用
   * 按订阅、资源组、位置和标记筛选逻辑应用
   * 按资源组、类型、订阅和位置对逻辑应用进行分组

## <a name="view-logic-app-properties"></a>查看逻辑应用属性

1. 在 Azure 门户中，[查找并打开逻辑应用](#find-logic-app)。

1. 从逻辑应用的菜单中，**在"设置"** 下，选择 **"属性**"。

1. 在 **"属性"** 窗格中，您可以查看和复制有关逻辑应用的以下信息：

   * **名称**
   * **资源 ID**
   * **资源组**
   * **位置**
   * 类型  
   * **订阅名称**
   * **订阅 ID**
   * **访问终结点**
   * **运行时传出 IP 地址**
   * **访问终结点 IP 地址**
   * **连接器传出 IP 地址**

## <a name="disable-or-enable-logic-apps"></a>禁用或启用逻辑应用

您可以在 Azure 门户中同时启用或禁用[单个逻辑应用](#disable-enable-single-logic-app)或[多个逻辑应用](#disable-or-enable-multiple-logic-apps)。 您还可以在[Visual Studio 中启用或禁用逻辑应用](manage-logic-apps-with-visual-studio.md#disable-or-enable-logic-app)。

禁用逻辑应用会影响工作流实例，并按以下方式运行：

* 所有正在进行的和挂起的运行将继续，直到它们完成。 根据这些运行的数量，此过程可能需要一些时间。

* 逻辑应用引擎不会创建或运行新的工作流实例。

* 下次满足其条件时，触发器不会触发。

* 触发器状态记住逻辑应用停止的点。 因此，如果重新启用逻辑应用，触发器将触发自上次运行以来所有未处理的项目。

  要阻止逻辑应用自上次运行以来触发未处理的项目，请清除触发器的状态，然后再重新启用逻辑应用：

  1. 在 Azure 门户中，[查找并打开逻辑应用](#find-logic-app)。

  1. 编辑逻辑应用触发器的任何部分。

  1. 保存所做更改。 此步骤将重置触发器的当前状态。

  1. [重新启用逻辑应用](#disable-enable-single-logic-app)。

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-single-logic-app"></a>禁用或启用单个逻辑应用

1. 在 Azure 门户中，[查找并打开逻辑应用](#find-logic-app)。

1. 在逻辑应用的菜单中，选择 **"概述**"。 从以下选项中进行选择：

   * 在工具栏中选择“禁用”。****

     ![在 Azure 门户中禁用单个逻辑应用](./media/manage-logic-apps-with-azure-portal/disable-single-logic-app.png)

     如果逻辑应用已被禁用，则只能看到 **"启用**"选项。

   * 在工具栏中选择“启用”。****

     ![在 Azure 门户中启用单个逻辑应用](./media/manage-logic-apps-with-azure-portal/enable-single-logic-app.png)

     如果逻辑应用已启用，则只能看到 **"禁用**"选项。 

   Azure 门户在主 Azure 工具栏上显示一条通知，用于确认操作是成功还是失败。

   ![确认操作状态的通知](./media/manage-logic-apps-with-azure-portal/operation-confirmation-notification.png)

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>禁用或启用多个逻辑应用

1. 在 Azure 门户中，找到要禁用或启用[的逻辑应用](#find-logic-app)。

1. 要检查逻辑应用当前是启用还是禁用，请在 **"逻辑应用"** 页上查看该逻辑应用**的状态**列。 

   ![逻辑应用状态列](./media/manage-logic-apps-with-azure-portal/view-logic-app-status.png)

   如果 **"状态**"列不可见，请在 **"逻辑应用"** 工具栏上选择 **"尝试预览**"。

   ![打开预览](./media/manage-logic-apps-with-azure-portal/select-try-preview.png)

1. 在复选框列中，选择要禁用或启用的逻辑应用。 在工具栏上，选择 **"禁用**"或 **"启用**"。

   ![在 Azure 门户中启用或禁用多个逻辑应用](./media/manage-logic-apps-with-azure-portal/enable-disable-multiple-logic-apps.png)

1. 当确认框出现时，选择 **"是**"以继续。

   Azure 门户在主 Azure 工具栏上显示一条通知，用于确认操作是成功还是失败。

## <a name="delete-logic-apps"></a>删除逻辑应用

您可以在 Azure 门户中同时[删除单个逻辑应用](#delete-single-logic-app)或删除[多个逻辑应用](#delete-multiple-logic-apps)。 您也可以在[Visual Studio 中删除逻辑应用程序](manage-logic-apps-with-visual-studio.md#delete-your-logic-app)。

删除逻辑应用会以以下方式影响工作流实例：

* 所有正在进行的和挂起的运行将继续，直到它们完成。 根据这些运行的数量，此过程可能需要一些时间。

* 逻辑应用引擎不会创建或运行新的工作流实例。

<a name="delete-single-logic-app"></a>

### <a name="delete-single-logic-app"></a>删除单个逻辑应用

1. 在 Azure 门户中，[查找并打开逻辑应用](#find-logic-app)。

1. 在逻辑应用的菜单中，选择 **"概述**"。 在逻辑应用的工具栏上，选择 **"删除**"。

   ![在逻辑应用工具栏上，选择"删除"](./media/manage-logic-apps-with-azure-portal/delete-single-logic-app.png)

1. 当确认框出现时，输入逻辑应用的名称，然后选择 **"删除**"。

   ![确认删除逻辑应用](./media/manage-logic-apps-with-azure-portal/delete-confirmation-single-logic-app.png)

   Azure 门户在主 Azure 工具栏上显示一条通知，用于确认操作是成功还是失败。

<a name="delete-multiple-logic-apps"></a>

### <a name="delete-multiple-logic-apps"></a>删除多个逻辑应用

1. 在 Azure 门户[中，查找要删除 的逻辑应用](#find-logic-app)。

1. 在复选框列中，选择要删除的逻辑应用。 在工具栏中选择“删除”。****

   ![删除多个逻辑应用](./media/manage-logic-apps-with-azure-portal/delete-multiple-logic-apps.png)

1. 当确认框出现时，输入`yes`，然后选择 **"删除**"。

   ![确认删除逻辑应用](./media/manage-logic-apps-with-azure-portal/delete-confirmation-multiple-logic-apps.png)

   Azure 门户在主 Azure 工具栏上显示一条通知，用于确认操作是成功还是失败。

<a name="manage-logic-app-versions"></a>

## <a name="manage-logic-app-versions"></a>管理逻辑应用版本

可以使用 Azure 门户对逻辑应用进行版本控制。 您可以找到逻辑应用的版本历史记录，并升级以前的版本。

<a name="find-version-history"></a>

### <a name="find-and-view-previous-versions"></a>查找和查看早期版本

1. 在 Azure 门户中，[找到要管理的逻辑应用](#find-logic-app)。

1. 在逻辑应用的菜单中，在**开发工具**下，选择 **"版本**"。

   ![在逻辑应用的菜单上，选择"开发工具"下的"版本"](./media/manage-logic-apps-with-azure-portal/logic-apps-menu-versions.png)

1. 从列表中选择要管理的逻辑应用**的版本**。 您可以在搜索栏中输入 **"版本**"标识符以筛选列表。

1. 在 **"历史记录"版本**页上，您将在只读模式下看到以前版本的详细信息。 您可以在逻辑应用**设计器**和**代码视图**模式之间进行选择。

   ![具有代码视图和逻辑应用设计器视图的逻辑应用的历史记录版本页](./media/manage-logic-apps-with-azure-portal/history-version.png)

<a name="promote-previous-versions"></a>

### <a name="promote-previous-versions"></a>升级早期版本

1. 在逻辑应用的版本历史记录中，[查找并选择要升级的版本](#find-version-history)。

1. 在 **"历史记录版本**"页上，选择 **"升级**"。

   ![在逻辑应用的版本历史记录中推广按钮](./media/manage-logic-apps-with-azure-portal/promote-button.png)

1. 在打开**的逻辑应用设计器**页面上，根据需要编辑要升级的版本。 您可以在**设计器**和**代码视图**模式之间切换。 您还可以更新**参数**、**模板**和**连接器**。

   ![用于推广早期版本的逻辑应用设计器页面](./media/manage-logic-apps-with-azure-portal/promote-page.png)

1. 要保存任何更新并完成升级以前的版本，请选择"**保存**"。 （或者，要取消更改，请选择 **"丢弃**"） 

   当您再次[查看逻辑应用的版本历史记录](#find-version-history)时，升级的版本将显示在列表顶部，并具有新的标识符。

## <a name="next-steps"></a>后续步骤

* [监视逻辑应用](monitor-logic-apps.md)
