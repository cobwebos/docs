---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: aebb82690a7a49aba071ed64349d37d516208cca
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173411"
---
若要重置你的设备，你需要安全地擦除数据磁盘，并且你的设备的启动磁盘上的所有数据。 

使用`Reset-HcsAppliance`cmdlet 来擦除数据磁盘并启动磁盘或只是数据磁盘。 `ClearData`和`BootDisk`开关可用于分别擦除数据磁盘和启动磁盘。

如果使用本地 web UI 中重置的设备，仅数据磁盘被安全地擦除，但启动磁盘保持不变。 启动磁盘包含设备配置。

1. [连接到 PowerShell 界面](#connect-to-the-powershell-interface)。
2. 在命令提示符处，键入：

    `Reset-HcsAppliance -ClearData -BootDisk`

    下面的示例演示如何使用此 cmdlet:
    ```
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk
    
    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
