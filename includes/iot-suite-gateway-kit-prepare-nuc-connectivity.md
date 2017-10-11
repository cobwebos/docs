## <a name="prepare-your-intel-nuc"></a>准备你 Intel NUC

若要完成硬件设置，你需要：

- 连接到电源工具包中包含的 Intel NUC。
- 连接到网络使用以太网电缆 Intel NUC。

你现在已经完成你 Intel NUC 网关设备的硬件设置。

### <a name="sign-in-and-access-the-terminal"></a>登录并访问终端

可以访问在 Intel NUC 上的终端环境的两个选项：

- 如果你有键盘和显示器连接到 Intel NUC，你可以直接访问命令行界面。 默认凭据是用户名**根**和密码**根**。

- 访问从台式计算机使用 SSH 你 Intel NUC 上的外壳。

#### <a name="sign-in-with-ssh"></a>使用 SSH 登录

若要使用 SSH 登录，你需要 Intel NUC 的 IP 地址。 如果你有键盘和显示器连接到 Intel NUC，使用`ifconfig`命令来查找 IP 地址。 此外，连接到你的路由器列出网络上的设备的地址。

使用用户名登录**根**和密码**根**。

#### <a name="optional-share-a-folder-on-your-intel-nuc"></a>可选： 共享 Intel NUC 上的文件夹

（可选） 你可能想要与你的桌面环境中共享 Intel NUC 上的文件夹。 共享文件夹，可使用你首选的桌面文本编辑器 (如[Visual Studio Code](https://code.visualstudio.com/)或[Sublime Text](http://www.sublimetext.com/)) 编辑文件上而不是使用你 Intel NUC`nano`或`vi`。

要与 Windows 共享文件夹，请在 Intel NUC 上配置的 Samba 服务器。 或者，在 Intel NUC 台式计算机上的 SFTP 客户端上使用 SFTP 服务器。
