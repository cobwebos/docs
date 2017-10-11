1. 登录到 [Azure 经典门户](http://manage.windowsazure.com)。  
2. 在窗口底部的命令栏中，单击**新建**。
3. 下**计算**，单击**虚拟机**，然后单击**从库**。
   
    ![创建新的虚拟机][Image1]
4. 下**SUSE**组，选择一个 OpenSUSE 虚拟机映像，，然后单击箭头以继续。
5. 在第一个**虚拟机配置**页：
   
   * 键入一个**虚拟机名称**，例如"testlinuxvm"。 名称必须介于 3 到 15 个字符，可包含字母、 数字和连字符，且必须以字母开头和以字母或数字结尾。
   * 验证**层**和选取**大小**。 层决定你可以从选择的大小。 大小会影响使用它，也可以如多少个数据磁盘的配置选项，可以附加的成本。 有关详细信息，请参阅[虚拟机的大小](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
   * 键入一个**新用户名**，或接受默认值， **azureuser**。 该名称将添加到 Sudoers 列表文件。
   * 决定哪种类型的**身份验证**使用。 有关一般密码指南，请参阅[强密码](http://msdn.microsoft.com/library/ms161962.aspx)。
6. 在下次**虚拟机配置**页：
   
   * 使用默认**创建新的云服务**。
   * 在**DNS 名称**框中，键入要使用的地址，例如"testlinuxvm"的一部分的唯一 DNS 名称。
   * 在**区域/地缘组/虚拟网络**框中，选择将托管此虚拟映像的区域。
   * 下**终结点**，保留 SSH 终结点。 你可以添加其他现在，或添加、 更改或创建虚拟机后删除它们。
     
     > [!NOTE]
     > 如果你希望虚拟机使用虚拟网络，你**必须**当你创建虚拟机时指定虚拟网络。 在创建虚拟机后，你无法将虚拟机添加到虚拟网络。 有关详细信息，请参阅[虚拟网络概述](../articles/virtual-network/virtual-networks-overview.md)。
     > 
     > 
7. 在最后一个**虚拟机配置**页上，保留默认设置，然后单击复选标记以完成。

门户将列出这些出新虚拟机**虚拟机**。 时的状态报告为**（预配）**，正在设置虚拟机。 当状态报告为**运行**，你可以转到下一步。

## <a name="connect-to-the-virtual-machine"></a>连接到虚拟机
你将使用 SSH 或 PuTTY 连接到虚拟机，具体取决于你将从连接的计算机上的操作系统：

* 从运行 Linux 的计算机，使用 SSH。 在命令提示符处，键入：
  
    `$ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180`
  
    键入用户的密码。
* 从运行 Windows 的计算机，使用 PuTTY。 如果你没有安装它，将其从下载[PuTTY 下载页][PuTTYDownload]。
  
    保存**putty.exe**到你的计算机上的目录。 打开命令提示符，导航到该文件夹，并运行**putty.exe**。
  
    键入主机名，例如"testlinuxvm.cloudapp.net"，然后键入"22"**端口**。
  
    ![PuTTY 屏幕][Image6]  

## <a name="update-the-virtual-machine-optional"></a>更新虚拟机 （可选）
1. 你已连接到虚拟机后，你可以选择安装系统更新和修补程序。 若要运行更新，请键入：
   
    `$ sudo zypper update`
2. 选择**软件**，然后**联机更新**若要列出可用的更新。 选择**接受**以开始安装，并应用所有新可用的修补程序 （除可选的）。
3. 安装完成后，选择**完成**。  现在，你的系统是最新。

[PuTTYDownload]: http://www.puttyssh.org/download.html

[Image1]: ./media/create-and-configure-opensuse-vm-in-portal/CreateVM.png

[Image6]: ./media/create-and-configure-opensuse-vm-in-portal/putty.png
