---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 880b630ae48eda086f6454f0d7108d27d3403b77
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66161169"
---
如果在非 DHCP 环境中启动时，请按照以下步骤来为数据框网关部署虚拟机。

1. [连接到设备的 Windows PowerShell 界面](#connect-to-the-powershell-interface)。
2. 使用`Get-HcsIpAddress`cmdlet 可列出在虚拟设备上启用的网络接口。 如果设备启用了单个网络接口，则分配到该接口的默认名称为 `Ethernet`。

    下面的示例显示了此 cmdlet 的用法：

    ```
    [10.100.10.10]: PS>Get-HcsIpAddress

    OperationalStatus : Up
    Name              : Ethernet
    UseDhcp           : True
    IpAddress         : 10.100.10.10
    Gateway           : 10.100.10.1
    ```

3. 使用 `Set-HcsIpAddress` cmdlet 配置网络。 请参阅以下示例：

    ```
    Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1
    ```

