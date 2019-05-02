---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: cf39baf34096691144181332566cf567ebc02310
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925625"
---
1. 建立到运行进程服务器的计算机的远程桌面连接。 
2. 运行 cspsconfigtool.exe 以启动 Azure Site Recovery 进程服务器配置工具。
    - 该工具就会自动启动首次登录到进程服务器。
    - 如果它未自动打开，请单击桌面上的快捷方式。

3. 在中**配置服务器的 FQDN 或 IP**，指定的名称或配置服务器用来注册进程服务器的 IP 地址。
4. 在中**配置服务器端口**，请确保指定了 443。 这是配置服务器侦听请求的端口。
5. 在中**连接通行短语**，指定配置服务器设置时指定的密码。 若要查找该通行短语：
    -  在配置服务器上，导航到 Site Recovery 安装文件夹 **\home\svssystems\bin\**。 
    - 运行以下命令： **genpassphrase.exe.n**。 这将显示通行短语，然后可以记下的位置。

6. 在中**的数据传输端口**，保留默认值，除非已指定自定义端口。

7. 单击**保存**保存设置，并注册进程服务器。

    
    ![注册进程服务器](./media/site-recovery-vmware-register-process-server/register-ps.png)
