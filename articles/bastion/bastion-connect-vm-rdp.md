---
title: 使用 Azure 堡垒连接到 Windows VM |Microsoft Docs
description: 本文介绍如何使用 Azure 堡垒连接到运行 Windows 的 Azure 虚拟机。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: cherylmc
ms.openlocfilehash: dc741007c7de8d8e24f9c0f9e4e0c03306d036a4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498358"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>使用 Azure 堡垒连接到 Windows 虚拟机

本文介绍如何使用 Azure 堡垒在 Azure 虚拟网络中将 RDP 安全且无缝地连接到 Windows Vm。 可通过 Azure 门户直接连接到 VM。 在使用 Azure Bastion 时，VM 无需客户端、代理和其他软件。 有关 Azure 堡垒的详细信息，请参阅[概述](bastion-overview.md)。

## <a name="before-you-begin"></a>开始之前

请确保已为 VM 所在的虚拟网络设置了 Azure 堡垒主机。 有关详细信息，请参阅[创建 Azure 堡垒主机](bastion-create-host-portal.md)。 在虚拟网络中设置并部署堡垒服务后，可以使用它连接到此虚拟网络中的任何 VM。 堡垒假设你使用 RDP 连接到 Windows VM，使用 SSH 连接到 Linux Vm。 有关连接到 Linux VM 的信息，请参阅[连接到 VM-Linux](bastion-connect-vm-ssh.md)。

需要使用以下角色进行连接：

* 虚拟机上的读者角色
* NIC 上的读者角色（使用虚拟机的专用 IP）
* Azure Bastion 资源上的读者角色

## <a name="rdp"></a>使用 RDP 进行连接

1. 打开 [Azure 门户](https://portal.azure.com)。 导航到要连接到的虚拟机，然后单击 "**连接**"。 使用 RDP 连接时，VM 应为 Windows 虚拟机。

   ![VM 连接](./media/bastion-connect-vm-rdp/connect.png)
1. 单击 "连接" 后，将显示一个侧栏，其中有三个选项卡-RDP、SSH 和堡垒。 如果已为虚拟网络预配了堡垒，则默认情况下，堡垒选项卡处于活动状态。 如果你没有为虚拟网络预配堡垒，你可以单击该链接来配置堡垒。 有关配置说明，请参阅[配置堡垒](bastion-create-host-portal.md)。

   ![VM 连接](./media/bastion-connect-vm-rdp/bastion.png)
1. 在 "堡垒" 选项卡上，选择虚拟机的用户名和密码，然后单击 "**连接**"。 通过堡垒连接到此虚拟机的 RDP 将直接在 Azure 门户（通过 HTML5）中使用端口443和堡垒服务打开。

   ![VM 连接](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>后续步骤

阅读[堡垒常见问题解答](bastion-faq.md)