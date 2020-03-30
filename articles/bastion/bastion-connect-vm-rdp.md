---
title: 使用 Azure 堡垒连接到 Windows VM
description: 在本文中，了解如何使用 Azure 堡垒连接到运行 Windows 的 Azure 虚拟机。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 6ec60dc313c8a4374637adf38ea0e5a7d4ed964b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597332"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>使用 Azure 堡垒连接到 Windows 虚拟机

使用 Azure 堡垒，可以直接在 Azure 门户中通过 SSL 安全无缝地连接到虚拟机。 使用 Azure 堡垒时，VM 不需要客户端、代理或其他软件。 本文介绍如何连接到 Windows VM。 有关连接到 Linux VM 的信息，请参阅[使用 Azure 堡垒 - Linux 连接到 VM。](bastion-connect-vm-ssh.md)

Azure Bastion 向预配它的虚拟网络中的所有 VM 提供安全连接。 使用 Azure Bastion 可防止虚拟机向外部公开 RDP/SSH 端口，同时仍然使用 RDP/SSH 提供安全访问。 有关详细信息，请参阅[概述](bastion-overview.md)。

## <a name="before-you-begin"></a>开始之前

请确保为 VM 所在的虚拟网络设置了 Azure 堡垒主机。 在虚拟网络中预配和部署 Bastion 服务后，可以使用它连接到虚拟网络中的任何 VM。 要设置 Azure 堡垒主机，请参阅[创建 Azure 堡垒主机](bastion-create-host-portal.md)。

### <a name="required-roles"></a>所需角色

要建立连接，需要以下角色：

* 虚拟机上的读者角色
* NIC 上的读者角色（使用虚拟机的专用 IP）
* Azure Bastion 资源上的读者角色

### <a name="ports"></a>端口

要连接到 Windows VM，必须在 Windows VM 上打开以下端口：

* 入站端口： RDP （3389）

## <a name="connect"></a><a name="rdp"></a>连接

1. 打开[Azure 门户](https://portal.azure.com)。 导航到要连接到的虚拟机，然后单击 **"连接**"并从下拉列表中选择 **"堡垒**"。

   ![VM 连接](./media/bastion-connect-vm-rdp/connect.png)
1. 单击"堡垒"后，将显示一个侧栏，该边栏具有三个选项卡 - RDP、SSH 和堡垒。 如果为虚拟网络预配了堡垒，则默认情况下堡垒选项卡处于活动状态。 如果未为虚拟网络预配堡垒，则可以单击该链接来配置堡垒。 有关配置说明，请参阅[配置堡垒](bastion-create-host-portal.md)。

   ![堡垒选项卡](./media/bastion-connect-vm-rdp/bastion.png)
1. 在"堡垒"选项卡上，输入虚拟机的用户名和密码，然后单击"**连接**"。 通过 Bastion 连接到此虚拟机的 RDP 连接将直接在 Azure 门户（通过 HTML5）中使用端口 443 和堡垒服务打开。

   ![RDP 连接](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>后续步骤

阅读[堡垒常见问题解答](bastion-faq.md)
