---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 03/11/2019
ms.author: ramamill
ms.openlocfilehash: 0d090f43b69b42a07f1c8949d1662e8e720f3cf4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57908529"
---
注销进程服务器的步骤取决于进程服务器与配置服务器的连接状态。

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>注销处于连接状态的进程服务器

1. 以管理员身份远程登录到进程服务器。
2. 启动**Control Panel** ，然后打开**程序 > 卸载程序**。
3. 卸载程序名为**Microsoft Azure Site Recovery 移动服务/主目标服务器**。
4. 卸载程序名为**Microsoft Azure Site Recovery 配置/进程服务器**。
5. 步骤 3 和 4 中的程序已卸载，则可以卸载**Microsoft Azure Site Recovery 配置/进程服务器依赖项**。

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>注销处于断开连接状态的进程服务器

> [!WARNING]
> 使用以下步骤，如果没有方法来恢复其安装进程服务器的虚拟机。

1. 以管理员身份登录到配置服务器。
2. 打开管理命令提示符，并浏览到目录 `%ProgramData%\ASR\home\svsystems\bin`。
3. 现在运行该命令。

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. 上面的命令将为进程服务器列表（在条目重复的情况下，进程服务器可能为多个）提供序列号（简称 S.No）、IP 地址（简称 IP）、部署进程服务器时所在 VM 的名称（简称“名称”）、VM 的检测信号（简称“检测信号”），如下所示。
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)
5. 现在，请输入要注销的进程服务器的序列号。
6. 这将从系统中清除进程服务器的详细信息，并将显示消息：**已成功取消注册服务器名称 > （服务器 IP 地址）**

