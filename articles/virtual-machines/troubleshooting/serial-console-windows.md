---
title: Azure 虚拟机串行控制台 | Microsoft Docs
description: Azure 虚拟机的双向串行控制台。
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/07/2018
ms.author: harijay
ms.openlocfilehash: e1884048d0f02de1b3a354bc4dac2b3e98dcccc9
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2018
ms.locfileid: "47411378"
---
# <a name="virtual-machine-serial-console"></a>虚拟机串行控制台


使用 Azure 上的虚拟机串行控制台可以访问适用于Windows 虚拟机的基于文本的控制台。 此控制台与虚拟机的 COM1 串行端口建立串行连接，可用于访问独立于虚拟机的网络或操作系统状态的虚拟机。 目前，只能通过 Azure 门户访问虚拟机的串行控制台，并且只允许对虚拟机拥有 VM 参与者或更高访问权限的用户执行此操作。 

有关适用于 Linux VM 的串行控制台文档，请[单击此处](serial-console-linux.md)。

> [!Note] 
> 虚拟机串行控制台已在全球 Azure 区域中正式发布。 目前，串行控制台尚不可用于 Azure 政府云或 Azure 中国云。

 

## <a name="prerequisites"></a>先决条件 

* 必须使用资源管理部署模型。 不支持经典部署。 
* 虚拟机上必须已启用[启动诊断](boot-diagnostics.md) 

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

* 使用串行控制台的帐户必须对 VM 和[启动诊断](boot-diagnostics.md)存储帐户拥有[参与者角色](../../role-based-access-control/built-in-roles.md)。 
* 你要针对其访问串行控制台的虚拟机必须也具有基于密码的帐户。 可以使用 VM 访问扩展的[重置密码](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password)功能创建一个 - 请参阅下面的屏幕截图。

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-reset-password.png)

## <a name="get-started-with-serial-console"></a>开始使用串行控制台
只能通过 [Azure 门户](https://portal.azure.com)访问虚拟机的串行控制台。 下面是通过门户访问虚拟机串行控制台的步骤。

  1. 打开 Azure 门户
  2. 在左侧菜单中选择虚拟机。
  3. 在列表中单击所需的 VM。 此时会打开该 VM 的概述页。
  4. 向下滚动到“支持 + 故障排除”部分，单击“串行控制台”选项。 此时会打开一个包含串行控制台的新窗格，并启动连接。


## <a name="enable-serial-console-in-custom-or-older-images"></a>在自定义或更低版本的映像中启用串行控制台
Azure 上较新的 Windows Server 映像默认情况下已启用[特殊管理控制台](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC)。 SAC 在服务器版本的 Windows 上受支持，但在客户端版本（例如 Windows 10、Windows 8 或 Windows 7）上不可用。 若要为 2018 年 2 月之前创建的 Windows 虚拟机启用串行控制台，请执行以下步骤： 

1. 通过远程桌面连接到 Windows 虚拟机
2. 从管理命令提示符运行以下命令 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. 重新启动系统以启用 SAC 控制台

![](/media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

如果需要，也可以使 SAC 脱机：

1. 将想要为其配置 SAC 的 Windows 磁盘作为数据磁盘附加到现有 VM。 
2. 从管理命令提示符运行以下命令 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled"></a>如何知道是否已启用 SAC？

如果未启用 [SAC] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx)，串行控制台将不会显示 SAC 提示符。 在某些情况下，将显示 VM 运行状况信息，在其他情况下，它将为空白。 如果使用的是 2018 年 2 月之前创建的 Windows Server 映像，则很可能未启用 SAC。

## <a name="enable-the-windows-boot-menu-in-serial-console"></a>在串行控制台中启用 Windows 启动菜单 

如果需要让 Windows 启动加载程序提示显示在串行控制台中，可以将以下附加选项添加到启动配置数据中。 请参阅 [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set)，了解更多详细信息

1. 通过远程桌面连接到 Windows 虚拟机
2. 从管理命令提示符运行以下命令 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. 重新启动系统以启用启动菜单

## <a name="use-serial-console-for-nmi-calls-in-windows-vms"></a>在 Windows VM 中使用串行控制台进行 NMI 调用
不可屏蔽的中断 (NMI) 旨在创建虚拟机上的软件不会忽略的信号。 过去，NMI 用来监视要求实现特定响应时间的系统上的硬件问题。  现在，程序员和系统管理员通常使用 NMI 作为用来对挂起的系统进行调试或故障排除的机制。

可以使用下面显示的命令栏上的键盘图标通过串行控制台向 Azure 虚拟机发送 NMI。 传送 NMI 后，虚拟机配置将控制系统的响应方式。 可以将 Windows 配置为在收到 NMI 时崩溃并创建内存转储。

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

有关配置 Windows 在收到 NMI 时创建故障转储的信息，请参阅：[如何在基于 Windows 上的系统上使用 NMI 生成完整的故障转储文件或内核故障转储文件](https://support.microsoft.com/en-us/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file)

## <a name="disable-serial-console"></a>禁用串行控制台
默认情况下，所有订阅为所有 VM 启用了串行控制台访问。 可以在订阅级别或 VM 级别禁用串行控制台。

> [!Note]       
> 若要为订阅启用或禁用串行控制台，必须具有订阅的写入权限。 这包括但不限于管理员或所有者角色。 自定义角色也可能具有写入权限。

### <a name="subscription-level-disable"></a>订阅级禁用
可以通过[禁用控制台 REST API 调用](https://aka.ms/disableserialconsoleapi)为整个订阅禁用串行控制台。 可以使用“API 文档”页面上提供的“试用”功能为订阅禁用和启用串行控制台。 输入 `subscriptionId`，在 `default` 字段中输入“default”，然后单击“运行”。 Azure CLI 命令尚不可用，将在以后的某个日期提供。 [在此处试用 REST API 调用](https://aka.ms/disableserialconsoleapi)。

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

另外，可以在 Cloud Shell 中使用以下命令集（显示的 bash 命令）来为订阅禁用、启用和查看串行控制台的状态。 

* 若要为订阅获取串行控制台的禁用状态，请使用以下命令：
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* 若要为订阅禁用串行控制台，请使用以下命令：
    ```azurecli-interactive 
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* 若要为订阅启用串行控制台，请使用以下命令：
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

### <a name="vm-level-disable"></a>VM 级禁用
可以通过禁用特定 VM 的启动诊断设置，为该 VM 禁用串行控制台。 只需从 Azure 门户关闭启动诊断，即可为 VM 禁用串行控制台。

## <a name="serial-console-security"></a>串行控制台安全性 

### <a name="access-security"></a>访问安全性 
只有对虚拟机拥有 [VM 参与者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)或更高访问权限的用户才能访问串行控制台。 如果 AAD 租户需要多重身份验证 (MFA)，则访问串行控制台时也需要执行 MFA，因为串行控制台是通过 [Azure 门户](https://portal.azure.com)访问的。

### <a name="channel-security"></a>通道安全性
来回发送的所有数据在线路上经过加密。

### <a name="audit-logs"></a>审核日志
对串行控制台的所有访问目前都会记录在虚拟机的[启动诊断](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics)日志中。 Azure 虚拟机管理员拥有并可控制这些日志的访问权限。  

>[!CAUTION] 
尽管不会记录控制台的访问密码，但如果在控制台中运行的命令包含或输出密码、机密、用户名或其他任何形式的个人身份信息 (PII)，则在实现串行控制台的回滚功能过程中，会将这些信息连同其他所有可见文本一起写入虚拟机的启动诊断日志。 这些日志不断轮替，只有对诊断存储帐户拥有读取权限的个人才能访问它们。但是，我们建议遵循有关将远程桌面用于涉及机密和/或 PII 的任何操作的最佳做法。 

### <a name="concurrent-usage"></a>并发使用
如果某个用户已连接到串行控制台，而另一个用户已成功请求访问同一个虚拟机，则第一个用户将断开连接，第二用户将建立连接，就好比是第一个用户站起来离开物理控制台，然后一个新用户在控制台旁边坐下来。

>[!CAUTION] 
这意味着，断开连接的用户并未注销！ 断开连接后强制注销（通过 SIGHUP 或类似机制）的功能目前仍在规划中。 对于 Windows，SAC 中会启用自动超时；但对于 Linux，可以配置终端超时设置。 

## <a name="common-scenarios-for-accessing-serial-console"></a>访问串行控制台的常见场景 
场景          | 串行控制台中的操作                
:------------------|:-----------------------------------------
错误的防火墙规则 | 访问串行控制台，并修复 Windows 防火墙规则。 
文件系统损坏/检查 | 访问串行控制台并恢复文件系统。 
RDP 配置问题 | 访问串行控制台并更改设置。 若要开始，请转到 [RDP 文档](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access)。
网络锁定系统| 通过门户访问串行控制台以管理系统。 [串行控制台 CMD 和 PowerShell 文档](serial-console-cmd-ps-commands.md)中列出了一些网络命令。 
与引导加载程序交互 | 通过串行控制台访问 BCD。 若要开始，请转到[让启动菜单显示在串行控制台中](#enabling-boot-menu-to-show-in-the-serial-console)。 

## <a name="accessibility"></a>可访问性
可访问性是 Azure 串行控制台的重点。 为此，我们已确保视觉和听觉障碍者，以及可能无法使用鼠标的人群能够访问串行控制台。

### <a name="keyboard-navigation"></a>键盘导航
使用键盘上的 `tab` 键在 Aure 门户中的串行控制台界面上导航。 屏幕上会突出显示你的位置。 若要使焦点离开串行控制台边栏选项卡，请在键盘上按 `Ctrl + F6`。

### <a name="use-serial-console-with-a-screen-reader"></a>结合使用串行控制台与屏幕阅读器
串行控制台附带内置屏幕阅读器支持。 在打开屏幕阅读器的情况下导航，屏幕阅读器可大声读出当前所选按钮的替换文字。

## <a name="errors"></a>Errors
大多数错误都是暂时性的，重试连接即可解决。 下表显示了错误和缓解措施的列表

错误                            |   缓解措施 
:---------------------------------|:--------------------------------------------|
无法检索“<VMNAME>”的启动诊断设置。 若要使用串行控制台，请确保为此 VM 启用了启动诊断。 | 确保 VM 已启用[启动诊断](boot-diagnostics.md)。 
VM 处于已停止/已解除分配状态。 启动 VM，然后重试串行控制台连接。 | 虚拟机必须处于已启动状态才能访问串行控制台
没有所需的权限，无法使用此 VM 的串行控制台。 请确保至少拥有 VM 参与者角色权限。| 需要特定的访问权限才能访问串行控制台。 有关详细信息，请参阅[访问要求](#prerequisites)
无法确定启动诊断存储帐户“<STORAGEACCOUNTNAME>”的资源组。 确认是否为此 VM 启用了启动诊断，以及是否有权访问此存储帐户。 | 需要特定的访问权限才能访问串行控制台。 有关详细信息，请参阅[访问要求](#prerequisites)
访问此 VM 的启动诊断存储帐户时遇到“已禁止”响应。 | 请确保启动诊断没有帐户防火墙。 若要使串行控制台运行，需要一个可访问的启动诊断存储帐户。
Web 套接字已关闭或无法打开。 | 你可能需要将 `*.console.azure.com` 加入允许列表。 一种更详细但更冗长的方法是将 [Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/en-us/download/details.aspx?id=41653)加入允许列表，该范围相当规律地定期更改。
连接到 Windows VM 时，仅显示运行状况信息| 如果尚未为 Windows 映像启用特殊管理控制台，则会显示此信息。 有关如何在 Windows VM 上手动启用 SAC 的说明，请参阅[访问 Windows 的串行控制台](#access-serial-console-for-windows)。 有关更多详细信息，请参阅 [Windows 运行状况信号](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)。

## <a name="known-issues"></a>已知问题 
我们注意到串行控制台存在的一些问题。 下面是这些问题和缓解措施的列表。

问题                             |   缓解措施 
:---------------------------------|:--------------------------------------------|
在出现连接标题后按 Enter 不会显示登录提示 | 请参阅此页：[按 Enter 不执行任何操作](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)。 如果你运行的自定义 VM、强化设备或 GRUB 配置导致 Windows 无法正确连接到串行端口，则可能会发生这种情况。
如果已启用内核调试，则无法在 SAC 提示符下键入内容 | 通过 RDP 连接到 VM，并从权限提升的命令提示符运行 `bcdedit /debug {current} off`。 如果无法建立 RDP 连接，可将 OS 磁盘附加到另一个 Azure VM，在该磁盘附加为数据磁盘的情况下使用 `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off` 对其进行修改，然后换回磁盘。
如果原始内容具有重复的字符，则在 SAC 中粘贴到 PowerShell 将导致第三个字符。 | 解决方法是从当前会话中对 PSReadLine 模块执行 Past 卸载。 运行 `Remove-Module PSReadLine` 从当前会话中卸载 PSReadLine 模块 - 这不会删除或卸载该模块。
某些键盘输入会生成奇怪的 SAC 输出（例如 `[A`、 `[3~`） | SAC 提示符不支持 [VT100](https://aka.ms/vtsequences) 转义序列。
粘贴很长的字符串会不起作用 | 串行控制台将粘贴到终端的字符串长度限制为 2048 个字符。 这是为了防止串行端口带宽过载。

## <a name="frequently-asked-questions"></a>常见问题 

**问：如何发送反馈？**

A. 可以访问 https://aka.ms/serialconsolefeedback 来提供问题反馈。 也可以通过 azserialhelp@microsoft.com，或者 http://feedback.azure.com 上的虚拟机类别发送反馈（不太建议）

**问：串行控制台是否支持复制/粘贴？**

A. 是的，它支持。 使用 Ctrl + Shift + C 和 Ctrl + Shift + V 复制并粘贴到终端。

**问：谁可以为我的订阅启用或禁用串行控制台？**

A. 若要在订阅范围级别启用或禁用串行控制台，必须具有订阅的写入权限。 具有写入权限的角色包括但不限于管理员或所有者角色。 自定义角色也可能具有写入权限。

**问：谁可以访问 VM 的串行控制台？**

A. 必须具有 VM 的参与者级别访问权限或更高级别访问权限才能访问 VM 的串行控制台。 

**问：我的串口控制台没有显示任何内容，我该怎么办？**

A. 你的映像可能配置错误，无法进行串行控制台访问。 有关配置映像以启用串行控制台的详细信息，请参阅[在自定义映像或较旧映像中启用串行控制台](#Enable-Serial-Console-in-custom-or-older-images)。

**问：串行控制台是否可用于虚拟机规模集？**

A. 目前，不支持访问虚拟机规模集实例的串行控制台。

## <a name="next-steps"></a>后续步骤
* 有关可以在 Windows SAC 中使用的 CMD 和 PowerShell 命令的深度指南，请单击[此处](serial-console-cmd-ps-commands.md)。
* 串行控制台也适用于 [Linux](serial-console-linux.md) VM。
* 详细了解[启动诊断](boot-diagnostics.md)。