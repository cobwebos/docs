---
title: 使用 Azure 堡垒连接到 Windows 虚拟机规模集 |Microsoft Docs
description: 本文介绍如何使用 Azure 堡垒连接到 Azure 虚拟机规模集。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 4f513aaf113ef4bd6e75e5c4b31e0f0252d45f10
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988084"
---
# <a name="connect-to-a-virtual-machine-scale-set-using-azure-bastion"></a>使用 Azure 堡垒连接到虚拟机规模集

本文介绍如何使用 Azure 堡垒在 Azure 虚拟网络中使用 Windows 虚拟机规模集实例安全且无缝地连接到 Windows 虚拟机规模集实例。 可以直接从 Azure 门户连接到虚拟机规模集实例。 在使用 Azure Bastion 时，VM 无需客户端、代理和其他软件。 有关 Azure 堡垒的详细信息，请参阅[概述](bastion-overview.md)。

## <a name="before-you-begin"></a>开始之前

请确保已为虚拟机规模集所在的虚拟网络设置了 Azure 堡垒主机。 有关详细信息，请参阅[创建 Azure 堡垒主机](bastion-create-host-portal.md)。 在虚拟网络中设置并部署堡垒服务后，你可以使用它连接到此虚拟网络中的虚拟机规模集实例。 堡垒假设你使用 RDP 连接到 Windows 虚拟机规模集，使用 SSH 连接到 Linux 虚拟机规模集。 有关连接到 Linux VM 的信息，请参阅[连接到 VM-Linux](bastion-connect-vm-ssh.md)。

## <a name="rdp"></a>使用 RDP 进行连接

1. 打开 [Azure 门户](https://portal.azure.com)。 导航到要连接到的虚拟机规模集。

   ![引导](./media/bastion-connect-vm-scale-set/1.png)
2. 导航到要连接到的虚拟机规模集实例，然后选择 "**连接**"。 使用 RDP 连接时，虚拟机规模集应为 Windows 虚拟机规模集。

   ![虚拟机规模集](./media/bastion-connect-vm-scale-set/2.png)
3. 选择 "**连接**" 后，将显示一条侧栏，其中包含三个选项卡-RDP、SSH 和堡垒。 从侧栏中选择 "**堡垒**" 选项卡。 如果你没有为虚拟网络预配堡垒，则可以选择配置堡垒的链接。 有关配置说明，请参阅[配置堡垒](bastion-create-host-portal.md)。

   ![堡垒选项卡](./media/bastion-connect-vm-scale-set/3.png)
4. 在 "堡垒" 选项卡上，为虚拟机规模集输入用户名和密码，然后选择 "**连接**"。

   ![连接](./media/bastion-connect-vm-scale-set/4.png)
5. 通过堡垒连接到此虚拟机的 RDP 将直接在 Azure 门户（通过 HTML5）中使用端口443和堡垒服务打开。

## <a name="next-steps"></a>后续步骤

阅读[堡垒常见问题解答](bastion-faq.md)。