---
title: 升级 Microsoft Azure 恢复服务 （MARS） 代理
description: 了解如何升级 Microsoft Azure 恢复服务 （MARS） 代理。
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 49daf438b855d19961519d93b6c3ec535de4756f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672880"
---
# <a name="upgrade-the-microsoft-azure-recovery-services-mars-agent"></a>升级 Microsoft Azure 恢复服务 （MARS） 代理

在本文中，你将学习如何：

* 使用早期版本的 MARS 代理标识服务器
* 更新这些服务器上的 MARS 安装

## <a name="identify-servers-with-earlier-versions-of-the-mars-agent"></a>使用早期版本的 MARS 代理标识服务器

对于 Azure 备份代理和 Azure 备份服务器的安装：

1. 导航到恢复服务保管库，其中已注册服务器，这些服务器可能由旧版本的代理进行备份。 您可以在 Azure 备份更新警报中找到具有较旧 Azure 备份代理的代表性保管库列表。
1. 在恢复服务保管库的左侧**设置**部分，在 **"管理**"部分下选择**备份基础结构**。
1. 要发现作为 Azure 备份服务器安装的一部分安装的 Azure 备份代理，请转到**管理服务器**下的**备份管理服务器**。 这将列出具有 Azure 备份服务器安装的服务器以及关联的 Azure 备份代理的版本号。

    ![作为 Azure 备份服务器安装的一部分安装的 MARS 代理列表](./media/upgrade-mars-agent/backup-management-servers.png)

1. 要检查 Microsoft Azure 恢复服务 （MARS） 代理安装或 Azure 备份代理的代理版本，请转到**管理服务器**下的**受保护服务器**。 然后在备份管理类型下选择**Azure 备份代理**。 这将列出具有 Azure 备份代理安装的服务器以及安装的版本号。

    ![安装了 MARS 代理的服务器列表](./media/upgrade-mars-agent/protected-servers.png)

1. 通过单击 MARS 代理安装的 **"代理版本**"列或 Azure 备份服务器安装的**Azure 备份代理版本**列，对 Azure 备份代理版本列进行排序。

1. 上一步将为您提供具有低于 2.0.9083.0 或代理版本列为空白的 Azure 备份代理的服务器的列表。 这些是需要更新 Azure 备份代理的服务器。

## <a name="update-the-mars-agent-installation-on-the-server"></a>更新服务器上的 MARS 代理安装

标识需要 Azure 备份代理更新的服务器后，请对每个标识的服务器执行以下步骤（使用 Azure 备份服务器或 MARS 代理）。 [按照以下步骤下载最新版本的 Azure 备份代理](https://aka.ms/azurebackup_agent)。

1. 单击 Azure 备份代理低于 2.0.9083.0 或空白的行。 这将打开服务器详细信息屏幕。

    ![具有过期代理版本的受保护服务器](./media/upgrade-mars-agent/old-agent-version.png)

    ![具有过期代理版本的 Azure 备份服务器](./media/upgrade-mars-agent/backup-management-servers-old-versions.png)

1. 单击"**连接"** 以接收远程桌面连接文件以与服务器连接或通过服务器上的远程桌面连接直接连接到服务器。

    ![通过远程桌面连接连接到服务器](./media/upgrade-mars-agent/connect-to-server.png)

    >[!NOTE]
    > 如果列出的服务器不存在或已停用，则可以忽略下面的其余步骤，并跳到下一个服务器。

1. 输入您的管理登录详细信息并登录。

1. 如果服务器或服务器的代理的 Internet 访问受限，请确保服务器/代理上的防火墙设置配置为允许适合您使用的 Azure 云的 URL：

    Azure 云 | 代码
    -- | ---
    Azure 云（公共） |   `https://login.windows.net`
    Azure 中国 21Vianet 云   | `https://login.chinacloudapi.cn`
    Azure 美国政府云 |   `https://login.microsoftonline.us`
    Azure 德国云  |  `https://login.microsoftonline.de`

1. 将 Azure 备份代理更新安装程序复制到服务器。

    ![将 Azure 备份代理更新安装程序复制到服务器](./media/upgrade-mars-agent/copy-agent-installer.png)

1. 运行安装程序。 打开 Microsoft Azure 恢复服务代理升级向导。

    ![微软 Azure 恢复服务代理升级向导](./media/upgrade-mars-agent/agent-upgrade-wizard.png)

1. 单击“下一步”****。

1. 单击“升级”****。

    ![安装微软 Azure 恢复服务代理](./media/upgrade-mars-agent/upgrade-installation.png)

1. 最终确认屏幕指示 Azure 备份代理已成功更新。

## <a name="for-system-center-data-protection-manager-sc-dpm-customers"></a>适用于系统中心数据保护管理器 （SC DPM） 客户

如果在系统中心数据保护管理器 （SC DPM） 服务器上安装了 Azure 备份代理，则需要按照以下步骤确定 DPM 服务器是否需要 Azure 备份代理更新：

1. 以管理员身份登录到 SC DPM 服务器。
2. 打开 DPM 控制台。
3. 单击控制台左下角导航中的**管理**。
4. 在左侧导航中显示的信息中，查找 Azure 备份代理版本信息。
5. 如果版本低于 2.0.9083.0，请下载最新的 Azure 备份代理安装程序，并在 DPM 服务器上运行安装程序以更新 Azure 备份代理。

对环境中的所有 DPM 服务器重复上述步骤。

## <a name="next-steps"></a>后续步骤

了解如何使用[Azure 备份 MARS 代理备份 Windows 计算机](backup-windows-with-mars-agent.md)
