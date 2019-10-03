---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d35f0ef783a2c48f8211657bc8829635c19495aa
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172977"
---
#### <a name="to-enter-maintenance-mode"></a>进入维护模式
1. 在串行控制台菜单中，选择选项 1“使用完全访问权限登录”  。
2. 键入密码。 默认密码为 **Password1**。
3. 在命令提示符处，键入
   
     `Enter-HcsMaintenanceMode`
4. 会看到一条警告消息，指示维护模式将中断所有 I/O 请求并断开到 Azure 经典门户的服务器连接，并会提示进行确认。 键入 **Y** 将进入维护模式。
   
    将重新启动两个控制器。 重新启动完成后，会显示另一条消息，指示设备处于维护模式。

