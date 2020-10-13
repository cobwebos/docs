---
title: 使用 Azure Bastion 连接到 Windows VM
description: 本文介绍如何使用 Azure Bastion 连接到运行 Windows 的 Azure 虚拟机。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/12/2020
ms.author: cherylmc
ms.openlocfilehash: 9c9bac20beb415f8bc29ca63d530e5cd8492d2d3
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91997006"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>使用 Azure Bastion 连接到 Windows 虚拟机

使用 Azure Bastion，可以直接在 Azure 门户中通过 SSL 安全无缝地连接到你的虚拟机。 使用 Azure Bastion 时，VM 不需要客户端、代理或其他软件。 本文说明如何连接到 Windows VM。 有关连接到 Linux VM 的信息，请参阅 [连接到 LINUX vm](bastion-connect-vm-ssh.md)。

Azure Bastion 为预配它的虚拟网络中的所有 VM 提供安全的连接。 使用 Azure Bastion 可防止虚拟机向外部公开 RDP/SSH 端口，同时仍然使用 RDP/SSH 提供安全访问。 有关详细信息，请参阅 [什么是 Azure 堡垒？](bastion-overview.md)。

## <a name="before-you-begin"></a>准备阶段

在开始之前，请验证是否满足以下条件：

* 已安装堡垒主机的 VNet。

   请确保已为 VM 所在的虚拟网络设置 Azure Bastion 主机。 在虚拟网络中预配和部署 Bastion 服务后，便可以使用它连接到此虚拟网络中的任何 VM。 若要设置 Azure 堡垒主机，请参阅 [创建堡垒主机](tutorial-create-host-portal.md#createhost)。
* 虚拟网络中的 Windows 虚拟机。
* 以下必需角色：
  * 虚拟机上的 "读取者" 角色。
  * 具有虚拟机专用 IP 的 NIC 上的读者角色。
  * Azure 堡垒资源上的 "读取者" 角色。
* 端口：若要连接到 Windows VM，你必须在 Windows VM 上打开以下端口：
  * 入站端口：RDP (3389)

## <a name="connect"></a><a name="rdp"></a>连接

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>后续步骤

阅读 [Bastion 常见问题解答](bastion-faq.md)。