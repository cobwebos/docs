---
title: 将非 Azure 计算机连接到 Azure 安全中心
description: 了解如何将非 Azure 计算机连接到安全中心
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 82b8161f92f337002e9d8bbdc45cd53d5921fc00
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91280668"
---
#  <a name="connect-your-non-azure-machines-to-security-center"></a>将非 Azure 计算机连接到安全中心

安全中心可以监视非 Azure 计算机的安全状态，但首先需要载入这些资源。 你可以从 " **入门** " 页或从 **库存** 中添加非 Azure 计算机，如下所述。

## <a name="add-non-azure-computers"></a>添加非 Azure 计算机 

1. 在安全中心的菜单中，打开 " **入门** " 页。
1. 选择“入门”选项卡。

    :::image type="content" source="./media/security-center-onboarding/onboarding-get-started-tab.png" alt-text=""入门" 页中的 "入门" 选项卡" lightbox="./media/security-center-onboarding/onboarding-get-started-tab.png":::

1. 在 " **添加非 Azure 服务器**" 下，选择 " **配置** "。

    > [!TIP]
    > 你还可以从 **清单** 页面的 " **添加非 Azure 服务器** " 按钮打开 "添加计算机"。

    此时将显示 Log Analytics 工作区的列表。 该列表包含启用自动预配时由安全中心创建的默认工作区（如果适用）。 选择此工作区或要使用的其他工作区。

    可以将计算机添加到现有的工作区，也可以新建一个工作区。 

1. （可选）若要创建新的工作区，请选择 "  **创建新工作区**"。

1. 从工作区列表中，选择 "为相关工作区 **添加服务器** "。
    此时将显示 " **代理管理** " 页。

    从此处选择以下相关过程，具体取决于要载入的计算机类型：

    - [载入 Azure Stack Vm](#onboard-your-azure-stack-vms)
    - [载入 Linux 计算机](#onboard-your-linux-machines)
    - [载入你的 Windows 计算机](#onboard-your-windows-machines)


### <a name="onboard-your-azure-stack-vms"></a>载入 Azure Stack Vm
若要添加 Azure Stack Vm，你需要 " **代理管理** " 页上的信息，并在 Azure Stack 上运行的虚拟机上配置 **Azure Monitor、更新和配置管理** 虚拟机扩展。
1. 从 " **代理管理** " 页上，将 " **工作区 ID** " 和 **"主密钥" 复制到记事本** 中。
1. 登录到 **Azure Stack** 门户并打开 " **虚拟机** " 页。
1. 选择要用安全中心保护的虚拟机。
    >[!TIP]
    > 有关如何在 Azure Stack 上创建虚拟机的信息，请参阅[适用于 Windows 虚拟机的此快速入门](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal)，或[适用于 Linux 虚拟机的此快速入门](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal)。
1. 选择“扩展”。 此时将显示此虚拟机上安装的虚拟机扩展列表。
1. 选择 " **添加** " 选项卡。" **新建资源** " 菜单显示可用虚拟机扩展的列表。
1. 选择 **Azure Monitor、更新和配置管理** 扩展，然后选择 " **创建**"。 此时将打开 " **安装扩展** 配置" 页。
    >[!NOTE]
    > 如果在市场中没有看到“Azure Monitor、更新和配置管理”扩展，请联系 Azure Stack 操作员以将其列出  。
1. 在 " **安装扩展** 配置" 页上，粘贴在上一步中复制到记事本中的 **工作区 ID** 和 **工作区密钥 (主密钥) ** 。
1. 完成配置后，选择 **"确定"**。 扩展的状态将显示为 " **预配已成功**"。 虚拟机可能需要长达一小时的时间才能显示在安全中心。


### <a name="onboard-your-linux-machines"></a>载入 Linux 计算机
若要添加 Linux 计算机，需要从 " **代理" 管理** 页中的 WGET 命令。
1. 从 " **代理管理** " 页，将 **WGET** 命令复制到记事本中。 将此文件保存到可以从你的 Linux 计算机访问的位置。
1. 在 Linux 计算机上，打开包含 WGET 命令的文件。 选择整个内容，并将其复制并粘贴到终端控制台。
1. 安装完成后，可以通过运行*pgrep*命令来验证是否已安装*omsagent* 。 命令将返回 *omsagent* PID。
    可以在以下位置找到该代理的日志： */var/opt/microsoft/omsagent/ \<workspace id> /log/* ，新的 Linux 计算机可能需要长达30分钟的时间才能显示在安全中心。


### <a name="onboard-your-windows-machines"></a>载入你的 Windows 计算机
若要添加 Windows 计算机，你需要 " **代理管理** " 页上的信息，并 (32/64 位) 下载适当的代理文件。
1. 选择适用于计算机处理器类型的“下载 Windows 代理”链接，以下载安装程序文件。
1. 从 " **代理管理** " 页上，将 " **工作区 ID** " 和 **"主密钥" 复制到记事本** 中。
1. 将已下载的安装程序文件复制到目标计算机并运行该文件。
1. 按照安装向导 (**接下来**，**我同意**，**接下来) ****下一**步。
    1. 在 " **Azure Log Analytics** " 页上，将 **工作区 ID** 和 **工作区密钥)  (** 复制到记事本中。
    1. 如果计算机应向 Azure 政府云中的 Log Analytics 工作区报告，请从 " **Azure 云**" 下拉列表中选择 " **azure 美国政府**"。
    1. 如果计算机需要通过代理服务器来与 Log Analytics 服务通信，请选择“高级”并提供代理服务器的 URL 和端口号。
    1. 输入所有配置设置后，选择 " **下一步**"。
    1. 在 " **准备安装** " 页上，查看要应用的设置，并选择 " **安装**"。
    1. 在“配置已成功完成”页上，选择“完成”。

完成后，**Log Analytics 代理**会显示在“控制面板”中。 可以在该处检查配置，并验证代理是否已连接。

有关安装和配置代理的详细信息，请参阅 [连接 Windows 计算机](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard)。


## <a name="verifying"></a>验证
祝贺！ 现在，你可以在同一个位置查看 Azure 和非 Azure 计算机。 打开 " [资产清单" 页](asset-inventory.md) ，并筛选到相关的资源类型。 这两个图标区分类型：

  ![icon1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) 非 Azure 计算机

  ![icon2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Azure VM


## <a name="next-steps"></a>后续步骤

本页介绍如何将非 Azure 计算机添加到 Azure 安全中心。 若要监视其状态，请使用以下页面中介绍的清单工具：

- [利用资产清单和管理工具浏览和管理资源](asset-inventory.md)