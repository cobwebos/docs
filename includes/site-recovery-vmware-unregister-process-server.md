---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 08/06/2018
ms.author: ramamill
ms.openlocfilehash: 81390d38b4c0c38b7ac6883ae2bf18c64542fa00
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39583012"
---
注销进程服务器的步骤取决于进程服务器与配置服务器的连接状态。

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>注销处于连接状态的进程服务器

1. 以管理员身份远程登录到进程服务器。
2. 启动“控制面板”，并打开“程序”>“卸载程序”
3. 卸载名为“Microsoft Azure Site Recovery 配置/进程服务器”的程序
4. 完成步骤 3 后，即可卸载“Microsoft Azure Site Recovery 配置/进程服务器依赖项”

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>注销处于断开连接状态的进程服务器

> [!WARNING]
> 如果无法恢复其上安装了进程服务器的虚拟机，则可使用以下步骤。

1. 以管理员身份登录到配置服务器。
2. 打开管理命令提示符，并浏览到目录 `%ProgramData%\ASR\home\svsystems\bin`。
3. 现在运行该命令。

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. 上面的命令将为进程服务器列表（在条目重复的情况下，进程服务器可能为多个）提供序列号（简称 S.No）、IP 地址（简称 IP）、部署进程服务器时所在 VM 的名称（简称“名称”）、VM 的检测信号（简称“检测信号”），如下所示。
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)
5. 现在，请输入要注销的进程服务器的序列号。
6. 此时会清除系统提供的进程服务器的详细信息，并会显示消息：**成功注销了 server-name> (server-IP-address)**

