---
title: 在 Azure 门户中创建和共享仪表板
description: 本文介绍如何在 Azure 门户中创建、自定义、发布和共享仪表板。
services: azure-portal
documentationcenter: ''
author: sewatson
manager: mtillman
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/29/2020
ms.author: mblythe
ms.openlocfilehash: fee3d9f0ef4f2c622d42373f4ed8c895f2c76adf
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901057"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>在 Azure 门户中创建和共享仪表板

仪表板是 Azure 门户中的云资源的集中、有组织的视图。 将仪表板用作工作区，可在其中快速启动日常操作的任务并监视资源。 例如，基于项目、任务或用户角色生成自定义仪表板。

Azure 门户提供作为起点的默认仪表板。 你可以编辑默认仪表板。 创建并自定义更多仪表板，并发布和共享仪表板，使其可供其他用户使用。 本文介绍如何创建新的仪表板、自定义界面以及发布和共享仪表板。

## <a name="create-a-new-dashboard"></a>创建新的仪表板

在此示例中，我们将创建一个新的私有仪表板并分配一个名称。 执行以下步骤以便开始：

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 从 "Azure 门户" 菜单中，选择 "**仪表板**"。 默认视图可能已设置为 "仪表板"。

    ![打开仪表板](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. 选择 "**新建仪表板**"。

    ![默认仪表板的屏幕截图](./media/azure-portal-dashboards/create-new-dashboard.png)

    此操作将打开 "**磁贴库**"，您将从中选择磁贴，并在其中排列磁贴的空网格。

    ![磁贴库和空网格的屏幕截图](./media/azure-portal-dashboards/dashboard-name.png)

1. 选择 "仪表板" 标签中的 "**我的仪表板**" 文本，并输入将帮助你轻松识别自定义仪表板的名称。

1. 在页眉中选择 "**完成自定义**" 退出编辑模式。

此时，仪表板视图将显示空仪表板。 选择仪表板名称旁的箭头，以查看可供你使用的仪表板。 此列表可能包括其他用户已创建和共享的仪表板。

## <a name="edit-a-dashboard"></a>编辑仪表板

现在，让我们编辑仪表板来添加、调整和排列代表 Azure 资源的磁贴。

### <a name="add-tiles"></a>添加磁贴

若要将磁贴添加到仪表板，请执行以下步骤：

1. 从页眉](./media/azure-portal-dashboards/dashboard-edit-icon.png) "**编辑**" !["编辑" 图标。

    ![面板突出显示编辑的屏幕截图](./media/azure-portal-dashboards/dashboard-edit.png)

1. 浏览**磁贴库**，或使用搜索字段查找所需的磁贴。

1. 选择 "**添加**" 以将磁贴添加到具有默认大小和位置的仪表板。 或者将磁贴拖动到网格，并将其放置在所需的位置。

许多资源页在命令栏中都包含图钉图标。 如果选择该图标，表示源页的磁贴将固定到当前处于活动状态的仪表板。 此方法是将磁贴添加到仪表板的另一种方法。

![带有 pin 图标的页面命令栏的屏幕截图](./media/azure-portal-dashboards/dashboard-pin-blade.png)

> [!TIP]
> 如果你使用多个组织，请将**组织标识**磁贴添加到仪表板，以清楚地显示资源所属的组织。
>
>

### <a name="resize-or-rearrange-tiles"></a>调整或重新排列磁贴

若要更改磁贴的大小或在仪表板上重新排列磁贴，请执行以下步骤：

1. 从页眉](./media/azure-portal-dashboards/dashboard-edit-icon.png) "**编辑**" !["编辑" 图标。

1. 选择磁贴右上角的上下文菜单。 然后，选择磁贴大小。 支持任何大小的磁贴也包括右下角的 "句柄"，可让你将磁贴拖动到所需大小。

    ![打开磁贴大小菜单的仪表板的屏幕截图](./media/azure-portal-dashboards/dashboard-tile-resize.png)

1. 选择磁贴，并将其拖动到网格上的新位置以排列仪表板。

### <a name="additional-tile-configuration"></a>其他磁贴配置

某些磁贴可能需要更多配置才能显示所需的信息。 例如，必须设置**度量值图表**磁贴，以显示**Azure Monitor**的指标。 你还可以自定义磁贴数据以替代仪表板的默认时间设置。

需要设置的任何磁贴会显示 "**配置" 磁贴**横幅，直到您自定义磁贴。 选择该横幅，然后执行所需的设置。

![需要配置的磁贴的屏幕截图](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> 通过 markdown 磁贴，你可以在仪表板上显示自定义的静态内容。 这可能是基本说明、图像、超链接集，甚至是联系信息。 有关使用 markdown 磁贴的详细信息，请参阅[使用 Azure 仪表板上的 markdown 磁贴显示自定义内容](azure-portal-markdown-tile.md)。
>
>
### <a name="customize-tile-data"></a>自定义磁贴数据

仪表板上的数据自动显示过去24小时内的活动。 若要为此磁贴显示不同的时间范围，请执行以下步骤：

1. 选择上下文菜单中的 "**自定义磁贴数据**" 或从磁贴的左上角](./media/azure-portal-dashboards/dashboard-filter.png) 筛选器的 "![筛选器" 图标。

    ![磁贴上下文菜单的屏幕截图](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. 选中此复选框可**在磁贴级别覆盖仪表板时间设置**。

    ![用于配置磁贴时间设置的对话框的屏幕截图](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. 选择要为此磁贴显示的时间跨度。 可以选择过去30分钟到过去30天的时间，也可以定义自定义范围。

1. 选择要显示的时间粒度。 可以将从1分钟开始到1个月的任何位置显示。

1. 选择“应用”。

## <a name="delete-a-tile"></a>删除磁贴

若要从仪表板中删除磁贴，请执行以下步骤：

* 选择磁贴右上角的上下文菜单，然后选择 "**从仪表板中删除**"。 或者，

* 选择 ![编辑 "图标](./media/azure-portal-dashboards/dashboard-edit-icon.png)"**编辑**"以进入自定义模式。 将鼠标悬停在磁贴的右上角，然后选择](./media/azure-portal-dashboards/dashboard-delete-icon.png) "删除" 图标 !["删除" 图标，从仪表板中删除该磁贴。

   ![显示如何从仪表板删除磁贴的屏幕截图](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>克隆仪表板

若要使用现有仪表板作为新仪表板的模板，请执行以下步骤：

1. 请确保仪表板视图显示要复制的仪表板。

1. 在页眉中，选择](./media/azure-portal-dashboards/dashboard-clone.png) **clone**![克隆图标。

1. 仪表板*名称*的名称为 "**克隆**" 的仪表板副本将在编辑模式下打开。 使用本文中的上述步骤重命名和自定义仪表板。

## <a name="publish-and-share-a-dashboard"></a>发布和共享仪表板

创建仪表板时，默认情况下它是专用的，这意味着你是唯一可以看到它的人。 若要使仪表板可供他人使用，你可以与其他用户共享。 首先，必须将仪表板作为 Azure 资源发布。 若要发布和共享自定义仪表板，请执行以下步骤：

1. 从页眉](./media/azure-portal-dashboards/dashboard-share-icon.png) "**共享**" 中选择 ![共享图标。 **共享 + 访问控制**将打开。

1. 验证是否显示了正确的仪表板名称。

1. 选择**订阅名称**。 有权访问订阅的用户可以使用共享仪表板。 对各个磁贴所代表的资源的访问权限由 Azure 基于角色的访问控制决定。

1. 选中此复选框将此仪表板发布到所选订阅的 "仪表板" 资源组。 或者，清除该复选框，并选择发布到现有资源组。

1. 选择仪表板资源的位置。 建议你查找包含其他资源的仪表板。 如果从现有资源组中选择，则仪表板将自动与该资源组定位。

1. 选择“发布”。

    ![仪表板发布对话框的屏幕截图](./media/azure-portal-dashboards/dashboard-publish.png)

### <a name="set-access-control-on-a-shared-dashboard"></a>在共享仪表板上设置访问控制

发布仪表板后，按以下步骤管理有权访问仪表板的人员：

1. 在 "**共享 + 访问控制**" 窗格中，选择 "**管理用户**"。

    !["仪表板共享 + 访问控制" 对话框的屏幕截图](./media/azure-portal-dashboards/dashboard-share-access-control.png)

1. "**访问控制**" 页将打开。 您可以查看某位用户的访问级别或添加新的角色分配。 在此处添加角色分配时，将授予对该仪表板的权限。

> [!NOTE]
> 磁贴是组织中资源的代表视图。 可以通过基于角色的访问控制分配来管理对资源的访问，并且权限会从订阅向下继承到资源。 向仪表板提供访问权限并不会自动为仪表板上显示的资源分配权限。 有关共享仪表板的权限和资源的基于角色的访问控制的详细信息，请参阅[使用基于角色的访问控制来共享 Azure 仪表板](azure-portal-dashboard-share-access.md)。

### <a name="open-a-shared-dashboard"></a>打开共享仪表板

若要查找并打开共享仪表板，请执行以下步骤：

1. 选择仪表板名称旁的箭头。

1. 如果要打开的仪表板未列出，请从显示的仪表板列表中选择或**浏览所有仪表板**。

    ![面板选择菜单的屏幕截图](./media/azure-portal-dashboards/dashboard-browse.png)

1. 在 "**类型**" 字段中，选择 "**共享仪表板**"。

1. 选择一个或多个订阅。 还可以按名称输入文本来筛选仪表板。

1. 从共享仪表板列表中选择一个仪表板。

## <a name="delete-a-dashboard"></a>删除仪表板

若要永久删除专用或共享仪表板，请执行以下步骤：

1. 从仪表板名称旁的列表中选择要删除的仪表板。

1. 从页眉](./media/azure-portal-dashboards/dashboard-delete-icon.png) "**删除**" !["删除" 图标。

1. 对于私有仪表板，请在确认对话框上选择 **"确定"** 以删除仪表板。 对于共享仪表板，请在 "确认" 对话框中选中相应的复选框以确认其他人将不再能查看已发布的仪表板。 选择“确定”。

    ![删除确认的屏幕截图](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>后续步骤

* [使用基于角色的访问控制来共享 Azure 仪表板](azure-portal-dashboard-share-access.md)
* [以编程方式创建 Azure 仪表板](azure-portal-dashboards-create-programmatically.md)
