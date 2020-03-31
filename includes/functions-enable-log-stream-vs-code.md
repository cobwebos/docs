---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/25/2019
ms.author: glenga
ms.openlocfilehash: 437b4ab62cc8c4903af88ca2f9632e89b953c798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "68669532"
---
若要在 Azure 中为函数应用启用流式处理日志，请执行以下操作：

1. 选择 F1 打开命令调色板，然后搜索并运行命令 Azure**函数：启动流式处理日志**。

1. 在 Azure 中选择函数应用，然后选择“是”**** 为函数应用启用应用程序日志记录。

1. 在 Azure 中触发函数。 请注意，日志数据显示在 Visual Studio Code 的“输出”窗口中。

1. 完成后，请记住运行**命令 Azure 函数：停止流式处理日志**以禁用函数应用的日志记录。