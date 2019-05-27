---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170027"
---
按照特定情况下的步骤。

### <a name="unregister-a-connected-process-server"></a>取消注册已连接的进程服务器

1. 建立远程连接到进程服务器以管理员身份。
2. 在中**Control Panel**，打开**程序 > 卸载程序**。
3. 卸载该程序**Microsoft Azure Site Recovery 移动服务/主目标服务器**。
4. 卸载该程序**Microsoft Azure Site Recovery 配置/进程服务器**。
5. 步骤 3 和 4 中的程序都卸载后，卸载**Microsoft Azure Site Recovery 配置/进程服务器依赖项**。

### <a name="unregister-a-disconnected-process-server"></a>注销已断开连接的进程服务器

如果没有方法来恢复其安装进程服务器的计算机，只能使用这些步骤。

1. 以管理员身份登录配置服务器。
2. 打开管理命令提示符，并浏览到`%ProgramData%\ASR\home\svsystems\bin`。
3. 运行此命令可获取一个或多个进程服务器的列表。

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - 序列 否： 进程服务器序列号。
    - IP/名称：IP 地址和运行进程服务器的计算机的名称。
    - 检测信号：从进程服务器计算机的最后一个检测信号。
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. 指定想要取消注册进程服务器的序列号。
5. 取消注册进程服务器从系统中删除所有其详细信息并显示消息：**已成功取消注册服务器名称 > （服务器 IP 地址）**

