---
title: "PowerShell for StorSimple 设备管理 | Microsoft Docs"
description: "了解如何使用 Windows PowerShell for StorSimple 来管理你的 StorSimple 设备。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 0ff3bb0d-897a-4676-bdcb-402c2628dac5
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/18/2016
ms.author: alkohli@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: d07d1c838d99d0de0c5b62aaf42330b447df102c
ms.openlocfilehash: 2000ee9d88dc0f2f0f9046b1ef6b3d2a104e7d6b


---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>使用 Windows PowerShell for StorSimple 管理设备
## <a name="overview"></a>概述
Windows PowerShell for StorSimple 提供了一个可用于管理 Microsoft Azure StorSimple 设备的命令行接口。 顾名思义，该命令行接口是内置于某个受约束运行空间的一个基于 Windows PowerShell 的命令行接口。 在命令行上从用户的角度看，受约束运行空间显示为受限制的 Windows PowerShell 版本。 此命令行接口在保持 Windows PowerShell 的某些基本功能的同时，还提供附加的专用 cmdlet，它们都是专门针对管理 Microsoft Azure StorSimple 设备而设计的。 

本文介绍 Windows PowerShell for StorSimple 功能，其中包括如何连接到此命令行接口；本文还包含一些指向使用此命令行接口可以执行的循序渐进步骤或工作流的链接。 这些工作流包括：如何注册设备，配置设备上的网络接口，安装要求设备处于维护模式下的更新，更改设备状态，以及解决可能会遇到的任何问题。

阅读本文后，将能够：

* 使用 Windows PowerShell for StorSimple 连接到 StorSimple 设备。
* 使用 Windows PowerShell for StorSimple 管理 StorSimple 设备。
* 获取关于 Windows PowerShell for StorSimple 的帮助信息。

> [!NOTE]
> * 使用 Windows PowerShell for StorSimple cmdlet，你可以从某个串行控制台或通过 Windows PowerShell 远程处理以远程方式管理 StorSimple 设备。 有关可以在此命令行接口中使用的各个 cmdlet 的详细信息，请参阅 [Windows PowerShell for StorSimple 的 cmdlet 参考](https://technet.microsoft.com/library/dn688168.aspx)。
> * Azure PowerShell StorSimple cmdlet 不同于使你能够自动从命令行执行 StorSimple 服务级别和迁移任务的 cmdlet 集合。 有关适用于 StorSimple 的 Azure Powershell cmdlet 的详细信息，请参阅 [Azure StorSimple cmdlet 参考](https://msdn.microsoft.com/library/azure/dn920427.aspx)。
> 
> 

可以使用以下方法之一访问 Windows PowerShell for StorSimple：

* [连接到 StorSimple 设备串行控制台](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [使用 Windows PowerShell 远程连接到 StorSimple](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>通过设备串行控制台连接到 Windows PowerShell for StorSimple
可以[下载 PuTTY](http://www.putty.org/) 或类似的终端模拟软件来连接到 Windows PowerShell for StorSimple。 你需要专门配置 PuTTY 才能访问 Microsoft Azure StorSimple 设备。 以下主题包含有关如何配置 PuTTy 并连接到设备的详细步骤。 此外还介绍了串行控制台中的各个菜单选项。

### <a name="putty-settings"></a>PuTTY 设置
请确保使用以下 PuTTY 设置从串行控制台连接到 Windows PowerShell 界面。

#### <a name="to-configure-putty"></a>配置 PuTTY 的步骤
1. 在 PuTTY“**重新配置**”对话框中，在“**类别**”窗格内，选择“**键盘**”。
2. 请确保选中以下选项（启动新会话时，默认会选中这些选项）。 
   
   | 键盘项 | 选择 |
   | --- | --- |
   | Backspace 键 |Control-? (127) |
   | Home 和 End 键 |标准 |
   | 功能键和键盘 |ESC[n~ |
   | 光标键的初始状态 |一般 |
   | 数字小键盘的初始状态 |一般 |
   | 启用额外的键盘功能 |Control-Alt 与 AltGr 不同 |
   
    ![支持的 Putty 设置](./media/storsimple-windows-powershell-administration/IC740877.png)
3. 单击“应用” 。
4. 在“**类别**”窗格中，选择“**转换**”。
5. 在“**远程字符集**”列表框中，选择“**UTF-8**”。
6. 在“**处理线条图形字符**”下，选择“**使用 Unicode 线条图形代码点**”。 下图显示了正确的 PuTTY 选择。
   
    ![UTF PuTTY 设置](./media/storsimple-windows-powershell-administration/IC740878.png)
7. 单击“应用” 。

现在可以通过执行以下步骤使用 PuTTY 连接到设备串行控制台。

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>关于串行控制台
通过串行控制台访问 StorSimple 设备的 Windows PowerShell 界面时，会显示一条后面跟着菜单选项的标题消息。 

这条标题消息包含基本 StorSimple 设备信息，比如型号、名称、安装的软件版本以及正在访问的控制器的状态。 下图显示了一个标题消息示例。

![串行标题消息](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> 可以使用标题消息来确定连接到的控制器是主动的还是被动的。
> 
> 

下图显示了串行控制台菜单中可用的各种运行空间选项。

![注册设备 2](./media/storsimple-windows-powershell-administration/IC740906.png)

你可以从以下设置中选择：

1. **以完全访问权限登录**：选择此选项可以（使用正确的凭据）连接到本地控制器上的 **SSAdminConsole** 运行空间。 （本地控制器是你当前正在通过 StorSimple 设备的串行控制台访问的控制器。）此选项还可以用于让 Microsoft 支持部门访问不受限制的运行空间（某个支持会话）以解决任何可能存在的设备问题。 在使用选项 1 登录之后，你可以通过运行某个特定的 cmdlet 让 Microsoft 支持部门的工程师访问不受限制的运行空间。 有关详细信息，请参阅[启动支持会话](storsimple-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple)。
2. **以完全访问权限登录对等控制器**：此选项与选项 1 相同，区别在于使用此选项你可以连接（使用正确的凭据）到对等控制器上的 **SSAdminConsole** 运行空间。 因为 StorSimple 设备是带有采用主动-被动配置的两台控制器的一台高可用性设备，所以对等方指的是正在通过串行控制台访问的设备中的另一台控制器。
   与选项 1 相似，此选项也可以用于让 Microsoft 支持部门访问对等控制器上不受限制的运行空间。
3. **以有限访问权限连接**：此选项用于在受限模式下访问 Windows PowerShell 界面。 不会提示你输入访问凭据。 与选项 1 和选项 2 相比，此选项连接到一个受限制更多的运行空间。  通过选项 1 可以执行而**无法*在此运行空间中执行的任务包括：
   
   * 重置为出厂设置
   * 更改密码
   * 启用或禁用支持访问权限
   * 应用更新
   * 安装修复程序 

    > [!NOTE]
    > 如果忘记了设备管理员密码并且无法通过选项 1 或选项 2 进行连接，那么这是首选选项。

4. **更改语言**：此选项用于更改 Windows PowerShell 界面上的显示语言。 支持的语言包括英语、日语、俄语、法语、韩语、西班牙语、意大利语、德语、中文和葡萄牙语（巴西）。

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>使用 Windows PowerShell for StorSimple 远程连接到 StorSimple
可以使用 Windows PowerShell 远程处理连接到 StorSimple 设备。 采取这种方式连接时，不会看到菜单。 （仅当使用设备上的串行控制台连接时，才会看到菜单。 远程连接使你直接进入串行控制台上的“选项 1 – 完全访问权限”的等效选项。）借助 Windows PowerShell 远程处理，可以连接到某个特定运行空间。 也可指定显示语言。 

显示语言与通过使用串行控制台菜单中的“**更改语言**”选项设置的语言无关。 如果未指定显示语言，远程 PowerShell 将自动选取连接所用设备的区域设置。

> [!NOTE]
> 如果使用的是 Microsoft Azure 虚拟主机和 StorSimple 虚拟设备，则可以使用 Windows PowerShell 远程处理和虚拟主机来连接到虚拟设备。 如果已设置了主机上的某个共享位置（该主机用于保存来自 Windows PowerShell 会话的信息），则应注意到 Everyone 主体仅包括已经过身份验证的用户。 因此，如果已设置共享以允许 Everyone 访问，并且连接时无需指定凭据，那么将使用未经身份验证的 Anonymous 主体，并且会显示一个错误。 若要解决此问题，必须在共享主机上启用来宾帐户，并且授予来宾帐户对该共享的完全访问权限，否则必须同时指定有效的凭据和 Windows PowerShell cmdlet。
> 
> 

可以使用 HTTP 或 HTTPS 来通过 Windows PowerShell 远程处理进行连接。 使用以下教程中的指导︰

* [使用 HTTP 远程连接](storsimple-remote-connect.md#connect-through-http)
* [使用 HTTPS 远程连接](storsimple-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>连接安全注意事项
在决定连接到 Windows PowerShell for StorSimple 的方式时，请考虑以下方面：

* 直接连接到设备串行控制台是安全的，但通过网络交换机连接到串行控制台并不安全。 通过网络交换机连接到设备串行控制台时，请警惕安全风险。
* 同通过网络上的串行控制台进行连接相比，通过 HTTP 会话进行连接可能具有更高的安全性。 虽然这不是最安全的方法，但在受信任的网络上是比较安全的。
* 通过 HTTPS 会话进行连接是最安全的选项（建议使用）。

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>使用 Windows PowerShell for StorSimple 管理 StorSimple 设备
下表显示了可以在你的 StorSimple 设备的 Windows PowerShell 界面内执行的所有常见管理任务和复杂工作流的摘要。 有关每个工作流的详细信息，请单击表中的相应条目。

#### <a name="windows-powershell-for-storsimple-workflows"></a>Windows PowerShell for StorSimple 工作流
| 目标 | 请遵循以下过程。 |
| --- | --- |
| 注册设备 |[使用 Windows PowerShell for StorSimple 配置和注册设备](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| 配置 Web 代理</br>查看 Web 代理设置 |[为你的 StorSimple 设备配置 Web 代理](storsimple-configure-web-proxy.md) |
| 修改设备上的 DATA 0 网络接口设置 |[修改 StorSimple 设备上的 DATA 0 网络接口](storsimple-modify-data-0.md) |
| 停止控制器 </br> 重新启动或关闭控制器 </br> 关闭设备</br>将设备重置为出厂默认设置 |[管理设备控制器](storsimple-manage-device-controller.md) |
| 安装维护模式更新和修补程序 |[更新设备](storsimple-update-device.md) |
| 进入维护模式 </br>退出维护模式 |[StorSimple 设备模式](storsimple-device-modes.md) |
| 创建支持包</br>解密和编辑支持包 |[创建和管理支持包](storsimple-create-manage-support-package.md) |
| 启动支持会话</br> |[在 Windows PowerShell for StorSimple 中启动支持会话](/storsimple-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>获取关于 Windows PowerShell for StorSimple 的帮助信息
在 Windows PowerShell for StorSimple 中，有关于 cmdlet 的帮助信息。 此帮助的在线最新版本也可用，可以用它来更新你的系统上的帮助信息。

获取关于此界面的帮助信息与获取关于 Windows PowerShell 的帮助信息很相似，且大多数与帮助信息相关的 cmdlet 将正常工作。 可以在 TechNet 库中找到关于 Windows PowerShell 的在线帮助信息：[使用 Windows PowerShell 编写脚本](http://go.microsoft.com/fwlink/?LinkID=108518)。

下面是关于此 Windows PowerShell 界面的帮助信息类型的简要说明，包括如何更新帮助信息。

#### <a name="to-get-help-for-a-cmdlet"></a>获取关于 cmdlet 的帮助信息
* 若要获取关于任何 cmdlet 或函数的帮助信息，请使用以下命令︰`Get-Help <cmdlet-name>`
* 若要获取关于任何 cmdlet 的在线帮助信息，请在上面的 cmdlet 命令中加上`-Online`参数︰`Get-Help <cmdlet-name> -Online`
* 若要获完整的帮助信息，可以使用 `–Full` 参数，例如，使用 `–Examples` 参数。

#### <a name="to-update-help"></a>更新帮助信息
在 Windows PowerShell 界面中更新帮助信息很容易。 执行以下步骤就可以更新你的系统上的帮助信息。

#### <a name="to-update-cmdlet-help"></a>更新 cmdlet 帮助信息
1. 在选中“**以管理员身份运行**”选项的情况下启动 Windows PowerShell。
2. 在命令提示符处，键入： `Update-Help`
3. 将安装更新的帮助文件。
4. 安装帮助文件之后，键入︰`Get-Help Get-Command`。 此操作将显示有可用帮助信息的 cmdlet 列表。

> [!NOTE]
> 若要获取在运行空间中所有可用的 cmdlet 的列表，请登录到相应的菜单选项并运行 `Get-Command` cmdlet。
> 
> 

## <a name="next-steps"></a>后续步骤
如果执行上述工作流之一时，你的 StorSimple 设备遇到任何问题，请参阅[用于解决 StorSimple 部署问题的工具](storsimple-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments)。




<!--HONumber=Nov16_HO3-->


