---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/28/2020
ms.author: glenga
ms.openlocfilehash: 0d83f3a92efe7bddcefdeebb5b6d580caf749206
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91408525"
---
1. 在 Visual Studio Code 中，按 F1 打开命令面板<kbd></kbd>。 在命令面板中，搜索并选择 `Azure Functions: Open in portal`。

1. 选择你的函数应用，然后按 Enter<kbd></kbd>。 随即将在 Azure 门户中打开函数应用页面。

1. 在“概览”选项卡中，选择“资源组”旁边的命名链接。

   :::image type="content" source="./media/functions-cleanup-resources-vs-code/functions-app-delete-resource-group.png" alt-text="从函数应用页选择要删除的资源组。":::

1. 在“资源组”页中查看所包括的资源的列表，然后验证这些资源是否是要删除的。
 
1. 选择“删除资源组”，然后按说明操作。

   可能需要数分钟才能删除完毕。 完成后会显示一个通知，持续数秒。 也可以选择页面顶部的钟形图标来查看通知。
