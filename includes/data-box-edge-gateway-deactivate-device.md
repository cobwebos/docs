---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129496"
---
要重置设备，您需要安全地擦除数据磁盘和设备引导磁盘上的所有数据。 

使用`Reset-HcsAppliance`cmdlet 擦除数据磁盘和引导磁盘，或者仅清除数据磁盘。 `ClearData`和`BootDisk`交换机允许您分别擦除数据磁盘和引导磁盘。

交换机`BootDisk`擦除引导磁盘并使设备无法使用。 仅当设备需要返回到 Microsoft 时，才应使用它。 有关详细信息，请参阅[将设备返回到 Microsoft](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device)。

如果在本地 Web UI 中使用设备重置，则仅安全地擦除数据磁盘，但引导磁盘保持不变。 引导磁盘包含设备配置。

1. [连接到 PowerShell 接口](#connect-to-the-powershell-interface)。
2. 在命令提示符处，键入：

    `Reset-HcsAppliance -ClearData -BootDisk`

    下面的示例演示如何使用此 cmdlet：

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
