---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 36a014fbff30c32a7149a83907c9586bc3b2f01a
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166105"
---
<!--author=SharS last changed: 11/18/16-->

#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>通过 Windows PowerShell for StorSimple 安装常规更新
1. 打开设备串行控制台，并选择选项 1“使用完整访问权限登录”。 键入密码。 默认密码为 *Password1*。 
2. 在命令提示符处，键入：
   
     `Get-HcsUpdateAvailability`
   
    将通知更新可用以及更新是中断性还是非中断性。
3. 在命令提示符处，键入：
   
     `Start-HcsUpdate`
   
    将启动更新过程。

> [!IMPORTANT]
> * 此命令仅适用于常规更新。 仅在一个控制器上运行此命令，但这两个控制器都将更新。 
> * 在更新过程中，你可能会注意到控制器故障转移；但是，故障转移不会影响系统可用性或操作。
> 
> 

