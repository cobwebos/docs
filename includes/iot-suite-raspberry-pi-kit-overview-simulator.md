## <a name="overview"></a>概述

在本教程中，你将完成以下步骤：

- 远程监视的预配置解决方案的实例部署到你的 Azure 订阅。 此步骤会自动部署并配置多个 Azure 服务。
- 将你的设备设置为与你的计算机和远程监视解决方案通信。
- 更新设备下面的代码示例连接到远程监视解决方案，并将你可以在解决方案面板查看的模拟的遥测。

## <a name="prerequisites"></a>必备条件

若要完成本教程，你需要有效的 Azure 订阅。

> [!NOTE]
> 如果你没有帐户，可以在几分钟内创建一个免费试用帐户。 有关详细信息，请参阅[Azure 免费试用版][lnk-free-trial]。

### <a name="required-software"></a>所需软件

你需要在台式计算机以使你能够远程访问 Raspberry Pi 上的命令行上的 SSH 客户端。

- Windows 不包括 SSH 客户端。 我们建议使用[PuTTY](http://www.putty.org/)。
- 大多数 Linux 分发和 Mac OS 包括命令行的 SSH 实用工具。 有关详细信息，请参阅[SSH 使用 Linux 或 Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md)。

### <a name="required-hardware"></a>所需的硬件

若要使您能够远程连接到 Raspberry Pi 上的命令行台式计算机。

[Microsoft IoT Starter Kit for Raspberry Pi 3] [ lnk-starter-kits]或等效的组件。 本教程使用工具包中的以下项目：

- 树莓 Pi 3
- MicroSD 卡 （与 NOOBS)
- USB 迷你电缆
- 以太网电缆

[lnk-starter-kits]: https://azure.microsoft.com/develop/iot/starter-kits/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/