---
title: 在 Azure 实验室服务中启用适用于 Linux 的图形远程桌面 | Microsoft Docs
description: 了解如何在 Azure 实验室服务的实验室中启用适用于 Linux 虚拟机的远程桌面。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4f50510adf978aeb63b2b5337d21e6f9dec76196
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445757"
---
# <a name="enable-graphical-remote-desktop-for-linux-virtual-machines-in-azure-lab-services"></a>在 Azure 实验室服务中启用适用于 Linux 虚拟机的图形远程桌面
本文演示如何完成以下任务：

- 为 Linux VM 启用图形远程桌面会话
- 如何使用 RDP（远程桌面协议）或 X2Go 远程桌面客户端连接到 Linux VM

## <a name="set-up-graphical-remote-desktop-solution"></a>设置图形远程桌面解决方案
从 Linux 映像创建实验室时，将自动配置 SSH（安全外壳）访问权限，以便讲师可以使用 SSH 从命令行连接到模板 VM。  同样，发布模板 VM 后，学生也可以使用 SSH 连接到其 VM。

若要使用 GUI（图形用户界面）连接到 Linux VM，建议使用 RDP 或 X2Go。  这两个选项均要求讲师对模板 VM 进行一些其他设置：

### <a name="rdp-setup"></a>RDP 设置
若要使用 RDP，讲师必须：
  - 启用远程桌面连接；这是打开用于 RDP 的 VM 端口所特别需要的。
  - 安装 RDP 远程桌面服务器。
  - 安装 Linux 图形桌面环境（例如 MATE、XFCE 等）。

### <a name="x2go-setup"></a>X2Go 设置
若要使用 X2Go，讲师必须：
- 安装 X2Go 远程桌面服务器。
- 安装 Linux 图形桌面环境（例如 MATE、XFCE 等）。

X2Go 使用已为 SSH 启用的相同端口。  因此，无需进行额外配置即可在 VM 上为 X2Go 打开端口。

> [!NOTE]
> 在某些情况下，例如使用 Ubuntu LTS 18.04 时，X2Go 可提供更好的性能。  如果使用 RDP 并在与图形桌面环境交互时注意到延迟，请考虑尝试 X2Go，因为它可能会提高性能。

> [!IMPORTANT]
>  一些市场映像已经安装了图形桌面环境和远程桌面服务器。  例如，[适用于 Linux (Ubuntu) 的 Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) 已安装 [XFCE 和 X2Go 服务器，并配置为接受客户端连接](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro#x2go)。

## <a name="enable-remote-desktop-connection-for-rdp"></a>为 RDP 启用远程桌面连接

仅在使用 RDP 进行连接时需要执行此步骤。  如果计划使用 X2Go，则可以跳到下一部分，因为 X2Go 使用 SSH 端口。

1.  在实验室创建期间，讲师可以选择“启用远程桌面连接”。  讲师必须**启用**此选项才能打开 Linux VM 上 RDP 远程桌面会话所需的端口。  否则，如果禁用此选项，则仅会打开用于 SSH 的端口。
  
    ![为 Linux 映像启用远程桌面连接](./media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

2. 在“启用远程桌面连接”消息框中，选择“继续启用远程桌面”。 

    ![为 Linux 映像启用远程桌面连接](./media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

## <a name="install-rdp-or-x2go"></a>安装 RDP 或 X2Go

创建实验室后，讲师需要确保已在模板 VM 上安装图形桌面环境和远程桌面服务器。  讲师必须先使用 SSH 连接到模板 VM，然后才能安装以下包：
- RDP 或 X2Go 远程桌面服务器。
- 图形桌面环境，例如 MATE、XFCE 等。

完成此设置后，讲师可以使用 Microsoft 远程桌面 (RDP) 客户端或 X2Go 客户端连接到模板 VM。

请按照以下步骤设置模板 VM：

1. 如果在工具栏上看到“自定义模板”，请选择它。 然后，在“自定义模板”对话框中选择“继续”。 此操作会启动模板 VM。  

    ![自定义模板](./media/how-to-enable-remote-desktop-linux/customize-template.png)
1. 在模板 VM 启动后，你可以在工具栏上选择“连接到模板”，然后选择“通过 SSH 进行连接”。 

    ![创建实验室后，通过 RDP 连接到模板](./media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
1. 你会看到下面的“连接到虚拟机”对话框。 选择文本框旁的“复制”按钮，将其复制到剪贴板。 保存 SSH 连接信息。 从 SSH 终端（如 [Putty](https://www.putty.org/)）使用此信息连接到虚拟机。
 
    ![SSH 连接字符串](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. 安装 RDP 或 X2Go 以及所选的图形桌面环境。  请参阅以下说明：
    - [安装和配置 RDP](https://docs.microsoft.com/azure/virtual-machines/linux/use-remote-desktop)
    - [安装和配置 X2Go](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/X2GoRemoteDesktop)

## <a name="connect-to-the-template-vm-via-the-gui"></a>通过 GUI 连接到模板 VM

设置模板 VM 后，讲师可以使用 Microsoft 远程桌面 (RDP) 客户端或 X2Go 客户端通过 GUI 进行连接。  使用的客户端取决于是将 RDP 还是 X2Go 配置为模板 VM 上的远程桌面服务器。  

### <a name="microsoft-remote-desktop-rdp-client"></a>Microsoft 远程桌面 (RDP) 客户端

Microsoft 远程桌面 (RDP) 客户端用于连接到已配置 RDP 的模板 VM。  远程桌面客户端可用于 Windows、Chromebook、Mac 等设备。  有关更多详细信息，请参阅有关[远程桌面客户端](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)的文章。

根据用于连接模板 VM 的计算机的类型，执行以下步骤：

- Windows
  1. 单击实验室工具栏上的“连接到模板”，然后选择“通过 RDP 连接”，连接到模板 VM。 
  1. 保存 RDP 文件，并使用它通过远程桌面客户端连接到模板 VM。 
  1. 通常情况下，远程桌面客户端已在 Windows 上安装和配置。  因此，只需单击 RDP 文件将其打开并启动远程会话即可。

- Mac
  1. 单击实验室工具栏上的“连接到模板”，然后选择“通过 RDP 连接”，保存 RDP 文件。  
  1. 然后，请参阅操作说明文章[在 Mac 上使用 RDP 连接到 VM](connect-virtual-machine-mac-remote-desktop.md)。

- Chromebook
  1. 单击实验室工具栏上的“连接到模板”，然后选择“通过 RDP 连接”，保存 RDP 文件。  
  1. 然后，请参阅操作说明文章[在 Chromebook 上使用 RDP 连接到 VM](connect-virtual-machine-chromebook-remote-desktop.md)。

### <a name="x2go-client"></a>X2Go 客户端

X2Go 客户端用于连接到配置了 X2Go 的模板 VM。  使用模板 VM 的 SSH 连接信息，按照操作说明文章[使用 X2Go 连接到 VM](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-x2go) 中的步骤操作。

## <a name="next-steps"></a>后续步骤
讲师在其模板 VM 上设置 RDP 或 X2Go 并发布后，学生可以通过 GUI 远程桌面或 SSH 连接到其 VM。

有关详细信息，请参阅：
 - [连接到 Linux VM](how-to-use-remote-desktop-linux-student.md)
