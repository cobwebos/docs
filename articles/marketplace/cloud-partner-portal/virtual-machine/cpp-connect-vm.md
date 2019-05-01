---
title: 连接到基于 Microsoft Azure 的虚拟机 | Microsoft Docs
description: 介绍如何连接到 Azure 上创建的新虚拟机。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: fd68846b9144c3efcc71dec369d64119427758a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744465"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>连接到基于 Azure 的虚拟机

本文介绍如何连接并登录到 Azure 创建的虚拟机 (VM)。  成功连接后，可以使用该 VM，就如同已本地登录到其主机服务器一样。 

## <a name="connect-to-a-windows-based-vm"></a>连接到基于 Windows 的 VM

使用远程桌面客户端连接到托管在 Azure 上的基于 Windows 的 VM。  大多数 Windows 版本原生包含对远程桌面协议 (RDP) 的支持。  对于其他计算机，可在[远程桌面客户端](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)中找到有关客户端的详细信息。  

以下文章详细介绍了如何使用内置 Windows RDP 支持连接到 VM:[如何连接并登录到运行 Windows 的 Azure 虚拟机](../../../virtual-machines/windows/connect-logon.md)。  

>[!TIP]
> 在此过程中，你可能会收到安全警告，例如，.rdp 文件来自未知的发布者，或无法验证你的用户凭据。  可以放心忽略这些警告。


## <a name="connect-to-a-linux-based-vm"></a>连接到基于 Linux 的 VM

若要连接到基于 Linux 的 VM，需要一个安全外壳 (SSH) 协议客户端。  本文使用免费的 [PuTTY](https://www.ssh.com/ssh/putty/) SHH 终端。

1. 在 [Azure 门户](https://ms.portal.azure.com)的“虚拟机”边栏选项卡中，选择要连接到的 VM。  
2. 如果该 VM 尚未运行，请将它**启动**。
3. 单击 VM 的名称打开其“概述”页。
4. 记下 VM 的公共 IP 地址和 DNS 名称。  （如果尚未设置这些值，必须[创建一个网络接口](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)）

   ![VM 概述设置](./media/publishvm_019.png)
 
5. 打开 PuTTY 应用程序。  
6. 在 PuTTY 的“配置”对话框中，输入 VM 的 IP 地址或 DNS 名称。 

   ![PuTTY 终端设置](./media/publishvm_020.png)
 
7. 单击“打开”以打开 PuTTY 终端。  
8. 出现提示时，请输入 Linux VM 帐户的帐户名称和密码。 

   如果遇到连接问题，请参阅文档 SSH 客户端，例如[第 10 章：常见的错误消息](https://www.ssh.com/ssh/putty/putty-manuals/0.68/Chapter10.html#errors)。

有关详细信息，包括如何将桌面添加到预配的 Linux VM，请参阅[安装并配置远程桌面以连接到 Azure 中的 Linux VM](../../../virtual-machines/linux/use-remote-desktop.md)。


## <a name="stop-unused-vms"></a>停止未使用的 VM
当 VM 处于运行或空闲状态时，Azure 会计收 VM 托管费。  因此，最佳做法是停止当前未使用的 VM。  例如，可以关闭测试、备份或已淘汰的 VM。 若要关闭 VM，请执行以下步骤：

1. 在“虚拟机”边栏选项卡中，选择要停止的 VM。 
2. 在靠近页面顶部处的工具栏中，单击“停止”按钮。

   ![停止 VM](./media/publishvm_018.png)

Azure 会在名为 *deallocation* 的进程中快速停止该 VM，这不仅会关闭该 VM 上的操作系统，而且还会释放以前为它预配的硬件和网络资源。

如果以后想要重新激活已停止的 VM，请选择它，然后单击“启动”按钮。


## <a name="next-steps"></a>后续步骤

建立远程连接后，可以[配置 VM](./cpp-configure-vm.md)。
