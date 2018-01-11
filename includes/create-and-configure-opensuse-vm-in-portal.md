1. 登录到 [Azure 门户](http://portal.azure.com)。  
2. 单击“新建” > “计算” > “查看全部”，然后键入“SUSE”。
   
3. 选择一个 OpenSUSE 虚拟机映像，并单击箭头以继续。
5. 在第一个“虚拟机配置”页上：
   
   * 键入“虚拟机名称”，例如“testlinuxvm”。 该名称必须包含 3 至 15 个字符，只能包含字母、数字和连字符，必须以字母开头，并且必须以字母或数字结尾。
   * 验证“层”并选取“大小”。 层决定可以选择的大小。 大小会影响其使用成本，还会影响某些配置选项，例如，可以附加的数据磁盘数。 有关详细信息，请参阅[虚拟机大小](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
   * 键入“新用户名”，或接受默认值 **azureuser**。 该名称会添加到 Sudoers 列表文件中。
   * 确定要使用的“身份验证”类型。 有关一般性的密码指导原则，请参阅[强密码](http://msdn.microsoft.com/library/ms161962.aspx)。
6. 在下一个“虚拟机配置”页上执行以下操作：
   
   * 使用默认选项“创建新云服务”。
   * 在“DNS 名称”框中，键入唯一的 DNS 名称作为地址的一部分（例如“testlinuxvm”）。
   * 在“区域/地缘组/虚拟网络”框中，选择将托管此虚拟映像的区域。
   * 在“终结点”下，保留 SSH 终结点。 可以立即添加其他终结点，也可以在创建虚拟机之后添加、更改或删除终结点。
     
     > [!NOTE]
     > 如果希望虚拟机使用虚拟网络，则**必须**在创建虚拟机时指定虚拟网络。 创建虚拟机后，不能将它添加到虚拟网络中。 有关详细信息，请参阅[虚拟网络概述](../articles/virtual-network/virtual-networks-overview.md)。
     > 
     > 
7. 在最后一个“虚拟机配置”页上，保留默认设置，并单击复选标记以完成操作。

门户会在“虚拟机”下列出新虚拟机。 如果状态报告为“(正在预配)”，则表示正在设置虚拟机。 如果状态报告为“正在运行”，则可以继续执行下一个步骤。

## <a name="connect-to-the-virtual-machine"></a>连接到虚拟机
视将要连接的计算机上的操作系统而定，将使用 SSH 或 PuTTY 连接到虚拟机：

* 从运行 Linux 的计算机使用 SSH。 在命令提示符处，键入：
  
    `$ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180`
  
    键入用户的密码。
* 从运行 Windows 的计算机使用 PuTTY。 如果尚未安装，请从 [PuTTY 下载页][PuTTYDownload]下载。
  
    将 **putty.exe** 保存到计算机上的某个目录。 打开命令提示符，导航到该文件夹，并运行 **putty.exe**。
  
    键入主机名（例如“testlinuxvm.cloudapp.net”），并为“端口”键入“22”。
  
    ![PuTTY 屏幕][Image6]  

## <a name="update-the-virtual-machine-optional"></a>更新虚拟机（可选）
1. 在连接到虚拟机后，可以选择安装系统更新和修补程序。 若要运行更新，请键入：
   
    `$ sudo zypper update`
2. 选择“软件”，并选择“联机更新”列出可用的更新。 选择“接受”开始安装，并应用所有新的可用修补程序（可选修补程序除外）。
3. 安装完成后，选择“完成”。  系统现在已为最新。

[PuTTYDownload]: http://www.puttyssh.org/download.html

[Image6]: ./media/create-and-configure-opensuse-vm-in-portal/putty.png
