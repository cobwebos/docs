---
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 11/09/2018
ms.author: lahugh
ms.openlocfilehash: bd51eabcff412de4928c682683b2a69b3e82e601
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66127362"
---
### <a name="retrieve-output-files"></a>检索输出文件

可以使用 Azure 门户下载 ffmpeg 任务生成的输出 MP3 文件。 

1. 单击“所有服务” > “存储帐户”，然后单击存储帐户的名称。
2. 单击“Blob” > “输出”。
3. 右键单击一个输出 MP3 文件，然后单击“下载”。 在浏览器中按提示打开或保存该文件。

![下载输出文件](./media/batch-common-tutorial-download/download.png)

也可以编程方式从计算节点或存储容器下载这些文件（但在本示例中未演示）。
