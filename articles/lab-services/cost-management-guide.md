---
title: Azure 实验室服务的成本管理指南
description: 了解查看 Lab 服务成本的不同方法。
author: rbest
ms.author: rbest
ms.date: 08/16/2020
ms.topic: article
ms.openlocfilehash: 98e04ba6bb1310935c4893a3616dfd68c2e99a55
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88797626"
---
# <a name="cost-management-for-azure-lab-services"></a>Azure 实验室服务的成本管理

对于 Azure 实验室服务，成本管理可分为两个不同的区域：成本估算和成本分析。 在设置实验室时，会发生成本估算，以确保实验室的初始结构适合预期预算。 成本分析通常在月底发生，以确定下个月的必要操作。

## <a name="estimate-the-lab-costs"></a>估计实验室成本

每个实验室仪表板都有 **成本 & 计费** 部分，该部分对实验室将按月收费的内容进行大致估计。 成本估算用用户的最大数量和每小时的预估成本来汇总工时使用情况。 若要获取最准确的估算值，请设置实验室，包括 [计划](how-to-create-schedules.md)。 该仪表板将反映预估成本。 

此估计值可能不会显示所有可能的成本。 不包括几个资源：

- 模板准备成本。 创建模板所需的时间量可能会有很大差异。 运行模板的成本与每小时的总体实验室成本相同。 
- 任何 [共享图像库](how-to-use-shared-image-gallery.md) 成本，因为库可以在多个实验室之间共享。 
- 实验室创建者启动虚拟机 (VM) 时所发生的小时数。

> [!div class="mx-imgBorder"]
> ![显示仪表板成本估算的屏幕截图。](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyze-the-previous-months-usage"></a>分析上个月的使用情况

成本分析用于查看上个月的使用情况，以帮助你确定实验室的任何调整。 你可以在 [订阅成本分析](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis)中发现过去成本的细分。 在 Azure 门户中，可以在 "搜索" 框中输入 **订阅** ，然后选择 " **订阅** " 选项。 

> [!div class="mx-imgBorder"]
> ![显示搜索框和订阅选项的屏幕截图。](./media/cost-management-guide/subscription-search.png)

选择要查看的特定订阅。

> [!div class="mx-imgBorder"]
> ![显示订阅选择的屏幕截图。](./media/cost-management-guide/subscription-select.png)

在 "**成本管理**" 下的左窗格中选择 "**成本分析**"。

> [!div class="mx-imgBorder"]
> ![显示图表上的订阅成本分析的屏幕截图。](./media/cost-management-guide/subscription-cost-analysis.png)

此仪表板允许进行深度的成本分析，其中包括按计划导出到不同文件类型的功能。 有关详细信息，请参阅 [成本管理 + 计费概述](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)。

可以按资源类型进行筛选。 使用 `microsoft.labservices/labaccounts` 将只显示与实验室服务关联的成本。

## <a name="understand-the-usage"></a>了解用法

下面的屏幕截图是成本分析的一个示例。

> [!div class="mx-imgBorder"]
> ![显示订阅的示例成本分析的屏幕截图。](./media/cost-management-guide/cost-analysis.png)

默认情况下，有六列： **资源**、 **资源类型**、 **位置**、 **资源组名称**、 **标记**和 **成本**。 **资源**列包含有关实验室帐户、实验室名称和 VM 的信息。 显示实验室帐户、实验室名称和默认 (第二行和第三行) 的行是实验室的费用。 使用的 Vm 的成本是显示实验室帐户、实验室名称、默认值和 VM 名称的行。 

在此示例中，添加第一个和第二行 (从 **aaalab/dockerlab** 开始) 将为你提供 "aaalab" 实验室帐户中实验室 "dockerlab" 的总费用。

若要获取映像库的总体开销，请将资源类型更改为 `Microsoft.Compute/Galleries` 。 共享图像库可能不会显示在成本上，具体取决于存储库的位置。

> [!NOTE]
> 共享映像库连接到实验室帐户。 这意味着多个实验室可以使用同一个映像。

## <a name="separate-the-costs"></a>分隔成本

某些大学使用实验室帐户和资源组来分隔类。 每个类都有其自己的实验室帐户和资源组。 

在 "成本分析" 窗格中，根据资源组名称添加类的筛选器，并为类添加适当的资源组名称。 然后，只有该类的成本才可见。 这样就可以在查看成本时，使类之间的略图更清晰。 您可以使用成本分析的 [计划导出](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) 功能，在单独的文件中下载每个类的成本。

## <a name="manage-costs"></a>管理成本

根据类的类型，可以通过多种方法来管理成本，以减少在没有学生使用的情况下运行的 Vm 的实例。

### <a name="automatic-shutdown-settings-for-cost-control"></a>成本控制的自动关闭设置

使用自动关闭功能可以防止实验室中的 VM 使用时间浪费。 以下设置捕获用户意外使其虚拟机保持运行的大多数情况：

> [!div class="mx-imgBorder"]
> ![显示三个自动关闭设置的屏幕截图。](./media/cost-management-guide/auto-shutdown-disconnect.png)

你可以同时配置实验室帐户级别和实验室级别的这些设置。 如果在实验室帐户级别启用这些帐户，则这些帐户将应用到实验室帐户中的所有实验室。 对于所有新的实验室帐户，默认情况下将启用这些设置。 

#### <a name="automatically-disconnect-users-from-virtual-machines-that-the-os-deems-idle"></a>自动断开用户与 OS 认为空闲的虚拟机的连接

> [!NOTE]
> 此设置仅适用于 Windows 虚拟机。

当启用 " **当虚拟机处于空闲状态时断开用户连接** " 设置时，如果 Windows OS 认为会话处于空闲状态，则该用户将从实验室中的所有计算机断开连接 (包括模板虚拟机) 。 [WINDOWS OS 的空闲定义](https://docs.microsoft.com/windows/win32/taskschd/task-idle-conditions#detecting-the-idle-state)使用两个条件： 

* 用户缺勤：没有键盘或鼠标输入。
* 缺少资源消耗：所有处理器和所有磁盘在一定时间百分比处于空闲状态。

断开连接之前，用户会在 VM 中看到类似于下面的消息： 

> [!div class="mx-imgBorder"]
> ![屏幕截图，显示一条警告消息，指出会话在其时间限制内处于空闲状态，并且将断开连接。](./media/cost-management-guide/idle-timer-expired.png)
 
用户断开连接时，虚拟机仍在运行。 如果用户通过登录来重新连接到虚拟机，则在断开连接之前已打开的窗口或已打开的文件或未保存的工作将仍然存在。 在此状态下，由于虚拟机正在运行，它仍将计为活动并产生开销。 
 
若要自动关闭断开连接的空闲 Windows 虚拟机，请在 **虚拟机处于空闲状态时使用断开连接用户** 的组合，并 **在用户断开连接设置时关闭虚拟机** 。

例如，如果配置设置，如下所示：
 
* **当虚拟机处于空闲状态时断开用户连接**：检测到空闲状态后15分钟。
* 用户**断开连接时，请关闭虚拟机**：用户断开连接5分钟。
 
Windows 虚拟机将在用户停止使用后的20分钟后自动关闭。 
 
> [!div class="mx-imgBorder"]
> ![图示显示了导致自动 VM 自动关闭的设置组合。](./media/cost-management-guide/vm-idle-diagram.png)

#### <a name="automatically-shut-down-virtual-machines-when-users-disconnect"></a>用户断开连接时自动关闭虚拟机
 
" **用户断开连接时关闭虚拟机** " 设置同时支持 Windows 和 Linux 虚拟机。 当此设置为 on 时，将在以下情况下自动关闭：
 
* 对于 Windows，远程桌面 (RDP) 连接已断开连接。
* 对于 Linux，SSH 连接已断开连接。
 
> [!NOTE]
> 仅支持 [特定的 linux 分发和版本](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux#supported-linux-distributions) 。
 
可以指定虚拟机在自动关机之前应等待用户重新连接的时间长度。 

#### <a name="automatically-shut-down-virtual-machines-that-are-started-but-users-dont-connect"></a>自动关闭已启动但用户未连接的虚拟机
 
在实验室中，用户可能会启动一个虚拟机，但永远不会连接到该虚拟机。 例如：
 
* 实验室中的计划将为类会话启动所有虚拟机，但有些学生不会显示，也不会连接到其计算机。 
* 用户启动一个虚拟机，但忘记连接。 
 
" **用户未连接时关闭虚拟机** " 设置将捕获这些情况，并自动关闭虚拟机。 
 
有关如何在断开连接时配置和启用 Vm 自动关闭的信息，请参阅以下文章：

* [为实验室帐户配置 Vm 自动关闭](how-to-configure-lab-accounts.md)
* [配置实验室的 Vm 自动关闭](how-to-enable-shutdown-disconnect.md)

### <a name="scheduled-time-vs-quota-time"></a>计划时间与配额时间

了解 [计划时间](classroom-labs-concepts.md#schedules) 和 [配额时间](classroom-labs-concepts.md#quota) 将帮助你配置实验室，以便更好地满足教授和学生的需求。 

计划时间是所有学生 Vm 均已启动并可用于连接的设置时间。 当所有学生都拥有自己的 Vm，并在一天中的设定时间内按教授的方向 (例如类小时) 时，通常使用计划时间。 缺点是所有学生 Vm 均已启动，并会产生成本，即使学生未登录到 VM 也是如此。 

配额时间是分配给每个学生的时间，按其自己的意愿使用，通常用于独立研究。 在学生启动 VM 之前，Vm 不会启动。 

实验室可以使用配额时间或计划时间，或者两者的组合。 如果类不需要计划时间，则只使用配额时间来最有效地利用 Vm。

### <a name="scheduled-event-stop-only"></a>计划事件：仅停止

在计划中，你可以添加将在特定时间停止所有计算机的仅停止事件类型。 某些实验室所有者已设置每日午夜的 "仅停止" 事件，以降低学生忘记关闭正在使用的 VM 时的成本和配额使用量。 此类事件的缺点是，所有 Vm 都将关闭，即使某个学生正在使用 VM 也是如此。

### <a name="other-costs-related-to-labs"></a>与实验室相关的其他成本 

一些成本不会汇总到实验室服务中，但可以绑定到实验室服务。 你可以将共享的映像库连接到实验室，但它不会显示在实验室服务成本下，并会产生成本。 为了帮助保持总体成本，你应该从库中删除所有未使用的映像，因为映像具有固有的存储成本。 

实验室可以通过虚拟网络连接到其他 Azure 资源。 删除实验室后，应删除虚拟网络和其他资源。

## <a name="conclusion"></a>结束语

但愿您可以更好地理解本文中的信息，以便您更好地了解可帮助您减少使用成本的工具。
