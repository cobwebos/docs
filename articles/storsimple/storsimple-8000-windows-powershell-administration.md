---
title: "适用于 StorSimple 设备 management PowerShell |Microsoft 文档"
description: "了解如何使用 Windows PowerShell for StorSimple 来管理你的 StorSimple 设备。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/03/2017
ms.author: alkohli@microsoft.com
ms.openlocfilehash: 89e1054117f19e787da5330932021351fb016209
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>使用 Windows PowerShell for StorSimple 来管理你的设备

## <a name="overview"></a>概述

Windows PowerShell for StorSimple 提供一个命令行接口，可用于管理你的 Microsoft Azure StorSimple 设备。 顾名思义，它是基于 Windows PowerShell 受限运行空间中内置的命令行界面。 从命令行上的用户的角度来看，受限运行空间显示为受限制版本的 Windows PowerShell。 在同时保持 Windows PowerShell 的基本功能的部分，此接口还提供更多的专用的 cmdlet，方便你管理你的 Microsoft Azure StorSimple 设备。

本文介绍 Windows PowerShell for StorSimple 功能，包括如何连接到此接口，并包含指向分步过程或工作流，你可以使用此接口来执行。 工作流包括如何注册你的设备、 配置你的设备上的网络接口、 安装要求设备处于维护模式下，将更改设备状态，并解决你可能会遇到任何问题的更新。

在阅读本文后，你将能够：

* 连接到你使用 Windows PowerShell for StorSimple 的 StorSimple 设备。
* 管理你使用 Windows PowerShell for StorSimple 的 StorSimple 设备。
* StorSimple 的 Windows PowerShell 中获取帮助。

> [!NOTE]
> * Windows PowerShell for StorSimple cmdlet 可以从串行控制台或通过 Windows PowerShell 远程处理管理 StorSimple 设备。 有关每个 cmdlet 的可以在此界面中使用的详细信息，请转到[Windows powershell for StorSimple 的 cmdlet 参考](https://technet.microsoft.com/library/dn688168.aspx)。
> * Azure PowerShell StorSimple cmdlet 是 cmdlet 的可用于自动执行 StorSimple 服务级和从命令行迁移任务的不同的集合。 有关适用于 StorSimple 的 Azure PowerShell cmdlet 的详细信息，请转到[Azure StorSimple cmdlet 参考](https://docs.microsoft.com/powershell/servicemanagement/azure.storsimple/v3.1.0/azure.storsimple)。


你可以访问 Windows PowerShell for StorSimple 使用以下方法之一：

* [连接到 StorSimple 设备串行控制台](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [远程连接到 StorSimple 使用 Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>连接到 Windows PowerShell for StorSimple，通过设备串行控制台

你可以[下载 PuTTY](http://www.putty.org/)或类似终端模拟软件以连接到 Windows PowerShell for StorSimple。 你需要专门配置 PuTTY 以访问 Microsoft Azure StorSimple 设备。 以下主题包含有关如何配置 PuTTy 并连接到设备的详细的步骤。 另外还介绍了串行控制台中的各个菜单选项。

### <a name="putty-settings"></a>PuTTY 设置

请确保使用以下 PuTTY 设置以从串行控制台连接到的 Windows PowerShell 界面。

#### <a name="to-configure-putty"></a>若要配置 PuTTY

1. PuTTY 中**重新配置**对话框中，在**类别**窗格中，选择**键盘**。
2. 请确保选择以下选项 （当你启动新会话时，这些是默认设置）。
   
   | 键盘项 | 选择 |
   | --- | --- |
   | Backspace 键 |控件的？ (127) |
   | Home 和 End 键 |Standard |
   | 功能键和键盘 |ESC [n ~ |
   | 光标键的初始状态 |正常 |
   | 数字小键盘的初始状态 |正常 |
   | 启用额外的键盘功能 |Control-alt 与 AltGr 不同 |
   
    ![支持的 Putty 设置](./media/storsimple-windows-powershell-administration/IC740877.png)
3. 单击 **“应用”**。
4. 在**类别**窗格中，选择**转换**。
5. 在**远程字符集**列表框中，选择**utf-8**。
6. 下**处理线条图形字符**，选择**使用 Unicode 线条图形代码点**。 以下屏幕截图显示正确的 PuTTY 选择。
   
    ![UTF Putty 设置](./media/storsimple-windows-powershell-administration/IC740878.png)
7. 单击 **“应用”**。

你现在可以使用 PuTTY 通过执行以下步骤连接到设备串行控制台。

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>有关串行控制台

在你访问 Windows PowerShell 用户可以看到通过串行控制台横幅消息 StorSimple 设备的界面，然后菜单选项。

横幅消息包含基本的 StorSimple 设备信息，例如模型、 名称、 已安装的软件版本和正在访问的控制器的状态。 下图显示了横幅消息示例。

![连续横幅消息](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> 你可以使用横幅消息来确定是否已连接到的控制器是_Active_或_被动_。

下图显示了串行控制台菜单中提供的各种运行空间选项。

![注册你的设备 2](./media/storsimple-windows-powershell-administration/IC740906.png)

你可以从以下设置中进行选择：

1. **完全访问权限登录**此选项允许你 （使用适当的凭据） 连接到**SSAdminConsole**本地控制器上的运行空间。 （本地控制器是你当前通过 StorSimple 设备的串行控制台访问的控制器。）此选项还可让 Microsoft 支持部门访问不受限制的运行空间 （支持会话） 任何可能的设备问题进行故障排除。 使用选项 1 登录之后，你可以允许 Microsoft 支持人员来运行特定 cmdlet 来访问不受限制的运行空间。 有关详细信息，请参阅[启动支持会话](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple)。
   
2. **登录到具有完全访问权限的对等控制器**此选项等同于选项 1，只不过可以 （使用适当的凭据） 连接到**SSAdminConsole**对等控制器上的运行空间。 StorSimple 设备是具有两个控制器采用主动-被动配置高可用性设备，因为对等是指你通过串行控制台访问设备的另一个控制器）。
   与选项 1 相似，此选项还可让 Microsoft 支持部门访问不受限制的运行空间，在对等控制器上。

3. **具有有限访问权限连接**此选项用于访问在 limited 模式下的 Windows PowerShell 接口。 不会提示输入访问凭据。 此选项可连接到与选项 1 和 2 相比更受限制运行空间。  一些任务，可通过选项 1，**无法*执行在此命名空间是：
   
   * 重置为出厂设置
   * 更改密码
   * 启用或禁用支持访问
   * 应用更新
   * 安装修补程序

    > [!NOTE]
    > 如果你忘记了设备管理员密码并且无法通过选项 1 或 2 进行连接，这是首选的选项。

4. **更改语言**此选项允许你更改 Windows PowerShell 界面上的显示语言。 支持的语言包括英语、 日语、 俄语、 法语、 韩语、 西班牙语、 意大利语、 德语、 中文和葡萄牙语 （巴西）。

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>远程连接到 StorSimple 使用 Windows PowerShell for StorSimple

你可以使用 Windows PowerShell 远程处理连接到 StorSimple 设备。 这种方式连接时，你将不会看到菜单。 （仅当你使用在设备上的串行控制台连接，将看到一个菜单。 远程连接可直接进入在串行控制台上的"选项 1 – 完全访问权限"的等效项。）使用 Windows PowerShell 远程处理，则连接到特定运行空间。 你还可以指定显示语言。

显示语言与你使用设置的语言无关**更改语言**串行控制台菜单中的选项。 远程 PowerShell 将自动选取如果未指定从连接的设备的区域设置。

> [!NOTE]
> 如果你正在使用 Microsoft Azure 虚拟主机和 StorSimple 云设备，你可以使用 Windows PowerShell 远程处理和虚拟主机连接到云设备。 如果你设置了主机上用于保存在 Windows PowerShell 会话中的信息的共享位置，你应注意， _Everyone_主体身份验证的用户仅包括。 因此，如果你已设置共享以允许访问_Everyone_和连接没有指定凭据、 将使用未经身份验证的匿名主体和你将看到一个错误。 若要解决此问题，请在共享主机上你必须启用来宾帐户，然后为来宾帐户完全访问权限提供的共享或你必须指定有效的凭据的 Windows PowerShell cmdlet。


你可以使用 HTTP 或 HTTPS 连接通过 Windows PowerShell 远程处理。 在以下教程中使用的说明：

* [使用 HTTP 远程连接](storsimple-remote-connect.md#connect-through-http)
* [远程使用 HTTPS 连接](storsimple-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>连接安全注意事项

在决定如何连接到 Windows PowerShell for StorSimple，考虑以下方面：

* 直接连接到设备串行控制台是安全的但不是通过网络交换机连接到串行控制台。 通过网络交换机连接到串行设备时，则请警惕安全风险。
* 通过 HTTP 会话进行连接可能会提供更高的安全性，比通过串行控制台通过网络进行连接。 虽然这不是最安全的方法，就可以对可信网络。
* 通过 HTTPS 会话进行连接是最安全和推荐的选项。

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>管理你使用 Windows PowerShell for StorSimple 的 StorSimple 设备

下表显示在 StorSimple 设备的 Windows PowerShell 界面内的所有常见管理任务和复杂工作流可以执行的摘要。 有关每个工作流的详细信息，请单击表中的相应条目。

#### <a name="windows-powershell-for-storsimple-workflows"></a>适用于 StorSimple 工作流 Windows PowerShell

| 如果你想要执行此操作... | 使用此过程。 |
| --- | --- |
| 注册你的设备 |[配置和注册使用 Windows PowerShell for StorSimple 设备](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| 配置 web 代理</br>查看 web 代理设置 |[配置 StorSimple 设备的 web 代理](storsimple-8000-configure-web-proxy.md) |
| 修改你的设备上的 DATA 0 网络接口设置 |[修改你的 StorSimple 设备的 DATA 0 网络接口](storsimple-8000-modify-data-0.md) |
| 停止控制器 </br> 重启或关闭控制器 </br> 关闭设备</br>将设备重置为出厂默认设置 |[管理设备控制器](storsimple-8000-manage-device-controller.md) |
| 安装维护模式更新和修补程序 |[更新你的设备](storsimple-update-device.md) |
| 进入维护模式 </br>退出维护模式 |[StorSimple 设备模式](storsimple-8000-device-modes.md) |
| 创建支持包</br>解密和编辑支持包 |[创建和管理支持包](storsimple-8000-create-manage-support-package.md) |
| 启动支持会话</br> |[启动支持会话在 Windows PowerShell for StorSimple](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>StorSimple 的 Windows PowerShell 中获取帮助

在 Windows PowerShell for StorSimple，cmdlet 帮助器可用。 此帮助的联机的最新版本也可用，则可以用于更新你的系统上的帮助。

在此界面中获取帮助是 Windows PowerShell 中的相似，大部分帮助相关 cmdlet 也工作。 你可以在 TechNet 库中找到 Windows PowerShell 帮助联机：[使用 Windows PowerShell 编写脚本](http://go.microsoft.com/fwlink/?LinkID=108518)。

下面是帮助的有关此 Windows PowerShell 接口，包括如何更新帮助类型的简短说明。

### <a name="to-get-help-for-a-cmdlet"></a>若要获取有关 cmdlet 的帮助

* 若要获取任何 cmdlet 或函数的帮助，请使用以下命令：`Get-Help <cmdlet-name>`
* 若要获取任何 cmdlet 的联机帮助，请使用上面的 cmdlet 使用`-Online`参数：`Get-Help <cmdlet-name> -Online`
* 对于完整的帮助，你可以使用`–Full`参数，有关示例，并用`–Examples`参数。

### <a name="to-update-help"></a>若要更新帮助

你可以轻松更新的 Windows PowerShell 界面中的帮助。 执行以下步骤以更新你的系统上的帮助。

#### <a name="to-update-cmdlet-help"></a>若要更新 cmdlet 帮助
1. 使用“以管理员身份运行”选项启动 Windows PowerShell  。
2. 在命令提示符下键入：`Update-Help`
3. 将安装更新的帮助文件。
4. 安装的帮助文件后，请键入： `Get-Help Get-Command`。 这将显示都有帮助的 cmdlet 的列表。

> [!NOTE]
> 若要在命名空间中获取所有可用 cmdlet 的列表，请登录到相应的菜单选项并运行`Get-Command`cmdlet。


## <a name="next-steps"></a>后续步骤

如果在执行上述工作流之一时，请与你的 StorSimple 设备遇到任何问题，请参阅[工具以排查关于 StorSimple 部署](storsimple-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments)。

