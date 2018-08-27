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
ms.openlocfilehash: 66354db65d5e615780ec49683fbc72f1156ac5e1
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2018
ms.locfileid: "42141930"
---
# <a name="virtual-machine-serial-console-preview"></a>虚拟机串行控制台（预览版） 


使用 Azure 上的虚拟机串行控制台可以访问适用于 Linux 和 Windows 虚拟机的基于文本的控制台。 此控制台与虚拟机的 COM1 串行端口建立串行连接，可用于访问虚拟机，且不与虚拟机的网络/操作系统状态相关。 目前，只能通过 Azure 门户访问虚拟机的串行控制台，并且只允许对虚拟机拥有 VM 参与者或更高访问权限的用户执行此操作。 

有关适用于 Linux VM 的串行控制台文档，请[单击此处](../linux/serial-console.md)。

> [!Note] 
> 同意使用条款即可使用预览版。 有关详细信息，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 目前，此服务以**公共预览版**提供，可以访问已在 Azure 全球区域开通的虚拟机的串行控制台。 目前，串行控制台不可用于 Azure 政府云、Azure 德国云和 Azure 中国云。

 

## <a name="prerequisites"></a>先决条件 

* 必须使用资源管理部署模型。 不支持经典部署。 
* 虚拟机上必须已启用[启动诊断](boot-diagnostics.md) 
* 使用串行控制台的帐户必须对 VM 和[启动诊断](boot-diagnostics.md)存储帐户拥有[参与者角色](../../role-based-access-control/built-in-roles.md)。 

## <a name="open-the-serial-console"></a>打开串行控制台
只能通过 [Azure 门户](https://portal.azure.com)访问虚拟机的串行控制台。 下面是通过门户访问虚拟机串行控制台的步骤 

  1. 打开 Azure 门户
  2. 在左侧菜单中选择虚拟机。
  3. 在列表中单击所需的 VM。 此时会打开该 VM 的概述页。
  4. 向下滚动到“支持 + 故障排除”部分，单击“串行控制台(预览版)”选项。 此时会打开一个包含串行控制台的新窗格，并启动连接。

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

## <a name="disable-serial-console"></a>禁用串行控制台
默认情况下，所有订阅为所有 VM 都启用了串行控制台访问。 你可以在订阅级别或 VM 级别禁用串行控制台。

### <a name="subscription-level-disable"></a>订阅级禁用
可以通过[禁用控制台 REST API 调用](https://aka.ms/disableserialconsoleapi)为整个订阅禁用串行控制台。 可以使用“API 文档”页面上提供的“试用”功能为订阅禁用和启用串行控制台。 输入你的 `subscriptionId`，在 `default` 字段中输入“default”，然后单击“运行”。 Azure CLI 命令尚不可用，将在以后的某个日期提供。 [在此处试用 REST API 调用](https://aka.ms/disableserialconsoleapi)。

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

另外，可以在 Cloud Shell 中使用下面的命令集（显示的 bash 命令）来为订阅禁用、启用和查看串行控制台的状态。 

* 若要为订阅获取串行控制台的禁用状态，请使用以下命令：
    ```
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* 若要为订阅禁用串行控制台，请使用以下命令：
    ```
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* 若要为订阅启用串行控制台，请使用以下命令：
    ```
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"')) 

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

### <a name="vm-level-disable"></a>VM 级禁用
可以通过禁用特定 VM 的启动诊断设置，为该 VM 禁用串行控制台。 只需要从 Azure 门户关闭启动诊断，便可为 VM 禁用串行控制台。

## <a name="serial-console-security"></a>串行控制台安全性 

### <a name="access-security"></a>访问安全性 
只有对虚拟机拥有 [VM 参与者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)或更高访问权限的用户才能访问串行控制台。 如果 AAD 租户需要多重身份验证 (MFA)，则访问串行控制台时也需要执行 MFA，因为串行控制台是通过 [Azure 门户](https://portal.azure.com)访问的。

### <a name="channel-security"></a>通道安全性
来回发送的所有数据在线路上都将进行加密。

### <a name="audit-logs"></a>审核日志
对串行控制台的所有访问目前都会记录在虚拟机的[启动诊断](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics)日志中。 Azure 虚拟机管理员拥有并可控制这些日志的访问权限。  

>[!CAUTION] 
尽管不会记录控制台的访问密码，但如果在控制台中运行的命令包含或输出密码、机密、用户名或其他任何形式的个人身份信息 (PII)，则在实现串行控制台的回滚功能过程中，会将这些信息连同其他所有可见文本一起写入虚拟机的启动诊断日志。 这些日志不断轮替，只有对诊断存储帐户拥有读取权限的个人才能访问它们。但是，我们建议遵循有关将远程桌面用于涉及机密和/或 PII 的任何操作的最佳做法。 

### <a name="concurrent-usage"></a>并发使用
如果某个用户已连接到串行控制台，而另一个用户已成功请求访问同一个虚拟机，则第一个用户将断开连接，第二用户将建立连接，就好比是第一个用户站起来离开物理控制台，然后一个新用户在控制台旁边坐下来。

>[!CAUTION] 
这意味着，断开连接的用户并未注销！ 断开连接后强制注销（通过 SIGHUP 或类似机制）的功能目前仍在规划中。 对于 Windows，SAC 中会启用自动超时；但对于 Linux，可以配置终端超时设置。 


## <a name="access-serial-console-for-windows"></a>访问适用于 Windows 的串行控制台 
Azure 上较新的 Windows Server 映像默认情况下已启用[特殊管理控制台](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC)。 SAC 在服务器版本的 Windows 上受支持，但在客户端版本（例如 Windows 10、Windows 8 或 Windows 7）上不可用。 若要为使用 Feb2018 或更低版本的映像创建的 Windows 虚拟机启用串行控制台，请执行以下步骤： 

1. 通过远程桌面连接到 Windows 虚拟机
2. 从管理命令提示符运行以下命令 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. 重新启动系统以启用 SAC 控制台

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

如果需要，也可以使 SAC 脱机。 

1. 将想要为其配置 SAC 的 Windows 磁盘作为数据磁盘附加到现有 VM。 
2. 从管理命令提示符运行以下命令 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled-or-not"></a>如何知道是否已启用 SAC 

如果未启用 [SAC] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx)，串行控制台将不会显示 SAC 提示符。 在某些情况下它可能会显示 VM 运行状况信息，或者会为空。  

### <a name="enabling-boot-menu-to-show-in-the-serial-console"></a>让启动菜单显示在串行控制台中 

如果需要让 Windows 启动加载程序提示符显示在串行控制台中，可以将以下附加选项添加到 Windows 启动加载程序。

1. 通过远程桌面连接到 Windows 虚拟机
2. 从管理命令提示符运行以下命令 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. 重新启动系统以启用启动菜单

> [!NOTE] 
> 就目前而言，对功能键的支持未启用，如果需要高级启动选项，请使用 use bcdedit /set {current} onetimeadvancedoptions on，有关详细信息，请参阅 [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set)

## <a name="using-serial-console-for-nmi-calls-in-windows-vms"></a>在 Windows VM 中使用串行控制台进行 NMI 调用
不可屏蔽的中断 (NMI) 设计用来创建虚拟机上的软件不会忽略的信号。 过去，NMI 用来监视要求实现特定响应时间的系统上的硬件问题。  当前，程序员和系统管理员通常使用 NMI 作为用来对挂起的系统进行调试或故障排除的机制。

可以使用下面显示的命令栏上的键盘图标通过串行控制台向 Azure 虚拟机发送 NMI。 在 NMI 送达后，虚拟机配置将控制系统如何响应。 可以将 Windows 配置为在收到 NMI 时崩溃并创建内存转储。

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

有关配置 Windows 在收到 NMI 时创建故障转储的信息，请参阅：[如何在基于 Windows 上的系统上使用 NMI 生成完整的故障转储文件或内核故障转储文件](https://support.microsoft.com/en-us/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file)


## <a name="errors"></a>错误
大多数错误都是暂时性的，重试连接即可解决。 下表显示了错误和缓解措施的列表 

错误                            |   缓解措施 
:---------------------------------|:--------------------------------------------|
无法检索“<VMNAME>”的启动诊断设置。 若要使用串行控制台，请确保为此 VM 启用了启动诊断。 | 确保 VM 已启用[启动诊断](boot-diagnostics.md)。 
VM 处于已停止/已解除分配状态。 启动 VM，然后重试串行控制台连接。 | 虚拟机必须处于已启动状态才能访问串行控制台
没有所需的权限，无法使用此 VM 的串行控制台。 请确保至少拥有 VM 参与者角色权限。| 需要特定的访问权限才能访问串行控制台。 有关详细信息，请参阅[访问要求](#prerequisites)
无法确定启动诊断存储帐户“<STORAGEACCOUNTNAME>”的资源组。 确认是否为此 VM 启用了启动诊断，以及是否有权访问此存储帐户。 | 需要特定的访问权限才能访问串行控制台。 有关详细信息，请参阅[访问要求](#prerequisites)
访问此 VM 的启动诊断存储帐户时遇到“已禁止”响应。 | 请确保启动诊断没有帐户防火墙。 若要使串行控制台运行，需要一个可访问的启动诊断存储帐户。
Web 套接字已关闭或无法打开。 | 你可能需要将 `*.console.azure.com` 加入允许列表。 一种更详细但更冗长的方法是将 [Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/en-us/download/details.aspx?id=41653)加入允许列表，该范围相当规律地定期更改。

## <a name="known-issues"></a>已知问题 
由于串行控制台访问功能目前仍处于预览阶段，我们正在努力解决一些已知问题。下面是已知问题及其可能的解决方法的列表 

问题                             |   缓解措施 
:---------------------------------|:--------------------------------------------|
没有相应的选项用于访问虚拟机规模集实例的串行控制台 | 在预览期，不支持访问虚拟机规模集实例的串行控制台。
在出现连接标题后按 Enter 不会显示登录提示 | [按 Enter 不起任何作用](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)
连接到 Windows VM 时，仅显示运行状况信息| [Windows 运行状况信号](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)
如果已启用内核调试，则无法在 SAC 提示符下键入内容 | 通过 RDP 连接到 VM，并从权限提升的命令提示符运行 `bcdedit /debug {current} off`。 如果无法建立 RDP 连接，可将 OS 磁盘附加到另一个 Azure VM，在该磁盘附加为数据磁盘的情况下使用 `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off` 对其进行修改，然后换回磁盘。
如果原始内容具有重复的字符，则在 SAC 中粘贴到 PowerShell 将导致第三个字符。 | 一种解决方法是删除 PSReadLine 模块。 `Remove-Module PSReadLine` 将从当前会话中删除 PSReadLine 模块。
某些键盘输入会生成奇怪的 SAC 输出（例如 `[A`、 `[3~`） | SAC 提示符不支持 [VT100](https://aka.ms/vtsequences) 转义序列。
访问此 VM 的启动诊断存储帐户时遇到“已禁止”响应。 | 请确保启动诊断没有帐户防火墙。 若要使串行控制台运行，需要一个可访问的启动诊断存储帐户。

## <a name="frequently-asked-questions"></a>常见问题 
**问：如何发送反馈？**

A. 可以访问 https://aka.ms/serialconsolefeedback 来提供问题反馈。 也可以通过 azserialhelp@microsoft.com，或者 http://feedback.azure.com 上的虚拟机类别发送反馈（不太建议）

**问：我无法访问串行控制台，在哪里可以提出支持案例？**

A. 此预览功能遵守 Azure 预览条款。 最好是通过上面所述的渠道请求此功能的支持。 

## <a name="next-steps"></a>后续步骤
* 有关可以在 Windows SAC 中使用的 CMD 和 PowerShell 命令的深度指南，请单击[此处](serial-console-cmd-ps-commands.md)。
* 串行控制台也适用于 [Linux](../linux/serial-console.md) VM。
* 详细了解[启动诊断](boot-diagnostics.md)。
