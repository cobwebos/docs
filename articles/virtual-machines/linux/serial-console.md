---
title: Azure 虚拟机串行控制台 | Microsoft Docs
description: Azure 虚拟机的双向串行控制台。
services: virtual-machines-linux
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/07/2018
ms.author: harijay
ms.openlocfilehash: 857998c73abed76c9e20d5b3422ce607fb9f733d
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782874"
---
# <a name="virtual-machine-serial-console-preview"></a>虚拟机串行控制台（预览版） 


使用 Azure 上的虚拟机串行控制台可以访问适用于 Linux 虚拟机的基于文本的控制台。 此控制台与虚拟机的 COM1 串行端口建立串行连接，可用于访问独立于虚拟机网络或操作系统状态的虚拟机。 目前，只能通过 Azure 门户访问虚拟机的串行控制台，并且只允许对虚拟机拥有 VM 参与者或更高访问权限的用户执行此操作。 

有关适用于 Windows VM 的串行控制台文档，请[单击此处](../windows/serial-console.md)。

> [!Note] 
> 同意使用条款即可使用预览版。 有关详细信息，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 目前，此服务以**公共预览版**提供，可以访问已在 Azure 全球区域开通的虚拟机的串行控制台。 目前，串行控制台不可用于 Azure 政府云、Azure 德国云和 Azure 中国云。


## <a name="prerequisites"></a>先决条件 

* 必须使用资源管理部署模型。 不支持经典部署。 
* 虚拟机必须已启用[启动诊断](boot-diagnostics.md) - 请参阅下面的屏幕截图。

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

* 使用串行控制台的 Azure 帐户必须对 VM 和[启动诊断](boot-diagnostics.md)存储帐户拥有[参与者角色](../../role-based-access-control/built-in-roles.md)。 
* 你要针对其访问串行控制台的虚拟机必须也具有基于密码的帐户。 可以使用 VM 访问扩展的[重置密码](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password)功能创建一个 - 请参阅下面的屏幕截图。

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-reset-password.png)

* 有关特定于 Linux 分发版的设置，请参阅[访问适用于 Linux 的串行控制台](#access-serial-console-for-linux)



## <a name="get-started-with-serial-console"></a>开始使用串行控制台
只能通过 [Azure 门户](https://portal.azure.com)访问虚拟机的串行控制台。 下面是通过门户访问虚拟机串行控制台的步骤 

  1. 打开 Azure 门户
  2. 在左侧菜单中选择虚拟机。
  3. 在列表中单击所需的 VM。 此时会打开该 VM 的概述页。
  4. 向下滚动到“支持 + 故障排除”部分，单击“串行控制台(预览版)”选项。 此时会打开一个包含串行控制台的新窗格，并启动连接。

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE] 
> 串行控制台要求配置一个带有密码的本地用户。 目前，仅仅配置有 SSH 公钥的 VM 无法访问串行控制台。 若要创建带有密码的本地用户，请使用 [VM 访问扩展](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension)（在门户中单击“重置密码”也可以访问它），然后创建带有密码的本地用户。

## <a name="access-serial-console-for-linux"></a>访问适用于 Linux 的串行控制台
要使串行控制台正常运行，必须将来宾操作系统配置为向串行端口读取和写入控制台消息。 大多数[认可的 Azure Linux 分发版](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)默认都已配置串行控制台。 只需在 Azure 门户中单击“串行控制台”部分即可访问控制台。 

分发版      | 串行控制台访问
:-----------|:---------------------
Red Hat Enterprise Linux    | Azure 中提供的 Red Hat Enterprise Linux 映像默认已启用控制台访问。 
CentOS      | Azure 中提供的 CentOS 映像默认已启用控制台访问。 
Ubuntu      | Azure 中提供的 Ubuntu 映像默认已启用控制台访问。
CoreOS      | Azure 中提供的 CoreOS 映像默认已启用控制台访问。
SUSE        | Azure 中提供的较新 SLES 映像默认已启用控制台访问。 如果在 Azure 上使用旧版（10 或更低）SLES，请遵照此[知识库文章](https://www.novell.com/support/kb/doc.php?id=3456486)启用串行控制台。 
Oracle Linux        | Azure 中提供的 Oracle Linux 映像默认已启用控制台访问。
自定义 Linux 映像     | 若要为自定义 Linux VM 映像启用串行控制台，请在 /etc/inittab 中启用控制台访问，以便在 ttyS0 上运行终端。 下面是在 inittab 文件中添加此条目的示例：`S0:12345:respawn:/sbin/agetty -L 115200 console vt102`。 有关正确创建自定义映像的详细信息，请参阅[在 Azure 中创建和上传 Linux VHD](https://aka.ms/createuploadvhd)。

## <a name="common-scenarios-for-accessing-serial-console"></a>访问串行控制台的常见场景 
场景          | 串行控制台中的操作                
:------------------|:-----------------------------------------
受损的 FSTAB 文件 | 按 `Enter` 键继续，然后使用文本编辑器修复 fstab 文件。 可能需要在单用户模式下执行此操作。 请参阅[如何修复 fstab 问题](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors)和[使用串行控制台访问 GRUB 和单用户模式](serial-console-grub-single-user-mode.md)获取入门信息。
错误的防火墙规则 | 访问串行控制台并修复 iptables。 
文件系统损坏/检查 | 访问串行控制台并恢复文件系统。 
SSH/RDP 配置问题 | 访问串行控制台并更改设置。 
网络锁定系统| 通过门户访问串行控制台以管理系统。 
与引导加载程序交互 | 通过串行控制台访问 GRUB。 参阅[使用串行控制台访问 GRUB 和单用户模式](serial-console-grub-single-user-mode.md)获取入门信息。 

## <a name="disable-serial-console"></a>禁用串行控制台
默认情况下，所有订阅为所有 VM 启用了串行控制台访问。 可以在订阅级别或 VM 级别禁用串行控制台。

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
尽管不会记录控制台的访问密码，但如果在控制台中运行的命令包含或输出密码、机密、用户名或其他任何形式的个人身份信息 (PII)，则在实现串行控制台的回滚功能过程中，会将这些信息连同其他所有可见文本一起写入虚拟机的启动诊断日志。 这些日志不断轮替，只有对诊断存储帐户拥有读取权限的个人才能访问它们。但是，我们建议遵循有关将 SSH 控制台用于涉及机密和/或 PII 的任何操作的最佳做法。 

### <a name="concurrent-usage"></a>并发使用
如果某个用户已连接到串行控制台，而另一个用户已成功请求访问同一个虚拟机，则第一个用户将断开连接，第二用户将建立连接，就好比是第一个用户站起来离开物理控制台，然后一个新用户在控制台旁边坐下来。

>[!CAUTION] 
这意味着，断开连接的用户并未注销！ 断开连接后强制注销（通过 SIGHUP 或类似机制）的功能目前仍在规划中。 对于 Windows，SAC 中会启用自动超时；但对于 Linux，可以配置终端超时设置。 为此，只需将 `export TMOUT=600` 添加到登录控制台时所用的用户帐户的 .bash_profile 或 .profile 中，以便在 10 分钟后使会话超时。

## <a name="accessibility"></a>可访问性
可访问性是 Azure 串行控制台的重点。 为此，我们已确保视觉和听觉障碍者，以及可能无法使用鼠标的人群能够访问串行控制台。

### <a name="keyboard-navigation"></a>键盘导航
使用键盘上的 `tab` 键在 Aure 门户中的串行控制台界面上导航。 屏幕上会突出显示你的位置。 若要使焦点离开串行控制台边栏选项卡，请在键盘上按 `Ctrl + F6`。

### <a name="use-serial-console-with-a-screen-reader"></a>结合使用串行控制台与屏幕阅读器
串行控制台附带内置屏幕阅读器支持。 在打开屏幕阅读器的情况下导航，屏幕阅读器可大声读出当前所选按钮的替换文字。

## <a name="errors"></a>Errors
大多数错误都是暂时性的，重试串行控制台连接往往可以解决。 下表显示了错误和缓解措施的列表

错误                            |   缓解措施 
:---------------------------------|:--------------------------------------------|
无法检索“<VMNAME>”的启动诊断设置。 若要使用串行控制台，请确保为此 VM 启用了启动诊断。 | 确保 VM 已启用[启动诊断](boot-diagnostics.md)。 
VM 处于已停止/已解除分配状态。 启动 VM，然后重试串行控制台连接。 | 虚拟机必须处于已启动状态才能访问串行控制台
没有所需的权限，无法使用此 VM 来访问串行控制台。 请确保至少拥有 VM 参与者角色权限。| 需要特定的访问权限才能访问串行控制台。 有关详细信息，请参阅[访问要求](#prerequisites)
无法确定启动诊断存储帐户“<STORAGEACCOUNTNAME>”的资源组。 确认是否为此 VM 启用了启动诊断，以及是否有权访问此存储帐户。 | 需要特定的访问权限才能访问串行控制台。 有关详细信息，请参阅[访问要求](#prerequisites)
Web 套接字已关闭或无法打开。 | 你可能需要将 `*.console.azure.com` 加入允许列表。 一种更详细但更冗长的方法是将 [Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/en-us/download/details.aspx?id=41653)加入允许列表，该范围相当规律地定期更改。
## <a name="known-issues"></a>已知问题 
由于串行控制台访问功能目前仍处于预览阶段，我们正在努力解决一些已知问题。 下面是已知问题及其可能的解决方法的列表：

问题                           |   缓解措施 
:---------------------------------|:--------------------------------------------|
没有相应的选项用于访问虚拟机规模集实例的串行控制台 |  在预览期，不支持访问虚拟机规模集实例的串行控制台。
在出现连接标题后按 Enter 不会显示登录提示 | 请参阅此页：[按 Enter 不执行任何操作](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)。 如果你运行的自定义 VM、强化设备或 GRUB 配置导致 Linux 无法正确连接到串行端口，则可能会发生这种情况。
访问此 VM 的启动诊断存储帐户时遇到“已禁止”响应。 | 请确保启动诊断没有帐户防火墙。 若要使串行控制台正常运行，需要一个可访问的启动诊断存储帐户。
串行控制台文本仅占用屏幕大小的一部分（通常在使用文本编辑器后） | 这是一个已知问题：串行连接下屏幕大小未知。 建议安装可提供“重设大小”命令的 xterm 或某些其他类似实用工具。 运行“重设大小”可解决此问题。


## <a name="frequently-asked-questions"></a>常见问题 
**问：如何发送反馈？**

A. 可以访问 https://aka.ms/serialconsolefeedback 来提供问题反馈。 也可以通过 azserialhelp@microsoft.com，或者 http://feedback.azure.com 上的虚拟机类别发送反馈（不太建议）

**问：我无法访问串行控制台，在哪里可以提出支持案例？**

A. 此预览功能遵守 Azure 预览条款。 最好是通过上面所述的渠道请求此功能的支持。 

**问：是否可以使用串行控制台替代 SSH 连接？**

A. 虽然这从技术上来看是可能的，但串行控制台主要用作无法通过 SSH 进行连接时的故障排除工具。 不建议使用串行控制台替代 SSH 的两个原因如下：

1. 串行控制台没有 SSH 那样大的带宽 - 它是仅限文本的连接，因此，很难在串行控制台中进行大量的 GUI 交互。
1. 目前仅能通过用户名和密码访问串行控制台。 SSH 密钥比用户名/密码组合安全得多，因此，从登录安全性的角度来说，建议使用 SSH 而不是串行控制台。



## <a name="next-steps"></a>后续步骤
* 使用串行控制台[启动到 GRUB 并进入单用户模式](serial-console-grub-single-user-mode.md)
* 使用串行控制台执行 [NMI 和 SysRq 调用](serial-console-nmi-sysrq.md)
* 串行控制台也适用于 [Windows](../windows/serial-console.md) VM
* 详细了解[启动诊断](boot-diagnostics.md)