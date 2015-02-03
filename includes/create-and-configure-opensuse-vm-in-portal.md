<properties writer="kathydav" editor="tysonn" manager="jeffreyg" /> 

**重要**：如果您希望虚拟机使用虚拟网络，请确保在创建虚拟机时指定虚拟网络。仅当创建虚拟机后，才能将该虚拟机配置为加入虚拟网络。有关虚拟网络的详细信息，请参阅[Azure 虚拟网络概述](http://go.microsoft.com/fwlink/p/?LinkID=294063)。


1. 登录到[Azure 管理门户][AzurePreviewPortal]使用你的 Azure 帐户。

2. 在管理门户中，在左下角的网页上，单击**+ 新建**，单击**虚拟机**，然后单击**从库**。

	![Create a New Virtual Machine][Image1]

3. 选择一个 OpenSUSE 虚拟机映像从**平台映像**，然后单击右下角的页上的下一步箭头。


4. 在上**虚拟机配置**页上，提供以下信息：

	- 提供**虚拟机名称**，例如"testlinuxvm"。
	- 指定**新用户名**，例如"newuser"将添加到 Sudoers 列表文件。
	- 在**新密码**框中，键入[强密码](http://msdn.microsoft.com/zh-cn/library/ms161962.aspx)。
	- 在**确认密码**框中，再次键入该密码。
	- 选择适当的**大小**从下拉列表。

	单击下一步箭头以继续。

5. 在**虚拟机模式**页上，提供以下信息：
	- 选择**独立虚拟机**。
	- 在**DNS 名称**框中，键入有效的 DNS 地址。例如"testlinuxvm"。
	- 在**区域/地缘组/虚拟网络**框中，选择将承载此虚拟映像的区域。

   单击下一步箭头以继续。
	
6. 在上**虚拟机选项**页上，选择**(无)**中**可用性集**框。单击复选标记以继续。
	
7. 请等候 Azure 准备你的虚拟机。

##配置终结点
创建虚拟机后，您必须配置终结点才能进行远程连接。

1. 在管理门户中，单击**虚拟机**，然后单击新的虚拟机的名称，然后单击**终结点**。

2. 单击**编辑终结点**底部的页上，并编辑 SSH 终结点，以便其**公用端口**为 22。

##连接到虚拟机
当设置虚拟机和配置终结点后，可以使用 SSH 或 PuTTY 连接到虚拟机。

###使用 SSH 进行连接
如果您使用的是 Linux 计算机，请使用 SSH 连接到 VM。在命令提示符处，运行：

	$ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180

输入用户的密码。

###使用 PuTTY 进行连接
如果您使用的是 Windows 计算机，请使用 PuTTY 连接到 VM。可以从下载 puTTY [PuTTY 下载页][PuTTYDownLoad]。 

1. 将 **putty.exe** 下载并保存到您的计算机上的某个目录。打开命令提示符，导航到该文件夹，然后执行 **putty.exe**。

2. 输入 "testlinuxvm.cloudapp.net" **主机名**和 "22" **端口**。
![PuTTY Screen][Image6]  

##更新虚拟机（可选）
1. 在连接到虚拟机后，您可以选择安装系统更新和修补程序。运行：

	`$ sudo zypper update`

2. 选择**软件**然后**在线更新**。将显示更新列表。选择**接受**来开始安装和应用的所有新修补程序 (除外可选），当前可供您的系统。 

3. 安装完成后，请选择**完成**。您的系统现在已为最新。

[PuTTYDownload]: http://www.puttyssh.org/download.html
[AzurePreviewPortal]: http://manage.windowsazure.com

[Image1]: ./media/create-and-configure-opensuse-vm-in-portal/CreateVM.png




[Image6]: ./media/create-and-configure-opensuse-vm-in-portal/putty.png
