---
title: 导出或删除 Azure 门户设置 | Microsoft Docs
description: 了解如何导出或删除用户设置、 专用仪表板和在 Azure 门户中的自定义设置。
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: kfollis
ms.openlocfilehash: fde7ffbaa3ef4d47eea48302a99948932aeb4f00
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60551632"
---
# <a name="export-or-delete-user-settings"></a>导出或删除用户设置

您可以使用的设置和功能在 Azure 门户中创建自定义体验。 有关自定义设置的信息存储在 Azure 中。 可以导出或删除以下用户数据：

* 在 Azure 门户中的专用仪表板
* 用户设置，例如最喜欢的订阅或目录和最后一个登录的目录
* 主题和其他自定义门户设置

它是一个好主意导出和删除它们之前，请查看你的设置。 重新生成的仪表板或重做自定义设置可能非常耗时。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>导出或删除门户设置

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在门户的标题中，选择**设置**。

    ![显示门户设置齿轮的屏幕截图](media/azure-portal-export-delete-settings/azure-portal-settings-icon.png)

3. 选择“导出所有设置”或“删除所有设置和专用仪表板”。

    ![显示在门户的屏幕截图导出和删除设置](media/azure-portal-export-delete-settings/azure-portal-export-delete-settings.png)

      下表介绍了这些操作。

      | 操作 | 描述 |
      | --- | --- |
      | **导出所有设置** | 创建包含您的用户设置颜色主题、 收藏夹和专用仪表板等的.json 文件。|
      | **删除所有设置和专用仪表板** | 删除专用仪表板和其他自定义设置到门户所做的所有链接。 |

> [!NOTE]
> 由于用户设置的动态特性和数据损坏的风险，您不能从导入设置的.json 文件。
>
>


## <a name="next-steps"></a>后续步骤

* [创建并共享 Azure 仪表板](azure-portal-dashboard-share-access.md)
* [添加、 删除和排序收藏夹](azure-portal-add-remove-sort-favorites.md)
