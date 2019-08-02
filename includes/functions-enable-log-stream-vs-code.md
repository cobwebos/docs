---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/25/2019
ms.author: glenga
ms.openlocfilehash: 437b4ab62cc8c4903af88ca2f9632e89b953c798
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68669532"
---
若要在 Azure 中为函数应用启用流式处理日志，请执行以下操作：

1. 选择 F1 打开命令面板, 然后搜索并运行命令**Azure Functions:** 开始流式处理日志”。

1. 选择 Azure 中的函数应用, 然后选择 **"是"** 以启用函数应用的应用程序日志记录。

1. 在 Azure 中触发函数。 请注意, 日志数据显示在 Visual Studio Code 的 "输出" 窗口中。

1. 完成后, 请记得运行命令**Azure Functions:** 停止流式处理日志”为函数应用禁用日志记录。