---
title: "如何创建 Azure RemoteApp 的自定义模板映像 | Microsoft Docs"
description: "了解如何创建 Azure RemoteApp 的自定义模板映像。 可以将此模板和混合或云集合一起使用。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
editor: 
ms.assetid: b9ec5b51-f7cd-470b-8545-d0fd714c5982
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: c5b87c3a814b816e8bdc28d3e22b64c2197579af
ms.lasthandoff: 03/31/2017


---
# <a name="how-to-create-a-custom-template-image-for-azure-remoteapp"></a>如何创建 Azure RemoteApp 的自定义模板映像
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

Azure RemoteApp 使用 Windows Server 2012 R2 模板映像托管所有要与用户共享的项目。 若要创建自定义 RemoteApp 模板映像，可以由现有映像开始或者新建一个映像。 

> [!TIP]
> 你是否知道你可以从 Azure VM 创建映像？ 千真万确，并且这样可以节省导入映像所需的时间。 在 [此处](remoteapp-image-on-azurevm.md) 查看步骤。
> 
> 

可以上载和 Azure RemoteApp 一起使用的映像要求为：

* 映像大小应是 MB 的倍数。 如果尝试上载的映像大小不是准确的倍数，则上载会失败。
* 映像大小必须为 127 GB 或更小。
* 其必须位于 VHD 文件（当前不支持VHDX 文件 [Hyper-V 虚拟硬盘]。
* VHD 不能为第 2 代虚拟机。
* VHD 可以是固定大小或可动态扩展的。 建议使用动态扩展的 VHD，因为其上载到 Azure 所需的时间少于固定大小的 VHD 文件所需的时间。
* 磁盘必须使用主启动记录 (MBR) 分区类型初始化。 不支持 GUID 分区表 (GPT) 分区类型。
* VHD 必须包含 Windows Server 2012 R2 的单个安装程序。 其可以包含多个卷，但仅能包含一个含有 Windows 安装程序的卷。
* 必须安装远程桌面会话主机 (RDSH) 角色和桌面体验功能。
* 一定*不要*安装远程桌面连接代理角色。
* 必须禁用加密文件系统 (EFS)。
* 映像必须使用参数 **/oobe /generalize /shutdown** 进行 sysprep（请勿使用 **/mode:vm** 参数）。
* 不支持上载快照链中的 VHD。

**开始之前**

您需要在创建服务之前执行以下操作：

* [注册](https://azure.microsoft.com/services/remoteapp/) RemoteApp。
* 在 Active Directory 中创建要用作 RemoteApp 服务帐户的用户帐户。 限制此帐户的权限，以便它可以仅将计算机加入到域。 请参阅 [为 RemoteApp 配置 Azure Active Directory](remoteapp-ad.md) 了解详细信息。
* 收集有关你的本地网络的信息︰IP 地址信息和 VPN 设备详细信息。
* 安装 [Azure PowerShell](/powershell/azureps-cmdlets-docs) 模块。
* 收集有关你想要授予访问权限的用户的信息。 这可以是用户的 Microsoft 帐户信息，或 Active Directory 工作帐户信息。

## <a name="create-a-template-image"></a>创建模板映像
以下是从头开始创建新模板映像的高级别步骤：

1. 查找 Windows Server 2012 R2 更新 DVD 或 ISO 映像。
2. 创建 VHD 文件。
3. 安装 Windows Server 2012 R2。
4. 安装远程桌面会话主机 (RDSH) 角色和桌面体验功能。
5. 安装应用程序需要的其他功能。
6. 安装并配置应用程序。 专门在 **%systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs 中将要共享的任意应用或程序添加到映像的“**开始**”菜单，可以使共享应用更为简单。
7. 执行应用程序所需的其他所有 Windows 配置。
8. 禁用加密文件系统 (EFS)。
9. **要求：**转到 Windows 更新并安装所有重要更新。
10. 对映像进行 sysprep。

创建新映像的详细步骤是：

1. 查找 Windows Server 2012 R2 更新 DVD 或 ISO 映像。
2. 使用磁盘管理创建 VHD 文件。
   
   1. 启动磁盘管理 (diskmgmt.msc)。
   2. 创建大小为 40 GB 或更高的动态扩展 VHD。 （估计 Windows、应用程序和自定义需要的空间大小。 已安装 RDSH 角色和桌面体验功能的 Windows Server将需要 10 GB 的空间。）
      
      1. 单击“**操作 > 创建 VHD**”。
      2. 指定位置、大小和 VHD 格式。 选择“**动态扩展**”，然后单击“**确定**”。
      
      这会运行几秒钟的时间。 VHD 创建完成后，应该会在磁盘管理控制台中看到无任何驱动器号并且处于“未初始化”状态的新磁盘。
      
      * 右键单击该磁盘（非未分配空间），然后单击“**初始化磁盘**”。 选择“**MBR**”（主启动记录）作为分区类型，然后单击“**确定**”。
      * 创建新卷：右键单击未分配空间，然后单击“**新建简单卷**”。 可以接受向导中的默认设置，但是一定要分配驱动器号，以避免在上载模板映像时可能出现的问题。
      * 右键单击磁盘，然后单击“**分离 VHD**”。
3. 安装 Windows Server 2012 R2：
   
   1. 新建虚拟机。 在 Hyper-V 管理器或客户端 Hyper-V 中使用“新建虚拟机”向导。
      1. 在“指定代数”页面上，选择“**第 1 代**”。
      2. 在“连接虚拟硬盘”页面上，选择“**使用现有虚拟硬盘**”，然后浏览至在上一步中创建的 VHD。
      3. 在“安装选项”页面上，选择“**从引导 CD/DVD_ROM 安装操作系统**”，然后选择 Windows Server 2012 R2 安装媒体的位置。
      4. 在向导中选择安装 Windows 和应用程序需要的其它选项。 完成向导。
   2. 向导完成后，编辑虚拟机的设置和安装 Windows 和程序需要的其他任意更改（例如虚拟处理器的数量），然后单击“**确定**”。
   3. 连接到虚拟机并安装 Windows Server 2012 R2。
4. 安装远程桌面会话主机 (RDSH) 角色和桌面体验功能：
   1. 启动服务器管理器。
   2. 在“欢迎”屏幕或从“**管理**”菜单上单击“**添加角色和功能**”。
   3. 在“开始之前”页面上，单击“**下一步**”。
   4. 选择“**基于角色或基于功能的安装**”，然后单击“**下一步**”。
   5. 从列表中选择本地计算机，然后单击“**下一步**”。
   6. 选择“**远程桌面服务**”，然后单击“**下一步**”。
   7. 展开“**用户界面和结构**”，然后选择“**桌面体验**”。
   8. 单击“**添加功能**”，然后单击“**下一步**”。
   9. 在“远程桌面服务”页面上，单击“**下一步**”。
   10. 单击“**远程桌面会话主机**”。
   11. 单击“**添加功能**”，然后单击“**下一步**”。
   12. 在“确认安装选择”页面上，选择“**如果需要，自动重新启动目标服务器**”，然后在重新启动警告上单击“**是**”。
   13. 单击“安装” 。 计算机将重新启动。
5. 安装应用程序需要的其他功能，例如 .NET Framework 3.5。 若要安装功能，运行“添加角色和功能向导”。
6. 安装并配置要通过 RemoteApp 发布的程序和应用程序。

> [!IMPORTANT]
> 在安装应用程序前安装 RDSH 角色，以确保在将映像上载到 RemoteApp 前发现应用程序兼容性方面存在的问题。
> 
> 确保应用程序的快捷方式（**.lnk** 文件）显示在所有用户的“**开始**”菜单中 (%systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs)。 并确保在“**开始**”菜单中看到的图标是想让用户看到的内容。 如果不是，请更改。 （*不必*将应用程序添加到“开始”菜单，但是这样可以使在 RemoteApp 中发布应用程序更为简单。 否则，在发布应用时必须提供应用程序的安装路径。）
> 
> 

1. 执行应用程序所需的其他所有 Windows 配置。
2. 禁用加密文件系统 (EFS)。 在提升的命令窗口中运行以下命令：
   
     Fsutil behavior set disableencryption 1
   
   或者，可以在注册表中设置或添加以下 DWORD 值：
   
     HKLM\System\CurrentControlSet\Control\FileSystem\NtfsDisableEncryption = 1
3. 如果正在 Azure 虚拟机内生成映像，请重命名 **\%windir%\Panther\Unattend.xml** 文件，因为它会阻止稍后使用的上载脚本工作。 将该文件的名称更改为 Unattend.old，这样在需要还原部署时会仍具备此文件。
4. 转到 Windows 更新并安装所有重要更新。 可能需要多次运行 Windows 更新才能获取所有更新。 （有时安装了一个更新程序，而此更新本身需要进行更新。）
5. 对映像进行 sysprep。 在提升的命令提示符中运行以下命令：
   
   **C:\Windows\System32\sysprep\sysprep.exe /generalize /oobe /shutdown**
   
   **注意：**即使它是一个虚拟机，也不要使用 SYSPREP 命令的 **/mode:vm** 开关。

## <a name="next-steps"></a>后续步骤
既然已经拥有自定义模板映像，则需要将该映像上载到 RemoteApp 集合中。 使用下文中的信息创建自己的集合：

* [如何创建 RemoteApp 的混合集合](remoteapp-create-hybrid-deployment.md)
* [如何创建 RemoteApp 的云集合](remoteapp-create-cloud-deployment.md)


