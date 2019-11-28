---
title: 教程 - 从 Azure 成本管理创建和管理导出的数据
description: 本文介绍如何创建和管理导出的 Azure 成本管理数据，以便在外部系统中使用。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/12/2019
ms.topic: tutorial
ms.service: cost-management-billing
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: a462b3d165a596673049abbbb8b5b8d346f5fc9d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229833"
---
# <a name="tutorial-create-and-manage-exported-data"></a>教程：创建和管理导出的数据

如果你已阅读成本分析教程，则会熟悉如何手动下载成本管理数据。 但是，可以创建一个定期任务，按日、按周或按月自动将成本管理数据导出到 Azure 存储。 导出的数据采用 CSV 格式，它包含成本管理收集的所有信息。 然后，可以在外部系统使用从 Azure 存储导出的数据，并将其与自己的自定义数据相结合。 在类似仪表板或其他财务系统等的外部系统中，你都可以使用导出的数据。

观看[如何使用 Azure 成本管理计划对存储的导出](https://www.youtube.com/watch?v=rWa_xI1aRzo)视频，了解如何创建计划导出以将 Azure 成本数据导出到 Azure 存储。

本教程将通过示例，指导你导出成本管理数据，然后验证数据是否已成功导出。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建每日导出
> * 验证收集的数据

## <a name="prerequisites"></a>先决条件
针对各种 Azure 帐户类型提供了数据导出功能，包括[企业协议 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) 客户。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](understand-cost-mgt-data.md)。 对于按用户和组导出的数据，每个订阅均支持以下 Azure 权限或作用域。 有关范围的详细信息，请参阅[了解并使用范围](understand-work-scopes.md)。

- 所有者 - 可以为订阅创建、修改或删除计划导出。
- 参与者 - 可以创建、修改或删除自己的计划导出。 可以修改其他人创建的计划导出的名称。
- 读者 - 可以计划他们有权访问的导出。

对于 Azure 存储帐户：
- 无论导出权限如何，更改配置的存储帐户都需要写入权限。
- 必须为 Blob 或文件存储配置 Azure 存储帐户。

## <a name="sign-in-to-azure"></a>登录 Azure
在 [https://portal.azure.com](https://portal.azure.com/) 中登录 Azure 门户。

## <a name="create-a-daily-export"></a>创建每日导出

要创建或查看数据导出或计划导出，请在 Azure 门户中打开所需的作用域，然后在菜单中选择“成本分析”  。 例如，导航到“订阅”，从列表中选择订阅，然后在菜单中选择“成本分析”   。 在“成本分析”页的顶部，单击“导出”，然后选择导出选项  。 例如，单击“计划导出”  。  

> [!NOTE]
> - 除了订阅之外，还可以针对资源组、帐户、部门和注册创建导出。 有关范围的详细信息，请参阅[了解并使用范围](understand-work-scopes.md)。
>- 在计费帐户范围内或在客户的租户上以合作伙伴身份登录时，可以将数据导出到链接到合作伙伴存储帐户的 Azure 存储帐户。 但是，必须在 CSP 租户中具有活动订阅。
>


单击“添加”，为导出键入名称，然后选择“本月至今日成本每日导出”选项   。 单击“下一步”。 

![显示导出类型的新导出示例](./media/tutorial-export-acm-data/basics_exports.png)

指定 Azure 存储帐户的订阅，然后选择存储帐户。  指定要将导出文件放置到其中的存储容器和目录路径。  单击“下一步”。 

![显示存储帐户详细信息的新导出示例](./media/tutorial-export-acm-data/storage_exports.png)

查看导出详细信息，然后单击“创建”。 

新导出将出现在导出列表中。 默认情况下，新导出为启用状态。 如果要禁用或删除计划的导出，请单击列表中的任何项，然后再单击“禁用”  或“删除”  。

最初，在导出运行之前，可能需要一到两个小时。 但是，在导出文件中显示数据之前，它可能需要四个小时。

### <a name="export-schedule"></a>导出计划

计划的导出受刚开始创建导出时的时间以及星期几的影响。 创建计划的导出以后，随后的每次导出都会在一天的同一时间运行。 例如，你创建了一个在每日下午 1:00 进行的导出。 下一次导出在第二天下午 1:00 运行。 当前时间以相同的方式影响所有其他的导出类型—它们始终在一天的同一时间运行，与刚开始创建导出时的运行时间一样。 在另一示例中，你创建一个在每周一下午 4:00 进行的导出。 下一次导出在下一个星期一的下午 4:00 运行。  导出的数据在运行导出后四小时内即可可用。

每次导出都会创建新文件，因此旧的导出不会被覆盖。

有三种类型的导出选项：

**每日导出本月至今的成本** – 初始导出立即运行。 后续导出在第二天运行，其时间与初始导出一样。 最新数据是根据以前的每日导出聚合的。

**过去 7 天的每周导出成本** – 初始导出立即运行。 后续导出在每周的特定星期几运行，其时间与初始导出一样。 成本是过去七天的。

**自定义** – 可以使用 week-to-date 和 month-to-date 选项计划每周和每月的导出。  初始导出会立即运行。

如果有即用即付、MSDN 或 Visual Studio 订阅，则发票计费周期可能与日历月不一致。 对于这些类型的订阅和资源组，可以创建一个与发票周期或日历月一致的导出。 若要创建与发票月一致的导出，请导航到“自定义”，然后选择“计费周期至今”。    若要创建与日历月一致的导出，请选择“本月至今”。 
>
>

![“新建导出 - 基本信息”选项卡，显示自定义的每周 week-to-date 选择](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

## <a name="verify-that-data-is-collected"></a>验证收集的数据

可以轻松验证正在收集的成本管理数据，并使用 Azure 存储资源管理器查看导出的 CSV 文件。

在导出列表中，单击存储帐户名称。 在存储帐户页上，单击“在资源管理器中打开”。 如果看到确认框，单击“是”  ，即可在 Azure 存储资源管理器中打开文件。

![存储帐户页面，其中显示示例信息和“在资源管理器中打开”链接](./media/tutorial-export-acm-data/storage-account-page.png)

在存储资源管理器中，导航到你想要打开的容器，并选择与当前月份相对应的文件夹。 然后将显示 CSV 文件列表。 选择一个文件，然后单击“打开”  。

![存储资源管理器中显示的示例信息](./media/tutorial-export-acm-data/storage-explorer.png)

该文件将通过设置为打开 CSV 文件扩展名的程序或应用程序打开。 下面是 Excel 中的一个示例。

![Excel 中显示的导出 CSV 数据示例](./media/tutorial-export-acm-data/example-export-data.png)


## <a name="access-exported-data-from-other-systems"></a>从其他系统访问导出的数据

导出成本管理数据的用途之一是从外部系统访问数据。 你可能使用的是仪表板系统或其他财务系统。 此类系统各不相同，因此，只通过一个示例说明并不能解决实际问题。  但是，你可以在 [Azure 存储简介](../storage/common/storage-introduction.md)中开始学习如何访问应用程序中的数据。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 创建每日导出
> * 验证收集的数据

进入下一个教程，通过识别闲置和未充分利用的资源来优化和提高效率。

> [!div class="nextstepaction"]
> [查看并采纳优化建议](tutorial-acm-opt-recommendations.md)
