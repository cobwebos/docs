---
title: 在 Azure 实验室服务中连接到 Linux VM | Microsoft Docs
description: 了解如何在 Azure 实验室服务的实验室中使用适用于 Linux 虚拟机的远程桌面。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d13868477ff2e3378d87d7785789a7498ed17e59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85443411"
---
# <a name="connect-to-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>在 Azure 实验室服务的课堂实验室中连接到 Linux 虚拟机
本文说明了学生如何使用以下方式连接到实验室中的 Linux 虚拟机 (VM)：
- SSH（安全外壳协议）终端
- GUI（图形用户界面）远程桌面

> [!IMPORTANT] 
> 自动配置 SSH，使学生和讲师都可以通过 SSH 连接到 Linux VM，而无需进行任何其他设置。 但是，如果学生需要使用 GUI 远程桌面连接，则讲师可能需要执行其他设置。  有关详细信息，请参阅[启用适用于 Linux 虚拟机的远程桌面](how-to-enable-remote-desktop-linux.md)。

## <a name="connect-to-the-student-vm-using-ssh"></a>使用 SSH 连接到学生 VM

1. 当学生直接 (`https://labs.azure.com`) 或使用注册链接 (`https://labs.azure.com/register/<registrationCode>`) 登录实验室门户后，会显示该学生有权访问的每个实验室的磁贴。 
   
1. 在磁贴上，如果 VM 处于停止状态，则切换按钮以启动 VM。 

2. 选择“连接”。 你会看到两个用于连接到 VM 的选项：SSH 和 RDP。

    ![学生 VM - 连接选项](./media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

3. 选择“SSH”选项后，就会看到以下“连接到虚拟机”对话框：  

    ![SSH 连接字符串](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. 单击文本框旁边的“复制”按钮，将 SSH 连接信息复制到剪贴板。 

5. 例如，将 SSH 连接信息保存在文本板中，方便你在下一步中使用此连接信息。

6. 从 SSH 终端（如 [Putty](https://www.putty.org/)）连接到 VM。

## <a name="connect-to-the-student-vm-using-gui-remote-desktop"></a>使用 GUI 远程桌面连接到学生 VM
讲师可以选择配置 VM，以便学生还可以使用 GUI 远程桌面建立连接。  在这种情况下，学生需要从讲师那里了解是使用 Microsoft 远程桌面 (RDP) 还是 X2Go  客户端应用程序连接到其 VM。  这两个应用程序都允许学生远程连接到其 VM，并允许他们在本地计算机上显示 Linux 图形桌面。

### <a name="connect-to-the-student-vm-using-microsoft-remote-desktop-rdp"></a>使用 Microsoft 远程桌面 (RDP) 连接到学生 VM
在讲师使用适用于 Linux 图形桌面环境（如 MATE、XFCE 等）的 RDP 和 GUI 包设置实验室之后，学生可以使用 Microsoft 远程桌面 (RDP) 连接到他们的 Linux VM。 请使用以下步骤进行连接： 

1. 在 VM 的磁贴上，确保 VM 正在运行，并单击“连接”。 你会看到两个用于连接到 VM 的选项：SSH 和 RDP。

    ![学生 VM - 连接选项](./media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)
2. 选择“RTMP”选项。  将 RDP 文件下载到计算机上后，将其保存到 VM。

3. 如果要从 Windows 计算机连接，通常是已安装并配置了 Microsoft 远程桌面 (RDP) 客户端。  因此，只需单击 RDP 文件将其打开并启动远程会话即可。

    如果要从 Mac 或 Chromebook 进行连接，请参阅以下步骤：
   - [在 Mac 上使用 RDP 连接到 VM](connect-virtual-machine-mac-remote-desktop.md)。
   - [在 Chromebook 上使用 RDP 连接到 VM](connect-virtual-machine-chromebook-remote-desktop.md)。  

### <a name="connect-to-the-student-vm-using-x2go"></a>使用 X2Go 连接到学生 VM
在讲师使用适用于 Linux 图形桌面环境（如 MATE、XFCE 等）的 X2Go 和 GUI 包设置实验室之后，学生可以使用 X2Go 连接到他们的 Linux VM。

学生需要询问讲师他们安装了哪些 Linux 图形桌面环境。  在使用 X2Go 客户端进行连接的后续步骤中需要用到此信息。

1. 在本地计算机上安装 [X2Go 客户端](https://wiki.x2go.org/doku.php/doc:installation:x2goclient)。

1. 按照[第一部分](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-ssh)中的说明复制 VM 的 SSH 连接信息。  使用 X2Go 客户端进行连接时需要用到此信息。

1. 获得 SSH 连接信息后，打开 X2Go 客户端，然后选择“会话” > “新建会话”。
   ![X2Go 创建新会话](./media/how-to-use-classroom-lab/x2go-new-session.png)

1. 根据 SSH 连接信息，在“会话首选项”窗格中输入值。  例如，连接信息如下所示：

    ```bash
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
    ```

    使用本例，输入以下值：

   - 会话名称 - 指定名称，例如 VM 的名称。
   - 主机 - VM 的 ID；例如 **`ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`** 。
   - 登录 - VM 的用户名；例如 student。
   - SSH 端口 - 分配给 VM 的唯一端口；例如 12345。
   - 会话类型 - 选择讲师为 VM 配置的 Linux 图形桌面环境。  你需要从讲师那里获取上述信息。

    最后，单击“确定”即可创建会话。

    ![X2Go 会话首选项](./media/how-to-use-classroom-lab/x2go-session-preferences.png)

1.  在右侧窗格中单击会话。

    ![X2Go 启动新会话](./media/how-to-use-classroom-lab/x2go-start-session.png)

    > [!NOTE] 
    > 如果系统显示类似的消息，请选择“是”继续输入密码：无法建立主机“[`00000000-0000-0000-0000-000000000000.eastus2.cloudapp.eastus.cloudapp.azure.com`]:12345”的验证。ECDSA 密钥指纹为 SHA256:00000000000000000000000000000000000000000000。是否确实要继续连接（是/否）？

2. 根据提示输入密码，然后单击“确定”。  现在，你将远程连接到 VM 的 GUI 桌面环境。

## <a name="next-steps"></a>后续步骤
若要了解如何在课堂实验室中为 Linux VM 启用远程桌面连接功能，请参阅[为 Linux 虚拟机启用远程桌面](how-to-enable-remote-desktop-linux.md)。 

