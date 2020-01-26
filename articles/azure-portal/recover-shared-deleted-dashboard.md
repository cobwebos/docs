---
title: 在 Azure 门户中恢复已删除的仪表板 |Microsoft Docs
description: 如果在 Azure 门户中删除已发布的仪表板，则可以恢复该仪表板。
services: azure-portal
author: mgblythe
ms.author: mblythe
ms.date: 01/21/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: ead9dab61e0cb60505aed9db43c61eb73a0e22a9
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760192"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>在 Azure 门户中恢复已删除的仪表板

如果在 Azure 门户中删除_已发布_的仪表板，则可以在删除的14天内恢复该仪表板。 如果仪表板未发布，则不能恢复它，您必须重新生成它。 有关发布仪表板的详细信息，请参阅[发布仪表板](azure-portal-dashboard-share-access.md#publish-dashboard)。 请按照以下步骤恢复已发布的仪表板：

1. 从 "Azure 门户" 菜单中，选择 "**资源组**"，然后选择要在其中发布仪表板的资源组（默认情况下，它名为 "**仪表板**"）。

1. 在 "**活动日志**" 下，展开 "**删除仪表板**" 操作。 选择 "**更改历史记录**" 选项卡，然后选择 **\<删除的资源\>** 。

    !["更改历史记录" 选项卡的屏幕截图](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. 选择并复制左窗格的内容，然后将其保存到文件扩展名为_json_的文本文件中。 门户使用 JSON 文件重新创建仪表板。

    ![更改历史记录差异的屏幕截图](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. 从 "Azure 门户" 菜单中，选择 "**仪表板**"，然后选择 "**上传**"。

    ![仪表板上传屏幕截图](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. 选择保存的 JSON 文件。 门户将重新创建与已删除仪表板具有相同名称和元素的仪表板。

1. 选择 "**共享**" 以发布仪表板并重新建立相应的访问控制。

    ![仪表板共享的屏幕截图](media/recover-shared-deleted-dashboard/dashboard-share.png)
