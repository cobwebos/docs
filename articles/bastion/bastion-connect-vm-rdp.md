---
title: 连接到 Windows VM 使用 Azure 堡垒 |Microsoft Docs
description: 在本文中，了解如何连接到 Azure 虚拟机通过在运行 Windows 的 Azure 堡垒。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 376b7042a513dd50647dc8f88bf1de70f65bb21c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478407"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion-preview"></a>连接到使用 Azure 堡垒 （预览版） 的 Windows 虚拟机

本文介绍如何对安全、 无缝地通过 rdp 连接到 Windows Vm 中的 Azure 虚拟网络使用 Azure 堡垒。 可通过 Azure 门户直接连接到 VM。 在使用 Azure Bastion 时，VM 无需客户端、代理和其他软件。 有关 Azure 堡垒详细信息，请参阅[概述](bastion-overview.md)。

> [!IMPORTANT]
> 此公共预览版在提供时没有附带服务级别协议，不应用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="before-you-begin"></a>开始之前

请确保已设置 VM 所驻留的虚拟网络的 Azure 堡垒主机。 有关详细信息，请参阅[创建 Azure 堡垒主机](bastion-create-host-portal.md)。 一旦堡垒服务预配和部署在虚拟网络，可用于连接到此虚拟网络中的任何 VM。 在此预览版中，堡垒假定您使用 RDP 连接到 Windows VM 和 SSH 连接到 Linux Vm。 有关连接到 Linux VM 的信息，请参阅[连接到 VM-Linux](bastion-connect-vm-ssh.md)。

需要使用以下角色进行连接：

* 虚拟机上的读者角色
* NIC 上的读者角色（使用虚拟机的专用 IP）
* Azure Bastion 资源上的读者角色

## <a name="rdp"></a>使用 RDP 进行连接

1. 使用[此链接](https://aka.ms/BastionHost)，打开 Azure 堡垒预览门户页。 导航到你想要连接到虚拟机，然后单击**Connect**。 使用 RDP 连接时，应将 VM Windows 虚拟机。

    ![VM 连接](./media/bastion-connect-vm-rdp/connect.png)

1. 单击连接后，侧栏会显示具有三个选项卡-RDP、 SSH，并堡垒。 如果堡垒已预配为虚拟网络，则堡垒选项卡处于活动状态默认情况下。 如果你未为虚拟网络进行预配堡垒，可以单击链接以配置堡垒。 有关配置说明，请参阅[配置堡垒](bastion-create-host-portal.md)。 如果没有看到**堡垒**列出，则表示尚未打开在预览门户。 打开在门户中使用这[预览链接](https://aka.ms/BastionHost)。

    ![VM 连接](./media/bastion-connect-vm-rdp/bastion.png)

1. 在堡垒选项卡、 用户名和密码为虚拟机，然后单击**Connect**。 与堡垒通过此虚拟机的 RDP 连接将使用端口 443 和堡垒服务 （通过 HTML5) 在 Azure 门户中直接打开。

    ![VM 连接](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>后续步骤

读取[堡垒常见问题](bastion-faq.md)
