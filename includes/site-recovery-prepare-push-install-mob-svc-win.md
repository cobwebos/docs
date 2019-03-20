---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: ffc9b09c72ef1bf5180a0d626908d09b6fdd41ca
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2018
ms.locfileid: "58113881"
---
### <a name="prepare-for-a-push-installation-on-a-windows-computer"></a>在 Windows 计算机上准备推送安装

1. 确保 Windows 计算机与进程服务器之间已建立网络连接。
1. 创建可供进程服务器用来访问计算机的帐户。 该帐户应具有管理员权限（本地或域）。 只能将此帐户用于推送安装和代理更新。

   > [!NOTE]
   > 如果不使用域帐户，请在本地计算机上禁用远程用户访问控制。 若要禁用远程用户访问控制，请在 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 注册表项，添加一个新的 dword 值：LocalAccountTokenFilterPolicy。 将值设置为 **1**。 若要执行此任务，请在命令提示符下运行以下命令：  
   `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
   >
   >
1. 在要保护的计算机的 Windows 防火墙中，选择“允许应用或功能通过防火墙”。 启用“文件和打印机共享”和“Windows Management Instrumentation (WMI)”。 对于属于某个域的计算机，可以使用组策略对象 (GPO) 配置防火墙设置。

   ![防火墙设置](./media/site-recovery-prepare-push-install-mob-svc-win/mobility1.png)

1. 添加在 CSPSConfigtool 中创建的帐户。 执行以下步骤:

    a. 登录到配置服务器。

    b. 打开 **cspsconfigtool.exe**。 桌面上有该工具的快捷方式，也可以在 %ProgramData%\home\svsystems\bin 文件夹中找到它。

    c. 在“管理帐户”选项卡中，选择“添加帐户”。

    d. 添加已创建的帐户。

    e. 输入为计算机启用复制时使用的凭据。
