---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: bff9457220efbb6a6b318550a5b2dd9af4ae230f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361377"
---
根据你的具体情况执行以下步骤。

### <a name="unregister-a-connected-process-server"></a>注销已连接的进程服务器

1. 以管理员身份与进程服务器建立远程连接。
2. 在“控制面板”  中，打开“程序”>“卸载程序”  。
3. 卸载 Microsoft Azure Site Recovery Mobility Service/Master Target Server 程序。
4. 卸载 Microsoft Azure Site Recovery Configuration/Process Server 程序。
5. 卸载步骤 3 和 4 中的程序后，请卸载 Microsoft Azure Site Recovery Configuration/Process Server Dependencies。

### <a name="unregister-a-disconnected-process-server"></a>注销已断开连接的进程服务器

仅当无法恢复安装了进程服务器的计算机时，才使用这些步骤。

1. 以管理员身份登录配置服务器。
2. 打开管理命令提示符，并浏览到 `%ProgramData%\ASR\home\svsystems\bin`。
3. 运行此命令以获取一个或多个进程服务器的列表。

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. 号：进程服务器序列号。
    - IP/名称：运行进程服务器的计算机的 IP 地址和名称。
    - 检测信号：进程服务器计算机发出的上一个检测信号。
    ![屏幕截图中有进程服务器和文本相关信息的纯文本显示。请选择上述某个服务器进行注销。(media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. 指定要注销的进程服务器的序列号。
5. 注销进程服务器将从系统中删除其所有详细信息，并显示消息：**已成功注销服务器名称> (服务器 IP 地址)**

