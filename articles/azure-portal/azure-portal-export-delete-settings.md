---
title: 导出或删除 Azure 门户设置
description: 了解如何在 Azure 门户中导出或删除用户设置、私有仪表板和自定义设置。
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
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900759"
---
# <a name="export-or-delete-user-settings"></a>导出或删除用户设置

您可以使用 Azure 门户中的设置和功能创建自定义体验。 有关自定义设置的信息存储在 Azure 中。 您可以导出或删除以下用户数据：

* Azure 门户中的私有仪表板
* 用户设置（如收藏的订阅或目录）以及最后的登录目录
* 主题和其他自定义门户设置

最好在删除之前导出和查看设置。 重新生成仪表板或重做自定义设置可能非常耗时。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>导出或删除门户设置

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在门户的标题中，选择 "![设置" 图标](media/azure-portal-export-delete-settings/settings-icon.png)**设置**"。

1. 选择“导出所有设置”或“删除所有设置和专用仪表板”。

    ![Azure 门户设置和设置选项](media/azure-portal-export-delete-settings/azure-portal-settings-with-export-delete.png)

      下表介绍了这些操作。

      | 行动 | Description |
      | --- | --- |
      | **导出所有设置** | 创建一个包含用户设置的*json*文件，如颜色主题、收藏夹和专用仪表板。|
      | **删除所有设置和专用仪表板** | 删除指向私有仪表板的所有链接和你对门户所做的其他自定义设置。 |

> [!NOTE]
> 由于用户设置的动态特性和数据损坏的风险，无法从*json*文件导入设置。
>
>

## <a name="next-steps"></a>后续步骤

* [使用基于角色的访问控制来共享 Azure 仪表板](azure-portal-dashboard-share-access.md)
* [添加、删除和重新排列收藏夹](azure-portal-add-remove-sort-favorites.md)
