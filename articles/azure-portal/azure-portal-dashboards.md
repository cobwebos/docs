---
title: 创建和共享 Azure 门户仪表板 | Microsoft 文档
description: 本文介绍如何创建、 自定义、 发布和共享在 Azure 门户中的仪表板。
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
ms.date: 07/01/2019
ms.author: kfollis
ms.openlocfilehash: 8dd1349ca9ab62484eb6693291e3b869ff079dc1
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537226"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>在 Azure 门户中创建和共享仪表板

仪表板提供一种方法，以便在你的云资源的 Azure 门户中创建和组织更有针对性的视图。 作为一个工作区，您可以快速启动的日常操作的任务并监视资源使用仪表板。  生成基于项目、 任务或用户角色，例如自定义仪表板。  在 Azure 门户提供的默认仪表板作为起始点。 可以编辑默认的仪表板、 创建和自定义其他仪表板，并发布和共享仪表板，以向其他用户提供。 本文介绍如何创建新的仪表板、 自定义界面，并发布和共享仪表板。

## <a name="create-a-new-dashboard"></a>创建新的仪表板

在此示例中，我们将创建新的专用仪表板，并分配一个名称。 请按照以下步骤来开始操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 选择**仪表板**从左侧边栏的上半部分。 默认视图可能已设置为仪表板中。
1. 选择 **+ 新仪表板**。

    ![默认的仪表板的屏幕截图](./media/azure-portal-dashboards/dashboard-new.png)

4. 此操作将打开**磁贴库**，你将从中选择磁贴，以及一个空网格，其中将排列磁贴。

    ![磁贴库和空网格的屏幕截图](./media/azure-portal-dashboards/dashboard-name.png)

5. 选择**我的仪表板**仪表板中的文本标签和输入将帮助你轻松识别自定义仪表板的名称。
1. 选择**完成自定义**在页头退出编辑模式。

现在，仪表板视图显示空仪表板。 选择要查看可供你的仪表板的仪表板名称旁边的下拉列表-列表可能包括的其他用户创建和共享仪表板。

## <a name="edit-a-dashboard"></a>编辑仪表板

现在，让我们编辑仪表板以添加、 调整大小和排列表示你的 Azure 资源的磁贴。

### <a name="add-tiles"></a>添加磁贴

若要添加到仪表板磁贴，请执行以下步骤：
1. 选择![编辑图标](./media/azure-portal-dashboards/dashboard-edit-icon.png)**编辑**页标头中。

    ![突出显示编辑的仪表板的屏幕截图](./media/azure-portal-dashboards/dashboard-edit.png)

2. 浏览**磁贴库**或使用搜索字段查找所需的磁贴。
1. 选择**添加**自动将该磁贴添加到仪表板使用的默认大小和位置。 或者，拖动到网格中该磁贴，并将它放置在要。

许多资源页面 （也称为"边栏选项卡"） 的命令栏中包括图钉图标。 如果您选择的图标，表示源页的磁贴固定到当前处于活动状态的仪表板。 此方法是将磁贴添加到你的仪表板的替代方法。

![使用图钉图标页命令栏的屏幕截图](./media/azure-portal-dashboards/dashboard-pin-blade.png)

> [!TIP]
> 如果使用多个组织时，将添加**组织标识**磁贴到仪表板可清晰显示服务的组织资源属于。
>
>
### <a name="resize-or-rearrange-tiles"></a>重设大小或重新排列磁贴
若要更改磁贴的大小或重新排列磁贴在仪表板上的，执行以下步骤：

1. 选择![编辑图标](./media/azure-portal-dashboards/dashboard-edit-icon.png)**编辑**页标头中。
1. 选择上下文菜单中的磁贴右上角。 然后，选择磁贴大小。 支持任意大小的磁贴右下角，可将该磁贴拖到所需的大小还包含一个"句柄"。

   ![包含磁贴大小菜单打开的仪表板的屏幕截图](./media/azure-portal-dashboards/dashboard-tile-resize.png)

3. 选择一个磁贴和网格排列您的仪表板上将其拖到新位置。

### <a name="additional-tile-configuration"></a>更多磁贴配置

一些磁贴可能需要更多的配置，以显示所需的信息。 例如，**度量值图表**磁贴必须设置为显示从指标**Azure Monitor**。 此外可以自定义磁贴数据重写仪表板的默认时间设置。

需要设置显示任何磁贴**配置磁贴**横幅之前自定义磁贴。 选择该标题中，然后执行操作所需的设置。

![需要配置的磁贴的屏幕截图](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Markdown 磁贴可以显示在仪表板上的自定义的静态内容。 这可能是基本说明、 图像、 一组超链接，或甚至联系信息。 有关使用 markdown 磁贴的详细信息，请参阅[使用自定义 markdown 磁贴](azure-portal-markdown-tile.md)。
>
>
### <a name="customize-tile-data"></a>自定义磁贴数据

在仪表板上的数据自动显示在过去 24 小时内的活动。 若要显示不同时间范围只是此磁贴，请执行以下步骤：

1. 选择![筛选器图标](./media/azure-portal-dashboards/dashboard-filter.png)中的磁贴或选择左上角的筛选器图标**自定义磁贴数据**从上下文菜单。

   ![磁贴上下文菜单的屏幕截图](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

2. 选择到复选框**重写级别的磁贴的仪表板时间设置**。

   ![配置磁贴时设置的对话框的屏幕截图](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

3. 选择要显示此磁贴的时间跨度。 可以选择从过去 30 分钟到过去 30 天内，也可以定义自定义范围。
1. 选择要显示的时间粒度。 可以显示任何位置从一分钟为增量为 1 个月。
1. 选择“应用”。 

## <a name="delete-a-tile"></a>删除磁贴

若要从仪表板删除磁贴，请按照下列步骤：

* 该磁贴右上角选择上下文菜单并选择**从仪表板中删除**。 或者，
* 选择![编辑图标](./media/azure-portal-dashboards/dashboard-edit-icon.png)**编辑**输入自定义模式。 将鼠标悬停在右上角的磁贴，然后选择![删除图标](./media/azure-portal-dashboards/dashboard-delete-icon.png)删除图标以从仪表板删除磁贴。

   ![显示如何从仪表板删除磁贴的屏幕截图](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>克隆仪表板

若要为新的仪表板作为模板使用现有的仪表板，请按照下列步骤：

1. 请确保仪表板视图显示了你想要复制的仪表板。
1. 在页眉中，选择![克隆图标](./media/azure-portal-dashboards/dashboard-clone.png)**克隆**。
1. 一份名为仪表板"的克隆*仪表板名称*"中的打开编辑模式。 使用本文中前面的步骤来重命名和自定义仪表板。

## <a name="publish-and-share-a-dashboard"></a>发布和共享仪表板

创建仪表板，时，默认情况下，这意味着您是唯一可以看到它的人为私有。 若要使仪表板提供给其他人，可以与其他用户共享它们。 首先，必须发布为 Azure 资源仪表板。 若要发布和共享自定义仪表板，请执行以下步骤：

1. 选择![共享图标](./media/azure-portal-dashboards/dashboard-share-icon.png)**共享**页标头中。 **共享 + 访问控制**窗体随即出现。
1. 验证显示正确的仪表板名称。
1. 选择**订阅名称**。 具有订阅的访问权限的用户可以使用共享的仪表板。 由 Azure 基于角色的访问控制决定对各个磁贴所表示的资源的访问。
1. 选择该复选框以将此仪表板发布到所选订阅的仪表板资源组。 或者，清除该复选框，并选择改为发布到现有的资源组。
1. 选择仪表板资源的位置。 我们建议你找到的其他资源的仪表板。 注意： 如果选择现有资源组中，则仪表板是与该资源组会自动放在一起。
1. 选择“发布”  。

   ![仪表板发布对话框的屏幕截图](./media/azure-portal-dashboards/dashboard-publish.png)

### <a name="set-access-control-on-a-shared-dashboard"></a>共享仪表板上设置访问控制

发布仪表板后，管理谁有权访问仪表板通过执行以下步骤：

1. 在中**共享 + 访问控制**窗格中，选择**管理用户**。

   ![仪表板共享 + 访问的屏幕截图控制对话框](./media/azure-portal-dashboards/dashboard-share-access-control.png)

2. **访问控制**页随即打开。 在此页上，可以查看其他人的访问级别，或添加新的角色分配。 当你添加角色分配时，授予对仪表板的权限。

> [!NOTE]
> 磁贴是在组织中的资源的代表视图。 通过基于角色的访问控制分配管理对资源的访问和权限继承到资源的订阅。 向仪表板的权限不会自动将权限分配给在仪表板上显示的资源中。 有关共享仪表板和资源的基于角色的访问控制权限的详细信息，请参阅[与基于角色的访问控制共享仪表板](azure-portal-dashboard-share-access.md)。

### <a name="open-a-shared-dashboard"></a>打开共享的仪表板

若要查找和打开共享的仪表板，请执行以下步骤：

1. 选择仪表板名称旁边的下拉列表。
1. 从列表中显示的仪表板中进行选择或**浏览所有仪表板**如果未列出你想要打开的仪表板。

   ![仪表板选择菜单的屏幕截图](./media/azure-portal-dashboards/dashboard-browse.png)

3. 在中**类型**字段中，选择**共享仪表板**。
1. 选择一个或多个订阅。 您还可以输入文本以按名称筛选仪表板。
1. 从共享的仪表板的列表中选择一个仪表板。

## <a name="delete-a-dashboard"></a>删除仪表板

若要永久删除私有或共享仪表板，请执行以下步骤：

1. 选择你想要从仪表板名称旁边的下拉列表中删除的仪表板。
1. 选择![删除图标](./media/azure-portal-dashboards/dashboard-delete-icon.png)**删除**页标头中。
1. 对于专用仪表板中，选择**确定**在确认对话框中删除仪表板。 对于共享的仪表板，在确认对话框中，选择复选框以确认不再将其他人可查看已发布的仪表板。 选择“确定”。 

   ![删除确认的屏幕截图](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>后续步骤

* [使用基于角色的访问控制共享仪表板](azure-portal-dashboard-share-access.md)
* [以编程方式创建 Azure 仪表板](azure-portal-dashboards-create-programmatically.md)
