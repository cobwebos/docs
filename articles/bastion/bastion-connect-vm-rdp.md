---
title: 使用 Azure Bastion 连接到 Windows VM
description: 本文介绍如何使用 Azure Bastion 连接到运行 Windows 的 Azure 虚拟机。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 79eb09a005f62846fc2f7e3e7b493d5e366edabc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84744317"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>使用 Azure Bastion 连接到 Windows 虚拟机

使用 Azure Bastion，可以直接在 Azure 门户中通过 SSL 安全无缝地连接到你的虚拟机。 使用 Azure Bastion 时，VM 不需要客户端、代理或其他软件。 本文说明如何连接到 Windows VM。 若要了解如何连接到 Linux VM，请参阅[使用 Azure Bastion 连接到 VM - Linux](bastion-connect-vm-ssh.md)。

Azure Bastion 为预配它的虚拟网络中的所有 VM 提供安全的连接。 使用 Azure Bastion 可防止虚拟机向外部公开 RDP/SSH 端口，同时仍然使用 RDP/SSH 提供安全访问。 有关详细信息，请参阅[概述](bastion-overview.md)。

## <a name="before-you-begin"></a>准备阶段

请确保已为 VM 所在的虚拟网络设置 Azure Bastion 主机。 在虚拟网络中预配和部署 Bastion 服务后，便可以使用它连接到此虚拟网络中的任何 VM。 若要设置 Azure Bastion 主机，请参阅[创建 Azure Bastion 主机](bastion-create-host-portal.md)。

### <a name="required-roles"></a>必需的角色

若要建立连接，需要以下角色：

* 虚拟机上的读者角色
* NIC 上的读者角色（使用虚拟机的专用 IP）
* Azure Bastion 资源上的读者角色

### <a name="ports"></a>端口

若要连接到 Windows VM，必须在 Windows VM 上打开以下端口：

* 入站端口：RDP (3389)

## <a name="connect"></a><a name="rdp"></a>连接

1. 打开 [Azure 门户](https://portal.azure.com)。 导航到要连接到的虚拟机，然后单击“连接”并从下拉列表中选择“Bastion”。

   ![VM 连接](./media/bastion-connect-vm-rdp/connect.png)
1. 单击 "堡垒" 后，会出现一个侧栏，其中有三个选项卡-RDP、SSH 和堡垒。 如果已为虚拟网络预配了 Bastion，则默认情况下“Bastion”选项卡处于活动状态。 如果未为虚拟网络预配 Bastion，可单击链接来配置 Bastion。 有关配置说明，请参阅[配置 Bastion](bastion-create-host-portal.md)。

   ![“Bastion”选项卡](./media/bastion-connect-vm-rdp/bastion.png)
1. 在“Bastion”选项卡上，输入虚拟机的用户名和密码，然后单击“连接”。 通过 Bastion 连接到此虚拟机的 RDP 将使用端口 443 和 Bastion 服务在 Azure 门户中（通过 HTML5）直接打开。

   ![RDP 连接](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>后续步骤

阅读 [Bastion 常见问题解答](bastion-faq.md)
