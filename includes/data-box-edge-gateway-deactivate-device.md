---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "79129496"
---
若要重置设备，需要安全地擦除设备的数据磁盘和启动磁盘上的所有数据。 

使用 `Reset-HcsAppliance` cmdlet 擦除数据磁盘和启动磁盘，或者只擦除数据磁盘。 `ClearData`和 `BootDisk` 交换机允许你分别擦除数据磁盘和启动磁盘。

此 `BootDisk` 开关将擦除启动磁盘并使设备不可用。 仅当需要将设备退回到 Microsoft 时，才应使用此开关。 有关详细信息，请参阅将 [设备返回到 Microsoft](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device)。

如果在本地 Web UI 中使用设备重置，则只会安全地擦除数据磁盘上的数据，而启动磁盘会保持不变。 启动磁盘包含设备配置。

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
