---
title: 升级 Microsoft Azure 恢复服务（MARS）代理
description: 了解如何升级 Microsoft Azure 恢复服务（MARS）代理。
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 49daf438b855d19961519d93b6c3ec535de4756f
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672880"
---
# <a name="upgrade-the-microsoft-azure-recovery-services-mars-agent"></a>升级 Microsoft Azure 恢复服务（MARS）代理

在本文中，你将学习如何：

* 识别具有早期版本的 MARS 代理的服务器
* 在这些服务器上更新 MARS 安装

## <a name="identify-servers-with-earlier-versions-of-the-mars-agent"></a>识别具有早期版本的 MARS 代理的服务器

对于 Azure 备份代理和 Azure 备份服务器的安装：

1. 导航到恢复服务保管库，其中你的已注册服务器可能已由较旧版本的代理进行备份。 你可以从 Azure 的 Azure 备份更新警报中找到包含旧版 Azure 备份代理的保管库的代表性列表。
1. 在恢复服务保管库的 "左侧**设置**" 部分中，选择 "**管理**" 部分下的 "**备份基础结构**"。
1. 若要发现在安装 Azure 备份服务器的过程中安装的 Azure 备份代理，请在**管理服务器**下切换到 "**备份管理服务器**"。 这会列出安装了 Azure 备份服务器的服务器以及关联的 Azure 备份代理的版本号。

    ![在 Azure 备份服务器安装过程中安装的 MARS 代理列表](./media/upgrade-mars-agent/backup-management-servers.png)

1. 若要检查 Microsoft Azure 恢复服务（MARS）代理安装或 Azure 备份代理的代理版本，请在**管理服务器**下切换到**受保护的服务器**。 然后选择 "备份管理类型" 下的 " **Azure 备份代理**"。 这会列出与安装的版本号一起安装了 Azure 备份代理的服务器。

    ![安装了 MARS 代理的服务器的列表](./media/upgrade-mars-agent/protected-servers.png)

1. 通过单击 MARS 代理安装的 "**代理版本**" 列或 azure 备份服务器安装的 " **Azure 备份代理版本**" 列对 azure 备份代理版本列进行排序。

1. 上一步将为你提供包含版本低于2.0.9083.0 的 Azure 备份代理的服务器的列表或作为空白列出的代理版本。 这些是需要更新 Azure 备份代理的服务器。

## <a name="update-the-mars-agent-installation-on-the-server"></a>更新服务器上的 MARS 代理安装

确定需要 Azure 备份代理更新的服务器后，请针对每个标识的服务器（使用 Azure 备份服务器或 MARS 代理）执行以下步骤。 在执行以下步骤之前，请[下载最新版本的 Azure 备份代理](https://aka.ms/azurebackup_agent)。

1. 单击 "Azure 备份代理低于 2.0.9083.0" 或 "空白" 的行。 这将打开 "服务器详细信息" 屏幕。

    ![具有过期代理版本的受保护服务器](./media/upgrade-mars-agent/old-agent-version.png)

    ![具有过期代理版本的 Azure 备份服务器](./media/upgrade-mars-agent/backup-management-servers-old-versions.png)

1. 单击 "**连接**" 接收远程桌面连接文件以连接到服务器，或通过服务器上的 "远程桌面连接" 直接连接到服务器。

    ![通过远程桌面连接连接到服务器](./media/upgrade-mars-agent/connect-to-server.png)

    >[!NOTE]
    > 如果列出的服务器不存在或已被取消，你可以忽略以下剩余步骤，并跳到下一个服务器。

1. 输入管理登录详细信息并登录。

1. 如果服务器或服务器的代理具有有限的 internet 访问权限，请确保将服务器/代理上的防火墙设置配置为允许使用适用于你正在使用的 Azure 云的 URL：

    Azure 云 | 代码
    -- | ---
    Azure 云（公共） |   `https://login.windows.net`
    Azure 中国世纪互联云   | `https://login.chinacloudapi.cn`
    Azure 美国政府云 |   `https://login.microsoftonline.us`
    Azure 德国云  |  `https://login.microsoftonline.de`

1. 将 Azure 备份代理更新安装程序复制到服务器。

    ![将 Azure 备份代理更新安装程序复制到服务器](./media/upgrade-mars-agent/copy-agent-installer.png)

1. 运行安装程序。 Microsoft Azure 恢复服务代理升级向导将打开。

    ![Microsoft Azure 恢复服务代理升级向导](./media/upgrade-mars-agent/agent-upgrade-wizard.png)

1. 单击“下一步”。

1. 单击“升级”。

    ![安装 Microsoft Azure 恢复服务代理](./media/upgrade-mars-agent/upgrade-installation.png)

1. 最终确认屏幕指示已成功更新 Azure 备份代理。

## <a name="for-system-center-data-protection-manager-sc-dpm-customers"></a>适用于 System Center Data Protection Manager （SC DPM）客户

如果已在 System Center Data Protection Manager （SC DPM）服务器上安装 Azure 备份代理，则需要按照以下步骤来确定 DPM 服务器是否需要 Azure 备份代理更新：

1. 以管理员身份登录到你的 SC DPM 服务器。
2. 打开 DPM 控制台。
3. 在控制台的左下角导航中，单击 "**管理**"。
4. 在左侧导航栏中显示的信息中，查找 Azure 备份代理的版本信息。
5. 如果版本低于2.0.9083.0，请下载最新的 Azure 备份代理安装程序，并在 DPM 服务器上运行该安装程序以更新 Azure 备份代理。

为环境中的所有 DPM 服务器重复上述步骤。

## <a name="next-steps"></a>后续步骤

了解如何[使用 Azure 备份 MARS 代理备份 Windows 计算机](backup-windows-with-mars-agent.md)
