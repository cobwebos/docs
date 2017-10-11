## <a name="prepare-your-raspberry-pi"></a>准备树莓 Pi

### <a name="install-raspbian"></a>安装 Raspbian

如果这是首次使用 Raspberry Pi，你需要安装工具包中包含 SD 卡上使用 NOOBS Raspbian 操作系统。 [Raspberry Pi 软件指南][ lnk-install-raspbian]介绍如何在 Raspberry Pi 上安装操作系统。 本教程假定你已在 Raspberry Pi 上安装 Raspbian 操作系统。

> [!NOTE]
> 包含在 SD 卡[Microsoft Azure IoT Starter Kit for Raspberry Pi 3] [ lnk-starter-kits]已 NOOBS 安装。 可以启动此卡 Raspberry Pi，还可以选择安装 Raspbian OS。

若要完成硬件设置，你需要：

- 连接到包含工具包中的电源 Raspberry Pi。
- 连接到网络使用你工具包中包含的以太网电缆 Raspberry Pi。 或者，你可以设置[无线连接][ lnk-pi-wireless] Raspberry pi。

你现在已经完成 Raspberry Pi 的硬件设置。

### <a name="sign-in-and-access-the-terminal"></a>登录并访问终端

可以访问在 Raspberry Pi 上的终端环境的两个选项：

- 如果你有键盘和监视器连接到 Raspberry Pi，可以使用 Raspbian GUI 访问终端窗口。

- 访问从台式计算机使用 SSH 你 Raspberry Pi 上的命令行。

#### <a name="use-a-terminal-window-in-the-gui"></a>使用在 GUI 中的终端窗口

Raspbian 的默认凭据是用户名**pi**和密码**raspberry**。 在 GUI 中的任务栏中，你可以启动**终端**实用工具使用如下所示监视器的图标。

#### <a name="sign-in-with-ssh"></a>使用 SSH 登录

可用于 SSH 的命令行访问到 Raspberry Pi。 文章[SSH (Secure Shell)] [ lnk-pi-ssh]描述如何配置 SSH 上你 Raspberry Pi，以及如何从连接[Windows] [ lnk-ssh-windows]或[Linux 和 Mac OS][lnk-ssh-linux]。

使用用户名登录**pi**和密码**raspberry**。

#### <a name="optional-share-a-folder-on-your-raspberry-pi"></a>可选： 共享 Raspberry Pi 上的文件夹

（可选） 你可能想要与你的桌面环境中共享 Raspberry Pi 上的文件夹。 共享文件夹，可使用你首选的桌面文本编辑器 (如[Visual Studio Code](https://code.visualstudio.com/)或[Sublime Text](http://www.sublimetext.com/)) 编辑文件上而不是使用你 Raspberry Pi`nano`或`vi`。

若要使用 Windows 共享的文件夹，请 Raspberry Pi 上配置 Samba 服务器。 或者，使用内置[SFTP](https://www.raspberrypi.org/documentation/remote-access/)在桌面上的 SFTP 客户端的服务器。

[lnk-install-raspbian]: https://www.raspberrypi.org/learning/software-guide/quickstart/
[lnk-pi-wireless]: https://www.raspberrypi.org/documentation/configuration/wireless/README.md
[lnk-pi-ssh]: https://www.raspberrypi.org/documentation/remote-access/ssh/README.md
[lnk-ssh-windows]: https://www.raspberrypi.org/documentation/remote-access/ssh/windows.md
[lnk-ssh-linux]: https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md
[lnk-starter-kits]: https://azure.microsoft.com/develop/iot/starter-kits/