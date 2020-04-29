---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79129496"
---
若要重置设备，需要安全地擦除数据磁盘和设备的启动磁盘上的所有数据。 

使用`Reset-HcsAppliance` cmdlet 擦除数据磁盘和启动磁盘，或者只擦除数据磁盘。 `ClearData`和`BootDisk`交换机允许你分别擦除数据磁盘和启动磁盘。

此`BootDisk`开关将擦除启动磁盘并使设备不可用。 仅当需要将设备返回到 Microsoft 时，才应使用此方法。 有关详细信息，请参阅将[设备返回到 Microsoft](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device)。

如果在本地 web UI 中使用设备重置，则只会安全地擦除数据磁盘，但启动磁盘会保持不变。 启动磁盘包含设备配置。

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
