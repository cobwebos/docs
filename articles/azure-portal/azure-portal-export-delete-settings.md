---
title: 导出或删除 Azure 门户设置
description: 了解如何在 Azure 门户中导出或删除用户设置、专用仪表板和自定义设置。
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: accdfbd939fad73ca7d008450c358d366e7f8b70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900759"
---
# <a name="export-or-delete-user-settings"></a>导出或删除用户设置

你可以使用 Azure 门户中的设置和功能来创建自定义体验。 有关自定义设置的信息存储在 Azure 中。 可以导出或删除以下用户数据：

* Azure 门户中的专用仪表板
* 用户设置，例如最喜欢的订阅或目录，以及上次登录的目录
* 主题和其他自定义门户设置

在删除设置之前，建议先导出并检查设置。 重新生成仪表板或重做自定义设置可能非常耗时。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>导出或删除门户设置

1. 登录到 Azure[门户](https://portal.azure.com)。

1. 在门户的标题中，选择![“设置”图标“设置”](media/azure-portal-export-delete-settings/settings-icon.png) ****。

1. 选择“导出所有设置”**** 或“删除所有设置和专用仪表板”****。

    ![Azure 门户设置和设置选项](media/azure-portal-export-delete-settings/azure-portal-settings-with-export-delete.png)

      下表介绍了这些操作。

      | 操作 | 描述 |
      | --- | --- |
      | **导出所有设置** | 创建一个 .json** 文件，其中包含你的用户设置（如颜色主题、收藏夹和专用仪表板）。|
      | **删除所有设置和专用仪表板** | 删除指向专用仪表板的所有链接以及你对门户所做的其他自定义设置。 |

> [!NOTE]
> 由于用户设置的动态性质和数据损坏风险，因此不能从 .json** 文件导入设置。
>
>

## <a name="next-steps"></a>后续步骤

* [通过使用基于角色的访问控制来共享 Azure 仪表板](azure-portal-dashboard-share-access.md)
* [添加、删除和重新排列收藏夹](azure-portal-add-remove-sort-favorites.md)
