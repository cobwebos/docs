---
title: " (MARS) 代理升级 Microsoft Azure 恢复服务"
description: 了解如何 (MARS) 代理升级 Microsoft Azure 恢复服务。
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: a1ee26db962781643e9599069282647658301bac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89181470"
---
# <a name="upgrade-the-microsoft-azure-recovery-services-mars-agent"></a> (MARS) 代理升级 Microsoft Azure 恢复服务

在本文中，你将学习如何：

* 识别装有早期版本的 MARS 代理的服务器
* 更新这些服务器上的 MARS 安装

## <a name="identify-servers-with-earlier-versions-of-the-mars-agent"></a>确定装有早期版本的 MARS 代理的服务器

对于 Azure 备份代理和 Azure 备份服务器的安装：

1. 导航到可能由旧版代理备份的已注册服务器所在的恢复服务保管库。 可以在 Azure 发出的 Azure 备份更新警报中，找到包含旧版 Azure 备份代理的保管库的代表性列表。
1. 在恢复服务保管库的左侧“设置”部分，选择“管理”部分下的“备份基础结构”  。
1. 若要发现在安装 Azure 备份服务器过程中一起安装的 Azure 备份代理，请转到“管理服务器”下的“备份管理服务器” 。 此时会列出包含 Azure 备份服务器安装的服务器，以及关联 Azure 备份代理的版本号。

    ![在安装 Azure 备份服务器过程中一起安装的 MARS 代理列表](./media/upgrade-mars-agent/backup-management-servers.png)

1. 若要检查 Microsoft Azure 恢复服务 (MARS) 代理安装或 Azure 备份代理的代理版本，请在 "**管理服务器**" 下，选择 "**受保护的服务器**"。 然后选择“备份管理类型”下的“Azure 备份代理”。 此时会列出包含 Azure 备份代理安装的服务器以及安装的版本号。

    ![装有 MARS 代理的服务器列表](./media/upgrade-mars-agent/protected-servers.png)

1. 通过选择 MARS 代理安装对应的“代理版本”列，或 Azure 备份服务器安装对应的“Azure 备份代理版本”列，来对 Azure 备份代理版本列进行排序 。

1. 上述步骤会列出包含 2.0.9083.0 以下版本的 Azure 备份代理或者代理版本为空白的服务器。 需要在这些服务器中更新 Azure 备份代理。

## <a name="update-the-mars-agent-installation-on-the-server"></a>更新服务器上的 MARS 代理安装

确定需要进行 Azure 备份代理更新的服务器后，针对确定的每台服务器执行以下步骤（使用 Azure 备份服务器或 MARS 代理）。 在执行以下步骤之前，请[下载最新版本的 Azure 备份代理](https://aka.ms/azurebackup_agent)。

1. 选择版本低于 2.0.9083.0 或者为空白的 Azure 备份代理所在的行。 此时会打开服务器详细信息屏幕。

    ![代理版本已过时的受保护服务器](./media/upgrade-mars-agent/old-agent-version.png)

    ![代理版本已过时的 Azure 备份服务器](./media/upgrade-mars-agent/backup-management-servers-old-versions.png)

1. 选择“连接”以接收用于连接服务器的远程桌面连接文件，或者通过服务器上的远程桌面连接直接连接到服务器。

    ![通过远程桌面连接连接到服务器](./media/upgrade-mars-agent/connect-to-server.png)

    >[!NOTE]
    > 如果列出的服务器不存在或已被解除，则可以忽略下面的剩余步骤，并跳转到下一台服务器。

1. 输入管理登录详细信息并登录。

1. 如果服务器或服务器的代理具有有限的 internet 访问权限，请确保将服务器/代理上的防火墙设置配置为允许使用适用于你正在使用的 Azure 云的 URL：

    Azure 云 | URL
    --- | ---
    Azure 云 (公有)  |   `https://login.windows.net`
    Azure 中国世纪互联云   | `https://login.chinacloudapi.cn`
    Azure 美国政府云 |   `https://login.microsoftonline.us`
    Azure 德国云  |  `https://login.microsoftonline.de`

1. 将 Azure 备份代理更新安装程序复制到服务器。

    ![将 Azure 备份代理更新安装程序复制到服务器](./media/upgrade-mars-agent/copy-agent-installer.png)

1. 运行安装程序。 Microsoft Azure 恢复服务代理升级向导将打开。

    ![Microsoft Azure 恢复服务代理升级向导](./media/upgrade-mars-agent/agent-upgrade-wizard.png)

1. 选择“下一步”  。

1. 选择“升级”。

    ![安装 Microsoft Azure 恢复服务代理](./media/upgrade-mars-agent/upgrade-installation.png)

1. 最终确认屏幕将指出已成功更新 Azure 备份代理。

## <a name="for-system-center-data-protection-manager-sc-dpm-customers"></a>对于 System Center Data Protection Manager (SC DPM) 客户

如果已在 System Center Data Protection Manager (SC DPM) 服务器上安装 Azure 备份代理，则需要遵循以下步骤来确定 DPM 服务器是否需要 Azure 备份代理更新：

1. 以管理员身份登录到 SC DPM 服务器。
2. 打开 DPM 控制台。
3. 选择控制台左下导航栏中的“管理”。
4. 在左侧导航栏上显示的信息中，查看 Azure 备份代理版本信息。
5. 如果版本低于 2.0.9083.0，请下载最新的 Azure 备份代理安装程序，并在 DPM 服务器上运行该安装程序以更新 Azure 备份代理。

针对环境中的所有 DPM 服务器重复上述步骤。

## <a name="next-steps"></a>后续步骤

了解如何[使用 Azure 备份 MARS 代理备份 Windows 计算机](backup-windows-with-mars-agent.md)
