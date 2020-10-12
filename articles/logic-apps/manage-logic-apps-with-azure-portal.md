---
title: 在 Azure 门户中管理逻辑应用
description: 使用 Azure 门户编辑、启用、禁用或删除逻辑应用。
services: logic-apps
ms.suite: integration
author: lauradolan
ms.author: ladolan
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 07/20/2020
ms.openlocfilehash: d50f577a7170982be004cc8957114f79675fbc6e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87078625"
---
# <a name="manage-logic-apps-in-the-azure-portal"></a>在 Azure 门户中管理逻辑应用

可以使用 [Azure 门户](https://portal.azure.com)或 [Visual Studio](manage-logic-apps-with-visual-studio.md) 管理逻辑应用。 本文介绍如何在 Azure 门户中编辑、禁用、启用或删除逻辑应用。 如果不熟悉 Azure 逻辑应用，请参阅[什么是 Azure 逻辑应用](logic-apps-overview.md)？

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 一个现有的逻辑应用。 若要了解如何在 Azure 门户中创建逻辑应用，请参阅[快速入门：使用 Azure 逻辑应用创建第一个工作流 - Azure 门户](quickstart-create-first-logic-app-workflow.md)。

<a name="find-logic-app"></a>

## <a name="find-your-logic-apps"></a>查找逻辑应用

若要找到并打开你的逻辑应用，请执行以下步骤：

1. 使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 搜索栏中输入 `logic apps`，然后选择“逻辑应用”。

   ![Azure 门户菜单的屏幕截图，在搜索栏中搜索和选择逻辑应用。](./media/manage-logic-apps-with-azure-portal/find-select-logic-apps.png)

1. 在“逻辑应用”页上，找到并选择要管理的逻辑应用。

   该逻辑应用的“概览”窗格打开后，可通过以下方式筛选“逻辑应用”页上显示的列表： 

   * 按名称搜索逻辑应用
   * 按订阅、资源组、位置和标记筛选逻辑应用
   * 按资源组、类型、订阅和位置对逻辑应用进行分组

## <a name="view-logic-app-properties"></a>查看逻辑应用属性

1. 在 Azure 门户中，[找到并打开你的逻辑应用](#find-logic-app)。

1. 在逻辑应用菜单中的“设置”下，选择“属性”。 

1. 在“属性”窗格中，可以查看并复制有关逻辑应用的以下信息：

   * **名称**
   * 资源 ID
   * **资源组**
   * **位置**
   * **类型** 
   * **订阅名称**
   * **订阅 ID**
   * **访问终结点**
   * **运行时传出 IP 地址**
   * **访问终结点 IP 地址**
   * **连接器传出 IP 地址**

## <a name="disable-or-enable-logic-apps"></a>禁用或启用逻辑应用

可以在 Azure 门户中启用或禁用[单个逻辑应用](#disable-enable-single-logic-app)，或者一次性启用或禁用[多个逻辑应用](#disable-or-enable-multiple-logic-apps)。 还可以[在 Visual Studio 中启用或禁用逻辑应用](manage-logic-apps-with-visual-studio.md#disable-or-enable-logic-app)。

禁用逻辑应用会对工作流实例和运行造成以下影响：

* 所有正在进行的以及挂起的运行将会继续，直到完成。 此过程可能需要一段时间，具体取决于这些运行的数量。

* 逻辑应用引擎不会创建或运行新的工作流实例。

* 下一次满足触发器的条件时，触发器不会触发。

* 触发器状态会记住逻辑应用的停止位置。 因此，如果重新启用逻辑应用，将会针对自上次运行以来未处理的所有项触发此触发器。

  若要阻止逻辑应用针对自上次运行以来未处理的项触发，请在重新启用逻辑应用之前清除触发器的状态：

  1. 在 Azure 门户中，[找到并打开你的逻辑应用](#find-logic-app)。

  1. 编辑逻辑应用触发器的任何部分。

  1. 保存所做更改。 此步骤会重置触发器的当前状态。

  1. [重新启用逻辑应用](#disable-enable-single-logic-app)。

<a name="disable-enable-single-logic-app"></a>

### <a name="disable-or-enable-single-logic-app"></a>禁用或启用单个逻辑应用

1. 在 Azure 门户中，[找到并打开你的逻辑应用](#find-logic-app)。

1. 在逻辑应用的菜单中，选择“概览”。 从以下选项中进行选择：

   * 在工具栏中选择“禁用”。

     ![逻辑应用工具栏的屏幕截图，显示 "禁用" 按钮已选中。](./media/manage-logic-apps-with-azure-portal/disable-single-logic-app.png)

     如果逻辑应用已禁用，则只会看到“启用”选项。

   * 在工具栏中选择“启用”。

     ![逻辑应用工具栏的屏幕截图，其中显示了 "启用" 按钮。](./media/manage-logic-apps-with-azure-portal/enable-single-logic-app.png)

     如果逻辑应用已启用，则只会看到“禁用”选项。 

   Azure 门户会在 Azure 主工具栏上显示一条确认操作是成功还是失败的通知。

   ![Azure 门户的屏幕截图，显示确认操作状态的通知](./media/manage-logic-apps-with-azure-portal/operation-confirmation-notification.png)

<a name="disable-or-enable-multiple-logic-apps"></a>

### <a name="disable-or-enable-multiple-logic-apps"></a>禁用或启用多个逻辑应用

1. 在 Azure 门户中，[找到要禁用或启用的逻辑应用](#find-logic-app)。

1. 若要检查某个逻辑应用当前是已启用还是已禁用，请在“逻辑应用”页上查看该逻辑应用的“状态”列。  

   !["Azure 门户逻辑应用" 页的屏幕截图，显示按状态列排序的逻辑应用列表。](./media/manage-logic-apps-with-azure-portal/view-logic-app-status.png)

   如果“状态”列不可见，请在“逻辑应用”工具栏上选择“试用预览版”。  

   !["Azure 门户逻辑应用" 页的屏幕截图，显示 "尝试预览" 按钮已选中。](./media/manage-logic-apps-with-azure-portal/select-try-preview.png)

1. 在复选框列中，选择要禁用或启用的逻辑应用。 在工具栏上选择“禁用”或“启用”。 

   ![Azure 门户逻辑应用 "页的屏幕截图，其中显示了多个逻辑应用的" 启用 "和" 禁用 "按钮。](./media/manage-logic-apps-with-azure-portal/enable-disable-multiple-logic-apps.png)

1. 确认框出现时，选择“是”以继续。

   Azure 门户会在 Azure 主工具栏上显示一条确认操作是成功还是失败的通知。

## <a name="delete-logic-apps"></a>删除逻辑应用

可以在 Azure 门户中[删除单个逻辑应用](#delete-single-logic-app)，或者[一次性删除多个逻辑应用](#delete-multiple-logic-apps)。 还可以[在 Visual Studio 中删除逻辑应用](manage-logic-apps-with-visual-studio.md#delete-your-logic-app)。

删除逻辑应用会对工作流实例造成以下影响：

* 所有正在进行的以及挂起的运行将会继续，直到完成。 此过程可能需要一段时间，具体取决于这些运行的数量。

* 逻辑应用引擎不会创建或运行新的工作流实例。

> [!NOTE]
> 如果删除并重新创建了子逻辑应用，则必须重新保存父逻辑应用。 重新创建的子应用将具有不同的元数据。
> 如果在重新创建父逻辑应用的子逻辑应用后未重新保存父逻辑应用，则对子逻辑应用的调用将失败，并出现“未授权”错误。 此行为适用于父子逻辑应用，例如，那些使用集成帐户中的项目或调用 Azure 函数的逻辑应用。

<a name="delete-single-logic-app"></a>

### <a name="delete-single-logic-app"></a>删除单个逻辑应用

1. 在 Azure 门户中，[找到并打开你的逻辑应用](#find-logic-app)。

1. 在逻辑应用的菜单中，选择“概览”。 在逻辑应用的工具栏上，选择“删除”。

   ![逻辑应用工具栏的屏幕截图，显示 "删除" 按钮已选中。](./media/manage-logic-apps-with-azure-portal/delete-single-logic-app.png)

1. 确认框出现时，输入逻辑应用的名称，然后选择“删除”。

   ![逻辑应用提示确认删除单个逻辑应用的屏幕截图。](./media/manage-logic-apps-with-azure-portal/delete-confirmation-single-logic-app.png)

   Azure 门户会在 Azure 主工具栏上显示一条确认操作是成功还是失败的通知。

<a name="delete-multiple-logic-apps"></a>

### <a name="delete-multiple-logic-apps"></a>删除多个逻辑应用

1. 在 Azure 门户中，[找到要删除的逻辑应用](#find-logic-app)。

1. 在复选框列中，选择要删除的逻辑应用。 在工具栏中选择“删除”。

   ![逻辑应用页的屏幕截图，其中显示了一个选定要删除的列表中的多个逻辑应用。](./media/manage-logic-apps-with-azure-portal/delete-multiple-logic-apps.png)

1. 确认框出现时，输入 `yes` 并选择“删除”。

   ![逻辑应用提示确认删除多个逻辑应用的屏幕截图。](./media/manage-logic-apps-with-azure-portal/delete-confirmation-multiple-logic-apps.png)

   Azure 门户会在 Azure 主工具栏上显示一条确认操作是成功还是失败的通知。

<a name="manage-logic-app-versions"></a>

## <a name="manage-logic-app-versions"></a>管理逻辑应用版本

可以使用 Azure 门户来控制逻辑应用的版本。 可以查找逻辑应用的版本历史记录，提升以前的版本。

<a name="find-version-history"></a>

### <a name="find-and-view-previous-versions"></a>查找和查看以前的版本

1. 在 Azure 门户中，[找到要管理的逻辑应用](#find-logic-app)。

1. 在逻辑应用菜单中的“开发工具”下，选择“版本”。 

   ![Azure 门户中逻辑应用的屏幕截图，显示 "开发工具" 下的 "版本" 页的选择。](./media/manage-logic-apps-with-azure-portal/logic-apps-menu-versions.png)

1. 从列表中选择要管理的逻辑应用的“版本”。 可以在搜索栏中输入“版本”标识符以筛选列表。

1. 在“历史记录版本”页上，将会看到以前版本的只读模式的详细信息。 可以在逻辑应用的“设计器”和“代码视图”模式之间进行选择。 

   ![逻辑应用历史记录版本页的屏幕截图，显示代码视图和设计器视图选项。](./media/manage-logic-apps-with-azure-portal/history-version.png)

<a name="promote-previous-versions"></a>

### <a name="promote-previous-versions"></a>提升以前的版本

1. 在逻辑应用的版本历史记录中，[找到并选择要提升的版本](#find-version-history)。

1. 在“历史记录版本”页上，选择“提升”。 

   ![逻辑应用的版本历史记录屏幕截图，显示用于升级以前版本的按钮。](./media/manage-logic-apps-with-azure-portal/promote-button.png)

1. 在打开的“逻辑应用设计器”页上，按需编辑所要提升的版本。 可以在“设计器”和“代码视图”模式之间进行切换。  还可以更新“参数”、“模板”和“连接器”。  

   ![逻辑应用设计器的屏幕截图，显示用于升级逻辑应用的以前版本的按钮。](./media/manage-logic-apps-with-azure-portal/promote-page.png)

1. 若要保存任何更新并完成以前版本的提升，请选择“保存”。 （或者，若要取消更改，请选择“放弃”。） 

   再次[查看逻辑应用的版本历史记录](#find-version-history)时，提升的版本会显示在列表顶部，并具有新的标识符。

## <a name="next-steps"></a>后续步骤

* [监视逻辑应用](monitor-logic-apps.md)
