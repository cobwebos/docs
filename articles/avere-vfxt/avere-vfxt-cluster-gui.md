---
title: 访问 Avere vFXT 控制面板 - Azure
description: 如何连接到 vFXT 群集和基于浏览器的 Avere 控制面板以配置 Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 72d1676613de699abda2136a7743a974b2b17c01
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2018
ms.locfileid: "52162852"
---
# <a name="access-the-vfxt-cluster"></a>访问 vFXT 群集

要更改设置及监视 Avere vFXT 群集，请使用 Avere 控制面板。 Avere 控制面板是群集的基于浏览器的图形界面。

由于 vFXT 群集位于专用虚拟网络中，因此必须创建 SSH 隧道或使用其他方法来访问群集的管理 IP 地址。 有两个基本步骤： 

1. 在工作站和专用 VNet 之间创建连接 
1. 在 Web 浏览器中加载群集的控制面板 

> [!NOTE] 
> 本文假设已在群集控制器或群集虚拟网络中另一 VM 上设置了公共 IP 地址。 本文介绍了如何使用该 VM 作为主机来访问群集。 如果使用 VPN 或 ExpressRoute 进行 VNet 访问，请跳到[连接到 Avere 控制面板](#connect-to-the-avere-control-panel-in-a-browser)。

在连接之前，请确保在本地计算机上安装了创建群集控制器时所用的 SSH 公钥/私钥对。 如需帮助，请阅读有关适用于 [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) 或适用于 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) 的 SSH 密钥文档。  

## <a name="ssh-tunnel-with-a-linux-host"></a>在 Linux 主机上使用 SSH 隧道

如果使用基于 Linux 的客户端，请通过以下形式使用 SSH 隧道： 

ssh -L local_port:cluster_mgmt_ip:443 controller_username@controller_public_IP

此命令通过群集控制器的 IP 地址连接到群集的管理 IP 地址。

示例：

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

如果使用 SSH 公钥创建群集，并且在客户端系统上安装了匹配的密钥，则身份验证会自动执行。

## <a name="ssh-tunnel-with-a-windows-host"></a>在 Windows 主机上使用 SSH 隧道

此示例使用常见的基于 Windows 的终端实用工具 PuTTY。

使用群集控制器用户名及其 IP 地址填写 PuTTY **主机名**字段：*your_username*@*controller_public_IP*。

示例： ``azureuser@203.0.113.51``

在“配置”面板中：

1. 展开左侧的“连接” > “SSH”。 
1. 单击“隧道”。 
1. 输入源端口，如 8443。 
1. 对于目标，请输入 vFXT 群集的管理 IP 地址和端口 443。 
   示例：``203.0.113.51:443``
1. 单击“添加”。
1. 单击“打开”。

![Putty 应用程序的屏幕截图，显示了单击以添加隧道的位置](media/avere-vfxt-ptty-numbered.png)

如果使用 SSH 公钥创建群集，并且在客户端系统上安装了匹配的密钥，则身份验证会自动执行。

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>在浏览器中连接到 Avere 控制面板

此步骤使用 Web 浏览器以连接到 vFXT 集群上运行的配置实用程序。

* 对于 SSH 隧道连接，请打开 Web 浏览器并导航到 https://127.0.0.1:8443。 

  你在创建隧道时已连接到群集 IP 地址，因此只需要在浏览器中使用 localhost IP 地址。 如果你使用了 8443 之外的本地端口，请改用你的端口号。

* 如果你使用 VPN 或 ExpressRoute 来访问群集，请在浏览器中导航到群集管理 IP 地址。 示例：``https://203.0.113.51``

可能需要单击“高级”并确认是否可以安全地继续浏览该页面，具体取决于浏览器。

输入创建群集时提供的用户名 `admin` 以及密码。

![填写了用户名“管理员”和密码的 Avere 登录页面的屏幕截图](media/avere-vfxt-gui-login.png)

单击“登录”或在键盘上按 Enter。

## <a name="next-steps"></a>后续步骤

现可访问群集，启用[支持](avere-vfxt-enable-support.md)。
