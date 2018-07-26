---
title: 在 Azure Site Recovery 中管理进程服务器 | Microsoft Doc
description: 本文介绍如何在 Azure Site Recovery 中管理为 VMware VM 和物理服务器复制设置的进程服务器。
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/21/2018
ms.author: ramamill
ms.openlocfilehash: f8368aab1bc979492143d77a191a31afef754c4c
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213004"
---
# <a name="manage-process-servers"></a>管理进程服务器

默认情况下，将 VMware VM 或物理服务器复制到 Azure 时使用的进程服务器安装在本地配置服务器计算机上。 需要在几个实例中设置单独的进程服务器：

- 对于大型部署，可能需要使用额外的本地进程服务器来扩展容量。
- 对于故障回复，需要在 Azure 中设置临时进程服务器。 故障回复完成后，可以删除此 VM。 

本文汇总了这些额外的进程服务器的典型管理任务。

## <a name="upgrade-a-process-server"></a>升级进程服务器

升级在本地或 Azure 中运行的进程服务器（用于故障回复），如下所示：

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

> [!NOTE]
  通常，使用 Azure 库映像在 Azure 中创建用于故障回复的进程服务器时，该进程服务器运行最新的可用版本。 Site Recovery 团队会定期发布修补程序和增强功能，因此我们建议你使进程服务器保持最新。



## <a name="reregister-a-process-server"></a>重新注册进程服务器

如果需要将在本地或 Azure 中运行的进程服务器重新注册到配置服务器中，请执行以下操作：

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

保存设置后，请执行以下操作：

1. 在进程服务器上，打开管理员命令提示符。
2. 浏览到文件夹 **%PROGRAMDATA%\ASR\Agent**，并运行以下命令：

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>修改本地进程服务器的代理设置

如果在进程服务器使用代理连接到 Azure 中的 Site Recovery 的情况下需要修改现有代理设置，请使用此过程。

1. 登录到进程服务器计算机。 
2. 打开管理员 PowerShell 命令窗口并运行以下命令：
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```
2. 浏览到文件夹 **%PROGRAMDATA%\ASR\Agent**，并运行以下命令：
  ```
  cmd
  cdpcli.exe --registermt

  net stop obengine

  net start obengine

  exit
  ```


## <a name="remove-a-process-server"></a>删除进程服务器

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]


